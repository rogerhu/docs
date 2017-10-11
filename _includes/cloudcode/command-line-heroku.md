# Command Line Tool with Heroku

We've partnered with [Heroku](https://www.heroku.com/) to create a smooth experience for you to run your server code on either Heroku or the Parse Cloud.
Before adding native support for Heroku to the Parse Tools, you could accomplish the same thing, but needed to follow many steps like, creating the Heroku app, deploying to it, book keeping between a Parse app and its corresponding Heroku app, registering the webhooks, etc.
With this integration all these steps are managed by the Parse Tool, for most simple use cases you can run your server code on Heroku being totally agnostic about Heroku tools.
Please refer to the announcement [here](http://blog.parse.com/announcements/introducing-heroku-parse/) for more details.

## How does it work?
When you want to add server code to your Parse app, you can choose either Heroku or Parse.
If you choose Heroku, we'll automatically create a Heroku app for you and use it as the backend for running your server code.
You'll be able to deploy to the Heroku app, view its logs and current releases, and rollback a release, just as you do with Parse Cloud Code.
All you have to do is link your Heroku account to Parse (just visit this [page](https://parse.com/account/edit) and click the “Link Heroku” button at the bottom).
Note that by linking your Heroku account to your Parse account, you're only authorizing Parse to create Heroku apps on your behalf. Parse does not have any other permissions.
For instance, we will not be able to list all your Heroku apps, change settings for Heroku apps not created through Parse, or change your Heroku account settings.
This ensures additional security and makes the linking more trustworthy to even the advanced Heroku users.

### What happens under the hood
Once you link Heroku to Parse, we'll have an access token that can create apps on Heroku.
For any app thus created, Heroku provides us with an app-scoped access token, that can perform various actions on the created Heroku app and it alone.
We store these credentials in our database at Parse.
Whenever, you or a collaborator of your Parse app performs an action on server code, we generate a unique access token and use it to perform
actions like deploying, fetching logs, and viewing releases of your Heroku app.

### Can I link an existing Heroku app to a Parse app?
Unfortunately, this is not possible at this time.
If you have an existing Heroku app for your webhooks, you can continue to use the Heroku CLI to deploy to it according to our [blog post](http://blog.parse.com/learn/using-node-js-with-parse/).

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

## Creating a Parse app backed by Heroku

You can create a new Parse app using `parse new`.
It asks you a series of questions and at the end of it, you will have a new Parse app (with the given name), linked to a Heroku app
where your server code will be run.
You can test that everything works by performing `parse deploy` and executing the curl command printed at the end of output.

```bash
$ parse new
Would you like to create a new app, or add Cloud Code to an existing app?
Type "(n)ew" or "(e)xisting": n
Please choose a name for your Parse app.
Note that this name will appear on the Parse website,
but it does not have to be the same as your mobile app's public name.
Name: MyHerokuApp
Which of these providers would you like use for running your server code:
1) Heroku (https://www.heroku.com)
2) Parse  (https://parse.com/docs/cloudcode/guide)
Type 1 or 2 to make a selection: 1

Let's create a new Heroku app in which server code will be run.
The Heroku app will be named: "myherokuapp-bpru0l-4485"
Note that this can be changed later using Heroku API or Dashboard.

Awesome! Now it's time to set up some server code for the app: "MyHerokuApp",
Next we will create a directory to hold your server code.
Please enter the name to use for this directory,
or hit ENTER to use "MyHerokuApp" as the directory name.

Directory Name:

You can either set up a blank project or create a sample Node.js project.
Please type "(b)lank" if you wish to setup a blank project, otherwise press ENTER:
Successfully configured email for current project to: "${PARSER_EMAIL}"
Your server code has been created at ${CUR_DIR}/${CODE_DIR}.

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

## Setting up server code on Heroku

You can set up server code on Heroku for an existing Parse app very easily using `parse new` command.
It does not matter whether the given Parse app already has Cloud Code or not.


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
Type 1 or 2 to make a selection: 1

Let's create a new Heroku app in which server code will be run.
The Heroku app will be named: "myapp-9j6adf-8139"
Note that this can be changed later using Heroku API or Dashboard.

Awesome! Now it's time to set up some Cloud Code for the app: "MyApp",
Next we will create a directory to hold your Cloud Code.
Please enter the name to use for this directory,
or hit ENTER to use "MyApp" as the directory name.

Directory Name: myapp

You can either set up a blank project or create a sample Node.js project.
Please type "(b)lank" if you wish to setup a blank project, otherwise press ENTER:
Successfully configured email for current project to: "${PARSER_EMAIL}"
Your server code has been created at ${CUR_DIR}/${CODE_DIR}.

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

Note: This flow assumes that you already set up [account keys](https://parse.com/docs/cloudcode/guide#command-line-account-keys).
See also [multiple account keys](https://parse.com/docs/cloudcode/guide#command-line-multiple-account-keys), if you are developing on apps belonging to different Parse accounts.

The `myapp` directory looks like:

```bash
myapp
├── .parse.local
├── .parse.project
├── Procfile
├── README.md
├── cloud
│   └── main.js
├── package.json
├── public
│   └── index.html
├── scripts
│   ├── record-webhooks.js
│   └── register-webhooks.js
└── server.js
```

`.parse.local` and `.parse.project` are config files that store application info, and project level info, respectively.
The `cloud` directory stores your server code,
and the `public` directory stores any static content that you want to host on Heroku.
In the `cloud` directory, you'll typically just be editing `main.js`, which stores all the server side logic for your app.

For now, just check that these files were created successfully.
If you're using source control, you can check all of these files in.

We recommend using source control to check in all of these files.
If you're not already set up with source control, try this
[tutorial from GitHub](https://guides.github.com/introduction/getting-your-project-on-github/).

The same code can be deployed to multiple different applications.
This is useful so that you can have separate "development" and "production" applications.
Then you test the code on a development application before launching it in production.

The first application that is added (by the `new` command) will be the default application for all command line operations.
All commands except `new` take an optional app name that the command will be performed on.

## Deploying
### What does deploy mean?
When you execute `parse deploy` we will deploy your code or config changes to the Heroku app that runs server code for your Parse app.
For node.js, if you use our middleware, we have a post install script that automatically registers webhooks for your Parse app.
If you choose to not use our middleware or write server code in a language that does not yet have Parse middleware support,
you can just provide a `webhooks.json` file in the same directory, and the Command Line Tool will automatically register these webhooks with Parse.
Note that this is a very important step because Parse is only aware of those endpoints (of the Heroku app) that you register as webhooks with Parse.
For more information on the `webhooks.json` file format please read the [docs](https://parse.com/docs/cloudcode/guide#command-line-webhooks).
This functionality is very useful even if you are an advanced Heroku user. Until now, you had to perform a deploy on Heroku via
`git push heroku master` and also manage the registration of webhooks with Parse. But with this functionality, you do both in just one step - saving the context switch!

To deploy a new release, run `parse deploy` from the command line:

This deploys the code (in `cloud` and `public`) for the Heroku app corresponding to the default target which is the first app that was added or the one you set using `parse default`.
You can choose to deploy to a different target by adding the target as an argument to `deploy`:

```bash
$ parse deploy
Successfully set the following vars to heroku:
HOOKS_URL https://myherokuapp-9j6adf-8139.herokuapp.com/
PARSE_APP_ID ************************************itMu
PARSE_MASTER_KEY ************************************RZ3Z
PARSE_WEBHOOK_KEY ************************************i8Ja
Initialized empty Git repository in ${CUR_DIR}/${CODE_DIR}/.git/
...
remote: Building source:
...
remote:        > Parse-Cloud-Code-Heroku@1.0.0 postinstall /tmp/build_e8aa01307d875739c887afce4663e3a2
remote:        > NODE_PATH=$NODE_PATH:./lib node scripts/register-webhooks.js
remote:        Hooks registration completed.
remote:        Registered function for: hello
remote:        jsonfile@2.2.3 node_modules/jsonfile
...
remote: Verifying deploy... done.
...

$ parse functions
The following cloudcode or webhook functions are associated with this app:
Function name: "hello", URL: "https://myherokuapp-9j6adf-8139.herokuapp.com/webhooks/function_hello"
```

You can add release notes to the deploy with the `-d` or `--description` option

Note that deploying to Heroku is equivalent to pushing to the Heroku git repository for the linked Heroku app.
If the contents of the code have changed significantly, git can reject such changes.
You can override this behavior with the `-f` or `--force` flag. Providing this flag forces a deploy in any case.

```bash
$ parse deploy
Successfully set the following vars to heroku:
PARSE_MASTER_KEY ************************************RZ3Z
PARSE_WEBHOOK_KEY ************************************i8Ja
HOOKS_URL https://myherokuapp-9j6adf-8139.herokuapp.com/
PARSE_APP_ID ************************************itMu
To https://:${HEROKU_OAUTH_ACCESS_TOKEN}@git.heroku.com/myherokuapp-9j6adf-8139.git
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to 'https://:${HEROKU_OAUTH_ACCESS_TOKEN}@git.heroku.com/myherokuapp-9j6adf-8139.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
Unable to push to git remote: git.heroku.com/myherokuapp-9j6adf-8139.git
```

When embedding `parse deploy` within other scripts (such as in an automated testing/deploy environment) you can rely on the exit code from the Parse command line tool to indicate whether the command succeded.
It will have an exit code of 0 on success and a non-zero exit code when the deploy failed.

### A Simple Function

Following ancient tradition, let's see how to run the simplest possible function in the cloud. If you take a look at `cloud/main.js`, you'll see an example function that just returns a string:

```js
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
```objc
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
```csharp
// .NET SDK
ParseCloud.CallFunctionAsync<IDictionary<string, object>>("hello", new Dictionary<string, object>()).ContinueWith(t => {
  var result = t.Result;
// result is "Hello world!"
});
```
```js
// JavaScript SDK
Parse.Cloud.run('hello', {}, {
  success: function(result) {
    // result is 'Hello world!'
  },
  error: function(error) {
  }
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

Congratulations! You've successfully deployed and run your server code, hosted on Heroku.

This is a good time to play around with the deployment cycle.
Try changing `"Hello world!"` to a different string, then deploy and run the function again to get a different result.
You can use the [Parse node](https://www.npmjs.com/package/parse) npm module and the [middleware](http://blog.parse.com/learn/using-node-js-with-parse/)
to write the server side logic for your Parse app.

## Local Development
Using popular server side programming environments like node.js, ruby on rails, django and others, makes it easier to test your changes locally,
since you can run the full server side environment on your local machine.

You can easily test local changes using [ngrok](https://ngrok.com/) and the [Hooks API](https://parse.com/docs/rest/guide#hooks).
[Ngrok](https://ngrok.com/) allows you to expose a service running on your local machine, through a public URL protected by HTTPS.
If you run an HTTP server on your local machine listening on port 5000, you can then run ngrok http 5000 and configure your webhooks using the provided URL.
It is also possible to debug your server code while it runs.

Once you’ve got ngrok running, you can now configure your webhooks with Parse.
This can be done from the dashboard, or through the Hooks API.

Further, you can use the `parse configure hooks` command to configure webhooks for your Parse app very easily.
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

NOTE: For node.js, when using [Parse middleware](https://github.com/ParsePlatform/CloudCode-Express), you can just run `node scripts/record-webhooks.js`
to generate the webhooks json configuration file.

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


## Adding a New Target

You can add a new Parse app (with server code running on Heroku) as a target by running the `add` command.

If the Parse app you want to add to the config is not yet linked to a Heroku app, then we'll create a new Heroku app for you

```bash
$ parse add
1: MyApp
2: MyOtherApp
Select an App to add to config: 2
Let's create a new Heroku app in which server code will be run.
The Heroku app will be named: "myotherapp-uygdrx-2101"
Note that this can be changed later using Heroku API or Dashboard.

Written config for "MyOtherApp"
```

If the Parse app you want to add is already linked to a Heroku app, we'll just add it to config

```bash
$ parse add
1: MyApp
2: MyOtherApp
Select an App to add to config: 2
Written config for "MyOtherApp"
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
Rollback to v1
Current version: 2
```

This rolls back to the previous version of the code.
You can also specify the release name to roll back to by using the `-r` or `--release` option.

## Reading the Logs

Every deploy, rollback, and activation of server code is logged.
You can retrieve the end of logs using the `parse logs` command.

*   -t, --tail=false: Emulates tail -f and streams new messages from the server
*   -n, --num=50: The number of the messages to display

```bash
$ parse logs
2015-10-19T06:02:36.359126+00:00 heroku[api]: Enable Logplex by parser@email.com
2015-10-19T06:02:36.359180+00:00 heroku[api]: Release v2 created by parser@email.com
2015-10-19T06:13:03.671645+00:00 heroku[api]: Set HOOKS_URL, PARSE_APP_ID, PARSE_MASTER_KEY, PARSE_WEBHOOK_KEY config vars by 11211@oauth.heroku.com
2015-10-19T06:13:03.671645+00:00 heroku[api]: Release v3 created by 11211@oauth.heroku.com
2015-10-19T06:13:26.898011+00:00 heroku[api]: Scale to web=1 by 11211@oauth.heroku.com
2015-10-19T06:13:26.963451+00:00 heroku[api]: Deploy 7ceb235 by 11211@oauth.heroku.com
2015-10-19T06:13:26.963451+00:00 heroku[api]: Release v4 created by 11211@oauth.heroku.com
2015-10-19T06:13:27.037831+00:00 heroku[slug-compiler]: Slug compilation started
2015-10-19T06:13:27.037840+00:00 heroku[slug-compiler]: Slug compilation finished
2015-10-19T06:13:28.543433+00:00 heroku[web.1]: Starting process with command `node server.js`
2015-10-19T06:13:31.007200+00:00 app[web.1]: Cloud Code Webhooks server running on port 38751.
2015-10-19T06:13:32.155941+00:00 heroku[web.1]: State changed from starting to up
2015-10-19T06:15:37.176606+00:00 heroku[api]: Rollback to v3 by 11211@oauth.heroku.com
2015-10-19T06:15:37.176666+00:00 heroku[api]: Release v5 created by 11211@oauth.heroku.com
```

## Listing Releases

You can list the known set of releases on the linked Heroku app with the `releases` command.

```bash
$ parse releases
Version             Update At                      Description
4                   2015-10-19 06:13:26 +0000 UTC  Deployed by: "11211@oauth.heroku.com". Deploy 7ceb235

3                   2015-10-19 06:13:03 +0000 UTC  Deployed by: "11211@oauth.heroku.com". Set HOOKS_URL, PARSE_APP_ID, PARSE_MASTER_KEY, PARSE_WEBHOOK_KEY config vars

2                   2015-10-19 06:02:36 +0000 UTC  Deployed by: "parser@gmail.com". Enable Logplex

1                   2015-10-19 06:02:35 +0000 UTC  Deployed by: "parser@email.com". Initial release
...
```
