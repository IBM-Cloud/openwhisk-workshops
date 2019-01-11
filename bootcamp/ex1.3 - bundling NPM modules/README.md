# bundling NPM modules

*This additional exercise is for developers using the Node.js runtime. If this isn't you, feel free to skip…*

This exercise will explain how to use external NPM modules from Node.js actions on IBM Cloud Functions.

*Once you have completed this exercise, you will have…*

- **Understood how to use zip files for actions.**
- **Packaged Node.js module as an action.**
- **Added NPM modules to deployment package.**

Once this exercise is finished, you will know how to bundle external dependencies when creating actions.

## Table Of Contents

* [Background](#background)
* [Packaging actions as Node.js modules](#packaging-actions-as-node.js-modules)
* [Adding NPM dependencies](#adding-npm-dependencies)

## Instructions

### Background

OpenWhisk [supports creating Node.js Actions from a zip file](https://github.com/openwhisk/openwhisk/blob/master/docs/actions.md#packaging-an-action-as-a-nodejs-module). The archive file will be extracted into the runtime environment by the  platform. This allows us to split microservice logic across multiple  files, use third-party [NPM modules](https://www.npmjs.com/) or include non-JavaScript assets (configuration files, images, HTML files).

The Node.js runtime for OpenWhisk comes [built-in with a number of popular NPM modules](https://raw.githubusercontent.com/ibm-functions/runtime-nodejs/master/nodejs8/package.json). These libraries can be used without having to manually package up those dependences in the action handler. If you want to use additional libraries, these files needs to be bundled within a zip file. Actions can be created from Node.js modules, provided in that zip file.

### Packaging actions as Node.js modules

Let’s look at a “Hello World” example of registering a serverless  function from a zip file.

Our archive will contain two files, the [package descriptor](https://docs.npmjs.com/files/package.json) and a JavaScript file.

1. Create a minimal `package.json` file required for loading a module from a directory.

```json
{
  "main": "my_file.js"
}
```

2. Create a  `my_file.js` file with the following code. Action handlers are exposed through the `main` property on the `exports` object. Handlers must [implementsthe Action interface.](https://github.com/openwhisk/openwhisk/blob/master/docs/actions.md#creating-and-invoking-javascript-actions)

```javascript
exports.main = function (params) {
  return {result: "Hello World"};
};
```

3. Creating a zip file from the current directory.

```
$  zip -r action.zip *
```

4. Create a new action from the `wsk` CLI. The `kind` property needs to be set when using zip files.

```
$ ibmcloud wsk action create hello_world --kind nodejs:default action.zip
```

5. Invoke the action.

```
$ ibmcloud wsk action invoke hello_world --result
{
    "result": "Hello world"
}
```

*When this Action is invoked, the archive will be unzipped into a  temporary directory. OpenWhisk loads the directory as a Node.js module and invokes the function property on the module for each invocation.*

### Adding NPM dependencies

***This step requires you to have the [Node.js](https://nodejs.org/en/) and [NPM](https://www.npmjs.com/) development tools installed.***

Let's build a sample action which uses an external NPM dependency, not available in the runtime environment.

1. Create a `package.json` with the following JSON in: 

```json
{
  "name": "my-action",
  "main": "index.js",
  "dependencies" : {
    "left-pad" : "1.1.3"
  }
}
```

2. Create an `index.js  `with the following code in:

```javascript
function myAction(args) {
    const leftPad = require("left-pad")
    const lines = args.lines || [];
    return { padded: lines.map(l => leftPad(l, 30, ".")) }
}

exports.main = myAction;
```

Note that the action is exposed through `exports.main`; the action handler itself can have any name, as long as it conforms to the usual signature of accepting an object and returning an object (or a `Promise` of an object). Per Node.js convention, you must either name this file `index.js` or specify the file name you prefer as the `main`property in package.json.

To create an OpenWhisk action from this package:

3. Install first all dependencies locally

```
$ npm install
```

4. Create a `.zip` archive containing all files (including all dependencies):

```
$ zip -r action.zip *
```

> Please note: Using the Windows Explorer action for creating the zip file will result in an incorrect structure. OpenWhisk zip actions must have `package.json` at the root of the zip, while Windows Explorer will put it inside a nested folder. The safest option is to use the command line `zip` command as shown above.

5. Create the action:

```
$ ibmcloud wsk action create packageAction action.zip --kind nodejs:default
```

Note that when creating an action from a `.zip` archive using the CLI tool, you must explicitly provide a value for the `--kind` flag.

6. You can invoke the action like any other:

```
$ ibmcloud wsk action invoke --result packageAction --param lines "[\"and now\", \"for something completely\", \"different\" ]"
{
    "padded": [
        ".......................and now",
        "......for something completely",
        ".....................different"
    ]
}
```

Finally, note that while most `npm` packages install JavaScript sources on `npm install`, some also install and compile binary artifacts. The archive file upload currently does not support binary dependencies but rather only JavaScript dependencies. Action invocations may fail if the archive includes binary dependencies.

🎉🎉🎉 **Node.js has a huge ecosystem of third-party packages which can be used on OpenWhisk with this method. The platform does come built-in with popular packages listed [here](https://github.com/apache/incubator-openwhisk/blob/master/docs/reference.md#javascript-runtime-environments) This method also works for any other runtime.** 🎉🎉🎉
