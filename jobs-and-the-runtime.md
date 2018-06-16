---
layout: page
title: Jobs & the Runtime
published: true
order: 4
---

<div class="col s12 no-padding">
  <ul class="tabs tabs-fixed-width">
    <li class="tab col s2"><a href="#runtime"><i class="material-icons">storage</i> The runtime</a></li>
    <li class="tab col s2"><a href="#logical-operators"><i class="material-icons">settings</i> Logical operators</a></li>
    <li class="tab col s2"><a href="#substitution"><i class="material-icons">swap_horiz</i> Command substitution</a></li>
    <li class="tab col s2"><a href="#async"><i class="material-icons">sync</i> Async</a></li>
    <li class="tab col s2"><a href="#variables"><i class="material-icons">attach_money</i> Variables</a></li>
    <li class="tab col s2"><a href="#io-redirection"><i class="material-icons">call_split</i> I/O redirection</a></li>
  </ul>
</div>
<div class="container">
  <div id="runtime" class="col s12">
<div markdown="1">

### Runtime

Every job run on drush.io is executed in an isolated container, offering a clean runtime environment for every run. No state persists between runs, ensuring predictable behavior.

#### __The environment__
Because job runs are queued by __individuals__, every job run is invoked in the context of the individual account's [credential](/the-basics#concepts). That means all commands executed during the job run are run with terminus pre-authenticated and with SSH keys appropriate to run remote commands. No need to run `terminus auth:login` in your job!

All commands are run in an initially empty file directory. You can populate the current working directory using tools like `git`, `composer`, `npm`, etc.

You can navigate the file directory using shell built-in commands like `cd` and `pwd`.

#### __Available CLI tools__
The following CLI tools are available and considered supported in the runtime:

- `terminus 1.x`
- `git 2.13`
- `mysql`
- `redis-cli 3.x`
- `composer 1.x`
- `php 5.6`
- `node 6.x`
- `npm 3.x`

Jobs are run on a recent version of Alpine Linux and all command line tools normally available therein are available to you, including `curl`.

To get the most out of drush.io, you'll want to make sure you're familiar with [terminus](https://pantheon.io/docs/terminus/), Pantheon's CLI. It's also important to note that, while drush.io jobs resemble a shell script, they aren't exactly shell scripts. Stick to the supported job syntax and you'll be golden!

Speaking of syntax, dive in with [logical operators](#logical-operators).

</div>
  </div>
  <div id="logical-operators" class="col s12">
<div markdown="1">

### Logical Operators

All commands exit with a status code when they complete: `0` means everything went according to plan, any number greater than that indicates some type of error or logical negative state. You can employ these exit codes to your advantage using _logical operators_ to chain commands together in your jobs.

- `&&` between commands means "if the first command exits 0, run the next command.
- `||` between commands means "only run the next command if the first command exited > 0"

Take this statement as an example:

```sh
terminus env:info my-corp.my-env || terminus env:create my-corp.test my-env
```

Because the `env:info` terminus command exits `1` if the `my-env` multidev doesn't exist, the `env:create` command only ever runs when it needs to.

Next, learn about [command substitution](#substitution).

</div>
  </div>
  <div id="substitution" class="col s12">
<div markdown="1">

### Command Substitution

Sometimes, you want to use the output of a command as an argument to another command, or occasionally, the basis for another command entirely. Use _command substitution_ to solve these needs.

Here's an example statement that uses the output of one command as an argument in another.

```sh
terminus env:create my-corp.test `git rev-parse --abbrev-ref HEAD`
```

This would create a multidev environment, based on the database and files of the test environment, named after the currently active git branch in the current working directory.

This example statement uses the output of one command as the basis for a different command.

```sh
`terminus connection:info my-corp.test --field=mysql_command` -e "TRUNCATE sessions;"
```

This would truncate the `sessions` table on the test environment using the MySQL client.

Next, learn about [asynchronous commands](#async).

</div>
  </div>
  <div id="async" class="col s12">
<div markdown="1">

### Asynchronous Commands

Some terminus commands initiate (and then wait for) potentially long-running operations. An example of this is multidev environment creation.

While in most cases, you would want to wait for the operation to complete before proceeding or ending the job, occasionally, you may wish to run a job __asynchronously__, or in the background.

As an example, you might have a _Recreate Integration Environment_ job, like this:

```sh
terminus env:delete my-corp.integrate --yes
terminus env:create my-corp.live integrate &
sleep 10
```

Here we're _synchronously_ deleting the `integrate` environment, then once complete, _asynchronously_ creating the same environment using the `&` operator.

Note the use of the `sleep` command, which gives terminus time to reach out to Pantheon and initiate multidev creation before the job ends.

Beware, not all terminus commands work this way.

Next, learn about [variables](#variables).

</div>
  </div>
  <div id="variables" class="col s12">
<div markdown="1">

### Variables

As you build out your library of jobs, you may find that some of your operations are generic enough that they could easily be run on any number of sites. Instead of copy/pasting jobs, you can use _variables_ to reduce repetition.

You'll likely want to use variables with __default values__ as you're starting out:

```sh
terminus env:clear-cache ${TSITE_ENV:-my-corp.test}
```

This allows you to run the job using the drush.io UI (which doesn't provide a way to customize variables at run-time), but enables API users to provide custom values.

For commands that don't make sense without custom values, you could use __simple variables__ like this:

```sh
terminus env:delete $TSITE_ENV --yes
```

This command would only delete a multidev environment if a value like `my-corp.my-env` were provided via the API.

Also note that you can concatenate variables to form a single argument. For example, if you primarily work on one site, but wish to add the flexibility of swapping sites in your job, you could use this common pattern:

```sh
terminus env:delete ${TSITE:-my-corp}.$TENV --yes
```

Here, you could choose to only provide a `TENV` value in your API call, which would default to deleting the provided environment on the `my-corp` site. Or, you could provide both a `TENV` and a `TSITE` to delete a multidev elsewhere.

#### Setting variables inline

Variables are stateful and can be set and unset as in a shell script. This can help make jobs clearer and easier to digest. For example:

```sh
URL='https://live-my-corp.pantheonsite.io'
echo "Reading headers for url $URL"
curl -I --silent $URL
```

Variables can also be used to store and re-use a value that takes time to calculate or return: 

```sh
URL=`terminus env:view my-corp.${TENV:-live}--print`
echo "Reading headers for url $URL"
curl -I --silent $URL
```

Note also that variables may be unset.

```sh
URL='https://live-my-corp.pantheonsite.io'
echo $URL
unset URL
echo $URL
```

Next, learn about [I/O redirection](#io-redirection)

</div>
  </div>
  <div id="io-redirection" class="col s12">
<div markdown="1">

### I/O Redirection

Sometimes, you don't want the output of a command to be displayed. In such cases, you can use _I/O redirection_ to pipe command output away from standard out.

A common use-case for this is running set-up commands, but returning a clean, API response:

```sh
terminus env:create ${TSITE}.test $TENV --yes > /dev/null 2>&1 && \
  terminus env:info ${TSITE}.$TENV --format=json
```

This statement would create a multidev environment and, if successful, print a JSON payload to the log output containing useful information like domain or creation date.

By hiding the output of the set-up commands, you can design a JSON API for yourself based on the log output of the job run.

#### Simple Pipes

In addition to stdout/stderr redirection, drush.io supports basic piping. This is commonly used for filtering the output of a command, but you can pass the output of any command to any other command.  For example:

```sh
terminus site:list --field=name | grep client-name
```

Now that you know the runtime environment and job syntax, you're ready to build a custom ops platform on [the drush.io API](/api).

</div>
  </div>
</div>
