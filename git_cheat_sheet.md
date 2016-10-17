# Git Better at Git : Git Cheatsheet

## Note

This article is written with the assumption that we use [fork-model](https://www.atlassian.com/git/tutorials/comparing-workflows/forking-workflow) of Git. This is a standard Git workflow in Reston CPS. Some fine, subtle details might have been ignored due to this fact.

Before start, I want to point out some of the things that you really should follow these two rules working with Git.

1. **Your commit should be atomic**
2. **Make your commit message meaningful**

Those two rules are pretty self-explainatory. But one may ask why one should follow those rules.

**If your commit is atmoic, rolling back a commit and making change becomes effortless.** A little bit of anecdote, I implemented something that was unnecessary and even potentiall harmful due to miscommunication with my manager. The good thing was, I kept atomic and legible commits. I undid a commit with one git revert command, and I was done. If I did this one big commit. It would have more work for me to do. 

Also, if you make those atomic commits with meaningful commit messages, it's definitely easier for other developers to follow your work. Souce control is used for collaborative purpose. A commit message to a commit is analogous to a comment to your block of code. 

At quick glance, if your commit message is something like "update exception block", I don't know what that means as a fellow developer. Was this referring to exception block in this certain logic that I wrote some time ago, or was it referring to something completely different? If that message was something like "Updated an unchecked exception in SomeService.java with newly define error called NoSuchThingException." This is definitely more legible.

It's okay if you make atomic commits here and there. If your commits are all over the place, don't worry. There is a way to squashing and re-writing your commit afterward. 

---

## Tips 

- **Rewriting Commits**

Honestly, I think this [article](https://medium.com/@porteneuve/getting-solid-at-git-rebase-vs-merge-4fa1a48c53aa#.qt3q90kgq). explains this significantly better than I can. Skip to **Cleaning up your local history before pushing**. I highly recommend reading the entire article. 

If you don't want to read it. I can 


- **I have changes in one file that should be in different commit**



- **I have to undo a commit that is way back in the history** 

//TODO revert and reset

- **I can't checkout to another branch.**

Sometimes it's because you created a new file, or you staged the changes already. An easy way to avoid this is 

```
> git stash
```

This is easy way to preserve your change. As it may convey, your changes are 'stashed' or put away for a while. So you can checkout to other branch and make changes. When you come back to this branch, do 

```
> git stash apply
```

This above command applies the **latest** stashed change and remove that stash from stash list. If you made a stash some time ago...

```
> git stash list
```

This command will list all stashes. If you want to apply stash a specific stash that is not your latest stash. 

- **When your change is not 'worthy' of a commit**

We all want clean, nice Git history. But sometimes, you make a small mistake that's not really a worth make an extra commit to fix the changes. 

For an example, before your pull request merges, you make a typo in your readme. And your last commit was updating a readme due to implementing some feature. Commit messages of "fixed readme typo" right after "updated readme due to feature X" is unnecessary. 

Or it could be the case where after pushing your commits into your remote, your code fails to build due to some style guide issue from your latest commit. 

Making some change, and keeping the comment from your latest commit is simple.

```
> git add ...
> git commit --no-edit --amend
> git push -u --force origin someBranch/name
```

--force is needed if you pushed your latest commit onto your repository previously. What really happened is that your latest commit is technically removed and replaced with a brand new commit without altering a commit message. So if you remove --force tag while your old commit is there, your push will be rejected. 

**Note** 

This is technically rewriting a history. If other people have a copy of your old commit already, you SHOULD NOT do this. At that point, make another commit, push another separate PR.

This is obviously something quick you can do. As I explained earlier, you can also achieve something similar with git rebase -i.
