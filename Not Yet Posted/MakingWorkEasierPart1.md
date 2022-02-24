# Making work easier, part 1: Git Flow

This is part 1 of a series where we'll look at tips, frameworks, workflows, etc. that make team projects easier.

Today we'll be looking at **Git Flow**, a set of rules about how a Git repository should be structured.

When working with Git it might me tempting to just use the `main` default branch (or also the `dev` branch if you're feeling extra special that day). That way of structuring your project might work if you're working by yourself, but when working with a team with multiple people commiting to the same project, things can get ugly quickly.

Git Flow defines a set of branches that a project should have, making sure that stuff that isn't finished yet is separate from production code, as well as adding several levels of checks so that the code that makes it to production is as good as possible. 

At the time of writing this article there was a fake news story about Microsoft starting development on Windows 12, so why not explain the different branches that Git Flow defines using an imaginary repository that contains Windows 12's code?

## Main branch
This is the branch that holds production code. This branch holds a record of a project's official releases. As this branch only holds production code, it might be a good idea to set up Continuous Delivery to act when stuff is added to this branch

## Dev branch
This branch integrates all finished features. This branch holds a project's complete history.

## Feature branches
These branches are derived from the dev branch and it's where the actual coding happens. Each of the project's features gets its own branch, when a feature is finished, the branch is merged into `dev` and the the feature branch is deleted. The fact that active development is made on separate branches makes it easy to test new stuff without fear of breaking production code or your team member's work as well as making it easy to roll back code if a feature is cancelled before it's completed (because you just need to delete the appropriate feature branch!). This division also makes it easy to integrate Pull Request into your workflow.

Now, let's imagine that the first feature to be developed for Windows 12 is support for two TPM modules, after a while a separate team member starts working in revamping the start menu. Windows 12's repository might look like this:

- 🟦: Main commits
- 🟩: Dev commits
- 🟨: Feature commits

```
🟦 <- main
 |
🟩 ------------ 🟩 ----------------- 🟩     <- dev
 |               |     |               |
🟨 - 🟨 - 🟨 - 🟨    |               |     <- feature/TwoTPMs
                       |               |
                      🟨 - 🟨 - 🟨 - 🟨    <- feature/RevampStartMenu
```

## Release branches
A release branch is created when the `dev` branch has enough features to justify a release, or when a release date is approaching. **No new features are introduced in these branches**. The only changes that are introduced in these branches are bug fixes, documentation or additional tests. When the release branch is ready, it's merged into `main`, tagging that commit with a new version number and merged back into dev (this is because important bug fixes might be introduced in the release branch).

Separating releases into different branches allows developers to continue to push new changes into the dev branch without worrying about breaking the release.

Let's go back to our imaginary Windows 12 repo and let's say that it's time for the first Insider build (version 22800) to be released:

- 🟦: Main commits
- 🟩: Dev commits
- 🟨: Feature commits
- 🟪: Release commits

```
                                                version 22800 
                                                       |
                                                       v  
🟦 -------------------------------------------------- 🟦 <- main
 |                                                     |
 |                                                     |
 |                                    🟪 - 🟪 - 🟪 - 🟪 <- release/22800
 |                                     |               |
🟩 ------------ 🟩 ----------------- 🟩 ------------ 🟩     <- dev
 |               |     |               |
🟨 - 🟨 - 🟨 - 🟨    |               |     <- feature/TwoTPMs
                       |               |
                      🟨 - 🟨 - 🟨 - 🟨    <- feature/RevampStartMenu
```

## Hotfix branches
The final branch type defined by Git Flow are Hotfix branches, these branches are used to quickly patch production releases. **These branches are the only ones that are based on main and not on dev** (so that the hotfix doesn't include features that aren't ready yet). When the hotfix is finished, the branch is merged onto `main`, `dev` and any active `release` branches.

Imagine that during the preparation of release 22900, a bug is discovered in production that needs to be fixed quickly. Windows 12's imaginary repository might look like this:

- 🟦: Main commits
- 🟩: Dev commits
- 🟨: Feature commits
- 🟪: Release commits
- 🟧: Hotfix commits

```
                                                version 22800                                     version 22850
                                                       |                                                |
                                                       v                                                v
🟦 -------------------------------------------------- 🟦 -------------------------------------------- 🟦  <- main
 |                                                     |                |                               |
 |                                                     |               🟧 - 🟧 - 🟧 -------------------------------- <- hotfix/Heartbleed2.0
 |                                                     |                                                     |      |
 |                                                     |                                                     |      |
 |                                    🟪 - 🟪 - 🟪 - 🟪 <- release/22800                                   |      |
 |                                     |               |                        🟪 - 🟪 - 🟪 - 🟪  ------ 🟪      |    <- release/22900
 |                                     |               |                         |                                  | 
🟩 ------------ 🟩 ----------------- 🟩 ------------ 🟩 -------------------------------------------------------- 🟩  <- dev
 |               |     |               |
🟨 - 🟨 - 🟨 - 🟨    |               |     <- feature/TwoTPMs
                       |               |
                      🟨 - 🟨 - 🟨 - 🟨    <- feature/RevampStartMenu
```

So that's a basic rundown of Git Flow! Though I do wanna mention that although Git Flow  has its advantages, it also has some disadvantages, like making merging code harder (because when merging you're probably merging a lot of commits at once) as well as having an increased probability of merge conflicts between branches (like if two developers modify the same file on different feature branches). I encourage you to find the Git workflow that best fits your current team. You can checkout more workflows [here](https://www.atlassian.com/git/tutorials/comparing-workflows)