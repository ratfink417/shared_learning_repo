
## **DISCLAIMER**
![image alt ><](../Images/SNL-Fixed-it.gif)

_THIS DOCUMENT IS MENT TO PROVIDED ADDED INSIGHT INTO THE NUANCES OF GIT FROM PAINFUL LESSONS LEARNED. THIS IS NOT THE END ALL AND BE ALL OF GIT KNOWLEDGE. JUST A HELPFUL GUIDE ON HOW TO FIX YOUR INEVITABLE FAILURES. PLEASE UPDATE THIS DOCUMENT AS YOU UNCOVER NEW CATASTROPHES IN THE PURSUIT OF ALL GIT. GOOD LUCK!!!_


* * *
<a name="History-of-Git-Part-1"></a>

# History of the Git Part 1
Git was first developed and named by Linus Torval on April 7th, 2005 as the result of being [**_IARC'd_**](#IARCd) by the [license holder](#Larry-McVoy) of [BitKeeper](#BitKeeper). When asked about the name Git _(which means "unpleasant person" in British English slang)_, Torval sarcastically quipped : "I'm an egotistical bastard, and I name all my projects after myself. First 'Linux', now 'git'.

Torval's [Initial revision of "git", the information manager from hell](https://github.com/git/git/blob/e83c5163316f89bfbde7d9ab23ca2e25604af290/README). Describes "Git" as:  
  
       GIT - the stupid content tracker

       "git" can mean anything, depending on your mood.
       - random three-letter combination that is pronounceable, and not actually used by any common UNIX command. 
         The fact that it is a mispronounciation of "get" may or may not be relevant.
       - stupid. contemptible and despicable. simple. Take your pick from the dictionary of slang.
       - "global information tracker": you're in a good mood, and it actually
         works for you. Angels sing, and a light suddenly fills the room. 
       - "goddamn idiotic truckload of sh*t": when it breaks

## Index:
- [Using .gitignore](#Gitignore)
  - [Troubleshooting .gitignore issues](#Troubleshooting-Git-Ignore)
- [Rewriting Git History](#History-Rewrite)
  - [Using Filter-Branch](#filter-branch)
  - [Using git-filter-repo](#git-filter-repo)

- [Additional Sources of Knowledge](#External-Links)
<a name="Gitignore"></a>

# Gitignore
The ability to ignore certain files or groups of files is critical to working with git. Often certain files commited to a repo may change that you may no want to track.
Gitignore's work on a reverse order of presidence with the .gitignore in the parent directory being supersceded by a gitignore in a child directory.

[Back to Index](#Index)
<a name="Troubleshooting-Git-Ignore"></a> 

## Troubleshooting Git Ignore
### _Some files in some directory are being exluded but I don't know where or which ones?!_
  
Change Directory to the root folder of the repo and run this command:
``` bash
$ find . -type f |git check-ignore --stdin
```
This command will recurisvely list every file and location being ignored in the repo:
``` bash
user@workstation MINGW64 /h/workspace/utilities/tools/VSCode-Portable (testing)
$ find . -type f |git check-ignore --stdin
/data/user-data/CachedExtensions/user
/data/user-data/CachedExtensionVSIXs/aeppert.bro-0.6.0
./data/user-data/CachedExtensionVSIXs/arcsector.vscode-splunk-search-linter-0.4.1
./data/user-data/CachedExtensionVSIXs/arcsector.vscode-splunk-search-syntax-1.2.7
./data/user-data/CachedExtensionVSIXs/atlassian.atlascode-2.7.0
``` 
If you have several .gitignore files nested with in the repo and your not sure which one is the cause use this command:
``` bash
$ find . -type f |git check-ignore --verbose --stdin
```
The addition of verbose will now list the .gitignore file, the pattern that is being used, and the file itself in the form of:  
`<source>:<linenum>:<pattern>:`             `<pathname>`
``` bash
user@workstation MINGW64 /h/workspace/utilities/tools/VSCode-Portable (testing)
$ find . -type f |git check-ignore --verbose --stdin
.gitignore:15:*.map     ./data/extensions/codezombiech.gitignore-0.6.0/node_modules/es6-promise/dist/es6-promise.auto.map

.gitignore:15:*.map     ./data/extensions/codezombiech.gitignore-0.6.0/node_modules/es6-promise/dist/es6-promise.min.map

.gitignore:15:*.map     ./data/extensions/eamodio.gitlens-10.2.2/dist/extension.js.map

tools/.gitignore:6:VSCode-Portable/data/user-data/logs/ ./data/user-data/logs/20200828T142913/userDataSync.log

tools/.gitignore:4:VSCode-Portable/data/user-data/Cache*        ./data/extensions/codezombiech.gitignore-0.6.0/node_modules/es6-promise/dist/es6-promise.min.map

tools/.gitignore:21:!*.map      ./data/extensions/codezombiech.gitignore-0.6.0/node_modules/es6-promise/dist/es6-promise.auto.map
```
### **!!!! IMPORTANT EXCEPTION TO GIT IGNORE!!!!** 
If a **!** is being used to un-ignore a file it will still show up in the search pattern output because it is still matching a .gitignore pattern regardless if it hapens to be used to un-ignore an ignore. Since .gitignores' precedent order is the child supersedes the parent, the path of the .gitignore will be in reverse order of the child directory being searched.  The line `.gitignore:15:*.map` indicates that it is ignoring all `*.map` files by the .gitignore file located in  **/h/workspace/utilities/** folder which is the parent folder for  **/tools**.  The **!** exception highlighted with `tools/.gitignore:21:!*.map` indicates that match to ignore is now being excluded and thus the`*.map` files are included in that folder by the .gitignore located in **/h/workspace/utilities/tools/**.

For a more complete list of commands the manual can be found [here](https://git-scm.com/docs).

[Back to Index](#Index)
<a name="History-Rewrite"></a>

# Rewriting Git History
There are many reasons you may need to alter git log history. Git Sh*t Happens otherwise you wouldn't be here right now reading this guide. For undoing commits or resetting back to a previous state I would highly recommend checking out Oh, Shit, Git listed in the [**Additional Sources of Knowledge**](#External-Links) section first. The most common problems you will experience are well documented there. For more peculiar problems read on.

<a name="filter-branch"></a>
## The Nuclear Option: A word about filter-branch
The best I can say about this option is I hope to god you never have to use it! This function is designed to cut and slice changes from a repo's entire history regardless of branch. There are a very small number of scenarios for which you would ever need to do this. Best examples in our case is committing a piece of sensitive or classified information into your code, storing a password or key in plain text, or storing a binary or compiled software like an idiot not realizing every change that is made expands the folder size exponentially even after deletion! One _could_ venture a guess as to which scenario prompted the addition of this subsection.

The first step is **DO NOT USE `filter-branch`!!!** There is a better method for doing this task listed below using a python application called **`git-filter-repo`**. Git's own documentation references this application over use of `filter-branch` as a much better solution and I expect in the future this application will become part of Git's core in a future release.  [If you have no other option the instructions are below git-filter-repo.](#git-branch-inst)

<a name="git-filter-repo"></a>
## A Better way to Nuke: git-filter-repo
`Git-filter-repo` can be installed using pip. For troubleshooting installation problems refer to their [Github Page](https://github.com/newren/git-filter-repo/blob/main/INSTALL.md)
```bash
$ pip3 install git-filter-repo. 
```

## **_IMPORTANT NOTICE!!!!_**
**The fix you are preforming will inevitably replace the entire server repo with the copy that is on your local machine. This will affect all branches of the repo, not just the current one you are working in. It is advised that you delete the local copy of the repo you are working on from your local machine with `rm -rf <repo folder>`. Then do a fresh git clone of that repo from the server to your local machine before making any alterations. You will be reminded of this again before you reach the command of no return.**

For this example our repo is `Utilities` and I have cloned this repo to the following location:
 ```bash
 user@workstation MINGW64 /h/2-Internal-Projects/Tester/utilities (production)
```
Using `du` with the flags -had 1 (Human Readable, All, Directory Depth number)
```bash
user@workstation MINGW64 /h/2-Internal-Projects/Tester/utilities (production)
$ du -had 1
3.3G    ./.git
1.0K    ./.gitignore
12K     ./gitstuff
237K    ./ids
8.0K    ./misc
2.1M    ./qradar
344K    ./SplunkRefSetTool
1.4M    ./yara
3.3G    .
```

We see that the .git folder which contains all git history is 3.3GB! One of the best things about Git-filter-repo is the built in tools for identifying where the problem child maybe. First run `git-filter-repo --analyze`.
```bash
user@workstation MINGW64 /h/2-Internal-Projects/Tester/utilities (production)
$ git-filter-repo --analyze

Processed 13050 blob sizes
Processed 145 commits
Writing reports to .git\filter-repo\analysis...done.
```
The list of reports can be found with in this folder.
```bash
user@workstation MINGW64 /h/2-Internal-Projects/Tester/utilities (production)
$ ls .git/filter-repo/analysis/

blob-shas-and-paths.txt    directories-deleted-sizes.txt  extensions-deleted-sizes.txt  path-deleted-sizes.txt  renames.txt
directories-all-sizes.txt  extensions-all-sizes.txt       path-all-sizes.txt            README
```
Based on the large size of the .git folder we know we are looking for something that had been deleted. The report automatically lists the paths in reverse order starting from the largest **Folder** followed by largest **file** within that folder. So the first few lines should help identify the problem.

```bash
user@workstation MINGW64 /h/2-Internal-Projects/Tester/utilities (production)
$ cat .git/filter-repo/analysis/path-deleted-sizes.txt |head -10

=== Deleted paths by reverse accumulated size ===
Format: unpacked size, packed size, date deleted, path name(s)
  3086872576 3087527742 2020-10-06 Firmware/733_QRadar_FixPack4-2019.14.4.20200704141002.sfs
   113048488  112141769 2020-10-06 Firmware/iDRAC-with-Lifecycle-Controller_Firmware_40T1C_WN32_2.63.60.61_A00.EXE
   204890864   86875833 2021-01-12 tools/VSCode-Portable/Code.exe
   100537720   41983218 2020-08-28 tools/VScodePortable/VSCode-Portable/Code.exe
    33927064   33161618 2020-08-26 tools/VScodePortable/VSCode-Portable/data/user-data/CachedExtensionVSIXs/ms-python.python-2020.8.103604
    12777505   12734666 2020-08-26 tools/VScodePortable/VSCode-Portable/data/user-data/CachedExtensionVSIXs/visualstudioexptteam.vscodeintellicode-1.2.10
    13026436    7560490 2021-01-12 tools/VSCode-Portable/resources.pak
    15611648    5620367 2021-01-12 tools/VSCode-Portable/libGLESv2.dll
```
Here we see the largest folder is `Firmware` with `733_QRadar_FixPack4-2019.14.4.20200704141002.sfs` being the largest file at over 3 GBs. So the best thing to do is remove the entire Firmware Folder from the git history. Another good feature of git-filter-repo is unlike filter-branch you can do a dry-run to get a report of what you are going to keep after running the filter command without doing any damage incase you made a mistake in your filtering.


```bash
user@workstation MINGW64 /h/2-Internal-Projects/Tester/utilities (production)
$ git-filter-repo --dry-run --path-match Firmware --invert-paths

Parsed 145 commits
New history written in 9.58 seconds; now repacking/cleaning...
NOTE: Not running fast-import or cleaning up; --dry-run passed.
      Requested filtering can be seen by comparing:
        .git\filter-repo\fast-export.original
        .git\filter-repo\fast-export.filtered
 ```

GIt-Filter-Repo carves out what is selected and deletes everything else. We find the folder by utilizing the `--path-match` switch followed by Firmware as the folder we want. Now since we want to remove that folder and keep everything else we use `--invert-paths` to filter everything EXCEPT the Firmware folder. A cat of the first 15 lines shows the first commit and the files it contains. So this is what will be kept when we run the command without `--dry-run`
```bash
user@workstation MINGW64 /h/2-Internal-Projects/Tester/utilities (production)
$ cat .git/filter-repo/fast-export.filtered |head -15

feature done
reset refs/remotes/origin/autodiode/2019.10.07.215009.719592197
commit refs/remotes/origin/autodiode/2019.10.07.215009.719592197
mark :1
author James Dickenson <james.dickenson@iarc.nv.gov> 1568333982 -0700
committer James Dickenson <james.dickenson@iarc.nv.gov> 1568333982 -0700
data 43
first commit. live free or fatal error -jd
M 100644 dc5f9c251814d6c4f499898c5af9ca1688d5c1bf cartographer/__pycache__/qradar.cpython-35.pyc
M 100644 a75386654bb394cff3b029a482636fd788a03287 cartographer/ad_chain_CA.crt
M 100644 1656b47144f7c541afe0c8285cfe436f8d7fcd77 cartographer/cartographer.py
M 100644 c11fce743ca93645b119d006b6a7082c1cc8114d cartographer/cartographer.sh
M 100644 ed8265c666218021277774949b0ec73d326e6c61 cartographer/config.ini
M 100644 12a27c43e31d22a97596894dc96acd7f35ba1359 cartographer/input.txt
M 100644 c658718568a78e75d9555bec3b7447cf862f85a2 cartographer/mappings/mapped_opensigs.txt
```
After running the command again this time without the `--dry-run` flag. We run du again on the folder to find: 

```bash
user@workstation MINGW64 /h/2-Internal-Projects/Tester/utilities (testing)

$ du -had 1
291M    ./.git
1.0K    ./.gitignore
12K     ./gitstuff
237K    ./ids
8.0K    ./misc
2.1M    ./qradar
344K    ./SplunkRefSetTool
1.4M    ./yara
295M    .
```
All that is left to do now is put the repo back on the server. This process MAY have removed all Git Remote Url information so you may have to re-add it. Run this command: 
```bash
$ git remote -v
```
If nothing returns we will have to add the server back in. For this test the Repo URL is `ssh://git@las00-152-01-0.iarc:7990/~user/tester.git` Run the following commands:

```bash
$ git remote add origin ssh://git@las00-152-01-0.iarc:7990/~user/tester.git
$ git remote set-url origin ssh://git@las00-152-01-0.iarc:7990/~user/tester.git
```
Now all we do is push and set the Upstream branch right?! 
```bash
user@workstation MINGW64 /h/2-Internal-Projects/Tester/utilities (testing)
$ git push --set-upstream origin testing

To ssh://las00-152-01-0.iarc:7990/~user/tester.git
 ! [rejected]        testing -> testing (fetch first)
error: failed to push some refs to 'ssh://git@las00-152-01-0.iarc:7990/~user/tester.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
<div align="center">

# **!!!!WARNING!!!!!**
![image alt ><](../Images/Self-Destruct.jpg)
</div>

<br>

**YOU ARE ABOUT TO REPLACE THE ENTIRE REPO WITH THIS NEXT COMMAND SO YOU BETTER MAKE SURE YOU HAVE PREFORMED THE FILTERING ON THE LATEST COMMIT AND NO ONE ELSE IS COMMITING TO THE REPO WHILE YOU DO THIS! THIS WILL REPLACE ALL BRANCHES IN THE REPO WITH YOUR LOCAL COPY! YOU HAVE BEEN WARNED!..... UNLESS.... YOU ARE USING BITBUCKET...._AND_ THE HISTORY YOU ARE ALTERING SPANS PULL REQUESTS!!!! THEN THIS METHOD BELOW WONT WORK NO MATTER HOW MANY TIMES YOU FORCE PUSH! ADVANCE TO [HERE](#BitBucket-Blows) FOR THE PAINFUL SOLUTION!**

 ```bash
 user@workstation MINGW64 /h/2-Internal-Projects/Tester/utilities (testing)
 $ git push --set-upstream origin testing --force

Enumerating objects: 90, done.
Counting objects: 100% (90/90), done.
Delta compression using up to 4 threads
Compressing objects: 100% (47/47), done.
Writing objects: 100% (85/85), 1.98 MiB | 21.77 MiB/s, done.
Total 85 (delta 39), reused 78 (delta 32)
remote:
remote: Create pull request for testing:
remote:   https://las00-152-01-0.iarc:8443/users/user/repos/tester/pull-requests?create&sourceBranch=refs/heads/testing
remote:
To ssh://las00-152-01-0.iarc:7990/~user/tester.git
 + c707769...c621758 testing -> testing (forced update)
Branch 'testing' set up to track remote branch 'testing' from 'origin'.
```
[Back to Index](#Index)  

<a name="git-branch-inst"></a>

## Your Last Resort:  Using filter-branch
The only reason these instructions are here is for the unforseen situation python isn't installed or pip can not be utilitzed to install the tool. That said I would HIGHLY recommend you git clone the repo from the server to another folder location. Then preform:
```bash
$ git remote rm origin
```
 This will disconnect the test repo from the server so you don't accidentally push your mistakes back upstream to the server. Hopefully your mistake is limited to a single folder or file you wish to remove. In this case lets assume what you want to remove is/was in a particular folder within your repo. For this example our repo is `Utilities` a clone of this repo has been downloaded to:
 ```bash
 user@workstation MINGW64 /h/2-Internal-Projects/Tester/utilities (production)
```
Using `du` with the flags -had 1 (Human Readable, All, Directory Depth number)
```bash
user@workstation MINGW64 /h/2-Internal-Projects/Tester/utilities (production)
$ du -had 1
3.3G    ./.git
1.0K    ./.gitignore
12K     ./gitstuff
237K    ./ids
8.0K    ./misc
2.1M    ./qradar
344K    ./SplunkRefSetTool
1.4M    ./yara
3.3G    .
```
We see that the .git folder which contains all git history is 3.3GB! There is no easy way using `filter-branch` for me to know which folder and/or files caused this level of bloat. I know what it is because of the use of the prefered tool of `git-filter-repo` and for the sake of time I'll just tell you the folder causing my heartache was called _Firmware_. Instead of preforming a `git revert` to the first apperance of the folder and so on use the following command:
```bash
user@workstation MINGW64 /h/2-Internal-Projects/Tester/utilities (production)
$ git filter-branch --tree-filter "rm -rf Firmware" --prune-empty HEAD
...
WARNING: git-filter-branch has a glut of gotchas generating mangled history
         rewrites.  Hit Ctrl-C before proceeding to abort, then use an
         alternative filtering tool such as 'git filter-repo'
         (https://github.com/newren/git-filter-repo/) instead.  See the
         filter-branch manual page for more details; to squelch this warning,
         set FILTER_BRANCH_SQUELCH_WARNING=1.
Proceeding with filter-branch...

Rewrite <"some hash value"> (<"Current commit">/<"Number of Commits modifying">) (<"time in seconds">, <"estimated time remaing">) 
...
```
More detailed use of the `filter-branch` function is listed [here on Git's website.](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History/#_the_nuclear_option_filter_branch)

[Back to Index](#Index) 

<br>

<a name="BitBucket-Blows"></a>

# Bitbucket Reverse Suction

<div align="center">

![image alt ><](../Images/young-frankenstein-toast.gif)
</div>

## So You Thought the Answer to All Your Life's Problems Had Been Finally Solved Huh???
If you read through [Atlassian's Help Article](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History/#_the_nuclear_option_filter_branch), _if you could even call it that_, there is a little warning towards the bottom that references a caveat on correcting a repo that has pull requests.... long story short... you can't. Pull request between branches preserve the data on the back end and it can't be rewritten even if the options to rewrite history on the repo have been turned off. So now you maybe wondering, "If it can't be done why the hell did you have me do all these steps?!" There is a painful work around that can only be done if you have admin access to the project on bitbucket. 
## Ready, Fire, Aim!
First create a new repo in the project folder of the one you will be replacing. It doesn't matter what it is called your going to change it later. For our example I'll call it utilities-new.

![Create a new repo](New-Repo.png)

Once you click create you will be provided some info on your repository. The only information you care about are the two lines circled in red.


![If File not displaying go to images/New-Repo-info.png... Even though it display's just fine on my machine!!!](New-Repo-Info.png)


If you had already corrected the missing r epo url information earlier for the repo you are fixing, you will neeed to replace that with the URL infomration above.

First Run:

```bash
git remote rm origin
```
Followed by the url information from your new repo. Using our test example above, our new repo url information would be:
```bash
git remote add origin ssh://git@las00-152-01-0.iarc:7990/do/utilities-new.git
git remote set-url origin ssh://git@las00-152-01-0.iarc:7990/utilties-new.git
```
then run

```bash
git push -u origin -all
git push -u origin -tags
```
Verify that your new repo on the Bitbucket server contains all the pull requests, branches, and commits from the previous repo minus whatever you removed. Once you have confirmed that. Delete the old utilites repo. Bitbucket will ask you to confirm this by typing the name of the repo. Once that is done go back into the repo settings of your new repo, in our case "utilties-new" and rename it to that of the repo you just deleted and click save.

![New Repo Info](Rename-Repo.png)

**_You will notice the URL for the repo changes to match the repository name and it now matches the original url of the repo we were trying to fix._**

Now on your local machine inside the original utilties folder run

```bash
git rebase
```
Everyone who had this repo _may_ need to preform this rebase action the next time they go to sync this repo. If the rebase action fails, back out of the repo and run a `rm -rf <repo folder name>` and then git clone the fixed repo back to your local machine from the server.

<div align="center">

## _:::::Cue the Applause:::::_
<br>

![alt Image ><](Multiple-clapping.gif)
</div>


[Back to Index](#Index) 

<br>

<a name="External-Links"></a> 
## Additional Sources of Knowledge

[Oh Shit, Git!?!](https://ohshitgit.com/) - Very helpful in undoing your mistakes.

[Git Reference Documentation ](https://git-scm.com/docs) - Main source of all current Git commands.

[Back to Index](#Index)  

<br>

## References:
___
<a name="IARCd"></a>

#### **IARC'd** 
- Ask anyone who has been at the IARC for over a year for a definition. Any answer provided is valid.

<a name="BitKeeper"></a>

#### **BitKeeper** 
- SCM solution that had been freely licensed for use in continuing development of the Linux kernel.
- No ™ required as Bitkeeper went opensource 11 years after pulling it's free use from Linux, _because who needed it after Git?_

<a name="Larry-McVoy"></a>

#### **Larry McVoy** 
- License holder of BitKeeper. 
- Discontinued Linux's free use of BitKeeper cause he was [butt-hurt](https://lwn.net/Articles/169178/) by [Andrew Tridgell's](#Andrew-Tridgell) attempt at developing SourcePuller.

<a name="Andrew-Tridgell"></a>

#### **Andrew Tridgell** 
- Often just referred to as "Tridge"
- Major Contributer in the development  of Rsync and Samba
- [His Wikipedia Page](https://en.wikipedia.org/wiki/Andrew_Tridgell)

[Back to Top](#History-of-Git-Part-1)

