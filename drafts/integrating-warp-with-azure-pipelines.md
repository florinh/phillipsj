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

![](/images/warp-pipelines/NewPipeline.png)

![](/images/warp-pipelines/RenamePipeline.png)

![](/images/warp-pipelines/RenamedPipeline.png)

We search for *core* and just use the ASP .NET Core project to get us started.

![](/images/warp-pipelines/SearchAndSelectCoreTemplate.png)

![](/images/warp-pipelines/ApplyCoreTemplate.png)

We need to make some minor tweaks to get what we want, like not looking for *web* projects.

![](/images/warp-pipelines/ChangePublishedTask.png)

Not zipping up our publish folder and setting our runtime option on our publish.

![](/images/warp-pipelines/ChangedPublishTask.png)

Now run the build and validate it still all works.

![](/images/warp-pipelines/TestFirstBuildFirst.png)

There is a small warning, but we will take care of that in a second. Now that have used the default bootstrapping to create our initial build steps, let's make sure those execute without error before we continue. Remember, build these steps up little by little to make sure you don't have any issues.

![](/images/warp-pipelines/FirstBuildSucceeded.png)

With a working build let's add the step that adds our Warp tool. We will add it at the beginning of the build, no sense in continuing if we can't install it.

![](/images/warp-pipelines/AddTask.png)

![](/images/warp-pipelines/WarpPackerToolInstaller.png)

![](/images/warp-pipelines/AddTool.png)

![](/images/warp-pipelines/MoveWarpTool.png)

![](/images/warp-pipelines/MovedWarpTool.png)

Now let's run to make sure everything is working.

![](/images/warp-pipelines/ToolBuildSucceeded.png)

Great, now to add the publishing Warp-Packer step. We first need to add a PowerShell Task.

![](/images/warp-pipelines/AddTask.png)

![](/images/warp-pipelines/AddWarpPackerTask.png)

The task will end up as the last step as shown below.

![](/images/warp-pipelines/MoveWarpPackerTask.png)

![](/images/warp-pipelines/MoveedWarpPackerTask.png)

We are going to move it above the *Publish Artifact* task and modify it. We will need to rename it, make it an *Inline* script and add the following:

```PowerShell
warp-packer --arch linux-x64 --input_dir src/pipelines-demo-app/bin/Release/netcoreapp2.1/linux-x64/publish --exec pipelines-demo-app --output pipelines-demo-app
```

![](/images/warp-pipelines/ModifiedWarpPackerTask.png)

Now all that is is left is to grab the executable created by Warp and add it to your artifacts. Adding a *Copy Files* task to move the executable to the artifacts directory so it gets bundled.

![](/images/warp-pipelines/AddTask.png)

## Download it and see if it runs

