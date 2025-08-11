![Amazon Q Developer header](/images/q-vscode-header.png)

## Understanding Codebases

**Overview**

In this lab we are going to build upon the project we have been building, adding some documentation to help folk who come after us to understand what this codebase does.

**Task-01**

Amazon Q CLI is a great tool to use if you are trying to explore a codebase that is new to you. You can use it to explain the entire project, or dive into specific parts, functions, or classes. Lets take a look at some prompts that we might use.

First from a new terminal, delete the README.md file in the project directory.

Start Amazon Q CLI if you are not already in a session, and from the ">" prompt enter the following:

```
> Explore the codebase in the current project workspace. I want you to explain the key architecture components of this application, and then walk me through how it works.
```

Follow the output and what its doing. You should see it reading the files into the current project workspace.

You can dive into specific details of how the code works, for example type this in:

```
> can you provide indepth and deep explaination of how the authorization works in this codebase. Go level 500
```

Review the output. It should walk you through in great detail how authorization works for this codebase. Imagine you were new to this codebase, how useful do you think this would be?

At the end of the review, did it also suggest areas of improvement?

You can use this approach for your own codebases. There are some things that you should be aware of. From the ">" prompt, enter "/usage" to view your context window and the available tokens you have. For this small codebase, its not going to be an issue, but if you are working on very large codebases, you are going to need to scope down where Amazon Q CLI looks or you might exceed the limits.

---

**Task-02**

They say a picture is worth a thousand words, and when look at codebases nothing beats having architecture, entity relationship, or sequence diagrams to provide a quick overview of how the application works. You can use Amazon Q CLI to generate these for you, so lets look at that now.

From the ">" prompt in Amazon Q CLI session, enter the following prompt:

```
Generate an ERD for the data model in this application. Create it in a new mardown document called "data-model.md"
```

> Whilst you can view the output in the terminal, it is best viewed using a markdown viewer. I use the [Enhanced Markdown Viewer](https://marketplace.visualstudio.com/items?itemName=shd101wyy.markdown-preview-enhanced) extension in VSCode as it displays Marmaid diagrams very nicely indeed. Check out how it displays the output of the above.

![example ERD diagram](/images/q-cli-erd.png)

Next lets see how it copes with generating UML sequence diagrams. Try this prompt:

```
> Generate a UML sequence diagram for the user registration and login process. Add this to the README.md file.
```

Again you will need to use an enhanced markdown viewer to view these diagrams. This is what it produced for me.

![example of UML sequence diagram](/images/q-cli-uml.png)

---

**Task-03**

Lets do something a little different this time. Open up a new terminal, and create a new temporary directory.

```
mkdir qcli-oss
cd qcli-oss
```

And we will now clone the Amazon Q CLI GitHub repo into this directory, and then change into this directory.

```
git clone https://github.com/aws/amazon-q-developer-cli.git
cd amazon-q-developer-cli
```

From this new terminal start Amazon Q CLI. Lets use this to explore and understand this project. When MCP support was added to Amazon Q CLI, it was not clear how to configure the mcp.json file, so I actually used Amazon Q CLI to help me. This is the prompt I used, so try this for yourself.

```
> Can you review the code and tell me how to configure mcp.settings
```

You will be asked to trust the tool, so respond with "t" and then watch as it walks through the codebase. This is the output it generated for me, and I was pretty happy with this.

```
The MCP configuration file should follow this structure:

json
{
  "mcpServers": {
    "server-name": {
      "command": "path-to-executable",
      "args": ["optional", "command", "arguments"],
      "env": {
        "ENV_VAR1": "value1",
        "ENV_VAR2": "value2"
      },
      "timeout": 120000
    }
  }
}


Where:
• server-name: A unique identifier for the MCP server (must follow the pattern:
^[a-zA-Z][a-zA-Z0-9_]*$)
• command: The path to the executable that implements the MCP server
• args: (Optional) Command line arguments to pass to the executable
• env: (Optional) Environment variables to set when running the executable
• timeout: (Optional) Timeout in milliseconds (defaults to 120000 ms or 2
minutes)

### Example Configuration

Here's an example of what an MCP configuration file might look like:

json
{
  "mcpServers": {
    "myTool": {
      "command": "/usr/local/bin/my-mcp-tool",
      "args": ["--verbose"],
      "env": {
        "API_KEY": "your-api-key"
      },
      "timeout": 60000
    }
  }
}
```

---

**Task-04**

Next we are going to ask it a question I had recently, which was what does qterm do? This is part of the Amazon Q CLI distribution, and I was not sure exactly what it does, so from the ">" Amazon Q CLI session, I entered the following prompt:

```
> Review the codebase and explain what qterm does
```

Review the output - do you now understand what qterm does?

---


**Summary and clean up**

In this lab we used Amazon Q CLI to help us understand code, whether that was ad hoc questions we have about how it works, or how to use it to document our projects. This is the last lab and so you should look to clean up resources created. You should:

* delete the project directory we created ("aqcli-app")
* review and remove as needed any Amazon Q CLI configuration files (mcp.json for example)

You can [proceed back to the README](/README.md) and grab some follow up resources, or if you have an AWS Account, why not try the next lab, [07-Working with AWS](/workshop/07-working-with-aws.md).

