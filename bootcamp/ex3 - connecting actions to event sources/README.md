# connecting actions to event sources

This exercise introduces concepts (triggers and rules) used by the platform to integrate external event providers.

*Once you have completed this exercise, you will have…*

- **Understood how event sources are integrated into the platform.**
- **Created example triggers and bound to actions using rules.**
- **Tested connecting triggers to external event sources.**

Once this exercise is finished, we can start to develop event-driven serverless applications using IBM Cloud Functions!

## Table Of Contents

* [Background](#background)
  * [Triggers](#triggers)
  * [Rules](#rules)
* [Creating Triggers](#creating-triggers)
* [Using Rules](#using-rules)
  * [Creating Rules](#creating-rules)
  * [Testing Rules](#testing-rules)
  * [Disabling Rules](#disabling-rules)
* [Connecting Trigger Feeds](#connecting-trigger-feeds)

## Instructions

### Background

Serverless applications are often described as "event-driven" because you can connect serverless functions to external event sources, like message queues and database changes. When these external events fire, the serverless functions are automatically invoked, without any manual intervention.

In the previous example, we've been manually invoking actions using the command-line. Let's move onto connecting your actions to external event sources. OpenWhisk supports multiple external event sources like CouchDB, Apache Kafka, a cron-based scheduler and more.

Before we jump into the details, let's review some concepts which explain how this feature works in Apache OpenWhisk.

#### Triggers

Triggers are a named channel for a class of events. The following are examples of triggers:

- A trigger of location update events.
- A trigger of document uploads to a website.
- A trigger of incoming emails.

Triggers can be *fired* (activated) by using a dictionary of key-value pairs. Sometimes this dictionary is referred to as the *event*. As with actions, each firing of a trigger results in an activation ID.

Triggers can be explicitly fired by a user or by an external event source. A *feed* is a convenient way to configure an external event source to fire trigger events that can be consumed by IBM Cloud Functions. Examples of feeds include the following:

- CouchDB data change feed that fires a trigger event each time a document in a database is added or modified.
- A Git feed that fires a trigger event for every commit to a Git repository.

🎉🎉🎉 **Triggers are an implementation of the Observer pattern. Instances of triggers can be fired with parameters. Next we need to find out how to register observers. ** 🎉🎉🎉

#### Rules

A rule associates one trigger with one action, with every firing of the trigger causing the corresponding action to be invoked with the trigger event as input.

With the appropriate set of rules, it's possible for a single trigger event to invoke multiple actions, or for an action to be invoked as a response to events from multiple triggers.

For example, consider a system with the following actions:

- `classifyImage` action that detects the objects in an image and classifies them.
- `thumbnailImage` action that creates a thumbnail version of an image.

Also, suppose that there are two event sources that are firing the following triggers:

- `newTweet` trigger that is fired when a new tweet is posted.
- `imageUpload` trigger that is fired when an image is uploaded to a website.

You can set up rules so that a single trigger event invokes multiple actions, and have multiple triggers invoke the same action:

- `newTweet -> classifyImage` rule.
- `imageUpload -> classifyImage` rule.
- `imageUpload -> thumbnailImage` rule.

The three rules establish the following behavior: images in both tweets and uploaded images are classified, uploaded images are classified, and a thumbnail version is generated.

🎉🎉🎉 **Just remember rules allow you to register an observer on a trigger. That's all we need to know for now, let's look at using these new concepts… ** 🎉🎉🎉

### Creating Triggers

*Triggers* represent a named "channel" for a stream of events.

Let's create a trigger to send *location updates*:

```
$ bx wsk trigger create locationUpdate
ok: created trigger locationUpdate
```

You can check that the trigger has been created like this:

```
$ bx wsk trigger list
triggers
locationUpdate                         private
```

So far we have only created a named channel to which events can be fired.

Let's now fire the trigger by specifying its name and parameters:

```
$ bx wsk trigger fire locationUpdate -p name "Donald" -p place "Washington, D.C"
ok: triggered locationUpdate
```

Triggers also support default parameters. Firing this trigger without any parameters will pass in the default values.

```
$ bx wsk trigger update locationUpdate -p name "Donald" -p place "Washington, D.C"
ok: updated trigger locationUpdate
$ bx wsk trigger fire locationUpdate
ok: triggered locationUpdate
```

Events you fire to the `locationUpdate` trigger currently do not do anything. To be useful, we need to create a rule that associates the trigger with an action.

🎉🎉🎉 **That was easy? Let's keep going by connecting actions to triggers…** 🎉🎉🎉

### Using Rules

Rules are used to associate a trigger with an action. Each time a trigger event is fired, the action is invoked with the event parameters.

#### Creating Rules

As an example, create a rule that calls the `hello` action whenever a location update is triggered.

1. Check the `hello` action exists and responds to the correct event parameters.

```
$ bx wsk action invoke --result hello --param name Bernie --param place Vermont
{
    "payload": "Hello, Bernie from Vermont"
}
```

2. Check the trigger exists.

```
$ bx wsk trigger get locationUpdate
ok: got trigger a
{
    "namespace": "user@host.com_dev",
    "name": "locationUpdate",
    "version": "0.0.1",
    "limits": {},
    "publish": false
}
```

3. Create the rule using the command-line. The three parameters are the name of the rule, the trigger, and the action.

```
$ bx wsk rule create myRule locationUpdate hello
ok: created rule myRule
```

4. Retrieve rule details to show the trigger and action bound by this rule.

```
$ bx wsk rule get myRule
ok: got rule myRule
{
    "namespace": "user@host.com_dev",
    "name": "myRule",
    "version": "0.0.1",
    "status": "active",
    "trigger": {
        "name": "locationUpdate",
        "path": "user@host.com_dev"
    },
    "action": {
        "name": "hello",
        "path": "user@host.com_dev"
    },
    "publish": false
}
```

#### Testing Rules

1. Fire the `locationUpdate` trigger. Each time that you fire the trigger with an event, the `hello` action is called with the event parameters.

```
$ bx wsk trigger fire locationUpdate --param name Donald --param place "Washington, D.C."
ok: triggered /_/locationUpdate with id 5c153c01d76d49dc953c01d76d99dc34
```

2. Verify that the action was invoked by checking the activations list.

```
$ bx wsk activation list --limit 2
activations
5ee74025c2384f30a74025c2382f30c1 hello
5c153c01d76d49dc953c01d76d99dc34 locationUpdate
```

We can see the trigger activation (`5c153c01d76d49dc953c01d76d99dc34`) is recorded, followed by the `hello` action activation (`5ee74025c2384f30a74025c2382f30c1`).

3. Retrieving the trigger activation record will show the actions and rules invoked from this activation.

```
$ bx wsk activation result 5ee74025c2384f30a74025c2382f30c1
{
   "payload": "Hello, Donald from Washington, D.C."
}
```

You can see that the hello action received the event payload and returned the expected string.

Activation records for triggers store the rules and actions fired for an event and the event parameters.

```
$ bx wsk activation result 5c153c01d76d49dc953c01d76d99dc34
{
    "name": "Donald",
    "place": "Washington, D.C."
}
$ bx wsk activation logs 5c153c01d76d49dc953c01d76d99dc34
{"statusCode":0,"success":true,"activationId":"5ee74025c2384f30a74025c2382f30c1","rule":"user@host.com_dev/myRule","action":"user@host.com_dev/hello"}
```

You can create multiple rules that associate the same trigger with different actions. 

**Can you create another trigger and rule that calls the `hello` action? 🤔**

You can also use rules with sequences. For example, one can create an action sequence `recordLocationAndHello`that is activated by the rule `anotherRule`.

```
$ bx wsk action create recordLocationAndHello --sequence /whisk.system/utils/echo,hello
$ bx wsk rule create anotherRule locationUpdate recordLocationAndHello
```

#### Disabling Rules

Rules are enabled upon creation but can be disabled and re-enabled using the command-line. 

1. Disable the rule connecting the `locationUpdate` trigger and `hello` action.

```
$ bx wsk rule disable myRule
```

2. Fire the trigger again.

```
$ bx wsk trigger fire locationUpdate --param name Donald --param place "Washington, D.C."
ok: triggered /_/locationUpdate with id 53f85c39087d4c15b85c39087dac1571
```

3. Check the activation list there are no new activation records.

```
$ bx wsk activation list --limit 2
activations
5ee74025c2384f30a74025c2382f30c1 hello
5c153c01d76d49dc953c01d76d99dc34 locationUpdate
```

The latest activation records were from the previous example.

*Activation records for triggers are only recorded when they are bound to an active rule.*

🎉🎉🎉 **Right, now we have a way to connect actions to events in OpenWhisk, how do we connect triggers to event sources like messages queues? Enter trigger feeds…** 🎉🎉🎉

### Connecting Trigger Feeds

Trigger feeds allow you to connect triggers to external event sources. Event sources will fire registered triggers each time an event occurs. Here's a list of the event sources currently supported on IBM Cloud Functions: <https://github.com/apache/incubator-openwhisk/blob/master/docs/catalog.md>

This example shows how to use a feed in the [Alarms package](https://github.com/apache/incubator-openwhisk-package-alarms/blob/master/README.md) to fire a trigger every minute, which invokes an action using a rule.

1. Get a description of the feeds in the `/whisk.system/alarms` package.

```
$ bx wsk package get --summary /whisk.system/alarms
package /whisk.system/alarms: Alarms and periodic utility
   (parameters: *apihost, *trigger_payload)
 feed   /whisk.system/alarms/interval: Fire trigger at specified interval
   (parameters: minutes, startDate, stopDate)
 feed   /whisk.system/alarms/once: Fire trigger once when alarm occurs
   (parameters: date, deleteAfterFire)
 feed   /whisk.system/alarms/alarm: Fire trigger when alarm occurs
   (parameters: cron, startDate, stopDate)
```

2. Retrieve the details for the `alarms/interval` feed.

```
$ bx wsk action get --summary /whisk.system/alarms/interval
action /whisk.system/alarms/interval: Fire trigger at specified interval
   (parameters: *apihost, *isInterval, minutes, startDate, stopDate, *trigger_payload)
```

The `/whisk.system/alarms/interval` feed has the following parameters we need to pass in:

- `minutes`:  An integer representing the length of the interval (in minutes) between trigger fires.
- `trigger_payload`: The payload parameter value to set in each trigger event.

3. Create a trigger that fires every minute using this feed.

```
$ bx wsk trigger create everyMinute --feed /whisk.system/alarms/interval -p minutes 1 -p trigger_payload "{\"name\":\"Mork\", \"place\":\"Ork\"}"
ok: invoked /whisk.system/alarms/interval with id b2b4c3cb38224f44b4c3cb38228f44be
...
ok: created trigger everyMinute
```

4. Connect this trigger to the `hello` action with a new rule.

```
$ bx wsk rule create everyMinuteRule everyMinute hello
ok: created rule everyMinuteRule
```

5. Check that the action is being invoked every minute by polling for activation logs.

```
$ bx wsk activation poll
Activation: 'hello' (b2fc4b00c7be4143bc4b00c7bed1431c)
[]
Activation: 'everyMinute' (cec7eb38739c4d4287eb38739ccd42ef)
[
    "{\"statusCode\":0,\"success\":true,\"activationId\":\"b2fc4b00c7be4143bc4b00c7bed1431c\",\"rule\":\"james.thomas@uk.ibm.com_dev/everyMinuteRule\",\"action\":\"james.thomas@uk.ibm.com_dev/hello\"}"
]
```

You should see activations every minute the trigger and the action. The action receives the parameters `{"name":"Mork", "place":"Ork"}` on every invocation.

**IMPORTANT: Let's delete the trigger and rule or this event will be running forever!**

```
$ bx wsk trigger delete everyMinute
$ bx wsk rule delete everyMinuteRule
```

🎉🎉🎉 **Understanding triggers and rules allows you to build event-driven applications on OpenWhisk. Create some actions, hook up events and let the platform take care of everything else, what could be easier?** 🎉🎉🎉
