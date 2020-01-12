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
# install node dependencies with yarn
cd src && yarn install
```

```bash
# Start a local server at http://localhost:3000/ in Debug mode
DEBUG=src:* yarn start
```
