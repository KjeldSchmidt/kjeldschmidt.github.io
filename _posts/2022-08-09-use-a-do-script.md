---
title: I think you should use a do-script
date: 2022-08-09 00:00:00 +0000
categories: [Software Development, Software Engineering]
tags: [software development, software engineering]
---

Let's get to the point:

- I think you should have a `do`-script in your project
- I'll explain what that even is and why I think you should have one
- The only reason I'm writing about it is that I can't find any sources online
- This might be because the term "`do`-script" is incredibly generic and hard to google.
- It might also be because the concept isn't all that popular, or generally known under a different name.
- I'm not gonna try to find a more marketable term. You're already here.
- There's a lot of "why" coming up. If you're future me and just need the code, scroll to the bottom.

Alright, a definition:

> A `do`-script contains a code-as-documentation, version-controlled list of actions that a developer might want to perform when working on the code-base.

Ever found yourself verbally explaining how to compile a project? Searched your shell history to remember what command invokes the autoformatter? Is your environment setup meticulously explained in your project's `README`, with liberal use of code blocks showing which commands to put into the terminal?

Then you need a `do`-script.

If a common operation is known to the developers, but not written down (or written down outside of the repository), put it down, in _executable_ code. If a common operations is _already documented_ as code, as is frequently the case in `README`-files, move it into an executable file.

Okay, enough of the marketing, let's show how this works. In my (entirely facetious) current project, there exists a file called `do` at the root of the project. If I didn't know already, the `README` would helpfully tell me that I should try running it in a terminal.

```
> ./do

Usage: ./do
         setup-venv   Creates the virtual environment for the project and installs all dependencies
         run          Runs the webserver locally
         test         Runs fast unit tests
         test-e2e     Runs slow e2e tests  
         fmt-check    Checks if all files follow the formatting style
         fmt          Applies the autoformatting style
         deploy       Deploy the code to a specificed environment, given as the first argument
```

And if I wanted to, say, run unit tests after making some code changes, I would simply do

```
> ./do test

<Test framework output>
```

And at this point in time, I don't have to care about what exact command to invoke, what parameters to pass, I don't even have to activate the virtual environment - the `do`-script does all that for me. And if I do want to know, I can just look at the script!


## Discoverability and Maintainability

The power of the `do`-script is threefold.

First, documentation - it encourages you to put knowledge that exists in your head or in non-executable documentation into an executable context.
This means that people will actually _use_ this code, which means people will notice if things become outdated and they'll update them.
We all known very well that this would not be the case for non-executable documentation.

Second, discoverability - it allows new team members to easily discover what things are important and which are not. Gradle tasks, for example, do something similar: there is a list of actions that can be taken through Gradle for a given project. But in my experience, those are often overwhelming. Applying a single gradle plugin often introduces a handful of commands, only one of which is actually relevant for the project. How are we gonna figure out whether we want to use `./gradlew SpotlessApply` or `./gradlew SpotlessJavaApply` or whatever else?

Well, if we have a `do`-script, we can know that there is _one_ command to format, and just use it. If we ever care about details, we can look at the source, or we can just not. No need to ask someone who's more experienced on the project (not that I want to discourage communication - I want to encourage talking about team practices, project context and business details, not how to invoke the damn formatter)

And third, maintainability. Does the build process rely on some specific IDE setup? This can be fine in some cases, but for a long-lived project, you probably don't want to get tied to specific IDEs or even specific versions. If you have a shell-driven process, you remove a lot of dependencies. Even better if the script itself takes care to install all dependencies that _are_ still needed.

And actually, since the script already performs so much setup and encodes quality gates...

## CI/CD and the `do`-script

...we can and should use it in our CI/CD-pipeline! What better way to make sure that the executable documentation stays up-to-date and accurate than making it a core component of your deployment system?

There are a bunch of different CI/CD systems with a bunch of different ways to perform common actions. But all of them also allow you to just invoke a bash command. Why not decouple yourself from the CI/CD runtime at least a little bit by invoking your `do`-script instead of using a runtime-specific solution? Chances are, there is large overlap between the things you want to run locally and those you want to run in CI/CD. And you are absolutely allowed to pass parameters or check for environment variables to adapt the execution logic between developer machines and pipeline machines!


## Be pragmatic

The `do`-script is supposed to be a living document, a testament to the ways the project is being used. Change it when needed, ignore edge-cases when they seem too complicated, have several `do`-scripts in subfolders. Don't make it the subject of business requirements.

Also, call it `do`, not `do.sh`. Solely and exclusively because it's faster to type. For the same reason, the tasks in our above example was called "fmt-check", not "format_check".


## And finally, code:

Alright, here's the content for a baseline `do`-script.

```bash
#!/usr/bin/env bash

## example-thing: Perfoms a thing or something
function task_example_thing {
  echo "Doing very important work, I swear"
}

function internal_helper_function {
  echo "I will not be listed in the tasks for this file"
}

#-------- All task definitions go above this line --------#

function task_usage {
    echo "Usage: $0"
    sed -n 's/^##//p' <"$0" | column -t -s ':' |  sed -E $'s/^/\t/'
}

cmd=${1:-}
shift || true
resolved_command=$(echo "task_${cmd}" | sed 's/-/_/g')
if [[ "$(LC_ALL=C type -t "${resolved_command}")" == "function" ]]; then
    pushd "$(dirname "${BASH_SOURCE[0]}")" >/dev/null
    ${resolved_command} "$@"
else
    task_usage
    if [ -n "${cmd}" ]; then
      echo "'$cmd' could not be resolved - please use one of the above tasks"
      exit 1
    fi
fi
```

Let's see some action:

```
> ./do

Usage: ./do
         example-thing   Perfoms a thing or something

> ./do example-thing
Doing very important work, I swear
> ./do example_thing
Doing very important work, I swear
> ./do unknown-task

Usage: ./do
         example-thing   Perfoms a thing or something

'unknown-task' could not be resolved - please use one of the above tasks
```

Note that tasks can only be invoked if their function definition has a `task_`-prefix. This means we can have helper functions which are not "exposed". This is purely to remove noise - nothing about the `do`-script as such is secretive, of course.

Note also that the tasks listed is generated by looking for lines that start with `##`, so there is still a hint of non-living code here. In my experience, that's okay - since these tasks are actively being used, and the list of tasks looked at, discrepancies are quickly discovered and can be fixed immediately, since the "interface" is not external in any way. 

That's it. Have a splendid time.