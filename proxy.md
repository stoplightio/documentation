# Introduction

The Prism API Proxy is a performant, dependency free proxy server, with many conveniences built in.

Amongst other things, it can:
- Act as a mock server, routing incoming requests to example responses.
- Act as a transformation layer, manipulating incoming requests and outgoing responses.
- Act as a validation layer, validating incoming requests and outgoing responses.
- Log all or a subset of traffic to configurable locations.
- Extend existing APIs with new endpoints or capabilities.

## Dead Simple

Run it anywhere. It runs on OS X, Windows, and Linux, with no external dependencies. It is a single, self contained
binary file, that you can easily run from your terminal with a single command.

::: meta
  ### We are here to help!

  - Email: support@stoplight.io
  - Twitter: [@stoplightio](https://twitter.com/stoplightio)
  - [Slack](https://slack.stoplight.io)
  - Chat: Click the chat button in the bottom right of the designer.

  ### Found an issue or typo in these docs?

  Help us out and submit a pull request in our docs [git repository](https://github.com/stoplightio/documentation).
:::

# Instances

The easiest way to get started with Prism is via the [StopLight API Designer](/docs/designer).
The API Designer is tightly integrated with both the hosted version of Prism, and the standalone,
local version of Prism.

When you send HTTP requests through the API designer, it sends the requests through a Prism
instance (hosted or local). The API Designer also includes visual tooling and code editors
to help you write your transformation scripts ([middleware](/docs/designer/environments-before-script)),
and tools to define [mock endpoints](/docs/designer/mocking).

When you're done defining your endpoints, transformation scripts, etc, the API
Designer can generate the corresponding .json definition files that
Prism needs to run your custom request transformations, mocking, and more.

## Hosted

StopLight runs a hosted instance of Prism at prism-proxy.io. Every environment created in the StopLight API
Designer gets a clean instance, available at https://{environmentId}.prism-proxy.io. The hosted Prism instance
will proxy requests, transform, mock, and do anything else you've set it up to do with the API Designer.

You can find the URL to an environment's hosted Prism instance in the environment editor's proxy settings tab, or in the
proxy bar at the bottom of the environment and request editors.

## Standalone

You can find the standalone Prism binary for OS X at [https://download.prism-proxy.io](https://download.prism-proxy.io).
Unzip the downloaded file somewhere on your computer. Navigate to that folder in your terminal, and run
`./prism -h` for more information.

Prism takes one argument, `--config`. The value of this parameter should be the absolute path to the folder on your
computer that contains the Prism .json definition files. It can also be a ".", denoting the current folder.

`./prism --config .` or `./prism --config /path/to/my/config`

_Windows and Linux Prism builds are coming soon._

# Configuration

Prism is configured via a set of .json definition files. The only required file is the config.json file, which is
structured like this:

```json
{
  "port": "4010",
  "forwardHost": "http://myapi.com",
  "basePath": "/v2",
  "debug": false,
  "mock": false,
  "log": true,
  "logLocation": "https://api.stoplight.io/v1/entries",
  "startupMessage": "Hi, this is my sweet proxy.",
  "sslCert": null,
  "sslKey": null
}
```

- __port:__ The port on this computer that the proxy will run on.
- __forwardHost:__ The proxy will forward all incoming requests to this host.
- __basePath:__ If the API is namespaced under a certain path, include that path here, with a leading slash.
- __debug:__ When true, the proxy will output extra debug information. This introduces a performance penalty, do not use
in production.
- __mock:__ When true, the proxy will mock every request if possible (if endpoint matched).
- __log:__ When true, the proxy will log all traffic to the location set in the logLocation property.
- __logLocation:__ The absolute URL the proxy will log request/response data to.
- __startupMessage:__ This message will be shown when the proxy is started.
- __sslCert:__ The text content from your .csr file. The proxy will use this cert instead of it's default cert, when decrypting and encrypting https traffic.
- __sslKey:__ The text content from your .key file. The proxy will use this cert instead of it's default cert, when decrypting and encrypting https traffic.

In addition to the config.json file, Prism can be further customized with a set of .json definition files, which include:

- __endpoints.json__: Endpoint definitions, used to define mock examples, before/after scripts.
- __environment.json__: Scripts that will apply to all traffic flowing through the proxy.
- __schemadefinitions.json__: JSON Schemas, referenced in your endpoint definitions.
- __utilityfunctions.json__: Re-usable functions, accessible in your scripts under the SL.utilities namespace.
- __variables.json__: The default SL.variables, made available to your scripts.

_The format of these definition files is not final. We recommend that you generate and manage these scripts
with the StopLight API Designer, not by hand._

## SSL

Prism uses an embedded, self-signed SSL certificate by default. It uses this certificate to decrypt, and
then re-encrypt, https traffic flowing through it.

Assuming the proxy is running locally on port 4010, you can obtain a copy of this certificate by visiting:

`http://localhost:4010/stoplight.cer`

You can then install and trust this certificate on your computer, in order to proxy traffic transparently
through the proxy, amongst other things.

To configure Prism to use another SSL certificate, see the sslCert and sslKey config options described above.

# Scripts

Prism scripts are written in Javascript (ecmascript 5), and there are two important properties to make note
of when writing and organizing your scripts.

Where in the request lifecycle is the script going to be run? Scripts can be setup to run _before_ the request
reaches your end api server, or _after_.

In what context is the script being run? Scripts can be setup to run on every request that passes through
the _environment_, or only for particular _endpoints_.

We recommend that you write these scripts using the StopLight API Designer. It can produce the .json definition
files that Prism needs to run your custom proxy. Those definition include these before and after scripts.

## Before

Before scripts are run on incoming traffic - before it reaches the end api server (as indicated by the
forwardHost config property). These scripts have access to the ctx object, and the request object. Their function
signature looks like this:

```javascript
function(ctx, request) {
  // your code here
}
```

In before scripts, you can modify the request body, host, headers, url, path, query string, etc. You can also
hijack the request completely, preventing it from being forwarded to the end API altogether.

## After

After scripts are run on traffic on the way back from the end API - before it returns to the consumer.
These scripts have access to the ctx object, the request object, and the response object. Their function
signature looks like this:

```javascript
function(ctx, request, response) {
  // your code here
}
```

In after scripts, you can modify the response body, host, headers, url, path, query string, etc. You also have access
to the request, but modifying it will not do much, since the request has already been proxied to the end api, and is
on it's way back to the consumer.

In addition to response transformations, you can completely overwrite the response, programatically control wether
the current request is logged or valid, and more.

## Environment

Before and after scripts can be run in the context of an environment.
This is where you can put global handling that should apply to all HTTP traffic.
Environment scripts are run on every single request that is processed by Prism.

## Endpoint

Before and after scripts can be run in the context of an endpoint.
This is where you can put scripting logic that is specific to a particular endpoint definition.
Endpoint scripts are only run on requests that match the endpoint definition.

When a request is received by Prism, it attempts to match that incoming request to an
endpoint definition. It does this by searching the endpoint definitions for an endpoint with the request's HTTP method
and path. You can think of this like an internal router, used to match incoming requests with a defined endpoint.
If Prism finds an endpoint match, it will run the endpoint's before and after script, passing in the request's data.

# Javascript API

Prism scripts include several objects, properties, and helper methods, to aid you.

## SL

### SL.sleep(duration:int)
```javascript
// Pause execution for given number of milliseconds.
SL.sleep(1000)
```

### SL.rand(min:int, max:int) int
```javascript
// Get a random int between min and max.
var rand = SL.rand(0, 100);
```

### SL.runEndpoint()
```javascript
// If there is an endpoint match for the current request, then run the matched endpoint's before or after script. Should be called in the environments before and after scripts.
SL.runEndpoint();
```

### SL.variables json [object](/docs/designer/sl-variables)
```javascript
// Get and set back to your variables, by accessing it as a regular js object.
SL.variables.APIKey = "123";
```

### SL.sendRequest(method:string, url:string, contentType:string, body:object|string|null, options:object)
```javascript
// Send an async http request.
// All parameters are required.
// If the url is relative, the API Host set for the current environment will be used.
SL.sendRequest("GET", "http://todos.stoplight.io/todos", "application/json", null, {
  headers: {
    foo: "bar"
  },
  basicAuth: {
    username: "marc",
    password: "123"
  }
});
```

## ctx

The ctx object stores values shared during a single request's lifetime. For example, if you set a value on the ctx
object in a before script, that value will still be available in the after script for a given request.

### ctx.matched.get() bool
```javascript
// Returns true if this request matches an endpoint defined in the current environment.
var isMatched = ctx.matched.get();
```

### ctx.log.get() bool
```javascript
// Returns true if this request will be logged, else false.
var isLogging = ctx.log.get();
```

### ctx.log.set(log:bool)
```javascript
// Toggles logging on or off for this request.
// Logging is on by default.
ctx.log.set(true);
```

### ctx.mock.get() {enabled:bool, statusCode:int}
```javascript
// Returns an object with data about the mock settings for the current request.
var mockSettings = ctx.mock.get();
```

### ctx.mock.set(enabled:bool, statusCode:int)
```javascript
// Set wether this request should be mocked, and if so, which status code.
ctx.mock.set(true, 200);
```

## request

The request object represents the incoming HTTP request.

### request.validate() [{type:string, location:string, field:string, description:string, context:string}]
```javascript
// For this request, if there is a matched endpoint, then run validation.
// Returns an array of error messages. If the request is valid, this array will be empty.
// Note: If request.validate() is not called manually, Prism will automatically run validation on the request.
var messages = request.validate();
```

### request.valid.get() bool
```javascript
// Returns true if a request is valid.
var isValid = request.valid.get();
```

### request.valid.set(valid:bool)
```javascript
// Set whether this request is valid.
// Use this when performing your own custom validations, outside of the standard request.validate() process.
request.valid.set(true);
```

### request.logInfo(object|string)
```javascript
// Attach a info message to this request. The message is displayed in the API Designer request pane.
request.logInfo("Make America's APIs Great Again!");
request.logInfo({foo: "bar"});
```

### request.logError(object|string)
```javascript
// Attach a error message to this request. The message is displayed in the API Designer request pane.
request.logError("Donald Trump!");
request.logError({foo: "bar"});
```

### request.hijack(statusCode:int, contentType:string, body:string|object|null) [response](/docs/proxy/javascript-api-response)
```javascript
// For this request, do not forward it to it's final destination.
// Instead, create and return a response object immediately.
var response = request.hijack(200, "application/json", {foo: "bar"});
```

### request.setBasicAuth(username:string, password:string)
```javascript
// Set the request's Authorization header using HTTP Basic Authentication with the provided username and password.
request.setBasicAuth("bart", "homer");
```

### request.method.get() string
```javascript
// Returns this request's HTTP method.
var method = request.method.get();
```

### request.method.set(method:string)
```javascript
// Set this request's HTTP method.
request.method.set("GET");
```

### request.url.host.get() string
```javascript
// Return this request's url host.
var host = request.url.host.get();
```

### request.url.host.set(host:string)
```javascript
// Set this request's url host.
request.url.host.set("localhost:4010");
```

### request.url.scheme.get() string
```javascript
// Returns this request's url scheme.
var scheme = request.url.scheme.get();
```

### request.url.scheme.set(scheme:string)
```javascript
// Set this request's url scheme.
request.url.scheme.set("https");
```

### request.url.query.get(key:string) string
```javascript
// Returns the value associated with the given key in this request's query string.
// If there is no value associated for the given key, then an empty string is returned.
var query = request.url.query.get("limit");
```

### request.url.query.set(key:string, value:string|number|bool)
```javascript
// Sets the key, value pair in this request's query string.
// It replaces any existing values.
request.url.query.set("limit", 10);
```

### request.url.query.add(key:string, value:string|number|bool)
```javascript
// Adds the key, value pair for this request's query string.
// It appends to any existing values associated with key.
request.url.query.add("limit", 10);
```

### request.url.query.del(key:string)
```javascript
// Deletes the values associated with key for this request's query string.
request.url.query.del("limit");
```

### request.url.path.get() string
```javascript
// Return this request's url path.
var path = request.url.path.get();
```

### request.url.path.set(path:string)
```javascript
// Set this request's url path.
request.url.path.set("/api/task_lists");
```

### request.url.fragment.get() string
```javascript
// Returns this request's url fragment, without the '#'.
var fragment = request.url.fragment.get();
```

### request.url.fragment.set(fragment:string)
```javascript
// Set this request's url fragment.
request.url.fragment.set("foo");
```

### request.header.get(header:string) string
```javascript
// Returns the first value associated with given header for this request.
// If there are no values associated for given header key, then an empty string is returned.
var header = request.header.get("Authorization");
```

### request.header.set(header:string, value:string|number|bool)
```javascript
// Sets the key, value pair for this request's header.  It replaces any existing values.
request.header.set("Authorization", "Bearer 123");
```

### request.header.add(header:string, value:string|number|bool)
```javascript
// Adds the key, value pair for this request's header.  It appends to any existing values associated with key.
request.header.add("Content-Encoding", "deflate");
```

### request.header.del(header:string)
```javascript
// Deletes the values associated with the header key for this request's header.
request.header.del("Content-Length");
```

### request.body.get() object
```javascript
// Returns valid JSON object or a string for this request's body.
var body = request.body.get();
```

### request.body.set(body:object|string)
```javascript
// Sets the request's body.
request.body.set({foo: "bar"});
```

### request.contentLength.get() int
```javascript
// Returns this request's content length.
var contentLength = request.contentLength.get();
```

### request.contentLength.set(contentLength:int)
```javascript
// Sets this request's content length.
request.contentLength.set(-1);
```

## response

The response object represents an outgoing HTTP response, that was captured or created by Prism.

### response.validate() [{type:string, location:string, field:string, description:string, context:string}]
```javascript
// For this request, if there is a matched endpoint, then run validation.
// Returns an array of error messages. If the request is valid, this array will be empty.
// Note: If response.validate() is not called manually, Prism will automatically run validation on the response.
var messages = response.validate();
```

### response.valid.get() bool
```javascript
// Returns true if a response is valid.
var isValid = response.valid.get();
```

### response.valid.set(valid:bool)
```javascript
// Set whether this response is valid.
// Use this when performing your own custom validations, outside of the standard response.validate() process.
response.valid.set(true);
```

### response.logInfo(object|string)
```javascript
// Attach a info message to this response. The message is displayed in the API Designer response pane.
response.logInfo("Make America's APIs Great Again!");
response.logInfo({foo: "bar"});
```

### response.logError(object|string)
```javascript
// Attach a error message to this response. The message is displayed in the API Designer response pane.
response.logError("Donald Trump!");
response.logError({foo: "bar"});
```

### response.statusCode.get() int
```javascript
// Returns the status code for this response.
var statusCode = response.statusCode.get();
```

### response.statusCode.set(statusCode:int)
```javascript
// Sets the status code for this response.
response.statusCode.set(200);
```

### response.header.get(header:string) string
```javascript
// Returns the first value associated with given header for this response.
// If there are no values associated for given header key, then an empty string is returned.
var header = response.header.get("Content-Type");
```

### response.header.set(header:string, value:string|number|bool)
```javascript
// Sets the key, value pair for this response's header.  It replaces any existing values.
response.header.set("Content-Type", "application/json");
```

### response.header.add(header:string, value:string|number|bool)
```javascript
// Adds the key, value pair for this response's header.  It appends to any existing values associated with key.
response.header.add("Content-Encoding", "deflate");
```

### response.header.del(header:string)
```javascript
// Deletes the values associated with the header key for this response's header.
response.header.del("Content-Encoding");
```

### response.body.get() object
```javascript
// Returns a valid JSON object or string, for this response's body.
var body = response.body.get();
```

### response.body.set(body:object|string)
```javascript
// Sets the response's body.
// This overwrites the entire response body.
response.body.set({foo: "bar"});
```

### response.contentLength.get() int
```javascript
// Returns this response's content length.
var contentLength = response.contentLength.get();
```

### response.contentLength.set(contentLength:int)
```javascript
// Sets this response's content length.
response.contentLength.set(-1);
```

Below, find some examples that demonstrate some basic Prism scripting functionality. If there are further
examples that you think would be useful to put here, please let us know!

# Request Examples

## Transform Body

Transforming the request and the response body works the same way. First, you get the body. In most cases, the returned body
will either be a string, null, or an object. Make changes to the body object, and then set the entire body object back
to the request/response in order to persist the changes.

```javascript
// Here, in a before script we add a property to the JSON request body.
function(ctx, request) {
  var body = request.body.get();
  body.myNewProp = "hi there";
  request.body.set(body);
}
```

## Add Headers

In this example before script, we check to see if there is an Authorization header.
If there is no header, we add one, using a property set on SL.variables.

```javascript
function(ctx, request) {
  if (request.header.get('Authorization') === '') {
    request.header.set('Authorization', 'Bearer ' + SL.variables.apiKey);
  }
}
```

## Change Settings

In this example before script, we check the request's query string to see if we should enable mocking.

```javascript
function(ctx, request) {
  // With the code below, adding the ?mock=200 query string to a request url will cause Prism to mock the 200 response code example.
  var mock = request.url.query.get("mock")
  if (mock) {
    ctx.mock.set(true, mock)
  }
}
```

## Hijacking

In this example before script, if the request is not valid, hijack it and alert the user.

```javascript
function(ctx, request) {
  if (request.valid.get() === false) {
    var response = request.hijack(400, "application/json", "Please check current endpoint's documentation in the Stopight API Designer.");
    // do something with response.. or not.. hijack sets the response for you.
  }
}
```

## Add Basic Auth

In this example before script, if there is no authorization header, then set authorization using basic auth.

```javascript
function(ctx, request) {
  if (request.header.get('Authorization') === '') {
    request.setBasicAuth('Foo', 'Bar');
  }
}
```

## Redirections

In this example before script, we redirect the request if there is a value associated with targetPath in the query string.

```javascript
function(ctx, request) {
  var path = request.url.query.get('targetPath');
  if (path !== '') {
    request.url.path.set(path);
  }
}
```

# Response Examples

## Transform Body

Transforming the request and the response body works the same way. First, you get the body. In most cases, the returned body
will either be a string, null, or an object. Make changes to the body object, and then set the entire body object back
to the request/response in order to persist the changes.

```javascript
// Here, in an after script we add a property to the JSON response
function(ctx, request, response) {
  var body = response.body.get();
  body.myNewProp = "bye there";
  response.body.set(body);
}
```

## Set Validation

In this example after script, we run validation on the response.
If there are any errors, we will save the errors on the response object,
so that they can be viewed later in the Stoplight API Designer tool.

_Note: If you don't call response.validate() manually, then Prism will run something similar to this automatically._

```javascript
function(ctx, request, response) {
  var messages = response.validate(),
      isValid = true;

  if (messages.length) {
    isValid = false;
    for (var i in messages) {
      response.logError(messages[i]);
    }
  }

  ctx.isValid = isValid;
  response.valid.set(isValid);
}
```

# SL.variables Examples

In this example after script we check to see if there is an API Key in the response body.
If there is, then we are going to save it to SL.variables, which is available in the API Designer, and in
other before/after scripts.

```javascript
function(ctx, request, response) {
  var body = response.body.get();
  if (body.apiKey) {
    SL.variables.apiKey = body.apiKey;
  }
}
```

# Sending HTTP Requests

In this example before script, check the request's query string for a target url.
If a target url exists, then send a request for the given url.
Also use basic authentication, and set additional request headers.

```javascript
function (ctx, request) {
  var target = request.url.query.get('target');
  if (target !== '') {
    SL.sendRequest(
      'POST',
      target,
      'application/json',
      {data: 'APIs are everywhere.'},
      {
        headers: {
          bear: "foo"
        },
        basicAuth: {
          username: "foo",
          password: "bear"
        }
      }
    );
  }
}
```
