#git init
#git config --global init.defaultBranch <name>
#git branch -m <name>
#git remote add origin "gitlink"
#git remote -v
#git add "name"
#git commit -m "commit-message"
#git commit --amend -m "New commit message"

#git push -u origin main

#git help

#git status		//Find the changed files in the working directory
#git diff		//Compare codebase states: git diff
#git add			//Add changes to your next commit
#git add -p		//Add selected changes into your next commit
#git commit -amend	//Change the last commit
#git commit -a		//Commit all local changes in tracked files
#git commit		//Commit previously staged changes
#git branch -m new-name	//Rename a Local branch
#git remote -v		//List all currently configured remotes
#git remote show		//View information about a remote
#git remote add		//Add a new remote repository
#git remote remove [rn]	//Delete a remote repository
#git fetch		//Download all changes from the remote repository
#git pull branch		//Download all changes from the remote repository and merge into HEAD
#git branch [bn]		//Create a new branch with the command

git repack -a -d -f --depth=250 --window=250
