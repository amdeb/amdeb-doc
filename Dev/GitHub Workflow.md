##Amdeb GitHub Workflow##
---

This document describes the basic workflow for source code management. 
It covers topics such as feature request, bug database, code review, release management.

Following are some good online Git resources to give a developer a good start. 

* [Pro Git Book] (http://git-scm.com/book)
* [GitHub Guides] (https://guides.github.com/)
* [GitHub Flow](http://scottchacon.com/2011/08/31/github-flow.html) 
This is the workflow we use and describe below. 
* [Github Helps for Pull Reuqest] (https://help.github.com/categories/63/articles)
It is very important to understand the pull request concepts and operations. 
* [An interactive Git Cheat Sheet] (http://ndpsoftware.com/git-cheatsheet.html)
Very interesting. 
* [GitHub Cheat Sheet] (https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf)
A Pdf Cheat sheet. 
* [Interactive Free Git Course] (https://www.codeschool.com/courses/git-real)
* [Git Workflow Book] (https://github.com/janosgyerik/git-workflows-book) 
It explains some useful scenarios. 

###Source Code Workflow Requirement###
---

The workflow is used by a small team with no more than 10 developers. 
The basic requirements are

* It should be as simple as possible, but not simpler.
* All developers have all rights to all source code.
* Development starts with a feature request or issue report.
* It supports code review.
* Check-in is linked to a feature/issue to make it traceable.   
 
###GitHub Flow###
---

It turned out that Scott Chacon's [GitHub Flow](http://scottchacon.com/2011/08/31/github-flow.html) 
is a good one that meets all above requirements. It uses a Shared Repository Model. 
All developers share a single master branch and many topic branches. They use
pull request to review, discuss and contribute changes. 
Below are detail steps copied from the Scott's article: 

> * Anything in the master branch is deployable
> * To work on something new, create a descriptively named branch off of master (ie: new-oauth2-scopes)
> * Commit to that branch locally and regularly push your work to the same named branch on the server
> * When you need feedback or help, or you think the branch is ready for merging, open a pull request
> * After someone else has reviewed and signed off on the feature, you can merge it into master
> * Once it is merged and pushed to ‘master’, you can and should deploy immediately

###Amdeb Flow###
---

Based on the above GitHub Flow, below are the detail flow used in Amdeb. 

#### 1. Share one stable deployable master branch####

There is only one master branch in a shared repository that is stable and ready to deploy. 
Everyone should clone and sync with this shared repository.  

#### 2. Create an issue for each task #### 

All development activity starts with an issue creation. An issue is a task that could be a bug or a new feature 
that should not be too big. We may use labels to categorize a feature/issue. 
It can be assigned to a developer. There is a good article titled 
["Mastering Issues"] (https://guides.github.com/features/issues/).

#### 3. Create a branch for each issue #### 

When an issue is assigned to a developer, the developer creates a descriptive branch 
from the stable master branch. The branch name should be a descriptive one reflecting 
a task to be done. This kind of branch is called a topic branch. 
Suppose the shared remote repository is origin. 

    # Sync local repository with the shared master branch 
    git fetch origin            
     
    # create a branch for an issue
    git checkout -b my-feature-123 origin/master
     
    # push the newly create branch to shared repository
    git push origin my-feature-123

The first command create a topic branch named my-feature-123 
and the second command push the branch to the shared repository 
thus every developer can see it. 

#### 4. Work on a branch for an issue and push to the named branch constantly ###

The developer should commit as often as he/she likes and 
push changes of the topic branch to GitHub. 
  
    git commit -a -m "add a new feature" 
    git push origin my-feature-123

There are two reasons that we push to the topic branch on the server constantly. 
First, our work is always backed up in GitHub. Second, a simple `git fetch` gives 
a list of tasks being worked on. 

#### 5. Create a pull request for an issue ####

When the code is completed and passed all tests, you can open a pull request 
for the topic branch. Actually you don't have to wait for code completion to 
create a pull request. Whenever you want to discuss the task, you can create 
a pull request that is used as a branch conversation view.  

The article [How we use PUll Request to Build GitHub]
(https://github.com/blog/1124-how-we-use-pull-requests-to-build-github)
suggests open a pull request as early as possible and 
a pull request doesn't have to be merged. It is a great way to get 
feedback and track progress on a branch.
 
After a pull request is create, all developers are welcome 
to discuss and review it. One can even comment on changes at source code lines 
and contribute changes to the topic branch. 

#### 6. Merge after pull request is reviewed ####

The philosophy is one commit per feature. Once all your commits of 
a branch are happily reviewed, you start to merge the changes back 
to master by squashing all commits related to a single issue. 

Unless it is a trivial change, please do NOT use GitHub's 
"Merge pull request" button to merge your changes.
You should merge locally first, test the merge, then push to the remote master branch. 

When commit all changes, please provide information of the solved issue number 
thus GitHub can close the issue automatically. 
The commit message has a pattern of the "Fixes#123 and closes#456" syntax. 
    
    # Go to local master
    git checkout master
        
    # Sync local with origin. Because we never change master
    # this should be a fast forward if there are any changes
    git pull origin master
    
    # Merge the changes of topic branch to master
    # ToDo: need to add "Fixes#123" in commit message
    git merge --squash my-feature-123
    
    # Sync the shared master with topic branch changes
    git push origin 
    
    # Delete the local branch
    git branch -d my-feature-123
    
    # Delete remote branch in the shared repository
    git push origin --delete my-feature-123
     
    
    
     






