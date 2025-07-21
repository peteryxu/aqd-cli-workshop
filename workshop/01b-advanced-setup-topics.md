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

**Enable experimental/beta mode in Amazon Q CLI**

From time to time there will be features released that are only available if you enable Amazon Q CLI's beta/experimental model. To enable this, we can use the **"q settings"** command. Make sure you close any Amazon Q CLI sessions you have open for these settings to take effect.

```
q settings app.beta true
```

To verify, when you run "q settings all" you should see the following in the list.
```
app.beta = true
```

Once you restart your Amazon Q CLI session, you will now be able to try out any experimental features. Typically those features will have their own switches. For example, at the time of writing, to enable the **["/knowledge"](https://github.com/aws/amazon-q-developer-cli/blob/main/docs/knowledge-management.md)** experimental feature, you would run the following command:

```
q settings chat.enableKnowledge true
```

> **Learn more about "/knowledge" experimental feature by reading [Manage context rot by exploring new experimental features in Amazon Q CLI](https://dev.to/aws/manage-context-rot-by-exploring-new-experimental-features-in-amazon-q-cli-10ki)

---

**Available settings in Amazon Q CLI**

As Amazon Q CLI is an open source project, I was able to use it to review the source files and create a list of available settings. The following list is what was produced. I have not had a chance to review them all, and they will change over time.

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

---

**Enabling SigV4 Authentication**

With version 1.12.6 of the Amazon Q CLI, you can now enable experimental SigV4 by setting an environmnet variable before starting your Amazon Q Chat session.

```
export AMAZON_Q_SIGV4=1
```

This will enable SigV4 support when accessing AWS endpoints that you might want to use.

---

### Supporting Resources

Some additional reading material that dives deeper into this topic if you want to explore:

* [The essential guide to installing Amazon Q Developer CLI on Windows](https://dev.to/aws/the-essential-guide-to-installing-amazon-q-developer-cli-on-windows-lmh)
* [The essential guide to installing Amazon Q Developer CLI on Linux (headless and desktop)](https://dev.to/aws/the-essential-guide-to-installing-amazon-q-developer-cli-on-linux-headless-and-desktop-3bo7)


---


### Completed!

Now that you have set everything up, we can proceed to the next step which is [exploring how to get started with Amazon Q CLI](02a-getting-started.md)
