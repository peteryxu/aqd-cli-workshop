![Amazon Q Developer header](/images/q-vscode-header.png)

## Diving deeper into Amazon Q CLI

In this lab we are going to build on what you have already learned about Amazon Q CLI's features, looking at some of the more advanced features and capabilities.


**Tools and Trust**

In previous labs (**Task 05**) you looked at **Tools** and how Amazon Q CLI requires you to control how those tools work. We looked at trusting tools as part of the chat session, where you entered **"t"** to trust the tool for the duration of that chat session.

Tool permissions have two possible states:

* **Trusted**: Amazon Q can use the tool without asking for confirmation each time.
* **Per-request**: Amazon Q must ask for your confirmation each time before using the tool.

You can use the **"/tools trust {tool}"** and **"/tools untrust {tool}"** commands to enable and disable trust for a specified tool that is available in your Amazon Q CLI session.

You can also trust all the tools by using the **"/tools trustall"** command.

When you exit your Amazon Q CLI session, all trust is reset back the the defaults. When starting Amazon Q CLI from the terminal, you can configure the level of trust of either all or specific tools by using some command line options.

* **q --trust-all-tools** - this will start your Amazon Q CLI session and trust all the available tools
* **q --trust-tools {tool}** - this will start your Amazon Q CLI session and trust just the specified tools you list

You might want to disable tools but not want to exit your Amazon Q CLI session. We have already covered how you can use **"/tools untrust {tool}"** to reset each tool back to untrusted. You can also use **"/tools reset"** to reset all of the tools.

**Task 15**

Open up a new Amazon Q CLI session and run the following commands:

```
> /tools
```

Review the output. Which tools are trusted and which are not?

Use the **"/tools trust {name}"** command to enable the **"use_aws"** tool. Re-run the **"/tools"** command and review the status. 

Now run **"/tools trustall"** and review the findings. Run **"/tools reset"** and then check again. You should find that the trust levels have been reset.

---

**Model Context Protocol (MCP)**

What is Model Context Protocol (MCP)? MCP is like a USB-C port for your AI applications. Just as USB-C offers a standardized way to connect devices to various accessories, MCP standardizes how your AI apps connect to different data sources and tools.

At its core, MCP follows a client-server architecture where a host application can connect to multiple servers. It has three key components: Host, Client, and Server

* MCP Host represents any AI app (Amazon Q CLI for example) that provides an environment for AI interactions, accesses tools and data, and runs the MCP Client. The host manages the AI model and runs the MCP Client.
* MCP Client within the host and facilitates communication with MCP servers. It's responsible for discovering server capabilities and transmitting messages between the host and servers.
* MCP Servers exposes specific capabilities and provides access to data, like,

![MCP Overview](/images/mcp-overview.png)

> **When is a server not a server?** Although they are called "MCP Servers", you might be thinking that this is a machine running a process or endpoint you need to connect to. MCP Servers can actually be a local process (program, executable) that you run on your local machine, and infact, most MCP Servers as of writing this workshop (May, 2025) run locally. When folk mention MCP Servers, they often mention the method of running those locally as STDIO.

*Native or Container*

I have seen two common ways that people are integrating MCP Servers locally using STDIO: they are either installing/running libraries or executables, or they are running the same libraries/executables but through a container image. Most of the examples (including my original post) showed the first way (running direct libraries). You might be thinking why would you use one method over another. It depends, but when you run MCP Servers locally you need to make sure that you meet all the dependencies. You might encounter some MCP Servers that have a lot of dependencies (libraries, binaries, etc) or maybe dependencies that clash with your setup. In these circumstances then running those MCP Servers in a container is probably a good approach.

Another good reason to run your MCP Servers in a container is to limit its access to your local machine. When I configure my dev tools to use MCP Servers, I am always using the container option, and I have written about this in some blog posts (see additional resources section at the end).

I have added some additional resources at the end of this lab if you want to dive deeper into this topic.

> **SECURITY ALERT!!**
> Be very very careful when exploring MCP Servers. They represent a new attack vector as you are enabling executables to run in your environment. Key security considerations when using MCP:
> 
> * Only install servers from trusted sources
> * Review tool descriptions and annotations before approving
> * Use environment variables for sensitive configuration
> * Keep MCP servers and the Q CLI updated
> * Monitor MCP logs for unexpected activity
>
> The MCP security model in Amazon Q Developer CLI is designed with these principles:
>
> 1. Explicit Permission: Tools require explicit user permission before execution
> 2. Local Execution: MCP servers run locally on your machine
> 3. Isolation: Each MCP server runs as a separate process
> 4. Transparency: Users can see what tools are available and what they do
>

---

*Adding MCP Servers to Amazon Q CLI*

Adding MCP Servers to Amazon Q CLI is super easy, all you need to do is create a configuration file (called **"mcp.json"**). You have a number of ways you can do this, based on your preference. You might want to hand craft configuration files, alternatively you can use the Amazon Q CLI to add them via a number of command arguments.

> For a list of AWS MCP Servers, check out the [AWS MCP Server directory](https://awslabs.github.io/mcp/). You can explore a number of different MCP Servers, and the docs proivide an overview of the features that each MCP Server offers, together with the available Tools. If I scroll a bit further I get the installation details.

MCP Servers exist at the global level (every time you start an Amazon Q CLI session), or at specific project workspaces (they will start just for those projects). The location of the **"mcp.json"** file determines this:

* Global MCP Settings - **"~/.aws/amazonq/mcp.json"**
* Workspace specific MCP Settings - **".amazonq/mcp.json"** (relative to the current directory you are in)

**Task 16a**

We are going to add an MCP Server to our Amazon Q CLI setup. We are going to use the [Promptz MCP Server](https://www.promptz.dev/mcp), which is an online resource to discover and explore prompts created by the community to enhance your Amazon Q workflow. What we want to do is:

- configure this MCP server based on [its documentation here](https://www.promptz.dev/mcp)
- make this MCP server global
- add some environment settings
- keep all other options as default

Exit Amazon Q CLI so that you are at the terminal. We will use both the cli and the hand crafting methods.


**Adding MCP Servers via the cli** 

Introduced in 1.10 of Amazon Q CLI, **"q mcp"**  provides you with tools to help you manage your MCP server settings. When we run this command we can see what it gives us:

```
Usage: qchat mcp [OPTIONS] <COMMAND>

Commands:
  add     Add or replace a configured server
  remove  Remove a server from the MCP configuration
  list    List configured servers
  import  Import a server configuration from another file
  status  Get the status of a configured server
  help    Print this message or the help of the given subcommand(s)
```

We are going to use some of these in this lab. From the command line we can use **"q mcp add"** to add a new MCP Server. Enter the following command which will create our global mcp server configuration:

```
q mcp add --name "promptz.dev/mcp" \
--command "npx" \
--args "-y" \
--args "@promptz/mcp" \
--scope "global" \
--env "PROMPTZ_API_URL = https://retdttpq2ngorbx7f5ht4cr3du.appsync-api.eu-central-1.amazonaws.com/graphql" \
--env "PROMPTZ_API_KEY = da2-45yiufdo5rcflbas7rzd3twble"
```

You can see we have used:

* **--name** to give this mcp server a name in the configuration file
* **--command** to provide it the command to run,
* **--args** to provide the additional arguments for the command (note we could have done this either with a single quote separated by commas, or individual --arg statements as per the above example)
* **--scope** defines the scope for this MCP configuration at the global level
* **--env** provides environment variables 


When you run this command, you should see something like the following:

```
q mcp add --name "promptz.dev/mcp" \
--command "npx" \
--args "-y" \
--args "@promptz/mcp" \
--scope "global" \
--env "PROMPTZ_API_URL = https://retdttpq2ngorbx7f5ht4cr3du.appsync-api.eu-central-1.amazonaws.com/graphql" \
--env "PROMPTZ_API_KEY = da2-45yiufdo5rcflbas7rzd3twble"

📁 Created MCP config in '/Users/ricsue/.aws/amazonq/mcp.json'

To learn more about MCP safety, see https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line-mcp-security.html


✓ Added MCP server 'promptz.dev/mcp' to 🌍 global
```

We can check the configuration by looking at the **mcp.json** file in the **"~/.aws/amazonq"** directory, which we can see looks like the following.

```
{
  "mcpServers": {
    "promptz.dev/mcp": {
      "command": "npx",
      "args": [
        "-y",
        "@promptz/mcp"
      ],
      "env": {
        "PROMPTZ_API_URL": "https://retdttpq2ngorbx7f5ht4cr3du.appsync-api.eu-central-1.amazonaws.com/graphql",
        "PROMPTZ_API_KEY": "da2-45yiufdo5rcflbas7rzd3twble"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

We can check also the status of the MCP Server configuration with the following command:

```
q mcp status --name promptz.dev/mcp
```

which should give you some output like:

```
─────────────
Scope   : 🌍 global
File    : /Users/ricsue/.aws/amazonq/mcp.json
Command : npx
Timeout : 120000 ms
Env Vars: PROMPTZ_API_URL, PROMPTZ_API_KEY
```

It is important to note that this does not load this up and check that its working, this is just checking that the configuration files are all in order. To do that, we need to start Amazon Q CLI. 

As mentioned previously, as you start exploring and integrating MCP Servers is that these are downloading and installing libraries or containerised images. As such, you will need to make sure that you have installed any dependencies. These are typically documented in the MCP Server details.  In this particular case, you will need to make sure I have **uv** and **Python** running, otherwise this is going to fail. Different MCP Servers will have different requirements so make sure you meet them before proceeding. 

Start Amazon Q CLI from the terminal, and review the output. You should see something similar to the following (you might miss it as it is only on thre screen for a few moments):

```
0 of 1 mcp servers initialized. ctrl-c to start chatting now
```

and very shortly after that, you should see the familiar Amazon Q CLI splash screen but with some additional text at the top:

```
✓ promptzdevmcp loaded in 1.29 s
✓ 1 of 1 mcp servers initialized
```

To view which MCP Servers you have installed and running at any time, use the **"/mcp"** command. Here is an example of the output:

```
> /mcp
promptzdevmcp
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
✓ promptzdevmcp loaded in 2.82 s

```

Exit Amazon Q CLI (using **"/q"**), and create a new project directory somewhere on your machine. From this directory open up Amazon Q CLI. From the **">"** prompt, type in the following:

```
> create a new flask app. use prompts from promptz
```

Review the output. You should get something similar to what I had when I ran this:

```
I'll help you create a new Flask application. Let me check for available prompts from promptz.dev
that might be useful for Flask applications.


🛠️  Using tool: list_prompts from mcp server promptzdevmcp
 ⋮
 ● Running list_prompts with the param:
 ⋮  {
 ⋮    "arguments": {
 ⋮      "tags": [
 ⋮        "Flask"
 ⋮      ]
 ⋮    },
 ⋮    "name": "list_prompts"
 ⋮  }
Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:
```

You will notice something - we have been asked to trust this tool. When you configure MCP Servers, you are in effect adding new Tools to your Amazon Q CLI. You can see this by running the following command:

```
/tools
```

which should give you the following output:

```
Tool                              Permission
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
Built-in:
- use_aws                         * trust read-only commands
- execute_bash                    * trust read-only commands
- report_issue                    * trusted
- fs_read                         * trusted
- fs_write                        * not trusted

promptzdevmcp (MCP):
- promptzdevmcp___list_rules      * not trusted
- promptzdevmcp___get_rule        * not trusted
- promptzdevmcp___get_prompt        trusted
- promptzdevmcp___list_prompts      trusted

```

As you add more MCP Servers, each Tool will be listed and you will be able to control whether you trust specific, all, or none of the tools.

> **Tip!** As you work with MCP Servers, using **"/tools"** is a good way to see if they have loaded correctly and are working as expected

---

> **Note!**
> The latest version of Amazon Q CLI load these up in the background. When you do a **"/tools"** you might see something like:
>
> ```
>Servers still loading
> ▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
> - promptzdevmcp
>
> Trusted tools can be run without confirmation
> ```
>
> You will have to wait until these load, so try again after a few seconds to make sure they are all then listed.

---

Back to the prompt - respond **"t"** to trust the tool. It will then being looking through the available prompts that are stored to find a relevant one. When I ran this, it generated the following:

```
I don't see any Flask-specific prompts available. Let me check for more general web development
prompts:


🛠️  Using tool: list_prompts (trusted) from mcp server promptzdevmcp
 ⋮
 ● Running list_prompts with the param:
 ⋮  {
 ⋮    "arguments": {
 ⋮      "tags": [
 ⋮        "Web"
 ⋮      ]
 ⋮    },
 ⋮    "name": "list_prompts"
 ⋮  }
 ⋮
 ● Completed in 0.208s


Let me check for any available prompts without specific tags:


🛠️  Using tool: list_prompts (trusted) from mcp server promptzdevmcp
 ⋮
 ● Running list_prompts with the param:
 ⋮  {
 ⋮    "arguments": {},
 ⋮    "name": "list_prompts"
 ⋮  }
 ⋮
 ● Completed in 0.234s


I found a relevant prompt for creating a Flask application. Let me get the details:


🛠️  Using tool: get_prompt from mcp server promptzdevmcp
 ⋮
 ● Running get_prompt with the param:
 ⋮  {
 ⋮    "arguments": {
 ⋮      "name": "Build a UI for a votingapp"
 ⋮    },
 ⋮    "name": "get_prompt"
 ⋮  }
Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:
```

You can exit this (CTRL + C) or leave it running until it finishes if you want.

Congratulations, you have configured and tested your first MCP Server. 

---

**Adding MCP Servers manually**

**Task 16b**

This is an optional lab which you can skip if you are happy using the cli to configure your MCP Servers. Before proceeding, exit from Amazon Q CLI, and then delete the mcp.json file in the "~/.aws/amazonq" directory.

If you prefer you can generate the **"mcp.json"** files yourself, making sure that you configure them in the specific directory based on whether you want them to be project specific, or global. The process is:

1. Create a **"mcp.json"** file
2. Edit the **"mcp.json"** file to add any MCP Servers you want to add

When you make those changes, the next time you restart Amazon Q CLI, it will try and load up any MCP Servers you have configured.

Make sure you are at the **"~/.aws/amazonq"** directory in yout terminal (the ~ is your home directory)

From this directory create a new file called **"mcp.json"**. Edit that file to add the following:

```
{
  "mcpServers": {
    "promptz.dev/mcp": {
      "command": "npx",
      "args": [
        "-y",
        "@promptz/mcp"
      ],
      "env": {
        "PROMPTZ_API_URL": "https://retdttpq2ngorbx7f5ht4cr3du.appsync-api.eu-central-1.amazonaws.com/graphql",
        "PROMPTZ_API_KEY": "da2-45yiufdo5rcflbas7rzd3twble"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}


```

Save this file. Restart Amazon Q CLI, and you should see that the MCP Server is now available. Use **"/mcp"** to see the status.

---

**Reviewing your MCP Servers**

At any time you can see what MCP Servers you have configured by using the **"q mcp list"** command from the terminal. Using the previous configuration, when we run it from the same directory we see the following output

(the directory on my macbook is "/Users/ricsue/amazon-q-developer-cli/mcp-test", yours will be different)

```
q mcp list

📄 workspace:
  /Users/ricsue/amazon-q-developer-cli/mcp-test/.amazonq/mcp.json
    (empty)

🌍 global:
  /Users/ricsue/.aws/amazonq/mcp.json
    • promptz.dev/mcp npx


```

As you can see we have not defined a local, project workspace MCP Server and so all we see is our global one.

**Task 16c**

Exit from Amazon Q CLI so you are at the terminal. In the current directory (in the example above, mine is "/Users/ricsue/amazon-q-developer-cli/mcp-test/" but yours will be different), create a ".amazonq" directory.

Move the mcp.json file from the "~/.aws/amazonq" directory to your current directory, into the ".amazonq" directory you created in the previous step. Make sure you are **moving** the file and not copying it.

Now we can re-run the command, and we can see that our MCP Server is now a local, project MCP Server rather than a global one.

```
q mcp list

📄 workspace:
  /Users/ricsue/amazon-q-developer-cli/mcp-test/.amazonq/mcp.json
    • promptz.dev/mcp npx

🌍 global:
  /Users/ricsue/.aws/amazonq/mcp.json
    (empty)

```

---

**MCP Prompts**

MCP Servers can provide three types of resources: Tools, Resources, and Prompts. When you are reviewing MCP Server details, they will typically provide documentation that covers which of these they support. Tools are the most common resource MCP Server provide and provide the ability for you to define functions that can be called by your AI coding assistant. Resources in MCP are a way to share data between the server and clients. Unlike tools, which are used for executing actions, resources are used for sharing and synchronizing state (for example, static data like configuration files, dynamic data that changes over time like user data, binary data like an image file, etc). Prompts in MCP enable you to create templates of prompts that allow you to create consistent, reusable prompts for your user interaction.

We have already looked at how Amazon Q CLI supports MCP Tools, but it also supports MCP Prompts with the **"/prompts"** command. This will look at any MCP Servers that are providing Prompts resources, and then list them. You are then able to use these within your Amazon Q CLI sessions, referring to defined prompt templates with the **"@{prompt}"** command.

**Task 17**

Open a new terminal window and create a new directory (for example, "~/projects/mcp-prompts").

From this window, create a file called **"mcp-server.py"** and add the following code. This will generate a simple MCP Server that provides a Tool and a Prompt:

```
import asyncio
import sys

from crawl4ai import *
from mcp.server.fastmcp import Context, FastMCP

# Create an MCP server
mcp = FastMCP("ContextScraper")

@mcp.tool()
async def crawl(url: str, ctx: Context) -> str:
    """crawls a given webpage URL and returns a markdown representation of the webpage content"""

    # Suppress stdout outputs
    dev_null = open("/dev/null", "w")
    sys.stdout = dev_null

    # Crawl the given webpage using Crawl4AI
    async with AsyncWebCrawler() as crawler:
        result = await crawler.arun(url=url)
        return result.markdown

@mcp.prompt()
def create_context_from_url(url: str) -> str:
    """A prompt that takes a URL and uses the crawl tool to add webpages to Q developer context rules"""
    return f'Use the crawl tool to crawl url={url}. Write the webpage content to a markdown file in ".amazonq/rules/" of the current directory. Choose a fitting name for the file'

@mcp.prompt()
def create_new_project() -> str:
    """A prompt that bootstraps a new project repository as per our requirements"""
    return f'Create a new project layout. Create a top level src directory, and in the src directory create subdirectories for templates, models, routes, and static. Add a README.md to the src directory.'
```

After saving this file, create a virtual python environment and install a couple of dependencies:

```
python -m venv .mcp
source .mcp/bin/activate
pip install mcp crawl4ai mcp[cli]
```

After load these, create a new **"mcp.json"** configuration file in a folder called **".amazonq"** in the current directory.

```
mkdir .amazonq
touch .amazonq/mcp.json
```

And then add the following to this file:

```
{
    "mcpServers": {
        "ContextScraper": {
            "command": "uv",
            "args": ["run", "--with", "mcp", "mcp", "run", "mcp-server.py"]
        }
    }
}
```

All we are doing here is providing the mechanism by which to run our MCP Server, so in this case, just using uv to run it with these parameters.

Save this file and then return back to the new directory you created. You should end up with something like this:

```
mcp-prompts/
  ├── .mcp
  ├── .amazonq
  │    └── mcp.json
  └── mcp-server.py
```

Make sure you are not in the ".amazonq" directory. Start an Amazon Q CLI session.

You will see it try and load up the MCP Server we have specified in the mcp.json.

From the **">"** prompt, type **"/prompts"** and hit return. You should see something like the following:

```
> /prompts list

Prompt                    Arguments (* = required)
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
context_scraper (MCP):
- create_context_from_url url*
- create_new_project
```

We can try this out now, from a new **">"** enter the following

```
@create_new_project
```

Which should generate output like the following:

```
> @create_new_project
I'll help you create this project layout with the requested directory structure and README.md file. Let
me do that for you.


🛠️  Using tool: execute_bash
 ⋮
 ● I will run the following shell command:
mkdir -p src/templates src/models src/routes src/static
 ⋮
 ↳ Purpose: Creating the project directory structure with src and its subdirectories


Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:

> t

 ⋮
 ● Completed in 0.29s
```

Check out the results in your current directory. It should have implemented the new project layout.

We can try the other Prompt, which takes input (and URL) and then creates a context file based on searching that web page. (feel free to change the web page for something different)

```
> @create_context_from_url url=https://blog.beachgeek.co.uk/mcp-finch/
```

Once finished, run the **"/context show"** command. 

Do you see this now added to your context? This could be useful when you need to add some new API docs for a library you are working on and want to add them to your context.

---

**Disabling and overriding MCP Server settings**

What if you wanted to temporarily disable an MCP Server, but not delete the configuration file? Taking the **mcp.json** file we have been working with, we can disable it by changing the value of **"disabled"** from False to True. In the following configuration we have disabled it.


```
{
  "mcpServers": {
    "promptz.dev/mcp": {
      "command": "npx",
      "args": [
        "-y",
        "@promptz/mcp"
      ],
      "env": {
        "PROMPTZ_API_URL": "https://retdttpq2ngorbx7f5ht4cr3du.appsync-api.eu-central-1.amazonaws.com/graphql",
        "PROMPTZ_API_KEY": "da2-45yiufdo5rcflbas7rzd3twble"
      },
      "disabled": true,
      "autoApprove": []
    }
  }
}
```

If we make the change at the Global level (~/.aws/amazonq/mcp.json) then this will disable the MCP Server globally. If we start a new Q session in a terminal after making this change, we will see the following:

```
○ promptzdevmcp is disabled

🤖 You are chatting with claude-4-sonnet

```

You can validate that this has not been configured by running **"/tools"** to see that any tools have been added.


There might be times when you want to:

* disable Global MCP Servers for the current project workspace you are in
* enable an MCP Server that has been disabled Globally

You can do this by adding a **mcp.json** configuration in your project workspace, of the MCP Server you want to change. This will **override** the MCP Server status - whatever you configure in your local workspace will preside. You will see a different message when you start Amazon Q CLI though:

```
WARNING: MCP config conflict for promptz.dev/mcp. Using workspace version.
✓ promptzdevmcp loaded in 2.64 s

🤖 You are chatting with claude-4-sonnet
```

This tells you that it detected a conflict and is overiding with the project workspace configuration.

---

**Context Hooks**

Context hooks are a feature in Amazon Q CLI that you can use to automatically inject context into your conversations with Q Developer CLI. Instead of manually adding context with the **"/context"** command, context hooks run commands and include their output as context.

Amazon Q CLI supports two types of context hooks:

* **Conversation start hooks** - Run once at the beginning of a conversation. Their output is added to the conversation context and persists throughout the session.
* **Per-prompt hooks** - Run with each user message. Their output is added only to the current prompt.

You can view your current context hooks using the "/context hooks" command in the Amazon Q CLI session which will give you output similar to the following:

```
> /context hooks

🌍 global:
    Conversation Start:
      <none>
    Per Prompt:
      <none>

👤 profile (default):
    Conversation Start:
      <none>
    Per Prompt:
      <none>

Use /context hooks help to manage hooks.
```

As I do not have any configured, nothing is showing.

---

**Task 18**

Lets add a new context hook that is invoked every time we prompt. Exit Amazon Q CLI and create a new file called **"MYHOOK.md"** in the current directory, and add this to the file:

```
talk like a pirate
make jokes
```

We will now add this as a context hook to see if we can make our Amazon Q CLI talk like a pirate. We use the **"/context hooks"** command, using the "add" to add a context hook. We give it a name (in our case, pirate) and then define which of the two kinds of hooks we want to use (in this case, we want to run this every time we prompt). Finally we run the command, so here just echoing the markdown doc with the instructions.

```
> /context hooks add pirate --trigger per_prompt --command "cat MYHOOK.md"
```
You should see output similar to the following:

```
> /context hooks add pirate --trigger per_prompt --command "cat MYHOOK.md"

Added profile hook 'pirate'.
```

Lets test this out now. Enter the following prompt:

```
> create a simple flask app that returns a json date string
```

Review the output. What happens? 

Run the **"/context hooks"** command. What has changed.

---

You can add context hooks, you can add them at the local or global level, using the **"--global"** to make the hook global, or setting it as a local, profile context hook without using that argument. 

Like with context, when you add context hooks these are added to the **"context.json"** file that we looked at earlier. Here is what mine looks like after adding a context hook that reads a file called "todo-hook.md" as part of every prompt when it is run.

```
{
  "paths": [
    "project-standards.md"
  ],
  "hooks": {
    "todo": {
      "trigger": "per_prompt",
      "type": "inline",
      "disabled": false,
      "timeout_ms": 30000,
      "max_output_size": 10240,
      "cache_ttl_seconds": 0,
      "command": "cat MYHOOK.md"
    }
  }
}
```

You can check what context hooks you have within your Amazon Q CLI session by running the **"/context show --expand"** command, which will provide you with the following summary at the top of the page.

```
🌍 global:
    .amazonq/rules/**/*.md (2 matches)
    README.md (1 match)
    AmazonQ.md

    🔧 Hooks:
    On Session Start:
      <none>
    Per User Message:
      <none>

👤 profile (default):
    <none>

    🔧 Hooks:
    On Session Start:
      <none>
    Per User Message:
      <none>
```

(It will also dump out any context files you have added, so if you have defined those you will need to scroll upwards to see the above summary)


---

**Managing conversations (Chats) across Amazon Q CLI sessions**

Whilst you are working within your Amazon Q CLI session, you might need to stop and exit. However, you want to then return and then carry on working where you left off. Don't worry, Amazon Q CLI has you covered.

*Starting with --resume*

The first thing you can do is to use the **"--resume"** option when starting Amazon Q CLI. When you do this, it will look at the current directory you are in, and then look to carry on where you left off. You can see here in the screen that I start Amazon Q CLI with the "--resume" option, and it then looks at the current directory, sees that we were working on something and then is ready to continue.

```
 q chat --resume
✓ promptzdevmcp loaded in 2.83 s
✓ 1 of 1 mcp servers initialized.
Picking up where we left off...

╭─────────────────────────────── Did you know? ────────────────────────────────╮
│                                                                              │
│     You can resume the last conversation from your current directory by      │
│                        launching with q chat --resume                        │
│                                                                              │
╰──────────────────────────────────────────────────────────────────────────────╯

/help all commands  •  ctrl + j new lines  •  ctrl + s fuzzy search
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
We've briefly discussed the Model Context Protocol (MCP) in Amazon Q, which is an open protocol that allows
applications to provide context to LLMs and extend capabilities through additional tools.

```

*Saving and Loading conversations*

As you are working, there might be times when want to provide a quick summary of the current conversation you have been having within your Amazon Q CLI. Perhaps it has been a long session and you want to retain all the prompts and information so you can review at a later time.

You can now do this, using the **"/save {name}"** command, providing a name of the conversation you want to save. It will then generate a json document in the current directory.

```
> /save project

✔ Exported conversation state to project.json
```

You can also load up a conversation from an Amazon Q CLI session using the **"/load {name}"** and it will then load up the conversation, leaving you ready to carry on where you left off.

**Task 19**

From your Amazon Q CLI session, at the **">"** prompt try saving your current conversation:

```
> /save project-customer-survey
```

After it has saved, open up a new terminal and review the output.

Now close your Amazon Q CLI session and start it again. From the **">"** prompt, lets reload that conversation:

```
> /load project-customer-survey.json
```

After it loads, ask it something about the customer survey application. 

---

### Supporting Resources

Some additional reading material that dives deeper into this topic if you want to explore:

* [Configuring Model Context Protocol (MCP) with Amazon Q CLI](https://dev.to/aws/configuring-model-context-protocol-mcp-with-amazon-q-cli-e80)

* [Running Model Context Protocol (MCP) Servers on containers using Finch](https://dev.to/aws/running-model-context-protocol-mcp-servers-on-containers-using-finch-kj8)

* [Q-Bits: Enhance Amazon Q Developer CLI's Context Using MCP for Web Crawling](https://community.aws/content/2ulohBgBuogjKVEKTjGff71oOY3/q-bits-enhance-amazon-q-developer-cli-s-context-using-mcp-for-web-crawling) 

* [Running Model Context Protocol (MCP) Servers on containers using Finch](https://dev.to/aws/running-model-context-protocol-mcp-servers-on-containers-using-finch-kj8)

* [Launch ECS Local Container Endpoints](https://gist.github.com/nathanpeck/6b03e647e79455a460551f8f295c7f9e)


### Completed!

Now that we have an idea of how Amazon Q CLI works and explored some of the features, we can take a look at some of the use cases in [Exploring Use Cases](03a-use-cases.md)


