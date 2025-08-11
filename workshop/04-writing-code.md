![Amazon Q Developer header](/images/q-vscode-header.png)

## Writing Code

**Overview**

In the first part of this lab we are going to build a simple application to show how good Amazon Q CLI is at generating code. We will share good practices that you should adopt to improve the output generated, and use some of the things we learned in the previous lab.

*What are we going to build?*

We are going to build a customer feedback survey application. The application will initially have some basic functionality, an MLP - Minimum Lovable Product. We will be using Amazon Q Developer to help us add code and improve the basic functionality. We will dive into the details as we get into the workshop, but we want our application to be able to:

* allow users to register and then login to this application to use it
* let logged in users create surveys
* make it so that anyone can submit feedback from a survey
* let only owners of a survey see the survey results

**Creating our project workspace**

From a terminal, navigate to a new directory where we will start work (In my setup I have a "projects" directory  where I have all the code I write, so change this to whatever you use). Once in your chosen project directory, type in the following. 

```
mkdir aqcli-app
cd aqcli-app
```

This will be our working directory for this lab. Don't go into Amazon Q CLI yet, as there are some thing we want to setup before we start.

---

**Task-01**

The first thing we are going to do is create some context files which Amazon Q CLI will use when generating output. As we mentioned in earlier parts of this tutorial, context is critical in getting good results when using AI coding assistants. As we think about what context we might want to provide to help steer Amazon Q CLI, we are going to create additional context around how we want the code to be structured, and provide some technologies that we want it to use. 

In the current (aqcli-app) directory:

1. Create a new directory **"steering"**
2. In the **"steering"** create two files. **"1.project-layout-rules.md"** and **"2.project-spec.md"**
3. In the **"1.project-layout-rules.md"** file, add the following:

```
Use the following project structure when writng code

src/
├── static/
├── models/
├── routes/
├── templates/
├ extensions.py
├ app.py
```

4. In the **"2.project-spec.md"** file add the following:

```
When creating Python code, use the following guidance

- Use Flask as the web framework
- Follow Flask's application factory pattern
- Use environment variables for configuration
- Implement Flask-SQLAlchemy for database operations
```

After saving these files, this is what you should have in your project workspace:

```
└── steering
    ├── 1.project-layout-rules.md
    └── 2.project-spec.md
```

Now we have setup resources we are going to use to help provide context to Amazon Q CLI, we can proceed.

---

**Task-02**

As we work on our new project, we are going to create a new custom agent which we will use to configure everything we need whilst developing our application.

Start Amazon Q CLI, and from the **">"** prompt, lets create our custom agent

```
> /agent create -d . --name customer-survey-project
```

> **Notice!** In previous labs we created globally scoped custom agents, but this time we have used the **"-d ."** argument, which will create a project scoped custom agent. This creates the JSON configuration file in the local project workspace in the **".amazonq/cli-agents"** directory.

This will bring up the editor to edit the JSON configuration for this custom agent. Edit the file so that it looks like the following:

```
{
  "$schema": "https://raw.githubusercontent.com/aws/amazon-q-developer-cli/refs/heads/main/schemas/agent-v1.json",
  "name": "customer-survey-project",
  "mcpServers": {},
  "tools": ["*"],
  "toolAliases": {},
  "allowedTools": ["fs_read"],
  "resources": [
    "file://steering/*.md, "file://data-model/*"
  ],
  "hooks": {}
}

```

We have created a custom agent with one allowed tool (**fs_read**), and configured it to look for context resources in a couple of directories. We will come back to this again later on to make changes.

You can check that this has been configured correctly by checking using the **"/agent list"** command:

```
> /agent list

* q_cli_default
  customer-survey-project
  python-developer
```

(Note, if you have not done the previous labs, you will only see q_cli_default and the customer-survey-project)

If we look at our file system now, it should look somethig like:

```
├── .amazonq
│   └── cli-agents
│       └── customer-survey-project.json
└── steering
    ├── 1.project-layout-rules.md
    └── 2.project-spec.md
```

Now that we have created this, lets re-start our Amazon Q CLI session so that it uses it. To do that we run the following command:

```
q chat --agent customer-survey-project 
```

You will notice that even though this is a local custom agent, we did not need to specify this when using the **"--agent"** argument. This is because Amazon Q CLI will look at the local project workspace first before looking for globally scoped custom agents.

You should notice that your prompt looks like the following:

```
[customer-survey-project] >
```

We have successfully created our first local custom agent, and configured some local resources as context. However, as we are going to be building an application we want to add some more context next.

---

**Task-03**

When writing code, providing a data model as context is a useful way to ensure that the code generated has a good baseline to work from.

Exit your Amazon Q CLI session, and create a new directory called "data model" in the current project directory, and then copy the [data model from the resources directory](/resources/database_schema.yaml) into this directory.

You should now have the following directory layout.

```
├── .amazonq
│   └── cli-agents
│       └── customer-survey-project.json
├── data-model
|   └── database_schema.yaml
└── steering
    ├── 1.project-layout-rules.md
    └── 2.project-spec.md

```

Start Amazon Q CLI again, this time using the following command so that we start using our local custom agent:

```
q chat --agent customer-survey-project 
```

When Amazon Q CLI starts, run **"/context show"** to see how your environment looks.

```
[customer-survey-project] > /context show

👤 Agent (customer-survey-project):
    steering/*.md (2 matches)
    data-model/* (1 match)

💬 Session (temporary):
    <none>

3 matched files in use:
👤 /Users/ricsue/amazon-q-developer-cli/workshop/aqcli-app/steering/1.project-layout-rules.md (~40 tkns)
👤 /Users/ricsue/amazon-q-developer-cli/workshop/aqcli-app/steering/2.project-spec.md (~60 tkns)
👤 /Users/ricsue/amazon-q-developer-cli/workshop/aqcli-app/data-model/database_schema.yaml (~520 tkns)

Total: ~620 tokens
```

Amazon q CLI has picked up our steering files for context, as well as the data model that we want to use. Run these commands in your own Amazon Q CLI session and confirm that you get the same output.

---

**Task-04**

We are now ready to ask Amazon Q CLI to write some code for use. For that we need a prompt, so here is one I preparaed earlier.

```
Build a simple Customer Survey application. 

- Generate a web application that can be used in a browser
- Users will need to register with an email address to login
- When Users login, a Dashboard will be displayed that provides a simple explanation of what the application does.
- From the home Dashboard, Users will be displays any available Customer Surveys that they have been created
- When Users are viewing their Customer Surveys, they will have the ability to view the results
- When Users are viewing the Dashboard, any available Customer Surveys will provide a link that can be shared so people can submit feedback on a specific survey
- Provide a simple web design that can be updated easily using CSS
- Ensure you review the data model when building the application
- Ensure the database is initialised properly
```

We don't want to write this in line by line (using either \ or CTRL + J after each line), so configure Amazon Q CLI to use an editor. In the following I am going to use vim, but feel free to switch this to your preferred editor.

Exit Amazon Q CLI to return to the command line. From the command line enter the following:

```
export EDITOR=vi
```

Check to make sure that this has set correctly (from the command line, echo $EDITOR or the equivalent for your OS), and then restart your Amazon Q CLI session with the local custom agent.

```
q chat --agent customer-survey-project 
```

Now run **"/editor"** which should bring up your editor. Paste the prompt above into this file, and then save/edit. If you are using vim, press ESC, then write ":wq!".

You should now see Amazon Q CLI start to work. As it works, you will need to review Tools that it wants to use (for example, writing to your file system). 

* You will notice that when the **fs_read** tool is run, that you are not asked for permission. This is because we have configured this tool to be trusted (allowed) in the local custom agent
* When Amazon Q CLI tries to use the **fs_write** tool, you will be asked for permission (it might look something like this)

```
🛠️  Using tool: fs_write
 ⋮
 ● Path: /Users/ricsue/amazon-q-developer-cli/workshop-test2/aqcli-app/src/extensions.py

 <code>

 Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:
```

Lets modify our local custom agent so that we are not prompted and that we give permission for Amazon Q CLI to write files.Exit out of this session by typing CTRL + C, and then edit the local custom agent JSON configuration file.

It will be in the **".amazonq/cli-agents"** directory. Edit the file so that it looks like the following:

```
{
  "$schema": "https://raw.githubusercontent.com/aws/amazon-q-developer-cli/refs/heads/main/schemas/agent-v1.json",
  "name": "customer-survey-project",
  "mcpServers": {},
  "tools": ["*"],
  "toolAliases": {},
  "allowedTools": ["fs_read", "fs_write"],
  "resources": [
    "file://steering/*.md", "file://data-model/*"
  ],
  "hooks": {}
}
```

You can see that the only change we have made is that we have added **"fs_write"** to the **allowedTools** configuration option.

Now restart Amazon Q CLI with the local custom agent:

```
q chat --agent customer-survey-project 
```

And re-enter the prompt using **"/editor"** - you should notice that this time you are not prompted and your Amazon Q CLI session will start building the application for you.

This will take around 7-8 minutes to complete, so a great time to stretch your legs and rehyrdrate.

---

*Viewing the code*

When I use Amazon Q CLI to write code, to make it easier to review it I either start the Amazon Q CLI session from within a terminal in the IDE I am using, or I open up a new terminal, and then start an IDE session from the project directory. This way I now have a graphical tool to interact and review the files that have been created.

![running Amaozn Q CLI in VSCode terminal](/images/q-cli-ide.png)

(example of running Amazon Q CLI in the terminal within VSCode)

---

**Task-05**

Now that we have some code that has been generated by Amazon Q CLI, we are going to see how we can use Amazon Q CLI to help us run the application. In doing so, if it encounters errors, it will be able to resolve these and then iterate on the code.

From the ">" prompt in Amaozn Q CLI enter the following:

```
> Create a virtual python environment (.venv) and the install dependencies and then run the application
```

Follow along as it first creates the virutal environment and installs the dependencies, and then tries to run the application.

With luck, you should see something like the following:

```
 * Serving Flask app 'src.app'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on http://127.0.0.1:5001
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 100-674-860
```

Open up a web browser and enter "http://127.0.0.1:5001" and see what happens.

With luck you should be able to view our Customer Feedback application. If this does not happen, and you get an error, then you can use Amazon Q CLI to help troubleshoot (see the next section). If it does load up ok, then try the following:

* Register a user
* Login using that user
* Create a survey

You can stop the application at any time by pressing CTRL + C.

---

**Troubleshooting**

First stop the application if it is running by pressing CTRL + C.

Review the error in the logs. And the use that within the chat session. Use prompts such as the following:

* "When doing xx with this application, I get the following error -  {paste the error} "
* "What does error {paste error} mean"

You can try your own variations, but the key to getting good output is to frame what you were doing, together with the error message. Sometimes you might need to include a lot of the additional information in the error, other time just the error is suffucient.

---

**Adding Tests**

Amazon Q CLI makes it easy to add tests to our project. In this next lab we are going to generate tests for the code we have just created. 

**Task-06**

From the **">"** enter the following prompt:

```
> Add unit tests to this project for the routes and models. Aim for 50% test coverage
```

This will take around 10-15 minutes to complete, but as it starts to work you will see it first build up a picture of the project before then proceeding to implement test fixtures and then the tests themselves.

It should run the tests and then show you a report. It is almost certain that you will get some warnings come up (typically around deprecated classes/methods/libraries). You can remediate these warnings, using them as inputs and crafting prompts to update code to address those warnings.  For example, this is a warning that came up with the code it generated:

```
tests/test_survey_routes.py::TestSurveyRoutes::test_delete_survey
  /Users/ricsue/amazon-q-developer-cli/workshop-test/tests/test_survey_routes.py:209: LegacyAPIWarning: The Query.get() method is considered legacy as of the 1.x series of SQLAlchemy and becomes a legacy construct in 2.0. The method is now available as Session.get() (deprecated since: 2.0) (Background on SQLAlchemy 2.0 at: https://sqlalche.me/e/b8d9)
    deleted_survey = Survey.query.get(survey_id)

====================================== 37 passed, 49 warnings in 7.34s =======================================
```

I then used the following prompt to get Amazon Q CLI to help address this:

```
> Review the code and update the code to address warnings from the test, specifically "LegacyAPIWarning: The Query.get() method is considered legacy as of the 1.x series of SQLAlchemy and becomes a legacy construct in 2.0. The method is now available as Session.get()"
```



When adding tests, you should make sure you review the test code and sense check to make sure that it is testing what you want and need it to test. 

---

**Task-07**

I have got into the habbit of running **"/usage"** as I work on various tasks within Amazon Q CLI to see the impact they have on the context window and the amout of tokens used. 

From the ">" prompt, enter "/usage" and review the output. This is what mine looked like:

```
[customer-survey-project] > /usage


Current context window (69640 of 200k tokens used)
|████████████████████████████████████████████████████████████████████████████████ 34.82%

█ Context files: ~740 tokens (0.37%)
█ Tools:     ~2510 tokens (1.25%)
█ Q responses:   ~31640 tokens (15.82%)
█ Your prompts:  ~34750 tokens (17.38%)


💡 Pro Tips:
Run /compact to replace the conversation history with its summary
Run /clear to erase the entire chat history
Run /context show to see tokens per context file
```

As you can see, this looks comfortable at the moment and we are not reaching any limits. We can see the bulk of the tokens are being used in the chat conversation (prompts and Amazon Q CLI's responses back).

---

**Task-08**

We have our application up and running, but I want to do one final thing before we are done. I want to check the generated web application for accessibility. There are various open source tools that help you perform audits and help you take remedial action. Some of these are now available as MCP Servers, so we are going to configure one and then use it against the code we have created.

Exit the Amazon Q CLI session, and then edit the local custom agent JSON configuration so that it looks like the following:

```
{
  "$schema": "https://raw.githubusercontent.com/aws/amazon-q-developer-cli/refs/heads/main/schemas/agent-v1.json",
  "name": "customer-survey-project",
  "mcpServers": {
    "a11y": {
      "command": "npx",
      "args": ["a11y-mcp"],
      "disabled": false
    }
  },
  "tools": ["*"],
  "toolAliases": {},
  "allowedTools": ["fs_read", "fs_write"],
  "resources": [
    "file://steering/*.md", "file://data-model/*"
  ],
  "hooks": {}
}

```

> **Note!** This MCP Server requires NodeJS to be available on your machine. From a new terminal window, try running:
>
> ```
> npx a11y-mcp
> ```
> If you get an error, then you will need to check the [documentation](https://github.com/priyankark/a11y-mcp?tab=readme-ov-file#installation) and install any dependenecies needed.


After saving, restart your Amazon Q CLI session with:

```
q chat --agent customer-survey-project
```

You should notice that this has now loaded up an MCP Server, which we can check using the following commands:

```
> /mcp
a11y
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
✓ a11y loaded in 33.84 s
```

and we can see what additional tools have been made available with **"/tools"**:

```
[customer-survey-project] > /tools


Tool               Permission
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
Built-in:
- execute_bash     * trust read-only commands
- fs_read          * trusted
- fs_write         * trusted
- report_issue     * trusted
- use_aws          * trust read-only commands

a11y (MCP):
- audit_webpage    * not trusted
- get_summary      * not trusted
```

Lets use this tool to run an audit on our code. From the [documentation](https://github.com/priyankark/a11y-mcp?tab=readme-ov-file#available-tools) we can see that we need to provide a url for this to work.

Open up a new terminal session, and then start the application up (depending on the code that was generated, you might need to change the commands to start your application if they are different to what I have below)

```
source .venv/bin/activate
python run.py
```

The application starts, and I can see that its listening for requests. Opening up a browser confirms it all looks good.

I can now use the following prompt:

```
> Use the a11y MCP server to audit http://127.0.0.1:5001 for accessibility issues
```

Amazon Q CLI asks me to confirm that I am happy to run this tool as I did not modify the custom agents permissions.

```
[customer-survey-project] > Use the a11y MCP server to audit http://127.0.0.1:5001 for accessibility issues

> I'll audit the webpage at http://127.0.0.1:5001 for accessibility issues using the a11y MCP server.


🛠️  Using tool: audit_webpage from mcp server a11y
 ⋮
 ● Running audit_webpage with the param:
 ⋮  {
 ⋮    "name": "audit_webpage",
 ⋮    "arguments": {
 ⋮      "url": "http://127.0.0.1:5001"
 ⋮    }
 ⋮  }

Allow this action? Use 't' to trust (always allow) this tool for the session. [y/n/t]:
```

I confirm by responding "t" and then leave the tool to run and generate its findings. This is the output it generated for me.

```
The accessibility audit found several issues with your Flask application at http://127.0.0.1:5001:

## Critical Issues Found

### 1. Color Contrast Problems (Serious Impact)
• **Login link**: Insufficient contrast ratio of 3.97 (needs 4.5:1 minimum)
• **Register link**: Same contrast issue
• **Button elements**: Same contrast issue
• All use white text (#ffffff) on blue background (#007bff)

### 2. Missing Main Heading (Moderate Impact)
• The page lacks a level-one heading (h1 element)
• This is important for screen readers and page structure

## Recommendations

### Fix Color Contrast
Update your CSS to use a darker blue or different color scheme:
css
/* Replace #007bff with a darker blue like #0056b3 */
.nav-link, button {
    background-color: #0056b3; /* Darker blue for better contrast */
}


### Add Main Heading
Add an h1 element to your main template:
html
<h1>Survey Application</h1> <!-- Or appropriate page title -->


## Summary
• **26 tests passed** ✅
• **2 violations found** ❌
• **0 incomplete tests**
• **62 inapplicable tests**

The main issues are fixable with CSS color adjustments and adding proper heading structure. Would you like me to help
you implement these fixes in your Flask application?
```

Notice that at the end Amazon Q CLI asks me if I want to go ahead and fix these issues. I respond with "yes please, implement these fixes" and it then proceeds to make some updates.

After a few minutes, the code has been updated and it has helpfully re-run the accessibility audit. This time it looks much better.

```
Excellent! 🎉 The accessibility fixes have been successfully implemented. Here's a summary of what was accomplished:

## ✅ Accessibility Issues Fixed

### 1. Color Contrast Issues Resolved
• Updated primary color from #007bff to #0056b3 (darker blue)
• Updated hover color from #0056b3 to #004085 (even darker)
• All navigation links and buttons now meet WCAG 2 AA contrast ratio requirements (4.5:1 minimum)

### 2. Proper Heading Structure Implemented
• Updated all page templates to use h1 as the main heading:
  • Dashboard: "Welcome to Customer Survey App"
  • Login: "Login"
  • Register: "Register"
  • Create Survey: "Create New Survey"
  • View Survey: Uses survey title as h1
  • Public Survey: Uses survey title as h1
  • Thank You: "Thank You!"
• Maintained proper heading hierarchy (h1 → h2 → h3)

### 3. Enhanced Focus Accessibility
• Added visible focus indicators for navigation links
• Added focus styles for buttons with proper outline colors
• Improved keyboard navigation experience

## 📊 Audit Results
• **Before**: 2 violations, 26 passes
• **After**: 0 violations, 27 passes ✅
• **100% accessibility compliance** for tested criteria

The application now meets WCAG 2 AA accessibility standards for the audited elements. Users with visual impairments, screen
readers, and keyboard navigation will have a much better experience with your Flask survey application.

```

Just to make sure, I access the application from a browser to make sure that this looks ok. When I check it definitley looks better, so that is a big win.

---

**Summary**

In this lab we have seen how we can use Amazon Q CLI, together with local custom agents to help us build an application within minutes, leveraging MCP Servers to make sure we build inclusive and accessible applications.

You can [proceed back to the README](/README.md) or try the next lab, [05-Automation](/workshop/05-automation.md)