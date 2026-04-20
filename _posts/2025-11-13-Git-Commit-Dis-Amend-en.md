---
date: 2025-11-11 23:48:05
layout: post
title: 'Git Dis-amend: The Danger of "Rewriting" History'
subtitle: 
description: >-
  In this article, I show possible ways to abuse the behavior of the `git commit --amend` command.
image: >-
  /assets/img/Git-Commit/00.jpg
optimized_image: >-
  /assets/img/Git-Commit/00.jpg
category: Research
tags:
  - Git
  - GitHub
  - Paper 
author: squ4nch
paginate: false
toc: true
lang: en
---


# Introduction

Git is the main version control system used today and has an extensive list of commands for the most diverse purposes. One of these commands, `git commit --amend`, is normally used to replace the most recent commit, allowing for correcting errors or adding last-minute changes.

However, `git commit --amend` doesn't actually "rewrite" the previous commit. Due to the immutable nature of commits, what really happens is the creation of a new commit, causing Git to stop referencing the old commit in its place.

But what happens to that old commit? The answer is that it continues to exist. Although it is no longer shown in the branch's history, it remains accessible if someone knows its hash.

This article shows how this seemingly harmless behavior can be abused for malicious purposes.

# How Git Works

Git is an open-source version control system, designed to intelligently track changes in files, being extremely useful when multiple individuals are altering the same files simultaneously.

It allows multiple collaborators to create a branch from a copy of the main project, enabling them to edit files independently and safely. When making changes (commits) to files stored in a Git repository, the system automatically begins to manage and track these changes.

This way, users can pull the latest files from the remote repository and push their changes. Git is then responsible for intelligently merging this flow of changes back into the main repository, keeping everyone working on the most updated version.

## Difference Between Git and GitHub

Git is the version control system itself; that is, it is the software that tracks and intelligently manages changes to files. It is the technology upon which GitHub is built.

GitHub, on the other hand, is a cloud-based platform that offers a place to store projects, acting as a remote repository. In practice, when you send files to GitHub, they are stored in Git repositories, which causes Git to automatically start monitoring the changes.

Generally, Git is the software that deals with the history and merging of changes, while GitHub is the hosting and collaboration tool that uses Git to allow people to work together.

## git commit --amend

Git has an extensive list of commands, for the most diverse purposes. In the context of this article, I would like to highlight one of them, `git commit --amend`.

The `git commit --amend` command is used to replace the most recent commit on the current branch. Instead of creating an additional new commit, it "rewrites" it, staging the changes made (as if they had been added with `git add` or `git rm`). Git then prepares the new file tree (including the index content) and uses the original commit's message as a starting point, if a new message hasn't been specified with options like `-m` or `-F`.

The resulting new commit maintains the same parents and author as the previous commit, if the `--reset-author` option is not used. It is important to note that this command rewrites the commit history, so if you modify a commit that has already been published, you must understand the implications of this history rewrite.

Despite this change in the commit history, the `git commit --amend` command doesn't actually "rewrite" the last commit, because commits have an important characteristic: they are immutable. In practice, what happens then is that a new commit is created, and then Git stops referencing the old commit and starts referencing the new commit in its place.

# Using the "amend" Command for Malicious Purposes

Due to this characteristic I mentioned above, I started to wonder: what happens to the old commit? Does it stay in limbo? Is it possible to access its content?

And the answer is: yes! It continues to exist, it is possible to access its content (if you know the commit name), and it stays in "limbo" because, despite continuing to exist, it is not shown anywhere in the repository.

Then, the next question came: Could I use this in some malicious way?

Well, I thought of a few options and attack ideas abusing this behavior, let's go through each one.

## Monitoring to Retrieve Sensitive Information

In practice, a person will only use the `git commit --amend` command when they make a mistake; after all, it's not necessary to "rewrite" the commit if it's correct. This mistake could be something simple, or it could be the exposure of sensitive information, such as credentials or API keys.

So, one idea is to monitor a specific repository to track its commits in order to identify when a commit has been changed and then access it to try to find that information.

As a proof of concept, I simulated an accidental commit to my personal repository containing a secret message.

As can be seen, the last commit in the repository is "544b1f6...".

![](/assets/img/Git-Commit/01.png){: .align-center}

By accessing the commit's content, it's possible to see the message "secret stuff".

![](/assets/img/Git-Commit/02.png){: .align-center}

Then, I used a script to "monitor" this repository. It basically gets the content of the commits page and saves it in a file to be compared in the future.

![](/assets/img/Git-Commit/03.png){: .align-center}

After that, I used `git commit --amend` to create a new commit to replace the last one without the secret message.

![](/assets/img/Git-Commit/04.png){: .align-center}

Accessing the commits page now, it's possible to see that the last commit is "04cabeb.." and "544b1f6..." has simply disappeared.

![](/assets/img/Git-Commit/05.png){: .align-center}

In this new commit, the "secret stuff" message doesn't exist, so the "problem" has been fixed.

![](/assets/img/Git-Commit/06.png){: .align-center}

Using the script again, it is possible to compare the current content of the commits with the previously saved content, and it's possible to identify a change in the last commit.

![](/assets/img/Git-Commit/07.png){: .align-center}

In the information, we have the hash and the URL of each commit; although the 544b1f6... commit has "disappeared", it still exists and is simply not being referenced by git.

By accessing its URL, it's possible to recover the secret message again.

![](/assets/img/Git-Commit/08.png){: .align-center}

On the commit page, a message is shown informing that this commit is no longer part of any branch in the repository, but even so, it is possible to see its content.

This is just a simple POC, and the main idea here is to show a possible way to abuse this behavior. In a realistic approach, it could monitor specific targets daily (or hourly) and identify when they leak sensitive information in their repositories.

Limitations: you need to have access to the repository, so it will work with public repositories, and of course, with private repositories that you have access to.

## Data Exfiltration

Another possible way to abuse this behavior, following the same idea, is to use it for data exfiltration.

Consider the case of an insider who wants to exfiltrate data from a company, but this company has strong controls regarding uploading files to external sites. An alternative would be to make a commit with this information to a repository that the company allows access to, and then use `git commit --amend` to "hide" this information.

This way, one just needs to know the commit's URL to access the leaked information.

## Hidden Malicious Code in Good Repositories

A third idea would be to hide malicious code within well-known repositories.

The idea is again very similar: the malicious agent could make a commit containing malicious code to the repository and, after that, use `git commit --amend` to "hide" this code. This way, the malicious code will be inside the repository, but it will not be referenced anywhere.

To create a POC, I followed the same example as the first case, but this time passing two commands: `whoami` and `pwd`.

![](/assets/img/Git-Commit/09.png){: .align-center}

In the same way, a new commit was generated using `--amend` to "hide" this "malicious" content.

Knowing the commit's URL, we can now use PowerShell to make the request and execute the command directly in the terminal.

![](/assets/img/Git-Commit/10.png){: .align-center}

Again, the idea here is just to show a simple POC, proving that it is possible to abuse this functionality for malicious purposes. In a real scenario, creativity has no limits.

# Conclusion

The `git commit --amend` command is, without a doubt, a useful tool in day-to-day work, allowing developers to quickly correct the most recent commit. However, as I tried to show in this article, the fact that commits are immutable and that `amend` actually creates a new commit — stopping the reference to the old one — generates behavior that can be exploited for malicious purposes.

In this article, I provided three simple examples, showing possible exploitation scenarios. Although the examples presented were simple, they prove that it is possible to exploit this functionality. In a real-world scenario and with more creativity, the potential for abusing this characteristic has no limits.

# References

(1) https://git-scm.com/docs/

(2) https://docs.github.com/en/get-started/start-your-journey/about-github-and-git