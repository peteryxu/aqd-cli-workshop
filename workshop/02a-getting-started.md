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

You access this from your terminal by either entering “q” or “q chat”, or you can provide some inputs and it will begin processing that once it starts, for example, "q 'show me a Docker file that runs a Python flask application' ".

When you start an Amazon Q CLI chat session, that sessions loads configuration settings from a number of different locations so its worth noting these.

* **Global Configurations** - Amazon Q CLI will load up general settings that you define using the **"q settings"** command from the settings.json file (which you saw in the Advanced Setup part of this workshop)
* **Session Configuration** - Amazon Q CLI will load up "custom agent configuration" for your particular session, allowing you to define things like MCP Servers, Context Resources, Tools permissions (we will explore these later in this workshop)

**Amazon Q CLI custom agents**

Starting with v1.13.1 of the Amazon Q CLI tool, when you start your Amazon Q CLI chat session, you will load up something called a "custom agent" configuration. Amazon Q CLI allows you to create something called **Custom Agents** which act as a configuration repository allowing you to define things like resources, tools, context. Don't worry, we will dive into this later, but its important to know that when you start Amazon Q CLI, when you enter **"q chat"** with no arguments, you will be loading up the default Agent configuration which is called **q_cli_default**.

You can start your Amazon Q CLI chat with different custom agents by using the **"--agent {name-of-agent}"** argument. For example, if you created a custom agent called "php-dev" then you would start your Amazon Q CLI session with the command 
**"q chat --agent php-dev"** and this would load up that configuration rather than the default. 

We will be exploring custom agents in more detail later on in this lab.

**Task-03**

From the command line, type in "q" or "q chat" and you should enter the Amazon Q CLI chat screen, which looks like this:

![starting amazon q cli](/images/q-cli-splash.png)

Type in the following:

```
> what do you prefer, tabs or spaces
```

It should generate some response. Amazon Q CLI can be used for a very broad range of use cases (check out the [Use Cases](/workshop/03-use-cases.md) section to explore this) - you are not limited to using it for just AWS work. We will see this as we proceed along this tutorial.

To get help, you can type in **"/help"**. From your Amazon Q CLI session **">"**, type:

```
> /help
```

At this stage, don't worry too much about what all those commands mean as we will be exploring these over the coming labs.

In the previous section we introduced Amazon Q CLI custom agents. We can look at what custom agent we have loaded by running the **"/agent list"** command. Type:

```
/agent list
```

You should get output similar to the following:

```
> /agent list

* q_cli_default
```

This shows that we are using the default agent configuration called **q_cli_default**. We will be exploring this in much more detail later on.

We can exit the Amazon Q CLI chat by either entering "/q" at the ">" prompt, or by pressing CTRL + C. Try exiting the chat now.

---

**Task-04**

Amazon Q CLI does more than just respond to your prompts, and has full agentic capabilities. It takes your prompts and then breaking these down into tasks which it will act upon. You will notice this as you start using it and see the word "Thinking..." appear. As it starts to work on the tasks you have given it, it will prompt you along the way as and when it needs more input, to confirm an operation it wants to do, or to keep you informed.

Open up Amazon Q CLI chat (by typing in "q" in the terminal)

From the **">"** prompt, enter the following:

```
> show me WITHOUT CREATING ANY FILES, an optimised Docker file to run a Python Flask application. Make sure it uses the latest version of Python
```

Review the output - does it look ok to you?

---

**Tools**

The previous prompt added the phrase "WITHOUT CREATING ANY FILES" deliberately. We wanted Amazon Q CLI just to show us something. It is capable of doing much more than this though. Amazon Q CLI has a concept of **Tools** that allow it to interact with the wider world. To make sure that we have control, Amazon Q CLI has the concept of permissions for these **Tools**, allowing you to set the level of trust and automation.

Amazon Q CLI confgiures **Tools** as **"Trusted"** or **"Untrusted"**. What this means is that when Amazon Q CLI wants to use one of these Tools, if a Tool is "Untrusted" you will be prompted to ask for permission to proceed with running that Tool. You can either trust this one time, trust it going forward, or decline.

By default, Amazon Q CLI auto trusts the built in Tools that perform "Read" operations, but does not trust any tool that needs to write to the file system or execute a command or program. Amazon Q CLI gives you the ability to control though.

Why is this important? Imagine you are working on a big project, that might write files and run programs. If you leave the Tools setting to untrusted, you will be asked every time to proceed. You might want to automatically approve in some situations, and therefore havinv the control to trust these tools will let Amazon Q CLI proceed without prompting you.

Lets take a look at this, exploring Tools permissions and then seeing how this work by extending the previous task.

**Task-05**

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

**Task-06**

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

**Configuring your favorite text editor**

When it comes to editors, its hard to beat vim. I know that others have a different (wrong in my view) perspective :-). The good news is that whatever is your favourite editor, you can configure Amazon Q CLI to use it in only a few steps.

First, you need create an environment variable called EDITOR. You can [see a list of some editor options here](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line-editor.html?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el), but other editors not on this list will probably work. I want to use vim, so from the current terminal I enter the following:

```
export EDITOR=vim
```

I launch Amazon Q CLI, and now I can use the **"/editor"** command and it will launch vim for me, and I can edit the prompt until I am happy. When I save and exit, it returns me back to Amazon Q CLI, and it starts to work.

**Task-07**

Close your Amazon Q CLI session, and set your EDITOR environment variable to your preferred editor. Here are some options:

* export EDITOR=vi
* export EDITOR="code -w"
* export EDITOR="nano"

Feel free to try with your own preferred editor. 

Once you have done that, open up Amazon Q CLI and run the **"/editor"** command. This should launch the editor you configured. Write a multi-line prompt, save and then exit. You should see Amazon Q CLI chat now processing your prompt.

---

**Using images as part of your prompt**

Amazon Q CLI supports images as part of your prompts. This means that you can refer to images within your project workspace as part of what you want Amazon Q CLI to help you with. For example, maybe you have some architecture diagrams you want to help build out some infrastructure as code, or you have a entity relationship diagram of a data model you want to build some SQL for, or maybe you have some sample design layouts you want to convert to some prototype code.

In the next lab we will take a look at how you can do this.

**Task-08**

In the resources directory you will find a file called [example-erd.png](/resources/example-erd.png). Copy this to your local directory into a directory called "data-model".

Start Amazon Q CLI and then from the **">"** enter the following prompt:

```
> Create a sample data model in python from the example-erd.png diagram in the data-model directory
```

Review the output. You should see how it is able to read the file, extract the key elements and then start generated code.


---

**Selecting the large language model to use in your Amazon Q CLI session?**

You can select the model you want Amazon Q to use to respond to your requests during chat sessions. A default model is set when you start a chat session, and you can either change the model Amazon Q uses for a given session or set a default model for all sessions.

There are two ways you can set the Model you want to use:

* Launching a chat sessions using **"q chat --model <model-name>"**
* From within the chat session using the **"/model"** command, and then selecting the Model you want to use with the up and down arrows

As of wrting this (June 2025) the following Models are available:

* claude-3.5-sonnet
* claude-3.7-sonnet
* claude-4-sonnet

When you exit your Amazon Q CLI chat session, the Model will revert back to the default Model. The default Model can be configured within your settings file. In the [advanced setup](/workshop/01b-advanced-setup-topics.md) we looked at how you can configure the default Model in your settings file.


**Task-09**

If you are not already in a chat session, start Amazon Q CLI, and from the **">"** prompt, type in the following:

```
/model
```

Change the Model and try some Prompts. Exit the chat session and then from the command line type the following:

```
q chat --model claude-4-sonnet
```

From the chat session, run the **"/model"** command again. You should see that the Model has changed to use Claude v4. 

Exit the chat session and then restart Amazon Q CLI. Re-run the **"/model"** command. The Model should now have reverted back to the default Model.

---

### Context Engineering

Context is what Amazon Q CLI will use together with the prompt you provide, to generate its output. Being able to manage context when using Amazon Q CLI is critical to get good output. In this section we will look at this in more detail.

Context is **not** and infinite resource, and needs to be managed. The **"context window"** is a finite resource that Amazon Q CLI uses to shape the generated output. The **"context window"** is measured in tokens, which are the unit of text processing that large language models use. Currently Amazon Q CLI has a maximum context window of 200K tokens. 

Research done by Stanford University into the efficacy of different context sizes against different foundation models shows that as you increase context, the output degrades. So having more is not always a good thing.

![graph of context window and performance](/images/sota-context-limits.png)

In Amazon Q CLI, you can configure **Resources** that provide the context you want to provide. Resources are markdown files that live within your filesystem. When you start Amazon Q CLI, it will use the agent configuration (don't worry, we will come back to what this is later) to determine what resources are loaded into context. The default agent (q_cli_default) will configure the following resouces automatically:

|Directory|File|Notes|
|---------|----|-----|
| Current directory | README.md | In the current directory where you launched Amazon Q CLI, look for a file called README.md|
| Current directory | AmazonQ.md | In the current directory where you launched Amazon Q CLI, look for a file called AmazonQ.md|
| Current directory/.amazonq/rules/ | *.md | In the current directory where you launched Amazon Q CLI, look for any markdown files in the .amazonq/rules directory|


From the Amazon Q CLI chat session, we can look at what **Resources** you have currently defined as context by typing in the **"/context show"** command.

```
> /context show

👤 Agent (q_cli_default):
    AmazonQ.md
    README.md
    .amazonq/rules/**/*.md

No files in the current directory matched the rules above.
```

We can see here that it is looking at the default agent configuration (q_cli_default), and this configuration has files that match **".amazonq/rules/\*\*/*.md"**, **"README.md"**, and **"AmazonQ.md"**. If you have these in your project workspace, it will read these and add those as context and you will see **1 match** displayed against them.

---

**Task-10**

Open up an Amazon Q CLI chat session, and run the following command at the **">"** prompt:

```
> /context show
```

Does it look like the output above? When running some of the previous steps, it might have created a README.md file, and if so you should see this appear.

Exit the Amazon Q CLI session and create AmazonQ.md or README.md files in the current directory. You can leave the files blank. Restart Amazon Q CLI and then re-run the **"/context show"** command. What changed?

You should notice that Amazon Q CLI has picked up these files, and now shows them as **"Matched"**.

---

**Adding and removing Resources to your Context**

In the previous lab we looked at **"Resources"** and how these provide context to your Amazon Q CLI sessions. We need to be able to manage this so we can add/remove those resources and have better control over the context we provide Amazon Q CLI. To do that, we can use the **"/context add"** and **"/context remove"** commands (you can also use **"/context rm"**).


**Task-11**

In your current directory I create a new file, called **"project-standards.md"**. Edit the file and add some text (don't worry, just whatever comes to mind). Then add it to your local context usig the following command:

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

👤 Agent (q_cli_default):
    AmazonQ.md 
    README.md
    project-standards.md (1 match)
    .amazonq/rules/**/*.md

1 matched file in use:
👤 /Users/ricsue/amazon-q-developer-cli/workshop/project-standards.md (~590 tkns)

Total: ~590 tokens

```

You might be wondering, ok I know how to add files to context but what if I made a mistake. How do I remove or change? Amazon Q CLI has you covered with the "/context rm" command, which you can use to remove context.

```
> /context rm project-standards.md
```

If you re-run the **"/context show"** command, you should now see this has disappeared.


The final command you should be aware of is the **"/context clear"** which should be used with care as it will remove ALL of your current configured resources you have added as context. When you run this command, it will only remove them for the duration of the session you are currently in. 

Once you exit and then restart your Amazon Q CLI session they will come back. One thing to note though, **ONLY THOSE RESOURCES DEFINED IN THE CUSTOM AGENT** will get added (and yeah, this custom agent thing keeps coming up and I promise we are getting to that very soon!).

Lets look at this in action. Add a new resource to your context

```
> /context add project-standards.md
```

Then run the **"/context show"** command. You should see this has been added to your context. Now run the **"/context clear"** command

```
> /context clear
```

It shoudl generate the following output:

```
Cleared context
```

Run the **"/context show"** command and review the output. Now exit your Amazon Q CLI session, restart it and then re-run the **"/context show"** command. You should see that your context has all the original, default resources, but the one we added ("project-standards.md") is not longer there.

---

**Managing Context**

We have already mentioned that Amazon Q CLI provides 200K tokens for your **"context window"**. As you add resources to your context, you might be wondering how much of that you are using up? As you add more resources to context in your Amazon Q CLI sessions, you will start to use up your 200K limit. Managing your **"context window"** is therefore important and will ensure that you have consistent and good outcomes when using Amazon Q CLI. This next lab will look at some of the options and strategies you can use.

---

*Context usage*

You can check your current **"context window"** by using the **"/usage"** command. When you run this on a new chat session, with no markdown docs added to the context, you will see something like the following:

```
> /usage

Current context window (2650 of 200k tokens used)
|████████████████████████████████████████████████████████████████████████████████ 1.32%

█ Context files: ~120 tokens (0.06%)
█ Tools:     ~2530 tokens (1.26%)
█ Q responses:   ~0 tokens (0.00%)
█ Your prompts:  ~0 tokens (0.00%)

```

If you start exceeding these limits, you will start to see variability and inconsistencies in the output you get, so its important to manage this.

**Task-12**

Open up an Amazon Q CLI session and run the **"/usage"** command.

Review the output and take a few minutes to review this section.

---

**Compacting**

If you do start to reach the limits, you can compact your context. Compacting will generate a summary of everything in the current context. Once this summary has been completed, it will replace your current context.

I run **"/compact"** after doing some work on an existing code base to add some new features. I ran **"/usage"** before and after running **"/compact"** so you can see the kind of output generated.


```
> /usage

Current context window (50930 of 200k tokens used)
████████████████████████████████████████████████████████████████████████████████ 25.47%

█ Context files: ~10400 tokens (5.20%)
█ Tools:     ~2530 tokens (1.26%)
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
█ Tools:     ~2530 tokens (1.26%)
█ Q responses:   ~580 tokens (0.29%)
█ Your prompts:  ~10 tokens (0.00%)
```

You can see that it has reduced the overall "context window" in this example.

If you try to do this when you have plenty of context available, you will see the following message:

```
> /compact

Conversation too short to compact.

```

The default behaviour is that Amazon Q CLI will automatically try and compact as you reach the limit of your context window. You might want to control this behaviour, and the good news is that you can do this by setting the following in your Amazon Q CLI settings.json.

```
q settings chat.disableAutoCompaction true
```

After you run this command, compaction will now not automatically occur and you can control this within your Amazon Q CLI Session.

---

**Task-13**

Exit your Amazon Q CLI session, and in the current directory create two files, "AmazonQ.md" and "README.md". Fill these with some text (if you are stuck, I have provided a [resource here](/resources/compact.md) that you can use to copy/paste into these files).

Start an Amazon Q CLI session and run the following prompts (and make up some of your own)

```
> Review the AmazonQ.md file and provide a summary of what is planned
> Review and suggest improvements to the design
```

Once this has completed, run the **"/usage"** command. Then run the following:

```
/compact
```

What happened? Review the usage again, you should notice a change.

---

**Tangent Mode**

In the previous labs, we have explored context engineering and looked at some of the techniques you have available to you to effectively manage your context. In this lab we are going to introduce a new experimental feature that provides some useful additional capabilities to help you minimise polluting your context. What does this mean? As your Amazon Q CLI sessions progress, you might need to use Amazon Q CLI to do adjacent tasks (for example, maybe troubleshoot an issue, or perhaps dive into a rabbit hole) but those tasks might not necessarily contribute to your objective and so you might not want to include the output of those interactions in your context. 

**Tangent mode** creates conversation checkpoints, allowing you to explore side topics without disrupting your main conversation flow. Enter tangent mode, ask questions or explore ideas, then return to your original conversation exactly where you left off.


**Task-14**

The first thing we need to do is enable this by using the /experiment mode ( [see the advanced section for more details if you missed that section out](/workshop/01b-advanced-setup-topics.md) ) - move down to "Tangent Mode" and then press space

```
? Select an experiment to toggle ›
  Knowledge          [ON]  - Enables persistent context storage and retrieval across chat sessions (/knowledge)
  Thinking           [OFF] - Enables complex reasoning with step-by-step thought processes
❯  Tangent Mode      [OFF]  - Enables entering into a temporary mode for sending isolated conversations (/tangent)
  Todo Lists         [OFF] - Enables Q to create todo lists that can be viewed and managed using /todos
```

After pressing space, you should see the followiing.

```
 Tangent Mode experiment enabled
```

From the Amazon Q CLI session, you can now run the command **"/tangent"**

```
> /tangent
```

You will notice that the **">"** prompt changes to **"↯ >"**, to let you know that you are now in tangent mode. What might you do when you are in tangent mode? Here are some example use cases:

* Explore alternatives approaches - you might have started along one path, but you can use tangents to explore alternatives so that they do not add to your existing context
* Getting help on Amazon Q CLI - if you need to ask Amazon Q CLI for help on some of its features, but dont want that to pollute your context
* Clarification - you might want to dive into some details to make sure that you have everything you need, but want to do this in a way that does not distract your existing context

You can check out some [additional examples use cases here](https://github.com/aws/amazon-q-developer-cli/blob/main/docs/tangent-mode.md#usage-examples)

```
> /tangent

Created a conversation checkpoint (↯). Use ctrl + t or /tangent to restore the conversation later.
Note: this functionality is experimental and may change or be removed in the future.

↯ >
```

You can now use Amazon Q CLI in exactly the same way, knowing that your previous conversations have been check pointed and safe. Once you have finished, you can exit your session and return back to your previous conversation by running the **"/tangent"** command again.

```
↯ > /tangent

Restored conversation from checkpoint (↯). - Returned to main conversation.
```

You will notice that your prompt changes back, and you are now ready to carry on with your session.

> If you see the following when you run the command (**"/tangent"**) command, you have not enabled Tangent in the experimental model
> 
> ```
> > /tangent
> Tangent mode is disabled. Enable it with: q settings chat.enableTangentMode true
> ```

---

**Clearing**

There might be times when you want to reset your current context. For example, during an Amazon Q CLI session, you notice that the quality of the responses are not what you think they should be. Or perhaps you have completed one task and you want to reset context to begin another.

You can clear the current context by using the **"/clear"** command, which will first ask you to confirm that you want to do this.

**Task-15**

Following on from the previous session, from the **">"** run the following command:

```
> /clear

Are you sure? This will erase the conversation history and context from hooks for the current session. [y/n]:

> y

Conversation history cleared.
```

---

### Rules 

As you start using Amazon Q CLI on a more regular basis, you will start to find that providing certain instructions over and over to complete certain tasks.This repetitive setup reduces productivity and creates the potential for inconsistent outputs if you forget.  **Rules** are a way to build a library of coding standards and best practices that **are automatically used as context**.  What I find most compelling about using rules with Amazon Q CLI is how it minimizes the repetitive setup that usually comes with completing tasks. Instead of repeatedly instructing your AI assistant on your preferences and standards for each request, you can define these once as rules. This creates a consistent, predictable experience that automatically respects what you want it to do.

You define rules in Markdown files, stored in your project’s workspace. Amazon Q CLI will look at a number of files and directories for these rules by default, and we can see this when we run the following command **"/context show"**:

```
> /context show

👤 Agent (q_cli_default):
    AmazonQ.md
    README.md
    .amazonq/rules/**/*.md

💬 Session (temporary):
    <none>

No files in the current directory matched the rules above.
```

You will notice the **.amazonq/rules/**/*.md** which automatically looks for markdown files which will then be added as rules. As we have not created any rules, then none show up. We will cover this in a future lab.

**Sharing Rules files**

Within your project workspace, storing rules files withi your project workspace (in the ".amazonq/rules" directory) allows you to share these will automatically be available for anyone who checks out the code.

**Rules vs Context**

You might be wondering why separate Rules from the resources we can add as context. This is a good question, and the way I like to think of this is that Rules are what you want to persist across all your prompts when using Amazon Q CLI, whereas Context is resources you want to add (and remove) based on the task you are doing.

For a deeper dive, Check out the supporting resources for a nice deep dive on Rules, how they work and how to optimise the rules that you create.

---

### Executing commands

So far we have been using Amazon Q CLI in chat mode to write prompts and then get responses. Sometimes you might want to run a command from within your chat session. You could exit and then restart, but you will lose any context and conversation history that you have built up. Don't worry though, there is a way. You can use the **"!"** to preface any command you want to run, and it will execute the command and then return the results back to you in the chat session.

There are two things you need to bear in mind When running commands. **First**, each invocation is stateless. If I use "! cd {directory}" and then run "! ls" I will not get the directory listing for the {directory} but for the current Amazon Q CLI directory where you launched it. If you need to do that you should concatanate your commands, so using this as an example "!cd {directory} && ls". **Second** The output of the commands you run is added to the context window. If you run command that generate a lot of output, you will potentially use up your context window more quickly.

**Task-16**

Open up Amazon Q CLI and run the following commands:

```
> ! ls -altr
```

Review the output - you should see your current directory listed out. Experiment with a few other commands.

---

**Managing conversations (Chats) across Amazon Q CLI sessions**

Whilst you are working within your Amazon Q CLI session, you might need to stop and exit. However, you want to then return and then carry on working where you left off. Don't worry, Amazon Q CLI has you covered.

*Starting with --resume*

The first thing you can do is to use the **"--resume"** option when starting Amazon Q CLI. When you do this, it will look at the current directory you are in, and then look to carry on where you left off. You can see here in the screen that I start Amazon Q CLI with the "--resume" option, and it then looks at the current directory, sees that we were working on something and then is ready to continue.

```
 q chat --resume

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
> /save session-checkpoint

✔ Exported conversation state to session-checkpoint
```

You can also load up a conversation from an Amazon Q CLI session using the **"/load {name}"** and it will then load up the conversation, leaving you ready to carry on where you left off.

**Task-17**

From a new Amazon Q CLI session, at the **">"** prompt lets generate some history by asking the following (feel free to use your own if you prefer)

```
> I want to design a customer service application. can you provide me with the top three things i need to think about
> What sort of data model would a customer service application need? Just show me, don't generate any code.
```

Now try saving your current conversation:

```
> /save session-checkpoint
```

After it has saved, open up a new terminal and review the output.

Now close your Amazon Q CLI session and start it again. From the **">"** prompt, lets reload that conversation:

```
> /load session-checkpoint
```

After it loads, ask it something about the customer service application (or if you did your own prompts, something about those)

You should notice that Amazon Q CLI is able to carry on where it left off.

---

### Supporting Resources

Some additional reading material that dives deeper into this topic if you want to explore:

* [Getting started with Amazon Q Developer CLI](https://dev.to/aws/getting-started-with-amazon-q-developer-cli-4dkd)

* [Mastering Amazon Q Developer with Rules](https://aws.amazon.com/blogs/devops/mastering-amazon-q-developer-with-rules/?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el)

* [Amazon Q Developer CLI supports image inputs in your terminal](https://aws.amazon.com/blogs/devops/amazon-q-developer-cli-supports-image-inputs-in-your-terminal/?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el)

### Completed!

Now that you have got started with Amazon Q CLI, you can explore [Amazon Q CLI Advanced Topics](/workshop/02b-advanced-topics.md) to dive deeper and get the most out of this tool.

Alternatively, you can explore some of the use cases in [Exploring Use Cases](03a-use-cases.md)


