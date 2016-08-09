The various components of this version of WRF-Chem are linked as Git
submodules, so they haven't actually been cloned yet.

To do so, in THIS directory, run the commands

git submodule init
git submodule update

If a future update to this repo updates submodules, then after "git pull"
you will need to run "git submodule update" again.

!!!============================================!!!
!!! IMPORTANT NOTES TO MAINTAINERS AND MODDERS !!!
!!!============================================!!!

The following section only applies to maintainers that will be editing the submodules and
pushing changes back to those repos. If you are one of those people READ THIS WHOLE SECTION
FIRST. Seriously. Submodules are handy, but you can potentially break not only the main repo
but the submodule itself if you aren't careful.

If you edit one of the submodules and wish to push the changes back to the submodule's repo
(e.g. edits within the BUILD folder are to be pushed back to the AutoWRFChem-Base remote)
check if there are new commits on the remote that you would need to merge first.

In my experience, as long as the changes you've made can be applied with a fast-forward merge,
(i.e. there are no commits on the remote you do not have locally), then the only difference to
a normal push operation is that you will need to specify the remote and branch names, i.e.

    git push origin master
    
since the submodules technically exist in a detached head state. If however, there ARE remote
commits you do not have locally, things get messier. In my experience, you will run into an 
issue where:
  -- you can pull the remote changes and merge them without trouble, but
  -- when you try to push, it will fail saying that you don't have some of the remote commits.
  
** DO NOT, UNDER ANY CIRCUMSTANCE, TRY TO FIX THIS WITH git push -f. IT WILL BREAK THINGS. **

It may be possible to fix this by telling the submodule it is on a branch somehow, but I found
it easier to fix this using patches. Let us assume, for the sake of example, that this is the
situation:
  -- the last commit common to both the remote and local repos is 000000
  -- the remote has one additional commit past this, 111111
  -- the local repo has two commits past 000000 that need pushed: aaaaaa and bbbbbb.
  
The following process should be fairly robust to incorporate the changes from the submodule into
the remote:
  1) On the local repo with aaaaaa and bbbbbb, run "git format-patch 000000..bbbbbb". This will
     generate two .patch files, starting in 0001 and 0002.
  2) Clone the submodule's repo directly somewhere else. If, for example, the changes are in the
     BUILD folder, clone the AutoWRFChem-Base repo somewhere completely outside the 
     AutoWRFChem-R2SMH folder. (If you do not know the remote's address, "git remote -v" inside 
     the submodule will show you.)
  3) In the new clone, checkout a new branch from the last common commit. In this case, that would
     be done with "git checkout -b fix 000000".
  4) Copy the patch files to the new clone.
  5) In the new clone, on the new branch ("fix" in this example), apply the patch files with 
     "git am < 0001-blah.patch", replacing "0001-blah.patch" with each patch file in turn. Once
     done, you should see the commits each patch file represents in the log for that branch.
  6) In the new clone, switch back to the master branch. Merge in the "fix" branch (or whatever you
     called it).
  7) Push the result to the remote.

As far as fixing the submodule without simply deleting the whole AutoWRFChem-R2SMH repo and starting 
over, I have not tested these steps, but in theory the following should work:
  1) Make sure that there are no changes in the submodule you'd be sad if you lost that haven't been 
     applied via patch to the new clone (particularly working directory changes).
  2) Reset the submodule to the last common commit, here, "git reset --hard 000000"
  3) Pull from the remote; it should fast-forward now without difficulty.

In either case (complex patching fix or easy fast forward), once you've got the submodule in the state
you want, you will need to update the reference to it in the main repo. From this folder, stage the 
changes to the submodule folder as if it were a file, e.g. if the submodule in BUILD has be modified:

git add BUILD

followed by "git commit" once everything else you want to go in this commit is ready is all you need to
do. You shouldn't need to do "git submodule update", since effectively you just manually did an update.
You can then push the main AutoWRFChem-R2SMH repo back to its remote.
