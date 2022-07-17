# Demystifying (part of) Git

Git is a very powerful (Distributed) Version Control System (VCS) that is extremely useful for any software project, but it can be challenging to begin using it.

There is a *lot* of information out there, from detailed documentation to simple and extensive tutorials, but at the beginning it can be hard to parse through it all without getting even more confused. After trying to explain how Git works to many of my friends (and trying to understand it myself) I decided to write a short guide on *some* of the concepts and commands that people commonly struggle with, by bringing together information from blog posts, articles, and documentation. Hopefully this will help you make a little more sense of how Git works, and how to use it more confidently and effectively.

**NOTE** While this guide will eventually start talking about the role of Github, the first concept that is important to understand is that Git and Github are two separate things. The former is the VCS tool, the latter is essentially a cloud storage *for* Git. While this may seem obvious to experienced users, it was not obvious to me nor to many people that I talked to when they started using Git.

Another disclaimer: this is not a beginner's guide to Git, and assumes basic working knowledge. If you are using Git but don't actually know what your commands are doing, this should clear some things up.

## Workspace, Staging Area, and (Local) Repository

Without an understanding of the difference between *workspace* (or *working directory*), *staging area* (also known as the *index*, or *cache*) and *repository*, it's difficult to grasp what each command does as most of the documentation will make free use of this terminology, often without explanation.

Let's say you've managed to set up a repository and made your first commit. Now the real work begins, so you open one of your files and start editing. This is the workspace. In general, only the files will be moving around the different data stores. If you now run `git status` in your terminal you will see that Git noticed you edit this file and has marked it as modified.

**TIP** Always run `git status` before/after issuing your next Git command to check everything is ok

Happy with your changes? You can move the edited file to the staging area with `git add`, and proceed to commit your work to the repository with `git commit`. Does Git take pleasure in making things more complicated than they need to be? Why do we have to add before committing?

Imagine you're moving to a new house. You have some books in front of you, so you put them in a box, close it up and send it along after marking it "Books". Maybe you have a lot of books, however, so before closing it up you move around the house, grabbing other books, every now and then checking the state of the box, and once you're finally happy with its contents, you close it up and send it along after marking it "Books". What happens if instead of just books you also had cutlery and socks? You could very well be a psychopath and place everything in a single box, close it up and mark it "Books, Cutlery, and Socks". *Or* you could first put the books in one box, close it and mark it "Books", take a second box in which you put cutlery, close it up and mark it "Cutlery", etc.

In this analogy, your house items are your edited files, the box is the staging area, and the closed and marked boxes are the commits and their descriptive message. I hope it's starting to be clear how the staging area can be a valuable tool, so let's drop the analogy and start talking about how it can be used effectively.

#### Using the Staging Area

Here are two great blog posts on how to use the staging area in your workflow. I will summarise their main points, although they are nice articles and recommend that you read them.

##### Use the staging area as a checkpoint

[My Git Workflow - Oliver Steele](https://blog.osteele.com/2008/05/my-git-workflow/)

As you keep on working on a specific commit, by regularly running `git add` you can create a sort of checkpoint, and be able to compare your current work to your last `git add` by using `git diff`, as well as all the changes you've made since your last commit with `git diff HEAD`.

This article contains a great graphic that visualizes Git's data stores and some commands to move between them.

![](https://images.osteele.com/2008/git-transport.png)

Small aside, if you find `HEAD` confusing you can read more about it [here](https://www.cloudbees.com/blog/git-detached-head).

##### Use the staging area to subdivide your changes into multiple elegant commits

[The Thing About Git - Ryan Tomayko](https://tomayko.com/blog/2008/the-thing-about-git)

Like in the moving analogy, to remain sane and keep your repository clean it's better to create many smaller commits that contain only relevant changes. So you first add some files and commit them, and then add the others and proceed to your next commit.

But what happens if one of your edited files contains changes that should belong to two different commits? Git has you covered with `git add --patch`, which lets you add only the changes from a single file that you choose. The blog article does an excellent job of explaining how this works with examples.

## Remote Repositories

While VCS can be extremely useful for individual software projects, one of the main benefits of using software such as Git is that it enables collaborative programming. With a remote repository, you can upload your repository online such that other programmers can download it, make and upload changes, or sync any new updates.

This is where Github comes in. Once you upload your repository on Github, that remote will be synced with your repository. If other contributors `git clone` this repository, they will not only be able to download the files, but also the log of commits, all the branches that you have pushed to the remote, etc.

As shown in the image [above](#use-the-staging-area-as-a-checkpoint), the remote repository is a separate data store from your local repository. The remote will not be aware of edits, new commits, new branches, etc. until you `git push`. Likewise, any changes made to the remote by other collaborators will not be visible in your local remote until you `git pull`.

The remote can therefore function as an additional checkpoint for your work. In general, only working code should be pushed.

**TIP** When preparing to push, always compile and test your code to make sure the latest pushed commit is functional!

Maybe the feature you were working on not only doesn't work but somehow broke your code, and you can't figure out how to fix it back. Knowing that you have a known working commit gives you a safety net that lets you reset your branch to a working state.

```git
# If you want to backup your non-working changes first commit them
# and then create a new branch
git commit -a -m "Non-working backup of feature x"  # commit all with message
git branch broken-feature-backup  # new branch without switching to it

# Then reset your local branch (index + workspace) to a working state
git fetch <remote_name>
git reset --hard <remote_name>/<branch_name>
# WARNING --hard will delete your changes permanently if not saved
# somewhere (like with git branch above) before this operation
```

Explaining the commands goes beyond what this guide aims to explain, but here are some links if you want to learn more.

[git pull vs git fetch](https://stackoverflow.com/questions/292357/what-is-the-difference-between-git-pull-and-git-fetch?page=1&tab=votes#tab-top)

[Git - Reset Demystified](https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified)

[git-pull Documentation](https://git-scm.com/docs/git-pull), [git-fetch Documentation](https://git-scm.com/docs/git-fetch)

#### Multiple Remotes

You are not limited to a single remote repository, but can instead have an arbitrary number connected to your local repository. This can cause considerable confusion at the start, but is crucial when collaborating with many other programmers.

As a software project grows large, it may not be feasible to keep adding collaborators to a remote repository, and you may find that after a `git clone`, the first time you try to push you will encounter an error:

```git
remote: Permission to <remote_repository> denied to <username>.
fatal: unable to access <url_of_repository>: The requested URL returned error: 403
```

Even if you don't plan to push your changes to the original repository you cloned, you may still want to store your changes online, either to share them with others or simply to have an online backup. As a solution, you set up a second remote under your personal Github account. After [creating a new repository on GitHub](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository):

```git
git remote add <remote_name> <remote_URL>  # Remote name is arbitrary
git push <remote_name> <branch_you_want_to_push>
```

The first command lets your local repository know where to find your new remote, after which you can start pushing branches to the new remote, to which you have full control. It is then possible to share commits between your remote repository and the original via a [pull requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests) (not discussed in detail in this document).

This entire process is described here [Pushing commits to a remote repository - GitHub Docs](https://docs.github.com/en/get-started/using-git/pushing-commits-to-a-remote-repository).

As an example to why this can be useful, when working with Paparazzi I have 3 remotes set up.

```git
$ git remote
origin         # main paparazzi repository (paparazzi/paparazzi)
matteobarbera  # personal repository (matteobarbera/paparazzi)
tudelft        # backup + share with tudelft collaborators (tudelft/paparazzi)
# Once again these names are completely arbitrary
```

In general, while coding new features etc. I will push to my personal repository. I can create and push an arbitrary number of branches without cluttering anyone else's repository, and if something goes wrong or breaks, the only one that suffers the consequences is me. To backup the code to my new feature, I will push to the tudelft repository. This is especially important if this feature is part (or will be part) of a project that will be worked on by other tudelft programmers, as it's useful to have this code centralized in a single repository as opposed to needing the link to each personal repository.

The principal reason to keep a link to the main remote repository is to be able to regularly `git pull` the latest updates and merge them with my personal repository. This is important because

1. These updates continuously improve the software and may fix issues that (I may not know) I am having
  
2. Projects like Paparazzi will receive frequent updates, and if I don't regularly update my personal repository, it will become impossible to merge back without having to deal with a mountain of merge conflicts
  

## Git Checkout

Another common cause of confusion is the command `git checkout`, because, as this [blog post by Dan Fabulich](https://redfin.engineering/two-commits-that-wrecked-the-user-experience-of-git-f0075b77eab1) points out, it just does too many things. Common operations include:

- Switch branch or create a new branch
  
  ```git
  git checkout <branch>
  git checkout -b <new_branch>
  ```
  
- Switch your working directory to a past commit (read more about [detached HEAD](https://www.cloudbees.com/blog/git-detached-head))
  
  ```git
  git checkout <commit>
  ```
  
- Restore a file to a specific commit
  
  ```git
  git checkout <commit> -- <file>  # The -- tell git next arg is a file
  ```
  

It can be useful as you start using Git to avoid using `git checkout` for everything and instead use `git switch` to move around branches and `git restore` to roll back specific files to past commits. Here are two blog posts about these two commands ([blog1](https://bluecast.tech/blog/git-switch-branch/), [blog2](https://www.infoq.com/news/2019/08/git-2-23-switch-restore/)). The operations above could then look like this:

```git
git switch <branch>
git switch -c <new_branch>

# Restores file in workspace to index (last git add)
git restore <file>
# Restores file in index to HEAD (last commit). Same as git reset <file>
# Essentially unstages file (keeps changes)
git restore --staged <file>
# Restore both index and workspace to HEAD. Same as git checkout <file>
git restore --source=HEAD --staged --worktree <file>
```

`git restore` could look more intimidating, and may be confusing to get your head around the similar looking commands `git restore`, `git reset`, and `git revert`.

Here is a StackOverflow question about the difference between [these 3 commands](https://stackoverflow.com/questions/58003030/what-is-the-git-restore-command-and-what-is-the-difference-between-git-restor). And here is the documentation [git restore](https://git-scm.com/docs/git-restore), [git reset](https://git-scm.com/docs/git-reset), [git revert](https://git-scm.com/docs/git-revert).

If you are comfortable using `git checkout` it's absolutely not wrong to continue using it, but it's good to be aware of alternatives.

## Git Stash

`git stash` is another great command that is commonly used, but that sometimes causes headaches. You've been editing files, but you now need to switch to another branch. Git doesn't let you until you commit or stash your changes. No problem, `git stash` and off you go to your branch2. You make some changes, nothing committable yet, so `git stash` once again and back to branch1. `git stash apply` and suddenly disaster! There are now merge conflicts!?

One aspect of `git stash` that is not so obvious at first is that **it is not branch specific**, but shared for the entire repository. If you run the command

```git
git stash list
```

you will see a list containing both stashes you made in branch1 and branch2, along with possibly many other stashes you've made in the past. If you want to bring back the changes you originally stashed from branch1, you need to:

```git
git stash apply stash@{1}  # Number refers to stash you want to apply
git stash apply 1  # Alternative command
```

Relevant blog post [Resolving Merge Conflict after Git Stash Pop - jdhao's blog](https://jdhao.github.io/2019/12/03/git_stash_merge_conflict_handling/)

## Concluding remarks

If you are struggling with some other concept or have more questions on these topics, Google is your friend. There are probably many StackOverflow threads on your question, as well as instructive blog posts. A great place to start is the [Pro Git Book](https://git-scm.com/book/en/v2), which is available online for free and covers everything in this short guide and so much more.

This guide aims at shedding some light on certain issues that Git users commonly struggle with. If you think some other topic should be covered, feel free to add to this guide or let one of the authors know :)

##### Authors

Matteo Barbera - mbarbera@tudelft.nl
