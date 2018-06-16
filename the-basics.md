---
layout: page
title: The Basics
published: true
order: 1
---

<div class="col s12 no-padding">
  <ul class="tabs tabs-fixed-width">
    <li class="tab col s4"><a href="#overview"><i class="material-icons">layers</i> Platform Overview</a></li>
    <li class="tab col s4"><a href="#concepts"><i class="material-icons">lightbulb_outline</i> Concepts</a></li>
    <li class="tab col s4"><a href="#faq"><i class="material-icons">question_answer</i> FAQ</a></li>
  </ul>
</div>
<div class="container">
  <div id="overview" class="col s12">
<div markdown="1">

### Platform Overview
__drush.io__ is a continuous delivery platform that lets you encapsulate Pantheon workflow operations into self-service jobs. You can run these jobs at the push of a button or an HTTP request.

Use drush.io to increase collaboration and transparency in your DevOps practices.

There are three basic components to the drush.io platform:
- __The user interface__: for writing, collaborating on, and executing jobs,
- __The runtime matrix__: which manages job queueing and execution,
- __The API__: which you can use to run jobs from your own systems.

drush.io is complementary to cloud continuous integration services like Circle CI or Travis CI. Use those tools to automate your site build and test process. Use drush.io as the glue for all of the continuous delivery and operations processes around the build.

</div>
  </div>
  <div id="concepts" class="col s12">
<div markdown="1">

### Concepts

__Accounts__: When you sign up for drush.io, you're signing up for an _account_ representing you as an individual. You and only you have access to this account.

__Credentials__: In addition to basic information about who you are, your account also has a _credential_ tied to it; a credential consists of a Pantheon machine token (that you must provide) and an SSH key pair (that we generate for you). These values are encrypted on the platform.

__Projects__: Your account can be a member of one or more _projects_. Each project consists of a unique library of jobs and collaborators. Think of projects as a way to group jobs around a business or organization.

__Jobs__: On drush.io, a _job_ is a logical operation that may consist of one or more CLI commands, similar to a shell script. Jobs live under projects and can be edited or executed by any project member.

When you run a job, the job uses _your_ account credential to authenticate with Pantheon. This ensures audit-ability and keeps access control centralized on Pantheon.

__Job Runs__: When you execute a job, you're creating a _job run_, which can be in one of several states:
- `queued`: indicating the job execution request has been received, but has not been assigned to a runtime environment,
- `running`: indicating the job is in the process of executing,
- `complete`: indicating, the job run finished successfully and without error,
- `error`: indicating the job run finished, but one or more underlying commands resulted in an error.

Once a job run has finished, you can view its log output and the duration of the run.

__Teammates__: Any number of accounts can be linked to a project. All project members can write and execute jobs. Administrators can additionally invite/remove teammates from a project and connect the project with a Slack workspace.

__Integrations__: You can generate an _API token_ to run jobs via the drush.io API. Like credentials, API tokens are tied to _your_ individual account, not a project. You can generate as many API tokens for your account as you wish. You may also revoke API tokens at any time.

</div>
  </div>
  <div id="faq" class="col s12">
<div markdown="1">

### Frequently Asked Questions

#### __Is there a limit to the number of jobs I can run concurrently?__
Yes. Jobs are dynamically allocated to run against a distributed (but finite) matrix of runtimes. During the beta, each project is allocated 2 concurrent job runs; any additional run requests will be queued and run as soon as an existing run completes.

If you need more concurrent runs, contact us!

#### __Why are you called drush.io?__
This service was originally envisaged as a way to run Drupal's drush command line interface from anywhere. Although you can still run remote drush commands on drush.io, we've evolved the platform to serve even more DevOps needs.

We still like the name though, so we coined a backronym to describe what we do now: <b>d</b>istributed <b>r</b>untime <b>u</b>tility <b>sh</b>ell.

</div>
  </div>
</div>
