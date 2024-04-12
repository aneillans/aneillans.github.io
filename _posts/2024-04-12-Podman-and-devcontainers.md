---
layout: post
title:  "Podman and Devcontainers"
date:   2024-04-12 12:00:00 +0000
categories: technology
---

There has been a lot of talk in the development community about the practice of devcontainers; that is, using small disposable containers for developing, proving and then ultimately colaboration during your development process in order to remove the need to install tooling on your machine.

A lot of these practices still insist on using (what will be argued as the industry leading tool) Docker as the runtime, however, Podman is gaining a large amount of inroads into the space so I wanted to actually properly try it out and see how far I could get.  Why specifically? If I'm honest, I have never been keen on Docker since they made their license shift a few years ago. 

For my testing I had the Azure Function App Core Tools installed (4.x latest version) and VS Core.

I created a folder called e:\test, then ran func new within it.
This, as always, prompts for the project type. In my case I selected dotnet (isolated process), C# and HttpTrigger.  This generates a template project for us to work with.
Once generated go ahead and open this project up in VS Code.

Before we really get started, if you are working with VS Code, here are some extensions you will probably want to install:

[C# Dev Kit](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit)
[Remote Development Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)

Note that any extensions you install will not be available when you connect to the Remote Devcontainer, unless you install it within the container instance, but I will likely post about that process another time.

After you have installed these extensions, before you do anything else, you will need to tweak a couple of settings.
So pop into File > Settings on VS Code.
Go to Dev Containers.
Scroll down to Dev > Containers > Docker Path. And change it from docker to podman.
(You leave docker-compose, as there is a compatibility extension for podman that emulates docker-compose)

That's all the configuration taken care of (assuming you have done the actual setup of Podman itself!)

Now, go back to your VS Code project. Pop up the Command Palette, and select the Add Dev Container Configuration Files option.
Given you have a C# Project, select the .NET option, then the 8.0-Bookworm sub options. No further options are needed, except for opting to place them into your Workspace - this will put the relevant config files into your project.
However, before you move on to kick the project into life, there's a change you need to make - uncomment the root user line.

After that you should be in a position to connect up the container and "open the project in the container" - this will start the container, copy your project over and restart an instance of VS Code for you to connect to.

Job done! No more needing to maintain all the dev tooling on your machine ...

### What next?

You can do a lot more with devcontainers / podman too - such as run Minikube under it! 

#### Create a minikube cluster

Set the driver to podman
Set the container runtime to containerd or cri-o

