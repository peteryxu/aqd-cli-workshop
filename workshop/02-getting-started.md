![Amazon Q Developer header](/images/q-vscode-header.png)

## Exploring Amazon Q CLI

In this lab we are going to start exploring Amazon Q CLI's features. 

### Command Line Completion

Command completion is only available when you have a full installation of Amazon Q CLI. It provides a helper that will guide you to the various options of popular commands you use day in day out – aws cli? Docker? Git?

When you type in a command from the terminal, you will notice that there is now a helper that will provide suggestions as to what command options you want to use.

You can navigate using the up and down arrow keys for the various options, or you can keep typing and the options will change as you type. If you want those options to stop or go away, you can press ESC and it will cancel.

![demo of command line completion](/images/q-cli-command-line-completion.gif)

---

**Task-01**

Open up a terminal window and type in "git" and then press space. What happens? Use the Up and Down arrow keys to navigate, and hit ENTER to accept the option.

If you continue typing "git push" and then press space, you will see that the available options narrow down based on where you are navigating the command to.

Try a different command (try perhaps "docker", "java", or "dotnet").

You can press ESC to exit the menu at any time, and you will then be able to continue writing in the command line.

---

### Command Line Auto Suggestion

Beyond just providing you with helpers, when you have Amazon Q CLI installed it will also begin to suggest commands based on the context of what you are doing.

![demo of command auto suggestion](/images/q-cli-auto-suggestion.gif)

(in the video above we can see how Amazon Q CLI helps us naviate the command options for git)

There is no task for this feature as we will see this as we proceed with the tutorial.

---

### Command Line Translation

How many times have you forgotten the various parameters you need to use to use a CLI tool? This has always been the case – as tools get more features, we need to learn the new parameters and options. How many times do we need to use "--help"?

We can now do this a different way, which will save you more time. We can translate what you want to do (our intent), and Amazon Q CLI will figure out which command to use. It will present its suggestion, and then allow you to edit (if you need to) and then run that command. 

You invoke this by typing **“q ai”** or **“q translate”** in the command line, followed by the action you want Q to convert into commands.

![demo of command line translation](/images/q-cli-cmd-translation.gif)

(in the video above we can see how we can use Amazon Q CLI to translate a specific intent into commands which we can then run)

---

**Task-02**

Lets see how this works in practice. In the existing terminal you have, type in the following:

```
q ai "find the largest 3 files in this directory and any sub directories"
```

After a few seconds, you should see something like the following pop up.

```
  Shell · find . -type f -exec du -h {} \; | sort -rh | head -3

❯ ⚡ Execute command
  📝 Edit command
  🔄 Regenerate answer
  ❓ Ask another question
  ❌ Cancel
```

You can use the up and down arrows to select different options, hitting ENTER to select. If we are happy we can just run this command (the default option). We might want to slightly edit the command and then run it - for that we select EDIT. If we are not happy, we can ask it to try again and regenerate  new response.

Experiment with a few command line tools you are familiar with.

---

### Interactive (Agentic) Chat

So far we have used Amazon Q CLI as just a tool to help us run commands more effectively. You can use Amazon Q Developer CLI in chat mode, and engage in natural language conversations, ask questions, and receive responses from Amazon Q within your terminal environment.

You access this from your terminal by either entering “q” or “q chat”, or you can provide some inputs and it will begin processing that once it starts, for example, "q 'show me a Docker file that runs a Python flask application' "

**Task 03**

From the command line, type in "q" or "q chat" and you should enter the Amazon Q CLI chat screen, which looks like this:

![starting amazon q cli](/images/q-cli-splash.png)

Type in the following:

```
> what do you prefer, tabs or spaces
```

It should generate some response. Amazon Q CLI can be used for a very broad range of use cases (check out the [Use Cases](/workshop/03-use-cases.md) section to explore this) - you are not limited to using it for just AWS work. We will see this as we proceed along this tutorial.

We can exit the Amazon Q CLI chat by either entering "/q" at the ">" prompt, or by pressing CTRL + C. Try exiting the chat now.

---

**Task 04**

Amazon Q CLI does more than just respond to your prompts, and has full agentic capabilities. It takes your prompts and then breaking these down into tasks which it will act upon. You will notice this as you start using it and see the word "Thinking..." appear. As it starts to work on the tasks you have given it, it will prompt you along the way as and when it needs more input, to confirm an operation it wants to do, or to keep you informed.

Open up Amazon Q CLI chat (by typing in "q" in the terminal)

From the **">"** prompt, enter the following:

```
> show me without creating, an optimised Docker file to run a Python Flask application. Make sure it uses the latest version of Python
```

Review the output - does it look ok to you?

---

**Task 05**

The previous prompt added the phrase "without creating" deliberately. We wanted Amazon Q CLI just to show us something.

It is capable of doing much more than this though. Amazon Q CLI has a concept of **Tools** that allow it to interact with the wider world. The various tools allow it to read, write, and execute commands. Amazon Q CLI gives you control though, so before we get it to do those things, we need to give it permission.

Open up a Amazon Q CLI chat session. From the **">"** prompt, type the following:

```
/tools
```

You should see something like the following:

```
> /tools

Tool              Permission
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
Built-in:
- use_aws         * trust read-only commands
- report_issue    * trusted
- fs_read         * trusted
- fs_write        * not trusted
- execute_bash    * trust read-only commands


Trusted tools can be run without confirmation

* Default settings
```

You will notice that we have two tools that are automatically trusted - **fs_read** and **report_issue**. **fs_read** allows Amazon Q CLI to read files from your computer, and **report_issue** allows to generate issues you run into when using Amazon Q CLI that will be logged in the GitHub project repo.

We can see writing and executing files are not trusted. What this means is that if we ask Amazon Q CLI to do something that wants to use these tools, it is going to prompt us for permission. Lets see this in action.

Enter the following prompt:

```
> create an optimised Docker file to run a Python Flask application. Make sure it uses the latest version of Python
```

You should notice that it generates a similar output to before, but this time it now prompts you whether you want to trust the **fs_write** tool (which is used to write files). We can select **"y"** to accept this one time, **"n"** to reject, or **"t"** to always trust this tool so that we are not asked subsequently.

Enter **"t"** and hit return. You should see it complete the creation of the file, and create additional files too. Exit Amazon Q CLI and review the files.

If we exit Amazon Q CLI and go back, the trust settings reset back the the default trust settings. We will look more about Tools and Trust in a later lab.

---

**Multi-line prompts**

So far we have just tried a single line prompt in Amazon Q CLI chat, but you can enter multi line prompts in a number of ways. The first way is to press **CTRL and J** and you will be moved to a new line. Alternatively, you and add a backslash **"\\"** to the end of your line, and when you hit enter, you will start a new line. This is what most people who spend time at the terminal will be familiar with.

**Task 06**

From an Amazon Q CLI chat session, use the two methods to enter the following multi-line prompt:

```
When creating Python code, use the following guidance
- Use Flask as the web framework
- Follow Flask's application factory pattern
- Use environment variables for configuration
- Implement Flask-SQLAlchemy for database operations
```

Whilst using **CTRL + J** and **\\** works for adhoc multi-line prompts, you might find it easier enabling your favorite editor which we will look at next.

---

*Configuring your favorite text editor*

When it comes to editors, its hard to beat vim. I know that others have a different (wrong in my view) perspective :-). The good news is that whatever is your favourite editor, you can configure Amazon Q CLI to use it in only a few steps.

First, you need create an environment variable called EDITOR. You can [see a list of some editor options here](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line-editor.html?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el), but other editors not on this list will probably work. I want to use vim, so from the current terminal I enter the following:

```
export EDITOR=vim
```

I launch Amazon Q CLI, and now I can use the **"/editor"** command and it will launch vim for me, and I can edit the prompt until I am happy. When I save and exit, it returns me back to Amazon Q CLI, and it starts to work.

**Task 07**

Close your Amazon Q CLI session, and set your EDITOR environment variable to your preferred editor. Here are some options:

* export EDITOR=vi
* export EDITOR="code -w"
* export EDITOR="nano"

Feel free to try with your own preferred editor. 

Once you have done that, open up Amazon Q CLI and run the **"/editor"** command. This should launch the editor you configured. Write a multi-line prompt, save and then exit. You should see Amazon Q CLI chat now processing your prompt.

---

### Working with Context

Context is what Amazon Q CLI will use together with the prompt you provide, to help it understand what it needs to do. Being able to manage context when using Amazon Q CLI is critical to get good output. In this section we will look at this in more detail.

The first thing to understand is that context is not and infinite resource, and it has to be managed. The **"context window"** is a finite resource that Amazon Q CLI uses to help it generate output. The **"context window"** is measured in tokens, which are the unit of text processing that large language models use. Currently Amazon Q CLI has a maximum context window of 200K tokens. 

When you start Amazon Q CLI, it will automatically look for and add to the **"context window"** any content in markdown documents in the following directories:

|Directory|File|Notes|
|---------|----|-----|
| Current directory | README.md | In the current directory where you launched Amazon Q CLI, look for a file called README.md|
| Current directory | AmazonQ.md | In the current directory where you launched Amazon Q CLI, look for a file called AmazonQ.md|
| Current directory/.amazonq/rules/ | *.md | In the current directory where you launched Amazon Q CLI, look for any markdown files in the .amazonq/rules directory|


This means that if we create markdown files in these locations, Amazon Q CLI will pick these up and use them as context. This is good to know, but what if you want to add your additional files as context? Luckily for us we can do this easily using the **"/context add"** command, which we will see in a moment.

>*Markdown documents as context*
>
> Creating documents in markdown format is a good way to provide context and help steer the output. For example, I typically put together my own coding preferences in a markdown doc for Amazon Q CLI to use. It will review those resources and factor that into the output it creates.

When you start Amazon Q CLI, it will start with a fresh, clean context. It will look however for a number of resources to load up into its context memory. From the Amazon Q CLI chat session, we can look at this by typing in the **"/context show"** command.

```
> /context show

🌍 global:
    .amazonq/rules/**/*.md
    README.md
    AmazonQ.md

👤 profile (default):
    <none>

No files in the current directory matched the rules above.
```

We can see here that it is looking at files that match **".amazonq/rules/\*\*/*.md"**, **"README.md"**, and **"AmazonQ.md"**. If you have these in your project workspace, it will read these and add those as context.

> **Note!** It can be a bit confusing how it displays "global:" and then **".amazonq/rules/\*\*/*.md"** - what does this mean? This refers to the Global Amazon Q CLI configuration, which is located in **"~/.aws/amazonq...."**


*Adding local vs global context*

You can add files to both your local or global context. 

* Global context allows you to add, remove context files that will be used all the time when you interact with Amazon Q CLI.
* Local context allows you to add, remove context files in just the current profile you are working in. As you switch or change to different profiles, the context files you have configured will change. 

To add local context files you use the **"/context add {markdown file}"**. To add global context files, you use **"/context add --global {markdown file}"**

---

**Task 08**

Open up an Amazon Q CLI chat session, and run the following command at the **">"** prompt:

```
> /context show
```

Does it look like the output above? When running some of the previous steps, it might have created a README.md file, and if so you should see this appear.

When you run the **"/context show"** you also see something called **"profile"**. Lets look at what that is next.

---

**Profiles**

Profiles allow you to switch between sets of contexts, allowing you to create unique ways for Amazon Q CLI to behave and interact with your systems. When you start Amazon Q CLI, you start with a default profile. This contains a global context and workspace context:

- Global context: Files that are applied to **all profiles**
- Workspace context: Files specific to the current profile

You can create new profiles using the **"/profile create xxxx"** where xxx is a name (for example, python-dev). When you add new profiles, they will have their own unique **workspace context**, allowing you to specify patterns of files that make that profile behave and interact in ways unique to your workflow and processes.

For example, you might create:

* A "terraform" profile with infrastructure-as-code guidelines
* A "python" profile with Python coding standards
* A "java" profile with Java best practices

**Task 09**

From an Amazon Q CLI chat session, we will create two new profiles. One for Java Development, and another for Python. We might want to do this to reflect different preferences and rules we use that are unique to each language. From the **">"** prompt, enter:

```
> /profile create python-dev
```

Review the output. It should just return that it has created a profile. Now repeat this for a Java profile:

```
> /profile create java-dev
```

Review the context using **"/context show"** and pay attention to the profile details. Which profile are you currently using?


By having multiple profiles, you can quickly change the context that Amazon Q uses to provide responses without having to manually specify these files in each conversation. 

Ok so how do you switch to a different profile?

---

**Task 10**

We can change between profiles (inclduing the default profile) by using the **"/profile set"** command within the Amazon Q CLI chat session. Lets try this out now. 

```
> /profile set python-dev
```

Change to the Java and then back to the default profile and then run the **"/context"** show command.

When you exit Amazon Q CLI, the next time you use it you will be reset to the default profile. You can start Amazon Q CLI in a specific profile by starting it with the **"--profile"** argument.

```
q --profile python-dev
```

Which will start your Amazon Q CLI session using this profile.

---

**Add Context to Profiles**

Profiles provide us with a granular way of managing important context files. Lets take a look at how we add context files to specific Profiles.

**Task 11**

In your current directory I create a new file, called **"project-standards.md"** (dont worry, you can leave this empty). Then add it to your local context usig the following command:

```
> /context add project-standards.md
```

Then review your context by running:

```
> /context show
```

You should see something like the following:

```
> /context show

🌍 global:
    .amazonq/rules/**/*.md (2 matches)
    README.md (1 match)
    AmazonQ.md

👤 profile (default):
    project-standards.md (1 match)

```

As you can see I was using the default profile. You can try changing profiles to see how this looks different.

If you close your Amazon Q CLI session and launch it back in the same directory, you will notice that it still trys to load the files you specified into context. This is beacuase when you use the "/context add" command, it writes a configuration to the following directory: **"~/.aws/amazonq/profiles/{profile}/context.json"**. This is what the file looks like in my setup:

```
{
  "paths": [
    "project-standards.md"
  ],
  "hooks": {}
}
```

You can add files to your global context, which will mean that every time you start an Amazon Q CLI session it will look for that resource. To do this you use the **"/context add --global {markdown file}"** which will then change the scope of the files you have added from local (your current profile), to global.

---

**Task 12**

In your current directory I create a new file, called "coding-standards.md" (dont worry, you can leave this empty). Then add it to your global context usig the following command:

```
> /context add --global coding-standards.md
```

Review your context, and you should see that this appears in the global context.

When you use the "/context add --global" command, it writes a file in your "~/.aws/amazonq" directory called "global_context.json", containing a list of all the resources have defined. Here is what mine looks like:

```
{
  "paths": [
    ".amazonq/rules/**/*.md",
    "README.md",
    "AmazonQ.md",
    "LICENSE"
  ],
  "hooks": {}
}
```

You might be wondering, ok I know how to add files to context but what if I made a mistake. How do I remove or change? Amazon Q CLI has you covered with the "/context rm" command, which you can use to remove context (both local and global).

```
> /context rm project-standards.md
```

You can of course also edit the configuration files and restart Amazon Q CLI too.

---

**Managing Context**

You might be wondering how many docs, or how big those docs can be when adding these to your context? Amazon Q CLI provides 200K tokens for your **"context window"**. As you add more context in your Amazon Q CLI sessions, you will start to use up this. Managing your **"context window"** is therefore important and will ensure that you have consistent and good outcomes when using Amazon Q CLI. This next lab will look at some of the options and strategies you can use.

---

*Context usage*

You can check your current **"context window"** by using the **"/usage"** command. When you run this on a new chat session, with no markdown docs added to the context, you will see something like the following:

```
> /usage

Current context window (260 of 200k tokens used)
|████████████████████████████████████████████████████████████████████████████████ 0.13%

█ Context files: ~260 tokens (0.13%)
█ Q responses:   ~0 tokens (0.00%)
█ Your prompts:  ~0 tokens (0.00%)

```

You can find out how much context specific files are taking up by running the **"/context show"** command. 

In the following output I ran this command on a project where I have several files that are automatically added to the context (as they are in the rules directory of the project). You can see how this uses up tokens against the limit.


```
> /context show

🌍 global:
    .amazonq/rules/**/*.md (2 matches)
    README.md (1 match)
    AmazonQ.md

👤 profile (default):
    <none>

3 matched files in use:
🌍 /Users/ricsue/amazon-q-developer/katowice/book-sharing-app/book-sharing-app/.amazonq/rules/2.project-spec.md (~80 tkns)
🌍 /Users/ricsue/amazon-q-developer/katowice/book-sharing-app/book-sharing-app/README.md (~9880 tkns)
🌍 /Users/ricsue/amazon-q-developer/katowice/book-sharing-app/book-sharing-app/.amazonq/rules/1.project-layout-rules.md (~50 tkns)

Total: ~10010 tokens

```

If you start exceeding these limits, you will start to see variability and inconsistencies in the output you get, so its important to manage this.

**Task 13**

Open up an Amazon Q CLI session and run the **"/usage"** command.

Review the output and take a few minutes to review this section.

---

*Compacting*

If you do start to reach the limits, you can compact your context. Compacting will generate a summary of everything in the current context. Once this summary has been completed, it will replace your current context.

If you try to do this when you have plenty of context available, you will see the following message:

```
> /compact

Conversation too short to compact.

```

I run **"/compact"** after doing some work on an existing code base to add some new features. I ran **"/usage"** before and after running **"/compact"** so you can see the kind of output generated.


```
> /usage

Current context window (50930 of 200k tokens used)
████████████████████████████████████████████████████████████████████████████████ 25.47%

█ Context files: ~10400 tokens (5.20%)
█ Q responses:   ~16730 tokens (8.36%)
█ Your prompts:  ~23800 tokens (11.90%)

> /compact

✔ Conversation history has been compacted successfully!


════════════════════════════════════════════════════════════════════════════════
                       CONVERSATION SUMMARY
════════════════════════════════════════════════════════════════════════════════

## CONVERSATION SUMMARY
* Implementation of a book recommendation system for a book sharing application
* Creation of recommendation algorithms using collaborative filtering, content-based filtering, and popularity-based approaches
* Development of new routes and templates for displaying personalized book recommendations
* Integration of the recommendation system with the existing application

## TOOLS EXECUTED
* fs_read: Examined todo.md to identify the next task (implement book recommendation system)
* fs_read: Explored project structure and existing files
* fs_read: Analyzed models (book.py, user.py, book_rating.py) to understand data structure
* fs_read: Reviewed routes (books.py) to understand existing functionality
* fs_write: Created recommendation service (recommendation.py) with collaborative filtering algorithms
* fs_write: Created recommendation routes (recommendations.py) with endpoints for personalized and popular recommendations
* fs_write: Created templates for recommendations (index.html, popular.html, similar.html)
* fs_write: Updated app.py to register the new recommendations blueprint
* fs_write: Updated base.html to add navigation links to recommendations
* fs_write: Updated books/view.html to add links to similar books
* fs_write: Updated index.html to promote the recommendation feature
* fs_write: Updated requirements.txt to add numpy and scikit-learn dependencies

## CODE CREATED
* RecommendationService class with methods:
  * get_recommendations_for_user: Main method combining different recommendation approaches
  * _collaborative_filtering: Recommends books based on similar users' ratings
  * _content_based_filtering: Recommends books based on user preferences and book attributes
  * _popularity_based: Recommends popular books based on ratings and upvotes
  * _cosine_similarity: Helper method to calculate similarity between users
* New Flask routes:
  * /recommendations/ - For personalized recommendations
  * /recommendations/popular - For popular books
  * /recommendations/similar/<book_id> - For books similar to a specific book
* New templates for displaying recommendations in different views

## KEY INSIGHTS
* The recommendation system uses a hybrid approach combining collaborative filtering, content-based filtering, and popularity-based recommendations
* Collaborative filtering identifies similar users based on rating patterns and recommends books they enjoyed
* Content-based filtering uses user preferences (like favorite genre) to recommend similar books
* Popularity-based recommendations serve as a fallback when personalized recommendations are insufficient
* The system excludes books the user already owns or has rated from recommendations
* The implementation requires numpy and scikit-learn for mathematical operations like cosine similarity calculation

The conversation history has been replaced with this summary.
It contains all important details from previous interactions.
════════════════════════════════════════════════════════════════════════════════

> /usage

Current context window (12060 of 200k tokens used)
████||████████████████████████████████████████████████████████████████████████████ 6.03%

█ Context files: ~11470 tokens (5.73%)
█ Q responses:   ~580 tokens (0.29%)
█ Your prompts:  ~10 tokens (0.00%)
```

You can see that it has reduced the overall "context window" in this example.

---

*Clearing*

There might be times when you want to reset your current context. For example, during an Amazon Q CLI session, you notice that the quality of the responses are not what you think they should be. Or perhaps you have completed one task and you want to reset context to begin another.

You can clear the current context by using the **"/clear"** command, which will first ask you to confirm that you want to do this.

```
> /clear

Are you sure? This will erase the conversation history and context from hooks for the current session. [y/n]:

> y

Conversation history cleared.
```

---

### Advanced Topics

**Executing commands**

So far we have been using Amazon Q CLI in chat mode to write prompts and then get responses. Sometimes you might want to run a command from within your chat session. You could exit and then restart, but you will lose any context and conversation history that you have built up. Don't worry though, there is a way. You can use the **"!"** to preface any command you want to run, and it will execute the command and then return the results back to you in the chat session.

**Task 14**

Open up Amazon Q CLI and run the following commands:

```
> ! ls -altr
```

Review the output - you should see your current directory listed out. Experiment with a few other commands.

---

**Tools and Trust**

In **Task 05** you looked at **Tools** and how Amazon Q CLI requires you to control how those tools work. We looked at trusting tools as part of the chat session, where you entered **"t"** to trust the tool for the duration of that chat session.

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

Adding MCP Servers to Amazon Q CLI is super easy, and requires you to do two things:

1. Create a **"mcp.json"** file
2. Edit the **"mcp.json"** file to add any MCP Servers you want to add

When you make those changes, the next time you restart Amazon Q CLI, it will try and load up any MCP Servers you have configured.

You can add MCP Servers at the global level (every time you start an Amazon Q CLI session), or at specific project workspaces (they will start just for those projects). The location of the **"mcp.json"** file determines this:

* Global MCP Settings - **"~/.aws/amazonq/mcp.json"**
* Workspace specific MCP Settings - **".amazonq/mcp.json"**

Lets take a look at creating our **"mcp.json"** file, and we will do it at the global level. Before we edit it, we need an MCP servers to configure. In the next task we will do that.

> For a list of AWS MCP Servers, check out the [AWS MCP Server directory](https://awslabs.github.io/mcp/). You can explore a number of different MCP Servers, and the docs proivide an overview of the features that each MCP Server offers, together with the available Tools. If I scroll a bit further I get the installation details.

**Task 16**

We are going to add an MCP Server to our Amazon Q CLI setup. We are going to use the [Promptz MCP Server](https://www.promptz.dev/mcp), which is an online resource to discover and explore prompts created by the community to enhance your Amazon Q workflow. 

Exit Amazon Q CLI so that you are at the terminal. 

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

Save this file. 

Introduced in 1.10 of Amazon Q CLI, you now have a new argument **"q mcp"** that provides you with tools to help you manage your MCP server settings. When we run **"q mcp"** we get the following.

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

We can check the status of the MCP Server we have just configured running the following command:

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


As mentioned previously, as you start exploring and integrating MCP Servers is that these are downloading and installing libraries or containerised images. As such, you will need to make sure that you have installed any dependencies. These are typically documented in the MCP Server details. 

In this particular case, you will need to make sure I have **uv** and **Python** running, otherwise this is going to fail. Different MCP Servers will have different requirements so make sure you meet them before proceeding. 

Start Amazon Q CLI from the terminal, and review the output. You should see something similar to the following (you might miss it as it is only on thre screen for a few moments):

```
0 of 1 mcp servers initialized. ctrl-c to start chatting now
```

and very shortly after that, you should see the familiar Amazon Q CLI splash screen but with some additional text at the top:

```
✓ promptzdevmcp loaded in 1.29 s
✓ 1 of 1 mcp servers initialized
```

Exit Amazon Q CLI, and create a new project directory somewhere on your machine. From this directory open up Amazon Q CLI. From the **">"** prompt, type in the following:

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

> **Note!**
> The latest version of Amazon Q CLI load these up in the background. When you do a **"/tools"** you might see something like:
>
> ```
>Servers still loading
> ▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
> - promptzdevmcp
> - awslabscost_analysis_mcp_server
> - awslabscdk_mcp_server
>
> Trusted tools can be run without confirmation
> ```
>
> You will have to wait until these load, so try again after a few seconds to make sure they are all then listed.



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

**Task 17**

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

**Task 18**

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

* [Getting started with Amazon Q Developer CLI](https://dev.to/aws/getting-started-with-amazon-q-developer-cli-4dkd)

* [Configuring Model Context Protocol (MCP) with Amazon Q CLI](https://dev.to/aws/configuring-model-context-protocol-mcp-with-amazon-q-cli-e80)

* [Running Model Context Protocol (MCP) Servers on containers using Finch](https://dev.to/aws/running-model-context-protocol-mcp-servers-on-containers-using-finch-kj8)


### Completed!

Now that we have an idea of how Amazon Q CLI works and explored some of the features, we can take a look at some of the use cases in [Exploring Use Cases](03-use-cases.md)


