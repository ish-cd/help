---
layout: page
title: Getting Started
published: true
order: 2
---

<div class="col s12 no-padding">
  <ul class="tabs tabs-fixed-width">
    <li class="tab col s2"><a href="#intro"><i class="material-icons">open_in_browser</i> Intro</a></li>
    <li class="tab col s2"><a href="#setting-up-credentials"><i class="material-icons">fingerprint</i> Add a Credential</a></li>
    <li class="tab col s2"><a href="#creating-projects"><i class="material-icons">folder</i> Create a Project</a></li>
    <li class="tab col s2"><a href="#creating-jobs"><i class="material-icons">work</i> Create a job</a></li>
    <li class="tab col s2"><a href="#queueing-jobs"><i class="material-icons">queue</i> Queue a job</a></li>
    <li class="tab col s2"><a href="#inviting-teammates"><i class="material-icons">people</i> Inviting teammates</a></li>
  </ul>
</div>
<div class="container">
  <div id="intro" class="col s12">
<div markdown="1">

### Introduction

This guide is designed to get you started on drush.io. In particular, we'll cover 

- Account setup and authorization
- Project and job management,
- Job execution

All you need before you begin is a drush.io account. We're still in private beta, but you can [sign up for the beta waitlist here](https://www.drush.io/#beta).

</div>
  </div>
  <div id="setting-up-credentials" class="col s12">
<div markdown="1">

### Setting Up Credentials

In order for drush.io to execute workflow operations on your behalf, you need to provide a valid [Pantheon machine token](https://pantheon.io/docs/machine-tokens/).

1. First, log in to you drush.io account. By default, you land on the `Projects` tab of your account dashboard. Navigate to the `Credentials` tab.

2. Click the `Add Credential` button. A dialog will appear where you can enter a credential name like `Work Pantheon Account`
 
3. Click the `Create Machine Token and Paste Here` button. This will open a small window on the Pantheon machine token generation page (you may be prompted to log in to Pantheon).

4. In the Pantheon dashboard pop-up, click the `Generate Token` button. Once generated, copy the machine token to your clipboard.

5. Close the pop-up window and paste the contents of your clipboard into the `Pantheon Machine Token` field in the dialogue.

6. Click the `Add Credential` button in the dialog.

7. Once added, you'll see a confirmation dialog, which displays an SSH public key. In the background, drush.io will add the corresponding private key to your Pantheon account. This key pair is used to run `drush` and `wp-cli` commands invoked in your jobs.

8. You may optionally add this public key to other services (like Github or Bitbucket) for deeper integration into your operations stack.

With your credential set up, you're ready to [create a project](#creating-projects)!

</div>
  </div>
  <div id="creating-projects" class="col s12">
<div markdown="1">

### Creating Projects

Projects are a way to collaborate with your colleagues on a set of jobs specific to your organization.

1. First, navigate to your account dashboard. The `Projects` tab should be the default tab you land on.

2. Click the `Create Project` button. A dialog will appear where you can enter a project name. You may wish to use your organization's name for simplicity.

3. As you type your project name, you'll see a suggested machine name generated for you. This machine name is globally unique across the platform and can be used to identify your project using the drush.io API. You may modify this value if you don't like the generated value.

4. Click the `Save` button in the dialog and your project will be created.

With your project set up, you can [start writing jobs](#creating-jobs)!

</div>
  </div>
  <div id="creating-jobs" class="col s12">
<div markdown="1">

### Creating Jobs

A job is a set of instructions that encapsulate an operation you may want to run repeatedly.

As an example, imagine a `Refresh test environment` job that clones content and files from `live` to `test`, clears caches, and triggers a short-lived backup.

1. Navigate to the `Projects` tab of your account dashboard and click into the project you want this job to live under.

2. By default, you'll be taken to the `Job Runs` tab of your project dashboard. If you've never run a job before, the list of runs will be empty.

3. Navigate to the `Jobs` tab and click the `Create Job` button.

4. A dialog will appear. At the top is a job name field where you can enter a descriptive name. In the example job above, you would enter `Refresh Test Environment`.

5. Below the job name field are two sections. On the left is a text area where you will compose your job script. To the right is a preview area that displays job run progress and output when a preview run is triggered.

6. In the text area to the left, enter the commands that make up your job. For the example job above, it might look something like this:
```sh
terminus env:clone-content my-site.live test --yes
terminus env:clear-cache my-site.test
terminus backup:create my-site.test --keep-for=1
```
For details on the runtime environment and script syntax, see [Jobs and the Runtime](#).

7. Click the `Save` button in the job create dialog to complete the process.

Now that you have a job, [you're ready to run it](#queueing-jobs)!

</div>
  </div>
  <div id="queueing-jobs" class="col s12">
<div markdown="1">

### Queueing Jobs

On its own, a job is just a set of instructions. You can put these instructions in motion by creating a job run. There are several ways to queue a job for execution. We'll cover two mechanisms here.

#### __Via the job edit dialogue__
1. Navigate to the "Jobs" tab in the project dashboard where your job lives.

2. Click the job you wish to run. This will open a job edit dialogue, similar to the job create dialogue.

3. At the bottom of the dialogue, next to the `Save` button is a `Save and Preview` button. Click this to queue the job.

4. Once clicked, the preview area in the right-hand side of the dialogue will activate. The preview area will cycle through runtime acquisition and job run phases. 

5. Once the job run has completed, the log output from the job will be displayed in the preview area. If any changes to the job need to be made, you can do so in the script area to the left.

#### __Via the job action menu__
1. Navigate to the "Jobs" tab in the project dashboard where your job lives.

2. Click the action menu icon to the right of the job you wish to run. In the drop down menu, click `Queue Job`.

3. A notice will appear indicating the job was successfully queued to run.

4. Navigate to the `Job Runs` tab, where a new job run should appear in a `queued` or `running` state. Once the job has completed, you can view the job's log output by clicking the `View Log` item in the action menu to the right of the run.

You've now successfully created a job and run it on the platform! Time to [invite your teammates](#inviting-teammates).

</div>
  </div>
  <div id="inviting-teammates" class="col s12">
<div markdown="1">

### Inviting Teammates

Creating and running DevOps workflows right in the browser is just the beginning. Imagine what's possible when you invite the rest of your team!

1. Open up drush.io to the project dashboard for a project that you created or are an administrator of.

2. Navigate to the `Team` tab and click the `Invite Teammate` button.

3. Enter your colleague's e-mail address in the invitation dialogue and click `Invite`.

4. Your colleague will receive a welcome e-mail, indicating that they've been invited to your drush.io project. Let them know to open the e-mail and activate their account.

5. Until they activate their account, they'll show up in your team as as generic `drush.io user`.

6. By default, teammates are added as basic members, but you can grant teammates the administrator role using the action menu to the right of the user on the `Team` tab.

7. Be a dear and send them this documentation!

Now you're cookin' with grease! Time to [dive deeper into jobs and the runtime](/jobs-and-the-runtime).

</div>
  </div>
</div>
