Contents

-   [Why scripting in Bash?](https://betterdev.blog/minimal-safe-bash-script-template/#why-scripting-in-bash)
-   [Bash script template](https://betterdev.blog/minimal-safe-bash-script-template/#bash-script-template)
-   [Choose Bash](https://betterdev.blog/minimal-safe-bash-script-template/#choose-bash)
-   [Fail fast](https://betterdev.blog/minimal-safe-bash-script-template/#fail-fast)
-   [Get the location](https://betterdev.blog/minimal-safe-bash-script-template/#get-the-location)
-   [Try to clean up](https://betterdev.blog/minimal-safe-bash-script-template/#try-to-clean-up)
-   [Display helpful help](https://betterdev.blog/minimal-safe-bash-script-template/#display-helpful-help)
-   [Print nice messages](https://betterdev.blog/minimal-safe-bash-script-template/#print-nice-messages)
-   [Parse any parameters](https://betterdev.blog/minimal-safe-bash-script-template/#parse-any-parameters)
-   [Using the template](https://betterdev.blog/minimal-safe-bash-script-template/#using-the-template)
-   [Portability](https://betterdev.blog/minimal-safe-bash-script-template/#portability)
-   [Further reading](https://betterdev.blog/minimal-safe-bash-script-template/#further-reading)
-   [Closing notes](https://betterdev.blog/minimal-safe-bash-script-template/#closing-notes)

Bash scripts. Almost anyone needs to write one sooner or later. Almost no one says “yeah, I love writing them”. And that’s why almost everyone is putting low attention while writing them.

[![Tweet by Jake Wharton: The opposite of "it's like riding a bike" is "it's like programming in bash". A phrase which means that no matter how many times you do something, you will have to re-learn it every single time.](https://betterdev.blog/_astro/tweet.WvJq_i48_PBbnA.webp)](https://twitter.com/JakeWharton/status/1334177665356587008)

I won’t try to make you a Bash expert (since I’m not a one either), but I will show you a minimal template that will make your scripts safer. You don’t need to thank me, your future self will thank you.

## Why scripting in Bash?

The best summary of Bash scripting appeared recently on my Twitter feed:

But Bash has something in common with another widely beloved language. Just like JavaScript, it won’t go away easily. While we can hope that Bash won’t become the main language for literally everything, it’s always somewhere near.

Bash [inherited the shell throne](https://www.quora.com/Is-Bash-considered-the-lingua-franca-of-shells/answer/Paul-Reiber) and can be found on almost every Linux, including Docker images. And this is the environment in which most of the backend runs. So if you need to script the server application startup, a CI/CD step, or integration test run, Bash is there for you.

To glue few commands together, pass output from one to another, and just start some executable, Bash is the easiest and most native solution. While it makes perfect sense to write bigger, more complicated scripts in other languages, you can’t expect to have Python, Ruby, fish, or whatever another interpreter you believe is the best, available everywhere. And you probably should think twice and then once more before adding it to some prod server, Docker image, or CI environment.

Yet Bash is far from perfect. The syntax is a nightmare. Error handling is difficult. There are landmines everywhere. And we have to deal with it.

## Bash script template

Without further ado, here it is.

```
<span><span data-darkreader-inline-color="">#!/usr/bin/env bash</span></span>
<span></span>
<span><span data-darkreader-inline-color="">set</span><span data-darkreader-inline-color=""> -Eeuo</span><span data-darkreader-inline-color=""> pipefail</span></span>
<span><span data-darkreader-inline-color="">trap</span><span data-darkreader-inline-color=""> cleanup</span><span data-darkreader-inline-color=""> SIGINT</span><span data-darkreader-inline-color=""> SIGTERM</span><span data-darkreader-inline-color=""> ERR</span><span data-darkreader-inline-color=""> EXIT</span></span>
<span></span>
<span><span data-darkreader-inline-color="">script_dir</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">$(</span><span data-darkreader-inline-color="">cd</span><span data-darkreader-inline-color=""> "$(</span><span data-darkreader-inline-color="">dirname</span><span data-darkreader-inline-color=""> "${</span><span data-darkreader-inline-color="">BASH_SOURCE</span><span data-darkreader-inline-color="">[0]}")" &amp;</span><span data-darkreader-inline-color="">&gt;</span><span data-darkreader-inline-color="">/dev/null &amp;&amp; </span><span data-darkreader-inline-color="">pwd</span><span data-darkreader-inline-color=""> -P</span><span data-darkreader-inline-color="">)</span></span>
<span></span>
<span><span data-darkreader-inline-color="">usage</span><span data-darkreader-inline-color="">() {</span></span>
<span><span data-darkreader-inline-color="">  cat</span><span data-darkreader-inline-color=""> &lt;&lt;</span><span data-darkreader-inline-color=""> EOF</span><span data-darkreader-inline-color=""> # remove the space between &lt;&lt; and EOF, this is due to web plugin issue</span></span>
<span><span data-darkreader-inline-color="">Usage: $(</span><span data-darkreader-inline-color="">basename</span><span data-darkreader-inline-color=""> "${</span><span data-darkreader-inline-color="">BASH_SOURCE</span><span data-darkreader-inline-color="">[0]}") [-h] [-v] [-f] -p param_value arg1 [arg2...]</span></span>
<span></span>
<span><span data-darkreader-inline-color="">Script description here.</span></span>
<span></span>
<span><span data-darkreader-inline-color="">Available options:</span></span>
<span></span>
<span><span data-darkreader-inline-color="">-h, --help      Print this help and exit</span></span>
<span><span data-darkreader-inline-color="">-v, --verbose   Print script debug info</span></span>
<span><span data-darkreader-inline-color="">-f, --flag      Some flag description</span></span>
<span><span data-darkreader-inline-color="">-p, --param     Some param description</span></span>
<span><span data-darkreader-inline-color="">EOF</span></span>
<span><span data-darkreader-inline-color="">  exit</span></span>
<span><span data-darkreader-inline-color="">}</span></span>
<span></span>
<span><span data-darkreader-inline-color="">cleanup</span><span data-darkreader-inline-color="">() {</span></span>
<span><span data-darkreader-inline-color="">  trap</span><span data-darkreader-inline-color=""> -</span><span data-darkreader-inline-color=""> SIGINT</span><span data-darkreader-inline-color=""> SIGTERM</span><span data-darkreader-inline-color=""> ERR</span><span data-darkreader-inline-color=""> EXIT</span></span>
<span><span data-darkreader-inline-color="">  # script cleanup here</span></span>
<span><span data-darkreader-inline-color="">}</span></span>
<span></span>
<span><span data-darkreader-inline-color="">setup_colors</span><span data-darkreader-inline-color="">() {</span></span>
<span><span data-darkreader-inline-color="">  if</span><span data-darkreader-inline-color=""> [[ </span><span data-darkreader-inline-color="">-t</span><span data-darkreader-inline-color=""> 2</span><span data-darkreader-inline-color=""> ]] &amp;&amp; [[ </span><span data-darkreader-inline-color="">-z</span><span data-darkreader-inline-color=""> "${</span><span data-darkreader-inline-color="">NO_COLOR-</span><span data-darkreader-inline-color="">}"</span><span data-darkreader-inline-color=""> ]] &amp;&amp; [[ </span><span data-darkreader-inline-color="">"${</span><span data-darkreader-inline-color="">TERM-</span><span data-darkreader-inline-color="">}"</span><span data-darkreader-inline-color=""> !=</span><span data-darkreader-inline-color=""> "dumb"</span><span data-darkreader-inline-color=""> ]]; </span><span data-darkreader-inline-color="">then</span></span>
<span><span data-darkreader-inline-color="">    NOFORMAT</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[0m'</span><span data-darkreader-inline-color=""> RED</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[0;31m'</span><span data-darkreader-inline-color=""> GREEN</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[0;32m'</span><span data-darkreader-inline-color=""> ORANGE</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[0;33m'</span><span data-darkreader-inline-color=""> BLUE</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[0;34m'</span><span data-darkreader-inline-color=""> PURPLE</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[0;35m'</span><span data-darkreader-inline-color=""> CYAN</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[0;36m'</span><span data-darkreader-inline-color=""> YELLOW</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[1;33m'</span></span>
<span><span data-darkreader-inline-color="">  else</span></span>
<span><span data-darkreader-inline-color="">    NOFORMAT</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span><span data-darkreader-inline-color=""> RED</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span><span data-darkreader-inline-color=""> GREEN</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span><span data-darkreader-inline-color=""> ORANGE</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span><span data-darkreader-inline-color=""> BLUE</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span><span data-darkreader-inline-color=""> PURPLE</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span><span data-darkreader-inline-color=""> CYAN</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span><span data-darkreader-inline-color=""> YELLOW</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span></span>
<span><span data-darkreader-inline-color="">  fi</span></span>
<span><span data-darkreader-inline-color="">}</span></span>
<span></span>
<span><span data-darkreader-inline-color="">msg</span><span data-darkreader-inline-color="">() {</span></span>
<span><span data-darkreader-inline-color="">  echo</span><span data-darkreader-inline-color=""> &gt;&amp;2</span><span data-darkreader-inline-color=""> -e</span><span data-darkreader-inline-color=""> "</span><span data-darkreader-inline-color="">${1</span><span data-darkreader-inline-color="">-</span><span data-darkreader-inline-color="">}</span><span data-darkreader-inline-color="">"</span></span>
<span><span data-darkreader-inline-color="">}</span></span>
<span></span>
<span><span data-darkreader-inline-color="">die</span><span data-darkreader-inline-color="">() {</span></span>
<span><span data-darkreader-inline-color="">  local</span><span data-darkreader-inline-color=""> msg</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">$1</span></span>
<span><span data-darkreader-inline-color="">  local</span><span data-darkreader-inline-color=""> code</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">${2</span><span data-darkreader-inline-color="">-</span><span data-darkreader-inline-color="">1}</span><span data-darkreader-inline-color=""> # default exit status 1</span></span>
<span><span data-darkreader-inline-color="">  msg</span><span data-darkreader-inline-color=""> "</span><span data-darkreader-inline-color="">$msg</span><span data-darkreader-inline-color="">"</span></span>
<span><span data-darkreader-inline-color="">  exit</span><span data-darkreader-inline-color=""> "</span><span data-darkreader-inline-color="">$code</span><span data-darkreader-inline-color="">"</span></span>
<span><span data-darkreader-inline-color="">}</span></span>
<span></span>
<span><span data-darkreader-inline-color="">parse_params</span><span data-darkreader-inline-color="">() {</span></span>
<span><span data-darkreader-inline-color="">  # default values of variables set from params</span></span>
<span><span data-darkreader-inline-color="">  flag</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">0</span></span>
<span><span data-darkreader-inline-color="">  param</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span></span>
<span></span>
<span><span data-darkreader-inline-color="">  while</span><span data-darkreader-inline-color=""> :</span><span data-darkreader-inline-color="">; </span><span data-darkreader-inline-color="">do</span></span>
<span><span data-darkreader-inline-color="">    case</span><span data-darkreader-inline-color=""> "</span><span data-darkreader-inline-color="">${1</span><span data-darkreader-inline-color="">-</span><span data-darkreader-inline-color="">}</span><span data-darkreader-inline-color="">"</span><span data-darkreader-inline-color=""> in</span></span>
<span><span data-darkreader-inline-color="">    -h</span><span data-darkreader-inline-color=""> |</span><span data-darkreader-inline-color=""> --help</span><span data-darkreader-inline-color="">)</span><span data-darkreader-inline-color=""> usage</span><span data-darkreader-inline-color=""> ;;</span></span>
<span><span data-darkreader-inline-color="">    -v</span><span data-darkreader-inline-color=""> |</span><span data-darkreader-inline-color=""> --verbose</span><span data-darkreader-inline-color="">)</span><span data-darkreader-inline-color=""> set</span><span data-darkreader-inline-color=""> -x</span><span data-darkreader-inline-color=""> ;;</span></span>
<span><span data-darkreader-inline-color="">    --no-color</span><span data-darkreader-inline-color="">)</span><span data-darkreader-inline-color=""> NO_COLOR</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">1</span><span data-darkreader-inline-color=""> ;;</span></span>
<span><span data-darkreader-inline-color="">    -f</span><span data-darkreader-inline-color=""> |</span><span data-darkreader-inline-color=""> --flag</span><span data-darkreader-inline-color="">)</span><span data-darkreader-inline-color=""> flag</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">1</span><span data-darkreader-inline-color=""> ;; </span><span data-darkreader-inline-color=""># example flag</span></span>
<span><span data-darkreader-inline-color="">    -p</span><span data-darkreader-inline-color=""> |</span><span data-darkreader-inline-color=""> --param</span><span data-darkreader-inline-color="">)</span><span data-darkreader-inline-color=""> # example named parameter</span></span>
<span><span data-darkreader-inline-color="">      param="</span><span data-darkreader-inline-color="">${2</span><span data-darkreader-inline-color="">-</span><span data-darkreader-inline-color="">}</span><span data-darkreader-inline-color="">"</span></span>
<span><span data-darkreader-inline-color="">      shift</span></span>
<span><span data-darkreader-inline-color="">      ;;</span></span>
<span><span data-darkreader-inline-color="">    -</span><span data-darkreader-inline-color="">?*</span><span data-darkreader-inline-color="">)</span><span data-darkreader-inline-color=""> die</span><span data-darkreader-inline-color=""> "Unknown option: </span><span data-darkreader-inline-color="">$1</span><span data-darkreader-inline-color="">"</span><span data-darkreader-inline-color=""> ;;</span></span>
<span><span data-darkreader-inline-color="">    *)</span><span data-darkreader-inline-color=""> break</span><span data-darkreader-inline-color=""> ;;</span></span>
<span><span data-darkreader-inline-color="">    esac</span></span>
<span><span data-darkreader-inline-color="">    shift</span></span>
<span><span data-darkreader-inline-color="">  done</span></span>
<span></span>
<span><span data-darkreader-inline-color="">  args</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">(</span><span data-darkreader-inline-color="">"</span><span data-darkreader-inline-color="">$@</span><span data-darkreader-inline-color="">"</span><span data-darkreader-inline-color="">)</span></span>
<span></span>
<span><span data-darkreader-inline-color="">  # check required params and arguments</span></span>
<span><span data-darkreader-inline-color="">  [[ </span><span data-darkreader-inline-color="">-z</span><span data-darkreader-inline-color=""> "${</span><span data-darkreader-inline-color="">param-</span><span data-darkreader-inline-color="">}"</span><span data-darkreader-inline-color=""> ]] &amp;&amp; </span><span data-darkreader-inline-color="">die</span><span data-darkreader-inline-color=""> "Missing required parameter: param"</span></span>
<span><span data-darkreader-inline-color="">  [[ ${</span><span data-darkreader-inline-color="">#</span><span data-darkreader-inline-color="">args[@]} </span><span data-darkreader-inline-color="">-eq</span><span data-darkreader-inline-color=""> 0</span><span data-darkreader-inline-color=""> ]] &amp;&amp; </span><span data-darkreader-inline-color="">die</span><span data-darkreader-inline-color=""> "Missing script arguments"</span></span>
<span></span>
<span><span data-darkreader-inline-color="">  return</span><span data-darkreader-inline-color=""> 0</span></span>
<span><span data-darkreader-inline-color="">}</span></span>
<span></span>
<span><span data-darkreader-inline-color="">parse_params</span><span data-darkreader-inline-color=""> "</span><span data-darkreader-inline-color="">$@</span><span data-darkreader-inline-color="">"</span></span>
<span><span data-darkreader-inline-color="">setup_colors</span></span>
<span></span>
<span><span data-darkreader-inline-color=""># script logic here</span></span>
<span></span>
<span><span data-darkreader-inline-color="">msg</span><span data-darkreader-inline-color=""> "${</span><span data-darkreader-inline-color="">RED</span><span data-darkreader-inline-color="">}Read parameters:${</span><span data-darkreader-inline-color="">NOFORMAT</span><span data-darkreader-inline-color="">}"</span></span>
<span><span data-darkreader-inline-color="">msg</span><span data-darkreader-inline-color=""> "- flag: ${</span><span data-darkreader-inline-color="">flag</span><span data-darkreader-inline-color="">}"</span></span>
<span><span data-darkreader-inline-color="">msg</span><span data-darkreader-inline-color=""> "- param: ${</span><span data-darkreader-inline-color="">param</span><span data-darkreader-inline-color="">}"</span></span>
<span><span data-darkreader-inline-color="">msg</span><span data-darkreader-inline-color=""> "- arguments: ${</span><span data-darkreader-inline-color="">args</span><span data-darkreader-inline-color="">[*]</span><span data-darkreader-inline-color="">-</span><span data-darkreader-inline-color="">}"</span></span>
<span></span>
```

The idea was to not make it too long. I don’t want to scroll 500 lines to the script logic. At the same time, I want some strong foundations for any script. But Bash is not making this easy, lacking any form of dependencies management.

One solution would be to have a separate script with all the boilerplate and utility functions and execute it at the beginning. The downside would be to have to always attach this second file everywhere, losing the “simple Bash script” idea along the way. So I decided to put in the template only what I consider to be a minimum to keep it possible short.

Now let’s look at it in more detail.

### Choose Bash

```
<span><span data-darkreader-inline-color="">#!/usr/bin/env bash</span></span>
<span></span>
```

Script traditionally starts with a shebang. For the [best compatibility](https://stackoverflow.com/questions/21612980/why-is-usr-bin-env-bash-superior-to-bin-bash), it references `/usr/bin/env`, not the `/bin/bash` directly. Although, if you read comments in the linked StackOverflow question, even this can fail sometimes.

### Fail fast

```
<span><span data-darkreader-inline-color="">set</span><span data-darkreader-inline-color=""> -Eeuo</span><span data-darkreader-inline-color=""> pipefail</span></span>
<span></span>
```

The `set` command changes script execution options. For example, **normally Bash does not care if some command failed**, returning a non-zero exit status code. It just happily jumps to the next one. Now consider this little script:

```
<span><span data-darkreader-inline-color="">#!/usr/bin/env bash</span></span>
<span><span data-darkreader-inline-color="">cp</span><span data-darkreader-inline-color=""> important_file</span><span data-darkreader-inline-color=""> ./backups/</span></span>
<span><span data-darkreader-inline-color="">rm</span><span data-darkreader-inline-color=""> important_file</span></span>
<span></span>
```

What will happen, if the `backups` directory does not exist? Exactly, you will get an error message in the console, but before you will be able to react, the file will be already removed by the second command.

For details on what options exactly `set -Eeuo pipefail` changes and how they will protect you, I refer you to the [article I have in my bookmarks for a few years now](https://vaneyckt.io/posts/safer_bash_scripts_with_set_euxo_pipefail/).

Although you should know that there are some [arguments against setting those options](https://www.reddit.com/r/commandline/comments/g1vsxk/the_first_two_statements_of_your_bash_script/fniifmk?utm_source=share&utm_medium=web2x&context=3).

### Get the location

```
<span><span data-darkreader-inline-color="">script_dir</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">$(</span><span data-darkreader-inline-color="">cd</span><span data-darkreader-inline-color=""> "$(</span><span data-darkreader-inline-color="">dirname</span><span data-darkreader-inline-color=""> "${</span><span data-darkreader-inline-color="">BASH_SOURCE</span><span data-darkreader-inline-color="">[0]}")" &amp;</span><span data-darkreader-inline-color="">&gt;</span><span data-darkreader-inline-color="">/dev/null &amp;&amp; </span><span data-darkreader-inline-color="">pwd</span><span data-darkreader-inline-color=""> -P</span><span data-darkreader-inline-color="">)</span></span>
<span></span>
```

This line [does its best](https://stackoverflow.com/a/246128/2512304) to define the script’s location directory, and then we `cd` to it. Why?

Often our scripts are operating on paths relative to the script location, copying files and executing commands, assuming the script directory is also a working directory. And it is, as long as we execute the script from its directory.

But if, let’s say, our CI config executes script like this:

```
<span><span data-darkreader-inline-color="">/opt/ci/project/script.sh</span></span>
<span></span>
```

then our script is operating not in project dir, but some completely different workdir of our CI tool. We can fix it, by going to the directory before executing the script:

```
<span><span data-darkreader-inline-color="">cd</span><span data-darkreader-inline-color=""> /opt/ci/project</span><span data-darkreader-inline-color=""> &amp;&amp; </span><span data-darkreader-inline-color="">./script.sh</span></span>
<span></span>
```

But it’s much nicer to solve this on the script side. So, if the script reads some file or executes another program from the same directory, call it like this:

```
<span><span data-darkreader-inline-color="">cat</span><span data-darkreader-inline-color=""> "</span><span data-darkreader-inline-color="">$script_dir</span><span data-darkreader-inline-color="">/my_file"</span></span>
<span></span>
```

At the same time, the script does not change the workdir location. If the script is executed from some other directory and the user provides a relative path to some file, we will still be able to read it.

### Try to clean up

```
<span><span data-darkreader-inline-color="">trap</span><span data-darkreader-inline-color=""> cleanup</span><span data-darkreader-inline-color=""> SIGINT</span><span data-darkreader-inline-color=""> SIGTERM</span><span data-darkreader-inline-color=""> ERR</span><span data-darkreader-inline-color=""> EXIT</span></span>
<span></span>
```

```
<span><span data-darkreader-inline-color="">cleanup</span><span data-darkreader-inline-color="">() {</span></span>
<span><span data-darkreader-inline-color="">  trap</span><span data-darkreader-inline-color=""> -</span><span data-darkreader-inline-color=""> SIGINT</span><span data-darkreader-inline-color=""> SIGTERM</span><span data-darkreader-inline-color=""> ERR</span><span data-darkreader-inline-color=""> EXIT</span></span>
<span><span data-darkreader-inline-color="">  # script cleanup here</span></span>
<span><span data-darkreader-inline-color="">}</span></span>
<span></span>
```

Think about the `trap` like of a `finally` block for the script. At the end of the script – normal, caused by an error or an external signal – the `cleanup()` function will be executed. This is a place where you can, for example, try to remove all temporary files created by the script.

Just remember that the `cleanup()` can be called not only at the end but as well having the script done any part of the work. Not necessarily all the resources you try to cleanup will exist.

### Display helpful help

```
<span><span data-darkreader-inline-color="">usage</span><span data-darkreader-inline-color="">() {</span></span>
<span><span data-darkreader-inline-color="">  cat</span><span data-darkreader-inline-color=""> &lt;&lt;</span><span data-darkreader-inline-color=""> EOF</span><span data-darkreader-inline-color=""> # remove the space between &lt;&lt; and EOF, this is due to web plugin issue</span></span>
<span><span data-darkreader-inline-color="">Usage: $(</span><span data-darkreader-inline-color="">basename</span><span data-darkreader-inline-color=""> "${</span><span data-darkreader-inline-color="">BASH_SOURCE</span><span data-darkreader-inline-color="">[0]}") [-h] [-v] [-f] -p param_value arg1 [arg2...]</span></span>
<span></span>
<span><span data-darkreader-inline-color="">Script description here.</span></span>
<span></span>
<span><span data-darkreader-inline-color="">...</span></span>
<span><span data-darkreader-inline-color="">EOF</span></span>
<span><span data-darkreader-inline-color="">  exit</span></span>
<span><span data-darkreader-inline-color="">}</span></span>
<span></span>
```

Having the `usage()` relatively close to the top of the script, it will act in two ways:

-   to **display help** for someone who does not know all the options and does not want to go over the whole script to discover them,
-   as a **minimal documentation** when someone modifies the script (for example you, 2 weeks later, not even remembering writing it in the first place).

I don’t argue to document every function here. But a short, nice script usage message is a required minimum.

### Print nice messages

```
<span><span data-darkreader-inline-color="">setup_colors</span><span data-darkreader-inline-color="">() {</span></span>
<span><span data-darkreader-inline-color="">  if</span><span data-darkreader-inline-color=""> [[ </span><span data-darkreader-inline-color="">-t</span><span data-darkreader-inline-color=""> 2</span><span data-darkreader-inline-color=""> ]] &amp;&amp; [[ </span><span data-darkreader-inline-color="">-z</span><span data-darkreader-inline-color=""> "${</span><span data-darkreader-inline-color="">NO_COLOR-</span><span data-darkreader-inline-color="">}"</span><span data-darkreader-inline-color=""> ]] &amp;&amp; [[ </span><span data-darkreader-inline-color="">"${</span><span data-darkreader-inline-color="">TERM-</span><span data-darkreader-inline-color="">}"</span><span data-darkreader-inline-color=""> !=</span><span data-darkreader-inline-color=""> "dumb"</span><span data-darkreader-inline-color=""> ]]; </span><span data-darkreader-inline-color="">then</span></span>
<span><span data-darkreader-inline-color="">    NOFORMAT</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[0m'</span><span data-darkreader-inline-color=""> RED</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[0;31m'</span><span data-darkreader-inline-color=""> GREEN</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[0;32m'</span><span data-darkreader-inline-color=""> ORANGE</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[0;33m'</span><span data-darkreader-inline-color=""> BLUE</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[0;34m'</span><span data-darkreader-inline-color=""> PURPLE</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[0;35m'</span><span data-darkreader-inline-color=""> CYAN</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[0;36m'</span><span data-darkreader-inline-color=""> YELLOW</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">'\033[1;33m'</span></span>
<span><span data-darkreader-inline-color="">  else</span></span>
<span><span data-darkreader-inline-color="">    NOFORMAT</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span><span data-darkreader-inline-color=""> RED</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span><span data-darkreader-inline-color=""> GREEN</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span><span data-darkreader-inline-color=""> ORANGE</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span><span data-darkreader-inline-color=""> BLUE</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span><span data-darkreader-inline-color=""> PURPLE</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span><span data-darkreader-inline-color=""> CYAN</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span><span data-darkreader-inline-color=""> YELLOW</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span></span>
<span><span data-darkreader-inline-color="">  fi</span></span>
<span><span data-darkreader-inline-color="">}</span></span>
<span></span>
<span><span data-darkreader-inline-color="">msg</span><span data-darkreader-inline-color="">() {</span></span>
<span><span data-darkreader-inline-color="">  echo</span><span data-darkreader-inline-color=""> &gt;&amp;2</span><span data-darkreader-inline-color=""> -e</span><span data-darkreader-inline-color=""> "</span><span data-darkreader-inline-color="">${1</span><span data-darkreader-inline-color="">-</span><span data-darkreader-inline-color="">}</span><span data-darkreader-inline-color="">"</span></span>
<span><span data-darkreader-inline-color="">}</span></span>
<span></span>
```

Firstly, remove the `setup_colors()` function if you don’t want to use colors in text anyway. I keep it because I know I would use colors more often if I wouldn’t have to google codes for them every time.

Secondly, those **colors are meant to be used with the `msg()` function only**, not with the `echo` command.

The **`msg()` function is meant to be used to print everything that is not a script output**. This includes all logs and messages, not only the errors. Citing the great [12 Factor CLI Apps](https://medium.com/@jdxcode/12-factor-cli-apps-dd3c227a0e46) article:

> **In short: stdout is for output, stderr is for messaging.**  
> ~ [Jeff Dickey](https://jdxcode.com/), who [knows a little](https://github.com/oclif/oclif/graphs/contributors) about [building CLI apps](https://github.com/heroku/cli/graphs/contributors)

That’s why in most cases you shouldn’t use colors for `stdout` anyway.

Messages printed with `msg()` are sent to `stderr` stream and support special sequences, like colors. And colors are disabled anyway if the `stderr` output is not an interactive terminal or [one of the standard parameters](https://no-color.org/) is passed.

Usage:

```
<span><span data-darkreader-inline-color="">msg</span><span data-darkreader-inline-color=""> "This is a ${</span><span data-darkreader-inline-color="">RED</span><span data-darkreader-inline-color="">}very important${</span><span data-darkreader-inline-color="">NOFORMAT</span><span data-darkreader-inline-color="">} message, but not a script output value!"</span></span>
<span></span>
```

To check how it behaves when the `stderr` is not an interactive terminal, add a line like above to the script. Then execute it redirecting `stderr` to `stdout` and piping it to `cat`. Pipe operation makes the output no longer being sent directly to the terminal, but to the next command, so the colors should be disabled now.

```
<span><span data-darkreader-inline-color="">./test.sh</span><span data-darkreader-inline-color=""> 2&gt;&amp;1</span><span data-darkreader-inline-color=""> |</span><span data-darkreader-inline-color=""> cat</span></span>
<span><span data-darkreader-inline-color=""></span><span data-darkreader-inline-color="">This is a very important message, but not a script output value</span><span data-darkreader-inline-color="">!</span></span>
<span></span>
```

### Parse any parameters

```
<span><span data-darkreader-inline-color="">parse_params</span><span data-darkreader-inline-color="">() {</span></span>
<span><span data-darkreader-inline-color="">  # default values of variables set from params</span></span>
<span><span data-darkreader-inline-color="">  flag</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">0</span></span>
<span><span data-darkreader-inline-color="">  param</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">''</span></span>
<span></span>
<span><span data-darkreader-inline-color="">  while</span><span data-darkreader-inline-color=""> :</span><span data-darkreader-inline-color="">; </span><span data-darkreader-inline-color="">do</span></span>
<span><span data-darkreader-inline-color="">    case</span><span data-darkreader-inline-color=""> "</span><span data-darkreader-inline-color="">${1</span><span data-darkreader-inline-color="">-</span><span data-darkreader-inline-color="">}</span><span data-darkreader-inline-color="">"</span><span data-darkreader-inline-color=""> in</span></span>
<span><span data-darkreader-inline-color="">    -h</span><span data-darkreader-inline-color=""> |</span><span data-darkreader-inline-color=""> --help</span><span data-darkreader-inline-color="">)</span><span data-darkreader-inline-color=""> usage</span><span data-darkreader-inline-color=""> ;;</span></span>
<span><span data-darkreader-inline-color="">    -v</span><span data-darkreader-inline-color=""> |</span><span data-darkreader-inline-color=""> --verbose</span><span data-darkreader-inline-color="">)</span><span data-darkreader-inline-color=""> set</span><span data-darkreader-inline-color=""> -x</span><span data-darkreader-inline-color=""> ;;</span></span>
<span><span data-darkreader-inline-color="">    --no-color</span><span data-darkreader-inline-color="">)</span><span data-darkreader-inline-color=""> NO_COLOR</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">1</span><span data-darkreader-inline-color=""> ;;</span></span>
<span><span data-darkreader-inline-color="">    -f</span><span data-darkreader-inline-color=""> |</span><span data-darkreader-inline-color=""> --flag</span><span data-darkreader-inline-color="">)</span><span data-darkreader-inline-color=""> flag</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">1</span><span data-darkreader-inline-color=""> ;; </span><span data-darkreader-inline-color=""># example flag</span></span>
<span><span data-darkreader-inline-color="">    -p</span><span data-darkreader-inline-color=""> |</span><span data-darkreader-inline-color=""> --param</span><span data-darkreader-inline-color="">)</span><span data-darkreader-inline-color=""> # example named parameter</span></span>
<span><span data-darkreader-inline-color="">      param="</span><span data-darkreader-inline-color="">${2</span><span data-darkreader-inline-color="">-</span><span data-darkreader-inline-color="">}</span><span data-darkreader-inline-color="">"</span></span>
<span><span data-darkreader-inline-color="">      shift</span></span>
<span><span data-darkreader-inline-color="">      ;;</span></span>
<span><span data-darkreader-inline-color="">    -</span><span data-darkreader-inline-color="">?*</span><span data-darkreader-inline-color="">)</span><span data-darkreader-inline-color=""> die</span><span data-darkreader-inline-color=""> "Unknown option: </span><span data-darkreader-inline-color="">$1</span><span data-darkreader-inline-color="">"</span><span data-darkreader-inline-color=""> ;;</span></span>
<span><span data-darkreader-inline-color="">    *)</span><span data-darkreader-inline-color=""> break</span><span data-darkreader-inline-color=""> ;;</span></span>
<span><span data-darkreader-inline-color="">    esac</span></span>
<span><span data-darkreader-inline-color="">    shift</span></span>
<span><span data-darkreader-inline-color="">  done</span></span>
<span></span>
<span><span data-darkreader-inline-color="">  args</span><span data-darkreader-inline-color="">=</span><span data-darkreader-inline-color="">(</span><span data-darkreader-inline-color="">"</span><span data-darkreader-inline-color="">$@</span><span data-darkreader-inline-color="">"</span><span data-darkreader-inline-color="">)</span></span>
<span></span>
<span><span data-darkreader-inline-color="">  # check required params and arguments</span></span>
<span><span data-darkreader-inline-color="">  [[ </span><span data-darkreader-inline-color="">-z</span><span data-darkreader-inline-color=""> "${</span><span data-darkreader-inline-color="">param-</span><span data-darkreader-inline-color="">}"</span><span data-darkreader-inline-color=""> ]] &amp;&amp; </span><span data-darkreader-inline-color="">die</span><span data-darkreader-inline-color=""> "Missing required parameter: param"</span></span>
<span><span data-darkreader-inline-color="">  [[ ${</span><span data-darkreader-inline-color="">#</span><span data-darkreader-inline-color="">args[@]} </span><span data-darkreader-inline-color="">-eq</span><span data-darkreader-inline-color=""> 0</span><span data-darkreader-inline-color=""> ]] &amp;&amp; </span><span data-darkreader-inline-color="">die</span><span data-darkreader-inline-color=""> "Missing script arguments"</span></span>
<span></span>
<span><span data-darkreader-inline-color="">  return</span><span data-darkreader-inline-color=""> 0</span></span>
<span><span data-darkreader-inline-color="">}</span></span>
<span></span>
```

If there is anything that makes sense to parametrized in the script, I usually do that. Even if the script is used only in a single place. It makes it easier to copy and reuse it, which often happens sooner than later. Also, even if something needs to be hardcoded, usually there is a better place for that on a higher level than the Bash script.

There are [three main types of CLI parameters](https://betterdev.blog/command-line-arguments-anatomy-explained/) – flags, named parameters, and positional arguments. The `parse_params()` function supports them all.

The only one of the common parameter patterns, that is not handled here, is [concatenated multiple single-letter flags](https://betterdev.blog/command-line-arguments-anatomy-explained/#flags-and-named-arguments). To be able to pass two flags as `-ab`, instead of `-a -b`, some additional code would be needed.

The `while` loop is a manual way of parsing parameters. In every other language you should use one of the [built-in parsers](https://docs.python.org/3/library/argparse.html) or [available libraries](https://yargs.js.org/), but, well, this is Bash.

An example flag (`-f`) and named parameter (`-p`) are in the template. Just change or copy them to add other params. And do not forget to update the `usage()` afterward.

The important thing here, usually missing when you just take the first google result for Bash arguments parsing, is **throwing an error on an unknown option**. The fact the script received an unknown option means the user wanted it to do something that the script is unable to fulfill. So user expectations and script behavior may be quite different. It’s better to prevent execution altogether before something bad will happen.

There are two alternatives for parsing parameters in Bash. It’s `getopt` and `getopts`. There are [arguments both for and against](https://unix.stackexchange.com/questions/62950/getopt-getopts-or-manual-parsing-what-to-use-when-i-want-to-support-both-shor) using them. I found those tools not best, since by default `getopt` on macOS is [behaving completely differently](https://stackoverflow.com/questions/11777695/why-the-getopt-doesnt-work-well-in-my-mac-os), and `getopts` does not support long parameters (like `--help`).

## Using the template

Just copy-paste it, like most of the code you find on the internet.

Well, actually, it was quite honest advice. With Bash, there is no universal `npm install` equivalent.

After you copy it, you only need to change 4 things:

-   `usage()` text with script description
-   `cleanup()` content
-   parameters in `parse_params()` – leave the `--help` and `--no-color`, but replace the example ones: `-f` and `-p`
-   actual script logic

## Portability

I tested the template on MacOS (with default, archaic Bash 3.2) and several Docker images: Debian, Ubuntu, CentOS, Amazon Linux, Fedora. It works.

Obviously, it won’t work on environments missing Bash, like Alpine Linux. Alpine, as a minimalistic system, uses very lightweight ash (Almquist shell).

You could ask a question if it wouldn’t be better to use [Bourne shell](https://en.wikipedia.org/wiki/Bourne_shell)\-compatible script that will work almost everywhere. The answer, at least in my case, is no. Bash is safer and more powerful (yet still not easy to use), so I can accept the lack of support for a few Linux distros that I rarely have to deal with.

## Further reading

When creating a CLI script, in Bash or any better other language, there are some universal rules. Those resources will guide you on how to make your small scripts and big CLI apps reliable:

-   [Command Line Interface Guidelines](https://clig.dev/)
-   [12 Factor CLI Apps](https://medium.com/@jdxcode/12-factor-cli-apps-dd3c227a0e46)
-   [Command line arguments anatomy explained with examples](https://betterdev.blog/command-line-arguments-anatomy-explained/)

## Closing notes

I’m not the first and not the last to create a Bash script template. One good alternative is [this project](https://github.com/ralish/bash-script-template), although a little bit too big for my everyday needs. After all, I try to keep Bash scripts as small (and rare) as possible.

When writing Bash scripts, use the IDE that supports [ShellCheck](https://github.com/koalaman/shellcheck) linter, like JetBrains IDEs. It will prevent you from doing [a bunch of things](https://github.com/koalaman/shellcheck/blob/master/README.md#user-content-gallery-of-bad-code) that can backfire on you.

My Bash script template is also available as GitHub Gist (under [MIT license](https://gist.github.com/m-radzikowski/d925ac457478db14c2146deadd0020cd)):

If you found any problems with the template, or you think something important is missing – let me know in the comments.

**Update 2020-12-15**

After a lot of comments here, on [Reddit](https://www.reddit.com/r/programming/duplicates/kcxnag/minimal_safe_bash_script_template/), and [HackerNews](https://news.ycombinator.com/item?id=25428621), I did some improvements to the template. See revisions history in [gist](https://gist.github.com/m-radzikowski/53e0b39e9a59a1518990e76c2bff8038).

**Update 2020-12-16**

Link to this post reached the [front page of Hacker News (#9)](https://news.ycombinator.com/front?day=2020-12-15). This was completely unexpected.