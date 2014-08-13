## This is a git FAQ ##

### Set username and email ###

    git config --global user.name "myusername"
    git config --global user.email "myusername@myemaildomain.com"
    
    #check global setting
    git config --global -l

### Save username and password ###

Git credential helper can save your credentials into memory.
To cache username and password for 10 hours, use the below command:

    # for ubuntu, change wincred to cache
    # if you set it wrong, you need to unset it first
    # using command git config --globle --unset credential.helper
    
    git config --global credential.helper wincred
    git config credential.helper 'cache --timeout=36000' 
    
 