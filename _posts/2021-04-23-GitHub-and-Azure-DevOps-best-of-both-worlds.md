---
layout: post
title: "GitHub and Azure DevOps: best of both worlds"
date: 2021-04-23
---

Ever since Microsoft acquired GitHub we have been looking at how this will play out in the long run with their Azure DevOps offering. We've seen a lot of people move from the Azure DevOps team to GitHub by looking at their LinkedIn or other social network updates, so it only makes sense to wonder about the future. We know that several **very large** teams inside Microsoft are using Azure DevOps, together with some really large customers, which means Azure DevOps will stick around for a long time and will still receive regular updates to its functionality. Azure DevOps has a very strong offering, from 'everything in one suite' to mix and match with third party tools, to a good hosting model (data sovereignty is a big thing for us Europeans) and excellent Azure support with even integrated billing on the marketplace. 

GitHub on the other hand is the largest dev-community in the world, with some [56 million plus](https://octoverse.github.com/) developers on it. If you want to entice developers to work at your company, doing so with the tools they already know and love is an excellent selling point. GitHub is made by developers, for developers. This also gives them a lot of information from open source communities into their activity, languages used and even a great host of information they can pull from the open source code stored on the platform. Their now even hosting [security advisories](https://github.blog/changelog/2019-05-23-maintainer-security-advisories/) and enable scanning for vulnerability patterns in your codebase with [CodeQL](https://github.blog/2021-03-16-using-github-code-scanning-and-codeql-to-detect-traces-of-solorigate-and-other-backdoors/). 

# Exploring best of both worlds
In this post I wanted to share what are the strengths of both offerings and explain what parts I'd use of each of them and why. In my opinion both products have some unique features that the other doesn't have (yet), so using the right tool for the right job is only logical from that.

I've split things up in the following sections:
* Source control
* Work tracking
* Pipelines
* Artifacts
* Security features

# Source control
In the basis, source control on both platforms is very similar: Azure DevOps still has [TFVC support](https://docs.microsoft.com/en-us/azure/devops/repos/tfvc/what-is-tfvc?view=azure-devops&WT.mc_id=DOP-MVP-5003719), but I really don't recommend using that at all: Git is the standard these days and for good reasons (that I'll skip over for this post 😁).  
Both platforms have branch tracking, pull requests, a branch protection system, a good CLI to work with, so choosing what to use here is comes down to the extra features on top of the basics. That is where GitHub has the better options in my opinion because it offers these features:
* GPG commit signing
* [Branch protection rules](https://docs.github.com/en/github/administering-a-repository/managing-a-branch-protection-rule) with GPG commit signing
* Out of the box security features like code scanning

## GPG Commit signing
Commit signing is an extra layer of security on top of basic authentication. Without using commit signing it is rather easy to make some code changes for you, commit them locally to the repository and then push them upstream. I've automated parts of this lots of times know, so I know how easy it is to create. Running the software on you machine, scanning the repos and adding some malicious stuff to your projects is rather easy and you would not even know that it was happening right under your nose 😄. What's even worse, I could even run this on my own machine and push in code changes with **your** email address and user name attached to it. The configuration in Git is just meta data that is not verified anywhere in the chain: GitHub only checks if the user pushing the changes has write access to the repository and then lets the changes in. This is of course the standard for all source control systems using Git.

With [commit signing](https://www.gnupg.org/gph/en/manual/x110.html), you need to sign the commit with your private key that only you should have. By default, that key can only be used together with a passphrase (you can create it without a passphrase, but that would defeat its purpose). Adding the passphrase means that when you sign the commit, you have to type in the passphrase. There is no way to automatically fill in the passphrase for you (that I know of). This also means you cannot sign the commits automatically, which means someone cannot spoof your signage on your behalf.

The server side (GitHub in this case) will store the public part of your GPG key that they use to verify the signed commits: those are made by users with GPG signatures AND those signatures have been verified with their accompanying public key. This enables you to lock down the repository or specific branches in the repository to only allow verified commits.  
![Screenshot of GitHub.com showing a verified commit](/images/20210423/20210423_VerifiedCommit.png). 

# Work tracking
Work tracking has always been great in Azure DevOps and I think it still is the better offering. GitHub has several options to enable some of the same setup, with great integration into source control with [issues and pull requests](https://docs.github.com/en/github/writing-on-github/autolinked-references-and-urls#issues-and-pull-requests). You can even add subtasks to an issue with [task lists](https://docs.github.com/en/github/managing-your-work-on-github/about-task-lists), although I like the Epic setup with subtasks better from [Azure Boards](https://azure.microsoft.com/en-us/services/devops/boards?WT.mc_id=DOP-MVP-5003719).

## Available in both offerings
Let's look into what is available in both offerings: 
* Rich text setup (with markdown) that includes emoji, gifs and even video's in most fields.
* Easy way to create new items (even with CLI and API's)
* @Mention other users, issues or pull requests in the text of a work item / issue
* Discussing on the work that needs to be done
* Notification system for items that mention you
* Labeling and searching on them

Note: what GitHub doesn't have is the whole formal setup with approvals, adding in extra fields, adding story points (and summarizing them). Some of it can be replicated if you want. Personally I don't like all that extra stuff, it's unnecessary process ceremony in an effort to feel in control over the agile process, while it only brings extra gates to jump through without adding the correct value. That might be a different blog post 😄.

## GitHub's Projects vs Azure Boards
GitHub has created [project boards](https://docs.github.com/en/github/managing-your-work-on-github/managing-project-boards) in an effort to give us more control over issues then just the list it is by itself. It basically adds a kanban setup to track the issues through different stages. By default this is just 'To do', 'In progress' and 'Done'. This works great in an [OSS](https://en.wikipedia.org/wiki/Open-source_software) project where you don't operate in regular sprints (who knows when you have the time and energy to work on things). If you are working in an organization that has sprints, you need something to define them. That currently isn't available in GitHub: what they've created for it is [Milestones](https://docs.github.com/en/github/managing-your-work-on-github/about-milestones). 

By adding an issue to a milestone, you can track their state over a longer time period and view how much work is still left before the milestone is reached. The general idea comes again from the OSS where this works great: you create issues for things you find or features you want to add, mark them as part of a milestone and work together towards that goal.

You can use them for sprints as well, where a milestone can be seen as a sprint and (open) issues would be added to a milestone. That way they can be tracked over time and be used for planning. Only thing I don't like about this setup is the naming: a milestone is not a sprint: usually what we see as a milestone can easily take multiple sprints to achieve. 

On the other hand: I more and more dislike the idea of sprints: we humans are not meant to keep on sprinting all the time: from that we experience time like a treadmill where we run until we cannot run any longer. I feel like we should be working towards a milestone, with short iterations, delivering updates to production as soon as they are done and then enable the new features to the user when we feel like it is working 'good enough'.

In conclusion: we should be able to work with Milestones if have a simpler process, skip things like story points and think differently about how we work as a team.

# Azure Pipelines vs GitHub Actions
Automation for Continuous Integration (CI) and Continuous Delivery (CD) is available in both platforms. Azure DevOps has the benefit of being available for years now, whereas GitHub Actions only went GA for GitHub Enterprise in the beginning of 2021. GitHub Actions is catching up fast, but it is still catching up.  

Azure DevOps has a robust and powerful setup for CI/CD: you can use the classic pipelines or yaml pipelines (preferred) and mix and match after your own preference. You can have a CI pipeline for fast validation on a branch, and a more extensive one for creating the artefact that you want to deploy. There are lots of extensions available on the [marketplace](https://marketplace.visualstudio.com/azuredevops), so you don't have to reinvent the wheel. I just checked my Twitter bot [@AzDoMarketNews](https://twitter.com/azdomarketnews) and there are currently 1630 extensions available, with new ones still being added every once in a while.

Big things that are currently missing on GitHub Actions are:

* Templates
* Security scans on GitHub Actions
* Better CI/CD story


## Templates
The ability to use a template to group steps so you don't have to repeat them everywhere. Think of a common template to use for an NPM pipeline for example: often you want to run the same steps in there, like dependency scanning, linting, building and running tests. GitHub already has a setup for [composite actions](https://docs.github.com/en/actions/creating-actions/creating-a-composite-run-steps-action), but you cannot include other actions in them, only shell scripts. Same thing goes for defining something like a 'stage' in a deployment pipeline: I want to deploy my artefact on Dev/Test in the exact same way as to Production, with only some parameter changes targeting the correct cloud environment: currently, you have to repeat the same steps in both stages of the deployment. Adding a step in between means adding them in multiple places, or you risk drift between the environments.

## Security scans on GitHub Actions
Currently there is no real way to trust the actions you use, other then trusting their publisher. In Azure DevOps there was a process to get an action published on the marketplace that at least included some [protective scans](https://docs.microsoft.com/en-us/azure/devops/extend/publish/overview?view=azure-devops#publish-an-extension&?WT.mc_id=DOP-MVP-5003719) before the extension was available. You also had to create a publisher account and only if you had the correct [permissions](https://docs.microsoft.com/en-us/azure/devops/marketplace/how-to/grant-permissions?view=azure-devops&?WT.mc_id=DOP-MVP-5003719) you could install the extension into your tenant, where it would become available for everyone in the organization to use.

For GitHub Actions the story is a lot simpler, which also makes it less secure. The work to verify what the extension is doing, and keeping up to date with them (using the latest version is against the best [practice](https://rajbos.github.io/blog/2021/02/06/GitHub-Actions-Forking-Repositories)) can take up time for your team. **Anyone** with write access to the repository can include new actions in the workflows (the thing that executes your actions). **Anyone** with a public GitHub repo can create an action.yml file in their repository and you can use it in your workflow. Without any validation by GitHub. Therefore it is really important to lock down your organization and think about the actions you want to allow. It is critical to have a [process](https://rajbos.github.io/blog/2021/02/06/GitHub-Actions-Forking-Repositories) around GitHub Actions to still enable the DevOps engineers to run them, but in the safest way possible.

## Better CI/CD story
With GitHub Actions you have two options for CI/CD: 
1. Create one yaml file with both CI/CD in them
1. Create a yaml file for CI and a separate file for CD


# Artifacts
Docker images

# Security features