---
layout: page
title: Recipes
published: true
order: 3
---

<div class="col s12 no-padding">
  <ul class="tabs tabs-fixed-width">
    <li class="tab col s3"><a href="#intro"><i class="material-icons">open_in_browser</i> Intro</a></li>
    <li class="tab col s3"><a href="#common-job-templates"><i class="material-icons">content_paste</i> Common Job Templates</a></li>
    <li class="tab col s3"><a href="#custom-code"><i class="material-icons">code</i> Custom/Complex Code</a></li>
    <li class="tab col s3"><a href="#chatops"><i class="material-icons">chat</i> ChatOps</a></li>
  </ul>
</div>
<div class="container">
  <div id="intro" class="col s12">
<div markdown="1">

### Introduction

The drush.io runtime enables you to encapsulate jobs that are highly specific to your needs and the ways your team works.

Many workflows you may wish to implement are common among Pantheon users or can be accomplished using common strategies. Use these recipes to help you get up to speed more quickly.

- [Common job templates](#common-job-templates)
- [Running custom code in the runtime](#custom-code)
- [Get started with ChatOps](#chatops)

</div>
  </div>
  <div id="common-job-templates" class="col s12">
<div markdown="1">

### Common Job Templates

Below are a few common jobs you may wish to tailor to your needs or use as inspiration.

#### __Deploy to dev through live__
One common scenario is pulling the latest master branch from a remote git repository (like GitHub, GitLab, BitBucket, etc), pushing to Pantheon, then pushing through test and live. Use a job like this to make this process as simple and repeatable as possible.

```sh
# Clone from a non-Pantheon git remote to the cwd
git clone --quiet git@github.com:YOUR-ORG/YOUR-SITE .

# Push this code to your dev env.
git remote add pantheon ssh://codeserver.dev.UUID@codeserver.dev.UUID.drush.in:2222/~/repository.git
git push pantheon master

# Deploy to test env
terminus env:deploy YOUR-SITE.test -y --note="Deployed via drush.io"

# Deploy to live env
terminus env:deploy YOUR-SITE.live -y --note="Deployed via drush.io"
```

#### __Refresh test environment__
Keeping pre-production environments in a state closely matching production can be a chore. Use a job like this to help automate the process.

```sh
# Clone from live to test
terminus env:clone-content YOUR-SITE.live test -y

# Clean out unneeded data on the test site
`terminus connection:info YOUR-SITE.test --field=mysql_command` \
  -e "TRUNCATE watchdog;"

# Set up test user accounts if necessary
terminus remote:drush YOUR-SITE.test -- \
  user-add-role "administrator" --mail="user@example.com"
```

You may even wish to go a step further and run this job on a nightly basis using a cron service so that your test environment automatically reflects production each morning.

#### __Install and run a task runner__
Developers commonly use a task runners like `grunt` or `gulp` to compile and optimize front-end code. You can write a job on drush.io to clone from your non-Pantheon git repository, install and run your task runner and tasks, force commit the results, and push the built assets to Pantheon. For example:

```sh
echo "Cloning site code from GitHub"
git clone --quiet git@github.com:YOUR-ORG/YOUR-SITE .

echo "Installing gulp and other node modules required in package.json"
npm install -g gulp-cli
npm install

echo "Running gulp build task as defined in gulpfile.js"
gulp build

echo  "Force adding built files and vendor code that is normally gitignore'd"
git add --force .
git commit -a -m "Adding built assets."

echo "Force pushing compiled assets to Pantheon master"
git remote add pantheon ssh://codeserver.dev.UUID@codeserver.dev.UUID.drush.in:2222/~/repository.git
git push pantheon master --force
```

#### __Maintain a leaner codebase__
Another common theme in the PHP world is to build projects using composable dependencies using Composer and Packagist. You can use the same concept as the front-end code above to achieve similar outcomes with back-end vendor code.

```sh
echo "Cloning site code from GitHub"
git clone --quiet git@github.com:YOUR-ORG/YOUR-SITE .

echo "Installing vendor requirements"
composer install --ignore-platform-reqs

echo  "Force adding vendor code"
git add --force .
git commit -a -m "Adding vendor dependencies."

echo "Force pushing composed code to Pantheon master"
git remote add pantheon ssh://codeserver.dev.UUID@codeserver.dev.UUID.drush.in:2222/~/repository.git
git push pantheon master --force
```

Mix and match the above to suit your needs!

</div>
  </div>
  <div id="custom-code" class="col s12">
<div markdown="1">

### Custom and Complex Code

For jobs that require advanced conditional logic, parsing of API responses, or other complex customizations, we recommend [writing a Terminus plugin](https://pantheon.io/docs/terminus/plugins/create/) or using [an existing plugin](https://pantheon.io/docs/terminus/plugins/directory/).

You can use composer to install Terminus plugins from packagist.

```sh
composer create-project -d ~/.terminus/plugins terminus-plugin-project/terminus-site-status-plugin:~1
```

Once installed, you can use the Terminus plugin as intended:

```sh
terminus site:status
```

For additional resources, check...
- The [Pantheon docs on writing Terminus plugins](https://pantheon.io/docs/terminus/plugins/create/)
- A short list of [existing Terminus plugins](https://pantheon.io/docs/terminus/plugins/directory/)
- A [community repository of Terminus plugins](https://github.com/terminus-plugin-project)

</div>
  </div>
  <div id="chatops" class="col s12">
<div markdown="1">

### ChatOps

ChatOps is [a collaboration model](https://www.atlassian.com/blog/software-teams/what-is-chatops-adoption-guide) that puts people, process, tools, and automation into one place. Implemented well, it allows dev teams to work, collaborate, and learn together in an efficient, transparent way.

One common tool used to implement ChatOps at organizations is [Hubot](https://hubot.github.com/), an open source chat bot platform. Use [adapters](https://hubot.github.com/docs/adapters/) to integrate Hubot with your chat service (Slack, HipChat, etc). Install or write [scripts](https://hubot.github.com/docs/scripting/) to add custom functionality to your chatbot.

If you don't use Hubot already, check out these guides on getting started with your first chat bot:

- [Official Hubot documentation](https://hubot.github.com/docs/)
- [Set up your first Hubot](https://lornajane.net/posts/2015/set-up-your-first-hubot) by LornaJane

#### __drush.io hubot script__
You can install the [official drush.io hubot script](https://www.npmjs.com/package/@drush-io/hubot-drush-io) to quickly integrate drush.io with your chat bot. Once installed, you can run commands like

- `hubot drush.io token set [API TOKEN]` in private chat with your bot to authenticate with drush.io
- `hubot drush.io run [PROJECT] job [JOB]` to trigger a specific job on drush.io

#### __Custom drush.io hubot integration__
To get the most out of drush.io/hubot integration, you may wish to write your own library of hubot commands, built on top of the hubot drush.io script.

Here's an example custom hubot script that adds a "delete multidev" command that triggers a job called `delete-multidev` on the `MY-CORP` project, passing two custom variables (Pantheon site and Pantheon environment name) parsed from the command.

```coffeescript
# Description:
#   MY CORP's custom drush.io hubot integration
#
# Dependencies:
#   "@drush-io/hubot-drush-io": "~1.0.0"
#
# Commands:
#   hubot delete <site> multidev <multidev>

module.exports = (robot) ->
  drushIoProject = 'MY-CORP'

  # Hubot, delete (site) multidev (env)
  robot.hear /delete ([a-z0-9\-]{2,48}) multidev ([a-z0-9\-]{2,12})/i, (msg) ->
    jobPayload =
      TSITE: msg.match[1]
      TENV: msg.match[2]

    msg.send "Beginning multidev delete."
    robot.drush.io.run(msg, drushIoProject, 'delete-multidev', jobPayload).then (run) ->
      if (run.data.status == 'complete')
        msg.send "Multidev successfully deleted."
        msg.send run.data.log
      else
        msg.send "There may have been a problem..."
        msg.send run.data.log
```

On drush.io, the corresponding `delete-multidev` job might look something like this:

```sh
terminus env:delete ${TSITE}.${TENV} --delete-branch --yes
```

</div>
  </div>
</div>
