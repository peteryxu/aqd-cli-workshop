![Amazon Q Developer header](/images/q-vscode-header.png)

## Automation

**Overview**

In this lab we are going to build on the application we have just built, and automate the build and packaging of this application so that we can deploy it via a container image.

**Task 01**

The code we built in the previous lab is great for our local setup, but we need to change this if we want to run it on a server somehwere. Lets use Amazon Q CLI to help us update the application.

From the ">" prompt, enter the following prompt:

```
> Update the project so that it uses gunicorn to run.
```

Follow along and after a few minutes you should have an updated project that now allows you to run this application locally as well as via gunicorn.

**Task 02**

We are going to containerise this application, so in preparation for this lets ask Amazon Q CLI to help generate an entrypoint.sh script.

```
> This project is going to run via containers. Create an entrypoint.sh script that I can use.
```

Follow along and review the script that is created. Given the non deterministic nature of how tools like Amazon Q CLI work, you might find that it only creates this script. More likely is that it will also create supporting Dockerfile and even Docker Compose files in your project.

**Task 03**

Our next step is going to build a script to automate the building of this container image. 

From the Amazon Q CLI ">" prompt, I run the following prompt.

```
> Create a container build script that automates the task of building and tagging the container image build. Pass in as an argument to this script the tagged release version (for example, 1.0.0, 1.0.1, 1.1.0, etc). The script should automate the building of the container image so that it builds against x86 and arm based architectures.
```

> **Note** I do not use Docker and use Finch, so I added another line to this prompt - "I am not using Docker but Finch" - if you are using something else like Podman, Rancher, etc you can try adjusting the prompt in the same way.

Review the output, it should not take that long. Review the output. Again, what you get might be different to other people who are running this workshop. When I ran this, it generated the following:

* created a new script in the project directory (called "build-container.sh") which used (in my case) Finch to build multi architecture container images
* the script did also add functionality to publish this to a container repo
* it modified the application to include version details of the container image running

> **Note** As I was using Finch, I needed to follow up with another prompt ("can you update the build script so that you use Finch to build both amd64 and aarch64 container images") to get it to work and build a different architecture for me

Review the output. If you see code to publish to a container repository you can delete that as we will not be using that.

Try the script in a new terminal window and review the output - did it build ok? I have put the version of the script that it generated for me [here](/resources/build-container.sh) for you to look at (I am using Finch instead of Docker)

You could easily extend this script to ensure that you add tagging, or publishing this to a container repository like Amazon Elastic Container Registry (Amazon ECR). We are not going to do that in this lab, but you might want to try this yourself.

**Task 04**

Now that we have a script to build our container images locally every time we make a change, we are ready to move this to the next stage. We will ask Amazon Q CLI to help us with the creation of GitHub actions so that we can use GitHub actions to automate the builds for us.

From the same Amazon Q CLI session I enter the following prompt:

```
> " I want to use GitHub actions to automatically build the container image every time the code changes. Can you provide all the configuration details and update the project. Do not add tests."
```

After a few minutes, I now have a .github directory that container the required configuration for this automation.

We are not going to publish this to a git repo as part of this lab, however, if you want to try this for yourself then the steps are straight forward:

* initialise a new git repo in the current directory
* add files and then create a commit message
* push these to GitHub

You can then try and initialise the GitHub actions to build the project. If you run into errors, use Amazon Q CLI to help you troubelshoot. For example the first time I uploaded the repo, it generated authorization issues as it was trying to push to a container repository I had no access to. Amazon Q CLI updated this to use the GitHub

If you want to see what my project looked like, you can [check out the GitHub repo here](https://github.com/094459/aqd-cli-demo)

---


**Summary**

In this lab we looked at automating some of the developer tasks with Amazon Q CLI, but we really only touched upon this topic. Any sort of automation, whether that is creating scripts to make your life simpler, or automating via other tools, Amazon Q CLI lowers the barrier for you to use those tools.

You can [proceed back to the README](/README.md) or try the next lab, [06-Understanding Codebases](/workshop/06-understanding.md)