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

We will also need to clean up some of the files that we created in the previous lab. Make sure you delete the following files:

* "~/.aws/amazonq/mcp.json"
* "~/.aws/amazonq/profiles/default/context.json"

Don't worry, these files are not critial and were only created as part of the previous labs.

This will be our working directory for this lab. Don't go into Amazon Q CLI yet, as there are some thing we want to setup before we start.

**Task 01**

The first thing we are going to do is create some context files which Amazon Q CLI will use when generating output. In the current (aqcli-app) directory:

1. Create a new directory ".amazonq/rules"
2. In the ".amazonq/rules" create two files. "1.project-layout-rules.md" and "2.project-spec.md"
3. In the "1.project-layout-rules.md" file, add the following:

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

4. In the "2.project-spec.md" file add the following:

```
When creating Python code, use the following guidance

- Use Flask as the web framework
- Follow Flask's application factory pattern
- Use environment variables for configuration
- Implement Flask-SQLAlchemy for database operations
```

After saving these files, you should now have two files in the ".amazonq/rules" directory.

During the previous labs we learned about how Amazon Q CLI reads certain files when starting, to preload context. What we have done is provide context around what we expect from a project layout perspective, as well as guidelines as to how to build the application. We should see how this affects the output in the next steps.

**Task 02**

As we work on our new project, we are going to create a new profile and use this to define some additional context files we want to use just for this project.

Start Amazon Q CLI, and from the ">" prompt, lets create our profile

```
> /profile create customer-survey-project
```

which should look somethig like:

```
Created profile: customer-survey-project

[customer-survey-project] >
```

Ok this looks good, we are now ready for the next task. You will need to exit Amazon Q CLI as we are going to do more stuff on the command line.

**Task 03**

When writing code, providing a data model as context is a useful way to ensure that the code generated has a good baseline to work from.

Create a new directory called "data model" in the current project directory, and then copy the [data model from the resources directory](/resources/database_schema.yaml) into this directory.

You should now have the following directory layout.

```
├── .amazonq
│   └── rules
│       ├── 1.project-layout-rules.md
│       └── 2.project-spec.md
└── data-model
    └── database_schema.yaml
```

Start Amazon Q CLI again. We need to switch to the profile we created so run "/profile set customer-survey-project".

Now run the "/context show" directory. It should give you output similar to the following:

```
[customer-survey-project] > /context show

🌍 global:
    .amazonq/rules/**/*.md (2 matches)
    README.md
    AmazonQ.md

👤 profile (customer-survey-project):
    <none>

2 matched files in use:
🌍 /Users/ricsue/amazon-q-developer-cli/workshop-test/.amazonq/rules/1.project-layout-rules.md (~50 tkns)
🌍 /Users/ricsue/amazon-q-developer-cli/workshop-test/.amazonq/rules/2.project-spec.md (~80 tkns)

Total: ~130 tokens
```

We are going to add the data model as context, so we are going to run the following command:

```
> /context add data-model/database_schema.yaml
```

which should display the following:

```
Added 1 path(s) to profile context.
```

Now repeat the "/context show" command and review how its different. What has changed?

**Task 04**

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

> If you want to use VSCode, then you can use "export EDITOR="code -w"

Check to make sure that this has set correctly (from the command line, echo $EDITOR), and then go back into your Amazon Q CLI session.

First, from the ">" prompt, set the right profile ("/profile set customer-survey-project") to use.

Now run "/editor" which should bring up your editor. Paste the prompt above into this file, and then save/edit. If you are using vim, press ESC, then write ":wq!".

You should now see Amazon Q CLI start to work. As it works, you will need to review Tools that it wants to use (for example, writing to your file system). Respond "t" to trust and save you from having to enter this every time.

For the next 10-12 minutes, you should see Amazon Q CLI start to write this application. After a couple of minutes making sure that it is making progress, it is a good time to go for a cup of tea!

When it finishes, Amazon Q CLI provides a summary of what it has done. Often it will put this in a README.md file. Scroll back through the terminal to review the summary and compare what it has created against some of the context files we provided.

**Task 05**

In this task we are going to see how we can use Amazon Q CLI to help us run the application. In doing so, if it encounters errors, it will be able to resolve these and then iterate on the code.

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
 * Running on http://127.0.0.1:5000
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 100-674-860
```

Open up a web browser and enter "http://127.0.0.1:5000" and see what happens.

With luck you should be able to view our Customer Feedback application. If this does not happen, and you get an error, then you can use Amazon Q CLI to help troubleshoot (see the next section). If it does load up ok, then try the following:

* Register a user
* Login using that user
* Create a survey

You can stop the application at any time by pressing CTRL + C.

*Troubleshooting*

First stop the application if it is running by pressing CTRL + C.

Review the error in the logs. And the use that within the chat session. Use prompts such as the following:

* "When doing xx with this application, I get the following error -  {paste the error} "
* "What does error {paste error} mean"

You can try your own variations, but the key to getting good output is to frame what you were doing, together with the error message. Sometimes you might need to include a lot of the additional information in the error, other time just the error is suffucient.

**Task 06**

Before we finish this lab, take some time to review your context usage. From the ">" prompt, enter "/usage" and review the output.

---

**Summary**

In this lab we have seen how we can use Amazon Q CLI, together with profiles and context to help us build an application within minutes.

You can [proceed back to the README](/README.md) or try the next lab, [05-Automation](/workshop/05-automation.md)