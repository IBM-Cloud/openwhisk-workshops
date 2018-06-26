# creating and invoking actions

This exercise will introduce the concepts needed to create and use actions with IBM Cloud Functions.

*Once you have completed this exercise, you will have…*

- **Created and invoked actions.**
- **Understood how to pass parameters to actions.**
- **Created actions which return asynchronous results.**

Once this exercise is finished, we will be able to create simple serverless functions using IBM Cloud Functions!

## Table Of Contents

* [Background](#background)
* [Creating And Invoking Actions](#creating-and-invoking-actions)
  * [Creating Node.js actions](#creating-node.js-actions)
  * [Creating Swift actions](#creating-swift-actions)
  * [Invoking actions](#invoking-actions)
* [Using Action Parameters](#using-action-parameters)
  * [Passing parameters to an action (Node.js)](#passing-parameters-to-an-action-(node.js))
  * [Passing parameters to an action (Swift)](#passing-parameters-to-an-action-(swift))
  * [Setting default parameters](#setting-default-parameters)
* [Retrieving Action Logs](#retrieving-action-logs)
  * [Creating Activation Logs](#creating-activation-logs)
  * [Accessing Activation Logs](#accessing-activation-logs)
  * [Polling Activation Logs](#polling-activation-logs)
* [Calling Other Actions](#calling-other-actions)
  * [Proxy Example](#proxy-example)
* [Asynchronous Actions](#asynchronous-actions)
  * [Returning asynchronous results (Node.js)](#returning-asynchronous-results-(node.js))
  * [Returning asynchronous results (Swift)](#returning-asynchronous-results-(swift))
  * [Using actions to call an external API](#using-actions-to-call-an-external-api)
* [EXERCISES](#exercises)

## Instructions

### Background

Actions are stateless code snippets that run on the OpenWhisk platform. An action can be written as a JavaScript, Swift, PHP, or Python function, a Java method, static binary or a custom executable packaged in a Docker container. For example, an action can be used to detect the faces in an image, respond to a database change, aggregate a set of API calls, or post a Tweet.

Actions can be explicitly invoked, or run in response to an event. In either case, each run of an action results in an activation record that is identified by a unique activation ID. The input to an action and the result of an action are a dictionary of key-value pairs, where the key is a string and the value a valid JSON value. Actions can also be composed of calls to other actions or a defined sequence of actions.

### Creating And Invoking Actions

#### Creating Node.js actions

Review the following steps and examples to create your first JavaScript action.

1. Create a JavaScript file with the following content. For this example, the file name is 'hello.js'.

```javascript
function main() {
    return {payload: 'Hello world'};
}
```

The JavaScript file might contain additional functions. However, by convention, a function called `main` must exist to provide the entry point for the action.

2. Create an action from the following JavaScript function. For this example, the action is called 'hello'.

```
$ ic wsk action create hello hello.js
ok: created action hello
```

3. List the actions that you have created:

```
$ ic wsk action list
actions
hello       private
```

You can see the `hello` action you just created.

#### Creating Swift actions

Review the following steps and examples to create your first Swift action.

1. Create a Swift file with the following content. For this example, the file name is 'hello.swift'.

```swift
func main(args: [String:Any]) -> [String:Any] {
	return [ "payload" : "Hello world" ]
}
```

The Swift file might contain additional functions. However, by convention, a function called `main` must exist to provide the entry point for the action.

1. Create an action from the following Swift function. For this example, the action is called 'hello'.

```
$ ic wsk action create hello hello.swift
ok: created action hello
```

1. List the actions that you have created:

```
$ ic wsk action list
actions
/user@host.com_dev/hello                                     private swift:3.1.1
```

You can see the `hello` action you just created.

#### Invoking Actions

**After you create your action, you can run it on IBM Cloud Functions with the 'invoke' command.** 

You can invoke actions with a *blocking* invocation (i.e., request/response style) or a *non-blocking* invocation by specifying a flag (`—blocking`) on the command-line. A blocking invocation request will *wait* for the activation result to be available. The wait period is the lesser of 60 seconds or the action's configured [time limit](https://github.com/apache/incubator-openwhisk/blob/master/docs/reference.md#per-action-timeout-ms-default-60s). The result of the activation is returned if it is available within the wait period. Otherwise, the activation continues processing in the system and an activation ID is returned so that one may check for the result later, as with non-blocking requests (see [here](https://github.com/apache/incubator-openwhisk/blob/master/docs/actions.md#watching-action-output) for tips on monitoring activations).

1. Invoke the `hello` action using the command-line as a blocking activation.

```
$ ic wsk action invoke --blocking hello
```

```
ok: invoked hello with id 44794bd6aab74415b4e42a308d880e5b
```

```
{
    "result": {
        "payload": "Hello world"
    },
    "status": "success",
    "success": true
}
```

The command outputs two important pieces of information:

- The activation ID (`44794bd6aab74415b4e42a308d880e5b`)
- The invocation result if it is available within the expected wait period

The result in this case is the string `Hello world` returned by the JavaScript function. The activation ID can be used to retrieve the logs or result of the invocation at a future time.

If you don't need the action result right away, you can omit the `—blocking` flag to make a non-blocking invocation. You can get the result later by using the activation ID.

2. Invoke the `hello` action using the command-line as a non-blocking activation.

```
$ ic wsk action invoke hello
ok: invoked hello with id 6bf1f670ee614a7eb5af3c9fde813043
```

3. Retrieve the activation result

```
$ ic wsk activation result 6bf1f670ee614a7eb5af3c9fde813043
{
    "payload": "Hello world"
}
```

To access the most recent activation record, activation results or activation logs, use the `--last` or `-l` flag. 

4. Run the following command to get your last activation result.

```
$ ic wsk activation result --last
{
    "payload": "Hello world"
}
```

Note that you should not use an activation ID with the flag `--last`.

5. If you forget to record the activation ID, you can get a list of activations ordered from the most recent to the oldest. Run the following command to get a list of your activations:

```
$ ic wsk activation list
activations
44794bd6aab74415b4e42a308d880e5b         hello
6bf1f670ee614a7eb5af3c9fde813043         hello
```

🎉🎉🎉 **Great work, you have now learned how to create, deploy and invoke your own serverless functions on IBM Cloud Functions. What about passing data into actions? Let's find out more…** 🎉🎉🎉

### Using Action Parameters

Event parameters can be passed to the action when it is invoked. Let's look at a sample action which uses the parameters to calculate the return values.

#### Passing parameters to an action (Node.js)

1. Update the file `hello.js` with the following following content:

```
function main(params) {
    return {payload:  'Hello, ' + params.name + ' from ' + params.place};
}
```

The input parameters are passed as a JSON object parameter to the `main` function. Notice how the `name` and `place` parameters are retrieved from the `params` object in this example.

2. Update the `hello` action with the new source code.

```
$ ic wsk action update hello hello.js
```

#### Passing parameters to an action (Swift)

1. Update the file `hello.swift` with the following following content:

```swift
func main(args: [String:Any]) -> [String:Any] {
    if let name = args["name"] as? String, let place = args["place"] as? String {
        return [ "payload" : "Hello \(name) from \(place)" ]
    } else {
        return [ "payload" : "Hello stranger from nowhere" ]
    }
}
```

The input parameters are passed as a Dictionary (`String:Any`) to the `main` function. Functions must return a Dictionary of the same type with response values. Notice how the `name` and `place` parameters are retrieved from the `args` dictionary in this example.

1. Update the `hello` action with the new source code.

```
$ ic wsk action update hello hello.swift
```

#### Invoking action with parameters

When invoking actions through the command-line, parameter values can be passed as through explicit command-line parameters `—param/-p` or using an input file containing raw JSON.

3. Invoke the `hello` action using explicit command-line parameters.

```
$ ic wsk action invoke --result hello --param name Bernie --param place Vermont
{
    "payload": "Hello, Bernie from Vermont"
}
```

4. Create a file (`parameters.json`) containing the following JSON. 

```
{
    "name": "Bernie",
    "place": "Vermont"
}
```

5. Invoke the `hello` action using parameters from a JSON file.

```
$ ic wsk action invoke --result hello --param-file parameters.json
{
    "payload": "Hello, Bernie from Vermont"
}
```

*Notice the use of the `--result` option: it implies a blocking invocation where the CLI waits for the activation to complete and then displays only the result. For convenience, this option may be used without `--blocking` which is automatically inferred.*

##### nested parameters

Parameter values can be any valid JSON value, including nested objects. Let's update our action to use child properties of the event parameters.

6. Create the `hello-person` action with the following source code.

##### Node.js

```javascript
function main(params) {
    return {payload:  'Hello, ' + params.person.name + ' from ' + params.person.place};
}
```

##### Swift

```swift
func main(args: [String:Any]) -> [String:Any] {
    if let person = args["person"] as? [String: String], let name = person["name"] as? String, let place = person["place"] as? String {
        return [ "payload" : "Hello \(name) from \(place)" ]
    } else {
        return [ "payload" : "Hello stranger from nowhere" ]
    }
}
```

Now the action expects a single `person` parameter to have fields `name` and `place`. 

7. Invoke the action with a single `person` parameter that is valid JSON.

```
$ ic wsk action invoke --result hello-person -p person '{"name": "Bernie", "place": "Vermont"}'
```

The result is the same because the CLI automatically parses the `person` parameter value into the structured object that the action now expects:

```
{
    "payload": "Hello, Bernie from Vermont"
}
```

🎉🎉🎉 **That was pretty easy, huh? We can now pass parameters and access these values in our serverless functions. What about parameters that we need but don't want to manually pass in every time? Guess what, we have a trick for that…** 🎉🎉🎉

#### Setting default parameters

Actions can be invoked with multiple named parameters. Recall that the `hello` action from the previous example expects two parameters: the *name* of a person, and the *place* where they're from.

Rather than pass all the parameters to an action every time, you can bind default parameters. Default parameters are stored in the platform and automatically passed in during each invocation. If the invocation includes the same event parameter, this will overwrite the default parameter value.

Let's use the `hello` action from our previous example and bind a default value for the `place` parameter. 

1. Update the action by using the `—param` option to bind default parameter values.

```
$ ic wsk action update hello --param place Vermont
```

Passing parameters from a file requires the creation of a file containing the desired content in JSON format. The filename must then be passed to the `-param-file` flag:

Example parameter file called parameters.json:

```json
{
    "place": "Vermont"
}
```

```
$ ic wsk action update hello --param-file parameters.json
```

2. Invoke the action, passing only the `name` parameter this time.

```
$ ic wsk action invoke --result hello --param name Bernie
```

```
{
    "payload": "Hello, Bernie from Vermont"
}
```

Notice that you did not need to specify the place parameter when you invoked the action. Bound parameters can still be overwritten by specifying the parameter value at invocation time.

3. Invoke the action, passing both `name` and `place` values. The latter overwrites the value that is bound to the action.

```
$ ic wsk action invoke --result hello --param name Bernie --param place "Washington, DC"
{  
    "payload": "Hello, Bernie from Washington, DC"
}
```

🎉🎉🎉 **Default parameters are awesome for handling parameters like authentication keys for APIs. Letting the platform pass them in automatically means you don't have include these keys in invocation requests or include them in the action source code. Neat, huh?** 🎉🎉🎉

### Retrieving Action Logs

Application logs are essential to debugging production issues. In IBM Cloud Functions, all output written by  to `stdout` and `stderr` by actions is available in the activation records.

#### Creating Activation Logs

1. Create a new action (`logs`) from the following source files.

##### Node.js

```javascript
function main(params) {
    console.log("function called with params", params)
    console.error("this is an error message")
    return { result: true }
}
```

```
$ ic wsk action create logs logs.js
ok: created action logs
```

##### Swift

````javascript
import Foundation

var standardError = FileHandle.standardError

extension FileHandle : TextOutputStream {
    public func write(_ string: String) {
        guard let data = string.data(using: .utf8) else { return }
        self.write(data)
    }
}

func main(args: [String:Any]) -> [String:Any] {    
    print("function called with params", args)
    print("this is an error message",to:&standardError)
    return ["result": true]
}
````

```
$ ic wsk action create logs logs.swift
ok: created action logs
```

2. Invoke the `logs` action to generate some logs.

```
$ ic wsk action invoke -r logs -p hello world
{
    "result": true
}
```

#### Accessing Activation Logs

1. Retrieve activation record to verify logs have been recorded.

```
$ ic wsk activation get --last
ok: got activation 9fc044881705479580448817053795bd
{    
    ...   
    "logs": [
        "2018-03-02T09:49:03.021Z stdout: function called with params { hello: 'world' }",
        "2018-03-02T09:49:03.021Z stderr: this is an error message"
    ],
    ...
}
```

3. Logs can also be retrieved without showing the whole activation record, using the `activation logs` command.

```
$ ic wsk activation logs --last
2018-03-02T09:49:03.021404683Z stdout: function called with params { hello: 'world' }
2018-03-02T09:49:03.021816473Z stderr: this is an error message
```

#### Polling Activation Logs

Activation logs can be monitored in real-time, rather than manually retrieving individual activation records.

1. Run the following command to monitor logs from the `logs` actions.

```
$ ic wsk activation poll
Enter Ctrl-c to exit.
Polling for activation logs
```

2. In another terminal, run the following command multiple times.

```
$ ic wsk action invoke logs -p hello world
ok: invoked /_/logs with id 0e8d715393504f628d715393503f6227
```

3. Check the output from the `poll` command to see the activation logs.

```

Activation: 'logs' (ae57d06630554ccb97d06630555ccb8b)
[
    "2018-03-02T09:56:17.8322445Z stdout: function called with params { hello: 'world' }",
    "2018-03-02T09:56:17.8324766Z stderr: this is an error message"
]

Activation: 'logs' (0e8d715393504f628d715393503f6227)
[
    "2018-03-02T09:56:20.8992704Z stdout: function called with params { hello: 'world' }",
    "2018-03-02T09:56:20.8993178Z stderr: this is an error message"
]

Activation: 'logs' (becbb9b0c37f45f98bb9b0c37fc5f9fc)
[
    "2018-03-02T09:56:44.6961581Z stderr: this is an error message",
    "2018-03-02T09:56:44.6964147Z stdout: function called with params { hello: 'world' }"
]
```

### Calling Other Actions

Using serverless platforms to implement reusable functions means you will often want to invoke one action from another. IBM Cloud Functions provides a [RESTful API](http://petstore.swagger.io/?url=https://raw.githubusercontent.com/openwhisk/openwhisk/master/core/controller/src/main/resources/apiv1swagger.json) to invoke actions programmatically.

Rather than having to [manually construct the HTTP requests](https://github.com/apache/incubator-openwhisk/blob/master/docs/rest_api.md#actions) to invoke actions from within the IBM Cloud Functions runtime, client libraries are pre-installed to make this easier.

These libraries make it simple to invoke other actions, fire triggers and access all other platform services.

#### Proxy Example

Let's look an example of creating a "proxy" action which invokes another action if a "password" is present in the input parameters.

1. Create the following new action `proxy` from the following source files.

##### Node.js

```javascript
var openwhisk = require('openwhisk');

function main(params) {
  if (params.password !== 'secret') {
    throw new Error("Password incorrect!")
  }

  var ow = openwhisk();
  return ow.actions.invoke({name: "hello", blocking: true, result: true, params: params})
}
```

*The JavaScript library for Apache OpenWhisk is here: [https://github.com/apache/incubator-openwhisk-client-js/](https://github.com/apache/incubator-openwhisk-client-js/).* *This library is pre-installed in the IBM Cloud Functions runtime and does not need to be manually included.*

```
$ ic wsk action create proxy proxy.js
```

##### Swift

```swift
func main(args: [String:Any]) -> [String:Any] {
    guard let password = args["password"] as? String, password == "secret" else {
        return ["error": "Password is incorrect!"]
    }

    let resp = Whisk.invoke(actionNamed: "hello", withParameters: args, blocking: true)

    guard let response = resp["response"] as? [String: Any], let result = response["result"] as? [String: Any] else {
        return ["error": "Unable to parse response result."]
    }

    return result
}
```

This Swift file is included in the runtime to provide a small utility for invoking platform APIs: [https://github.com/apache/incubator-openwhisk-runtime-swift/blob/master/core/swift3.1.1Action/spm-build/_Whisk.swift](https://github.com/apache/incubator-openwhisk-runtime-swift/blob/master/core/swift3.1.1Action/spm-build/_Whisk.swift)

```
$ ic wsk action create proxy proxy.swift
```

2. Invoke the proxy with an incorrect password.

```
$ ic wsk action invoke proxy -p password wrong -r
{
    "error": "Password is incorrect!"
}
```

3. Invoke the proxy with the correct password.

```
$ ic wsk action invoke proxy -p password secret -p name Bernie -p place Vermont -r
{
    "greeting": "Hello Bernie from Vermont"
}
```

4. Review the activations list to show both actions were invoked.

```
$ ic wsk activation list -l 2
activations
8387302c81dc4d2d87302c81dc4d2dc6 hello
e0c603c242c646978603c242c6c6977f proxy
```

*These libraries can also be used to invoke triggers to fire events from actions.*

### Asynchronous actions

#### Returning asynchronous results (Node.js)

JavaScript functions that run asynchronously may need to return the activation result after the `main` function has returned. You can accomplish this by returning a Promise in your action.

1. Save the following content in a file called `asyncAction.js`.

```
function main(args) {
     return new Promise(function(resolve, reject) {
       setTimeout(function() {
         resolve({ done: true });
       }, 2000);
    })
 }
```

Notice that the `main` function returns a Promise, which indicates that the activation hasn't completed yet, but is expected to in the future.

The `setTimeout()` JavaScript function in this case waits for two seconds before calling the callback function. This represents the asynchronous code and goes inside the Promise's callback function.

The Promise's callback takes two arguments, resolve and reject, which are both functions. The call to `resolve()`fulfills the Promise and indicates that the activation has completed normally.

A call to `reject()` can be used to reject the Promise and signal that the activation has completed abnormally.

#### Returning asynchronous results (Swift)

Swift functions that use asynchronous processing must block returning until those asynchronous results are available. Here's an example which prints a message after an interval to demonstrate this approach.

1. Save the following content in a file called `asyncAction.swift`.

```swift
import Foundation
import CoreFoundation

func main(args: [String:Any]) -> [String:Any] {
  Timer.scheduledTimer(withTimeInterval: 2, repeats: false)  { _ in
    print("Timer fired.")
    CFRunLoopStop(CFRunLoopGetMain())
  }
  print("Waiting on timer...")
  CFRunLoopRun()
  return [ "done" : true ]
}
```

Using the `CFRunLoopRun` function, we can wait on the asynchronous timer to fire. Once the timer fires, the run loop is stopped.

Other approaches to blocking on asynchronous results in Swift include semaphores, dispatch barriers and other concurrency primitives in the language.

#### Testing asynchronous timeouts

1. Run the following commands to create the action and invoke it:

```
$ ic wsk action create asyncAction asyncAction.js
// OR....
$ ic wsk action create asyncAction asyncAction.swift
```

```
$ ic wsk action invoke --result asyncAction
{
    "done": true
}
```

Notice that you performed a blocking invocation of an asynchronous action.

2. Fetch the last activation log to see how long the async activation took to complete:

```
$ ic wsk activation get --last
{
     "duration": 2026,
     ...
}
```

Checking the `duration` field in the activation record, you can see that this activation took slightly over two seconds to complete.

**Actions have a `timeout` parameter that enforces the maximum duration for an invocation.** This value defaults to 60 seconds and can be changed to a maximum of 5 minutes.

Let's look at what happens when an action invocation takes longer than the `timeout`. 

1. Update the `asyncAction` timeout to 1000ms.

```
$ ic wsk action update asyncAction --timeout 1000
ok: updated action asyncAction
```

2. Invoke the action and block on the result.

```
$ ic wsk action invoke asyncAction --result
{
    "error": "The action exceeded its time limits of 1000 milliseconds."
}
```

The error message returned by the platform indicates the action didn't return a response within the user-specified timeout. If we change the `timeout` back to a value higher than the artificial delay in the function, it should work again.

1. Update the `asyncAction` timeout to 10000ms.

```
$ ic wsk action update asyncAction --timeout 10000
ok: updated action asyncAction
```

2. Invoke the action and block on the result.

```
$ ic wsk action invoke asyncAction --result
{
    "done": true
}
```

🎉🎉🎉 **Asynchronous actions are necessary for calling other APIs or cloud services. Don't forget about that timeout though! Let's have a look at using an asynchronous action to invoke another API…** 🎉🎉🎉

### EXERCISES

Let's try out your new SERVERLESS SUPERPOWERS 💪 to build a real serverless function. 

***Can you create a new action which takes a price and currency and returns a message contains the equivalent amount of Bitcoin?***

#### Input

The action should take two parameters, `amount` and `currency`. `amount` is a number, `currency` is a three letter [currency code](https://www.iban.com/currency-codes.html).

#### Output

Return the following JSON template with the input parameters and bitcoin amounts.

```json
{ 
  "amount": 1.5,
  "message": "10000 USD is worth 1.5 bitcoins."
}
```

If either the `amount` or `currency` parameters are missing, return an error with details.

#### Resources

This [Coindesk API](https://api.coindesk.com/v1/bpi/currentprice.json) returns real-time bitcoin prices. This includes rates for the `USD`, `GBP` and `EUR` currencies.

This [Currency Converter API](https://free.currencyconverterapi.com/) returns exchanges rates between traditional currencies.

Use the `request-promise` [module](https://www.npmjs.com/package/request-promise) to make external API requests. It comes pre-installed in the runtime.

#### Tests

Test with currencies in the Coindeck API response.

```
$ ic wsk action invoke bitcoin -r -p amount 1000 -p currency USD
{
    "amount": "0.160814",
    "label": "1000 USD is worth 0.160814 bitcoins."
}
$ ic wsk action invoke bitcoin -r -p amount 1000 -p currency EUR
{
    "amount": "0.187235",
    "label": "1000 EUR is worth 0.187235 bitcoins."
}
$ ic wsk action invoke bitcoin -r -p amount 1000 -p currency GBP
{
    "amount": "0.213012",
    "label": "1000 GBP is worth 0.213012 bitcoins."
}
```

Test with currencies not in the Coindeck API response.

```
$ ic wsk action invoke bitcoin -r -p amount 1000 -p currency AUD
{
    "amount": "0.10814",
    "label": "1000 AUD is worth 0.10814 bitcoins."
}
```

Test with missing parameters.

```
$ ic wsk action invoke bitcoin -r -p amount 1000
{
    "error": "Missing mandatory argument: currency"
}
$ ic wsk action invoke bitcoin -r  -p currency GBP
{
    "error": "Missing mandatory argument: amount"
}
```
