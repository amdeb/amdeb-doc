## This is a git FAQ ##

### Save username and password ###

Git credential helper can save your credentials into memory.
To cache username and password for 10 hours, use the below command:

    git config credential.helper 'cache --timeout=36000' 