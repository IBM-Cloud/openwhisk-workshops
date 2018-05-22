# managing actions with packages

This exercise will introduce the concepts needed to create and use packages with IBM Cloud Functions.

*Once you have completed this exercise, you will have…*

- **Learnt how to find public packages.**
- **Understood how to use public package actions and bindings.**
- **Created and shared custom packages.**

Once this exercise is finished, we will be able to create and share actions using packages using IBM Cloud Functions!

## Table Of Contents

* [Background](#background)
* [Browsing packages](#browsing-packages)
* [Invoking actions in a package](#invoking-actions-in-a-package)
* [Creating and using package bindings](#creating-and-using-package-bindings)
* [Creating new packages](#creating-new-packages)
* [Sharing packages](#sharing-packages)

## Instructions

### Background

In IBM Cloud Functions, you can use *packages* to bundle together related actions and even share them with others. Packages can only contain *actions*. *Triggers* and *rules* are not supported at the moment. Package nesting is not allowed, i.e. packages cannot contain other packages.

Packages also support default parameters. Package parameters are automatically passed into actions during invocations. This provides a convenient method to manage service credentials needed with multiple actions.

The following sections describe how to use packages with actions…

### Browsing packages

IBM Cloud Functions comes pre-installed with a number of public packages, which include trigger feeds used to register triggers with event sources.

Actions in public packages can be used by anyone, the caller pays the invocation cost.

Using `bx wsk` CLI you can get a list of packages in a namespace, list the entities in a package and get a description of the entities within a package.

1. Get a list of packages in the `/whisk.system` namespace.

```
$ bx wsk package list /whisk.system
packages
/whisk.system/combinators                                              shared
/whisk.system/websocket                                                shared
/whisk.system/watson-translator                                        shared
/whisk.system/watson-textToSpeech                                      shared
/whisk.system/github                                                   shared
/whisk.system/utils                                                    shared
/whisk.system/watson-speechToText                                      shared
/whisk.system/slack                                                    shared
/whisk.system/samples                                                  shared
/whisk.system/weather                                                  shared
/whisk.system/pushnotifications                                        shared
/whisk.system/alarms                                                   shared
/whisk.system/cloudant                                                 shared
/whisk.system/messaging                                                shared
```

2. Get a list of entities in the `/whisk.system/cloudant` package.

```
$ bx wsk package get --summary /whisk.system/cloudant
package /whisk.system/cloudant: Cloudant database service
   (parameters: *apihost, *bluemixServiceName, *dbname, *host, overwrite, *password, *username)
 action /whisk.system/cloudant/read: Read document from database
   (parameters: dbname, id, params)
  action /whisk.system/cloudant/write: Write document in database
   (parameters: dbname, doc)
 feed   /whisk.system/cloudant/changes: Database change feed
   (parameters: dbname, filter, query_params)
   ....
```

**Note:** Parameters listed under the package with a prefix `*` are predefined, bound parameters. Parameters without a `*` are those listed under the annotations for each entity. Furthermore, any parameters with the prefix `**` are finalized bound parameters. This means that they are immutable, and cannot be changed by the user. Any entity listed under a package inherits specific bound parameters from the package. To view the list of known parameters of an entity belonging to a package, you will need to run a `get —summary` of the individual entity.

This output shows that the Cloudant package provides the actions `read` and `write`, and the trigger feed called `changes`. The `changes` feed causes triggers to be fired when documents are added to the specified Cloudant database.

The Cloudant package also defines the parameters `username`, `password`, `host`, and `dbname`. These parameters must be specified for the actions and feeds to be meaningful. The parameters allow the actions to operate on a specific Cloudant account, for example.

3. Get a description of the `/whisk.system/cloudant/read` action.

```
$ bx wsk action get --summary /whisk.system/cloudant/read
action /whisk.system/cloudant/read: Read document from database
   (parameters: *apihost, *bluemixServiceName, *dbname, *host, *id, params, *password, *username)
```

This output shows that the Cloudant `read` action lists eight parameters, seven of which are predefined. These include the database and document ID to retrieve.

### Invoking actions in a package

You can invoke actions in a package, just as with other actions. The next few steps show how to invoke the `greeting` action in the `/whisk.system/samples` package with different parameters.

1. Get a description of the `/whisk.system/samples/greeting` action.

```
$ bx wsk action get --summary /whisk.system/samples/greeting
action /whisk.system/samples/greeting: Returns a friendly greeting
   (parameters: name, place)
```

Notice that the `greeting` action takes two parameters: `name` and `place`.

2. Invoke the action without any parameters.

```
$ bx wsk action invoke --result /whisk.system/samples/greeting
{
    "payload": "Hello, stranger from somewhere!"
}
```

The output is a generic message because no parameters were specified.

3. Invoke the action with parameters.

```
$ bx wsk action invoke --result /whisk.system/samples/greeting --param name Bernie --param place Vermont
{
    "payload": "Hello, Bernie from Vermont!"
}
```

Notice that the output uses the `name` and `place` parameters that were passed to the action.

### Creating and using package bindings

Although you can use the entities in a package directly, you might find yourself passing the same parameters to the action every time. You can avoid this by binding to a package and specifying default parameters. These parameters are inherited by the actions in the package.

For example, in the `/whisk.system/cloudant` package, you can set default `username`, `password`, and `dbname` values in a package binding and these values are automatically passed to any actions in the package.

In the following simple example, you bind to the `/whisk.system/samples` package.

1. Bind to the `/whisk.system/samples` package and set a default `place` parameter value.

```
$ bx wsk package bind /whisk.system/samples valhallaSamples --param place Valhalla
ok: created binding valhallaSamples
```

2. Get a description of the package binding.

```
$ bx wsk package get --summary valhallaSamples
package /namespace/valhallaSamples: Returns a result based on parameter place
   (parameters: *place)
 action /namespace/valhallaSamples/helloWorld: Demonstrates logging facilities
    (parameters: payload)
 action /namespace/valhallaSamples/greeting: Returns a friendly greeting
    (parameters: name, place)
 action /namespace/valhallaSamples/curl: Curl a host url
    (parameters: payload)
 action /namespace/valhallaSamples/wordCount: Count words in a string
    (parameters: payload)
```

Notice that all the actions in the `/whisk.system/samples` package are available in the `valhallaSamples` package binding.

3. Invoke an action in the package binding.

```
$ bx wsk action invoke --result valhallaSamples/greeting --param name Odin
{
    "payload": "Hello, Odin from Valhalla!"
}
```

Notice from the result that the action inherits the `place` parameter you set when you created the `valhallaSamples` package binding.

4. Invoke an action and overwrite the default parameter value.

```
$ bx wsk action invoke --result valhallaSamples/greeting --param name Odin --param place Asgard
{
    "payload": "Hello, Odin from Asgard!"
}
```

Notice that the `place` parameter value that is specified with the action invocation overwrites the default value set in the `valhallaSamples` package binding.

### Creating new packages

Custom packages can be used to group your own actions, manage default parameters and share entities with other users.

Let's demonstrate how to do this now using the `bx wsk` CLI tool…

1. Create a package called "custom".

  ```
  $ bx wsk package create custom
  ok: created package custom
  ```

2. Get a summary of the package.

  ```
  $ bx wsk package get --summary custom
  package /myNamespace/custom
     (parameters: none defined)
  ```
  Notice that the package is empty.

3. Create a file called `identity.js` that contains the following action code. This action returns all input parameters.

  ```javascript
  function main(args) { return args; }
  ```

4. Create an `identity` action in the `custom` package.

  ```
  $ bx wsk action create custom/identity identity.js
  ok: created action custom/identity
  ```
  Creating an action in a package requires that you prefix the action name with a package name.

5. Get a summary of the package again.

  ```
  $ bx wsk package get --summary custom
  ```
  ```
  package /myNamespace/custom
    (parameters: none defined)
   action /myNamespace/custom/identity
    (parameters: none defined)
  ```

  You can see the `custom/identity` action in your namespace now.

6. Invoke the action in the package.

  ```
  $ bx wsk action invoke --result custom/identity
  {}
  ```


*You can set default parameters for all the entities in a package. You do this by setting package-level parameters that are inherited by all actions in the package. To see how this works, try the following example:*

1. Update the `custom` package with two parameters: `city` and `country`.

  ```
  $ bx wsk package update custom --param city Austin --param country USA
  ok: updated package custom
  ```

2. Display the parameters in the package and action, and see how the `identity` action in the package inherits parameters from the package.

  ```
  $ bx wsk package get custom
  ok: got package custom
  ...
  "parameters": [
      {
          "key": "city",
          "value": "Austin"
      },
      {
          "key": "country",
          "value": "USA"
      }
  ]
  ...
  ```

  ```
  $ bx wsk action get custom/identity
  ok: got action custom/identity
  ...
  "parameters": [
      {
          "key": "city",
          "value": "Austin"
      },
      {
          "key": "country",
          "value": "USA"
      }
  ]
  ...
  ```

3. Invoke the identity action without any parameters to verify that the action indeed inherits the parameters.

  ```
  $ bx wsk action invoke --result custom/identity
  ```
  ```
  {
      "city": "Austin",
      "country": "USA"
  }
  ```

4. Invoke the identity action with some parameters. Invocation parameters are merged with the package parameters; the invocation parameters override the package parameters.

  ```
  $ bx wsk action invoke --result custom/identity --param city Dallas --param state Texas
  ```
  ```
  {
      "city": "Dallas",
      "country": "USA",
      "state": "Texas"
  }
  ```


### Sharing packages

After the actions and feeds that comprise a package are debugged and tested, the package can be shared with all OpenWhisk users. Sharing the package makes it possible for the users to bind the package, invoke actions in the package, and author OpenWhisk rules and sequence actions.

1. Share the package with all users:

  ```
  $ bx wsk package update custom --shared yes
  ok: updated package custom
  ```

2. Display the `publish` property of the package to verify that it is now true.

  ```
  $ bx wsk package get custom
  ok: got package custom
  ...
  "publish": true,
  ...
  ```


Others can now use your `custom` package, including binding to the package or directly invoking an action in it. Other users must know the fully qualified names of the package to bind it or invoke actions in it. Actions and feeds within a shared package are _public_. If the package is private, then all of its contents are also private.

1. Get a description of the package to show the fully qualified names of the package and action.

  ```
  $ bx wsk package get --summary custom
  ```
  ```
  package /myNamespace/custom: Returns a result based on parameters city and country
     (parameters: *city, *country)
   action /myNamespace/custom/identity
     (parameters: none defined)
  ```

  In the previous example, you're working with the `myNamespace` namespace, and this namespace appears in the fully qualified name.

