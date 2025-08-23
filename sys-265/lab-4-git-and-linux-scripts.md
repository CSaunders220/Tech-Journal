# Lab #4: Git and Linux Scripts

## Overview

This lab explored two primary topics, Git and SSH. The first part of the lab required the use of Git to move and create files for a simple application on docker using docker-compose. This allowed for the creation and storage of the configurations and the files needed for quick setup across machines.&#x20;

## Basic Setup

For windows machines, you can install GIT through the web installer which uses an .exe to install the service and for Linux environments you can use one of the two following commands depending on the distro of Linux and what package managers you have installed:

```
sudo dnf install git-all
//or
sudo apt install git-all
```

Once installed, you can test for a successful install by running the command git --version

After installing, setting the configs up for the user are critical so that you can be sure that you are pushing and pulling to your GitHub account. You can ensure this by running the two commands below and inputting your GitHub username and email:

```
git config --global user.name "<Username>"
git config --global user.email <Email>
```

If desired, you can also configure the default branch but this is not necessary for running a push or pull.&#x20;

## Using Git

First and foremost, you want to navigate to the local machine file path in terminal of where you want to have your GitHub files and all of the changes to be occurring. While editing the GitHub project it is critical that you are doing it in the right directory. Once there, you can run a "git init" to select this file as a starting space for your git projects.

At this time, if you wish to take a repository that you already have on GitHub, you can run a git clone followed by the GitHub link to the repository that you wish to copy and it will copy all files from that repo into your working directory.&#x20;

To track the status of the git repository and to see what has been synced to the GitHub repo, use the command git status.

Any files created in your working directory will be committed to the repo once the command git commit followed by git push are run. To add any files to this list of files that are being committed, use git add.

It is also possible to ignore files that you don't want to have synced by creating a .gitignore file. Any file names in this file will be ignored and not synced to the GitHub repo.
