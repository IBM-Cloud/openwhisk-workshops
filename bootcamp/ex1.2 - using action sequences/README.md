# using action sequences

This exercise will explain how to use sequences to compose new "meta-actions" from existing actions on IBM Cloud Functions.

*Once you have completed this exercise, you will have…*

- **Understood what action sequences do and how they work.**
- **Created, deployed and invoked a sample action sequence.**
- **Used deliberate errors to stop processing the action sequence.**

Once this exercise is finished, we will be able to use action sequences on IBM Cloud Functions!

## Table Of Contents

* [Background](#background)
* [Action Sequences](#action-sequences)
  * [Creating Sequence Actions](#creating-sequence-actions)
  * [Handling Errors](#handling-errors)

## Instructions

### Background

Developers often want to build actions as reusable components which can be combined to build "higher-order" serverless functions.

For example, what if you have serverless functions to implement an external API and want to enforce HTTP authentication? Rather than manually replicating the same authentication code across all action handlers, you can build an authentication action which can be invoked programmatically at runtime.

Unfortunately, this approach does incur additional charges. The application will be charged twice when the authentication function is called, as the calling action has to sit idle waiting for the response from the authentication function.

*OpenWhisk has a special type of action which resolves this problem...*

### Action Sequences

OpenWhisk supports a kind of action called a "sequence". Sequence actions are created using a list of existing actions. When the sequence action is invoked, each action in executed in order of the action parameter list. Input parameters are passed to the first action in the sequence. Output from each function in the sequence is passed as the input to the next function and so on. The output from the last action in the sequence is returned as the response result.

Here's an example of defining a sequence (`my_sequence`) which will invoke three actions (`a, b, c`).

```
$ ic wsk action create my_sequence --sequence a,b,c
```

*Sequences behave like normal actions, you create, invoke and manage them as normal through the CLI.*

Using a sequence can remove the need to manually invoke actions and sit idle waiting for a response. In the example above, a sequence would be created for each serverless function in the application, combing the action doing the authentication followed by the actual request processing action.

Let's look at an example of using sequences.  

#### Node.js

1. Create the file (`funcs.js`) with the following contents:

```javascript
function split(params) {
  var text = params.text || ""
  var words = text.split(' ')
  return { words: words }
}

function reverse(params) {
  var words = params.words || []
  var reversed = words.map(word => word.split("").reverse().join(""))
  return { words: reversed }
}

function join(params) {
  var words = params.words || []
  var text = words.join(' ')
  return { text: text }
}
```

1. Create the following three actions

```
$ ic wsk action create split funcs.js --main split
$ ic wsk action create reverse funcs.js --main reverse
$ ic wsk action create join funcs.js --main join
```

#### Swift

1. Create the file (`funcs.swift`) with the following contents:

```swift
import Foundation

func split(args: [String:Any]) -> [String:Any] {
    var words = [String]()

    if let text = args["text"] as? String {
        words = text.components(separatedBy: " ")
    }
    return ["words": words]
}

func reverse(args: [String:Any]) -> [String:Any] {
    guard let words = args["words"] as? [String] else {
        return ["words": []]
    }

    let reversed = words.map { String($0.characters.reversed()) }

    return ["words": reversed]
}

func join(args: [String:Any]) -> [String:Any] {
    guard let words = args["words"] as? [String] else {
        return ["words": ""]
    }

    return ["words": words.joined(separator: " ")]
}
```

1. Create the following three actions

```
$ bx wsk action create split funcs.swift --main split
$ bx wsk action create reverse funcs.swift --main reverse
$ bx wsk action create join funcs.swift --main join
```

#### Creating Sequence Actions

1. Test each action to verify it is working

```
$ ic wsk action invoke split --result --param text "Hello world"
{
    "words": [
        "Hello",
        "world"
    ]
}
$ ic wsk action invoke reverse --result --param words '["hello", "world"]'
{
    "words": [
        "olleh",
        "dlrow"
    ]
}
$ ic wsk action invoke join --result --param words '["hello", "world"]'
{
    "text": "hello world"
}
```

1. Create the following action sequence.

```
$ ic wsk action create reverse_words --sequence split,reverse,join
```

1. Test out the action sequence.

```
$ ic wsk action invoke reverse_words --result --param text "hello world"
{
    "text": "olleh dlrow"
}
```

Using sequences is a great way to develop re-usable action components that can be joined together into "high-order" actions to create serverless applications. 

#### Handling Errors

What if you want to stop processing functions in a sequence? This might be due to an application error or because the pre-conditions to continue processing have not been met. In the authentication example above, we only want to proceed if the authentication check passes.

If any action within the sequences returns an error, the platform returns immediately. The action error is returned as the response. No further actions within the sequence will be invoked.

Let's look at how this work using the authentication example from above....

##### Node.js

1. Create the file (`funcs.js`) with the following contents:

```javascript
function authentication (params) {
  if (params.password !== 'mysecret') {
      throw new Error("Password incorrect.")
  }
    
  return params  
}

function rot13 (params) {
  const source = params.encrypted.replace(/[a-zA-Z]/g, function (c){
    return String.fromCharCode((c<="Z"?90:122)>=(c=c.charCodeAt(0)+13)?c:c-26)}
  )

  return { decrypted: source }
}
```

2. Create the following three actions

```
$ ic wsk action create authentication funcs.js --main authentication
$ ic wsk action create rot13 funcs.js --main rot13
$ ic wsk action create decrypt --sequence authentication,rot13
```

3. Test out the action sequence with an incorrect password.

```
$ ic wsk action invoke decrypt -r -p password "somethingelse"
{
    "error": "An error has occurred: Error: Password incorrect."
}
```

4. Test out the action sequence with the correct password.

```
$ bx wsk action invoke decrypt -r -p password "mysecret" -p encrypted "Uryyb Jbeyq"
{
    "decrypted": "Hello World"
}
```



🎉🎉🎉 **Sequences are an "advanced" OpenWhisk technique. Congratulations for getting this far! Now let's move on to something all together different, connecting functions to external event sources…** 🎉🎉🎉



#### 