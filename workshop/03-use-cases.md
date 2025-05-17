![Amazon Q Developer header](/images/q-vscode-header.png)

## Exploring Use Cases

There are no labs in this section of this tutorial. We are just going to explore and look at some of the ways that you can use some of the features and capabilities within Amazon Q CLI.

If you have suggestions that we should add, please raise an issue/PR and we will review and update. I would love to see how folks are using Amazon Q CLI.

### Use Cases for Amazon Q CLI

So what use cases are best suited for using tools like Amazon Q Developer CLI. I have seen people using it for very broad and wide use cases, these are just a few examples.

* Automation - whether its writing bash scripts, or helping you to run your favorite cli commands, using Amazon Q CLI on the command line is a huge productivity boost. (check out [Making Makefiles for fun and profit](https://dev.to/aws/making-makefiles-for-fun-and-profit-kl6))
* Interacting with your AWS account - anything you can do using the aws cli, you can now get done with the help of Amazon Q CLI. Whether that is getting billing information about your AWS accounts, generating reports about your environments, or deploying workloads, Amazon Q CLI is a great helper.
* Writing code - whilst the other use cases have been more traditionally done in the command line, you can also use Amazon Q CLI to build full applications from the command line. (check out [Writing code with Amazon Q CLI and Zed](https://dev.to/aws/building-a-book-sharing-application-with-amazon-q-cli-5dl8))
* Vibe Coding - if you want to vibe code, then Amazon Q CLI can vibe with the best of them. From your prompts, let Amazon Q CLI build your projects and then run them, all in record time.
* Building games - a great way to learn coding is to write simple (or even more complex) games, and you can do that using Amazon Q CLI. (check out [How a 12 year old built a game with Amazon Q Developer CLI.](https://dev.to/aws/game-building-fun-with-a-12-year-old-and-the-amazon-q-developer-cli-2khg))
* Refactoring Code - have a code base that you want to refactor? Maybe want to go from PHP to Python? Then you can use Amazon Q CLI to refactor either parts of whole projects (check out [From PHP to Python - porting a Reddit clone with the help of Amazon Q Developer](https://dev.to/aws/from-php-to-python-porting-a-reddit-clone-with-the-help-of-amazon-q-developer-23g))
* Debugging issues - one of the strongest use cases that I use Amazon Q CLI for on a daily basis, is to help me troubleshoot and debug issues. From finding out how to fix different error messages, to reviewing logs, I am amazed how Amazon Q CLI lets me speed up this process.
* Documenting - writing project documentation, including creating diagrams, is something Amazon Q CLI can help you with. However, its not just limited to this. If you have a code base that has no documentation, you can use Amazon Q CLI to explain what the project does. When I was writing MCP blog posts, I needed to find out how logging and the configuration file worked. I asked Amazon Q CLI to explore the code base and answer these questions. After reviewing the output with the team working on the project, it got it spot on.

So these are just a few of the uses cases. Let me know if you are using it for something else.

### Use Cases for Profiles

Profiles allow you to switch between sets of contexts that give you unique ways for Amazon Q Developer CLI to interact with you and your systems. Here are some example scenarios where you might find these useful:

* **Language specific development** - Create separate profiles for Python, Java, JavaScript, etc., each with language-specific best practices and coding standards.
* **Role Based Workflow** - Maintain different profiles for when you work as a developer, architect, or DevOps engineer. Switch contexts based on your current role or responsibility.
* **Project separation** - Create distinct profiles for different client projects or internal initiatives. Keep project-specific requirements and documentation isolated.
* **Environment-Specific Development** - Maintain profiles for development, staging, and production environments. Include environment-specific configuration and deployment guidelines.
* **Technology Stack Separatation** - Separate profiles for frontend, backend, infrastructure, or data science work. Switch seamlessly between different technical domains.

There are probably more use cases where you can use Profiles, so look beyond this list - its just a starting point.

### Use Cases for Context files

Context files contain information like development rules, project details, or coding standards that Amazon Q uses to provide more relevant and tailored responses. Here are some example use cases where using them can provide significant benefits:

* **Project documentation** - Add README files, architecture diagrams, and project specifications. This will help Amazon Q understand the project's structure and purpose.
* **Coding standards** - Include style guides, formatting rules, and naming conventions. Ensures generated code follows your individual or organisational/team standards.
* **Architecture Guidelines** - Add system design documents and architectural decisions. Guide Amazon Q CLI to provide solutions aligned with your architecture.
* **Security Requirements** - Include security policies and compliance requirements. Ensure code suggestions follow security best practices.
* **API Documentation** - Add API specs, Swagger docs, or service contracts. Help Amazon Q CLI to provide accurate integration code.
* **Testing Requirements** - Include test strategies, coverage requirements, and QA procedures. Guide Amazon Q CLI to provide appropriate test code and strategies.
* **Managing tasks** - Breaking down large complex software projects into a set of tasks, and then tracking these via markdown documents which Amazon Q CLI can manage.
* **Capturing State** - For large projects when you start to reach the maximum of the context window, dumping the current state so that it can be re-used in susbsequent sessions will maintain understanding of what Amazon Q CLI has done before. 
* **Enforcing Organisational coding/technical standards** - You might want to your developers to use specific practices/boilerplate code/etc that are approved by your organisation.


The list of how I am seeing people use context files continues to grow, and is an area where you should be exploring and experimenting.

---

### Supporting Resources

**Managing your prompts**

We have seen that managing files that you use as context is as important as the prompts you use.

I have found managing these in markdown documents work well, and I managed these via a local repository that I used to store them based on the project I was working on. However, there is a better way, and I am beginning to use an amazing tool created by an AWS Community Builder, Christian Bonzelete, called [Promptz](https://www.promptz.dev/). 

You can login and save your own prompts, as well as see what prompts others have created and try these out. Even better, you can connect to the MCP Server for tools and prompts. We use this in the Getting Start lab for MCP Servers.

Check this incredible resource out - learn from the community, and be sure to share back too.

---

### Completed!

Congratulations, you have a good foundation in how to use Amazon Q CLI. 

The next lab will show you how you can [use Amazon Q CLI to write code](/workshop/04-writing-code.md)

