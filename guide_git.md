- Git initial CLI config
    - `git config --global user.email "user@domain.com..."`
    - `git config --global user.name "username..."`
- Create a new repository
    - https://help.github.com/articles/creating-a-new-repository/
    - https://github.com/new
    - Create without a README, .gitignore nor license for now
- Create a directory for the code
- Put the code in there
- Initialize and push to Git
    - `git init`
    - `git add .`
    - `git commit -m "First commit"`
    - Copy the URL from the repo's "Quick Setup" section in the Git website
    - `git remote add origin remote_repo_url`
    - Verify the URL with `git remote -v`
    - Push your initial commit with `git push -u origin master` 
- Make some changes
- Branch
    - `git checkout -b "stdin-support"`
    - `git add palendrome.py`
    - `git commit`
- Switch to master and merge
    - `git checkout master`
    - `git merge stdin-support`
    - `git commit`
    - `git push`
- Check out an existing repo from somewhere
    - `git clone https://github.com/someone/repo`
