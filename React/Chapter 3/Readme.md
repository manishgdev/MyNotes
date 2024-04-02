# Chapter 3 - Webpack

- [Chapter 3 - Webpack](#chapter-3---webpack)
  - [What is Webpack ?](#what-is-webpack-)
    - [Installation \& Configuration](#installation--configuration)
  - [new topic](#new-topic)

## What is Webpack ?
  - Module bundler
  - Organize javascripts, creates bundle of JS
  - can run babel also via configuration
  - avoid global modules as package.json are not having the dependencies required to run the project (avoding to install live-server, babel-cli etc)

### Installation & Configuration
  - Install  `npm install webpack` or `npm install webpack@3.1.0` for specific version
  - Configuration
    - `webpack.config.js` needs to be created in root of the project
    - define entry of the app
    - define where the output of the file is going to be
    - Sample Config
        ```
        const path = require('path');

        module.exports = {
        entry: './src/app.js',
        output: {
            path: path.join(__dirname, 'public'),
            filename: 'bundle.js'
        }
        };

        ```
    - output path should be an absolute path, we can not use relative path
## new topic