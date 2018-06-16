---
layout: page
title: API
published: true
order: 5
---

<div class="col s12 no-padding">
  <ul class="tabs tabs-fixed-width">
    <li class="tab col s3"><a href="#intro"><i class="material-icons">open_in_browser</i> Intro</a></li>
    <li class="tab col s3"><a href="#authentication"><i class="material-icons">extension</i> API tokens</a></li>
    <li class="tab col s3"><a href="#running-jobs"><i class="material-icons">send</i> Running jobs</a></li>
    <li class="tab col s3"><a href="#job-status"><i class="material-icons">playlist_add_check</i> Job status</a></li>
    <li class="tab col s3"><a href="#libraries"><i class="material-icons">library_books</i> Libraries</a></li>
  </ul>
</div>
<div class="container">
  <div id="intro" class="col s12">
<div markdown="1">

### Introduction

Centralizing and collaborating on ops jobs is powerful, but you unleash the full potential of drush.io when you embed it into your day-to-day working environments and processes.

Use the drush.io API to run jobs in the right places at the right time:

- Integrate with your chatops platform and deploy from your team's channel,
- Build a friendly UI for site editors to trigger platform operations,
- Augment your ticket tracking, VCS, IDE, or code review tools with platform integrations,
- Trigger terminus commands from quicksilver scripts

All via a simple, RESTful JSON API! Start by [diving into authentication](#authentication).

</div>
  </div>
  <div id="authentication" class="col s12">
<div markdown="1">

### Authentication

All API requests on drush.io are authenticated using JSON web tokens (JWT) scoped to your account. In order to run any commands, you must generate a token using the drush.io UI.

1. First, log in to drush.io. By default, you'll be taken to the `Projects` tab on your account dashboard.

2. Navigate to the `Integrations` tab and click the `Generate Token` button.

3. A dialogue will appear where you must provide a name/description for your token. We recommend you provide some detail about the context in which you'll use the token, e.g. `chatbot integration`.

4. Click the `Generate` button. Another dialogue will appear, showing your JWT. This is the only time you'll ever see this token, so note it and keep it safe.

Note that this token grants the same access to the platform as your account and can be used to run jobs for any project you may be a member of. This token is valid until you revoke it.

### Authenticating Requests

The JWT should be provided in the `Authorization` header of every request you make to the platform. Use the `Bearer` authorization type, like this:

```http
POST /v1/projects/my-project/jobs/my-job/runs HTTP/1.1
Host: api.drush.io
Accept: application/json
Content-Type: application/json
Authorization: Bearer {api.token}
```

Where _{api.token}_ is the JWT token you generated, without any quotes or brackets.

Note that all requests to the drush.io API must be made over https. To keep your account safe, be sure to revoke any API tokens that you are no longer using.

Now that you know how to authenticate requests, [learn how to run a job](#running-jobs)!

</div>
  </div>
  <div id="running-jobs" class="col s12">
<div markdown="1">

### Running Jobs

Triggering a job run is as simple as a single HTTP POST request! All you need to know are identifiers for the project and job and you're ready to go.

All API requests should be made against the base URL `https://api.drush.io/v1` and should accept `application/json` responses.

Jobs, which are scoped to projects, live at a path like the following:

```
/projects/{project-ish}/jobs/{job-ish}
```

- __{project-ish}__ can either be the project machine name (which you supplied when creating the project and is visible on the `Projects` tab on your account dashboard) or the project ID (part of the URL of your project dashboard).
- __{job-ish}__ likewise can either be the job machine name (which is generated when you save the job) or the job ID.

We recommend you use machine names where possible to make your API calls easier to understand at a glance. Keep in mind that project and job machine names are editable, while IDs can't be changed.

#### __Simple job triggers__

Think of triggering a job as the act of creating a job run. In HTTP terms, this would be a `POST` request to the `/runs` sub-resource, like this:

```sh
curl --request POST \
  --url https://api.drush.io/v1/projects/{project-ish}/jobs/{job-ish}/runs \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer {api.token}' \
  --header 'Content-type: application/json'
```

The API will respond with a JSON payload representing a single job run, containing at least the following:

```json
{
  "id": "3abed1d7-68ec-4151-ae87-edcc918315b4",
  "status": "queued",
  "duration": 0,
  "created": 1498714037,
  "changed": 1498714037
}
```

#### __Passing variables as context__

As highlighted in the [variables section](/jobs-and-the-runtime#variables) of the Jobs and the Runtime documentation, variables can be used to make a single job useful in multiple scenarios.

To run a job using different variable values, pass in the variable values in the POST request body as JSON, like this:

```sh
curl --request POST \
  --url https://api.drush.io/v1/projects/{project-ish}/jobs/{job-ish}/runs \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer {api.token}' \
  --header 'Content-type: application/json' \
  --data '{"env": {"VAR_NAME": "var-value"}}'
```

The structure of the request body is a simple object whose keys are variable names and values are variable values, which is itself nested under an `env` key. Or, in formatted JSON, something like this:

```json
{
  "env": {
    "VAR1": "value1",
    "VAR2": "value two"
  }
}
```

Creating a job run takes almost no time because it simply _queues_ the job to be run. The actual execution of the job happens asynchronously. If you need to react based on job run status, you must [poll for details on the run](#job-status).

</div>
  </div>
  <div id="job-status" class="col s12">
<div markdown="1">

### Job Status

Reading a job run is a simple `GET` request.  Recall that the response payload on job run creation resembles the following:

```json
{
  "id": "3abed1d7-68ec-4151-ae87-edcc918315b4",
  "status": "queued",
  "duration": 0,
  "created": 1498714037,
  "changed": 1498714037
}
``` 

A job run can be read and re-read at a path like the following:

```
/projects/{project-ish}/jobs/{job-ish}/runs/{run-id}
```

Where _{run-id}_ is the `id` property returned in the response during job run creation. A CURL command to read a job run would look like the following:

```sh
curl --request GET \
  --url https://api.drush.io/v1/projects/{project-ish}/jobs/{job-ish}/runs/3abed1d7-68ec-4151-ae87-edcc918315b4 \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer {api.token}' \
  --header 'Content-type: application/json'
```

As a job's status moves from `queued` to `running` to `complete` or `error`, its properties will reflect those changes: `duration`, the number of seconds a job takes to execute; `changed`, the last time the status was updated.

Once a job has completed execution, if the job generated log output, it will be returned on the `log` property, like this:

```json
{
  "id": "3abed1d7-68ec-4151-ae87-edcc918315b4",
  "status": "complete",
  "duration": "26",
  "created": "1498714037",
  "changed": "1498714068",
  "log": " [notice] Command: my-corp.test -- drush cc all [Exit: 0]\n\n"
}
```

Use strategies like exponential back-off to efficiently poll on the job run endpoint until it is in either a `complete` or `error` state.

Don't want to code out the whole solution yourself? Build on top of existing [drush.io API clients](#libraries).

</div>
  </div>
  <div id="libraries" class="col s12">
<div markdown="1">

### Libraries

Want to get off the ground quickly? Use one of the community supported API clients:

- [Official node.js drush.io API client](https://www.npmjs.com/package/@drush-io/api-client)
- [Official drush.io Hubot script](https://www.npmjs.com/package/@drush-io/hubot-drush-io)

</div>
  </div>
</div>
