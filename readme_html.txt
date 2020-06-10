
React + Typescript + Webpack

https://dev.to/jacopobonta
Jan 19, 2020. ・4 min read

Ensure you have Node.js installed on your system.
Step #01
Setup 🗂

First things first, create a directory and init the project

mkdir react_typescript_webpack
cd react_typescript_webpack
npm init -y

Create a second directory inside the first one and call it src, here we are going to place our source code

mkdir src
touch src/index.tsx

Create also an entry point for the application called index.tsx.
Dependencies ⛓

A dependency is another code, usually in the form of a library, that we need to have in order to create a project.
Roughly speaking there are two types of dependency:

    Runtime dependencies: that one we need at runtime, usually we interact with them in the code.
    Build dependencies: all that tools that we use to build the project. Most of the time we only feed configurations to this type of dependency.

I use npm as a package manager so copy-paste this code in the package.json

{
  "name": "react_typescript_webpack",
  "version": "1.0.0",
  "description": "Build UI that scales",
  "scripts": {
    "build": "webpack --config webpack.config.js"
  },
  "keywords": [
    "react",
    "typescript",
    "webpack"
  ],
  "author": "bonta.jacopo@gmail.com",
  "license": "ISC",
  "dependencies": {
    "react": "^16.12.0",
    "react-dom": "^16.12.0"
  },
  "devDependencies": {
    "@types/react": "^16.8.24",
    "@types/react-dom": "^16.0.5",
    "@types/webpack": "4.1.4",
    "ts-loader": "^6.2.1",
    "typescript": "^3.4.3",
    "webpack": "^4.41.5",
    "webpack-cli": "^3.3.10"
  }
}

This file keeps a list of the dependencies of the project.

We set our runtime dependencies (react and react-dom) in the "dependencies" object while all the build dependencies in the "devDependencies" object.

    Note on @types: they are declaration files used for describing the shape of an existing JavaScript object to TypeScript.

Note also the "build" script that runs the webpack command using a specific configuration file.

To install the dependencies run the command

npm install

Step #02
Configuration files 🧾
TypeScript 🎢

Let's starts with TypeScript and create a tsconfig.json file in the project root directory:

touch tsconfig.json

{
  "compilerOptions": {
    "outDir": "./dist",
    "target": "es5",
    "module": "es6",
    "jsx": "react",
    "noImplicitAny": true,
    "allowSyntheticDefaultImports": true
  }
}

This file tells the transpiler how to transpile TypeScript code to JavaScript.
Webpack 📦

Webpack puts your code with all its dependencies in a single file called bundle.
It does this by looking at your code and resolving all dependencies starting from the entry point (index.tsx) and recursively in any file for each import statement encountered.

Create a configuration file for webpack

touch webpack.config.js

const path = require('path');

module.exports = {
    mode: 'none',
    entry: {
        app: path.join(__dirname, 'src', 'index.tsx')
    },
    target: 'web',
    resolve: {
        extensions: ['.ts', '.tsx', '.js']
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                use: 'ts-loader',
                exclude: '/node_modules/'
            }
        ],
    },
    output: {
        filename: '[name].js',
        path: path.resolve(__dirname, 'dist')
    }
}

The ts-loader tells webpack to run typescript for each .ts or .tsx file so it is converted to JavaScript before it gets into the final bundle.
Step #03
The code 📝

Now, the fun part!
App 🤖

All starts from index.tsx, the app entry point.
The only place where we really need react-dom.

import React from 'react';
import ReactDOM from 'react-dom';

import App from './App';

ReactDOM.render(<App />, document.querySelector('#root'));

Basically we are saying Render the App component in an HTML Element with id = 'root'.

We don't have already the App component so let's create one.
Always in your src directory

touch App.tsx

import React from 'react';

export default function App()
{
    return <h1>Hello, world!</h1>
}

A simple components that display "Hello, world!" inside an H1 tag.

At this point we have almost done, we can run the build command and see the bundle app.js appear in a directory called dist.

npm run build

Now we have a file in plain JavaScript of our app that can be included inside a web page.
Webpage 🌎

Let's create it in the src direcotry:

touch index.html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>React Typescript Webpack</title>
</head>
<body>
    <!-- React app root element -->
    <div id="root"></div>
</body>
</html>

Note that no script was referenced. This is because this file act as a template for every build using a plugin for webpack.
html-webpack-plugin 🔌

A plugin for webpack that automatically includes an html page with a reference to the bundle in the output folder.

npm install --save-dev html-webpack-plugin

and edit the webpack.config.js like this

const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    mode: 'none',
    entry: {
        app: path.join(__dirname, 'src', 'index.tsx')
    },
    target: 'web',
    resolve: {
        extensions: ['.ts', '.tsx', '.js']
    },
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                use: 'ts-loader',
                exclude: '/node_modules/'
            }
        ],
    },
    output: {
        filename: '[name].js',
        path: path.resolve(__dirname, 'dist')
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: path.join(__dirname, 'src', 'index.html')
        })
    ]
}

Done!

Now hit

npm run build

and you are ready to go.

You only need a server to serve your web app through the dist directory.
A dirty but fast solution would be

npm i -g live-server
cd dist
live-server

It is nice because it auto-reloads on every build.

Remember, you have an entire src directory for writing and structure your dream app ✨ For me is useful to put all the app components inside a directory called components to keep things clear.

I hope you enjoyed!
References

    React
    Typescript
    Webpack
    html-webpack-plugin

