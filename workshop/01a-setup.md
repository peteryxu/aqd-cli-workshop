![Amazon Q Developer header](/images/q-vscode-header.png)

## Setting up, and getting starting with Amazon Q CLI

We are now ready for the first hands on part of this lab, installing and setting up Amazon Q CLI. In this lab we are going to:

* Register a Builder ID which we will need to use Amazon Q CLI
* Download and install Amazon Q CLI
* Explore configuration options


### Creating your Builder ID

(If you already have a Builder ID, you can skip this step)

[Creating a Builder ID](https://s12d.com/builder-id) is the first step in being able to use Amazon Q CLI. All you need is a valid email address to create a Builder ID. Head over to the [Builder ID page](https://s12d.com/builder-id) and click on the "Sign in with Builder ID". This will pop up a browser window where you can now create your Builder ID, using your email address. 

You can follow these screenshows to see the flow. After accessing the Builder ID page, you will need to provide an email address (1) and then create an alias (Your Name) (2). You will be sent an email verification which you will need to enter (3), and the email should only take a few moments to arrive (but check your SPAM folder just in case)(4)

![creation of a builder id flow](/images/q-vscode-builderid-1.png)

Once you have received that code, enter it to validate your account (5), which should provide confirmation (6). You will be returned back to the initial screen, where you can now enter your email address (7) and password (8) and you should then see the Builder Profile page (9), where you can view your profile.

![completion of the builder id](/images/q-vscode-builderid-2.png)

When you hear the term Builder ID when working with AWS services, this is the account they are referring to. It is separate from the AWS account, but is used by a number of services to provide access to those who want to try and AWS without the need for a full AWS account. For example, you can use it to use the Amazon Q Developer IDE plugins for VSCode, Jetbrains, and Eclipse.


> **Logging out of your Builder ID** Sometimes you might need to log out of your Builder ID, and in order to do this you should head over to your [Builder ID Profile page](https://profile.aws.amazon.com/#/profile/details?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el) and use the **Sign Out** button on the top left. Clicking on this will return you to the sign on page.

---

### Installing Amazon Q CLI

Now that we have our Builder ID we can download, install, and then configure Amazon Q CLI. Before we do that, you need to talk about terminals and shells.

**Terminals and shells**

The command line is a text based user interface that you can use to interact with your system. You can run commands to make the system do something – run a tool, start an application, edit a file. These commands interact with the computer operating system, and nearly all computer operating system has some kind of command line.

When you use the command line, you use a piece of software called a terminal. When you open up that terminal it will run something called a shell (whether that is Bash or Zsh on Linux/Mac or command prompt or Power Shell on Windows).

> * What is a terminal - it is a piece of software where you can run text based commands that interact with your operating system. Every OS has these as part of its distribution, but you can also install third party ones. In fact, these are super popular
>
> * What is a shell - A shell is the program that interprets those commands and executes them. Think of the terminal as the display, and the shell as the translator and processor of commands within that display.

Amazon Q CLI runs in a terminal, configured and enabled via the shell. It supports most terminal applications (for example iterm2, Ghostty, tux - you can check out the source code for the complete list), and supports three shells today: bash, zsh, and fish.

---

**Different installation options**

**Windows**

To install Amazon Q CLI on Windows, you will need to use Windows Subsystem for Linux (wsl). I have put together an installation guide you can follow - [Installing Amazon q CLI in wsl](https://dev.to/aws/the-essential-guide-to-installing-amazon-q-developer-cli-on-windows-lmh)

> **Note!** Whilst there is currently no native Windows installer, because Amazon Q CLI is an open source project you can compile the source to generate your own binaries. You can see an [example](https://github.com/DiscreteTom/amazon-q-developer-cli-for-windows) of this [here](https://github.com/DiscreteTom/amazon-q-developer-cli-for-windows). I have not tried this compiled executable of Amazon Q CLI. Before you download and try it for yourself, make sure you follow your existing security procedures and policies. 

**MacOS**

If you have a MacOS, then the simplest way of installing Amazon Q CLI is via homebrew using the following command:

```
brew install amazon-q
```

There also a [binary distribution you can download](https://desktop-release.q.us-east-1.amazonaws.com/latest/Amazon%20Q.dmg?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el) and then install if you prefer.


**Linux options**

There are many flavours of Linux, so you will need to determine which installation approach works best for you. There are two types of installation - full and partial. The full installation installs Amazon Q CLI chat capabilities as well as a GUI control panel and graphical autocomplete. The minimal installation provices access to just the text based Amazon Q CLI chat capabilities.

* Full installation - there are debian packages available that work for Ubuntu 22 or later, or there is an AppImage available which provides an easy way to run Amazon Q CLI. Follow the [instructions in this page](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line-installing.html?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el) to walk you through those. You will need a GUI Linux system to use this.

* Minimal installation - you can alternatively download and unzip Amazon Q CLI from a zip file. Follow the [installation guide](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line-installing-ssh-setup-autocomplete.html?trk=fd6bb27a-13b0-4286-8269-c7b1cfaa29f0&sc_channel=el) to walk through installing Amazon Q CLI. This approach works on any Linux system including headless (remote) systems.

---

> **Compiling from source**
> 
> You can compile Amazon Q CLI from source if you want to build and run your own binaries. This is outside the scope of this workshop, but check out the "Supporting Resources" below for a blog post that walks you through the process.
> 

---

**Logging in**

The final step after we have installed Amazon Q CLI is to now login using the Builder ID we created earlier. From the command prompt we do this by entering:

```
q login
```

which will bring up a menu from the terminal

```
? Select login method ›
❯ Use for Free with Builder ID
  Use with Pro license
```

Select the first option (Use for Free with Builder ID) and this should then launch a browser window (on a headless system it will provide you with a web url which you can use to login), prompting you to login with your Builder ID.  

```
? Select login method ›
❯ Use for Free with Builder ID
  Use with Pro license

Confirm the following code in the browser
Code: PNDN-QVKB

Open this URL: https://view.awsapps.com/start/#/device?user_code=PNDN-QVKB
▰▰▱▱▱▱▱ Logging in...
```

Accept the pop ups that appear, and once you have completed those steps (it was say you can now close that browser window), returning to the terminal you should now see

```
Logged in successfully
```

You can watch this process in this short video.

[![Logging into Amazon Q CLI with Builder ID](/images/q-cli-builder-id-login.png)](https://youtu.be/WuInQ2Urmow)

**Additional login options**

The command line login supports a number of additional arguments that provides you with some fine grain control of how to manage the login process:

* **"q login --licence pro"** or **"q login --licence free"** to determine which kind of login you want to use
* **"q login --use-device-flow"** - sets loging to always use the OAuth device flow for authentication. Useful for instances where browser redirects cannot be handled

When you are using the **"q login --licence pro"** you will need to add two additional arguments:

* "--identity-provider {URL}" - the Identity provider URL for your AWS Identity Center
* "--region {REGION}" - the AWS region for your Idententy Centre


**Logging out**

You can log out at any time by running the following command:

```
q logout
```


---

**Confirming installation**

To confirm that Amazon Q CLI is installed correctly, you can use the "q doctor" command. Run it in a new terminal window, and if everything is configured correctly you should get something like

```
q doctor

✔ Everything looks good!

  Amazon Q still not working? Run q issue to let us know!
```

If you run into errors, then Amazon Q CLI will first try and fix anything that it thinks will fix the error. If things continue to fail, then check out the end of the "The essential guide to installing Amazon Q Developer CLI on Linux" blog post (see "Reference Section" below).

You can confirm the version you are running by typing the following:

```
q -V
```

which should produce the following. If you have a version OLDER than 1.9.1 then there is an issue, so make sure you check the installation method used. It is possible you have used an old binary perhaps.

```
q 1.13.1
```

---

**Updating Amazon Q CLI**

You can update Amazon Q CLI to the latest version by running the following command, which will check for updates.

```
q update
```

 You will be prompted to upgrade (Yes/No) which will then install the latest version for you automatically (if you proceed).

![upgrade screen for q cli](/images/q-cli-upgrade.png)

As Amazon Q CLI is an active project, make sure to check for updates and make use of the new capabilities as they are released.

You can also view the latest changelog from the command line by typing the following command:

```
q version --changelog
```

or if you want to dive into a specific release

```
q version --changelog 1.13.1
```

---

**Finding your Amazon Q CLI chat history**

Introduced in v1.16, when you use Amazon Q CLI, each command you enter will be stored in a history file that is located in **"~.aws/amazonq"** directory. This file is called **".cli_bash_history"** and you can view this to see all the previous prompts and commands you issued.

---

**Upgrading to Professional Tier**

Registering and using your Builder ID with Amazon Q CLI is the quickest way to get started, but at some point you might come up against limits that the generous free tier provides. What do you do then?

The good news is that you can easily upgrade to Professional Tier from the command line, using the **/subscribe** command within your Amazon Q CLI session. **You will need a valid AWS account**.

Check out this short video on how this works.

![Upgrading from Free Tier to Professional Tier using subscribe](/images/q-subscribe.gif)

What has just happened? Using /subscribe creates a new entry in your AWS account's Amazon Q Developer subscription panel which you can see in the following screenshot.

![AWS Console looking at Amazon Q Developer subscription settings](/images/q-subcriptions.png)

If you want to cancel this at any time, select the Builder ID and then use the "Unsubscribe" button, which will revert this Builder ID to a standard Builder ID account.

> **Things to be aware** Upgrading your Builder ID to Professional Tier will charge your AWS account $19 (as of writing, Jun 25) per month. You will see this on your AWS Bill. You can use AWS Credits against this usage if you have them.


---

### Supporting Resources

Some additional reading material that dives deeper into this topic if you want to explore:

* [The essential guide to installing Amazon Q Developer CLI on Windows](https://dev.to/aws/the-essential-guide-to-installing-amazon-q-developer-cli-on-windows-lmh)
* [The essential guide to installing Amazon Q Developer CLI on Linux (headless and desktop)](https://dev.to/aws/the-essential-guide-to-installing-amazon-q-developer-cli-on-linux-headless-and-desktop-3bo7)

---

### Completed!

You can optionally dive deeper into [more advanced setup options](/workshop/01b-advanced-setup-topics.md).

Now that you have set everything up, we can proceed to the next step which is [exploring how to get started with Amazon Q CLI](02a-getting-started.md)
