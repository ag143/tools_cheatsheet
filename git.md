https://www.freecodecamp.org/news/git-cheat-sheet/


Git is a distributed version control system that helps developers collaborate on projects of any scale.

Linus Torvalds, the developer of the Linux kernel, created Git in 2005 to help control the Linux kernel's development.

What is a Distributed Version Control System?
A distributed version control system is a system that helps you keep track of changes you've made to files in your project.

This change history lives on your local machine and lets you revert to a previous version of your project with ease in case something goes wrong.

Git makes collaboration easy. Everyone on the team can keep a full backup of the repositories they're working on on their local machine. Then, thanks to an external server like BitBucket, GitHub or GitLab, they can safely store the repository in a single place.

This way, different members of the team can copy it locally and everyone has a clear overview of all changes made by the whole team.

Git has many different commands you can use. And I've found that these fifty are the ones I use the most often (and are therefore the most helpful to remember).

So I have written them down and thought it'd be nice to share them with the community. I hope you find them useful – Enjoy.

How to check your Git configuration:
The command below returns a list of information about your git configuration including user name and email:

git config -l
How to setup your Git username:
With the command below you can configure your user name:

git config --global user.name "Fabio"
How to setup your Git user email:
This command lets you setup the user email address you'll use in your commits.

git config --global user.email "signups@fabiopacifici.com"
How to cache your login credentials in Git:
You can store login credentials in the cache so you don't have to type them in each time. Just use this command:

git config --global credential.helper cache
How to initialize a Git repo:
Everything starts from here. The first step is to initialize a new Git repo locally in your project root. You can do so with the command below:

git init
How to add a file to the staging area in Git:
The command below will add a file to the staging area. Just replace filename_here with the name of the file you want to add to the staging area.

git add filename_here
How to add all files in the staging area in Git
If you want to add all files in your project to the staging area, you can use a wildcard . and every file will be added for you.

git add .
How to add only certain files to the staging area in Git
With the asterisk in the command below, you can add all files starting with 'fil' in the staging area.

git add fil*
How to check a repository's status in Git:
This command will show the status of the current repository including staged, unstaged, and untracked files.

git status
How to commit changes in the editor in Git:
This command will open a text editor in the terminal where you can write a full commit message.

A commit message is made up of a short summary of changes, an empty line, and a full description of the changes after it.

git commit
How to commit changes with a message in Git:
You can add a commit message without opening the editor. This command lets you only specify a short summary for your commit message.

git commit -m "your commit message here"
How to commit changes (and skip the staging area) in Git:
You can add and commit tracked files with a single command by using the -a and -m options.

git commit -a -m"your commit message here"
How to see your commit history in Git:
This command shows the commit history for the current repository:

git log
How to see your commit history including changes in Git:
This command shows the commit's history including all files and their changes:

git log -p
How to see a specific commit in Git:
This command shows a specific commit.

Replace commit-id with the id of the commit that you find in the commit log after the word commit.

git show commit-id
How to see log stats in Git:
This command will cause the Git log to show some statistics about the changes in each commit, including line(s) changed and file names.

git log --stat
How to see changes made before committing them using "diff" in Git:
You can pass a file as a parameter to only see changes on a specific file.
git diff shows only unstaged changes by default.

We can call diff with the --staged flag to see any staged changes.

git diff
git diff all_checks.py
git diff --staged
How to see changes using "git add -p":
This command opens a prompt and asks if you want to stage changes or not, and includes other options.

git add -p
How to remove tracked files from the current working tree in Git:
This command expects a commit message to explain why the file was deleted.

git rm filename
How to rename files in Git:
This command stages the changes, then it expects a commit message.

git mv oldfile newfile
How to ignore files in Git:
Create a .gitignore file and commit it.

How to revert unstaged changes in Git:
git checkout filename
How to revert staged changes in Git:
You can use the -p option flag to specify the changes you want to reset.

git reset HEAD filename
git reset HEAD -p
How to amend the most recent commit in Git:
git commit --amend allows you to modify and add changes to the most recent commit.

git commit --amend
!!Note!!: fixing up a local commit with amend is great and you can push it to a shared repository after you've fixed it. But you should avoid amending commits that have already been made public.

How to rollback the last commit in Git:
git revert will create a new commit that is the opposite of everything in the given commit.
We can revert the latest commit by using the head alias like this:

git revert HEAD
How to rollback an old commit in Git:
You can revert an old commit using its commit id. This opens the editor so you can add a commit message.

git revert comit_id_here
How to create a new branch in Git:
By default, you have one branch, the main branch. With this command, you can create a new branch. Git won't switch to it automatically – you will need to do it manually with the next command.

git branch branch_name
How to switch to a newly created branch in Git:
When you want to use a different or a newly created branch you can use this command:

git checkout branch_name
How to list branches in Git:
You can view all created branches using the git branch command. It will show a list of all branches and mark the current branch with an asterisk and highlight it in green.

git branch
How to create a branch in Git and switch to it immediately:
In a single command, you can create and switch to a new branch right away.

git checkout -b branch_name
How to delete a branch in Git:
When you are done working with a branch and have merged it, you can delete it using the command below:

git branch -d branch_name
How to merge two branches in Git:
To merge the history of the branch you are currently in with the branch_name, you will need to use the command below:

git merge branch_name
How to show the commit log as a graph in Git:
We can use --graph to get the commit log to show as a graph. Also,
--oneline will limit commit messages to a single line.

git log --graph --oneline
How to show the commit log as a graph of all branches in Git:
Does the same as the command above, but for all branches.

git log --graph --oneline --all
How to abort a conflicting merge in Git:
If you want to throw a merge away and start over, you can run the following command:

git merge --abort
How to add a remote repository in Git
This command adds a remote repository to your local repository (just replace https://repo_here with your remote repo URL).

git add remote https://repo_here
How to see remote URLs in Git:
You can see all remote repositories for your local repository with this command:

git remote -v
How to get more info about a remote repo in Git:
Just replace origin with the name of the remote obtained by
running the git remote -v command.

git remote show origin
How to push changes to a remote repo in Git:
When all your work is ready to be saved on a remote repository, you can push all changes using the command below:

git push
How to pull changes from a remote repo in Git:
If other team members are working on your repository, you can retrieve the latest changes made to the remote repository with the command below:

git pull
How to check remote branches that Git is tracking:
This command shows the name of all remote branches that Git is tracking for the current repository:

git branch -r

How to fetch remote repo changes in Git:
This command will download the changes from a remote repo but will not perform a merge on your local branch (as git pull does that instead).

git fetch
How to check the current commits log of a remote repo in Git
Commit after commit, Git builds up a log. You can find out the remote repository log by using this command:

git log origin/main
How to merge a remote repo with your local repo in Git:
If the remote repository has changes you want to merge with your local, then this command will do that for you:

git merge origin/main
How to get the contents of remote branches in Git without automatically merging:
This lets you update the remote without merging any content into the
local branches. You can call git merge or git checkout to do the merge.

git remote update
How to push a new branch to a remote repo in Git:
If you want to push a branch to a remote repository you can use the command below. Just remember to add -u to create the branch upstream:

git push -u origin branch_name

How to remove a remote branch in Git:
If you no longer need a remote branch you can remove it using the command below:

git push --delete origin branch_name_here
How to use Git rebase:
You can transfer completed work from one branch to another using git rebase.

git rebase branch_name_here
Git Rebase can get really messy if you don't do it properly. Before using this command I suggest that you re-read the official documentation here

How to run rebase interactively in Git:
You can run git rebase interactively using the -i flag.
It will open the editor and present a set of commands you can use.

git rebase -i master
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
How to force a push request in Git:
This command will force a push request. This is usually fine for pull request branches because nobody else should have cloned them.
But this isn't something that you want to do with public repos.

git push -f




https://about.gitlab.com/images/press/git-cheat-sheet.pdf

GitLab | everyone can contribute about.gitlab.com
Git configuration
Starting A Project
$ git config --global user.name “Your Name”
Set the name that will be attached to your commits and tags.
$ git config --global user.email “you@example.com”
Set the e-mail address that will be attached to your commits and tags.
$ git config --global color.ui auto
Enable some colorization of Git output.
$ git init [project name]
Create a new local repository. If [project name] is provided, Git will
create a new directory name [project name] and will initialize a
repository inside it. If [project name] is not provided, then a new
repository is initialized in the current directory.
$ git clone [project url]
Downloads a project with the entire history from the remote repository.
01
02
Git Cheat Sheet
Day-To-Day Work
$ git status
Displays the status of your working directory. Options include new,
staged, and modified files. It will retrieve branch name, current commit
identifier, and changes pending commit.
$ git add [file]
Add a file to the staging area. Use in place of the full file path to add all
changed files from the current directory down into the directory tree.
$ git diff [file]
Show changes between working directory and staging area.
$ git diff --staged [file]
Shows any changes between the staging area and the repository.
$ git checkout -- [file]
Discard changes in working directory. This operation is unrecoverable.
$ git reset [file]
Revert your repository to a previous known working state.
$ git commit
Create a new commit from changes added to the staging area.
The commit must have a message!
03
GitLab | everyone can contribute about.gitlab.com
Git branching model
$ git branch [-a]
List all local branches in repository. With -a: show all branches
(with remote).
$ git branch [branch_name]
Create new branch, referencing the current HEAD.
$ git checkout [-b][branch_name]
Switch working directory to the specified branch. With -b: Git will
create the specified branch if it does not exist.
$ git merge [from name]
Join specified [from name] branch into your current branch (the one
you are on currently).
$ git branch -d [name]
Remove selected branch, if it is already merged into any other.
-D instead of -d forces deletion.
04
$ git rm [file]
Remove file from working directory and staging area.
$ git stash
Put current changes in your working directory into stash for later use.
$ git stash pop
Apply stored stash content into working directory, and clear stash.
$ git stash drop
Delete a specific stash from all your previous stashes.
Review your work
$ git log [-n count]
List commit history of current branch. -n count limits list to last n
commits.
$ git log --oneline --graph --decorate
An overview with reference labels and history graph. One commit
per line.
$ git log ref..
List commits that are present on the current branch and not merged
into ref. A ref can be a branch name or a tag name.
$ git log ..ref
List commit that are present on ref and not merged into current
branch.
$ git reflog
List operations (e.g. checkouts or commits) made on local repository.
05
GitLab | everyone can contribute about.gitlab.com
Tagging known commits
Reverting changes
Synchronizing repositories
$ git tag
List all tags.
$ git tag [name] [commit sha]
Create a tag reference named name for current commit. Add commit
sha to tag a specific commit instead of current one.
$ git tag -a [name] [commit sha]
Create a tag object named name for current commit.
$ git tag -d [name]
Remove a tag from local repository.
$ git reset [--hard] [target reference]
Switches the current branch to the target reference, leaving
a difference as an uncommitted change. When --hard is used,
all changes are discarded.
$ git revert [commit sha]
Create a new commit, reverting changes from the specified commit.
It generates an inversion of changes.
$ git fetch [remote]
Fetch changes from the remote, but not update tracking branches.
$ git fetch --prune [remote]
Delete remote Refs that were removed from the remote repository.
$ git pull [remote]
Fetch changes from the remote and merge current branch with its
upstream.
$ git push [--tags] [remote]
Push local changes to the remote. Use --tags to push tags.
$ git push -u [remote] [branch]
Push local branch to remote repository. Set its copy as an upstream.
06
07
08
Commit
Branch
Tag
Head
an object
a reference to a commit; can have a tracked upstream
a reference (standard) or an object (annotated)
a place where your working directory is now
GitLab | everyone can contribute about.gitlab.com
Ignoring Files
Ignoring Files
$ cat .gitignore
/logs/*
!logs/.gitkeep
/tmp
*.swp
Verify the .gitignore file exists in your project and ignore certain type
of files, such as all files in logs directory (excluding the .gitkeep file),
whole tmp directory and all files *.swp. File ignoring will work for the
directory (and children directories) where .gitignore file is placed.
B
C
Git installation The zoo of working areas
For GNU/Linux distributions, Git should be available in the standard
system repository. For example, in Debian/Ubuntu please type in
the terminal:
$ sudo apt-get install git
If you need to install Git from source, you can get it from
git-scm.com/downloads.
An excellent Git course can be found in the great Pro Git book by
Scott Chacon and Ben Straub. The book is available online for free
at git-scm.com/book.
Remote repository named origin? You’ve
probably made git clone from here.
Changes committed here will be safe. If you are
doing backups! You are doing it, right?
Git fetch or git pull Git push
Git commit
Git reset HEAD
Git stash
Git stash pop
Git add
Git push public master Remote repositories
Local repositories
Another remote repository. Git is a distributed
version control system. You can have as many
remote repositories as you want. Just remember
to update them frequently.
Only index will be committed.
Choose wisely what to add!
You do all the hecking right here!
A kind of shelf for the mess
you don’t want to include.
This is a tag. It looks like
a version so it’s probably
an object (annotated tag)
This is an
upstream branch
This is a merge commit,
it has two parents!
This is a tag. It looks like a developer’s note
so it’s probably a reference, not an object.
Your working directory is here
This is also a local branch
This is a local branch. It is 3 commits ahead,
you see it, right?
A D
Remote repo
(name: origin)
Repository
origin/fix/a fix/a
HEAD
Master
Working
directory
Remote repo
(name: public)
Index
(staging area)
Stash
working-version
V1.0.1 This is an initial commit,
it has no parents


https://dev.to/doabledanny/git-cheat-sheet-50-commands-free-pdf-and-poster-4gcn

Git Cheat Sheet 📄 (50 commands + PDF and poster)
#
git
#
github
#
programming
#
productivity
I was tired of looking up the same common Git commands - so I made a cheat sheet that I could print and put on my office wall.

This cheat sheet contains 50 commonly used Git commands on the following topics:

Setting up Git
Starting a project
Making a change
Basic concepts
Branching
Merging
Rebasing
Undoing things
Reviewing your repo
Stashing
Synchronising local and remote repositories
Git Commands Cheat Sheet PDF
One page PDF to make it easy to copy and paste in commands.

Download the Git Commands Cheat Sheet PDF here

Both PDF and poster are available in Light Mode and Dark Mode:

Git cheat sheet poster in light mode

Git cheat sheet poster in dark mode

Git Cheat Sheet Poster
Order a physical A3 poster for your office wall - so you can quickly look up commands, and keep them at the top of your head.

It comes in thick durable paper, and a matte, light-absorbing finish.

Order a Git Cheat Sheet Poster here

Here's mine on my office wall:

The Git cheat sheet poster on my office wall

Here are all of the commands from the cheat sheet:

Setup
Set the name and email that will be attached to your commits and tags
$ git config --global user.name "Danny Adams"

$ git config --global user.email "myemail@gmail.com"
Starting a Project with Git
Create a local repo (omit <directory> to initialise the current directory as a git repo)
$ git init <directory>
Download a remote repo
$ git clone <url>
Make a Change
Add a file to staging
$ git add <file>
Stage all files
$ git add .
Commit all staged files to git
$ git commit -m "commit message"
Add all changes made to tracked files & commit
$ git commit -am "commit message"
Basic Git Concepts
main: default development branch
origin: default upstream repo
HEAD: current branch
HEAD^: parent of HEAD
HEAD~4: great-great grandparent of HEAD

Branches
List all local branches. Add -r flag to show all remote branches. -a flag for all branches.
$ git branch
Create a new branch
$ git branch <new-branch>
Switch to a branch & update the working directory
$ git checkout <branch>
Create a new branch and switch to it
$ git checkout -b <newbranch>
Delete a merged branch
$ git branch -d <branch>
Delete a branch, whether merged or
not
$ git branch -D <branch>
Add a tag to current commit (often used for new version releases)
$ git tag <tag-name>
Merging
Merge branch a into branch b. Add --no-ff option for no-fast-forward merge

fast-forward vs no-fast-forward merge in Git
$ git checkout b

$ git merge a
Merge & squash all commits into one new commit
$ git merge --squash a
Rebasing
Rebase feature branch onto main (to incorporate new changes made to main). Prevents unnecessary merge commits into feature, keeping history clean

Rebasing feature onto main in Git
$ git checkout feature

$ git rebase main
Interactively clean up a branches commits before rebasing onto main
$ git rebase -i main
Interactively rebase the last 3 commits on current branch
$ git rebase -i Head~3
Undoing Things
Move (&/or rename) a file & stage move
$ git mv <existing_path> <new_path>
Remove a file from working directory & staging area, then stage the removal
$ git rm <file>
Remove from staging area only
$ git rm --cached <file>
View a previous commit (READ only)
$ git checkout <commit_ID>
Create a new commit, reverting the changes from a specified commit
$ git revert <commit_ID>
Go back to a previous commit & delete all commits ahead of it (revert is safer). Add --hard flag to also delete workspace changes (BE VERY CAREFUL)
$ git reset <commit_ID>
Review your Repo
List new or modified files not yet committed
$ git status
List commit history, with respective IDs
$ git log --oneline
Show changes to unstaged files. For changes to staged files, add --cached option
$ git diff
Show changes between two commits
$ git diff commit1_ID commit2_ID
Stashing
Store modified & staged changes. To include untracked files, add -u flag. For untracked & ignored files, add -a flag.
$ git stash
As above, but add a comment.
$ git stash save "comment"
Partial stash. Stash just a single file, a collection of files, or individual changes from within files
$ git stash -p
List all stashes
$ git stash list
Re-apply the stash without deleting it
$ git stash apply
Re-apply the stash at index 2, then delete it from the stash list. Omit stash@{n} to pop the most recent stash.
$ git stash pop stash@{2}
Show the diff summary of stash 1. Pass the -p flag to see the full diff.
$ git stash show stash@{1}
Delete stash at index 1. Omit stash@{n} to delete last stash made
$ git stash drop stash@{1}
Delete all stashes
$ git stash clear
Synchronizing
Add a remote repo
$ git remote add <alias> <url>
View all remote connections. Add -v flag to view urls.
$ git remote
Remove a connection
$ git remote remove <alias>
Rename a connection
$ git remote rename <old> <new>
Fetch all branches from remote repo (no merge)
$ git fetch <alias>
Fetch a specific branch
$ git fetch <alias> <branch>
Fetch the remote repo's copy of the current branch, then merge
$ git pull
Move (rebase) your local changes onto the top of new changes made to the remote repo (for clean, linear history)
$ git pull --rebase <alias>
Upload local content to remote repo
$ git push <alias>
Upload to a branch (can then pull request)
$ git push <alias> <branch>

  
  https://ndpsoftware.com/git-cheatsheet.html#loc=remote_repo;
  
  
  https://www.javatpoint.com/git-cheat-sheet
  Git Cheat Sheet
1. Git configuration
Git config
Get and set configuration variables that control all facets of how Git looks and operates.
Set the name:
$ git config --global user.name "User name"
Set the email:
$ git config --global user.email "himanshudubey481@gmail.com"
Set the default editor:
$ git config --global core.editor Vim
Check the setting:
$ git config -list
Git alias
Set up an alias for each command:
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
2. Starting a project
Git init
Create a local repository:
$ git init
Git clone
Make a local copy of the server repository.
$ git clone
3. Local changes
Git add
Add a file to staging (Index) area:
$ git add Filename
Add all files of a repo to staging (Index) area:
$ git add*
Git commit
Record or snapshots the file permanently in the version history with a message.
$ git commit -m " Commit Message"
4. Track changes
Git diff
Track the changes that have not been staged: $ git diff
Track the changes that have staged but not committed:
$ git diff --staged
Track the changes after committing a file:
$ git diff HEAD
Track the changes between two commits:
$ git diff Git Diff Branches:
$ git diff < branch 2>
Git status
Display the state of the working directory and the staging area.
$ git status
Git show Shows objects:
$ git show
5. Commit History
Git log
Display the most recent commits and the status of the head:
$ git log
Display the output as one commit per line:
$ git log -oneline
Displays the files that have been modified:
$ git log -stat
Display the modified files with location:
$ git log -p
Git blame
Display the modification on each line of a file:
$ git blame <file name>
6. Ignoring files
.gitignore
Specify intentionally untracked files that Git should ignore. Create .gitignore:
$ touch .gitignore List the ignored files:
$ git ls-files -i --exclude-standard
7. Branching
Git branch Create branch:
$ git branch List Branch:
$ git branch --list Delete a Branch:
$ git branch -d Delete a remote Branch:
$ git push origin -delete Rename Branch:
$ git branch -m
Git checkout
Switch between branches in a repository.
Switch to a particular branch:
$ git checkout
Create a new branch and switch to it:
$ git checkout -b Checkout a Remote branch:
$ git checkout
Git stash
Switch branches without committing the current branch. Stash current work:
$ git stash
Saving stashes with a message:
$ git stash save ""
Check the stored stashes:
$ git stash list
Re-apply the changes that you just stashed:
$ git stash apply
Track the stashes and their changes:
$ git stash show
Re-apply the previous commits:
$ git stash pop
Delete a most recent stash from the queue:
$ git stash drop
Delete all the available stashes at once:
$ git stash clear
Stash work on a separate branch:
$ git stash branch
Git cherry pic
Apply the changes introduced by some existing commit:
$ git cherry-pick
8. Merging
Git merge
Merge the branches:
$ git merge
Merge the specified commit to currently active branch:
$ git merge
Git rebase
Apply a sequence of commits from distinct branches into a final commit.
$ git rebase
Continue the rebasing process:
$ git rebase -continue Abort the rebasing process:
$ git rebase --skip
Git interactive rebase
Allow various operations like edit, rewrite, reorder, and more on existing commits.
$ git rebase -i
9. Remote
Git remote
Check the configuration of the remote server:
$ git remote -v
Add a remote for the repository:
$ git remote add Fetch the data from the remote server:
$ git fetch
Remove a remote connection from the repository:
$ git remote rm
Rename remote server:
$ git remote rename
Show additional information about a particular remote:
$ git remote show
Change remote:
$ git remote set-url
Git origin master
Push data to the remote server:
$ git push origin master Pull data from remote server:
$ git pull origin master
10. Pushing Updates
Git push
Transfer the commits from your local repository to a remote server. Push data to the remote server:
$ git push origin master Force push data:
$ git push -f
Delete a remote branch by push command:
$ git push origin -delete edited
11. Pulling updates
Git pull
Pull the data from the server:
$ git pull origin master
Pull a remote branch:
$ git pull
Git fetch
Download branches and tags from one or more repositories. Fetch the remote repository:
$ git fetch< repository Url> Fetch a specific branch:
$ git fetch
Fetch all the branches simultaneously:
$ git fetch -all
Synchronize the local repository:
$ git fetch origin
12. Undo changes
Git revert
Undo the changes:
$ git revert
Revert a particular commit:
$ git revert
Git reset
Reset the changes:
$ git reset -hard
$ git reset -soft:
$ git reset --mixed
13. Removing files
Git rm
Remove the files from the working tree and from the index:
$ git rm <file Name>
Remove files from the Git But keep the files in your local repository:
$ git rm --cached
  
  
  https://www.git-tower.com/blog/git-cheat-sheet/
  
  https://education.github.com/git-cheat-sheet-education.pdf
  
  
