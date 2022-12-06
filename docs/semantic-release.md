# semantic-release

Badge: [![semantic-release: angular](https://img.shields.io/badge/semantic--release-angular-e10079?logo=semantic-release)](https://github.com/semantic-release/semantic-release)

Semantic-release automates the whole package release workflow including: determining the next version number, generating the release notes and publishing the package.

# 1. Installation

In dev project: 
`npm install --save-dev semantic-release`
`npm install @semantic-release/git @semantic-release/changelog -D`

In CI: `npx semantic-release`

# 2. Configuration

Create a configuration file named `.realeaserc` file at the project root:

```json
{
  "branches": ["master", "main", "preprod"],
  "plugins": [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    [
      "@semantic-release/npm",
      {
        "npmPublish": false
      }
    ],
    [
      "@semantic-release/changelog",
      {
        "changelogFile": "CHANGELOG.md"
      }
    ],
    ["@semantic-release/github", {
      "assets": ["dist/", "package.json", "CHANGELOG.md"],
      "message": "chore(release): ${nextRelease.version} [skip ci]\n\n${nextRelease.notes}"
    }],
    ["@semantic-release/git", {
      "assets": ["dist/", "package.json", "CHANGELOG.md"],
      "message": "chore(release): ${nextRelease.version} [skip ci]\n\n${nextRelease.notes}"
    }]
  ],
  "repositoryUrl": "https://github.com/frdemoulin/test-semver"
}
```

## 3. Github secret

Need a Github personal access token to give repository push access to the CI
1. **Create a Github personal access token**: Profile > Settings > Developer settings > Personal access tokens > Fine-grained tokens > Generate new token
   Name: "Github token access for CI actions"
   Copy the value of the token
   Give repository access and User and Repository permissions
2. **Create a Github secret**: Repository home page > Settings > Secrets > Actions
    Secret name: GH_TOKEN
    Value: copy-paste the one from first step

# 4. CI

```yml
name: CI
on:
  [push, pull_request]
env:
  GH_TOKEN: ${{ secrets.GH_TOKEN }}
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      # Mandatory : fetch the current repository —————————————————————————————————————————————
      -   name: Checkout repository
          uses: actions/checkout@v2

      # To be faster, use cache system for the NPM 🐱 ————————————————————————————————————————————
      -   name: Cache NPM (node_modules)
          uses: actions/cache@v2
          env:
            cache-name: cache-node-modules
          with:
            path: ~/.npm
            key: ${{ runner.os }}-build-${{ env.cache-name }}-${{ hashFiles('**/package-lock.json') }}
            restore-keys: |
              ${{ runner.os }}-build-${{ env.cache-name }}-
              ${{ runner.os }}-build-
              ${{ runner.os }}-
      
      # Define the right Node.js's environment —————————————————————————————————————————————
      -   name: Environment for NPM
          uses: actions/setup-node@v2
          with:
            node-version: '18.12.1'

      # # NPM run build —————————————————————————————————————————————
      # -   name: NPM build
      #     run: |
      #       node -v
      #       npm ci --cache .npm --unsafe-perm --prefer-offline
      #       npm run build
      
      # # Upload artifacts (= builded files) to download them in the next job —————————————————————————————————————————————
      # -   name: NPM artifacts
      #     uses: actions/upload-artifact@v2
      #     with:
      #       name: npm-build
      #       retention-days: 1
      #       path: public/build/

  deploy:
    name: Symfony 5.4.0 (PHP ${{ matrix.php-version }})
    
    # Need the "build" job to be complete before started it
    needs: build
    runs-on: ubuntu-latest
    strategy:
      fail-fast: true
      matrix:
        php-version: ['7.4']
    steps:
      # —— Setup Github actions 🐙 —————————————————————————————————————————————
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2
      
      # https://github.com/shivammathur/setup-php (community)
      - name: "Setup PHP, extensions and composer with shivammathur/setup-php"
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ matrix.php-version }}
          ini-values: memory_limit=512M
          extensions: mbstring, xml, ctype, iconv, intl, pdo, pdo_mysql, dom, filter, gd, iconv, json, mbstring, pdo
          tools: composer:v2.4
          
      - name: Check PHP Version
        run: php -v
      
      ## —— Semantic Release 📦🚀 ———————————————————————————————————————————————————————————
      - name: Setup Semantic Release
        run: npm install -g semantic-release @semantic-release/git @semantic-release/changelog -D
      - name: release
        run: npx semantic-release
```

# 5. Usage

## 5.1. Commit analyzer

Commit message will be analyzed to auto detect semantic versioning:
- `fix(pencil): stop graphite breaking when too much pressure applied`: Patch Fix Release
- `feat(pencil): add 'graphiteWidth' option`: Minor Feature Release
```
perf(pencil): remove graphiteWidth option

BREAKING CHANGE: The graphiteWidth option has been removed.
The default graphite width of 10mm is always used for performance reasons.	Major Breaking Release
```
Note that the `BREAKING CHANGE:` token must be **in the footer** of the commit

It will generate automatically in the CI environment a new release with a tag with the version number

## 5.2. Grabbing the latest release version

[lien Github docs](https://docs.github.com/en/rest/releases/releases?apiVersion=2022-11-28#get-the-latest-release)

We can grab in the app code the latest release number version from the Github API.

GET `/repos/{owner}/{repo}/releases/latest`

```js
// Octokit.js
// https://github.com/octokit/core.js#readme
const octokit = new Octokit({
  auth: 'YOUR-TOKEN'
})

await octokit.request('GET /repos/{owner}/{repo}/releases/latest', {
  owner: 'OWNER',
  repo: 'REPO'
})
```