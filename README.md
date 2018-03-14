# AutoWRFChem-R2SMH a.k.a. RACM2_Berkeley2

This repository contains all the utilities needed to run WRF-Chem 3.5.1 updated
with the RACM2_Berkeley2 mechanism, described in Zare et al., 2018.

## Quickstart

The various components of this version of WRF-Chem are linked as Git
submodules, so they haven't actually been cloned yet.

To do so, in THIS directory, run the commands

```
git submodule init
git submodule update
```

If a future update to this repo updates submodules, then after "git pull"
you will need to run "git submodule update" again.

Nearly all of the functionality needed to run WRF-Chem with the automation wrapper are
accessed via the `autowrfchem` command. To see the available operations and flags, run
`./autowrfchem -h` or `./autowrfchem --help`. In general, there are four stages to running
automated WRF-Chem:

1. `./autowrfchem config` will run through WRF and WPS configuration, plus some additional
configuration for the automation, and then bring you into a text based menu to modify
your namelists.
* To modify namelists in the future, use `./autowrfchem config namelist`
* You may place template namelists in the BUILD/CONFIG/NAMELISTS folder and load them
from the main namelist config menu.
* Direct modifications to the namelists stored in BUILD/CONFIG will be lost due to how
AutoWRFChem stores temporary vs. permanent namelist changes.
2. `./autowrfchem compile` will compile WRF, WPS, emiss\_v04, convert\_emiss, MEGAN, and MOZBC
all with a single command. It will use environmental variables set up in the config step,
so you do not need to worry about setting those yourself.
3. `./autowrfchem prepinpt` will prepare all meteorology, emissions, and boundary condition data.
4. `./autowrfchem run` will execute WRF.

For more information, check out the [AutoWRFChem-Base wiki](https://github.com/CohenBerkeleyLab/AutoWRFChem-Base/wiki). 

## New namelist options

There are two new namelist options of note: scale\_nei\_emiss and scale\_closest\_year in the
chemistry namelist. These are present in the template namelist. These control NEI emissions scaling
to the appropriate year.

* scale\_nei\_emiss is a boolean (`.true.` or `.false.`) that turns on or off automatic scaling of
NEI emissions to the model year, based on total US emissions released annually by the EPA.
* scale\_closest\_year is also a boolean that controls how the emissions scaling should behave if
scaling factors are not defined for the model year. If `.false.`, then `convert_emiss` will fail
with an error. If `.true.`, then `convert_emiss` will use the closest available year.

## Important notes to maintainers and modders

The following section only applies to maintainers that will be editing the submodules and
pushing changes back to those repos. If you are one of those people READ THIS WHOLE SECTION
FIRST. Seriously. Submodules are handy, but you can potentially break not only the main repo
but the submodule itself if you aren't careful.

First, by default, each subrepo is checked out to a "detached HEAD" state, meaning you are not
on a branch. New commits will therefore not belong to any branch. You should first checkout a
*new* branch. I'll say that again: do not make changes to an existing branch. Follow good Git
practice and do development work on a new branch. (I have had trouble in the past pushing to
an existing branch when said existing branch had changes on the remote that I did not have 
locally. I may have been doing something wrong, but the approach I describe here is safer.)

Once your changes are committed on the new branch, push it to GitHub and open a pull request
against the appropriate branch. Once the pull request has been merged, pull the updated 
branch to the subrepo. You will then need to go to the main AutoWRFChem-R2SMH directory and 
commit changes to any modified submodules. *Be sure that you are updating the subrepos to a 
commit that exists in the subrepo GitHub repo.* If not, new users will not be able to check
out that subrepo.

