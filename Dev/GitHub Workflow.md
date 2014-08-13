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
* Development starts with a feature request or issue report.
* It supports code review.
* Check-in is linked to a feature/issue to make it traceable.  

It turned out that Scott Chacon's [GitHub Flow](http://scottchacon.com/2011/08/31/github-flow.html) 
is a good one that meets all above requirements. 
 
###GitHub Flow###
---

Below is a copy from [GitHub Flow](http://scottchacon.com/2011/08/31/github-flow.html)

> * Anything in the master branch is deployable
> * To work on something new, create a descriptively named branch off of master (ie: new-oauth2-scopes)
> * Commit to that branch locally and regularly push your work to the same named branch on the server
> * When you need feedback or help, or you think the branch is ready for merging, open a pull request
> * After someone else has reviewed and signed off on the feature, you can merge it into master
> * Once it is merged and pushed to ‘master’, you can and should deploy immediately

###Amdeb Flow###
---

Based on the above GitHub Flow, below are the detail flow used in Amdeb. 

#### 1. One stable deployable master branch####

There is only one master branch that is stable and ready to deploy. 

#### 2. Create an issue for each task #### 

All development activity starts with an issue creation. An issue is a task that could be a bug or a new feature 
that should not be too big. We may use labels to categorize  a feature/issue. 
It can be assigned to a developer. There is a good article titled 
["Mastering Issues"] (https://guides.github.com/features/issues/).

#### 3. Create a branch for each issue #### 

When an issue is assigned to a developer, the developer creates a descriptive branch 
from the stable master branch. The branch name should be a descriptive one reflecting 
a task to be done. In master branch, 
    
    git checkout -b my-feature-123 
    git push origin my-feature-123

The first command create a branch and the second command push the branch to the shared repository 
thus every developer can see it. 

#### 4. Work on a branch for an issue and push to the named branch constantly ###

The developer should commit as often as he/she likes and push changes of the branch to GitHub. 
  
    git commit -a -m "add a new feature" 
    git push origin my-feature-123

There two reasons that we push to a named branch on the server constantly. 
First, our work is always backed up in GitHub. Second, a simple `git fetch` gives 
a list of tasks being worked on. 

#### 5. Open a pull request for an issue ####

When the code is completed and passed all tests, you can open a pull request 
for its branch. Actually you don't have to wait for code completion to 
create a pull request. Whenever you want to discuss the task, you can create 
a pull request that is used as a branch conversation view.  

The article [How we use PUll Request to Build GitHub]
(https://github.com/blog/1124-how-we-use-pull-requests-to-build-github)
suggests open a pull request as early as possible and
 a pull request doesn't have to be merged. It is a great way to get feedback and 
 track progress on a branch. 

#### 6. Merge after pull request is reviewed ####

The philosophy is one commit per feature. Once all your commits of 
a branch are happily reviewed, you start to merge the changes back 
to master by squashing all commits related to a single issue. 

Please do NOT use GitHub's "Merge pull request" button to merge your changes.
You should merge locally first, test the merge, then push to the remote master branch. 

Assume the remote master branch is called origin (the default name). 

    # TODO: need to verify the following commands 
    git fetch origin
    git checkout master
    
    # the following is required if you are out of sync with the master
    # otherwise, no need to rebase
    git rebase master my-feature-123  
    
    git merge --squash my-feature-123
    git commit
    
    git branch -D my-feature-123
    git push origin 
    
    
     






