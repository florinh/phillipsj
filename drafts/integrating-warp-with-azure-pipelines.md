---
Title: "Integrating Warp with Azure Pipelines"
Published: 11/02/2018 20:52:41
Tags: 
- Open Source
- .NET Core
- Microsoft And Linux
- Tools
- Azure
- Azure DevOps
---
# Using Warp with Azure Pipelines

I wrote a post about using [Warp](https://www.phillipsj.net/posts/warp-single-executable-dotnet-core-app) to create a single executable for .NET Core apps. That is nice that you can do it manually, but let's turn it up a notch my integrating it into Azure Pipelines. To make this easier to retrieve Warp on Azure DevOps build agents, I created a extension called [Warp Tools](https://marketplace.visualstudio.com/items?itemName=JamiePhillips.WarpTools) that you can install in Azure DevOps that makes it easy to install Warp on your build agents as a tool. Currently that is the only functionality, but I am wanting to add more features going forward.

## Install Warp Tools Extension

Go to the [Visual Studio Marketplace](https://marketplace.visualstudio.com) and select the *Azure DevOps* tab. 

![](/images/warp-pipelines/marketplace.png)

Enter *Warp Tools* into the search box and hit enter.

![](/images/warp-pipelines/searchwarp.png)

You should see the following results, click on *Warp Tools*.

![](/images/warp-pipelines/selectwarp.png)

Now just click on the *Get it free* button and follow the remaining directions. If you are not an administrator in your Azure DevOps subscription, just let your admin know you put in a request.

![](/images/warp-pipelines/getitfree.png)

## Creating the Build

I have created a [repo](https://github.com/phillipsj/pipelines-demo-app) in GitHub that has a basic .NET Core project. We can use this as a great way to demonstrate how we can create a build using Warp.

Let's create a build from that repo. The selection made will be in the following screenshots. 

image

We search for *core* and just use the ASP .NET Core project to get us started.

image

We need to make some minor tweaks to get what we want, like not looking for *web* projects.

image

Not zipping up our publish folder and setting our runtime option on our publish.

image

Now run the build and validate it still all works. There is a small warning, but we will take care of that in a second.

image

Now that have used the default bootstrapping to create our initial build steps, let's make sure those execute without error before we continue. Remember, build these steps up little by little to make sure you don't have any issues.

image

With a working build let's add the step that adds our Warp tool. We will add it at the beginning of the build, no sense in continuing if we can't install it.

image

Now let's run to make sure everything is working.

image

Great, now to add the publishing
Add Warp packer step.

Grab it as an artifact.

## Download it and see if it runs

