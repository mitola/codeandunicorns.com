---
title: Finding faulty commit with git bisect
date: 2017-02-22T17:57:17+00:00
author:
- admin

categories:
  - How to
  - The Code
---
Recently the problem I have encountered was that the master got broken, possibly due to broken commit. Therefore I was investigating what would be the best and most appropriate way of finding a solution. Git bisect is a tool that uses binary search to find a faulty commit. Without something like this, your only other option is going through each of the commits until you found the one that is broken. In case where you look at a longer time frame, let's say a week or a month, this method will help you to find it a lot faster then otherwise.

It works with first specifying one of the commits that contains the broken version and and one of the commits that works properly.

Here is an example:

```
git bisect start
git bisect bad # The version on which you are currently based with git you define as bad
git bisect good f347fc667fb45dce4798f1d9469d44b696a790a2 # and this uses the commit that is working properly
```

This will checkout one of the random selected commits for a start probably somewhere in the middle. After you recompile your project and test it, you will see if it works or not.  
In case it works write:  
`
git bisect good
` 

If it doesn't:  
`
git bisect bad
` 

Git bisect will return something like this:  
`Bisecting: 10 revisions left to test after this (roughly 4 steps)`

Continue with this step until you find the broken commit.  
After finding it and wishing to reset git bisect command use the following command to point it to head:  
`git bisect reset`

Good luck finding the naughty commits!

### References

Extra documentation and information on it, if needed:  
Official docs: <https://git-scm.com/docs/git-bisect>  
Blogger: <http://webchick.net/node/99>  
Stack overflow: <http://stackoverflow.com/questions/4713088/how-to-use-git-bisect>