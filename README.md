# node-park

Node Playground app to demonstrate how to start a simple NodeJS server and hook up CI/CD to it to make it deployable.

## Getting Started

Generate a basic NodeJS server with a simple templated frontend using the following commands. Mainly inspired by the official [Express application generator](https://expressjs.com/en/starter/generator.html)

```bash
# Generate a sample Express JS app
# with the pug templating engine for the frontend code
# and store it in the "src" directory
npx express-generator --view=pug src
```

```bash
# Change to the "src" directory and
# install node dependencies with npm
cd src && npm install
```

```bash
# Start a local server at http://localhost:3000/ in Debug mode
DEBUG=src:* npm start
```

Add the following build and test (aka CI) steps to `.github/workflows/workflow.yml`

```yaml
name: express-generator app
on: [push]
jobs:
  build:
    name: Build
    runs-on: ubuntu-latest
    env:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      WORKING_DIRECTORY: ./src
    steps:
    # Checks-out your git repo under $GITHUB_WORKSPACE directory on the test VM,
    # so your workflow can access it.
    - uses: actions/checkout@v1

    # Downloads and caches node version 10.x
    - uses: actions/setup-node@v1
      with:
        node-version: '10.x'

    # Installs dependencies in the local node_modules directory
    - run: npm install
      working-directory: ${{ env.WORKING_DIRECTORY }}

    # Runs tests for the current version of the code
    - run: npm test
      working-directory: ${{ env.WORKING_DIRECTORY }}
```

## GitHub Docker Registry

Register by following the steps at https://github.com/features/packages

```bash
docker login docker.pkg.github.com --username <your-github-username>
```

## Prepare Dockerfile and push to Docker Registry

Create a basic `Dockerfile` in the `src/` directory which uses the node alpine image, exposes port 3000 and then runs `npm start` command to start the server on port 3000.

```Dockerfile
FROM node:12-alpine

EXPOSE 3000

CMD ["npm", "start"]
```

Append the following docker related steps to `.github/workflows/workflow.yml`

```bash
# Build and tag local image (needs Dockerfile)
- run: docker build --tag node-park .
    working-directory: ${{ env.WORKING_DIRECTORY }}

# Tag an image with the app name and version
# by referencing the docker build tagged as "node-park" above
- run: docker tag node-park docker.pkg.github.com/AgrimPrasad/repo/node-park:1.0.0
    working-directory: ${{ env.WORKING_DIRECTORY }}

# Push the docker image tagged above to the github packages registry
# This push to the github package registry
# is authenticated using the "GITHUB_TOKEN" secret token
# which is auto-generated for each build by GitHub Actions
- run: docker push docker.pkg.github.com/AgrimPrasad/repo/node-park:1.0.0
    working-directory: ${{ env.WORKING_DIRECTORY }}
```
