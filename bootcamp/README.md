# TOC

TODO

# Preface

During this workshop you will learn how to develop serverless applications composed of loosely coupled microservice-like functions. You'll explore OpenWhisk's latest CLI and UI and become an OpenWhisk star by implementing a weather bot using IBM's Weather Company Data service and Slack. You will also investigate how to use the recently added API Gateway and web actions capabilities. Finally, you will find out how to package and deploy your entire serverless application together using the Serverless Framework
We wish you a lot of fun and success…

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
Actions
Actions are small stateless pieces of code that run on the OpenWhisk platform. 
Creating and invoking JavaScript actions
An action can be a simple JavaScript function that accepts and returns a JSON object.

First, use your editor of choice (for instance, download the Atom editor from https://atom.io/) to create a file called hello.js with the following content (snippet 01):
function main() {
    return { message: "Hello world" };
}
Save the file to wherever you want.

Next, open a terminal window, navigate to the directory where you stored the file hello.js and create an OpenWhisk action called hello referencing the function in hello.js:
$ wsk action create hello hello.js
ok: created action hello
Notice that you can always list the actions you have already created like this:
$ wsk action list
actions
hello                                        private nodejs:6
