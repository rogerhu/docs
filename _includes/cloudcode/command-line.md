# Command Line Tool

The Parse command line tool allows you to interact with your Cloud Code from the terminal.
Some of the topics here are also covered in the [Cloud Code guide](#cloud-code), but they are repeated here for clarity.

## Installation

### Mac and Linux

In Mac OS X and Linux/Unix environments, you can get the parse command line tool by running this command:

```bash
curl -s https://www.parse.com/downloads/cloud_code/installer.sh | sudo /bin/bash
```

This installs a tool named "parse" to `/usr/local/bin/parse`.
There's no other junk, so to uninstall, just delete that file.
This will also update your command line tool if you already have it installed.

### Windows

The Parse command line tool for Windows is available [here](https://github.com/ParsePlatform/parse-cli/releases/latest).
Download the Windows executable named: `parse.exe` at this link. Note that this is not an installer, it is just a plain Windows executable.

## Creating a Parse app

You can create a new Parse app using `parse new`.
It asks you a series of questions and at the end of it, you will have a new Parse app (with the given name).
Additionally, you will also set up a Cloud Code project for the app.
You can test that everything works by performing `parse deploy` and executing the curl command printed at the end of output.

```bash
$ parse new
Would you like to create a new app, or add Cloud Code to an existing app?
Type "(n)ew" or "(e)xisting": n
Please choose a name for your Parse app.
Note that this name will appear on the Parse website,
but it does not have to be the same as your mobile app's public name.
Name: MyParseApp
Which of these providers would you like use for running your server code:
1) Heroku (https://www.heroku.com)
2) Parse  (https://parse.com/docs/cloudcode/guide)
Type 1 or 2 to make a selection: 2

Awesome! Now it's time to set up some Cloud Code for the app: "MyParseApp",
Next we will create a directory to hold your Cloud Code.
Please enter the name to use for this directory,
or hit ENTER to use "MyParseApp" as the directory name.

Directory Name: my_parse_app

You can either set up a blank project or create a sample Cloud Code project.
Please type "(b)lank" if you wish to setup a blank project, otherwise press ENTER:
Successfully configured email for current project to: "${ACCOUNT_EMAIL}"
Your Cloud Code has been created at ${CUR_DIR}/my_parse_app.

This includes a "Hello world" cloud function, so once you deploy,
you can test that it works, with the printed curl command.

Next, you might want to deploy this code with:

parse deploy

Once deployed you can test that it works by running:

curl -X POST \
 -H "X-Parse-Application-Id: ${APPLICATION_ID}" \
 -H "X-Parse-REST-API-Key: ${REST_API_KEY}" \
 -H "Content-Type: application/json" \
 -d '{}' \
 https://api.parse.com/1/functions/hello
```

## Setting up Cloud Code

If you already created a Parse app (for example, through the website or apps API) and want to set up Cloud Code for the app, `parse new` can still help you.

```bash
$ parse new
Would you like to create a new app, or add Cloud Code to an existing app?
Type "(n)ew" or "(e)xisting": e
1:	MyApp
2:	MyOtherApp
Select an App to add to config: 1
Which of these providers would you like use for running your server code:
1) Heroku (https://www.heroku.com)
2) Parse  (https://parse.com/docs/cloudcode/guide)
Type 1 or 2 to make a selection: 2

Please enter the name of the folder where we can download the latest deployed
Cloud Code for your app "MyApp"

Directory Name: myapp

You can either set up a blank project or download the current deployed Cloud Code.
Please type "(b)lank" if you wish to setup a blank project, otherwise press ENTER:
Successfully configured email for current project to: "${ACCOUNT_EMAIL}"
Your Cloud Code has been created at ${CUR_DIR}/myapp.

This includes a "Hello world" cloud function, so once you deploy,
you can test that it works, with the printed curl command.

Next, you might want to deploy this code with:

	parse deploy

Once deployed you can test that it works by running:
curl -X POST \
 -H "X-Parse-Application-Id: ${APPLICATION_ID}" \
 -H "X-Parse-REST-API-Key: ${REST_API_KEY}" \
 -H "Content-Type: application/json" \
 -d "{}" \
 https://api.parse.com/1/functions/hello
```

Note: This flow assumes that you already set up [account keys](#command-line-account-keys).
See also [multiple account keys](#command-line-multiple-account-keys), if you are developing on apps belonging to different Parse accounts.

The `myapp` directory looks like:

```bash
myapp
├── cloud
│   └── main.js
├── .parse.local
├── .parse.project
└── public
    └── index.html
```

`.parse.local` and `.parse.project` are config files that store application info, and project level info (for instance, Javascript SDK version), respectively.
The `cloud` directory stores your Cloud Code,
and the `public` directory stores any static content that you want to host on Parse.
In the `cloud` directory, you'll typically just be editing `main.js`, which stores all of your Cloud Code functions.

For now, just check that these files were created successfully.
If you're using source control, you can check all of these files in.

We recommend using source control to check in all of these files.
If you're not already set up with source control, try this
[tutorial from GitHub](https://guides.github.com/introduction/getting-your-project-on-github/).

The same code can be deployed to multiple different applications.
This is useful so that you can have separate `development` and `production` applications.
Then you test the code on a development application before launching it in production.

The first application that is added (by the `new` command) will be the default application for all command line operations.
All commands except `new` take an optional app name that the command will be performed on.

## Deploying

To deploy a new release, run `parse deploy` from the command line:
```bash
$ parse deploy
Uploading source files
Uploading recent changes to scripts...
The following files will be uploaded:
...
Uploading recent changes to hosting...
The following files will be uploaded:
...
Finished uploading files
New release is named vx (using Parse JavaScript SDK vx.x.x)
```

This pushes the new code (in `cloud` and `public`) to the Parse Cloud and deploys this code for the default target which is the first app that was added or the one you set using `parse default`.
You can choose to deploy to a different target by adding the target as an argument to `deploy`:

```bash
$ parse deploy "My Other App"
Uploading source files
Uploading recent changes to scripts...
The following files will be uploaded:
.....
Uploading recent changes to hosting...
The following files will be uploaded:
.....
Finished uploading files
New release is named vx (using Parse JavaScript SDK vx.x.x)
```

You can add release notes to the deploy with the `-d` or `--description` option

If the contents of your parse project remain unchanged then we skip deploy. You will see an output like:

```bash
$ parse deploy
Uploading source files
Finished uploading files
Not creating a release because no files have changed
```

You can override this behavior with the `-f` or `--force` flag. Providing this flag forces a deploy despite no changes to your project.

When embedding `parse deploy` within other scripts (such as in an automated testing/deploy environment) you can rely on the exit code from the Parse command line tool to indicate whether the command succeded.
It will have an exit code of 0 on success and a non-zero exit code when the deploy failed.

### A Simple Function

Following ancient tradition, let's see how to run the simplest possible function in the cloud. If you take a look at `cloud/main.js`, you'll see an example function that just returns a string:

```javascript
Parse.Cloud.define("hello", function(request, response) {
  response.success("Hello world!");
});
```

To deploy the code from your machine to the Parse Cloud, run this from the command line:

```bash
$ parse deploy
```

Once deployed, this function can be called from any of the SDKs or the command line like so:

```java
// Android SDK
ParseCloud.callFunctionInBackground("hello", new HashMap<String, Object>(), new FunctionCallback<String>() {
  void done(String result, ParseException e) {
    if (e == null) {
      // result is "Hello world!"
    }
  }
});
```
```objectivec
// iOS, OS X, tvOS, watchOS SDK: Objective-C
[PFCloud callFunctionInBackground:@"hello"
                   withParameters:@{}
                            block:^(NSString *result, NSError *error) {
   if (!error) {
     // result is @"Hello world!"
   }
}];
```
```swift
// iOS, OS X, tvOS, watchOS SDK: Swift
PFCloud.callFunctionInBackground("hello", withParameters: nil) {
  (response: AnyObject?, error: NSError?) -> Void in
  let responseString = response as? String
}
```
```php
// PHP SDK
$result = ParseCloud::run("hello", []);
```
```cs
// .NET SDK
ParseCloud.CallFunctionAsync<IDictionary<string, object>>("hello", new Dictionary<string, object>()).ContinueWith(t => {
  var result = t.Result;
// result is "Hello world!"
});
```
```javascript
// JavaScript SDK
Parse.Cloud.run('hello', {}).then(function(result) {
  // result is 'Hello world!'
}, function(error) {
	// handle error
});
```
```bash
# REST API, command line
curl -X POST \
 -H "X-Parse-Application-Id: ${APPLICATION_ID}" \
 -H "X-Parse-REST-API-Key: ${REST_API_KEY}" \
 -H "Content-Type: application/json" \
 -d '{}' \
 https://api.parse.com/1/functions/hello
```

You should see this response:

```json
{ "result": "Hello world!" }
```

Congratulations! You've successfully deployed and run Cloud Code.

This is a good time to play around with the deployment cycle.
Try changing `"Hello world!"` to a different string, then deploy and run the function again to get a different result.
The whole JavaScript SDK is available in Cloud Code, so there's a lot you can do.
The [Cloud Code guide](#cloud-code) goes over various examples in more detail.

## Developing Cloud Code

You can also run the Parse command line tool in development mode using the `develop` command.
This will make the tool watch the source directory for any updates and deploy them to Parse.
It also provides a live stream of Cloud Code logs.

```bash
$ parse develop hello
Your changes are now live.
I2015-08-15T17:01:02.214Z]Deployed v2 with triggers:
  Cloud Functions:
    hello

I2015-08-06T01:30:19.366Z]Deployed v1 with triggers:
  Cloud Functions:
    hello

I2015-08-06T01:30:25.110Z]v1 Ran cloud function hello with:
  Input: {}
  Result: Hello world!
```

Unlike the other commands, for `develop` you must specify the Parse App to push updates to.
This is to avoid accidentally running develop on your production app causing you to run untested code in your production app.

## Adding a New Target

You can add a new Parse app as a target by running the `add` command.

```bash
$ parse add
1: MyApp
2: MyOtherApp
Select an App to add to config:
```

The `add` command takes an optional argument which is an alias to assign to the application that can be used instead of the app name.

## Setting the Default App

`parse deploy`, `parse logs`, `parse rollback`, and `parse releases` use the default app to be run against the commands.
`parse default` allows you to change this default app.

```bash
$ parse default MyApp
Default app set to MyApp.
$ parse default
Current default app is MyApp.
```

## Rolling Back

You can roll back a release using `parse rollback`.
Just like with `parse deploy`, you can specify an optional target argument.

```bash
$ parse rollback
Rolled back to v1
```

This rolls back to the previous version of the code.
You can also specify the release name to roll back to by using the `-r` or `--release` option.

## Reading the Logs

Every deploy, rollback, and activation of Cloud Code is logged.
You can retrieve the end of logs using the `parse logs` command. There are two types of logs:

*   `INFO` - contains everything.
*   `ERROR` - contains only the errors.

The logs command takes an optional target as well as two options:

*   -f, --follow=false: Emulates tail -f and streams new messages from the server
*   -l, --level="INFO": The log level to restrict to. Can be 'INFO' or 'ERROR'.
*   -n, --num=10: The number of the messages to display

```bash
$ parse logs -n 1
I2015-08-15T17:01:02.214Z] beforeSave handler in release 'v1' ran for GameScore with the input:
  {"original": null, "update":{"score": 1337}}
 and failed validation with Each GamesScore must have a playerName
```

## Listing Releases

You can list the known set of releases on the Parse Cloud with the `releases` command.
Parse only tracks the last 10 releases.

```bash
$ parse releases
Name                            Description                     Date
v14                             Add background job              2015-08-11T18:12:52Z
v15                             No release notes given          2015-08-22T18:45:30Z
v16                             Moved to webhooks               2015-08-26T21:30:06Z
...
```

To view all files uploaded in a given release you can use the `-v` or `--version` option.

```bash
$ parse releases -v v14
Deployed cloud code files:
main.js

Deployed public hosting files:
index.html
```

## Setting the Javascript SDK version

The default Parse JavaScript SDK version that is used for the Cloud Code in this directory is the latest version at the time the `new` command was run for this directory.
If you want to change this, use `parse jssdk`.
You can see all available Parse JavaScript SDKs using `parse jssdk -a`.
You can also use `parse jssdk` to check which Parse JavaScript SDK version is currently being used.

```bash
$ parse jssdk
Current JavaScript SDK version is 1.5.0
$ parse jssdk -a
*  1.5.0
   1.4.2
   1.4.0
   1.3.5
   1.3.4
   1.3.3
   1.3.2
$ parse jssdk 1.4.2
Current JavaScript SDK version is 1.4.2
$ parse jssdk -a
   1.5.0
*  1.4.2
   1.4.0
   1.3.5
   1.3.4
   1.3.3
   1.3.2
```

## Updating

You can update the command line tool using `parse update`.
It will update your Parse command line tool to the latest version.

For instance, if you run this command on Mac OS X:

```bash
Downloading binary from https://github.com/ParsePlatform/parse-cli/releases/download/release_${LATEST_VERSION}/parse.
Successfully updated binary at: /usr/local/bin/parse
```

## Secure Config Format

If you are using a version of command line tool older than 2.2.2 the Cloud Code project you create has the following structure:

```bash
.
├── cloud
│   └── main.js
├── config
│   └── global.json
└── public
    └── index.html
```

All project configuration like:
applications to deploy to and their master keys,
default application associated with the project and
Javascript SDK version to use are stored in the config file at `config/global.json`.

If you did not want to expose your master keys but still wanted to share your code with someone, and want them to use the
same project level configurations (for instance, Javascript SDK version) that was not possible.

We have now broken up the config into two parts:
* `.parse.project`: project level config
* `.parse.local`: user level config

This is the new project structure:
```bash
├── cloud
│   └── main.js
├── .parse.local
├── .parse.project
└── public
    └── index.html
```

Storing master keys along with application code is in general not a good idea. Master keys can be used to perform various irreversible operations
on your application, and it is impossible to track down the offender.

Whether you are using an older version of config or the latest preferred version, going forward the command line tool (version > 2.2.2)
will not store master key when you perform `parse add`. Further, `parse new` will always create a project with the latest preferred
config format.

If you wish to migrate to the latest preferred, more secure config format you can use the `parse migrate` command

```bash
$ parse migrate
Successfully migrated to the preferred config format.
```

If for some reason, you still want to store master keys in the config format use the `--retain` or `-r` option.

## Account Keys

Account Keys are personal access tokens that can be used to create a new Parse app and list properties and keys for
the given Parse app. You can configure the command line tool to use account keys to perform various actions like
creating a new app and fetching master key for an app when required (for instance, when performing deploy, fetching logs, etc.).

As noted in this [section](#secure-config-format), we'll not store master keys in config files anymore. Instead we'll fetch them as needed.

### What does this mean for a regular deploy flow?

If you want to perform a deploy, you'll need the master key. Master key for the selected app can be fetched by using the [apps API](https://parse.com/docs/rest/guide#apps).
But apps API requires user authentication in the form of (`email`, `password`) with which you registered for the Parse account, or a valid
account key (created for that account).

If you do not configure account keys, you'll be prompted to enter an email & password. This disruption in the flow (for all commands
requiring master key), can easily be averted by configuring an account key using the `parse configure accountkey` command.

```bash
$ parse configure accountkey
Input your account key or press enter to generate a new one.
Account Key: ${ACCOUNT_KEY}
Successfully stored account key for: "${ACCOUNT_EMAIL}".
```

Account keys you configure are stored in `${HOME}/.parse/netrc`

You can configure multiple account keys, one for each Parse account (i.e., each unique email).
This is helpful if you have multiple Parse accounts.

However, if you have just one Parse account, then it's very useful to configure a default account key.

```bash
$ parse configure accountkey -d
Input your account key or press enter to generate a new one.
Account Key: ${ACCOUNT_KEY}
Successfully stored default account key.
```

### Multiple Account Keys
Let's say you have two Parse accounts: `ME` (your Parse account) and `WORK` (account you are a collaborator on).
You are working on `app-ME` (owned by `ME`) and `app-WORK` (owned by `WORK`).
Now if you want to deploy code to `app-ME`, you need to use the account key for `ME`.
To deploy to `app-WORK`, you'll need to use the account key for `WORK`.

####How would the command line tool know which account key to use?

You tell it what to use by configuring an email for the project using `parse configure email ${ACCOUNT_EMAIL}`.
This gets stored in the project config: `.parse.project` and any time you need to fetch the master key, this config setting helps identify
which account key to use. If it does not find any account key for the configured email, it will try to use the default
account key instead.

So, it is always a good idea to configure your default account key.

It is slightly different when you want to create an app or list properties for an app belonging to a specific account.
You can select the Parse account to use by providing the environment variable: `PARSER_EMAIL`

For instance, in Mac or Linux
```bash
PARSER_EMAIL=${ACCOUNT_EMAIL} parse new
PARSER_EMAIL=${ACCOUNT_EMAIL} parse list
```

In windows, (on powershell)
```bash
cmd /C set "PARSER_EMAIL=%ACCOUNT_EMAIL%" && parse new
cmd /C set "PARSER_EMAIL=%ACCOUNT_EMAIL%" && parse list
```

Alternatively, you can just set it as a system wide environment variable, so any subsequent parse commands including: deploy, logs, etc. also use the same Parse account.

### How to continuously deploy to your Parse app from Github
It is very easy to deploy to your Parse app on every push to Github.
For this we use [Travis CI](http://docs.travis-ci.com/user/getting-started/), a popular continuous integration framework.

Steps to follow:
- Set up your github repository with [travis](http://docs.travis-ci.com/user/getting-started/): you can use the `.travis.yml` in this [repo](https://github.com/pavanka/travis-hello) as a starting point.
- Add your [Parse account key](https://parse.com/docs/js/guide#command-line-account-keys) as a [secret](http://docs.travis-ci.com/user/encryption-keys/) environment variable.

That is all there is to it!
If you like to learn more, please take a look at this [github repository](https://github.com/pavanka/travis-hello).

## Webhooks

You can use the `parse configure hooks` command to configure webhooks for your Parse app very easily.

All you have to do is write a json file with the following rules:

```bash
# content of the file is an array of operations
{"hooks": [OPERATION]}

# where OPERATION is any of
OPERATION ->
{"op": "put", "function": {"functionName": "name", "url": "https_url"}}
{"op": "post", "function": {"functionName": "name", "url": "https_url"}}
{"op": "delete", "function": {"functionName": "name"}}

{"op": "put", "trigger": {"className": "cname", "triggerName": "tname", "url":"https_url"}}
{"op": "post", "trigger": {"className": "cname", "triggerName": "tname", "url":"https_url"}}
{"op": "delete", "trigger": {"className": "cname", "triggerName": "tname"}}
```

Content of the json file is `{"hooks" : [...]}`, where the operation is a json object indicating an `op` like: `put`, `post`, `delete`; on
a function or trigger webhook.

Example usage:

```bash
# you can provide a config file name to use
parse configure hooks webhooks.json

# you can also uses piping to provide configuration through stdin
cat webhooks.json| parse configure hooks
```

NOTE: The `configure hooks` command handles common error cases like, creating an already existing webhook and modifying or deleting a non-existant webhook.
So for instance, if you try to modify a non-existant webhooks, it will create it for you. If you are trying to create an existant webhook, it will modify the current
webhook with the url provided in configuration. It will skip deletion of non-existant webhooks.

This comes in pretty handy if you want to automate webhook creation or want to dynamically modify them.

A sample webhooks config file looks like follows:

```json
{
  "hooks": [
    {
      "op": "post",
      "function": {
        "functionName": "hello",
        "url": "/webhooks/function_hello"
      }
    },
    {
      "op": "post",
      "trigger": {
        "className": "TestObject",
        "triggerName": "beforeSave",
        "url": "/webhooks/beforeSave_TestObject"
      }
    },
    {
      "op": "post",
      "trigger": {
        "className": "TestObject",
        "triggerName": "afterSave",
        "url": "/webhooks/afterSave_TestObject"
      }
    },
    {
      "op": "post",
      "trigger": {
        "className": "TestObject",
        "triggerName": "beforeDelete",
        "url": "/webhooks/beforeDelete_TestObject"
      }
    },
    {
      "op": "post",
      "trigger": {
        "className": "TestObject",
        "triggerName": "afterDelete",
        "url": "/webhooks/afterDelete_TestObject"
      }
    }
  ]
}
```

Note that we did not provide the full `https` url, instead just provided the relative path.
You can register these webhooks using the `configure hooks -b` command.

```bash
parse configure hooks -b https://example.ngrok.io
```

Here we used the `-b` or `--base` option to pass the base webhook url to use.
The registered `hello` function would look like:

```bash
Function name: "hello", URL: "https://example.ngrok.io/webhooks/function_hello"
```
