# CI Pipeline: Express API with GitHub Actions

A small Express API with automated tests, built to demonstrate a continuous integration pipeline. Every push runs the test suite on a clean GitHub-hosted machine, so a broken change is caught before it reaches `main`.

## How the pipeline works

The workflow is defined in `.github/workflows/ci.yml`.

```
push to any branch  ─┐
                     ├─>  test job (ubuntu-latest)
pull request to main ┘        1. Checkout code
                              2. Set up Node.js 22
                              3. npm ci
                              4. npm test
```

| Step | Why |
|------|-----|
| `actions/checkout` | Gets the repository onto the runner |
| `actions/setup-node` (Node 22) | Pins the Node version so local and CI runs match |
| `npm ci` | Installs the exact versions in `package-lock.json`, which makes builds reproducible and fails if the lockfile and `package.json` disagree |
| `npm test` | Runs the Jest suite. A failing test fails the workflow |

Triggers: pushes to every branch give fast feedback while working, and pull requests targeting `main` check the merge candidate.

## The API

| Method | Path | Response |
|--------|------|----------|
| GET | `/` | `200`, plain text: `Welcome to the CI/CD Demo API` |
| GET | `/health` | `200`, JSON: `{"status": "healthy"}` |

## Tests

Tests live in `__tests__/app.test.js` and use Jest with Supertest. There are two tests, one per endpoint, each checking the status code and the response body.

`src/app.js` builds and exports the Express app, and `src/server.js` is the only file that starts listening on a port. Keeping them apart means tests can import the app and send requests to it without opening a real port.

## Run it locally

```bash
npm ci        # install dependencies from the lockfile
npm test      # run the test suite
npm start     # start the server on http://localhost:3000
```

## Project structure

```
.github/workflows/ci.yml   CI workflow
src/app.js                 Express app and routes
src/server.js              Starts the server on port 3000
__tests__/app.test.js      Jest and Supertest tests
package.json               Scripts and dependencies
package-lock.json          Exact dependency versions
```

## Not included yet

- Linting or formatting checks
- Test coverage reporting
- Dependency scanning (`npm audit` or Dependabot)
- A deployment stage after the tests pass
