- Unhide the Library Folder
	- Bring the finder application foremost
	- shift-cmd-H, cmd-2, cmd-j
	- Check "Show Library Folder"
- BASH profile
	- Assure /usr/local/bin is in $PATH
	- source ~/.bashrc if it exists
- Install XCode
	- `xcode-select 	-install`
- Install Homebrew
	- `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`
	- run brew doctor to check for any problems
	- run brew update to get updates
- Install python3.x with homebrew
	- `brew install python`
- Install virtualenv
	- `pip3 install virtualenv`
- Make some dirs
	- `mkdir ~/dev; cd ~/dev`
	- `mkdir -p projects virtualenvs ~/Library/Application\ Support/pip`
- Configure pip a little
	- `vim ~/Library/Application\ Support/pip/pip.conf`
	- Add to force pip to only run in virtualenvs to prevent accidental global installs:
```[install]
require-virtualenv = true

[uninstall]
require-virtualenv = true
- Add a bash function to let us use PIP in non-virtualenvs
	- `vi ~/.bashrc`
	- add these lines:
```# Function to allow pip to run in non-virtualenvs
# For ex to do system-wide upgrades:
# gpip install 	-upgrade pip setuptools wheel virtualenv
#
# the config in ~/Library/Application\ Support/pip/pip.conf will typically
# prevent pip from running globally
gpip(){
   PIP_REQUIRE_VIRTUALENV="0" pip3 "$@"
}```
- Create some virtualenvs
	- `cd ~/dev/virtualenvs`
	- virtualenv foobar
- Know more about https://virtualenv.pypa.io/en/stable/
	- get in with source project/bin/activate
	- deactivate to get out
- running a virtualenved script from cli
	- `/home/you/dev/virtualenvs/foobar/bin/python /path/to/script.py`

> Sources: https://hackercodex.com/guide/mac-development-configuration/
