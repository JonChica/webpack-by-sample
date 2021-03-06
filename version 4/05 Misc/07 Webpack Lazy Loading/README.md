# 06 Lazy Loading

In this demo we will load bundles asynchronously via npm.

We will start from sample _00 intro/05 jquery_.

Summary steps:

- Prerequisites:
  - nodejs packages must be installed
  - (Optional) starting point _00 intro/05 jquery_
- Install @babel/plugin-syntax-dynamic-import package via npm
- Configure the .babelrc configuration file
- Change the synchronous module import on students.js and load the averageService module lazily.
- Add the container div on index.html

# Steps to build it

## Prerequisites

Prerequisites, you will need to have nodejs installed (at least v 8.9.2) on your computer. If you want to follow this guide steps you will need to take as starting point sample _00 intro/05 jquery_.

## Steps

- `npm install` to install previous sample packages:

```bash
npm install
```

- Let's start by downloading the @babel/plugin-syntax-dynamic-import library via npm. In this case we will execute the following command on the command prompt: `npm install --save-dev @babel/plugin-syntax-dynamic-import`.

```
npm install --save-dev @babel/plugin-syntax-dynamic-import
```

It automatically adds that entry to our _package.json_.

### ./package.json

```diff
  "devDependencies": {
    "@babel/cli": "^7.2.3",
    "@babel/core": "^7.3.4",
+    "@babel/plugin-syntax-dynamic-import": "^7.2.0",
    "@babel/preset-env": "^7.3.4",
    "babel-loader": "^8.0.5",
    "html-webpack-plugin": "^3.2.0",
    "webpack": "^4.29.6",
    "webpack-cli": "^3.2.3",
    "webpack-dev-server": "^3.2.1"
  },
```

- Now we will configure the babel plugin syntax dynamic import at the plugin section of the _.babelrc_ file:

### ./.babelrc

```diff
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns": "entry"
      }
    ]
  ],
+  "plugins": ["@babel/plugin-syntax-dynamic-import"]
}
```

- Now it's ready to be used. Just to test it, let's change the synchronous import on the students.js file. Let's import the averageService module lazily when button clicked.

### ./students.js

```diff
- import {getAvg} from "./averageService";
$('body').css('background-color', 'lightSkyBlue');
- const scores = [90, 75, 60, 99, 94, 30];
- const averageScore = getAvg(scores);
- const messageToDisplay = `average score ${averageScore}`;
- document.write(messageToDisplay);
```

- Create getComponent function load the averageService module lazily, and return div element async with the average score:

```diff
$('body').css('background-color', 'lightSkyBlue');

+ const getComponent = () =>
+   import(/* webpackChunkName "averageService" */ "./averageService").then(
+     averageServiceModule => {
+       const scores = [90, 75, 60, 99, 94, 30];
+       const averageScore = averageServiceModule.getAvg(scores);
+       const messageToDisplay = `average score ${averageScore}`;
+
+       const element = document.createElement("div");
+       element.innerText = messageToDisplay;
+
+       return element;
+     }
+   );
```

- Create handleOnClick function will call the getComponent function and append the average score to container:

```diff
...
+ const handleOnClick = () => {
+   getComponent().then(element =>
+     document.getElementById("container").append(element)
+   );
+ };
```

- Create a button to handle on click event and load the average score:

```diff
...
+ const button = document.createElement("button");
+ button.innerText = "Lazy loading sample";
+ button.onclick = handleOnClick;
+ document.getElementById("container").append(button);
```

Then, our _students.js_ file look like this:

### ./students.js

```
$("body").css("background-color", "lightSkyBlue");

const getComponent = () =>
  import(/* webpackChunkName "averageService" */ "./averageService").then(
    averageServiceModule => {
      const scores = [90, 75, 60, 99, 94, 30];
      const averageScore = averageServiceModule.getAvg(scores);
      const messageToDisplay = `average score ${averageScore}`;

      const element = document.createElement("div");
      element.innerText = messageToDisplay;

      return element;
    }
  );

const handleOnClick = () => {
  getComponent().then(element =>
    document.getElementById("container").append(element)
  );
};

const button = document.createElement("button");
button.innerText = "Lazy loading sample";
button.onclick = handleOnClick;
document.getElementById("container").append(button);

```

- Finally we need to add the _div_ container in _index.html_:

### ./index.html

```diff

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Webpack 4.x by sample</title>
  </head>
  <body>
    Hello Webpack 4!
+    <div id="container"></div>
  </body>
</html>

```

## Running and Testing it

- Now we can just execute the app (`npm start`) and check how the averageService bundle load on handle button click event (checking the browser network dev tool).

```bash
npm start
```

# About Lemoncode

We are a team of long-term experienced freelance developers, established as a group in 2010.
We specialize in Front End technologies and .NET. [Click here](http://lemoncode.net/services/en/#en-home) to get more info about us.

For the LATAM/Spanish audience we are running an Online Front End Master degree, more info: http://lemoncode.net/master-frontend
