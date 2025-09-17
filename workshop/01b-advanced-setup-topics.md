![Amazon Q Developer header](/images/q-vscode-header.png)

## Diving deeper into Amazon Q CLI setup

This optional section provides a deeper look at how Amazon Q CLI is setup.

### Exploring Amazon Q CLI configuration (Optional)

This is an optional section for those of you who want to dive deeper at Amazon Q CLI, looking at how you can tune and configure it to your preferences. Feel free to skip this section if you want.

**Directory structure**

You can launch Amazon Q CLI from a terminal window, the current directory is called the **"Project Workspace directory"**. It will look for specific configuration files in this directory, in the **.amazonq/"** directory, if it exists.

In addition to this, the Amazon Q CLI **Global directory"** is always in the **"~/.aws/amazonq"** directory, where it also looks for specific configuration files.

To summarise.

* Global Amazon Q CLI directory - this is **"~/.aws/amazonq"** and is used by Amazon Q CLI to read in global configuration settings for context, profiles, and Model Context Protocol (MCP) settings
* Project Workspace directory - this is **".amazonq"** directory in the current directory where Amazon Q CLI was launched, and is used by Amazon Q CLI to read in project specific configuration files

Don't worry too much at this stage though, you do not need to do anything yet. I am just providing you with this background info so you understand how you can tailor and configure Amazon Q CLI to provide the best output. We will see how later in this workshop.

**Logging and debugging options**

You can view logs for Amazon Q CLI by acessing the following directories in a new terminal.

* macOS: $TMPDIR/qlog/
* Linux: ~/.local/share/amazonq/logs/
* Windows: ~/.local/share/amazonq/logs/

You can configure the verbository of the logs by setting an environment variable, Q_LOG_LEVEL.

```
export Q_LOG_LEVEL=debug
q 
```

This can be set to the following levels:

```
* error: Only error messages (default)
* warn: Warning and error messages
* info: Informational, warning, and error messages
* debug: Debug, informational, warning, and error messages
* trace: All messages including detailed trace information
```

You can get realtime logging information from Amazon Q CLI by running the following command in a terminal window:

```
q debug log
```

as you start using Amazon Q CLI, depending on the logging level set, you will now see realtime logging. This is useful if you are trying to troubleshoot issues.

You can view other options by checking out "q debug help".

---

**Getting diagnostic info from your Amazon Q CLI installation**

A very handy option when you have installed Amazon Q CLI is the following command:

```
q diagnostic
```

Which provides a summarised output of your installation setup. Your output will be different from mine, but this is what I got when I ran this:

```
[q-details]
version = "1.13.2"
hash = "e44ee53d76813a5ef107dcd0d6181b089xxxxxx"
date = "2025-08-08T20:32:47.811878Z (22h ago)"
variant = "full"

[system-info]
os = "macOS 15.6.0 (24G84)"
chip = "Apple M1 Pro"
total-cores = 10
memory = "32.00 GB"

[environment]
cwd = "/Users/USER/amazon-q-developer-cli/workshop"
cli-path = "/Users/USER/amazon-q-developer-cli/workshop"
os = "Mac"
shell-path = "/bin/zsh"
shell-version = "5.9"
terminal = "iTerm 2"
install-method = "brew"

[env-vars]
PATH = "{your path}"
QTERM_SESSION_ID = "xxxxxxxx"
Q_SET_PARENT_CHECK = "1"
Q_TERM = "1.13.1"
SHELL = "/bin/zsh"
TERM = "xterm-256color"
__CFBundleIdentifier = "com.googlecode.iterm2"
```


---

**Settings**

Amazon Q CLI allows you to set a number of different configuration parameters so that you can tailor how it works. You can configure these via Amazon Q CLI itself, using the **"q settings"** command, or by directly updating the configuration file (which we will cover below).

For example, if I wanted to change the default model that Amazon Q CLI uses, I can use the following command from the terminal.

```
q settings chat.defaultModel claude-4-sonnet
```

Some useful / interesting settings you can set

* **q settings app.disableAutoupdates true** - this will disable the auto update for Amazon Q CLI
* **q settings chat.enableThinking true** - this will enable Thinking mode in Amazon Q CLI, which might provide you better results under some use cases
* **q settings chat.greeting.enabled false** - disables the Amazon Q ascii banner
* **q settings chat.enableHistoryHints true** - enables inline prompt hints to be enabled, as these are disabled by default. When using chat, inline hints appear when beginning to type a prompt. First if / is typed, gives relevant command hints, otherwise looks through prompt history and display text in grey "ghost text" as a hint which can be accepted by pressing the right arrow key. 

You can also open up the settings file using the command **"q settings open"** which will open up the settings file in your default editor. For example, on my MacOS, these settings are written to the local **"~/Library/Application Support/amazon-q"** directory as "settings.json" - this is what mine looks like:

```
{
  "chat.defaultModel": "claude-4-sonnet",
  "chat.editMode": "vi",
  "mcp.loadedBefore": true
}
```

On Linux systems they will be localed in **"/home/{username}/.local/share/amazon-q"** (where "{username}" is the logged in username).



You can view your current settings, using the command **q settings all**:

```
q settings all

chat.defaultModel = "claude-4-sonnet"
chat.editMode = "vi"
mcp.loadedBefore = true
```

You can view the settings of individual settings using the format **q settings {setting}**, for example:

```
q setting chat.Mode
```


You can view the various options by using the "help" option when running this:

```
q settings help
```

---

### Enable experimental features in Amazon Q CLI

From time to time there will be features released that are initially only available if you enable them using Amazon Q CLI's beta/experimental mode. You can easily do this from within your Amazon Q CLI session by using the **"/experiment"** command, which will then allow you to toggle any of the experimental features that are currently available.

When you enter this command, you will see the following (you might see a different set of experimental features if you are using a later version of Amazon Q CLI)

```
> /experiment

⚠ Experimental features may be changed or removed at any time

? Select an experiment to toggle ›
❯ Knowledge          [ON]  - Enables persistent context storage and retrieval across chat sessions (/knowledge)
  Thinking           [OFF] - Enables complex reasoning with step-by-step thought processes
  Tangent Mode       [OFF] - Enables entering into a temporary mode for sending isolated conversations (/tangent)
  Todo Lists         [OFF] - Enables Q to create todo lists that can be viewed and managed using /todos

>
```


You can use the **UP** and **DOWN** arrow keys and then press **SPACE** to toggle the status of the experimental feature between ON or OFF. As you can see above, I have enabled the "Knowledge" feature, but the other remain disabled.

> What this feature is doing is simplifying configuration to your Amazon Q CLI settings file which we have covered above. As you toggle these different experimental features you will see that they are updated in your settings.json file.

---

**Available settings in Amazon Q CLI**

As Amazon Q CLI is an open source project, I was able to use it to review the source files and create a list of available settings. The following list is what was produced. I have not had a chance to review them all, and they will change over time.

* chat.defaultAgent (agent) - Allows you to set the default agent when starting Amazon Q CLI. The agent refers to the name of the agent you define when you run the "q agent create" command.
* chat.enableNotifications (boolean) - Enable notifications for chat (default: false)
* chat.greeting.enabled (boolean) - Show greeting message when starting chat (default: true)
* chat.editMode (string) - Set the edit mode for the chat interface
* chat.skimCommandKey (string) - Configure the command key for skim in chat (default is ctrl + s)
* api.timeout (integer, millesends) - 300000 (5 minutes) is the default timeout when calling backend Amazon Q services
* ssh.remote-prompt (string) - Configure remote prompt behavior (default: "ask")
* ssh.remote-prompt.timeout (integer) - Set timeout for remote prompt in milliseconds (default: 2000)
* telemetry.enabled (boolean) - Enable or disable telemetry (default: true)
* shell-integrations.enabled (boolean) - Enable shell integrations (default:true)
* shell-integrations.immediateLogin (boolean) - Prompt for immediate login in shell integrations
* dotfiles.enabled (boolean) - Enable dotfiles management (default: true)
* mcp.loadedBefore (boolean) - Track if Model Context Protocol has been loaded before
* mcp.initTimeout (integer, millesends) - 5000 (5 seconds) is the default timeout to wait for MCP Servers in interactive mode
* mcp.noInteractiveTimeout (integer, millesends) - 30000 (30 seconds) is the default timeout to wait for MCP Servers in interactive mode
* chat.enableHistoryHints - enables you to disable the prompt/command suggestions when you are using Amazon Q CLI (defailts to true) 

---

**Enabling SigV4 Authentication**

With version 1.12.6 of the Amazon Q CLI, you can now enable experimental SigV4 by setting an environmnet variable before starting your Amazon Q Chat session.

```
export AMAZON_Q_SIGV4=1
```

This will enable SigV4 support when accessing AWS endpoints that you might want to use.

---

**Recording your Amazon Q CLI sessions**

There might be times when you want to log the history of your Amazon Q CLI sessions. This can be done using some simple bash/zsh magic. Edit your **".zshrc"** or **".bashrc"** files to add the following function:

```
function qlog() {
  timestamp=$(date +"%Y%m%d_%H%M%S")
  logfile="$HOME/q_chat_logs/q_chat_$timestamp.txt"
  mkdir -p $HOME/q_chat_logs
  script -q /dev/null $(which q) chat | tee $logfile
}
```

This will create a new command (qlog) which wraps up Amazon Q CLI, and generates output into the defined directory (in this case, a directory called "q_chat_logs" in your home directory).

---

**Alerting when Amazon Q CLI completes a task**

As you start using Amazon Q CLI to do more complex tasks, you may run into the situation where you leave it running in a terminal, but then have to periodically check in to see if its finished.

To make this task more effecient, you can set an alert so that when Amazon Q CLI has completed its task, it will use your terminal alert to notify you. To enable this, you just need to run the following command from the terminal.

```
q settings chat.enableNotifications true
```

When a task finishes, your terminal will now notify you. Depending on the terminal you are using, you can configure your alert to let you know when to come back to your Amazon Q CLI session.

If you want to disable this at any time, just run the same command but use "false"

---

### Supporting Resources

Some additional reading material that dives deeper into this topic if you want to explore:

* [The essential guide to installing Amazon Q Developer CLI on Windows](https://dev.to/aws/the-essential-guide-to-installing-amazon-q-developer-cli-on-windows-lmh)
* [The essential guide to installing Amazon Q Developer CLI on Linux (headless and desktop)](https://dev.to/aws/the-essential-guide-to-installing-amazon-q-developer-cli-on-linux-headless-and-desktop-3bo7)
* Learn more about "/knowledge" experimental feature by reading [Manage context rot by exploring new experimental features in Amazon Q CLI](https://dev.to/aws/manage-context-rot-by-exploring-new-experimental-features-in-amazon-q-cli-10ki)


---


### Completed!

Now that you have set everything up, we can proceed to the next step which is [exploring how to get started with Amazon Q CLI](02a-getting-started.md)
