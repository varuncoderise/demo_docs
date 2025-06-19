name: Release

on:
  push:
    branches: [main]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout full history with tags
        uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Fetch all history for tag lookup

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - name: Install Dependencies
        run: npm ci

      - name: Detect if chore/hotfix needs 4-digit version
        id: version
        run: |
          git fetch --tags --force
          LAST_TAG=$(git tag --sort=-v:refname | head -n1 || echo "v0.0.0")
          echo "🧠 Latest tag: $LAST_TAG"

          COMMIT_MSG=$(git log -1 --pretty=%B)
          COMMIT_SHA=$(git log -1 --pretty=%H)
          echo "📌 Commit message: $COMMIT_MSG"
          echo "📌 Commit SHA: $COMMIT_SHA"

          # Get changed files with their paths
          CHANGED_FILES=$(git diff --name-only ${LAST_TAG} HEAD | xargs -I {} echo "- https://github.com/${{ github.repository }}/blob/${COMMIT_SHA}/{}" || echo "- No files changed.")
          echo "CHANGED_FILES<<EOF" >> $GITHUB_ENV
          echo "$CHANGED_FILES" >> $GITHUB_ENV
          echo "EOF" >> $GITHUB_ENV

          echo "COMMIT_MSG=$COMMIT_MSG" >> $GITHUB_ENV
          echo "COMMIT_SHA=$COMMIT_SHA" >> $GITHUB_ENV

          # Parse version (vX.Y.Z or vX.Y.Z.W)
          VERSION=${LAST_TAG#v}  # Strip 'v' prefix
          IFS='.' read -r MAJOR MINOR PATCH FOURTH <<< "$VERSION"

          # Default to 0 if FOURTH is empty
          FOURTH=${FOURTH:-0}

          if [[ "$COMMIT_MSG" =~ ^(chore|hotfix) ]]; then
            if [[ -z "$FOURTH" || "$FOURTH" == "0" ]]; then
              PATCH=$((PATCH + 1))
              FOURTH=1
            else
              FOURTH=$((FOURTH + 1))
            fi
            FORCE_VERSION="v$MAJOR.$MINOR.$PATCH.$FOURTH"
            echo "FORCE_VERSION=$FORCE_VERSION" >> $GITHUB_ENV
            echo "LAST_TAG=$LAST_TAG" >> $GITHUB_ENV
          fi

      - name: Generate Changelog for Maintenance Release
        if: env.FORCE_VERSION != ''
        run: |
          echo "Generating changelog for ${FORCE_VERSION}..."
          {
            echo "# Changelog for ${FORCE_VERSION}"
            echo ""
            echo "## Changes"
            echo "- ${COMMIT_MSG} ([${COMMIT_SHA:0:7}](https://github.com/${{ github.repository }}/commit/${COMMIT_SHA}))"
            echo ""
            echo "## Changed Files"
            echo "${CHANGED_FILES}"
          } > CHANGELOG.md

          git add CHANGELOG.md
          git config user.name "semantic-release-bot"
          git config user.email "bot@semantic-release"
          git commit -m "chore: update changelog for ${FORCE_VERSION}" || echo "No changes to commit in CHANGELOG.md"
          git push origin main

      - name: Create 4-digit tag and release
        if: env.FORCE_VERSION != ''
        env:
          GH_TOKEN: ${{ secrets.GH_PAT }}
        run: |
          if git rev-parse "${FORCE_VERSION}" >/dev/null 2>&1; then
            echo "Tag ${FORCE_VERSION} already exists. Skipping."
          else
            git config user.name "semantic-release-bot"
            git config user.email "bot@semantic-release"
            git tag -a "${FORCE_VERSION}" -m "chore: release ${FORCE_VERSION}"
            git push origin "${FORCE_VERSION}"

            echo "Creating GitHub release with changed files only..."
            echo "${CHANGED_FILES}" > release_notes.md
            gh release create "${FORCE_VERSION}" \
              --title "Maintenance Release: ${FORCE_VERSION}" \
              --notes-file release_notes.md
          fi

      - name: Run Semantic Release for normal releases
        if: env.FORCE_VERSION == ''
        env:
          GITHUB_TOKEN: ${{ secrets.GH_PAT }}
        run: npx semantic-release