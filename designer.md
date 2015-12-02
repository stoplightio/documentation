# Introduction

Hi there, and welcome to StopLight!

Thorough, accurate definitions are the foundation of a good API. StopLight includes many utilities that make it easier to both define your API, and maintain those definitions. The way that you use StopLight will depend largely on the maturity of your API, and your goals.

For example, if you have a pre-existing API, you will likely be interested in our [API Discovery](/docs/designer/#api-discovery) features to help you quickly stub out your endpoint definitions and schemas.

For new APIs, we recommend an approach where you design the API first in StopLight, and rely on our [mocking](/docs/designer/#mocking) and [validation](/docs/designer/#validation) features as you build the backend(s) out.

## Lock In

There are a few open API specifications that have emerged over the past several years. We built our API Designer with the intention of embracing these existing standards, not creating a new one.

Currently, the StopLight API Designer supports:

- [Swagger 2](http://swagger.io/)
- [RAML 0.8](http://raml.org/)
- [JSON schema v4](http://spacetelescope.github.io/understanding-json-schema/)

We have one-click importers and exporters for Swagger and RAML, and much of our internal tooling, from validation, to definition generation, is built around a subset of JSON Schema. __When you define your API in StopLight, you are not locked in to StopLight__. Our goal is to help you build and maintain thorough, AND portable, API definitions.

::: meta
### We are here to help!

- Email: support@stoplight.io
- Twitter: [@stoplightio](https://twitter.com/stoplightio)
- [Slack](https://slack.stoplight.io)
- Chat: Click the chat button in the bottom right of the designer.

### Found an issue with these docs?

Help us out and submit a pull request in our docs [git repository](https://github.com/stoplightio/documentation).
:::

You can reach us by email at support@stoplight.io, or on Twitter [@stoplightio](https://twitter.com/stoplightio).

# Getting Started

Getting started with StopLight is easy. There are several ways to get existing APIs defined, but for the purposes
of this example, we will start with a new API that has not been created yet.

We'll be creating a an API to manage pets, similar to one of the canonical Swagger examples, the
[petstore](http://petstore.swagger.io/v2/swagger.json). You could, of course, import that Swagger file into a new
StopLight project, but we'll be creating a slightly different API by hand.

Follow along in the video, or read below.

::: meta
@[vimeo](147427619)
:::

## API Setup

First things first, we need a [workspace](/docs/designer/#workspaces).
Every new account comes with an automatic "Personal" workspace. Select that, or create a new Workspace.

Next, we will create a new [project](/docs/designer/#projects) to house our API. In StopLight, you usually use one project per API.
Give the project a name, and click the "Create" button at the top of the editor.

Finally, we need to configure an [environment](/docs/designer/#environments). You will typically have one environment
per location your API is running (development, staging, production, etc).
Sometimes it also makes sense to create an environment per team member, so that each member can manage their own
variables and proxy settings.

For now, we'll just use the default "Master" environment. This environment is created automatically for every project,
and cannot be deleted. Even though our API is not yet built, you should fill in an API host. For now, use
\`http://localhost:3000\`. Next, click on the "Proxy Settings" tab, toggle on "Global Mocking",
and then hit "Save" at the top of the editor. This will
turn on [mocking](/docs/designer/#mocking) for all of the endpoints, essentially putting online a fake version
of our API, as we define it.

We're ready to start defining our API!

## Creating an Endpoint

Let's define an endpoint that will return a pet's data.

### GET /pets/{petId}

1. Click on the endpoints tab.
2. Give the endpoint a name, "Get Pet".
4. Set the path to "/pets/{petId}". The curly brackets there denote a [path parameter](/docs/designer/#endpoints-path-parameters).
5. Click "Create" at the top of the editor.
6. Click the "Responses" endpoint editor tab.
7. Click the plus icon in the left sidebar to create a new response. The [JSON Schema Editor](/docs/designer/#json-schema-editor) will pop up. This is where you can define the shape of the pet response.
8. In the JSON Schema Editor, click the example tab, and paste in the example JSON below.
9. Click the "Generate Definition From Example" button to automatically generate a JSON Schema from the example.
10. Click "Save" at the top of the editor.

\`\`\`
{
  "id": 123,
  "name": "Cody",
  "breed": "Rhodesian Ridgeback",
  "awesome": true
}
\`\`\`

That's it, first endpoint down! Click the "Reference" endpoint editor tab to check out the auto generated
[reference](/docs/designer/#endpoints-reference).

## Sending a Request

Sending an HTTP request with StopLight is easy. With the "Get Pet" endpoint we just created selected, click the
"Send Test Request" button at the top of the editor.

This will open the entry editor. Most fields should already be filled in for you. All you need to do is fill
in the petId path parameter, and click "Send Request" at the top of the editor.

A few seconds later, you should see your mocked response in the response viewer! Prism Proxy is mocking a response
for this request because earlier we turned on global mocking in the environment settings.

You can also visit this endpoint in your web browser. At the bottom of your screen, in the Proxy Bar, there is
a "Forwarding To" link. Click that to open the Prism Proxy instance for this environment in your web browser.
Once open in your browser, add a path, like "/pets/1", to the host, and press enter to load the page. You should
see your mocked example response, and the request captured in the StopLight requests list.

## Mocking an Endpoint

As you implement your real API, you will probably want to configure StopLight so that some requests are transparently
forwarded through to your real API, and some are mocked. To do this, turn off global mocking in the environment proxy
settings, and configure mocking on a per endpoint level, via the endpoint settings.

## Inviting a Team Member

StopLight works great with teams. All updates to your definitions and settings are propogated, in realtime, to all
team members working within a workspace. Environments allow your team members to scope their own variables, settings,
and API locations.

When you're ready to invite your team to your snazzy new Petstore project, head over to the workspace
editor (that this project is associated with). Adding team members to your project requires a paid workspace, so
click on the "Billing" editor tab. Every plan comes with a 7 day, risk free trial. Simply sign up
for the Team plan, then head over to the Members tab, and input the email for one of your team members. They will
receive an email with instructions on how to claim their StopLight account, and get started in your workspace!

# Workspaces

Workspaces allow you to group together projects - like your internal and external APIs, microservices, etc - under one account. You can easily invite teammates to manage your APIs and consolidate all projects under one billing account. It all starts with a Workspace - it is the top level resource in the API Designer.

## Inviting Members

You can easily invite other team members by email to collaborate on your workspace. Updates in the API Designer propagate to all team members in real-time. If the person that you are inviting does not yet have a StopLight account, one will be created for them, and they will be emailed instructions on how to claim the account and get started.

Projects, environments, endpoints, schemas, etc, under a workspace, are shared with all workspace members.

## Member Roles

- __Guest__: Can view the workspace and workspace resources. Cannot update, create, or delete anything.
- __Member__: Guest permissions + can update, create, and delete workspace resources.
- __Admin__: Member permissions + can manage members.
- __Owner__: Admin permissions + can manage billing, and delete the workspace.

# Projects

A workspace has many projects. Each project represents one API.

## Description

The description section is a markdown enabled, free-form area, for your team to write down general notes and information about the given project.

## Security Schemes

Typically, an API has one or more ways to authenticate a request. The method could be API key authentication via headers or query string, basic authentication, OAuth, etc. The security schemes section allows you to define one or more of these authentication methods, with defaults. Later, when defining endpoints, you can [reference any security schemes](/docs/designer/#endpoints-authentication) you've defined here.

Security schemes defined on a project are compatible with the [SL.variables](/docs/designer/#environments-sl-variables) system. This means that, for example, you could set Bearer \`<<apiKey>>\` as the default value for your "Authentication" header. Then, when using various features in the designer like "Send Test Request", your authentication header will automatically be added to the request, with the correct apiKey filled in by the variables system.

# Environments

A project has many environments. Environments let you scope SL.variables, and manage the multiple locations your API might run in. For example, you might have a staging environment and a dev environment, where the staging environment API Host is https://staging.myapi.com, and the dev environment API host is http://localhost:3000. Any logged requests are also scoped by environment, and sometimes it even makes sense for each developer on your team to create their own environment.

## Proxy Settings

The proxy settings are only applicable to the app version of StopLight. These settings affect the local instance of Prism Proxy that the app can optionally run on your computer.

## SL.variables

SL.variables is a JSON object. You can use any properties defined on this object in your endpoint definitions, scripts, and the request maker. Simply surround the property name with <<brackets>>. For example, if you add a header in the request maker with the name "Authentication", and value "Bearer \`<<apiKey>>\`", \`<<apiKey>>\` will be replaced by whatever value is set at SL.variables.apiKey before sending the request.

You can assign values to SL.variables in your before/after scripts, and they will update in this section. More details in the [SL.variables docs](/docs/designer/#sl-variables).

## Import

We support single file import from Postman, StopLight, Swagger 2, and RAML 0.8.

## Export

We support exporting to the Swagger 2, RAML 0.8, and StopLight formats. This section includes some additional settings applicable to one or more of the export formats (Swagger & RAML).

- __Default Request Content-Type__: The default request content type, applicable to your all of the endpoints in your API.
- __Default Response Content-Type__: The default response content type, applicable to your all of the endpoints in your API.
- __Supported Protocols__: Which protocols do you support in your API? HTTP, HTTPS, or both.

## Before Script

Prism API Proxy allows you to define scripts that are run before requests. In the environment before script section, you can define a script that runs before EVERY request that passes through Prism for this environment. You can find more details on writing scripts for Prism in the [Prism documentation](/docs/proxy).

## After Script

Prism API Proxy allows you to define scripts that are run after requests. In the environment after section, you can define a script that runs after EVERY request that passes through Prism for this environment. You can find more details on writing scripts for Prism in the [Prism documentation](/docs/proxy).

# Endpoints

Endpoints are the basic definition unit you will use to describe your API. Each endpoint represents one HTTP Method + URL combination.

To define a endpoint:
1. Make or select a Workspace->Project->Environment.
2. Click on the endpoints tab to start creating a new endpoint.
3. Give the endpoint a name, method, and url.
  * The url should be relative, not absolute. For example, \`/posts\`, not \`http://api.example.com/posts\`. You define the API root/host in the environment's settings. This is a more flexible approach to defining the API, and makes it easy to switch out the root location of your endpoints.

## Path Parameters

Endpoint paths often include dynamic parts. To define dynamic path parameters in your endpoint path, simply wrap the parameter name in \`{brackets}\`.

For example, the paths \`/posts/1\` and \`/posts/2\` represent the same endpoint. The path for this endpoint is, in reality, \`/posts/{postId}\`.

## Reference

The reference section is a markdown enabled, free-form area, for your team to write down general notes and information about the given endpoint. You can optionally include special tags, that when parsed, will use the rest of your endpoint definition to automatically produce formatted tables and code. The available tags are:

Tag Name | Output
--- | ---
Endpoint.name | The endpoint's name.
Endpoint.methodUrl | The endpoint's method and url path.
Endpoint.authentication | A string indicating whether authentication is required or not.
Endpoint.headers | A table of defined headers with types and default values.
Endpoint.queryString | A table of defined query string params with types and default values.
Endpoint.requestBody | A table of defined request body params with types and default values.
Endpoint.responses | Individual sections for each defined response code, with examples.

## Mocking

Turn mocking on or off for this endpoint. If an incoming request matches this endpoint's HTTP method and path, it will be mocked. More detail in the [mocking](/docs/designer/#mocking) section of the documentation.

## Authentication

In most APIs, some or all endpoints require authentication. If you have defined security schemes on your project, you can toggle on/off the schemes that this endpoint supports. If your endpoint supports both authenticated and un-authenticated requests, leave the "None" option checked. Remember, you must define the Security Schemes in your project settings first!

## Before Script

Prism API Proxy allows you to define scripts that are run before requests. In the endpoint before script section, you can define a script that runs before requests that pass through Prism AND match this endpoint's Method + Path. You can find more details on writing scripts for Prism in the [Prism documentation](/docs/proxy).

## After Script

Prism API Proxy allows you to define scripts that are run after requests. In the endpoint after script section, you can define a script that runs after requests that pass through Prism AND match this endpoint's Method + Path. You can find more details on writing scripts for Prism in the [Prism documentation](/docs/proxy).

## Request Headers

Here, you can define any request headers that your endpoint explicitly supports. This section is backed by [JSON schema](http://spacetelescope.github.io/understanding-json-schema/) for validation purposes, and supports use of the SL.variables system in default values.

## Request Query String

Here, you can define any request query string parameters that your endpoint explicitly supports. This section is backed by [JSON schema](http://spacetelescope.github.io/understanding-json-schema/) for validation purposes, and supports use of the SL.variables system in default values.

## Request Body

Here, you can define the request body for your endpoint. This section is only applicable to endpoints for HTTP methods that support bodies. For example, an endpoint defined with the HTTP method "GET" will not be allowed a request body. This section is backed by [JSON schema](http://spacetelescope.github.io/understanding-json-schema/) for validation purposes, and supports use of the SL.variables system for header default values.

At the moment, the API Designer only supports one request body definition per endpoint. Please get in touch if this presents a serious limitation for your use case.

## Responses

You can define multiple responses per endpoint. Each response is identified by one or more response codes (200, 400, etc), and a response content type. This section is backed by [JSON schema](http://spacetelescope.github.io/understanding-json-schema/) for validation purposes, and supports use of the SL.variables system for header default values.

If you already have an example of a response, you can paste (or write) it in the response example tab, and then click the "Generate Definition From Example" to automatically build the full JSON schema.

When you define response example, the example can be used in the reference section. In addition, the Prism mocking feature will use your example responses to generate the mock data.

## Send Test Request

Once you have defined an endpoint, you can click the "Send Test Request" at the top of the page to quickly send an HTTP request to your endpoint. The API Designer will automatically fill out all of the fields it can, including the query string, path params, headers, and request body. If you have defaults set in your definitions, they will be used when generating your test request.

# Schemas

As your API grows, you will often find that endpoint request and response body definitions begin repeating. Rather than duplicate JSON Schemas everywhere, you can define schemas once, and then reference those schemas in your endpoint definitions. This is part of the [JSON schema](http://spacetelescope.github.io/understanding-json-schema/) v4 specification.

To define a schema, first make sure you have selected a Workspace->Project->Environment. After selecting an environment and clicking on the schemas tab, the first thing you'll want to do is give the schema a name, namespace, and optional description.

StopLight schemas are powered by [JSON schema](http://spacetelescope.github.io/understanding-json-schema/). There are three ways to build a JSON schema, each providing varying levels of control and ease of use.

## By Example

This is the quickest way to build a schema, but provides the least control. If you have an idea, or example, of what you want an instance of this schema to look like, you can click on the "Example" tab, and write/paste the example in. Once you're done, make sure the editor is not reporting any errors, and then click the "Generate Definition From Example" button. Tada, schema generated! From here, you can tweak / modify as needed.

## With The Editor

Navigate to the Definition Tab -> Editor to use the API Designer schema editor to build JSON schema's visually. This provides a nice compromise between ease of use, and control. Note that not all of JSON schema features are supported in the editor. You can always get it 90% there with the editor, and then switch to direct schema editing if needed. Click the toolbox button located on the left side of each line to open up additional type and validation options.

## Direct Schema Editing

Navigate to the Definition Tab -> [JSON schema](http://spacetelescope.github.io/understanding-json-schema/) to manipulate your JSON schemas directly via a nice code editor. This is the most complicated option, but is available if you need more control. Here, you can take advantage of all of JSON schema's features.

## Using Schemas

You use schemas by referencing them in other schemas. To do this, we use the $ref property of the JSON schema specification. $ref support is built into the API Designer schema editor. After you have defined a schema, you can reference it in any other schema (including in endpoint definition!) by selecting the $ref property type from the types section of the property details pane (accessed via the toolbox icon on the right side of every line in the editor).

# Functions

Functions allow you to re-use scripting functionality across your project. They are made available in your before/after scripts under the SL.utilities namespace.

To define an function, first make sure you have selected a Workspace->Project->Environment. After selecting an environment and clicking on the functions tab, the first thing you'll want to do is give the function a name and optional description. The name must be a valid javascript variable! Your function will be made available in scripts under the SL.utilities namespace, with the name you set here. For example, if you name your function "addValidationHeader", then you can call it in other scripts with SL.utilities.addValidationHeader.

We automatically add several useful functions to every project. Read the descriptions on each to get a feel for what they do.

Please check out the Prism API Proxy documentation for more details on the variables, features, and functions available to scripts (including functions).

# Requests

The API Designer includes a full-blown HTTP request maker, and code generator.

To send a request, first make sure you have selected a Workspace->Project->Environment. After selecting an environment and clicking on the requests tab, simply choose an HTTP method, fill in the absolute URL, and click send! After a second or two, you should see the full details of the response.

## Request Options

We support most HTTP request options via our request maker. You can add/edit headers, path parameters, query string parameters, basic authentication, and the request body.

Every input in the request options supports the SL.variables system. If you have a variable defined on your environment called apiKey, you can use it anywhere in the request maker by surrounding it in brackets, ie \`<<apiKey>>\`. Check out the code generation tab to see if the variable is replaced correctly in the resulting request. When you click send, the API Designer will replace all variables before sending the request off.

Currently, we don't support multi-part file uploads. Please get in touch if this is an important feature for your project.

## Response Tab

The response tab includes a variety of ways to view your response, as well as meta data from the Prism API Proxy.

If the response is JSON, you can use our JSON explorer to navigate large json structures.

If the response is HTML (looking at you Rails error pages), the rendered section will allow you to view the rendered HTML instead of trying to grok the plain HTML.

Along the top bar of the response you'll notice extra tags indicating whether validation passed, the response was mocked, or if the response was hijacked. These are all features of the Prism API Proxy, that we expose in the API Designer. If validation failed, there will be an additional subsection called "Messages" that details the validation errors.

## Code Generation

As you fill out the request details, the code generation section will live update. If you are using any SL.variables in your request, they will be replaced in the code generator. Keeping an eye on the generated code is a good way to make sure your SL.variables are working correctly!

## Endpoint Generation

Given a request, you can automatically build the corresponding endpoint if it does not already exist in your project. Simply send the request off to get the full response back, and then click the "Build Endpoint" button at the top of the screen. Click save on the resulting endpoint and then bask in the glory that is automatic request / response body [JSON schema](http://spacetelescope.github.io/understanding-json-schema/) and example generation.

If there is already an endpoint defined in the project with the request's HTTP method + path, the "Build Endpoint" button will instead say "View Endpoint", and take you to the existing endpoint when clicked.

## Logging

All requests sent via the API Designer are logged to the current environment, and available for viewing by other workspace members. This makes it easy to debug troublesome requests with your team.

All requests that pass through the Prism API Proxy (wether sent from the API Designer) or not, are also logged to the appropriate environment. This means that you can point your mobile app, or API consumer, at the Prism Proxy host (either our hosted proxy instance, or a local instance on your computer), and the requests will be logged to the API Designer for debugging.

# Markdown

[Markdown](http://daringfireball.net/projects/markdown/) is used in several places throughout the designer.
It's even used to [power these docs](https://github.com/stoplightio/documentation)! When working with markdown in the designer,
you have access to most of the syntax, as well as a few extra StopLight-specific directives. Here is a handy markdown syntax
[cheat sheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet).

## Sections

Sections add padding, and a top border, to mark them off from the rest of the content. You can see them in use in this documentation.
There are two ways to create sections.

H1 tags, or those created with a single \`#\` character, automatically create a new section.

The special section directive will create a section around it's contents.

    ::: section
    I am a descrete section.
    :::

## Meta

The meta directive will render the content in place on smaller screens. On larger screens, however, it will add a sidebar
like you see in this documentation, and render the content into the sidebar corresponding with the section the meta directive
is in.

    ::: meta
    I am some extra info. If the screen is large enough, I will be rendered into a sidebar next to the section I am in, otherwise I'll be rendered inline.
    :::

## Code

To include code snippets in your markdown, use three backticks, as shown below.

    \`\`\`json
    {
      "foo": "bar"
    }
    \`\`\`

## Videos

To include videos (vimeo or youtube), use the syntax below. The value in the parenthesis is the video id.

    \`\`\`
    @[vimeo](147427619)
    @[youtube](MX00rGu92gQ)
    \`\`\`

# JSON Schema Editor

The StopLight API Designer includes a best in class [JSON Schema](http://spacetelescope.github.io/understanding-json-schema/)
editor. JSON Schema is a powerful way to describe and validate JSON objects.

You can create JSON schemas with our visual GUI editor, by directly editing the schema itself, or by generating schemas
from example JSON objects. You are free to mix an match these three editing methods.

## Visual Editor

To edit a properties name, simply click the name and start typing.

To edit property details, including the type(s), description, default, and validations, click the toolbox icon in the left gutter
for the corresponding property.

## Schema Generation

To generate a schema from an existing JSON object, fist click the example tab. Type or paste your example JSON object in the
text editor, and then click the "generate definition from example" button to generate a JSON Schema from the example object.

# SL.variables

Variables make it easy to re-use, and automatically set, values in requests and scripts. There are several ways to
set and use variables. Variables are scoped to environments, which means you can manage, for example, a different apiKey
variable for each environment in your project.

## Setting Variables

### Manually

The environment editor has an SL.variables tab. SL.variables is just a JSON object, that you can manually edit
via the code editor in this tab.

### Middleware Scripts

You can save values back to the current environment's variables in before and after scripts. Simply assign a
value to the SL.variables object. For example, if you have a \`POST /login\`  endpoint
in your API that responds with an apiKey, you could setup the after script for that endpoint to
automatically save the apiKey to SL.variables when called.

\`\`\`javascript
// An example endpoint after script
function (ctx, request, response) {
  var body = response.body.get();

  // If the request was valid,
  // automatically save the latest apiKey to this environments variables.
  if (response.statusCode.get() === 201) {
    SL.variables.apiKey = body.apiKey;
  }
}
\`\`\`

With this simple script, whenever a request flows through Prism to \`POST /login\`, the latest valid apiKey will
automatically be saved to your environment, ready for use!

## Using Variables

There are four primary places to use variables - project security schemes, endpoint definitions, JSON Schemas, and scripts.
As a general rule of thumb, you can use variables throughout most of the designer by surrounding the variable name in
\`<<variableName>>\`. You can access variables in scripts through the SL.variables object, ie \`SL.variables.variableName\`.

If you use variables in the designer, when you use the send test request feature, these values will
be automatically replaced for you.

### Project Security Schemes

Often your API will have one or more authentication mechanisms. Read more about project security schemes
[here](/docs/designer/#projects-security-schemes). You can use variables in your security schemes by using the
\`<<variableName>>\` notation.

For example, you could use the apiKey -> header security scheme, and set the header name to \`Authorization\`, and the
value to \`Bearer <<apiKey>>\`. With this setup, any endpoints that use this security scheme will have this header
automatically added when you use the "send test request" button, and the apiKey variable will be replaced when you
send the request.

### Endpoint Definitions

Use the \`<<variableName>>\` anywhere in the endpoint's definition. This includes the request headers, query string,
and body.

### JSON Schemas

You can use the \`<<variableName>>\` notation as a default value for any of the fields in your JSON Schemas. To
set a default value, click the toolbox icon on the left side of any property. When you use the "send test request" feature,
any default values with variables will be replaced when you send the request.

### Middleware Scripts

Access variables in middleware before / after scripts via the \`SL.variables\` object. For example:

\`\`\`javascript
// An example before script
function (ctx, request) {
  var apiKey = SL.variables.apiKey;
  request.header.set('Authorization', 'Bearer ' + apiKey);
}
\`\`\`

## Special Variables

- __host__: The host variable will always resolve to the current environments API Host property.
- __basePath__: The basePath variable will always resolve to the current environments Base Path property.

# Mocking

You can turn on mocking dynamically in before/after scripts, or on a per endpoint basis in the endpoint mocking settings section. For information on how to do dynamic mocking, please refer to the [Prism API Proxy documentation](/docs/proxy).

When mocking is on for an endpoint, any request matched to that endpoint (whether sent via the API Designer, or sent directly to the hosted or local instance of Prism API Proxy), will be mocked. Prism will not forward the request to the environment's API host, and will instead immediately return the response example defined in the endpoint.

Check out the [getting started](/docs/designer/#getting-started) guide / video for a quick introduction to mocking.

## Mock Server

Once you have your API defined, and mocking set up, you can download the Prism API Proxy and run a mock server wherever you like. Prism is a single small, dependency free binary file, easily run from the command line. The only argument when you run Prism is the path to the .json configuration files. The API Designer can generate these files for you! Make sure you have an environment selected, and then click the "Download -> Definitions Zip" button in the bottom right of the designer. There is a readme in that zip file with simple instructions on how to run your Prism instance.

# API Discovery

The app version of the API Designer includes a powerful API discovery feature. You can set Prism Proxy up to watch HTTP traffic flowing through your
API, which it will subsequently use to automatically generate endpoint definitions for you.

API endpoint discovery is the quickest way to stub out comprehensive definitions for an existing API.

### It will:
- Identify the method and path combination that uniquely identifies the endpoint.
- Identify dynamic path parameters if possible.
- Generate query string JSON Schema.
- Generate headers JSON Schema.
- Generate request body JSON Schema.
- Generate response definitions, including the content type, JSON Schema, and example.

Read more about it in our [blog post series](https://blog.stoplight.io/api-endpoint-discovery-part-i/).

# Questions?

If anything about this documentation is confusing, or missing, please let us know at support@stoplight.io! We're constantly working to improve it.

You can also help us out by submitting a pull request in our docs [git repository](https://github.com/stoplightio/documentation).

The StopLight Team
