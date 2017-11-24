# Table of Contents

- [Preface](#preface)
- [サーバレスコンピューティング](#サーバレスコンピューティング)
- [Prepare your engines!](#prepare-your-engines-)
- [Start your engines!](#start-your-engines-)
  * [Actions](#actions)
    + [Creating and invoking JavaScript actions](#creating-and-invoking-javascript-actions)
    + [Creating and invoking asynchronous actions](#creating-and-invoking-asynchronous-actions)
    + [Passing parameters to actions](#passing-parameters-to-actions)
    + [Setting default parameters](#setting-default-parameters)
    + [Using actions to call an external API](#using-actions-to-call-an-external-api)
    + [Working with packages and sequencing actions](#working-with-packages-and-sequencing-actions)
  * [Triggers and rules](#triggers-and-rules)
  * [Using rules to associate triggers and actions](#using-rules-to-associate-triggers-and-actions)
  * [Uploading dependencies](#uploading-dependencies)
- [Boost your engines!](#boost-your-engines-)
  * [Getting started with the OpenWhisk UI](#getting-started-with-the-openwhisk-ui)
  * [Actions](#actions-1)
    + [Invoking actions via REST calls](#invoking-actions-via-rest-calls)
    + [Invoking actions via web actions](#invoking-actions-via-web-actions)
      - [Web action responses](#web-action-responses)
    + [Invoking actions periodically](#invoking-actions-periodically)
  * [Logging](#logging)
  * [Working with packages](#working-with-packages)
    + [Sequencing actions](#sequencing-actions)
  * [Triggers](#triggers)
  * [Rules](#rules)
  * [Monitoring](#monitoring)
- [Build a weather engine!](#build-a-weather-engine-)
  * [Address to locations service](#address-to-locations-service)
  * [Forecast from location service](#forecast-from-location-service)
  * [Sending messages to Slack](#sending-messages-to-slack)
  * [Creating the weather bot using sequences](#creating-the-weather-bot-using-sequences)
  * [Bot forecasts](#bot-forecasts)
  * [Connecting to triggers](#connecting-to-triggers)
  * [Morning forecasts](#morning-forecasts)
- [Build a serverless microservice backend!](#build-a-serverless-microservice-backend-)
  * [Your first API](#your-first-api)
    + [Mapping actions to endpoints](#mapping-actions-to-endpoints)
  * [The Serverless Book Management Application](#the-serverless-book-management-application)
    + [Creating a Cloudant Instance](#creating-a-cloudant-instance)
    + [Creating a Database](#creating-a-database)
  * [Expose your weather services](#expose-your-weather-services)
- [Composing more complex serverless applications](#composing-more-complex-serverless-applications)
  * [Getting started](#getting-started)
  * [Your first Composition](#your-first-composition)
  * [More Compositions](#more-compositions)
  * [Nesting and data-forwarding](#nesting-and-data-forwarding)
  * [Inline coding](#inline-coding)
- [IBM App Connect & Message Hub](#ibm-app-connect---message-hub)
- [Special fuel for your engine!](#special-fuel-for-your-engine-)
  * [Developing with VS Code](#developing-with-vs-code)
  * [Developing with the Serverless Framework](#developing-with-the-serverless-framework)
    + [Installing the Serverless Framework](#installing-the-serverless-framework)
    + [Working with Actions](#working-with-actions)
    + [Working with Sequences](#working-with-sequences)
    + [Connecting API Endpoints](#connecting-api-endpoints)
    + [Working with Triggers and Rules](#working-with-triggers-and-rules)
    + [Packaging your weather services](#packaging-your-weather-services)
- [Node-RED and OpenWhisk](#node-red-and-openwhisk)
  * [Installing Node-RED](#installing-node-red)
  * [Starting Node-RED](#starting-node-red)
  * ["Hello World" with Node-RED](#-hello-world--with-node-red)
    + [Add an Inject node](#add-an-inject-node)
    + [Add a Debug node](#add-a-debug-node)
    + [Wire the two together](#wire-the-two-together)
    + [Deploy](#deploy)
    + [Add a Function node](#add-a-function-node)
  * [Adding Nodes to Node-RED](#adding-nodes-to-node-red)
    + [Invoking OpenWhisk actions from Node-RED](#invoking-openwhisk-actions-from-node-red)
  * [Invoking OpenWhisk Triggers from NodeRED](#invoking-openwhisk-triggers-from-nodered)
    + [Creating New OpenWhisk Actions from Node-RED](#creating-new-openwhisk-actions-from-node-red)
- [The coolest engines out there!](#the-coolest-engines-out-there-)
  * [Vision App](#vision-app)
  * [Dark Vision](#dark-vision)
  * [Skylink](#skylink)
- [Learning more](#learning-more)

# 序文

はじめに、 **IBM Bluemix OpenWhisk** は最近 **IBM Cloud Functions** に改名されました。 これは、公式の見解では、 **Apache OpenWhisk** は、Apacheで利用可能なオープンソース・プロジェクトを意味し、 **IBM Cloud Functions** はIBMのパブリック・クラウド上で動作するIBMのサーバレス・プラットフォームを指します（ IBM Bluemix）。 IBM Cloud Functions は、両方のプロジェクトが同じコアコードベースを共有するため、Apache OpenWhiskに完全に基づいています。

> Before you start, please note that **IBM Bluemix OpenWhisk** has recently been renamed to **IBM Cloud Functions**. This means that, from an official point of view, **Apache OpenWhisk** refers to the open-source project being available on Apache, while **IBM Cloud Functions** refers to IBM's serverless platform running on top of IBM's public cloud (IBM Bluemix). IBM Cloud Functions is entirely based on Apache OpenWhisk as both projects share the same core codebase.

In the following we will, for reasons of simplicity and since most examples would also work on any (locally) hosted OpenWhisk deployment, refer to **OpenWhisk** only and not distinguish between Apache OpenWhisk and IBM Cloud Functions even though we will run many of the examples on top of IBM Bluemix.

During this workshop you will learn how to develop **serverless applications** composed of loosely coupled microservice-like functions. You'll explore OpenWhisk's latest *CLI* (command line interface) and UI and become an OpenWhisk star by implementing a weather bot using *IBM's Weather Company Data service* and *Slack*. You will also investigate how to use other capabilities such us our API Gateway integration allowing you to easily expose functions via API endpoints. You will have a first glance at our research-driven tech-preview called *Composer* allowing you to compose more complex serverless applications by combining multiple functions using control logic and state. Finally, you will find out how to package and deploy your entire serverless application together using the *Serverless Framework*.

We wish you a lot of fun and success...


# サーバレスコンピューティング

**サーバレスコンピューティング**（別名**Funcions-as-a-Service (FaaS)**）は、サーバの存在が完全に抽象化されているモデルを指します。
つまり、サーバがまだ存在していても、開発者はサーバの運用を気にする必要から解放されます。
スケーラビリティ、高可用性、インフラストラクチャセキュリティなどの低レベルのインフラストラクチャと運用を考慮する必要から解放されています。
したがって、サーバレスコンピューティングは、開発者が付加価値の高いコードを開発することに迅速に集中できるように、基本的にメンテナンスの労力を軽減することです。

サーバレスコンピューティングは、クラウドネイティブアプリケーションの開発を簡素化し、
  特に複雑なアプリケーションを簡単に交換できる小さな独立したモジュールに分解するマイクロサービス指向のソリューションです。

サーバレスコンピューティングは、特定のテクノロジを参照するものではありません。その代わりに前に説明したモデルの基礎をなす概念を参照します。
それにもかかわらず、OpenWhiskのようなサーバレスモデルに続く開発アプローチの緩和が近年浮上しています。

OpenWhiskはクラウドファーストの分散イベントベースのプログラミングサービスで、イベントに応じてコードを実行できるFaaSプラットフォームです。

前述のサーバレスデプロイメントモデルとオペレーションモデルを提供し、
どのような規模でも細かい価格設定モデルを使用して、正確なリソースを提供します。- それ以上でもそれ以下でもありません - 実際に実行されるコードに対してのみ料金を請求します。柔軟なプログラミングモデルを提供します。*Java, JavaScript, PHP, Python, and Swift* などの言語や、*Docker* コンテナを使用してカスタムロジックの実行をサポートします。これにより、小さなアジャイルチームは既存のスキルを再利用し、目的に合った方法で開発することができます。また、開発したビルディングブロックをつなげて動作させるツールも提供します。オープンであり、ベンダーロックインを避けるためにどこでも実行できます。

まとめると、OpenWhiskは以下を提供します。
* ... 豊富なビルディングブロックを簡単につけたりはずしたりできます
* ... 低レベルで少ないインフラストラクチャと運用によりビジネスへの付加価値を重視することに注力出来ます
* ... シーケンスを利用して、簡単にマイクロサービスをつなげることが出来ます
* ... 制御ロジックと状態を使用して複数の機能を組み合わせることで、より複雑なサーバレスアプリケーションを構成することが出来ます（ * tech-preview * ）

まとめると、我々のバリューポジションと他との違いは:
* OpenWhiskはインフラストラクチャの複雑さを隠し、開発者がビジネスロジックに集中できるようにします。
* OpenWhiskはスケーリング、ロードバランシング、ロギング、フォールトトレランス、メッセージキューなどの低レベルの詳細を処理します。
* OpenWhiskは、さまざまな分野（分析、コグニティブ、データ、IoTなど）からのビルディングブロックの豊富なエコシステムを提供します。
* OpenWhiskは公開されており、オープンコミュニティをサポートするように設計されています。
* OpenWhiskはOpenWhiskパッケージ経由でマイクロサービスを共有できるオープンエコシステムをサポートしています
* OpenWhiskは、開発者が近代的な抽象化と連鎖を使用してソリューションを構成することを可能にします。
* OpenWhiskはJava、JavaScript、PHP、Python、Swiftなどの複数のランタイムやDockerコンテナにカプセル化された任意のバイナリプログラムをサポートします。
* OpenWhiskは実行するコードに対してのみ課金されます

OpenWhiskモデルは基本的に３つの概念で構成されています。
* `trigger` イベントが発生するクラス
* `action`, イベントハンドラ -- イベントに応答して実行されるコード
* `rule`, トリガーとアクションの関連付け

サービスはトリガとして発行するイベントを定義し、開発者はイベントを処理するアクションを定義します。

開発者は、必要なアプリケーションロジックの実装に注意するだけで済みます。システムは残りの部分を処理します。

# 環境の準備

はじめる前に:
* 作業を進めていくうちに、CLIがこのガイドとは異なるレスポンスを示すかもしれません。これはあなたがこの文書が作成された際とは異なるnamespaceを利用していることに起因するものです。これに伴う差異は極めて小さく、nameの関わる箇所でのみ起きる事象となります。
* 【重要】*Linux* ユーザーの方へ:`cURL`などいくつかのツールを追加でインストールする必要があります。(e.g. *Ubuntu* の場合以下のコマンドでインストールできます。`sudo apt-get update && sudo apt-get install curl`)
* 【重要】*Windows* ユーザーの方へ: *Git* (https://git-for-windows.github.io/)をダウンロードし *Git bash* から作業を行うことを推奨しています。また`cURL` for Windows (https://curl.haxx.se/download.html)のダウンロードも推奨しています。

IBM Cloud Functionsの利用のはじめの一歩:
1. ブラウザウィンドウを開きます。
2. https://console.ng.bluemix.net/openwhisk/ にアクセスします。
3. IBM Cloudにログインします。  
   アカウントを持っていない場合は`「フリーアカウントの作成」`をクリックするか,
   https://console.ng.bluemix.net/registration/ に直接アクセスしてアカウントを作成します。
4. `アメリカ南部`または`英国`のどちらかのリージョンを選択します。(自国に近い地域を選択すると良いでしょう。) `アメリカ南部`を選択しなかった場合、今後出てくる`URL`内の`.ng`フラグメントを他のものに変える必要があります。`英国`の場合は`.eu-gb`となります。Make sure to pick one of the following regions (pick the one closer to where you are): `US South` or `United Kingdom`
5. `「CLIのダウンロード」`をクリックします。
6. 手順の1から3に従って作業します。(4は必須ではありません) ここには、CLIのダウンロード、Cloud Functionsプラグインをインストール、 APIのエンドポイント・組織・namespaceを選択してBluemixにログインするという作業が含まれます。

# Start your engines!

The CLI allows you to work with OpenWhisk's basic entities, i.e. to create *actions, triggers, rules, and sequences*. Hence, let's learn how to work with the CLI.

## Actions

*Actions* are small stateless pieces of code that run on the OpenWhisk platform.

### Creating and invoking JavaScript actions

An action can be a simple *JavaScript* function that accepts and returns a *JSON* object.

First, use your editor of choice (for instance, download the *Atom* editor from https://atom.io/) to create a file called `hello.js` with the following content:

```javascript
function main() {
    return { message: "Hello world" };
}
```

Save the file to wherever you want.

Next, open a terminal window, navigate to the directory where you stored the file `hello.js` and create an OpenWhisk action called `hello` referencing the function in `hello.js`:

<pre>
$ bx wsk action create hello hello.js
<b>ok:</b> created action <b>hello</b>
</pre>

Notice that you can always list the actions you have already created like this:

<pre>
$ bx wsk action list
<b>actions</b>
hello                                  private nodejs:6
</pre>

To run an action use the ```bx wsk action invoke``` command.
A *blocking* (i.e. *synchronous*) invocation waits until the action has completed and returned a result. It is indicated by the ```--blocking``` option (or ```-b``` for short):

<pre>
$ bx wsk action invoke --blocking hello
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

The ```activation id``` can be used to retrieve the logs or the result of an (asynchronous) invocation at a future point in time. In case you forgot to note down an `activation id` you can retrieve the list of activations at any time:

<pre>
$ bx wsk activation list
<b>activations</b>
dde9212e686f413bb90f22e79e12df74             hello                                   
eee9212e686f413bb90f22e79e12df74             hello
</pre>

Notice that the list of ```activation ids``` is ordered with the most recent one first.

To obtain the result of a particular action invocation enter (notice that you need to replace the ```activation id``` shown below with the ```id``` you have received during the previous step):

<pre>
$ bx wsk activation get dde9212e686f413bb90f22e79e12df74
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
$ bx wsk action delete hello
<b>ok:</b> deleted action <b>hello</b>
</pre>

You can check whether an action was successfully deleted like this:

<pre>
$ bx wsk list
entities in namespace: <b>default</b>
<b>packages</b>
<b>actions</b>
<b>triggers</b>
<b>rules</b>
</pre>

### Creating and invoking asynchronous actions

*JavaScript* functions that run *asynchronously* need to return the activation result after the `main` function has returned. This can be accomplished by returning a `Promise`.

Again, use your editor of choice to create a file called `asyncAction.js` with the following content:

```javascript
function main(params) {
    return new Promise(function(resolve, reject) {
        setTimeout(function() {
            resolve({ message: "Hello world" });
        }, 2000);
    });
}
```

Notice that the `main` function returns a `Promise` which indicates that the activation has not completed yet, but is expected to in the future.

In this particular example the `setTimeout` function waits for two seconds before calling the callback function. This represents the asynchronous code and goes inside the `Promise's` callback function.

The `Promise's` callback takes two arguments, `resolve` and `reject`, which are both functions. The call to `resolve` fulfills the `Promise` and indicates that the activation has completed normally.

A call to `reject` can be used to reject the `Promise` and signal that the activation has completed abnormally.

Next, run the following commands to create the action and invoke it:

<pre>
$ bx wsk action create asyncAction asyncAction.js
<b>ok:</b> created action <b>asyncAction</b>

$ bx wsk action invoke --blocking --result asyncAction
{
    "message": "Hello world"
}
</pre>

Finally, run the following commands to fetch the activation log to see how long the activation took to complete:

<pre>
$ bx wsk activation list --limit 1 asyncAction
<b>activations</b>
b066ca51e68c4d3382df2d8033265db0       asyncAction

$ bx wsk activation get b066ca51e68c4d3382df2d8033265db0
{
    "start": 1455881628103,
    "end":   1455881648126,
    "duration": 2055,
    ...
}
</pre>

By looking at the `duration` entry being shown as part of the activation record, you can see that this activation took slightly over two seconds to complete.

### Passing parameters to actions

Actions may be invoked with several named parameters.

Change (and save) your `hello` action as follows:

```javascript
function main(params) {
    return { message: "Hello, " + params.name + " from " + params.place };
}
```

Again, create the action:

<pre>
$ bx wsk action create hello hello.js
<b>ok:</b> created action <b>hello</b>
</pre>

You can pass named parameters as *JSON* payload or via the CLI:

<pre>
$ bx wsk action invoke -b hello -p name "Bernie" -p place "Vermont" --result
{
    "message": "Hello, Bernie from Vermont"
}
</pre>

Notice the use of the `--result` parameter (or `-r` for short; available for blocking invocations only) to immediately print the result of the action invocation without the need of an `activation id`.

### Setting default parameters

Recall that the `hello` action above took two parameters: the `name` of a person, and the `place` where he or she is from.

Rather than passing all the parameters to an action every time, you can *bind* certain parameters. Let's bind the `place` parameter above so we have an action that defaults to the place `Vermont, CT`:

<pre>
$ bx wsk action create helloBindParams hello.js --param place "Vermont, CT"
<b>ok:</b> created action <b>helloBindParams</b>

$ bx wsk action invoke -b helloBindParams --param name "Bernie" --result
{
    "message": "Hello, Bernie from Vermont, CT"
}
</pre>

Notice that we did not need to specify the `place` parameter anymore when invoking the action. Moreover, bound parameters can still be overwritten by specifying the parameter value at invocation time.

### Using actions to call an external API

So far, the examples have been self-contained functions. You can also create an action that calls an external API, of course.

The following example invokes the *Yahoo Weather service* to get the current conditions at a specific location.

Again, use your editor of choice to create a file called `weather.js` with the following content:

```javascript
var request = require("request");

function main(params) {
    var location = params.location || "Vermont";
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
                var output = "It is " + temperature + " degrees Fahrenheit in " + location + " and " + text;

                resolve({params: output});
            }
        });
    });
}
```

Notice that the action above uses the *JavaScript request library* to make an *HTTP* request to the *Yahoo Weather API* and to extract certain fields from the *JSON* result.

The example also shows the need for asynchronous actions. The action returns a `Promise` to indicate that the result of this action is not available yet when the function returns. Instead, the result is available in the callback after the *HTTP* call completes, and is passed as an argument to the `resolve` function just as we have seen it earlier.

Now, run the following commands to create the action and invoke it:

<pre>
$ bx wsk action create yahooWeather weather.js
<b>ok:</b> created action <b>yahooWeather</b>

$ bx wsk action invoke --blocking --result yahooWeather --param location "Brooklyn, NY"
{
    "params": "It is 28 degrees Fahrenheit in Brooklyn, NY and Cloudy"
}
</pre>

### Working with packages and sequencing actions

You can also create an action that chains together a *sequence* of actions.
We will demonstrate how to use such sequences using actions shipped with *packages* available out of the box.

Generally, to reveal which packages are available out of the box run the following command:

<pre>
$ bx wsk package list /whisk.system
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

Next, to reveal the list of entities contained in the `/whisk.system/cloudant` package run the following command:

<pre>
$ bx wsk package get --summary /whisk.system/cloudant
<b>package</b> /whisk.system/cloudant: Cloudant database service
   (<b>parameters</b>: BluemixServiceName host username password dbname includeDoc overwrite)
<b>action</b> /whisk.system/cloudant/read: Read document from database
<b>action</b> /whisk.system/cloudant/write: Write document to database
<b>feed</b> /whisk.system/cloudant/changes: Database change feed
[...]
</pre>

The output shows that the `/whisk.system/cloudant` package provides multiple actions, e.g. `read` and `write`, and a trigger *feed* called `changes`. The `changes` feed causes triggers to be fired when documents are added to the specified *Cloudant* database.

The package also defines the parameters `username`, `password`, `host`, and `port`. These parameters must be specified for the actions and feeds to be meaningful. The parameters allow the actions to operate on a specific *Cloudant* account.

One way to access the actions in this package is by binding to them (you could alternatively access the package directly, of course). Bindings create a reference to the given package in your namespace. The advantage is that they allow you to access actions by typing `myUtil/actionName` instead of `/whisk.system/utils/actionName` every time.

Now, let's use a set of actions that are shipped with the package `/whisk.system/utils`.

Are you able to find out what is contained in this package?

To create a binding run the following command:

<pre>
$ bx wsk package bind /whisk.system/utils myUtil
<b>ok:</b> created binding <b>myUtil</b>
</pre>

This gives you access to the following actions:
* `myUtil/cat`: Action to transform lines of text into a *JSON* array
* `myUtil/head`: Action to return the first element in an array
* `myUtil/sort`: Action to sort an array of text

Let's now create a composite action that is a sequence of the above actions, so that the result of one action is passed as arguments to the next action:

<pre>
$ bx wsk action create myAction --sequence myUtil/sort,myUtil/head
<b>ok:</b> created action <b>myAction</b>
</pre>

The composite action above will return the first element of a sorted array:

<pre>
$ bx wsk action invoke -b myAction -p lines '["c","b","a"]' --result
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

Let's create a trigger to send *location updates*:

<pre>
$ bx wsk trigger create locationUpdate
<b>ok:</b> created trigger <b>locationUpdate</b>
</pre>

You can check that the trigger has been created like this:

<pre>
$ bx wsk trigger list
<b>triggers</b>
locationUpdate                         private
</pre>

So far we have only created a named channel to which events can be fired.

Let's now fire the trigger by specifying its name and parameters:

<pre>
$ bx wsk trigger fire locationUpdate -p name "Donald" -p place "Washington, D.C"
<b>ok:</b> triggered <b>locationUpdate</b> with id <b>11ca88d404ca456eb2e76357c765ccdb</b>
</pre>

Events you fire to the `locationUpdate` trigger currently do not do anything. To be useful, we need to create a rule that associates the trigger with an action.

## Using rules to associate triggers and actions

*Rules* are used to associate a trigger with an action. Hence, every time a trigger event is fired, the action is invoked together with the events' parameters.

Let's create a rule that calls the `hello` action whenever a location update is posted; required parameters are the `name` of the rule, the trigger, and the action:

<pre>
$ bx wsk rule create myRule locationUpdate hello
<b>ok:</b> created rule <b>myRule</b>
</pre>

Now, every time we fire a location update event, the `hello` action will be called with the corresponding event parameters:

<pre>
$ bx wsk trigger fire locationUpdate -p name "Donald" -p place "Washington, D.C"
<b>ok:</b> triggered <b>locationUpdate</b> with id <b>2c0b4602f5a84ea1b049a57c059e1ec1</b>
</pre>

We can check that the action was really invoked by checking the most recent activations:

<pre>
$ bx wsk activation list hello
<b>activations</b>
12ca88d404ca456eb2e76357c765ccdb       hello
8b61fbedb91144269fee474e5f503e67       hello
</pre>

Notice that the use of the optional argument `hello` filters the result so that only invocations of the `hello` action are being displayed.

Again, to obtain the result of the particular action invocation enter (notice that you once again need to replace the `activation id` with the `id`you have received during the previous step):

<pre>
$ bx wsk activation result 12ca88d404ca456eb2e76357c765ccdb
{
    "result": "Hello, Donald from Washington, D.C."
}
</pre>

We can finally see that the `hello` action received the event payload and returned the expected string.

## Uploading dependencies

As an alternative to writing all your code in a single *JavaScript* source file, you can implement an action as an `npm` package (requiring NodeJS (https://nodejs.org/) to be installed, of course).

The structure is supposed to look as follows:

First, define a `package.json` like this:

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

Next, define an `index.js` like this:

```javascript
function myAction(params) {
    const leftPad = require("left-pad");
    const lines = params.lines || [];

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
$ bx wsk action create packageAction --kind nodejs:6 action.zip
<b>ok:</b> created action <b>packageAction</b>
</pre>

Notice that when creating an action from a .zip archive using the CLI tool, you must explicitly provide a value for the `--kind` flag.

You can finally invoke the action like any other:

<pre>
$ bx wsk action invoke --blocking --result packageAction --param lines '["and now", "for something completely", "different"]'
{
    "padded": [
        ".......................and now",
        "......for something completely",
        ".....................different"
    ]
}
</pre>

Finally, notice that while most `npm` packages install *JavaScript* sources on `npm install`, some also install and compile binary artifacts. The archive file upload currently does not support binary dependencies but rather only *JavaScript* dependencies. Action invocations may fail if the archive includes binary dependencies.

# Boost your engines!

As an alternative to the CLI, you can also use the OpenWhisk UI, esp. the visual code editor, to work with OpenWhisk's basic entities, i.e. to create actions, triggers, rules, and sequences. Hence, let's learn how to work with the OpenWhisk UI.

## Getting started with the OpenWhisk UI

First, open a browser window, navigate to https://console.ng.bluemix.net/openwhisk/ and click `Develop`.

The OpenWhisk UI is comprised of the following sections:

1. `My Actions`  
   The `My Actions` section lists all actions you have created previously.  
   Clicking an action loads its code into the code editor.  
   Hovering over an action lets a trash bin appear allowing to delete the action.  
   At this point in time you should at least see the `hello` action we have created earlier.  

2. `My Sequences`  
   The `My Sequences` section lists all the sequences you have created previously.  
   Clicking a sequence loads its model into the visual modeler.  
   Hovering over a sequence lets a trash bin appear allowing to delete the sequence.  

3. `My Rules`  
   The `My Rules` section lists all the rules you have created previously.  
   Clicking a rule loads its model into the visual modeler.  
   Hovering over a rule lets a trash bin appear allowing to delete the rule.  
   At this point in time you should at least see the `myRule` rule we have created earlier.  

4. `My Triggers`  
   The `My Triggers` section lists all the triggers you have created previously.  
   Hovering over a trigger lets a flash icon appear allowing to fire the trigger as well as a trash bin allowing to delete the trigger.

## Actions

Let's start exploring the UI by creating some simple first actions similar to the ones we have created before when having used the CLI.

First, click the `Create an Action` button.  
Next, specify a name (e.g. `helloUI`) by entering it into the text field prefilled with the text `Choose a name for your new action`.  
Leave everything else as-is and click the `Create Action` button at the bottom of the screen.

Notice that even though you did not change any configuration options, you would have had the option to change the `language` you want to implement your action in as well as the `memory quota` and the `time limit`. Click the `Learn more` links for additional details and feel free to play around with these options on your own.

Copy the following code into the code editor replacing any existing code:

```javascript
function main() {
    return { message: "Hello world" };
}
```

Next, click the `Run this Action` button to test the action directly from within your browser.  
Before being able to run your action you need to make it live, hence click the `Make It Live` button when being prompted to do so.  
Afterwards click the `Run with this Value` button.

Notice that you do not need to specify any *JSON* input as the action is not expecting any parameters to be handed over.

You should see the following result:

<pre>
{
    "message": "Hello world"
}
</pre>

Next, to see how things work when working with an action accepting parameters click the `Create an Action` button again.  
Then, once again, specify a name (e.g. `helloUI2`) and click the `Create Action` button.

Next, copy the following code into the code editor replacing any existing code:

```javascript
function main(params) {
    return { message: "Hello, " + params.name + " from " + params.place };
}
```

Once again, click the `Run this Action` button and follow the same procedure as before to test this action directly from within your browser.

Notice that you this time need to specify some *JSON* input to specify proper parameter values. For instance, you could specify the following input:

```json
{
    "name": "Andreas",
    "place": "Stuttgart, Germany"
}
```

You should see the following result:

<pre>
{
    "message": "Hello, Andreas from Stuttgart, Germany"
}
</pre>

### Invoking actions via REST calls

Actions cannot only be invoked via the CLI or the OpenWhisk UI, they can also be invoked via simple *REST* API calls. All you need to do is to `POST` against the correct *REST* API endpoint.

To find out about the correct *REST* API endpoint for a particular action, select the action, e.g. the `helloUI` action we have created earlier, and click the `View REST Endpoint` link at the bottom right of the code editor.

To test this use the `cURL URL`; just click the `Copy` button displayed next to the `cURL URL` shown and submit it.

For instance, to invoke the `helloUI` action we have created earlier submit a call like this:

<pre>
$ curl -d "{\"arg\":\"value\"}" "https://openwhisk.ng.bluemix.net/api/v1/namespaces/andreas.nauerz%40de.ibm.com_dev/actions/helloUI?blocking=true" -XPOST -H "Content-Type: application/json" -H "Authorization: Basic xxx="
</pre>

You should see the following result:

<pre>
[...]
"response": {
    "result": {
        "message": "Hello world"
    },
    "success": true,
    "status": "success"
},
[...]
</pre>

### Invoking actions via web actions

Web actions are OpenWhisk actions annotated to quickly enable you to build web based applications. This allows you to program backend logic which your web application can access anonymously without requiring an OpenWhisk authentication key. It is up to the action developer to implement their own desired authentication and authorization (i.e. `OAuth` flow).

Web action activations will be associated with the user that created the action. This actions defers the cost of an action activation from the caller to the owner of the action. To allow the previously created `hello` action to be called as a web action enter:

<pre>
$ bx wsk action update hello --web true
<b>ok:</b> updated action <b>hello</b>
</pre>

Once enabled your action is supposed to be accessible as a web action via a new *REST* interface.

The `URL` is structured as follows: `https://{APIHOST}/api/v1/web/{QUALIFIED ACTION NAME}.{EXT}`. The fully qualified name of an action consists of three parts: the `namespace`, the `package name`, and the `action name`. The fully qualified name of a web action must include its package name, which is `default` if the action is not in a named package. The last part of the `URI` called the extension which is typically `.http` or .`json`. The web action API path may be used with `cURL` or `wget` without an API key. It may even be entered directly in your browser.

Try opening `https://openwhisk.ng.bluemix.net/api/v1/web/andreas.nauerz@de.ibm.com_dev/default/hello.json?name=Andreas&place=Stuttgart` in your web browser after having replaced the namespace `andreas.nauerz@de.ibm.com_dev` with your namespace.

Notice that you can also enable any action as a web action using the OpenWhisk UI. There you can also find out about the correct `URL` to invoke your web actions.

We leave this as a voluntary exercise for you – will you find the right place?

#### Web action responses

Web actions can also be used to implement *HTTP* handlers that respond with `headers`, `statusCode`, and `body` content of different types. The web action must still return a *JSON* object, but the OpenWhisk system (namely its `controller`) will treat a web action differently if its result includes one or more of the following as top level *JSON* properties:
* `headers`: a *JSON* object where the keys are header-names and the values are string values for those headers (default is no headers)
* `statusCode`: a valid *HTTP status code* (default is 200 OK)
* `body`: a string which is either plain text or a base64 encoded string (for binary data)

The `controller` will pass along the action-specified *headers*, if any, to the *HTTP* client when terminating the *request/response*. Similarly, the `controller` will respond with the given *status code* when present. Lastly, the *body* is passed along as the *body* of the *response*. Unless a *content-type header* is declared in the actions' result *header*, the *body* is passed along as is if it's a string (or results in an error otherwise). When the *content-type* is defined, the `controller` will determine if the *response* is binary data or plain text and decode the string using a *base64 decoder* as needed. Should the *body* fail to decode correctly, an error is returned to the caller.

Notice that a *JSON* object or array is treated as binary data and must be *base64* encoded.

Now, let's make use of the `headers` and `statusCode` property to send a redirect. To do so create an action named `webAction` like this:

```javascript
function main() {
    return {
        headers: { location: "http://openwhisk.org" },
        statusCode: 302
  };
}
```

Enable the action as web action:

<pre>
$ bx wsk action update webAction --web true
<b>ok:</b> updated action <b>webAction</b>
</pre>

Try opening
`https://openwhisk.ng.bluemix.net/api/v1/web/andreas.nauerz%40de.ibm.com_dev/default/webAction.http` in your browser (again after having replaced the namespace with yours). You should be redirected to the openwhisk.org site.

Next, let's make use of the `headers`, `status` and `body` properties to respond with an image. To do so update the previously created web action like this:

```javascript
function main() {
    let png = "<STRING REPRESENTING A BASE64 ENCODED IMAGE>"
    return { headers: { "Content-Type": "image/png" },
             statusCode: 200,
             body: png };
}
```

Notice that you can use any online available base64 encoder to generate the above mentioned string to encode an image of your choice.

Again, invoke the web action via your browser. You should see the your image.

Finally, let's make use of the `headers`, `status` and `body` properties to respond with simple *HTML*. To do so update the previously created web action like this:

```javascript
function main() {
    let html = "<html><body>Hello World!</body></html>"
    return { headers: { "Content-Type": "text/html" },
             statusCode: 200,
             body: html };
}
```

Again, invoke the web action via your browser. You should see the the message `Hello World!`.

### Invoking actions periodically

Also, actions cannot only be invoked in a blocking (synchronous) or non-blocking (asynchronous) fashion as explained before, they can also be invoked *periodically*.

To test this open the OpenWhisk UI and select the `helloUI` action we have created earlier.  
Next, click the `Automate this Action` button at the bottom right of the code editor.  
Next, from the next screen appearing select the `Periodic` icon.  
Next, create a new trigger aka alarm by clicking the `New Alarm` icon.  
To keep things simple select the `:MM minutes` icon and specify `N` to be `1` so that they action is supposed to be executed every minute.  
Specify a name for the periodic trigger and click the `Create Periodic Trigger` button.  
Finally, click `Next`, then `This Looks Good`, and then `Save Rule`.

To see the result click the `View Activity` button which redirects you to the dashboard. You should see a periodic invocation every minute – to stop this you have to disable the rule that has been created – will you find out how this can be done?

## Logging

Of course, OpenWhisk allows you to add custom log statements to your actions, too.

To see how logging works click the `Create an Action` button again.  
Then, once again, specify a name (e.g. `helloLogging`) and click the `Create Action` button.

Next, copy the following code into the code editor replacing any existing code:

```javascript
function main(params) {
    console.log("Running helloLogging... ");
    return { message: "Hello world!" };
}
```

Once again, click the `Run this Action` button and follow the same procedure as before to test this action directly from within your browser.

You should see the following result:

<pre>
{
    "message": "Hello world"
}
</pre>

To review the log you can click the `Show Logs` link after having invoked the action.

You should a log statement like this:

<pre>
2016-10-18T08:36:34.472273207Z stdout: Running helloLogging...
</pre>

At this point feel free to create your own little action, maybe one you implement using a different programming language. To learn how to do the latter refer to the official documentation available here:

https://github.com/openwhisk/openwhisk/blob/master/docs/actions.md#creating-python-actions
https://github.com/openwhisk/openwhisk/blob/master/docs/actions.md#creating-swift-actions
https://github.com/openwhisk/openwhisk/blob/master/docs/actions.md#creating-java-actions
https://github.com/openwhisk/openwhisk/blob/master/docs/actions.md#creating-docker-actions

## Working with packages

As you have already seen before, OpenWhisk provides you, out of the box, with a shared collection of actions and triggers as part of so called packages. The OpenWhisk UI makes it even easier to explore and test available packages. Let's learn how to do so now.

First, click the `Browse Public Packages` button at the top right of the screen.  
You'll be presented with a set of packages, represented by some icons, being available.

Click the `Samples` package.  
At the left of the screen you will be shown a short description of the functionality this package provides you with. At the top center of the screen you can access the different actions and triggers the package provides you with (notice that the samples package provides you only with actions).  
You can choose between the following actions: `curl`, `greeting`, `helloWorld`, `wordCount`. For each action a short description and sample input you can feed it with as well as sample output you can expect to get when invoking the action is being provided.

Let's play with the `wordCount` action.  
Hence, select the `wordCount` action and read the description to understand its purpose. Also review the sample input to understand how to properly feed the action when invoking it as well as the sample output to understand what you can expect after having invoked it.  
Click the `Run this Action` button.

Based on the sample input you have been shown before, specify the following input and click the `Run with this Value` button:

```json
{
    "payload": "OpenWhisk is simply super cool"
}
```

You should see the following result:

<pre>
{
    "count": "5"
}
</pre>

Instead of just reusing a package-provided action as-is you can also view its code (which you may want to use as a basis for your own action).

Let's review the code of the `curl` action.  
Hence, navigate back to the catalog (to do so you probably want to click the `Close` button to close the invocation console) and click the `Samples` package again.  
This time select the `curl` action and read the description to understand its purpose.  
Next, click the `View Source` button to (re)view and understand the action's code.

At this point feel free play with the other packages being available.

### Sequencing actions

Next, let's visually model a simple sequence similar to the one we have created earlier when having used the CLI.

Therefore, let's first create a very simple action (name it `echo`) the same way you learned to create actions before:

```javascript
function main(params) {
    return { payload: "Life is " + params.payload };
}
```

Obviously the action does nothing else than returning the text you have handed-over via the input parameter called `payload`.

Now, let's assume you want to define a sequence that allows any text you hand over to the action `echo` to be translated from English to French.

To make this happen, let's make use of the `translate` action part of the `Watson` package.

Hence, we first need to create an instance of the `Watson Language Translator` service.  
To do so click the `Catalog` (not the `Browse Public Packages`) link at the top right of the screen.  
From the menu appearing on the left of the screen select `Watson`.  
Next, click `Language Translator`.  
Leave all settings as they are and click the `Create` button at the bottom right of the screen.  
Next, switch to the `Service Credentials` tab and click the `View Credentials` link.
Note down `username` and `password`.

Now, let's try to understand how the mentioned package and action works.  
Hence, navigate back to the OpenWhisk UI and its catalog (`Browse Public Packages`) and click the `Watson Translator` package.  
Click the `translator` action and read the description as well as the sample input and output to understand its purpose.

Next, to be able to use Watson, we need to create a binding.  
Hence, click the `New Binding` button at the left of the screen.  
Then, specify an arbitrary name and select the `Language Translator` instance you have created before (or specify an arbitrary name and the `username` and `password` you noted down before) and click `Save Configuration`.

Next, select the binding (if not already selected), make sure that the `translator` action is still being selected, and click `Run this Action`.

Specify the following input and click the `Run with this Value` (you may need to click `Make It Live` before) button:

```json
{
    "translateTo": "fr",
    "payload": "Wonderful",
    "username": "xxx",
    "translateFrom": "en",
    "password": "xxx"
}
```

Notice that you need to replace the values for `username` and `password` with the values you specified when you created the binding. Alternatively, you can remove the parameters `username` and `password` entirely which causes the default bound parameters to be used.

You should see the following result:

```json
{
    "payload": " Merveilleux"
}
```

Now, let's chain the two actions together as a sequence.  
Hence, select the `echo` action you have created earlier.  
Click the `Link into a Sequence` button from the bottom right of the screen.  
Then, from the next screen appearing select the `Watson Translator` package and the `translator` action as well as the binding you have created earlier.  
Then, click the `Add to Sequence` button and then the `This Looks Good` button.  
Finally, specify a `name` for your sequence (optional) and click the `Save Action Sequence` and afterwards the `Done` button.  
To test the sequence select it and click the `Run This Sequence` button.

Specify the following input and click the `Run with this Value` button:

```json
{
    "translateTo": "fr",
    "payload": "wonderful",
    "translateFrom": "en"
}
```

You should see the following result:

```json
{
    "payload": "La vie est belle"
}
```

## Triggers

You can also work with triggers using the OpenWhisk UI.

Let's assume you want to invoke the `hello` action you have created earlier as soon as something changes in a particular *Github* repository.

First, select the `hello` action.  
Next, click the `Automate this Action` button at the bottom right of the screen.
Next, click the `Github` icon.  
Then, click the `New Trigger` icon.  

Notice that you need a *Github* account to proceed. In case you do not have an account sign-up now.

Specify your *Github* `username`, the `access token` and the `name` of the repository you want to watch.

Notice that in *Github* your `username` is shown when looking at what is shown under `Signed in as...` when being logged-in.   Your access token is shown under `Settings → Personal access tokens`. You probably have to create a new one by clicking the `Generate new token` button and by specifying a `name` and selecting the `repo` checkbox. Similarly, you may have to create a test repository to play around with.

Also notice that you can select the right repository from a pull-down menu after having specified your Gihub `username` and the `access token`.

For events simply specify `*` to listen to all events.

Once you have filled out all input fields click the `Save Configuration` button.  
Select the trigger you have just created (if not already selected) and click the `Next` button.  
Next, click the `This Looks Good` button and, finally, the `Save Rule `button.

For testing purposes let's first fire the trigger manually.

To be able to observe what's going on click the `View Activity` button to open the monitoring dashboard (details about this will be explained further below). At the top right you can see triggers that fired as well as actions that have been invoked.   The view updates itself periodically. You can also refresh it manually by clicking the `refresh` icon.  
To continue with the test select the browser tab showing your actions, triggers, and rules and hover over the trigger you have just created and click the `lightning` icon followed by the `Run with this Value` button to fire it.  
In the next screen appearing click the `Run with this Value` button.  
Then, navigate back to the browser tab showing the monitoring dashboard where you should see that the trigger has fired and, consequently the `hello` action been invoked.  

Finally, open another browser tab and log into *Github*.

Navigate to your repository and add a file or change a file's content and commit your change.  
Then, navigate back to the browser tab showing the monitoring dashboard where you should see that the trigger has fired and, consequently the `hello` action been invoked.

## Rules

Within the OpenWhisk UI rules are created in a similar way than sequences. You first select an action supposed to become part of the rule. Then you click the `Automate this Action` aka `Create a Rule` button just the way we already did it earlier.

Similarly than triggers rules can be manually invoked by clicking the `Fire This Trigger` button after having selected the rule. Alternatively, you can invoke the action part of a rule only by clicking the `Run Only the Action` button after having selected the rule. Try it out using the rules we have already created earlier.

## Monitoring

The monitoring dashboard that we have already used earlier allows you to observe your systems' behavior.

At the top left the dashboard visualizes the invocation counts per action or rule. This allows you to see how often particular actions or rules have been invoked and which actions or rules have been and are invoked most often. As part of the bar chart successful invocations are displayed in green, failed invocations in red.

At the top right the dashboard displays the recent activities, e.g. triggers that have fired or actions that have been invoked along with output being produced as well as some metadata like invocation times etc. Successful activities are displayed in green, failed ones in red. You can click an `activation id` to retrieve more details about a particular activation.

At the center the dashboard visualizes the invocation counts over time representing load as this reveals how many invocations took place at a certain point in time. Again, as part of the bar chart successful invocations are displayed in green, failed invocations in red.

Play around with the actions, triggers, etc., you have created before, i.e. fire triggers, invoke actions and have a look at the monitoring dashboard while doing so.

# 天気予報エンジンを作る！

さあ、あなたが学んだことをもとに、役立つデモを構築しましょう

このデモでは、ユーザーに天気予報を通知する*Slack*用のボットを作成します。ユーザーはチャットメッセージを送信する事により、ボットに特定の場所の予測を尋ねることが出来ます。例えば毎日午前８時のように、ボットは定期的な感覚で位置の予測を送信するように設定することも出来ます。

ボットはいくつかの機能が必要です。例えばアドレスを場所に変換し、場所の天気予報を取得し、*Slack*と統合します。ボットをモノシリックアプリケーションとして実装するのではなく、これらすべての機能のロジックを含んでいるため、別のサービスとして展開したいと考えています。

後に、シーケンスを使用してバインドする方法を見ていきます。コードを書かずにボットを作成することができます。

Notice ここで説明する成果物のほとんどの物は、CLIもしくはOpenWhisk UIのどちらかで作成できます。　あなた次第です。

## 住所を位置情報へ

このサービスは外部のジオコーディングAPIを使用して住所の「緯度」と「経度」の座標値の取得を行います。

アクション（マイクロサービス）は、以前に触れたように、単一の関数(`main`)内でアプリケーションロジックを実装します。以前と同じように、パラメータはオブジェクトの引数(`params`)として関数呼び出しに渡されます。サービスは*Google*ジオコーディングAPIへのAPI呼び出し行い、最初のアドレス一致の呼び出しの応答から結果を返します。

Notice 関数から`Promise`を返すことは、サービス応答を非同期に返すことが出来ることを意味します。

では、次のコードを使ってアクション(名前は`location_to_latlong`)を作りましょう。これまで述べたように、CLIやOpenWhisk UIを使用して、以前に学んだ方法と同じように作成する事ができます:

```javascript
var request = require("request");

function main(params) {
    var options = {
        url: "https://maps.googleapis.com/maps/api/geocode/json",
        qs: {address: params.text},
        json: true
    };

    return new Promise(function (resolve, reject) {
        request(options, function (err, resp) {
            if (err) {
                console.log(err);
                return reject({err: err});
            }

            if (resp.body.status !== "OK") {
                console.log(resp.body.status);
                return reject({err: resp.body.status});
            }

            resolve(resp.body.results[0].geometry.location);
        });
    });
}
```

次に、以前に学習した方法でアクションを展開しましょう:

<pre>
$ bx wsk action create location_to_latlong location_to_latlong.js
<b>ok:</b> created action <b>location_to_latlong</b>
</pre>

次に、アクションをテストしましょう:

<pre>
$ bx wsk action invoke location_to_latlong -b -r -p text "London"
{
    "lat": 51.5073509,
    "lng": -0.1277583
}
</pre>

## 位置情報からの予測

ここで、位置情報から予測を検索するサービスを実装しましょう。

このサービスでは、外部APIを使用して地域の天気予報を取得し、24時間以内に天気のテキストの説明を返します。

まず、*Weather Company Data service*.のインスタンスを作成する必要があります。
これをするには、画面の右上にある「カタログ」リンクをクリックしてください。
画面の左側に表示されるメニューから「データ＆分析」を選択します。
次に`Weather Company Data`をクリックします。
すべての設定をそのままにして、画面右下にある「作成」ボタンをクリックします。
次に、`サービス資格情報`タブに切り替え`資格情報の表示`リンクをクリックします。
※訳注）`資格情報の表示`が存在しない場合、`新規資格情報`を一度クリックし、作成します。
Note `username` and `password`を書き留めてください。もし米国南部に無いサービスを使用する場合は、`host`を書き留めてください。

次のコードを使用してアクション(名前は`forecast_from_latlong`)を作成しましょう:

```javascript
var request = require("request");

function main(params) {
    if (!params.lat) return Promise.reject("Missing latitude");
    if (!params.lng) return Promise.reject("Missing longitude");
    if (!params.username || !params.password) return Promise.reject("Missing credentials");
    var host = params.host || "twcservice.mybluemix.net";

    var url = "https://"+host+"/api/weather/v1/geocode/"+params.lat+"/"+params.lng+"/forecast/daily/3day.json";

    var options = {
        url: url,
        json: true,
        auth: {
            user: params.username,
            password: params.password
        }
      };

      return new Promise(function (resolve, reject) {
          request(options, function (err, resp) {
          if (err) {
              return reject({err: err});
           }
           resolve({text: resp.body.forecasts[0].narrative});
        });
    });
}
```

次に、アクションを再度デプロイしてみましょう:

<pre>
$ bx wsk action create forecast_from_latlong forecast_from_latlong.js
<b>ok:</b> created action <b>forecast_from_latlong</b>
</pre>

Notice サービスは４つのパラメータ、`緯度`と`経度｀が`サービス資格情報`(これまでに書き留めたもの)と一緒になることを期待しています。パラメータとして、`サービス資格情報`を渡すと、コード内に埋め込む必要がなくなり、実行時に動的に変更することが出来ます。

アクションをもう一度試してみましょう:

<pre>
$ bx wsk action invoke forecast_from_latlong -p lat "51.50" -p lng "-0.12" -p username &lt;username&gt; -p password &lt;password&gt; -p host &lt;host&gt; -b -r
{
    "text": "Partly cloudy. Lows overnight in the low 60s."
}
</pre>

リクエストごとに`サービス資格情報`を渡したくないので、デフォルトの別名バインドされたパラメータをアクションにバインドしましょう。つまり、`緯度`と`経度`パラメータでサービスを呼び出すだけです。

アクションを更新し、上記のパラメータをバインドしましょう:

<pre>
$ bx wsk action update forecast_from_latlong -p username &lt;username&gt; -p password &lt;password&gt; -p host &lt;host&gt;
<b>ok:</b> updated action <b>forecast_from_latlong</b>
</pre>

アクションをもう一度試してみましょう:

<pre>
$ bx wsk action invoke forecast_from_latlong -p lat "51.50" -p lng "-0.12" -b -r
{
    "text": "Partly cloudy. Lows overnight in the low 60s."
}
</pre>

## Slackへのメッセージ送信

予測ができたら、ボットのメッセージとして*Slack*に送信する必要があります。*Slack*は、*webhook*を利用して単純なボットを書く簡単な方法を提供します。着信webhookは、通常の*HTTP*リクエストを使用してデータを送信するURLをアプリケーションに提供します。*JSON*リクエスト本文の内容はぼっとメッセージとしてチャンネルに投稿されます。

まず、https://slack.com/にアクセスし、画面の一番上にある「新しいチームを作成」リンクをクリックして、*Slack*上に新しいチームを作成します。

指示に従ってチームを作成してください:
あなたの`メールアドレス`を入力し、`次へ`ボタンをクリックしてください。
メールで送信した`確認コード`を入力してください。
あなたの`名前`、`名字`、`ユーザ名`を入力して、`パスワード設定へ進む`ボタンをクリックします。
`パスワード`を設定し、`チーム情報設定へ進む`ボタンをクリックしてください。
`関心のあるグループ`のようなオプションを選択して*Slack*を使用する物を指定し、`グループ名設定に進む`ボタンをクリックします。
任意の`グループ名`を入力し、`チームURL指定へ進む`ボタンをクリックします。
任意の`チームURL`を入力し、`チームの作成`ボタンをクリックします。
`スキップ`ボタンをクリックして招待状を送信するプロセスをスキップし、`Slackを探索`ボタンをクリックして開始してください。
最後に、最下部のテキストフィールドに何かを入力して開始します。

コミュニケーションチャンネルを作成する方法は次のとおりです:
画面左側の`チャンネル`という文字の隣にある小さな`+`アイコンをクリックします
そして、あなたのチャンネルに`weather`という名前をつけて、`チャンネルを作成する`ボタンをクリックするだけです。

*Slack*が設定できました。

これらの*HTTP*リクエストの送信を行う別のアクション(マイクロサービス)を書くことが出来ましたが、既にご存知のように、OpenWhiskには、多くのサードパーティ製システムのインテグレーション(パッケージ提供)が付属しています。

利用可能なパッケージをもう一度見直してみましょう:

<pre>
$ bx wsk package list /whisk.system
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

ここでは`Slack`パッケージに注目します。この中にあるものを見てみましょう:

<pre>
$ bx wsk package get --summary /whisk.system/slack
<b>package</b> /whisk.system/slack: This package interacts with the Slack messaging service
   (<b>parameters</b>: username url channel token)
 <b>action</b> /whisk.system/slack/post: Post a message to Slack
</pre>

コードを記述することなく*Slack*にメッセージを投稿するアクションを呼び出す事ができます。

Notice 最初に着信webhookの`URL`をあなたのものに置き換える必要があります。あなたの着信webhookを知るには、次の手順で進めてください:

画面左上のあなたのチーム名をクリックします。
表示されるメニューから`App管理`を選択します。
画面上の`Appディレクトリを検索`に`incoming`を入力し、`着信Web フック`というエントリを選択します。
次に`設定を追加`をクリックします。
次に先ほど作成した`weather`チャンネルを選択し、`着信Web フック インテグレーションの追加`ボタンをクリックします。
次に`Webhook URL`項目に表示された`URL`をコピーします。

<pre>
$ bx wsk action invoke /whisk.system/slack/post -p url https://hooks.slack.com/services/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 -p channel weather -p text "Hello"
<b>ok:</b> invoked <b>/whisk.system/slack/post</b> with id <b>78070fe2acb54c70ae49c0fa047aee51</b>
</pre>

あなたの*Slack*チャンネルでメッセージがポップアップしているのが見えるということは、これがうまく行ったことを証明します。

繰り返しますが、アクションのデフォルトパラメータをバインドしたいのですが、グローバルパッケージをローカルネームスペースに最初にコピーしない限り、これはサポートされていません。

では、やってみましょう:

<pre>
$ bx wsk action create --copy webhook /whisk.system/slack/post -p url https://hooks.slack.com/services/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 -p channel weather -p username "Weather Bot" -p icon_emoji ":sun_with_face:"
<b>ok:</b> created action <b>webhook</b>
</pre>

カスタマイズされた*Slack*サービスは`text`パラメータだけで呼び出すことができ、`weather`チャンネルにフレンドリーなメッセージを送れます:

<pre>
$ bx wsk action invoke webhook -p text "Hello again"
<b>ok:</b> invoked <b>webhook</b> with id <b>a4044f7192544d69bc179a991a970559</b>
</pre>

## シーケンスを利用してお天気ボットを作成する

ボットのロジックを処理する3つのアクション(マイクロサービス)があります。ここでは、これらの3つの要素を以前に学習した方法でつなげるシーケンスを作成しましょう:

それではボットがこれらのサービスに参加するための新しいシーケンスを定義しましょう。

<pre>
$ bx wsk action create location_forecast --sequence location_to_latlong,forecast_from_latlong,webhook
<b>ok:</b> created action <b>location_forecast</b>
</pre>

このメタサービスを定義すると、最初のサービス（`text`）の入力パラメータで` location_forecast`アクションを呼び出すことができます。 その結果、その場所の予報が*Slack*に表示されます。

それでは試してみましょう。
結果は次のようになります:

<pre>
$ bx wsk action invoke location_forecast -p text "London" -b
<b>ok:</b> invoked <b>location_forecast</b> with id <b>d63b40bb36c54cfbaf8262b6f7e5c2e9</b>
{
    "activationId": "d63b40bb36c54cfbaf8262b6f7e5c2e9",
    "annotations": [],
    "end": 1473179750086,
    "logs": [],
    "name": "location_forecast",
    "namespace": "andreas.nauerz@de.ibm.com",
    "publish": false,
    "response": {
        "result": {},
        "status": "success",
        "success": true
    },
    "start": 1473179746914,
    "subject": "andreas.nauerz@de.ibm.com",
    "version": "0.0.1"
}
</pre>

アクションをWebアクションとして呼び出すには、次のように入力します:

<pre>
$ bx wsk action update location_forecast --web true
<b>ok:</b> updated action <b>location_forecast</b>
</pre>

## ボットの予報
ここでの問題は、ボットが予報に必要な場所をどのようにして知ることができるかです。
At this point the question is how we can ask the bot for forecasts about a location?

*Slack*は、チャンネルメッセージにキーワードが現れたときに*JSON*メッセージを外部のURLにポストする発信webhookを提供します。あなたのチャンネルに新しい発信Webhookを設定すると、ユーザーは`wetaher: london`という事でボットに応答させることができます。d.

したがって、定義されたトリガーワードで始まるメッセージが以前に作成した`weather`チャンネル経由で送信されると、アクションが適切に呼び出されていることを確認する必要があります。

これを実現するには、次のように進めます:
チャンネルに入ったら、一番上にある歯車アイコンをクリックし、表示されたメニューから `アプリを追加する`リンクを選択してください。
画面上の検索フィールドに`outgoing`という単語を入力し、`発信 Webフック`を選択します。
次に`設定を追加`と`発信 Webフック インタグレーションの追加`ボタンをクリックします。
インテグレーションの設定の`チャンネル`に以前に作成したチャンネルを選択します。
`引き金となる言葉`に`weather`を指定します。
`URL`には`location_forecast`アクションの`web action URL`を以下のように指定します:
`https://openwhisk.ng.bluemix.net/api/v1/web/andreas.nauerz@de.ibm.com_dev/default/location_forecast.json`  
次に、`設定を保存する`ボタンをクリックします。

次に、チャンネルを表示しているブラウザタブに戻り、チャンネルのメッセージ欄に以下を入力します:
`weather: london`

## トリガーに接続する

これまで学んだように、トリガーは外部からOpenWhiskへのイベントストリームを表すために使用されます。*REST* APIを使用して手動で呼び出すことも、トリガー フィードに接続してから自動的に呼び出すこともできます。アクションはルールを使用してトリガーにバインド出来ます。複数のアクションが同じトリガーを拾うすることが出来ます。

ボットサービスをサンプルトリガーにバインドし、そのトリガーを起動して間接的に呼び出す方法を見てみましょう:

<pre>
$ bx wsk trigger create forecast
<b>ok:</b> created trigger <b>forecast</b>

$ bx wsk rule create forecast_rule forecast location_forecast
<b>ok:</b> created rule <b>forecast_rule</b>

$ bx wsk trigger fire forecast -p text "London"
<b>ok:</b> triggered <b>forecast</b> with id <b>49914a20416d416d8c90282d59eebee3</b>
</pre>

トリガーを起動し、`text`パラメーターを渡すと、ボットは自動的に呼び出され、`London`の予報をチャンネルに投稿しました。

では、毎朝ボットを起動して仕事を開始する前に予報をしましょう。

## 朝の予報

新しい外部イベントが発生するたびに自動的に起動するようにトリガーを設定しましょう。これにより、ルールを介してこのトリガーにバインドされたすべてのアクションが呼び出されます。

これまでのように、トリガは、外部トリガフィードへの参照を渡すことによって、作成中に外部イベントソースにバインドされます。OpenWhiskのパブリックパッケージには、外部イベントソースに使用できる多数のトリガフィードが含まれています。

これらのパブリックトリガフィードの1つは、既に以前使用した`alarm`パッケージに含まれています。このように、このアラームフィードは予め指定された感覚でトリガを実行します。ウェザーボットのトリガーでこのフィードを使用することで、毎朝特定の住所について実行しロンドンの予報を行うことができます。

ではやってみましょう:

<pre>
$ bx wsk package get /whisk.system/alarms --summary
<b>package</b> /whisk.system/alarms: Alarms and periodic utility
   (<b>parameters</b>: cron trigger_payload)
 </b>feed</b>   /whisk.system/alarms/alarm: Fire trigger when alarm occurs

$ bx wsk trigger create regular_forecast --feed /whisk.system/alarms/alarm -p cron "*/10 * * * * *" -p trigger_payload "{\"text\":\"London\"}"
<b>ok:</b> created trigger <b>feed regular_forecast</b>

$ bx wsk rule create regular_forecast_rule regular_forecast location_forecast
<b>ok:</b> created rule <b>regular_forecast_rule</b>
</pre>

トリガースケジュールは`cron`パラメータによって提供されます。これはテストするために10秒ごとに実行するように設定されています。この新しいトリガーをボットサービスに結びつけることで、ロンドンの予報がチャンネルに表示されはじめます。

さて、うまくいったので怒られる前にアラームを止めておきましょう:

<pre>
$ bx wsk rule disable regular_forecast_rule
<b>ok:</b> rule <b>regular_forecast_rule</b> is <b>inactive</b>
</pre>

# Build a serverless microservice backend!

Actions can be seen as flexible and independently deployable microservices they are perfectly suited to build up entirely serverless microservice backends that expose functions via simply *APIs* (Application Programming Interfaces).

In this context APIs are the digital glue that links services, applications, sensors and mobile devices to create compelling customer experiences and help businesses tap into new market opportunities. They allow you to bring new digital services to market, open revenue channels and exceed customer expectations.

OpenWhisk's API Gateway integration is a new feature that enables you to easily expose your OpenWhisk actions as *RESTful* endpoints. You can assign actions to specific endpoints, and even have verbs (`GET`, `PUT`, `POST`, `DELETE`) from the same endpoint assigned to different actions.

There are two different approaches to expose your actions with the API gateway:
* Assigning API endpoint/verb combinations to specific actions individually
* Using a *Swagger* config file to map API endpoints to actions

You can define your APIs using our CLI or using our UI – in the following we will make use of both.

Notice that actions exposed via OpenWhisk's API Gateway integration are currently treated like web actions; hence once can make use of the properties `headers`, `status`, or `body` as seen before.

## Your first API

Let's examine how to expose an action able to generate Fibonacci numbers as a *REST* API.

Therefore, let's first have a look at the action itself:
It's a relatively simple action that generates numbers in a Fibonacci sequence, where every number after the first two is the sum of the two preceding values. When invoking this action from the command line, you specify a `num` parameter (for the n-th place in the sequence), and it will return the value, the complete sequence, and the number of recursive invocations of the Fibonacci method:

```javascript
var sequence = [1];
var invocations = 0;

function main(params) {
    invocations = 0;
    var int = parseInt(params.num);

    //num is a zero-based index
    return {
        body: "n: " + int + ", value: " + fibonacci(int) + ", sequence: " + sequence.slice(0,int+1) + ", invocations: " + invocations
    };
}

function fibonacci(num) {
    invocations ++;
    var result = 0;

    if (sequence[num] != undefined) {
        return sequence[num];
    }

    if (num <= 1 || isNaN(num)) {
        result = 1;
    } else {
        result = fibonacci(num-1) + fibonacci(num-2);
    }

    if (num >= 0) {
        sequence[num] = result;
    }

    return result;
}
```

Next, let's deploy and invoke the action:

<pre>
$ bx wsk action create fibonacci fibonacci.js
<b>ok:</b> created action <b>fibonacci</b>

$ bx wsk action invoke fibonacci -p num 5 -b -r
<b>ok:</b>  invoked action <b>fibonacci</b> with id <b>dde9212e686f413bb90f22e79e12df75</b>
{
  "body": "n: 5, value: 8, sequence: 1,1,2,3,5,8, invocations: 9"
}
</pre>

### Mapping actions to endpoints

Now, let's examine how a specific action can be associated with an API endpoint/verb. Using the OpenWhisk CLI, you must specify an `API path`, a `verb` (`GET`, `POST`, `PUT`, `DELETE`), and the `action`.

Notice that you may have to issue the following command and select the proper namespace before able to proceed:

<pre>
$ bx login -a api.ng.bluemix.net -o &lt;organization&gt; -s &lt;namespace&gt;
</pre>

If you are wondering what does the properties above mean, copy the full command from this link: https://console.bluemix.net/openwhisk/learn/cli

Now, we need to enable the action as web action:

<pre>
$ bx wsk action update fibonacci --web true
<b>ok:</b> updated action <b>fibonacci</b>
</pre>

Next, let's use the API path `/fibonacci`, and the verb `GET` to point to the action `fibonacci`:

<pre>
$ bx wsk api create /fibonacci get fibonacci
<b>ok:</b> created API /fibonacci GET for action <b>/_/fibonacci</b>
https://service.us.apiconnect.ibmcloud.com/gws/apigateway/api/8326f1d8a3dbc5afd14413a2682b7a78e17a55ee352f6c03f6be82718d69726e/fibonacci
</pre>

So, let's try it out:

<pre>
$ curl --request GET https://service.us.apiconnect.ibmcloud.com/gws/apigateway/api/8326f1d8a3dbc5afd14413a2682b7a78e17a55ee352f6c03f6be82718d69726e/fibonacci?num=10
n: 10, value: 89, sequence: 1,1,2,3,5,8,13,21,34,55,89, invocations: 19
</pre>

Notice that parameters that are passed via the query string will be available in the `params` object passed into the actions' `main` function.

## The Serverless Book Management Application

Now let's do something a bit more powerful: Let's say you want to expose a set of actions for managing books you have read etc. Therefore, you need to implement a couple of actions forming a serverless microservices backend for creating, reading, updating, and deleting books.

In the following we strongly recommend to use a *REST* client like *Insomnia*
(https://insomnia.rest/) to invoke the API endpoints that will be defined – otherwise you may get annoyed by escaping efforts. However, if you decide to use `curl` or another client, make sure you pass over the `application/json` header.

### Creating a Cloudant Instance

Let's assume you want to store the books in *Cloudant* as this would make things very easy as OpenWhisk already provides you with a *Cloudant* package that allows you to work with *Cloudant* without the need to write code.

From the OpenWhisk UI, click the `Catalog` (not the `Browse Public Packages`) link at the top right of the screen.  
From the menu on the left-side select `Data & Analytics`.  
Click `Cloudant NoSQL DB`.  
As service name specify `bookStore`, leave everything else as-is and click the `Create` button.

Once the instance has been created switch to the `Service Credentials` tab, create new credentials by clicking the `New credential` link and click the `View Credentials` link. You will need the `username`, `password` and `host` shown there throughout the rest of this chapter, hence leave this browser tab open.

### Creating a Database

Now that you have created a *Cloudant* instance, let's create a database for storing the books in.

OpenWhisk can automatically create package bindings for your (Bluemix) *Cloudant service* instances:

<pre>
$ bx wsk package refresh
_ refreshed successfully
created bindings:
Bluemix_bookStore_Credentials-1
[...]
</pre>

The refresh automatically creates a package binding for the *Cloudant service* instance that you created. To verify this:

<pre>
$ bx wsk package list
<b>packages</b>
/andreas.nauerz@de.ibm.com_dev/Bluemix_bookStore_Credentials-1        private
[...]
</pre>

Once again, to reveal the list of entities in the `/whisk.system/cloudant` package run the following command:

<pre>
$ bx wsk package get --summary Bluemix_bookStore_Credentials-1
<b>package</b> /whisk.system/cloudant: Cloudant database service
   (<b>parameters</b>: BluemixServiceName host username password dbname includeDoc overwrite)
<b>action</b> /whisk.system/cloudant/read: Read document from database
<b>action</b> /whisk.system/cloudant/write: Write document to database
<b>feed</b>   /whisk.system/cloudant/changes: Database change feed
[...]
</pre>

As before, to avoid the need to pass in the same parameters to the package's actions every time, let's bind certain parameters:

<pre>
$ bx wsk package bind /whisk.system/cloudant myBookStore -p username &lt;username&gt; -p password &lt;password&gt; -p host &lt;host&gt;
<b>ok:</b> created binding <b>myBookStore</b>
</pre>

One of the actions available is called `create-database`.
Let's use that one to create our database:

<pre>
$ bx wsk action invoke myBookStore/create-database -p dbname books
<b>ok:</b> invoked <b>/_/myBookStore/create-database</b> with id <b>3f67a23daa8b44efa35725fc22585f9</b>
</pre>

Now, we could easily store books into this database using the above package's `write` action. Alternatively, we could first map an API endpoint to this and other useful actions part of the package.

Before doing so let's update the binding so that we do not even need to pass in the `dbname` anymore:

<pre>
$ bx wsk package update myBookStore -p username &lt;username&gt; -p password &lt;password&gt; -p host &lt;host&gt; -p dbname books
<b>ok:</b> updated package <b>myBookStore</b>
</pre>

Before mapping our actions, let's create a `query index` for the field `name` so we can query books by name later on:

<pre>
$ bx wsk action invoke myBookStore/create-query-index -p index "{\"index\": {},\"type\":\"text\"}" --blocking
<b>ok:</b> invoked <b>/_/myBookStore/create-query-index</b> with id <b>4g67a23daa8b44efa35725fc22585f0</b>
</pre>

As we currently do not allow package bound actions to be enabled as web actions and as we, at the same time, require an action to be a web action in order to be able to expose it via our API Gateway we have to perform a little trick: We have to implement a simply proxy action that can be enabled as web action and simply calls the package bound action holding all the previously defined parameters using sequencing. But this is not a trick only, executing such proxy actions before and after the actual action is often even required: The action supposed to be executed before the actual action often has to take care of things like authentication while the one supposed to be executed after the actual action often has to perform data transformations.

Hence, create an action named `proxy` like this:

```javascript
function main(params) {
    return params;
}
```

Next, we create 3 sequences and enable them as web actions:

<pre>
$ bx wsk action create endpoint_get --sequence proxy,myBookStore/exec-query-find --web true
<b>ok:</b> created action <b>endpoint_get</b>

$ bx wsk action create endpoint_post --sequence proxy,myBookStore/write --web true
<b>ok:</b> created action <b>endpoint_post</b>

$ bx wsk action create endpoint_delete --sequence proxy,myBookStore/delete-document --web true
<b>ok:</b> created action <b>endpoint_delete</b>
</pre>

Next, let's map API endpoints to actions:

<pre>
$ bx wsk api create /books GET endpoint_get
<b>ok:</b> created API /books GET for action <b>/_/endpoint_get</b>
https://service.us.apiconnect.ibmcloud.com/gws/apigateway/api/8326f1d8a3dbc5afd14413a2682b7a78e17a55ee352f6c03f6be82718d69726e/books

$ bx wsk api create /books POST endpoint_post
<b>ok:</b> created API /books POST for action <b>/_/endpoint_post</b>
https://service.us.apiconnect.ibmcloud.com/gws/apigateway/api/8326f1d8a3dbc5afd14413a2682b7a78e17a55ee352f6c03f6be82718d69726e/books

$ bx wsk api create /books DELETE endpoint_delete
<b>ok:</b> created API /books DELETE for action <b>/_/endpoint_delete</b>
https://service.us.apiconnect.ibmcloud.com/gws/apigateway/api/8326f1d8a3dbc5afd14413a2682b7a78e17a55ee352f6c03f6be82718d69726e/books
</pre>

Now, let's store some books.
To do so use your *REST* client to submit a `POST` against the endpoint you have created prior.

Make sure to hand-over the following *JSON* data:

```json
{
    "doc": {
        "name":"firstBook"
    }
}
```

Output:

```json
{
    "ok": true,
    "id": "1d6e1a830c5a9c4bc62db7f14b1e91e0",
    "rev": "1-5cb5fef5b2fc44f78cb181f6ace8f8db"
}
```

And then:

```json
{
    "doc": {
        "name":"secondBook"
    }
}
```

Output:

```json
{
    "ok": true,
    "id": "2e6e1a830c5a9c4bc62db7f14b1e91e0",
    "rev": "2-6cb5fef5b2fc44f78cb181f6ace8f8db"
}
```

Next, let's query all books.
To do so use your *REST* client to submit a `GET` against the endpoint you have created prior.

Hand-over the following query parameter to define the *selector*:

```json
{
    "selector":{
        "name":{
            "$regex":".*"
        }
    },
    "sort":[
        {
            "name":"asc"
        }
    ]
}
```

Output:

```json
{
    "docs": [
        {        
	    "_id": "d67e49d6a85d20e9e2ec45710d12d816",
	    "_rev": "1-f4e3fbffab0b7dfcf8677c68689bf9c3",
	    "name": "firstBook"
	},
	{
	    "_id": "26a38a8193722046b2cac60e66dbb0f5",
	    "_rev": "1-3db09f4b6275c63cafca157112bb01d0",
	    "name": "secondBook"
	}
],
[...]
```

Next, let's query a particular book.
To do so use your *REST* client to submit a `GET` again.

Hand-over the following query parameter to define the selector:

```json
{
    "selector":{
        "name":{
            "$eq":"firstBook"
        }
    },
    "sort":[
        {
            "name":"asc"
        }
    ]
}
```


Output:

```json
{
    "docs": [
        {
	    "_id": "d67e49d6a85d20e9e2ec45710d12d816",
	    "_rev": "1-f4e3fbffab0b7dfcf8677c68689bf9c3",
	    "name": "firstBook"
	}
    ],
[...]
```

Now, let's delete the book we just queried.  
To do so use your *REST* client to submit a `DELETE`.  
Hand-over the `docid` and `docrev` of the book you just queried as query parameter.

Again, query all books to validate that the book has been properly deleted.

## Expose your weather services

Now, let's make the previously implemented weather services (functions) accessible via some simple APIs, too. This time we will use the UI (instead of the CLI) to define these APIs.

Again, open the OpenWhisk UI.  
Select the `API` tab.  
Click the `Create an OpenWhisk API` button (only visible if you haven't created any API before).  
As `API name` specify `weatherAPI`.  
Leave everything else as-is and click the `Save` button at the bottom of the screen.

On the next screen select the `Definition` tab from the navigation on the left of the screen.  
Click the `Create Operation` button.  
As `path` specify `location_to_latlong`.  
As `action` select the `location_to_latlong` action.  
Leave everything else as-is and click the `Save` button at the bottom of the screen.  
Click the `Save` button at the bottom of the screen.

To find out the `URL` to be used to invoke this operation switch to the `API Explorer` tab.  
From the list at the left select the `getLocationtolatlong` entry and copy the `GET URL` shown.  
Open a browser window and append they query parameter `?location=London`.  
You should be presented the latitude and longitude of London.

Switch back to the `Definition` tab and, based on what you just learned, expose the `forecast_from_latlong` action by adding another operation, too.

At this point feel free to play around with other features of our API Gateway integration.

# Composing more complex serverless applications

So far we have developed application logic only contained in single actions (aka functions) that could have been seen as loosely coupled microservices. They were rather simple and focussed on very specific tasks.

While the implementation of such microservices is rather simple, their composition or orchestration is way more complex. That's why frameworks like Kubernetes with additions like Istio have meanwhile become very popular. With IBM's new *Composer*  developers can now build apps that leverage multiple functions and that require more complex, coordinated flows for end to end solutions.

Composer is a programming model (extension) for composing individual functions into larger applications. *Compositions*, informally named *apps*, run in the cloud using automatically managed compute and memory resources. Composer enables stateful computation, control flow, and rich patterns of data flow.

This means Composer allows to develop more complex serverless applications by combining multiple functions using control logic and state.

Composer has two parts: The first is a library for describing compositions, programmatically. The library is currently available in Node.js. The second is a runtime that executes the composition.

## Getting started

To work with compositions the new functions programming shell (aka `fsh`) is required.

Hence, let's first install `fsh`:

<pre>
$ npm install -g @ibm-functions/shell
</pre>

After the installation, you can find out about `fsh`'s basic capabilities like this:

<pre>
$ fsh
Welcome to the IBM Cloud Functions Shell

Usage information:
[...]
</pre>

## Your first Composition

Compositions can be defined via JSON or, alternatively, using Node code that relies on the Composer SDK. In the following we will focus on the second approach which usually feels more natural for developers.

Combinators accept either inline Node functions or functions (aka actions) by name. For the latter, you can either use functions' fully qualified or short names.

One of the easiest to understand and out-of-the-box available composition methods is the `if`:
`composer.if(condition, consequent, alternate)` runs either the `consequent` task if the condition evaluates to true or the `alternate` task if not. The `condition`, `consequent`, and `alternate` tasks are all invoked on the input parameter object for the composition. The output parameter object of the condition task is discarded.

Let's first define the composition (and store it in a file named `demo_if.js`):

```javascript
composer.if('condition', /* then */ 'success', /* else */ 'failure')
```

This composition defines that if the function `condition` evaluates to `true` the function `success` is being executed and the function `failure` otherwise.

Now, let's define the three aforementioned functions.

First, the function `condition` (to be stored in a file named `condition.js`):

```javascript
function main(params) {
	if (params.password == "andreas") {
		return { value: true };
	}

    return { value: false };
}
```

Notice that returning a JSON object with a key names `value` and a value of type boolean is essential.

Second, the function `success` (to be stored in a file named `success.js`):

```javascript
function main() {
    return { message: "Success" };
}
```

Third, the function `failure` (to be stored in a file named `failure.js`):

```javascript
function main() {
    return { message: "Failure" };
}
```

Next, deploy the three functions:

<pre>
$ fsh action create condition condition.js
$ fsh action create success success.js
$ fsh action create failure failure.js
</pre>

Next, deploy the actual composition:

<pre>
$ fsh app create demo_if demo_if.js
</pre>

Before invoking the composition one can visualize it by entering `fsh app preview demo_if.js`.

Next, invoke the composition, first without specifing a password which should cause the function `condition` to return `false` and hence the function `failure` to be invoked:

<pre>
$ fsh app invoke demo_if
{
    message: "Failure"
}
</pre>

Finally, invoke the composition again, this time with specifing the password `andreas` which should cause the function `condition` to return `true` and hence the function `success` to be invoked:

<pre>
$ fsh app invoke demo_if -p password andreas
{
    message: "Success"
}
</pre>

By entering `fsh session get <session id>` one can also visualize the results of an invocation - try it out!

## More Compositions

Another easy to understand and out-of-the-box available composition methods is the `repeat`:
`composer.repeat(count, task)` runs `task` `count` times.

Let's first define the composition (and store it in a file named `demo_repeat.js`):

```javascript
composer.repeat(5, 'task_repeat')
```

Now, let's define the function `task_repeat` (to be stored in a file named `task_repeat.js`):

```javascript
function main(params) {
    x = params.count;
    x++;

    return { count: x };
}
```

Notice that the output of each invocation serves as input for the next invocation.

Next, deploy the function and composition:

<pre>
$ fsh action create task_repeat task_repeat.js
$ fsh app create demo_repeat demo_repeat.js
</pre>

Next, invoke the composition:

<pre>
$ fsh app invoke demo_repeat -p count 10
{
    count: 15
}
</pre>

And finally, another easy to understand and out-of-the-box available composition methods is the `while`:
`composer.while(condition, task)` runs `task` repeatedly while `condition` evaluates to true.

Let's first define the composition (and store it in a file named `demo_while.js`):

```javascript
composer.while('condition_while', 'task_while')
```

Now, let's define the function `condition_while` (to be stored in a file named `condition_while.js`):

```javascript
function main(params) {
    x = Math.random();

    if (x > 0.2) {
        return { value: true};
    }

    return { value: false };
}
```

Next, let's define the function `task_while` (to be stored in a file named `task_while.js`):

```javascript
function main(params) {
    x = params.count;
    x++;

    return { count: x };
}
```

Next, deploy the functions and composition:

<pre>
$ fsh action create condition_while condition_while.js
$ fsh action create task_while task_while.js
$ fsh app create demo_while demo_while.js
</pre>

Next, invoke the composition:

<pre>
$ fsh app invoke demo_while -p count 10
{
    count: 18
}
</pre>

Notice that you may get a different result (count) due to the random number being relevant here.

Once again, we recommend entering `fsh session get <session id>` again to visualize the results of the invocations.

An overview of all currently available compositions can be found here:
https://github.com/ibm-functions/composer/tree/master/docs#compositions-by-example

## Nesting and data-forwarding

An important property of combinators is that they can be nested. This encourages modularity and reuse.

Nesting and data-forwarding between nested compositions can best be explained along another example. Let's say you want to chain together two actions. The first action called `reverse` is supposed to reverse any *String* being handed over. The second action called `output` is supposed to dump the original (non-reverted) input *String* as well as the (reverted) *String*.

The `task_reverse` action (to be stored in `task_reverse.js`) could look as follows:

```javascript
function main(params) {
    return { reverse: params.input.split("").reverse().join("") };
}
```

The `task_output` action (to be stored in `task_output.js`) could look as follows:

```javascript
function main(params) {
    return { message: params.input + " reverted is: " + params.reverse };
}
```

The composition to chain both actions together (to be stored in `demo_nesting.js`) could look as follows:

```javascript
composer.sequence('task_reverse', 'task_output')
```

The `composer.sequence(task_1, task_2, ...)` composition runs a sequence of tasks (possibly empty). The input parameter object for the composition is the input parameter object of the first task in the sequence. The output parameter object of one task in the sequence is the input parameter object for the next task in the sequence. The output parameter object of the last task in the sequence is the output parameter object for the composition.

Now, let's deploy all artifacts:

<pre>
$ fsh action create task_reverse task_reverse.js
$ fsh action create task_output task_output.js
$ fsh app create demo_nesting demo_nesting.js
</pre>

Unfortunately, when invoking the composition we do not get really what we want:

<pre>
fsh invoke demo_nesting -p input myText -r
{
    message: "undefined reverted is: txeTym"
}
</pre>

It seems as if the sequencing (i.e. the nesting) itself works but the value of the initial input parameter is being lost.
This is because the output of the `task_reverse` is only the reverted *String*; the initial input *String* gets indeed lost.
Hence, what we need in addition to the sequencing as some data-forwarding capability.

This is what the `retain` composition is good for: `composer.retain(task[, flag])` runs `task` on the input parameter object producing an object with two fields `params` and `result` such that `params` is the input parameter object of the composition and `result` is the output parameter object of `task`.

That being said let's change our composition like that:

```javascript
composer.sequence(composer.retain('task_reverse'), 'task_output')
```

Let's change our `task_output` action like that:

```javascript
function main(params) {
    return { message: params.params.input + " reverted is: " + params.result.reverse };
}
```

Notice that the "first" `params` refers to the argument being passed to the `main` method.
The "second" `params` results from the use of the `retain` composition and provides access to the initial `input` as well as the `result` of the invocation of the `task_reverse` action.

Let's update the artifacts:

<pre>
$ fsh action update task_output task_output.js
$ fsh app update demo_nesting demo_nesting.js
</pre>

And try again:

<pre>
fsh invoke demo_nesting -p input myText -r
{
    message: "myText reverted is: txeTym"
}
</pre>

Works like a charm.

Once again, we recommend entering `fsh session get <session id>` again to visualize the results of the invocations.

## Inline coding

As said before it is not always necessary to define functions' code within separate files.
Especially simple logic can always be defined inline.

Let's define a composition (and store it in a file named `demo_inline.js`):

```javascript
composer.task(params => ({message: `Hello ${params.name}!`}))
```

Then, deploy it:

<pre>
$ fsh app create demo_inline demo_inline.js
</pre>

Finally, let's simply invoke it:

<pre>
fsh invoke demo_inline -p name Andreas -r
{
    message: "Hello Andreas!"
}
</pre>

To learn more about Composer visit: https://github.com/ibm-functions/composer

# IBM App Connect & Message Hub

*IBM App Connect* allows you to connect different applications to make your business more efficient. It allows you to set up automation flows to direct how events in one application trigger actions in another. It also allows you to map the information you want to share between them.

*IBM Message Hub* is an *IBM Bluemix* managed *Apache Kafka*, a scalable, high-throughput message bus service for building real-time data pipelines and streaming applications. It allows you to wire together micro-services using open protocols, to connect stream data to analytics to realize powerful insights, to feed event data to multiple applications to react in real time. It allows you to bridge to your on-premise messaging infrastructure to create a hybrid cloud messaging solution.

In the following we will show you can use *App Connect* to post data to a dedicated *Message Hub* topic which then causes an OpenWhisk trigger to fire to invoke an action.

First, let's set up a *Message Hub* instance and a topic:

From the OpenWhisk UI, click the `Catalog` (not the `Browse Public Packages`) link at the top right of the screen.  
From the menu appearing on the left of the screen select `Application Services`.  
Click `Message Hub`.  
Leave all settings as they are and click the `Create` button at the bottom right of the screen.  
Switch to the `Manage` tab and click the `+` icon to create a new topic. As topic name specify `openwhisk`.  
Leave all other settings as they are and click the `Create topic` button.

Second, let's set up an *App Connect* instance:  
Open a new browser tab.  
From the OpenWhisk UI, click the `Catalog` (not the `Browse Public Packages`) link at the top right of the screen.  
From the menu appearing on the left of the screen select `Integrate`.  
Click `App Connect`.  
Leave all settings as they are and click the `Create` button at the bottom right of the screen.

Now, sign-up for a *Salesforce* test account as we would like *App Connect* to post something to *Message Hub* (then causing the OpenWhisk trigger to fire and the action to be invoked) as soon as a new contact is being created:

Open a new browser tab and navigate to https://www.salesforce.com/form/signup/freetrial-sales.jsp  
Fill out all fields shown on the right-hand side and click the `Start free trial` button.

Navigate back to the browser tab showing your *App Connect* instance (if you do not have it anymore, click `Catalog` again, then, click the `hamburger` icon at the very left of the screen select `Apps` and then `Dashboard`).  
If necessary click the `Launch App Connect` button and skip the dialogs offering initial help.  
Click the `New` button and select `Create an event-driven flow` button.  
Click `Salesforce` and `New Contact`.  
Click the `Connect to Salesforce` button.  
In the new browser tab appearing click the `Allow` button.  
Back in the *App Connect* view click `Message Hub` and `Send Message`.  
Click the `Connect to Message Hub` button.  
Now, navigate back to the browser tab showing your *Message Hub* instance (if you do not have it anymore, click `Catalog` again, then, click the `hamburger` icon at the very left of the screen select `Services` and then `Dashboard`).  
Switch to the `Service Credentials` tab and click the `View Credentials` link.  
Click the icon that allows you to copy the entire *JSON* being shown.  
Next, navigate back to the browser tab showing your *App Connect* instance and paste the entire *JSON* you have just copied into the field labeled `Message Hub Service Credentials` and click the `Connect` button.  
As topic specify `openwhisk`.  
As payload select (after having clicked the little helper icon next to the input field) `lastname` for instance.  
Finally, click the `Exit and switch on button` at the top right of the screen.  

Now, let's make sure a trigger fires whenever something is being posted to the *Message Hub* topic we just created:

In the OpenWhisk UI switch to the Develop tab if not already there.  
Create a new action (name it `newContact`) the way you learned it before containing the following code:

```javascript
function main(params) {
    return { "message": "New Salesforce contact data: " + params.message };
}
```

Click the `Automate this Action` button at the bottom right of the screen to make sure it gets fired by a trigger.  
Click `Messaging`.  
Click `New Trigger`.  
Provide all the details being asked for (which you can get by navigating back to the browser tab showing your *Message Hub* instance and having a look at the service credentials).  
Click the `Save Configuration` button.  
Click the `This Looks Good` and the `Save Rule` buttons.  
Finally, switch to the `Monitor` tab to see what's going on.  

Navigate back to the browser tab showing the *Salesforce* application.  
From the main menu click `Contacts`.  
Click the `New` button at the top right of the screen.  
Provide at least a `given` and `family name` and click `Save`.  

When navigating back to the OpenWhisk UI monitoring should reveal that the action we just created has been invoked with information about the *Salesforce* contact that has just been created.

To summarize, the App Connect flow took care of posting to a dedicated *Message Hub* topic once a new *Salesforce* contact has been created. That caused the OpenWhisk Messaging trigger to fire which caused the associated action to be invoked.

# Special fuel for your engine!

OpenWhisk also integrates with other (3rd party) tools to provide developers with a great developer experience. In the following we have picked two simple samples to demonstrate such kind of integration.

## Developing with VS Code

Many, especially *JavaScript/NodeJS*, developers use *VS Code* to develop their code.
We have recently developed a prototypical extension (not yet officially supported) for *VS Code* that enables complete round trip cycles for authoring OpenWhisk actions inside the editor.

The key point for this extension is that it has full round trip for OpenWhisk actions (*list, create new local, create new remote, update, import from remote system, invoke, etc.*) without the need to leave the IDE which makes development cycles far shorter and easier. The extension works for action written in different languages (like *JavaScript and Swift*) and on different platforms (like *Windows, Mac, and Linux*).

In the future we plan to provide more such plug-ins for additional IDEs (this is no official commitment) and hence seek for early feedback.

First, download *VS Code* for your platform from here: https://code.visualstudio.com/  
Next, download the extension from here: https://ibm.box.com/s/r4pdwpdmmceubzpnmsskp3hoh65r9zmm (in the near future you will also get the latest code from here: https://github.com/openwhisk/openwhisk-vscode#downloads)

To install the extension open *VS Code* and switch to the extensions view (`View → Extensions`).  
Click the `more` menu (represented by the `•••` icon at the very top) and select `install from VSIX...`  
Point to the `VSIX file` you downloaded.

Once you have the extension installed, you will have to run `wsk property set` inside of *VS Code* to set the `apihost`, `auth`, and `namespace` values the same way you did configure your local CLI at the very beginning of this workshop:

Open the command palette via `View → Command Palette` or by pressing `F1` and entering: `wsk property set`

When prompted select the option `apihiost` and press `enter`.  
Next enter the correct value for the apihost property.

In the console you should see a result like this:

<pre>
$ wsk property set apiHost openwhisk.ng.bluemix.net
set config: apiHost=openwhisk.ng.bluemix.net
Configuration saved in C:\Users\IBM_ADMIN\.openwhisk\vscode-config.json
</pre>

Repeat this procedure to set the proper values for the `auth` and `namespace` properties.

Notice that you can retrieve the correct values from here (after clicking the `Looking for the wsk CLI?` link at the very bottom of the screen): https://new-console.ng.bluemix.net/openwhisk/cli

Now switch to the explorer (`View → Explorer`), implement the following action and save the file (name it `helloFromVSCode.js`):

```javascript
function main() {
    return { message: "Hello from VS Code" };
}
```

Press `F1` to open the command palette and enter:

<pre>
wsk action create
</pre>

When prompted enter an identifier for your action like `helloFromVSCode`.

In the console you should see a result like this:

<pre>
$ wsk action create helloFromVSCode
Creating a new action using the currently open document: file:///c%3A/Users/IBM_ADMIN/Downloads/helloFromVSCode.js
OpenWhisk action created: andreas.nauerz@de.ibm.com_dev/helloFromVSCode
</pre>

Finally, invoke the action by opening the command palette (`F1`) again and entering:

<pre>
wsk action invoke
</pre>

From the pull-down menu appearing select the action to be invoke – in our case the one named `helloFromVSCode`.

In the console you should see a result like this:

<pre>
$ wsk action invoke helloFromVSCode
......
{
    "result": {
        "message": "Hello from VS Code"
    },
    "success": true,
    "status": "success"
}
>> completed in 1855ms
</pre>

Feel free to experiment with the extension a bit on your own and provide us with any feedback you have.

## Developing with the Serverless Framework

Very recently we have announced our integration with the *Serverless Framework* (https://serverless.com/framework/). Hence, developers can now use the framework to build applications for the OpenWhisk platform.

The *Serverless Framework* is the most popular open-source framework for building serverless applications. Launching back in 2015, under a different name, the framework has experienced tremendous growth and now has over fourteen thousands stars on *Github*.

Thousands of developers are using the tool to build serverless applications every day.

Using a simple manifest file, developers can define serverless functions, connect them to event sources and declare cloud services needed by their application.

The framework handles deploying these serverless applications to the cloud provider. It also allows developers to monitor services in production, roll-out updates and assist debugging issues.

It also has a vibrant ecosystem of third-party plugins to extend the functionality of the framework.

With the aforementioned integration developers using the framework can now choose to deploy their serverless applications to any OpenWhisk platform instance. Multi-provider support also means moving applications between platforms is much easier and developers can even develop multi-cloud serverless applications.

### Installing the Serverless Framework

First, let's install the framework and the required dependencies:

<pre>
$ sudo npm install --global serverless serverless-openwhisk
</pre>

Now, using the `create` command, you can create an example service from the following the [following template](https://github.com/serverless/serverless/tree/master/lib/plugins/create/templates/openwhisk-nodejs).

<pre>
$ serverless create --template openwhisk-nodejs --path my_service
$ cd my_service
$ npm install
</pre>

Finally, let's deploy (the boilerplate includes an example that can be deployed without modification):

<pre>
$ serverless deploy
</pre>

If the deployment succeeds, the following messages will be printed to the console:

<pre>
$ serverless deploy
Serverless: Packaging service...
Serverless: Compiling Functions...
Serverless: Compiling API Gateway definitions...
Serverless: Compiling Rules...
Serverless: Compiling Triggers & Feeds...
Serverless: Deploying Functions...
Serverless: Deployment successful!

Service Information
platform:	openwhisk.ng.bluemix.net
namespace:	_
service:	my_service

actions:
[...]

triggers:
[...]

rules:
[...]

endpoints:
[...]

web-actions:
[...]
</pre>

### Working with Actions

First open the `serverless.yml` file and try to understand its basic structure which is pretty self-explanatory. It defines some of the OpenWhisk entities you have learned about before. For instance, it defines the name of your service (which represents the collection of all your artifacts), some functions (aka actions) and properties of these (like the handlers containing their code) and so forth. It also defines that you are working with OpenWhisk as your serverless engine.

One of the functions defined in the `serverless.yml` is called `hello`. The corresponding code lives in the file `handler.js` and, to be more precise, in a function the handler is pointing to (which is the function `main`).

Now, let's use the `invoke` command to test your newly deployed service:

<pre>
$ serverless invoke --function hello
{
    "payload": "Hello, World!"
}
</pre>

And once again with parameters:

<pre>
$ serverless invoke --function hello --data '{"name": "OpenWhisk"}'
{
    "payload": "Hello, OpenWhisk!"
}
</pre>

### Working with Sequences

Open the `serverless.yml` file and let's define a sequence by reusing the actions `myUtil/sort` and `myUtil/head` again. To define the sequence simply extend the `functions` section like this:

<pre>
functions:
    [...]
    mySequence:
        sequence:
            - /_/myUtil/sort
            - /_/myUtil/head
</pre>

Next, redeploy the service:

<pre>
$ serverless deploy
</pre>

Next, test the sequence:

<pre>
$ serverless invoke --function mySequence --data '{"lines":["c","b","a"]}'
{
    "lines": [
        "a"
    ],
    "num": 1
}
</pre>

### Connecting API Endpoints

Open the `serverless.yml` file and define an API endpoint for the `hello` action we have worked with prior. To define the endpoint simply extend the `functions` section like this:

<pre>
functions:
    [...]
    hello:
        handler: hello.handler
        events:
            - http: GET /api-demo/hello
</pre>

Next, redeploy the service:

<pre>
$ serverless deploy
</pre>

Watch the output of the redeployment.

Use the shown endpoints' `URL` to invoke the `hello` action:

<pre>
$ curl --request GET <endpoint_URL>
{
  "payload": "Hello, World!"
}
</pre>

### Working with Triggers and Rules

Open the `serverless.yml` file and define an triggers and rules for the `hello` action we have worked with prior. To define the endpoint simply extend the `functions` section like this:

<pre>
functions:
    [...]
    hello:
        handler: hello.handler
        events:
            - http: GET /api-demo/hello
            - trigger: myHelloTrigger
            - rule: myHelloRule
</pre>

Next, redeploy the service:

<pre>
$ serverless deploy
</pre>

Test the trigger and rule the same way you did earlier.
You can learn more about using the *Serverless Framework* here: https://github.com/serverless/serverless-openwhisk

### Packaging your weather services

At this point it may be a good exercise to package together the previously implemented weather services using the *Serverless Framework* – we leave this as a voluntary exercise for you.

# Node-RED and OpenWhisk

Especially IoT (Internet of Things) applications are often held up as a great use-case for serverless platforms.

Serverless platforms are ideally suited to building solutions for the IoT. IoT applications are inherently event-driven and come with unpredictable traffic patterns. Often, applications involve listening for data from externally connected devices and passing it through to an internally data store after applying some transformations.

In this section, we're going to look at using a popular open-source tool for integrating IoT devices, APIs and online services with OpenWhisk.

*Node-RED* describes itself as a "visual tool for wiring the Internet Of Things". It comes with a browser-based editor that allows you to visually build up "message flows", connecting devices to online APIs and services. Once the message flow has been developed, developers can deploy that flow to the backend service to make it live. *Node-RED* represents devices, online services and APIs as individual "nodes". The tool has a huge community of 3rd nodes for almost every kind of hardware device, APIs and third-party services.

OpenWhisk recently released nodes for *Node-RED* to represent actions and triggers. These nodes allow to create and invoke those resources through the *Node-RED* runtime. Using *Node-RED* with the OpenWhisk nodes allows you to easily build IoT applications that integrate with a serverless platform.

## Installing Node-RED

Let's start by getting *Node-RED* installed locally.

Notice that *Node-RED* uses the `Node.js` runtime and the `Node Package Manager` to allow developers to install the tool as a command-line utility. If you haven't got Node.js installed locally, please get the environment running first (https://nodejs.org/en/).

Running this command will install *Node-RED* as a command-line utility available to all users:

<pre>
$ npm install -g node-red
</pre>

This command will take a while to install *Node-RED* with all its dependencies. `npm` will generate lots of log messages during this process but this is normal. If the command finishes without an error, we can start to use the application.

## Starting Node-RED

Once *Node-RED* is installed, the tool can be started with the following command:

<pre>
$ node-red
</pre>

You should see the following output:

<pre>
Welcome to Node-RED
===================
24 Oct 10:33:00 - [info] Node-RED version: v0.15.1
24 Oct 10:33:00 - [info] Node.js  version: v6.2.2
24 Oct 10:33:00 - [info] Darwin 16.0.0 x64 LE
24 Oct 10:33:00 - [info] Loading palette nodes
24 Oct 10:33:01 - [info] Settings file  : /Users/andreas/.node-red/settings.js
24 Oct 10:33:01 - [info] User directory : /Users/andreas/.node-red
24 Oct 10:33:01 - [info] Flows file     : /Users/andreas/.node-red/flows.json
24 Oct 10:33:01 - [info] Server now running at http://127.0.0.1:1880/
24 Oct 10:33:01 - [info] Starting flows
24 Oct 10:33:01 - [info] Started flows
</pre>

Once you have started *Node-RED*, the server is running on port 1880.  
Open a web browser and visit the following URL to open the editor: http://localhost:1880

*Nodes* are available in the palette on the left-hand side of the screen. Users build "message flows" by dragging the nodes from the left-hand panel and dropping them on the grid. Nodes on the grid can be connected together with "wires" to exchange messages.

Notice that if you want more documentation for *Node-RED*, the project website has excellent information here: http://nodered.org/docs/

## "Hello World" with Node-RED

Let's start by building a "Hello World" example to get you started:

### Add an Inject node

The `Inject` node allows you to inject messages into a flow, either by clicking the button on the node, or setting a time interval between injects.

Drag one onto the workspace from the palette.  
Next, open the sidebar (`Ctrl-Space`, or via the dropdown menu) and select the `Info` tab.  
Then, select the newly added `Inject` node to see information about its properties and a description of what it does.

### Add a Debug node

The `Debug` node causes any message to be displayed in the debug sidebar. By default, it just displays the payload of the message, but it is possible to display the entire message object.

Drag one onto the workspace from the palette.

### Wire the two together

Connect the `Inject` and `Debug` nodes together by dragging between the output port of one to the input port of the other.

### Deploy

At this point, the nodes only exist in the editor and must be deployed to the server.  
Therefore, click the `Deploy` button - simple as that.

With the debug sidebar tab selected, click the (little left rectangle of the) `Inject` button. You should see numbers appear in the sidebar. By default, the `Inject` node uses the number of milliseconds since January 1st, 1970 as its payload. Let's do something more useful with that.

### Add a Function node

The `Function` node allows you to pass each message though a *JavaScript* function.

Wire the `Function` node in between the `Inject` and `Debug` nodes. You may need to delete the existing wire (select it and hit `delete` on the keyboard).

Next, double-click on the `Function` node to bring up the edit dialog.  
Copy the following code into the function field:

```javascript
// Create a Date object from the payload
var date = new Date(params.payload);
// Change the payload to be a formatted Date string
params.payload = date.toString();
// Return the message so it can be sent on
return params;
```

Next, click `Done` button to close the edit dialog and then click the `Deploy` button.   
Now, when you click the `Inject` button again, the messages in the sidebar will be more readable time stamps.

That example should give you an idea about how to use the editor to connect nodes together to build flows and deploy them to the *Node-RED* runtime.

Feel free to have a play around with the other nodes in the palette and build more complex flows.

## Adding Nodes to Node-RED

Node-RED has a huge community of external developers who publish nodes for connecting to thousands of 3rd party devices, APIs and online services.

This website catalogues all the available nodes: http://flows.nodered.org/

Let's look at installing the OpenWhisk nodes into *Node-RED*:  
If you type `openwhisk` into the search box, we see there are two entities that can be installed.

We want to install the result named `node-red-node-openwhisk`, which contains the officially supported OpenWhisk nodes for *Node-RED*.

So, if you go back into the *Node-RED* editor and click the menu icon in the top-right of the screen, it presents a menu including the `Manage palette` item.
Selecting this option will bring up the node installation dialoge in the left-hand panel. This panel shows you the list of installed nodes and also allows you to install extra nodes. We're going to use this to install the OpenWhisk nodes.

Select the `Install` tab and type in `node-red-node-openwhisk`.  
Click the `install` button.

Now, once you return to the main palette menu, you should see the OpenWhisk nodes.

### Invoking OpenWhisk actions from Node-RED

Now that we have the nodes installed, let's start by creating a flow which invokes an OpenWhisk action:

First, drag the OpenWhisk `action` node onto the flow panel.  
Make sure you drag the `action` node (not the `trigger` node) that has both input and output ports. The input port receives messages that triggers the action and (optionally) passes in parameters for the invocation. The output port returns the activation response message.

Next, double-click the node icon to open the editor panel.  
This editor panel allows us to define the name and namespace for the action to invoke when messages are received on the flow. These parameters can be overridden at runtime by passing parameters in as properties on the message object.

But, before we can invoke the action, we need to setup the OpenWhisk platform that *Node-RED* will talk to.

Click on the `pencil` icon next to the service configuration drop-down:

Fill in the form with the following details:
* `API URL`: `https://openwhisk.ng.bluemix.net/api/v1`
* `Auth Key`: Once again the auth key you can obtain when clicking Use the CLI (https://console.ng.bluemix.net/openwhisk/cli)
* `Name`: `OpenWhisk`

Finally, click the `Add` button to add this service to *Node-RED*.

Now, we can fill in the `action name` and `namespace` values (which you can, once again, obtain when clicking Use the CLI) for the action we want to invoke.  
Let's try it with the `hello` action you defined in the previous exercises.  
Then, once again, add an `Inject` node and a `Debug` node to the flow grid (unless you still have them because you haven't deleted them earlier). Wire up both nodes to the OpenWhisk action node.  
Then, deploy the flow using the `Deploy` button on the top-right hand corner.  
Now, once you click the `Inject` node a few times, it should trigger our action and print the results of the invocation to the debug panel:

<pre>
{"message: "Hello, undefined from undefined"}
</pre>

Next, let's update the flow to include a `name` parameter in the incoming message generated by the `Inject` node. Therefore, open the `Inject` node's editor panel and change the payload type to *JSON*.

Add the following field value:

<pre>
{"name": "Bernie"}
</pre>

Now, save the `Inject` node configuration, deploy the flow and try injecting a few messages again. It should return the response message with the name `Bernie Sanders` included in the greeting:

<pre>
{"message: "Hello, Bernie from undefined"}
</pre>

Hopefully that worked fine, so add a parameter for `place` on your own before you move onto firing triggers using *Node-RED*.

## Invoking OpenWhisk Triggers from NodeRED

Drag the OpenWhisk `Trigger` node onto the flow panel.

The `Trigger` node has a single input port which receives messages that fires the trigger and (optionally) passes in parameters for the invocation.

Now, double-click the node icon to open the editor panel.  
This editor panel allows us to define the `name` and `namespace` for the trigger to fire when messages are received on the flow. These parameters can be overridden at runtime by passing parameters in as properties on the message object.  
Once again, we can fill in the `service`, `name` and `namespace` values for the trigger we want to invoke. Since we have already setup the service credentials for OpenWhisk in the previous task, we don't need to this again.

Let's try it with the `locationUpdate` trigger you defined in the previous exercises:

First, save your node configuration before returning to the flow editor screen.  
Next, connect the `Inject` node on the screen to the `trigger` node you have just created.  
Next, deploy the flow using the `Deploy` button on the top-right hand corner.  
Now, test out this new invocation by clicking the `Inject` node a few times.

If you check the invocation logs using the `wsk` command-line utility, do you see the activations appear?

### Creating New OpenWhisk Actions from Node-RED

The OpenWhisk nodes also allow you to define new actions through the *Node-RED* editor panels. Using the code editor within the configuration panel, you can create and update the source for existing and new actions.

Let's try updating the source code for our existing `hello` action:

First, double-click the OpenWhisk `action` node to reveal the editor panel.  
The source code for the action should automatically be displayed.  
Next, let's change the greeting string that the action returns.  
Therefore, select the `Allow Edits` checkbox.  
Modify the greeting string to be:

<pre>
"Salutations " + params.name + "!"
</pre>

Now, add a new parameter with `key` (`name`) and `value` (`Donald`).  
Next, click `Done` to save your changes.  
Before we update the flow, let's set the payload of the `Inject` node back to timestamp, so that the action uses the default parameter.  
Finally, once you have done this, deploy the flow and check out the results in the console. This time it should return us the new message with the update greeting and default parameter.

# The coolest engines out there!

At this point in time we would like to show you four publicly available samples illustrating what you can build with OpenWhisk.

## Vision App

Details about Vision App can be found here:  
https://github.com/IBM-Bluemix/openwhisk-visionapp

Vision App is a sample iOS application to automatically tag images and detect faces by using IBM visual recognition technologies. It allows you to take a photo or select an existing picture to let the application generate a list of tags and detect people, buildings, objects in the picture. It then allows you to share the results with your (social) network.

## Dark Vision

Details about Dark Vision can be found here:  
https://github.com/IBM-Bluemix/openwhisk-darkvisionapp

Dark Vision processes videos to discover dark data. By analyzing video frames with IBM Watson Visual Recognition, Dark Vision builds a summary with a set of tags and famous people or building detected in the video. Use this summary to enhance video search and categorization.

## Skylink

Details about Skylink can be found here:  
https://github.com/IBM-Bluemix/skylink

Skylink is a sample application that lets you connect a DJI drone aircraft to the *IBM Cloud* with near realtime image analysis leveraging *IBM Cloudant, OpenWhisk, IBM Watson, and Alchemy Vision*.

# Learning more

Important resources:

* IBM Cloud Functions: https://www.ibm.com/cloud-computing/bluemix/de/openwhisk
* Apache OpenWhisk: http://openwhisk.org
* OpenWhisk on Github: https://github.com/openwhisk/openwhisk/
* OpenWhisk on Twitter: https://twitter.com/openwhisk
* OpenWhisk on Medium: https://medium.com/openwhisk
* OpenWhisk additional material on Slideshare: http://www.slideshare.net/OpenWhisk
* OpenWhisk additional material on Youtube: https://www.youtube.com/channel/UCbzgShnQk8F43NKsvEYA1SA
* OpenWhisk on Slack: http://slack.openwhisk.org/
* Other OpenWhisk material: https://github.com/openwhisk/awesome-openwhisk
* Composer: https://github.com/ibm-functions/composer
* The Serverless Framework: https://github.com/serverless/serverless-openwhisk
* Node-RED: https://nodered.org/
