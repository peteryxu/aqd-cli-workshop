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

**Which Model do you want to use in your Amazon Q CLI session?**

You can select the model you want Amazon Q to use to respond to your requests during chat sessions. A default model is set when you start a chat session, and you can either change the model Amazon Q uses for a given session or set a default model for all sessions.

There are two ways you can set the Model you want to use:

* Launching a chat sessions using **"q chat --model <model-name>"**
* From within the chat session using the **"/model"** command, and then selecting the Model you want to use with the up and down arrows

As of wrting this (June 2025) the following Models are available:

* claude-3.5-sonnet
* claude-3.7-sonnet
* claude-4-sonnet

When you exit your Amazon Q CLI chat session, the Model will revert back to the default Model. The default Model can be configured within your settings file. In the [advanced setup](/workshop/01b-advanced-setup-topics.md) we looked at how you can configure the default Model in your settings file.


**Task 07b**

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

**Executing commands**

So far we have been using Amazon Q CLI in chat mode to write prompts and then get responses. Sometimes you might want to run a command from within your chat session. You could exit and then restart, but you will lose any context and conversation history that you have built up. Don't worry though, there is a way. You can use the **"!"** to preface any command you want to run, and it will execute the command and then return the results back to you in the chat session.

**Task 14**

Open up Amazon Q CLI and run the following commands:

```
> ! ls -altr
```

Review the output - you should see your current directory listed out. Experiment with a few other commands.

---


### Supporting Resources

Some additional reading material that dives deeper into this topic if you want to explore:

* [Getting started with Amazon Q Developer CLI](https://dev.to/aws/getting-started-with-amazon-q-developer-cli-4dkd)


### Completed!

Now that you have got started with Amazon Q CLI, you can explore [Amazon Q CLI Advanced Topics](/workshop/02b-advanced-topics.md) to dive deeper and get the most out of this tool.

Alternatively, you can explore some of the use cases in [Exploring Use Cases](03a-use-cases.md)


