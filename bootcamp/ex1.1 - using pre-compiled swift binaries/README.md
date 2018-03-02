# using pre-compiled swift binaries

*This additional exercise is for developers using the Swift runtime. If this isn't you, feel free to skip…*

This exercise will explain how to improve the performance of Swift actions on IBM Cloud Functions.

*Once you have completed this exercise, you will have…*

- **Understood how cold activations affect performance.**
- **Created and deployed Swift binaries for the IBM Cloud Functions runtime.**
- **Checked the impact of performance of binary actions.**

Once this exercise is finished, we will be able to deploy Swift binaries to IBM Cloud Functions!

## Table Of Contents

* [Background](#background)
* [Cold Versus Warm Activations](#cold-versus-warm-activations)
* [Deploying Swift Binaries](#deploying-swift-binaries)
  * [Compiling Swift Binaries](#compiling-swift-binaries)
  * [Creating Binary Deployments](#creating-binary-deployments)
  * [Checking Performance](#checking-performance)

## Instructions

### Background

Swift is a static language, which uses a compiler to produce binaries to execute application code. When creating actions from Swift source files, the compilation phase happens during the first action execution. This compilation step introduces a delay for action invocation times, whilst the build process is completed.

*Once the binary has been produced, it is cached for future invocations to improve performance.* 

Activations will trigger the compilation stage are known as "cold activations", whereas those which re-use the cached binary are known as "warm activations".

If response time performance and predictability is a critical requirement for your application, you can significantly reduce the "cold activation" delay by removing the need for the platform to run the Swift build process….

Let's have a look at how this works!

### Cold Versus Warm Activations

Creating a new Swift action and invoking multiples times will demonstrate the difference between "cold" and "warm" activations.

1. Creating a new action (`delay`) with the following source code.

```swift
func main(args: [String:Any]) -> [String:Any] {    
  return [ "greeting" : "Hello from Swift!" ]
}
```

```
$ bx wsk action create delay delay.swift
```

2. Invoke the action for the first time, known as a "cold activation".

```
$ bx wsk action invoke delay -b
ok: invoked /_/delay with id 2924f189a71a449ea4f189a71a149eee
{
  ..
  "annotations": [        
        {
            "key": "initTime",
            "value": 2959
        }
        ...
    ],
    "duration": 2976,
    ...
}
```

Cold activations record an annotation in the activation record (`initTime`) with the milliseconds needed to initialise the action. In the Swift runtime, this time includes the compilation process. 

Looking at the `duration` field, the invocation took 2976 milliseconds, with the `initTime` comprising almost all of that time.

3. Invoke the action again within five minutes.

```
ok: invoked /_/delay with id 42cf8636712c4fa58f8636712cdfa5f0
{
    "duration": 17,
    ...
}
```

This activation record does not contain a `initTime` duration, which indicates a "warm activation", where the binary from the previous build process is re-executed. This also improves the duration, reducing it down to just 17 milliseconds.

*Runtime environments are kept in the cache for around five minutes. Environments do not process parallel requests, meaning if all the cached runtimes are in use, a "cold activation" will be triggered.*

### Deploying Swift Binaries

If we run the Swift build process locally, we can deploy the binary rather than the source file. The build process will be skipped, removing almost all of the cold start delay.

Making Swift binaries compatible with the IBM Cloud Functions needs you to compile for the correct platform architecture. There is also a shim file which handles invoking the function from event parameters which needs add to your code.

Docker can be used to run the Swift build process inside the IBM Cloud Functions Swift runtime. This ensures the binary is generated for the correct platform architecture.

There is also a Swift package which wraps the shim file into a normal library. This makes it simple to add this functionality to your code and register action handlers.

Let's show you how to do this with the sample action we created above…

#### Compiling Swift Binaries

1. Create a new Swift package that generates an executable.

```
$ mkdir Action && cd Action
$ swift package init --type executable
Creating executable package: Action
Creating Package.swift
Creating README.md
Creating .gitignore
Creating Sources/
Creating Sources/Action/main.swift
Creating Tests/
```

2. Replace the package manifest with this source code (`Package.swift`).

```swift
import PackageDescription

let package = Package(
    name: "Action",
    dependencies: [
        .Package(url: "https://github.com/jthomas/OpenWhiskAction.git", majorVersion: 0)
    ]
)
```

3. Update the `main.swift` file under the `Sources/Action` directory with the following source code.

```swift
import OpenWhiskAction

func main(args: [String:Any]) -> [String:Any] {    
  return [ "greeting" : "Hello from Swift!" ]
}

OpenWhiskAction(main: main)
```

OpenWhisk Swift actions use a [custom Docker image](https://hub.docker.com/r/openwhisk/action-swift-v3.1.1/) as the [runtime environment](https://github.com/apache/incubator-openwhisk-runtime-swift).

This command will run the `swift build` command within a container from this image. The host filesystem is mounted into the container at `/swift-package`. Binaries and other build artifacts will be available in `./.build/release/` after the command has executed.

4. Build the Swift binary using the Docker runtime.

```
$ docker run --rm -it -v $(pwd):/swift-package openwhisk/action-swift-v3.1.1 bash -e -c "cd /swift-package && swift build -v -c release"
```

5. Check the file type for the binary that has been produced.

```
$ file .build/release/Action
.build/release/Action: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.24, BuildID[sha1]=18bfb5c83e819840a01fc1cc9f92de05e72320c3, not stripped
```

#### Creating Binary Deployments

OpenWhisk actions can be created from a zip file containing action artifacts. The zip file will be expanded prior to execution. In the Swift environment, the Swift binary executed by the platform should be at `./.build/release/Action`.

If an action is deployed from a zip file which contains this file, the runtime will execute this binary rather than compiling a new binary from source code within the zip file.

1. Add the binary into a new zip archive.

```
$ zip action.zip .build/release/Action
  adding: .build/release/Action (deflated 67%)
```

2. Create an action from this archive file.

```
$ bx wsk action create warm-action --kind swift:default action.zip
```

#### Checking Performance

1. Invoke the new action for the first time.

```
$ bx wsk action invoke warm-action -b
ok: invoked /_/warm-action with id cdb3dbcd4d014f19b3dbcd4d01af19b4
{
    "annotations": [        
        {
            "key": "initTime",
            "value": 317
        }
    ],
    "duration": 494,
    ...
}
```

The existence of the `initTime` annotation confirms this was a "cold activation". However, the `initTime` is dramatically reduced from over two seconds to around three hundred milliseconds.

*Binary deployments also mean we can utilise additional external libraries through the Swift package manger. Libraries must support the Linux Swift runtime to be compatible.*

🎉🎉🎉 **Binary deployments are a fantastic way to improve the performance of all statically compiled languages. This approach should be used in production applications where performance is important…** 🎉🎉🎉
