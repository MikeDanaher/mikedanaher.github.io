---
layout: post
title:  "Introduction to Git"
date:   2014-03-28
visible: true
---

As long as developers have been writing software, they have realized that their software rarely stays the same. It is constantly being refactored, added to, and modified in some way. As requirements change and systems evolve, we as developers need a tool to help us track and manage these changes. We also need a tool that will allow more than one developer to work on a project at the same time without getting in each other’s way. Git helps us do both.

According to Wikipedia (and ultimately Linus Torvalds), Git is a distributed revision control and source code management (SCM) system with an emphasis on speed. Written by Linus Torvalds in the C programming language, Git is his answer to revision control and SCM where others fell short. It grew out of the need for a scalable, distributed, and safe system for storing the source code for Linux. It also needed to be extremely fast and easy to use. Written in just a few months, Git was born from these criteria.

### Revision Control

At the heart of Git is a powerful revision control system. Once you’ve told Git that you want it to track a directory, it will track every change you make to every file that’s added to that directory. It tracks these changes line by line and can show you those differences.

Once you get to a logical break point, and are happy with all the changes you’ve made, you need to save those changes. You tell Git you want to commit the changes you’ve been working on. This takes a snapshot of your current working directory and saves a pointer to it with a unique identifier. You’ll use these unique identifiers if you ever need to revert back to a previous commit.

It is interesting to consider that Git saves an entire snapshot of the working directory with each commit. By doing this, Git is able move backwards and forwards through time, giving you a complete version of all the changes. Each commit builds upon the one before it, so Git just has to compile them in order to get the complete directory structure. WIth Git in control of your files, there should be no fear in deleting or changing anything. You can always go back and see what you did or completely reverse any changes made. This piece of mind alone should be enough to want to use Git.

### Branching

While Git gives us the ability to roll back to a previous version, sometimes we don’t want to delete everything we’ve been working on, just a part of it. For these instances (and many more), Git gives us branching.

Branching is the method that switches the working directory to a new parallel directory. Once branched, work can continue along both paths, but any and all changes are completely separate from each other. Why might we want to do this?

One reason might be to try something out. Say we want to implement new functionality, but we don’t want to break anything in our program. We can branch the program and just try our new functionality in that branch. If everything works, we can then merge our branch with the main one and continue on.

Just as with going back in time with commits, branching gives us the ability to take a snapshot of the working program, try something new, and if we fail (or succeed), it’s trivial to go back to where we came from. In fact, it’s so trivial that Git encourages everyone to only work in parallel branches and never touch the main (master) branch until a new feature has been completed and tested.

### Distributed

Because each commit builds upon the previous ones, and each branch just forks the current directory, Git needs to have everything stored locally. It needs to be installed and used locally. While this may seem like a step back (given the current buzz around ‘cloud’ computing) it’s actually another positive attribute.

By keeping Git local, several developers can work on the same project at the same time, without stepping all over each other. They can edit any file they wish without having to worry about ‘locked for editing by [user x]’. Because of Git’s strong branching and merging abilities, each developer can simply merge their changes with each other (or one central repository) when they are done.

The distributed design of Git also helps protect data loss. If a developer’s machine crashes, only that person’s work is lost. The rest of the project files remain untouched on other machines. By working off more than one machine for every project, you can almost guarantee a catastrophic event will not destroy your work.

When Linus Torvalds was building Git, one of his main concerns was speed. He needed to be able to commit, branch, merge, and push his files without long delays. By keeping everything local, and building it in C, he achieved that goal. Git is incredibly fast. Having not worked with other SCM systems, I have nothing to compare it to, but using Git is definitely not a bottleneck in my own development.

### Getting Started

Getting started with Git is very easy. As a first step, visit git-scm.com to download and install Git for your machine (there are many other ways to install as well). Once installed locally, you have to tell it what to track. Open a terminal and navigate to the directory you would like Git to track. Use the init function:

`$ git init`

You’ve just told Git to watch this directory. From now on, any changes you make to files in this directory, Git will know about. As a best practice, you should make your first commit right away to give yourself a starting point.

`$ git add -A`

This step adds all the files you’ve been working on to the staging area. The staging area is a buffer that you can use to select which files you want to include in your next commit. The -A command adds all files, but you can specify files individually if there are some you’re not ready to commit. For the first commit, any and all should be added.

Once all your files are in the staging area, you’re ready to commit.

`$ git commit`

Optionally, you can add -m followed by your commit message in quotes. This message is stored with your commit so you (and others) know what was included in that commit. If you don’t add the -m here, Git will open your default text editor where you can type your message.

At this point you’ve successfully made the first commit. As you continue to make changes to your project, Git will track those changes as you go. At some point, you’ll want to see what files have changed, but haven’t been committed.

`$ git status`

All modified files will appear. For more detail about exactly what was changed:

`$ git diff`

Now you can view all the changes line by line. To see a history of all the commits:

`$ git log`

These basic commands will take you most of the way through Git. Next we’ll look at branching and how to work with others.

### Collaborative Git

While everything in Git is done locally, there is certainly some benefits to storing your files off site. If you’re working with a dispersed team you’ll want a central location where everyone can access the master branch. If you’re working alone, you’ll want a backup of your local machine. GitHub is a popular off site repository for Git projects, but there are many others.

If you are working with a remote repository, you’ll first have to download all the source files so you have them locally. This is done with clone:

`$ git clone <address of server and files>`

When all the files have downloaded, Git will automatically make your local copy a branch of the remote. It will call the remote ‘origin’ and yours will be ‘master’. Every change and commit you make will move along your local ‘master’ branch. Now, if you want to update the remote branch, you’ll have to push your commits to the remote.

`$ git push`

This pushes your local commit to the remote repository where everyone else on the team can see what you’ve done.

At this point you might be thinking, “what happens if someone else pushes their commit before I do?” This is where the power of Git really shines.

First, Git will try to merge in your commit to the rest of the branch. If no one else has changed the same files in the same places as you, everything continues as planned. If Git cannot merge your commit, it will say there is a merge conflict and you’ll have to manually fix the merge.

However, manually fixing the conflict is usually just a matter of looking at both changes and picking the one that should be used. Save the file and commit the merge. if you work as part of a team, a little communication can go a long way to avoid any major merge conflicts. Another best practice when working remotely is to always pull before you push.

`$ git fetch remote`

This will fetch any changes from the remote branch and bring them to your local machine. Then you’ll have to merge them with the files you’ve been working on. A shortcut for that process is:

`$ git pull`

A pull gets all the remote changes and try to merge them with your changes all at once. If everything looks good, you can then push everything back to the remote.

### Final Thoughts

From the little experience I’ve had with Git, I can already see how powerful it is. It’s been extremely easy to learn the basics, but I’m certain it will take a while to master. I’m currently trying to build the habit of committing often and only making changes to similar code within each commit. I haven’t done much with branching and would like to experiment with that next. I have much to learn, but I’m thankful that there are so many resources available and such a strong community of developers.
