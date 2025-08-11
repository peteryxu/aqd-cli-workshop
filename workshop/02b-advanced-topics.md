![Amazon Q Developer header](/images/q-vscode-header.png)

## Diving deeper into Amazon Q CLI

In this lab we are going to build on what you have already learned about Amazon Q CLI's features, looking at some of the more advanced features and capabilities.

**Custom Agents**

In the previous labs we referenced **custom agents**. These allow you to create a set of differnt "personas" where you group various related configuration and supporting resources to optimise your Amazon Q CLI session. 

For example, imagine that you are doing some front end work, some backend work, and maybe also some devops work. For each of these activities you might have a different set of resources (supporting files, images, code) and tools (Model Context Protocol, MCP which we will look at in a bit) that are optimised for these tasks. To optimise our sessions, and being mindful about context engineering good practices, we want to make sure that we only provide what is needed.

In previous versions of Amazon Q CLI, you would define these as **Profiles**, and whilst this was helpful it also had some limitations. **Custom agents** improved on that by allowing you to simplify everything you need to get started quickly. Now we can define a set of resources and tools (with permissions) for the work we are going to do, and we can share those configurations easily too.

> To read more about some of the use cases, check out [Benefits of using custom agents](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line-custom-agents-overview.html?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el#custom-agent-benefits) in the official documentation pages.

---

**Model Context Protocol (MCP)**

Before we dive into custom agents we need to talk about Model Context Protocol (MCP). MCP is like a USB-C port for your AI applications. Just as USB-C offers a standardized way to connect devices to various accessories, MCP standardizes how your AI apps connect to different data sources and tools.

At its core, MCP follows a client-server architecture where a host application can connect to multiple servers. It has three key components: Host, Client, and Server

* MCP Host represents any AI app (Amazon Q CLI for example) that provides an environment for AI interactions, accesses tools and data, and runs the MCP Client. The host manages the AI model and runs the MCP Client.
* MCP Client within the host and facilitates communication with MCP servers. It's responsible for discovering server capabilities and transmitting messages between the host and servers.
* MCP Servers exposes specific capabilities and provides access to data, like,

![MCP Overview](/images/mcp-response-diagram.png)

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

Later on in this lab we are going to show you how to integrate MCP Servers into your Amazon Q CLI sessions. Before that, we need to look at the mechanism that enables this, custom agents.

---

**Creating your first Custom agent**

Custom agents provide a way to customize Amazon Q CLI behavior by defining specific configurations for different use cases. Each custom agent is defined by a JSON configuration file that specifies which tools the agent can access, what permissions it has, and what context it should include.

In this lab we are going to explore custom agents so that you get comfortable with how they work and can start using them in the activities you do.

**Important:** Custom agent management primarily involves creating and editing configuration files. While some commands are available during chat sessions, switching between custom agents requires starting a new chat session with "q chat --agent [name]".

**Project vs Global custom agents**

When you create custom agents you have the choice of creating global or project custom agents.

* Global custom agents are available across all projects and directories on your system. JSON configuration for global agents are located at **"~/.aws/amazonq/cli-agents/{agent-name}.json"**

* Project-level custom agents are available only within the specific project directory and its subdirectories. JSON configuration for project level custom agents are in the current directory you are in, in the **".amazonq/cli-agents/{agent-name}.json"**

If you want to keep custom agents within your project workspace, then these will be shared across all developers who check out that project (assuming you are sharing this via something like git).

What if you have local, project custom agents and global custom agents? When Amazon Q CLI looks for an custom agent, it follows a specific precedence order: First it will look for **Local custom agents first**, checking for custom agents in the current working directory. Next it will look for **Global custom agents**, and fall back to custom agents in your home directory. If it fails to find any, it will use the **Built-in default** custom agent.

If both local and global directories contain custom agents with the same name, the local custom agent takes precedence. Amazon Q Developer CLI will display a warning message when this occurs:

```
WARNING: Agent conflict for my-agent. Using workspace version.
```

---

**Default Agent**

When you start Amazon Q CLI, it uses a default agent configuration that includes basic tools and permissions. This default agent is suitable for general use cases but may not be optimized for specific tasks. 

**Task-01**

This default agent does not have a json configuration file, but you can see it when you run the **"/agent list"** command. We access custom agents via the **"/agent"** command, and the first command we are going to look at is the command to list all available agents.

Run the following command from your Amazon Q CLI session:

```
> /agent list

* q_cli_default
```

When you start your Amazon Q CLI session without an **"--agent"** argument, you will use the default custom agent. You can change this behaviour by configuring a setting, selecting the custom agent you want to select as your default option when starting Amazon Q CLI. We will look at that later in this lab.

You can see all the available commands for **"/agent"** by using the **"--help"** argument.

Run the following command from your Amazon Q CLI session:

```
> /agent help

Agents allow you to organize and manage different sets of context files for different projects or tasks.

Notes
• Launch q chat with a specific agent with --agent
• Construct an agent under ~/.aws/amazonq/cli-agents/ (accessible globally) or cwd/.aws/amazonq/cli-agents (accessible in workspace)
• See example config under global directory
• Set default agent to assume with settings by running "q settings chat.defaultAgent agent_name"
• Each agent maintains its own set of context and customizations

Manage agents

Usage: /agent <COMMAND>

Commands:
  list         List all available agents
  create       Create a new agent with the specified name
  schema       Show agent config schema
  set-default  Define a default agent to use when q chat launches
  help         Print this message or the help of the given subcommand(s)

Options:
  -h, --help
          Print help (see a summary with '-h')
```

We have seen the first of these, and we will now look at the others.

---

**Agent Schema**

You might be wondering what the json configuration file looks like for defining a custom agent. Well wonder no more, as we can see what the schema is by typing **"/agent schema"** into your Amazon Q CLI session. It will display the complete schema for configuring a custom agent.

However, you typically do not need to create json configuration files with all the items within that schema. When you are beginning to define and work with custom agents, you should start small and then build up from there.

Before we proceed and start creating our first custom agents, let me walk you through some of the configuration items that you can define with your JSON file.

* **name** - this is a required field, and defines the name of your custom agent
* **description** - (optional) allows you to provide a description for your custom agent
* **mcpServers** - (optional) is where you configure MCP Servers for your custom agent
* **tools** - (optional) allows you to define what tools are available in your Amazon Q CLI session when using this custom agent
* **toolsAlias** - (optional) provides a mechanism to fix any tool name clashes you have when using multiple MCP Servers that provide tools
* **allowedTools** - (optional) lets you set the automatically trusted tools
* **resources** - (optional) is where you add additional context files in the form of markdown documents
* **hooks** - (optional) lets you define commands to run either once for your session or every prompt - we will explore these later in this lab
* **toolsSettings** - (optional) provides the ability to pass in configuration details for tools that need them
* **useLegacyMcpJson** - (optional) is a legacy switch that is enabled if this parameter is not set, and loads up MCP Server configuration from the deprecated mcp.json file

As you can see, you can create a very sparse JSON configuration for your custom agent, just defining a name. I am not sure it would do a lot of use though :O

You can check out the reference documentation about the custom agent configuration options by checking out [Configuration reference](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line-custom-agents-configuration.html?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el) from the official documentation pages.


---

**Creating Agents**

Ok so now we have all the theory, lets create a custom agents. We want to create the following:

* **"python developer"** - a custom agent that looks for resources (all the markdown files in the steering/* directory) that provide specific guidance for writing code in Python.

We will add to this configuration in subseqent labs showing you how you can add additional options to your custom agent. For now we will just create the custom agent and add **Resources** as the first items we want these custom agents to have.

**Task-02**

We are going to create and then launch two custom agents. From an Amazon Q CLI session, at the **">"** prompt, type the following:

```
> /agent create --name python-developer
```

This will bring up an editor (it will use the default if you are using Linux/Mac will be vim, remember from previous labs you can customise this by setting the EDITOR environment variable). It will look like the following:

```
{
  "$schema": "https://raw.githubusercontent.com/aws/amazon-q-developer-cli/refs/heads/main/schemas/agent-v1.json",
  "name": "python-developer",
  "description": "",
  "prompt": null,
  "mcpServers": {},
  "tools": [
    "*"
  ],
  "toolAliases": {},
  "allowedTools": [
    "fs_read"
  ],
  "resources": [
    "file://AmazonQ.md",
    "file://README.md",
    "file://.amazonq/rules/**/*.md"
  ],
  "hooks": {},
  "toolsSettings": {},
  "useLegacyMcpJson": true
}
```

Edit the file and change it so that it looks like the following:

```
{
  "$schema": "https://raw.githubusercontent.com/aws/amazon-q-developer-cli/refs/heads/main/schemas/agent-v1.json",
  "name": "python-developer",
  "mcpServers": {},
  "tools": [],
  "toolAliases": {},
  "allowedTools": [],
  "resources": [
    "file://steering/*.md"
  ],
  "hooks": {},
  "toolsSettings": {}
}
```

You can see we have adjusted the **"resources"** configuration item to match our requirements, i.e.. load up any markdown files in the steering directory.

Save and exit the file (:wq!)

Congratulations, you have just created your first custom agent. You can check this has worked by running the **"/agent list"** command.

```
/agent list
```

Which should produce output similar to the following:

```
* q_cli_default
  python-developer
```

Exit your Amazon Q CLI session. In the current directory, create a sub-directory called "steering" and then a file called "python-dev.md". You can [copy the contents of the file in the resources directory](/resources/python-dev.md) into this file and then save it.

Restart Amazon Q CLI, but this time, use the following arguments:

```
q chat --agent python-developer
```

When Amazon Q CLI starts, you should notice that your prompt now has **"[python-developer]"** as a prefix. This is a visual cue to remind you which custom agent you currently have loaded. You can also re-run the **"/agent list"** command and you will see that the current custom agent is highlighted with an asterix.

We can see what context is available in our session by running the **"/context show"** command:

```
/context show
```

You should see something like the following:

```
[python-developer] > /context show

👤 Agent (python-developer):
    steering/*.md (1 match)

1 matched file in use:
👤 /Users/ricsue/amazon-q-developer-cli/workshop/steering/python-dev.md (~590 tkns)

Total: ~590 tokens

```

As you can see, this has been picked up by your Amazon Q CLI session and is now part of your session context. 

---

**Configuring custom agents Tools and Trust**

**Task-03**

In previous labs you did earlier you looked at what tools are within Amazon Q CLI, and how to trust and untrust them. In the default custom agent (q_cli_default), all the available built in tools within Aamzon Q CLI are available. When we create a custom agent, we can configure both which tools are available, and the default permission we want to assign. In this lab we are going to explore this.

Using our freshly minted custom agent, we can look at what tools are available for us to use within our custom agent session. Run the **"/tools"** commands to see what tools are configured:

```
> /tools
```

You will notice that you have no tools available:

```
[python-developer] > /tools

Tool       Permission
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
Built-in:

```

This means your Amazon Q CLI session will not be able to understand the world around it (read and write files, execute commands, run the AWS cli, and more). There might be some use cases where this might be useful to you, but for our purposes we need it to be able to read/write files and execute commands.

Lets change this. First we need to find out where our JSON configuration file is, lets go on a short diversion...

When we created our custom agent, we used the command **"/create agent --name python-developer"**. When you create custom agents in this format, you are creating global custom agents. This means that the JSON configuration files are located in the **"~.aws/amazonq/cli-agents"** directory. 

Open up another terminal and navigate to this directory. When you do a directory listing you should see a file called **"python-developer.json"**. Open this in an editor, and you should see the same file you had above. We want to edit it to change the tools available for us to use.

We define which tools we want to make available by setting the **"tools"** configuration section. Amazon Q CLI has a number of different tools it comes with:

* execute_bash - allows it to run bash commands
* fs_read - read files from the file system
* fs_write - write files to the file system
* report_issue - allows you to generate an issue in the GitHub project
* use_aws - allows you to use the AWS CLI to run commands

We can define whcih specific tools by adding these into the configuration item like:

* "tools": [ "fs_read","fs_write","use_aws],

You can also use wildcards

* "tools": ["*"]

You can also use the special denominator "@builtin" to refer to all the builtin tools within Amazon Q CLI

* "tools": ["@builtin"]

We will come back to this later when we look at configuring MCP Servers, as if you connect to an MCP Server that exposes Tools then you will configure those tools here too using **"@server"**. 

Lets update our configuration file to allow all the built in commands for now.

```
{
  "$schema": "https://raw.githubusercontent.com/aws/amazon-q-developer-cli/refs/heads/main/schemas/agent-v1.json",
  "name": "python-developer",
  "mcpServers": {},
  "tools": ["@builtin"],
  "toolAliases": {},
  "allowedTools": [],
  "resources": [
    "file://steering/python-dev.md"
  ],
  "hooks": {},
  "toolsSettings": {}
}
```

Save the file, and now restart Amazon Q CLI with the same command to use the new custom agent, 

```
q chat --agent python-developer
```

Now re-run the **"/tools"** command. This time you should see all the available tools.

```
[python-developer] > /tools

Tool              Permission
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
Built-in:
- execute_bash    * trust read-only commands
- fs_read         * trusted
- fs_write        * not trusted
- report_issue    * trusted
- use_aws         * trust read-only commands

```

So all good, we now know how to configure tools. That said, we have the default permissions and maybe we want to set different default permissions for different custom agents. Lets see how we can do that.

Edit the custom agent JSON file again, and this time make edits as follows:

```
{
  "$schema": "https://raw.githubusercontent.com/aws/amazon-q-developer-cli/refs/heads/main/schemas/agent-v1.json",
  "name": "python-developer",
  "mcpServers": {},
  "tools": ["@builtin"],
  "toolAliases": {},
  "allowedTools": ["fs_read","fs_write","use-aws"],
  "resources": [
    "file://steering/python-dev.md"
  ],
  "hooks": {},
  "toolsSettings": {}
}
```

You can see we have changed the **"allowedTools"** and defined the tools we want to automatically trust. If you save this file, and then restart your Amazon Q CLI session:

```
q chat --agent python-developer
```

Now re-run the **"/tools"** command. This time you should see that the permissions of the tools has changed.

```
[python-developer] > /tools


Tool              Permission
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
Built-in:
- execute_bash    * trust read-only commands
- fs_read         * trusted
- fs_write        * trusted
- report_issue    * trusted
- use_aws         * trusted
```

We have just scratched the surface of how you can customise the tools and permissions, making it easy to create these custom agents and then share these with your developers.

We will come back to this again once we take a look at MCP Servers later in this tutorial.

---

**Setting a default custom agent**

When you start your Amazon Q CLI session, it will start the session using the default custom agent. If you create a number of different custom agents, you may decided that you want to make that the default one so that you don't always have to start your Amaozn Q CLI session using the "--agent {name}" command.

You can change the default custom agent by using the **"/agent set-default"** command. Lets take a look at that next.

**Task-04**

Exit and restart your Amazon Q CLI session without any arguments so you are starting using the default custom agent. From the **">"** prompt, enter the following:

```
/agent set-default -n python-developer
```

You should see something like the following:

```
[python-developer] > /agent set-default -n python-developer

✓ Default agent set to 'python-developer'. This will take effect the next time q chat is launched.
```

No exit your Amazon Q CLI session and restart it - again without using any arguments. What happens?

When you use this command, it writes into the Amazon Q CLI settings file. You can see this by running the following command from the terminal

```
q settings open
```

You will see this line appear in your config file:

```
chat.defaultAgent	"python-developer"
```

---

**Configuring context hooks in your custom agent**

Context hooks are a feature in Amazon Q CLI that you can use to automatically inject context into your conversations with Q Developer. Context hooks run commands and include their output as context. In previous versions of Amazon Q CLI, you configured context hooks using a command ("/hooks"), but these are now deprecated and we configure context hooks within custom agents.

Amazon Q CLI supports two types of context hooks:

* **Conversation start hooks** - Run once at the beginning of a conversation. Their output is added to the conversation context and persists throughout the session.
* **Per-prompt hooks** - Run with each user message. Their output is added only to the current prompt.

> Check out [Behavior and limitations](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line-context-hooks.html?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el#command-line-context-hooks-behavior) to understand some of the constraints when creating hooks

You can view any available context hooks by using the command **"/hooks"** within your Amazon Q CLI session. 

```
[python-developer] > /hooks

No hooks are configured.
```

As you can see, we do not currently have any setup so lets change that.

---

**Task-05**

Lets add a new context hook that is invoked every time we prompt. We are going to have some fun and create a per prompt hook that will make Amazon Q CLI talk like a pirate.

Exit Amazon Q CLI and create a new file called **"pirate.md"** in the current directory, and add this to the file:

```
Talk like a pirate and make bad jokes
```

Edit the custom agent JSON configuration file we worked on as part of the previous task. Edit the file so that it looks like the following:

```
{
  "$schema": "https://raw.githubusercontent.com/aws/amazon-q-developer-cli/refs/heads/main/schemas/agent-v1.json",
  "name": "python-developer",
  "description": "",
  "mcpServers": {},
  "tools": ["*"],
  "toolAliases": {},
  "allowedTools": ["fs_read","fs_write","use_aws"],
  "resources": [
    "file://steering/python-dev.md"
  ],
  "hooks": {"userPromptSubmit": [
      {
        "command": "cat pirate.md",
        "timeout_ms": 10000,
        "cache_ttl_seconds": 300
      }
    ]},
  "toolsSettings": {}
}
```

You can see we have modified the **"hooks"** configuration section. We have defined **"userPromptSubmit"** which configures a hook per prompt. We could also setup hooks using **"agentSpawn"** which would run when we start our Amazon Q CLI chat session.

> Check out the reference configuration [here](https://github.com/aws/amazon-q-developer-cli/blob/main/docs/agent-format.md#hooks-field)

After saving the file, restart your Amazon Q CLI session. Run the following command:

```
> /hooks
```

You should get output like the following:

```
[python-developer] > /hooks

userPromptSubmit:
  - cat pirate.md

```

Lets test this out now. Enter the following prompt:

```
> What is the model you are using
```

Review the output. What happens? 

You can edit and remove the context hook after this task if you want, or enjoy that Amazon Q CLI is talking to you in pirate.

---

**Adding MCP Servers to our custom agent**

At this beginning of this lab we introduced MCP and how Amazon Q CLI allows you to integrate MCP Servers to your Amazon Q CLI sessions. So far we have used custom agents to add resources (that provide context), understand how to setup tools that we can use in our Amazon Q CLI sessions, and create context hooks. These have all been done by editing the custom agent JSON configuration file.

MCP Servers can expose Tools, Resources, and Prompts (this varies from MCP Server to MCP Server, so consult the documentation as to what they provide). Amazon Q CLI can use Tools and Prompts.

When you add an MCP Server that provides Tools, these map to tools that we have already worked with in previous labs within Amazon Q CLI. We can apply the same controls for MCP Server Tools - give access to and control default permissions for. We will see this later in this lab.

We are going to build upon the previous custom agent we setup, "python-development" and add an MCP Server, specifically the [AWS Documentation MCP Server](https://awslabs.github.io/mcp/servers/aws-documentation-mcp-server). Clicking on that link will show you how to configure this MCP Server for any tool that integrates MCP Servers.

```
{
  "mcpServers": {
    "awslabs.aws-documentation-mcp-server": {
      "command": "uvx",
      "args": ["awslabs.aws-documentation-mcp-server@latest"],
      "env": {
        "FASTMCP_LOG_LEVEL": "ERROR",
        "AWS_DOCUMENTATION_PARTITION": "aws"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

> For a list of AWS MCP Servers, check out the [AWS MCP Server directory](https://awslabs.github.io/mcp/). You can explore a number of different MCP Servers, and the docs proivide an overview of the features that each MCP Server offers, together with the available Tools. If I scroll a bit further I get the installation details.

**Note!** Currently Amazon Q CLI **only** supports STDIO MCP Servers, which run locally on your machine. Bear this in mind as you go looking for MCP Servers you want to use.

**Task-06**

Edit the custom agent JSON configuration file we worked on as part of the previous task. Edit the file so that it looks like the following:

```
{
  "$schema": "https://raw.githubusercontent.com/aws/amazon-q-developer-cli/refs/heads/main/schemas/agent-v1.json",
  "name": "python-developer",
  "description": "",
  "mcpServers": {
	"awslabs.aws-documentation-mcp-server": {
        	"command": "uvx",
        	"args": ["awslabs.aws-documentation-mcp-server@latest"],
        	"env": {
          		"FASTMCP_LOG_LEVEL": "ERROR",
          		"AWS_DOCUMENTATION_PARTITION": "aws"
        	  }
		}
	},
  "tools": ["*"],
  "toolAliases": {},
  "allowedTools": ["fs_read","fs_write","use_aws"],
  "resources": [
    "file://steering/*.md"
  ],
  "hooks": {},
  "toolsSettings": {}
}
```
You can see we have taken the MCP Server configuration details from the MCP Server documentation site, and added it to our custom agent configuration.

Save the file, and then start your Amazon Q CLI session. Pay attention to what happens at the top of the splash screen. You should see something like the following appear:

```
⠏ 0 of 1 mcp servers initialized. ctrl-c to start chatting now
```

Once your Amazon Q CLI session has started you can view if has loaded by using the **"/mcp"** command that lists the available MCP Servers. 

From your Amazon Q CLI session, run the following command:

```
> /mcp
```

You should see something like this.

```
[python-developer] > /mcp

awslabs.aws-documentation-mcp-server
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
✓ awslabs.aws-documentation-mcp-server loaded in 10.37 s

```

So we have verified it has loaded ok.

---

**Configuring MCP Server Tools**

Adding MCP Servers provides you with additional tools you can configure for your Amazon Q CLI Session. These are typically documented by the MCP Server provider, so you will have an idea of what you can expect. Once you have integrated an MCP Server, if it surfaces up tools, you will also see this when you run the **"/tools"** command.

Tools added when you integrate MCP Servers are treated the same as other tools in your Amazon Q CLI session. They can be trusted or untrusted, which defines how these will operate when they are triggered. Like the built in tools within Amazon Q CLI, we can configure these by changing the properties of the custom agent by making updates to the JSON configuration file.

**Task-07**

Run the following command to view the available tools in your Amazon Q CLI session:

```
> /tools
```

You should see something similar to the following:

```
[python-developer] > /tools

Tool                      Permission
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
Built-in:
- execute_bash            * trust read-only commands
- fs_read                 * trusted
- fs_write                * trusted
- report_issue            * trusted
- use_aws                 * trusted

awslabs.aws-documentation-mcp-server (MCP):
- read_documentation      * not trusted
- recommend               * not trusted
- search_documentation    * not trusted

```

As you can see, we have three new tools available that have been provided by the AWS Documentation MCP Server.

Lets change this and make some changes for our custom agent to change what tools we want available for this custom agent as well as the default permissions.

Exit the Amazon Q CLI session, and edit your custom agent JSON configuration so that it looks like the following:

```
{
  "$schema": "https://raw.githubusercontent.com/aws/amazon-q-developer-cli/refs/heads/main/schemas/agent-v1.json",
  "name": "python-developer",
  "description": "",
  "mcpServers": {
	"awslabs.aws-documentation-mcp-server": {
        	"command": "uvx",
        	"args": ["awslabs.aws-documentation-mcp-server@latest"],
        	"env": {
          		"FASTMCP_LOG_LEVEL": "ERROR",
          		"AWS_DOCUMENTATION_PARTITION": "aws"
        	  },
		"disabled": false
		}
	},
  "tools": [
    "fs_read",
    "fs_write",
    "use_aws",
    "@awslabs.aws-documentation-mcp-server/read_documentation",
    "@awslabs.aws-documentation-mcp-server/search_documentation"
  ],
  "toolAliases": {},
  "allowedTools": ["@awslabs.aws-documentation-mcp-server/read_documentation"],
  "resources": [
    "file://steering/*.md"
  ],
  "hooks": {},
  "toolsSettings": {}
}
```

You will notice a few things that we have done:

* we have modified **"tools"** and included the tools from the MCP Server using the format **"@{mcp-server-name}/{tools}"**
* we have modified the **"allowedTools"** to specify one of the tools from the MCP Server, again using the same format

Restart your Amazon Q CLI session, and re-run the "/tools" command. Review the output. You should get something similar to the following:

```
[python-developer] > /tools


Tool                      Permission
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
Built-in:
- fs_read                 * trusted
- fs_write                * not trusted
- use_aws                 * trust read-only commands

awslabs.aws-documentation-mcp-server (MCP):
- read_documentation      * trusted
- search_documentation    * not trusted

```

Let's test this out. In the past I have written a lot about Managed Workflows for Apache Airflow (MWAA), and I know they keep adding new features and capabilities but I have kind of not kept up. I ask the following prompt:

```
> Check the AWS documentation for MWAA worker sizes
```

Follow the output. It should prompt you at some point to trust the "search_documentation" tool - if you look above, you can see that we did not trust this tool, only "read_documentation". This is what mine looked like:

```
> I'll search the AWS documentation for information about MWAA (Amazon Managed
Workflows for Apache Airflow) worker sizes.


🛠️  Using tool: search_documentation from mcp server awslabs.aws-documentation-mcp-server
 ⋮
 ● Running search_documentation with the param:
 ⋮  {
 ⋮    "name": "search_documentation",
 ⋮    "arguments": {
 ⋮      "search_phrase": "MWAA worker sizes Amazon Managed Workflows Apache Airflow",
 ⋮      "limit": 10
 ⋮    }
 ⋮  }

Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]
```

Respond "t" to trust this tool, and continue to watch the output. You should see at some point that Amazon Q CLI then uses the "read_documentation" tools, and this time does not ask you for permission.

```
> Perfect! I found the specific documentation page for environment classes. Let me read
the detailed information about MWAA worker sizes:


🛠️  Using tool: read_documentation (trusted) from mcp server awslabs.aws-documentation-mcp-server
 ⋮
 ● Running read_documentation with the param:
 ⋮  {
 ⋮    "name": "read_documentation",
 ⋮    "arguments": {
 ⋮      "url": "https://docs.aws.amazon.com/mwaa/latest/userguide/environment-class.html",
 ⋮      "max_length": 8000
 ⋮    }
 ⋮  }

```

Feel free to try out searches for your favorite AWS Services and see what you get.

Custom agents allow you to provide very flexible and granular control over your MCP Server tools selection and permissions.

---

**Clashing tool names - using toolAliases**

One thing to bear in mind as you add MCP Servers to your Amazon Q CLI is the potential for tools to have a name clash. For example, imagine if you have a tool called "get_issues" and had integrated the GitHub and GitLab MCP Servers. These both expose tools called "get_issues", so how do you manage this?

Within the custom agent configuration, you can create alias for tools and map these to the explicit tool you want to map it to. In the above example, we would add the following to the custom agent JSON configuration file:

```
  "toolAliases": {
    "@github-mcp/get_issues": "github_issues",
    "@gitlab-mcp/get_issues": "gitlab_issues"
  }
```

This is something to bear in mind as you start adding MCP Server tools and want to manage any conflicts that might arise.

---

**Changing how you refer to tools using toolsAliases**

In the previous section we shared how you can use **"toolsAliases"** to address namespace clashes.

You can also use aliases to create shorter or more intuitive names for frequently used tools. For example, if you have some long tools you can shorten them with something like:

```
{
  "toolAliases": {
    "@aws-cloud-formation/deploy_stack_with_parameters": "deploy_cf",
    "@kubernetes-tools/get_pod_logs_with_namespace": "pod_logs"
  }
}
```

This is a nice developer experience feature that I am using with some of my custom agent tools.


---

**Disabling MCP Servers**

We are able to disable MCP Servers from custom agents by editing the custom agent JSON file and adding new configuration item called "disabled" and setting this to true (the default is false).

**Task-08**

Edit the custom agent JSON configuration as follows:

```
{
  "$schema": "https://raw.githubusercontent.com/aws/amazon-q-developer-cli/refs/heads/main/schemas/agent-v1.json",
  "name": "python-developer",
  "description": "",
  "mcpServers": {
	"awslabs.aws-documentation-mcp-server": {
        	"command": "uvx",
        	"args": ["awslabs.aws-documentation-mcp-server@latest"],
        	"env": {
          		"FASTMCP_LOG_LEVEL": "ERROR",
          		"AWS_DOCUMENTATION_PARTITION": "aws"
        	  },
		"disabled": true
		}
	},
  "tools": ["*"],
  "toolAliases": {},
  "allowedTools": ["fs_read","fs_write","use_aws"],
  "resources": [
    "file://steering/*.md"
  ],
  "hooks": {},
  "toolsSettings": {}
}
```

Save the configuration file and then restart your Amazon Q CLI session. You should see output at the top of the screen that displays:

```
○ awslabs.aws-documentation-mcp-server is disabled
```

If you run **"/mcp"** what do you get?

---

**MCP Prompts**

We have already looked at how Amazon Q CLI supports MCP Tools, but it also supports MCP Prompts with the **"/prompts"** command. This will look at any MCP Servers that are providing Prompts resources, and then list them. You are then able to use these within your Amazon Q CLI sessions, referring to defined prompt templates with the **"@{prompt}"** command.

To do this we are going to implement a custom MCP Server that provides Prompt resources.

**Task-09**

Open a **new terminal window** and create a new directory (for example, "mcp-prompts").

From this window, create a file called **"mcp-server.py"** and add the following code. This will generate a simple MCP Server that provides a Prompt:

```
import asyncio
import sys

from mcp.server.fastmcp import Context, FastMCP

# Create an MCP server
mcp = FastMCP("QCLIPromptDemo")

@mcp.prompt()
def create_new_project() -> str:
    """A prompt that bootstraps a new project repository as per our requirements"""
    return f'Create a new project layout. Create a top level src directory, and in the src directory create subdirectories for templates, models, routes, and static. Add a README.md to the src directory.'
```

So what have we created here?

We have created an MCP Server which provides a sample prompt to bootstrap a project using a specific layout (this is a very simplified example, you could build something much more detailed and complex).

After saving this file, create a virtual python environment and install a couple of dependencies:

```
python -m venv .mcp
source .mcp/bin/activate
pip install mcp mcp[cli]
```

That's it we now have a simple custom MCP Server that provides Prompts. The configuration to use this is as follows:

```
{
    "mcpServers": {
        "QCLIPromptDemo": {
            "command": "uv",
            "args": ["run", "--with", "mcp", "mcp", "run", "mcp-server.py"]
        }
    }
}
```

We now need to add this MCP Server to our custom agent JSON configuration. We will use the one we have been using throughout this lab (but feel to create a new one if you want).

Edit this JSON configuration file so it looks like this:

```
{
  "$schema": "https://raw.githubusercontent.com/aws/amazon-q-developer-cli/refs/heads/main/schemas/agent-v1.json",
  "name": "python-developer",
  "description": "",
  "mcpServers": {
	"awslabs.aws-documentation-mcp-server": {
        	"command": "uvx",
        	"args": ["awslabs.aws-documentation-mcp-server@latest"],
        	"env": {
          		"FASTMCP_LOG_LEVEL": "ERROR",
          		"AWS_DOCUMENTATION_PARTITION": "aws"
        	  },
		      "disabled": false
		    },
	"QCLIPromptDemo": {
          "command": "uv",
          "args": ["run", "--with", "mcp", "mcp", "run", "mcp-server.py"]
        }
	},
  "tools": [
    "fs_read",
    "fs_write",
    "use_aws",
    "@awslabs.aws-documentation-mcp-server/read_documentation",
    "@awslabs.aws-documentation-mcp-server/search_documentation"
  ],
  "toolAliases": {},
  "allowedTools": ["@awslabs.aws-documentation-mcp-server/read_documentation"],
  "resources": [
    "file://steering/*.md"
  ],
  "hooks": {},
  "toolsSettings": {}
}
```

You can see we have added the new MCP Server we created by adding the following to the previous custom agent configuration file:

```
	"QCLIPromptDemo": {
          "command": "uv",
          "args": ["run", "--with", "mcp", "mcp", "run", "mcp-server.py"]
        }
```

After saving this file, in the same directory, start an Amazon Q CLI session and confirm that the MCP Server has started ok:

```
[python-developer] > /mcp

QCLIPromptDemo
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
✓ QCLIPromptDemo loaded in 9.79 s

awslabs.aws-documentation-mcp-server
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
✓ awslabs.aws-documentation-mcp-server loaded in 7.91 s
```

If you run **"/tools"**, what do you see? It should look exactly as previous. We have not added any new tools with this MCP Server, we have added prompts. So how do we see these? Simple, we use the **"/prompts"** command within our Amazon Q CLI Session.

From the **">"** prompt, type **"/prompts"** and hit return. You should see something like the following:

```
> /prompts list

Prompt                    Arguments (* = required)
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
QCLIPromptDemo (MCP):
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

```

Follow the output - you may need to provide permission as it will write files and we have not given this custom agent fs_write permission. Once it has finished, what did you get? Compare it to the prompt defined in the function - does it look like its created what was asked?

---

**Configuring fine grained permissions for your Tools**

We saw earlier in this lab how we can configure tools within custom agents, controlling which tools a specific custom agent had access to as well as the permissions granted. This is great, but what if you wanted to have more fine grain control over access. For example, we saw that we have a tool called **"execute_bash"** that allows you to run bash commands. What if you wanted to only allow certain commands to run?

This is possible using the **"toolsSettings"** configuration within custom agents. It allows you to configure specific controls that a given tool provides. For example, from the [Github pages](https://github.com/aws/amazon-q-developer-cli/blob/main/docs/built-in-tools.md) we can see that:

* **fs_write** and **fs_read** provide **"allowedPaths"** and **"deniedPaths"** configuration options
* **use_aws** provide **"allowedServices"** and **"deniedServices"** configuration options
* **execute_bash** provide **"allowedCommands"**, **"deniedCommands""**, and **"allowReadOnly"** options

You can provide explicity configuration references or use regex. Here is an example of how we would provide more fine grain control of what the **"execute_bash"** tool could do.

```
"toolsSettings": {
    "execute_bash": {
      "allowedCommands": ["git status", "git fetch"],
      "deniedCommands": ["git commit .*", "git push .*"],
      "allowReadOnly": true
    }
  }

```


If you are using tools that have been surfaced up via an MCP Server, you will need to review the documentation for that MCP Server and then use this configuration option in the same was as the built in ones. For example:

```
  "toolsSettings": {
    "@git/git_status": {
      "git_user": "$GIT_USER"
    }
  }
```

Not every tool has configuration options, so review documentation to find out if they do.

**Task-10**

Lets configure our custom agent so that we limit what files Amazon Q CLI has access to. We want it to be able to read files in the current project directory, but exclude more sensitive files.

Following on from previous examples, we will modify the custom agent as follows:

```
{
  "$schema": "https://raw.githubusercontent.com/aws/amazon-q-developer-cli/refs/heads/main/schemas/agent-v1.json",
  "name": "python-developer",
  "description": "",
  "mcpServers": {},
  "tools": [
    "fs_read",
    "fs_write",
    "use_aws"
  ],
  "toolAliases": {},
  "allowedTools": ["fs_read", "fs_write"],
  "resources": [
    "file://steering/*.md"
  ],
  "hooks": {},
  "toolsSettings": {
    "fs_read": {
      "allowedPaths": ["~/projects", "./src/**"],
      "deniedPaths": ["/tmp/*"]
    }
  }
}
```

As you can see we have added the following:

```
"toolsSettings": {
    "fs_read": {
      "allowedPaths": ["./**"],
      "deniedPaths": ["/tmp/*"]
    }
  }
```

Which will block Amazon Q CLI's ability to read files from a specific directory (in this case "/tmp",) but allow it to read files from the current directory and all subdirectories.

Lets create some files to test this out.

```
echo "import os" > /tmp/q-cli-test.py
echo "import os" > q-cli-test.py
echo "import os" > ~/q-cli-test.py
```

Restart your Amazon Q CLI session, and try the following prompts:

```
> Can you review the "q-cli-test.py" file and tell me what programming language its wriiten in
```

Does it run ok?

Now try the following:

```
> Can you review the "/tmp/q-cli-test.py" file and tell me what programming language its wriiten in
> Can you review the "~/q-cli-test.py" file and tell me what programming language its wriiten in
```

Now what happens? You should see something like the following:

```
> I'll read the "/tmp/q-cli-test.py" file to review it and identify the
programming language.

> I understand that the file read was rejected due to forbidden arguments. This is
likely because the path "/tmp/q-cli-test.py" is outside the allowed directory
scope or contains restricted content.

```

The ability to control at a granular level what your tools have access to is a very powerful capability that you can use to ensure that Amazon Q CLI is only operating on files that you want it to, or run commands that you want it to execute.

---

**Creating project scoped custom agents**

So far in this lab we have created custom agents that are global. We define whether an agent is global or project scoped by the location of the JSON configuration file.

At the beginning of this lab, we used the **"agent create -n xxx"** command to create our agent. By default, this creates custom agents with a global scope. What this means is that the JSON configuration file will get located in **"~/.aws/amazonq/client-agents"** directory.

However, you can add an additional argument **"-d"** to specify the directory where you want this to be created. When you do this, it will create the JSON configuration in the **".amazonq/client-agents"** directory, which sets the custom agent to be project scoped.

What if you have local, project custom agents and global custom agents? When Amazon Q CLI looks for an custom agent, it follows a specific precedence order: First it will look for **Local custom agents first**, checking for custom agents in the current working directory. Next it will look for **Global custom agents**, and fall back to custom agents in your home directory. If it fails to find any, it will use the **Built-in default** custom agent. If both local and global directories contain custom agents with the same name, the local custom agent takes precedence. Amazon Q Developer CLI will display a warning message when this occurs:

```
WARNING: Agent conflict for my-agent. Using workspace version.
```


> **Check out current good practices for organising custom agents** You can get some good ideas about how to organise your custom agents by checking out the official docs, [Best practices for organizing custom agents](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line-custom-agents-management.html?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el#custom-agent-organization)


---


### Supporting Resources

Some additional reading material that dives deeper into this topic if you want to explore:

* [Overcome development disarray with Amazon Q Developer CLI custom agents](https://aws.amazon.com/blogs/devops/overcome-development-disarray-with-amazon-q-developer-cli-custom-agents/?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el)

* [Running Model Context Protocol (MCP) Servers on containers using Finch](https://dev.to/aws/running-model-context-protocol-mcp-servers-on-containers-using-finch-kj8)

* [Building AIOps with Amazon Q Developer CLI and MCP Server](https://aws.amazon.com/blogs/machine-learning/building-aiops-with-amazon-q-developer-cli-and-mcp-server/?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el)

* [Containerize legacy Spring Boot application using Amazon Q Developer CLI and MCP server](https://aws.amazon.com/blogs/machine-learning/containerize-legacy-spring-boot-application-using-amazon-q-developer-cli-and-mcp-server/?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el)

### Completed!

Now that we have an idea of how Amazon Q CLI works and explored some of the features, we can take a look at some of the use cases in [Exploring Use Cases](03a-use-cases.md)


