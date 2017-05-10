# TOC

TODO

# Preface

During this workshop you will learn how to develop serverless applications composed of loosely coupled microservice-like functions. You'll explore OpenWhisk's latest CLI and UI and become an OpenWhisk star by implementing a weather bot using IBM's Weather Company Data service and Slack. You will also investigate how to use the recently added API Gateway and web actions capabilities. Finally, you will find out how to package and deploy your entire serverless application together using the Serverless Framework.

We wish you a lot of fun and success...

You can also find a PDF version of this workshop here: TODO

# Serverless Computing

**Serverless computing** refers to a model where the existence of servers is entirely abstracted away. I.e. that even though servers still exist, developers are relieved from the need to care about their operation. They are relieved from the need to worry about low-level infrastructural and operational details such as scalability, high-availability, infrastructure-security, and so forth. Hence, serverless computing is essentially about reducing maintenance efforts to allow developers to quickly focus on developing value-adding code.

Serverless computing simplifies developing cloud-native applications, especially microservice-oriented solutions that decompose complex applications into small and independent modules that can be easily exchanged.

Serverless computing does not refer to a specific technology; instead if refers to the concepts underlying the model described prior. Nevertheless, some promising solutions have recently emerged easing development approaches that follow the serverless model – such as OpenWhisk.

**OpenWhisk** is a cloud-first distributed event-based programming service and represents a Function-as-a-Service (FaaS) platform that allows you to execute code in response to an event.

It provides you with the previously mentioned serverless deployment and operations model, with a granular pricing model at any scale that provides you with exactly the resources – not more not less – you need and only charges you for code really running. It offers a flexible programming model. incl. support for languages like JavaScript, Swift, Python, and Java and even for the execution of custom logic via Docker containers. This allows small agile teams to reuse existing skills and to develop in a fit-for-purpose fashion. It also provides you with tools to declaratively chain together the building blocks you have developed. It is open and can run anywhere to avoid and kind of vendor lock-in.

In summary, OpenWhisk provides...
* ... a rich set of building blocks that they can easily glue/stitch together
*	... the ability to focus more on value-add business logic and less on low-level infrastructural and operational details
*	... the ability to easily chain together microservices to form workflows via composition

In summary, our value proposition and what makes us different is:
*	OpenWhisk hides infrastructural complexity allowing developers to focus on business logic
*	OpenWhisk takes care of low-level details such as scaling, load balancing, logging, fault tolerance, and message queues
*	OpenWhisk provides a rich ecosystem of building blocks from various domains (analytics, cognitive, data, IoT, etc.)
*	OpenWhisk is open and designed to support an open community
*	OpenWhisk supports an open ecosystem that allows sharing microservices via OpenWhisk packages
*	OpenWhisk allows developers to compose solutions using modern abstractions and chaining
*	OpenWhisk supports multiple runtimes including JavaScript, Swift, Python, Java, and arbitrary binary programs encapsulated in Docker containers
*	OpenWhisk charges only for code that runs

The OpenWhisk model consists of three concepts:
* `trigger`, a class of events that can happen,
*	`action`, an event handler -- some code that runs in response to an event, and
*	`rule`, an association between a trigger and an action.

Services define the events they emit as triggers, and developers define the actions to handle the events.

Developers only need to care about implementing the desired application logic - the system handles the rest.

# Prepare your engine!

A few important notes before you start:
*	When working through the lab you may see slightly different responses being returned from your CLI than those printed as part of these instructions.<br/>You do not need to worry about this. The reason is that you may use a different namespace than the one we used when generating this document; the differences will be minor and only result in some name-prefixing.
*	To ease your life you can download a digital copy of this document from here: https://ibm.box.com/v/serverlessconf-austin-17-ws
* Important remark: In case you have trouble with copying & pasting those code snippets longer than a single or a few lines (marked with snippet xx in the instructions, where xx corresponds to a number and defines the file name under which the snippet was stored) you can find copies of them here – easiest is to download the file all_snippets.zip: https://ibm.box.com/v/serverlessconf-austin-17-code
* Important remark for Windows users: Windows users are strongly advised to download Git (https://git-for-windows.github.io/) and to work from the Git bash. They are also advised to download cURL for Windows (https://curl.haxx.se/download.html)

In order to use OpenWhisk proceed as follows:
1. Open a browser window
2. Navigate to https://console.ng.bluemix.net/openwhisk/
3. Log-in with your Bluemix account
 Create one if you do not yet have one by clicking the sign-up link or by directly navigating to https://console.ng.bluemix.net/registration/
4. Click the  Download OpenWhisk CLI button
5. Follow steps 1 & 2 (you do not need to perform step 3), i.e. download the CLI for your particular platform and configure it by specifying your namespace and authorization key

# Start your engine!

The CLI (command line interface) allows you to work with OpenWhisk’s basic entities, i.e. to create actions, triggers, rules, and sequences. Hence, let’s learn how to work with the CLI.

## Actions

*Actions* are small stateless pieces of code that run on the OpenWhisk platform.

### Creating and invoking JavaScript actions

An action can be a simple JavaScript function that accepts and returns a JSON object.

First, use your editor of choice (for instance, download the Atom editor from https://atom.io/) to create a file called `hello.js` with the following content (snippet 01):

```javascript
function main() {
    return { message: "Hello world" };
}
```

Save the file to wherever you want.

Next, open a terminal window, navigate to the directory where you stored the file `hello.js` and create an OpenWhisk action called `hello` referencing the function in `hello.js`:

<pre>
$ wsk action create hello hello.js
<b>ok:</b> created action <b>hello</b>
</pre>

Notice that you can always list the actions you have already created like this:

<pre>
$ wsk action list
<b>actions</b>
hello                                  private nodejs:6
</pre>

To run an action use the ```wsk action invoke``` command. 
A blocking (i.e. synchronous) invocation waits until the action has completed and returned a result. It is indicated by the ```--blocking``` option (or ```-b``` for short):

<pre>
$ wsk action invoke --blocking hello
<b>ok:</b> invoked <b>hello</b> with id <b>dde9212e686f413bb90f22e79e12df74</b>
[...]
"response": {
    "result": {
        "message": "Hello world"
    }, 
    "status": "success",
    "success": true
},
[...]
</pre>

The above command outputs two important pieces of information:
*	the ```activation id``` (```dde9212e686f413bb90f22e79e12df74```)
*	the ```activation response``` which includes the result

The ```activation id``` can be used to retrieve the logs or the result of an (asynchronous) invocation at a future point in time. In case you forgot to note down an activation id you can retrieve the list of activations at any time:

<pre>
$ wsk activation list
<b>activations</b>
dde9212e686f413bb90f22e79e12df74             hello                                   
eee9212e686f413bb90f22e79e12df74             hello
</pre>
                                 
Notice that the list of ```activation ids``` is ordered with the most recent one first.

To obtain the result of a particular action invocation enter (notice that you need to replace the ```activation id``` shown below with the ```id``` you have received during the previous step):

To obtain the result of a particular action invocation enter (notice that you need to replace the ```activation id``` shown below with the ```id``` you have received during the previous step):

<pre>
$ wsk activation get dde9212e686f413bb90f22e79e12df74
<b>ok:</b> got activation <b>dde9212e686f413bb90f22e79e12df74</b>
[...]
"response": {
    "status": "success",
    "statusCode": 0,
    "success": true,
    "result": {
        "message": "Hello world"
    }
},
[...]
</pre>

You can delete an action like this:

<pre>
$ wsk action delete hello
<b>ok:</b> deleted action <b>hello</b>
</pre>

You can check whether an action was successfully deleted like this:

<pre>
$ wsk list
entities in namespace: <b>default</b>
<b>packages</b>
<b>actions</b>
<b>triggers</b>
<b>rules</b>
</pre>

### Creating and invoking asynchronous actions

JavaScript functions that run asynchronously need to return the activation result after the `main` function has returned. This can be accomplished by returning a `Promise`.

Again, use your editor of choice to create a file called `asyncAction.js` with the following content (snippet 02):

```javascript
function main(msg) {
    return new Promise(function(resolve, reject) {
        setTimeout(function() {
            resolve({ message: "Hello world" });
        }, 2000);
    })
}
```

Notice that the `main` function returns a `Promise` which indicates that the activation has not completed yet, but is expected to in the future.

In this particular example the `setTimeout` function waits for two seconds before calling the callback function. This represents the asynchronous code and goes inside the `Promise's` callback function.

The `Promise's` callback takes two arguments, `resolve` and `reject`, which are both functions. The call to `resolve` fulfills the `Promise` and indicates that the activation has completed normally.

A call to `reject` can be used to reject the `Promise` and signal that the activation has completed abnormally.

Next, run the following commands to create the action and invoke it:

<pre>
$ wsk action create asyncAction asyncAction.js
<b>ok:</b> created action <b>asyncAction</b>

$ wsk action invoke --blocking --result asyncAction
{
    "message": "Hello world" 
}
</pre>

Finally, run the following commands to fetch the activation log to see how long the activation took to complete:

<pre>
$ wsk activation list --limit 1 asyncAction
<b>activations</b>
b066ca51e68c4d3382df2d8033265db0       asyncAction

$ wsk activation get b066ca51e68c4d3382df2d8033265db0
{
    "start": 1455881628103,
    "end":   1455881648126,
    ...
}
</pre>

By comparing the start and end timestamps in the activation record, you can see that this activation took slightly over two seconds to complete.

### Passing parameters to actions

Actions may be invoked with several named parameters.

Change (and save) your `hello` action as follows (snippet 03):

```javascript
function main(msg) {
    return { message: "Hello, " + msg.name + " from " + msg.place };
}
```

Again, create the action:

<pre>
$ wsk action create hello hello.js
<b>ok:</b> created action <b>hello</b>
</pre>

You can pass named parameters as JSON payload or via the CLI:

<pre>
$ wsk action invoke -b hello -p name "Bernie" -p place "Vermont" --result
{
    "message": "Hello, Bernie from Vermont"
}
</pre>

Notice the use of the `--result` parameter (or `-r` for short; available for blocking invocations only) to immediately print the result of the action invocation without the need of an `activation id`.

### Setting default parameters

Recall that the `hello` action above took two parameters: the name of a person, and the place where he or she is from.

Rather than passing all the parameters to an action every time, you can *bind* certain parameters. Let’s bind the `place` parameter above so we have an action that defaults to the place `Vermont, CT`:

<pre>
$ wsk action create helloBindParams hello.js --param place "Vermont, CT"
<b>ok:</b> created action <b>helloBindParams</b>

$ wsk action invoke -b helloBindParams --param name "Bernie" --result
{
    "message": "Hello, Bernie from Vermont, CT"
}
</pre>

Notice that we did not need to specify the `place` parameter anymore when invoking the action. Moreover, bound parameters can still be overwritten by specifying the parameter value at invocation time. 

### Using actions to call an external API

So far, the examples have been self-contained functions. You can also create an action that calls an external API, of course.

The following example invokes the Yahoo Weather service to get the current conditions at a specific location.

Again, use your editor of choice to create a file called `weather.js` with the following content (snippet 04):
var request = require("request");

```javascript
function main(msg) {
    var location = msg.location || "Vermont";
    var url = "https://query.yahooapis.com/v1/public/yql?q=select item.condition from weather.forecast where woeid in (select woeid from geo.places(1) where text='" + location + "')&format=json";

    return new Promise(function(resolve, reject) {
        request.get(url, function(error, response, body) {
            if (error) {
                reject(error);
            }
            else {
                var condition = JSON.parse(body).query.results.channel.item.condition;
                var text = condition.text;
                var temperature = condition.temp;
                var output = "It is " + temperature + " degrees in " + location + " and " + text;

                resolve({msg: output});
            }
        });
    });
}
```

Notice that the action above uses the JavaScript request library to make an HTTP request to the Yahoo Weather API and to extract certain fields from the JSON result.

The example also shows the need for asynchronous actions. The action returns a Promise to indicate that the result of this action is not available yet when the function returns. Instead, the result is available in the callback after the HTTP call completes, and is passed as an argument to the `resolve` function just as we have seen it earlier.

Now, run the following commands to create the action and invoke it:

<pre>
$ wsk action create yahooWeather weather.js
<b>ok:</b> created action <b>yahooWeather</b>
$ wsk action invoke --blocking --result yahooWeather --param location "Brooklyn, NY"
{
    "msg": "It is 28 degrees in Brooklyn, NY and Cloudy"
}
</pre>

### Working with packages and sequencing actions

You can also create a composite action that chains together a *sequence* of actions.

This time we will use a set of actions that are shipped with OpenWhisk in a package called `/whisk.system/util`.

Generally, to reveal which packages are available out of the box run the following command:

<pre>
$ wsk package list /whisk.system
<b>packages</b>
/whisk.system/cloudant                 shared
/whisk.system/alarms                   shared
/whisk.system/watson                   shared
/whisk.system/websocket                shared
/whisk.system/weather                  shared
/whisk.system/system                   shared
/whisk.system/utils                    shared
/whisk.system/slack                    shared
/whisk.system/samples                  shared
/whisk.system/github                   shared
/whisk.system/pushnotifications        shared 
</pre>

Next, to reveal the list of entities in the `/whisk.system/cloudant` package run the following command:

<pre>
$ wsk package get --summary /whisk.system/cloudant
<b>package</b> /whisk.system/cloudant: Cloudant database service
   (<b>parameters</b>: BluemixServiceName host username password dbname includeDoc overwrite)
<b>action</b> /whisk.system/cloudant/read: Read document from database
<b>action</b> /whisk.system/cloudant/write: Write document to database
<b>feed</b> /whisk.system/cloudant/changes: Database change feed
[...]
</pre>

This output shows that the Cloudant package provides multiple actions, e.g. `read` and `write`, and a trigger feed called `changes`. The `changes` feed causes triggers to be fired when documents are added to the specified Cloudant database.

The Cloudant package also defines the parameters `username`, `password`, `host`, and `port`. These parameters must be specified for the actions and feeds to be meaningful. The parameters allow the actions to operate on a specific Cloudant account.

One way to access the actions in this package is by binding to them (you could alternatively access the package directly, of course). Bindings create a reference to the given package in your namespace. The advantage is that they allow you to access actions by typing `myUtil/actionName` instead of `/whisk.system/utils/actionName` every time.

Just run the following command:

<pre>
$ wsk package bind /whisk.system/utils myUtil
<b>ok:</b> created binding <b>myUtil</b>
</pre>

You now have access to the following actions:
* `myUtil/cat`: Action to transform lines of text into a JSON array
* `myUtil/head`: Action to return the first element in an array
* `myUtil/sort`: Action to sort an array of text

Let’s now create a composite action that is a sequence of the above actions, so that the result of one action is passed as arguments to the next action:

<pre>
$ wsk action create myAction --sequence myUtil/sort,myUtil/head
<b>ok:</b> created action <b>myAction</b>
</pre>

The composite action above will return the first element of a sorted array:

<pre>
$ wsk action invoke -b myAction -p lines '["c","b","a"]' --result
{
    "lines": [
        "a"
    ],
    "num": 1
}
</pre>

You can see that the first element of the sorted array is returned.

For learning how to create our own packages to enable services refer to the official documentation available here:
https://github.com/openwhisk/openwhisk/blob/master/docs/packages.md#creating-and-using-package-bindings

## Triggers and rules

*Triggers* represent a named "channel" for a stream of events.

Let’s create a trigger to send user location updates:

<pre>
$ wsk trigger create locationUpdate
<b>ok:</b> created trigger <b>locationUpdate</b>
</pre>

You can check that the trigger has been created like this:

<pre>
$ wsk trigger list
<b>triggers</b>
locationUpdate                         private
</pre>

So far we have only created a named channel to which events can be fired.

Let’s now fire a trigger by specifying its name and parameters:

<pre>
$ wsk trigger fire locationUpdate -p name "Donald" -p place "Washington, D.C"
<b>ok:</b> triggered <b>locationUpdate</b> with id <b>11ca88d404ca456eb2e76357c765ccdb</b>
</pre>

Events you fire to the `locationUpdate` trigger currently do not do anything. To be useful, we need to create a rule that associates the trigger with an action.

## Using rules to associate triggers and actions

*Rules* are used to associate a trigger with an action. Hence, every time a trigger event is fired, the action is invoked together with the events' parameters.

Let’s create a rule that calls the `hello` action whenever a location update is posted; required parameters are the name of the rule, the trigger, and the action:

<pre>
$ wsk rule create myRule locationUpdate hello
<b>ok:</b> created rule <b>myRule</b>
</pre>

Now, every time we fire a location update event, the `hello` action will be called with the corresponding event parameters:

<pre>
$ wsk trigger fire locationUpdate -p name "Donald" -p place "Washington, D.C"
<b>ok:</b> triggered <b>locationUpdate</b> with id <b>12ca88d404ca456eb2e76357c765ccdb</b>
</pre>

We can check that the action was really invoked by checking the most recent activations:

<pre>
$ wsk activation list hello
<b>activations</b>
12ca88d404ca456eb2e76357c765ccdb       hello
11ca88d404ca456eb2e76357c765ccdb       hello
</pre>

Notice that the use of the optional argument `hello` filters the result so that only invocations of the `hello` action are being displayed.

Again, to obtain the result of the particular action invocation enter (notice that you once again need to replace the `activation id` with the `id`you have received during the previous step):

<pre>
$ wsk activation result 12ca88d404ca456eb2e76357c765ccdb
{
    "result": "Hello, Donald from Washington, D.C."
}
</pre>

We can finally see that the `hello` action received the event payload and returned the expected string.

## Uploading dependencies

As an alternative to writing all your action code in a single JavaScript source file, you can implement an action as an `npm` package.

The structure is supposed to look as follows:

First, define a `package.json` like this (snippet 05):

```json
{
    "name": "my-action",
    "version": "1.0.0",
    "main": "index.js",
    "dependencies" : {
     "left-pad" : "1.1.3"
    }
}
```

Next, define an `index.js` like this (snippet 06):

```javascript
function myAction(args) {
    const leftPad = require("left-pad");
    const lines = args.lines || [];

    return { padded: lines.map(l => leftPad(l, 30, ".")) }
}

exports.main = myAction;
```

Notice that the action is exposed through `exports.main`; hence, the action handler itself can have any name, as long as it conforms to the usual signature of accepting an object and returning an object (or a `Promise` of an object).

Then, to create an OpenWhisk action from this package follow the following procedure:

First, install all dependencies locally:

<pre>
$ npm install
</pre>

Next, create a .zip archive containing all files (including all dependencies):

<pre>
$ zip -r action.zip *
</pre>

Next, create the action:

<pre>
$ wsk action create packageAction --kind nodejs:6 action.zip
ok: created action packageAction
</pre>

Notice that when creating an action from a .zip archive using the CLI tool, you must explicitly provide a value for the `--kind` flag.

You can finally invoke the action like any other:

<pre>
$ wsk action invoke --blocking --result packageAction --param lines '["and now", "for something completely", "different"]'
{
    "padded": [
        ".......................and now",
        "......for something completely",
        ".....................different"
    ]
}
</pre>

Finally, notice that while most `npm` packages install JavaScript sources on `npm install`, some also install and compile binary artifacts. The archive file upload currently does not support binary dependencies but rather only JavaScript dependencies. Action invocations may fail if the archive includes binary dependencies.

# Boost your engine!

As an alternative to the CLI, you can also use the OpenWhisk UI, esp. the visual code editor, to work with OpenWhisk’s basic entities, i.e. to create actions, triggers, rules, and sequences. Hence, let’s learn how to work with the OpenWhisk UI.

## Getting started with the OpenWhisk UI

First, open a browser window, navigate to https://console.ng.bluemix.net/openwhisk/ and click `Develop`.

The OpenWhisk UI is comprised of the following sections:

1.	My Actions
   The My Actions section lists all actions you have created previously.  
   Clicking an action loads its code into the code editor.  
   Hovering over an action lets a trash bin appear allowing to delete the action.  
   At this point in time you should at least see the hello action we have created earlier.  

2.	My Sequences  
   The My Sequences section lists all the sequences you have created previously.  
   Clicking a sequence loads its model into the visual modeler.  
   Hovering over a sequence lets a trash bin appear allowing to delete the sequence.  

3.	My Rules  
   The My Rules section lists all the rules you have created previously.  
   Clicking a rule loads its model into the visual modeler.  
   Hovering over a rule lets a trash bin appear allowing to delete the rule.  
   At this point in time you should at least see the myRule rule we have created earlier.  

4.	My Triggers  
   The My Triggers section lists all the triggers you have created previously.  
   Hovering over a trigger lets a flash icon appear allowing to fire the trigger as well as a trash bin allowing to delete the trigger.



xxx



