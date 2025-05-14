![Amazon Q Developer header](images/q-vscode-header.png)

## Introduction - Next Generation Developer Tools

*A hands on guide to working with Amazon Q CLI. Made by DevRel with 💖.*

This is a hands on tutorial that will help you explore what the future of software development looks like. We are going to run through how to use the next generation of developer tooling (Amazon Q CLI) to help make our jobs as developers more enjoyable and productive.

> **You do not have to have an AWS account or be an AWS user to go through this tutorial**. 
 
This hands on tutorial is split into a number of different labs which you can run either within a controlled workshop, or at your own pace. Feel free to contact me if you run into any issues.

**What is Amazon Q CLI?**

How often to you use your terminal or shell? As a developer, I spend probably most of my time in the terminal when I am not writing code in my IDE. If you are not a developer, maybe you work as a sysadmin, or perhaps a devops engineer, you might spend all of your time in the terminal - writing scripts to automate stuff, running various tools to help with your job, and helping to fix things when they go wrong. The terminal is an essential part of our job, and so we need to make sure we are 'command line confident' as I like to say.

To many folk though, using the command line can be daunting, for many reasons.

Trying to remember which commands are available to do certain tasks. Trying to recall the different command line parameters that command line tools require (as they change all the time). When you run them, if they generate messages understanding how to troubleshoot or change to get the command running, especially when they have sometimes hard to understand error messages.

If only we could get some help with this.


[Amazon Q CLI](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line.html?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el) is [an open source tool](https://github.com/aws/amazon-q-developer-cli) that you can install on your machine that brings IDE-style autocomplete and agentic capabilities to your terminal. It features:

* 🔮 Auto Completion: IDE-style completions to hundreds of popular CLIs like git, npm, docker, and aws.
* 💬 Natural Language Chat: Interact with your terminal using natural language to ask questions, debug issues, or explore the codebase.
* 🧠 Contextual Awareness: Integrates context from your local development environment, so answers are tailored to your specific code and setup.
* 🤖 Agentic Execution: Let Amazon Q take action: generate code, edit files, automate Git workflows, resolve merge conflicts, and more — with your permission. 

![splash page for Amazon Q CLI](/images/q-cli-splash.png)

It is pretty awesome, and this workshop will help you get hands on with it so that you can learn the basics and then help you understand how you can apply this to your every day activities.

---

**What you will need**

To follow along this workshop, you are going to need:

* A Windows (with Windows Subsystem for Linux - wsl configured), MacOS, or Linux machine with the ability to install software
* A [supported terminal]() software ([most are supported](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line-supported-envs.html?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el))
* A supported shell - bash, zsh, or fish
* (optional) A code editing tool (Zed, VSCode, etc) - this will make it easier to work with files that are generated, but vim also works
* (optional) A container runtime tool for your machine - Docker, Finch, Rancher, Podman are all ok

You will doing the following as part of this workshop:

* Create a Builder ID account, which will allow you to use Amazon Q CLI for free
* Learn how to install, configure, and understand the many features of Amazon Q CLI
* Use Amazon Q CLI to build a new application from scratch

---

**Overview**

This workshop will first of all get you up and running with Amazon Q CLI - installing, configuration, and understanding the different parts that make up Amazon Q CLI. Once we have had some time to explore its capabilities, we will use that knowledge against a number of typical use cases where you might use it.


## 1. Installation and Setup

[Follow this link](workshop/01-setup.md) to get started with installing Amazon Q CLI. You do not need an AWS account to use Amazon Q CLI. You can use the free tier by [creating a Builder ID](https://s12d.com/builder-id), and then using that Builder ID to login.

---

## 2. Learning about and exploring Amazon Q Developer capabilities

Once we have Amazon Q CLI installed, setup, and configured, we are ready to explore its capabilities. In this lab we are going to go over the features in detail and provide your first opportunity to get hands on.

[Follow this link](workshop/02-getting-started.md) to explore the wonderful world of Amazon Q CLI, including getting hands on with some excercises.

---

## 3. Use Cases

Understanding how and where you can use tools like Amazon Q CLI is the topic of this next section. [Follow this link](workshop/03-use-cases.md) where we will explore some ideas on how to do this.

---

## 4. Writing code with Amazon Q CLI 

In this lab we are going to use some of the things we have learned to help use write some code using Amazon Q CLI.

[Follow this link](workshop/04-writing-code.md) to get started.

---

## 5. Automation

In this lab we are going to look at sysadmin and automation use cases, exploring some of the ways you can use Amazon Q CLI in your day to day activities.

[Follow this link](workshop/05-automation.md) to start diving into automation.

---

## 6. Understanding and debugging

In this lab we will explore how we can use Amazon Q CLI to help us when things go wrong, as well as help us understand applications or code we are working with.

[Follow this link](workshop/06-understanding.md) dive into this lab.

---

## 7. Wrap up 

I hope during this hands on tutorial you have learned 

Before you go, if you found this tutorial useful/helpful, or have ideas how we can make it better, please please provide some feedback using [this link](https://pulse.aws/survey/1DM5TAZU) and as a thank you, I share some additional resources from some of my talks on Amazon Q Developer.
