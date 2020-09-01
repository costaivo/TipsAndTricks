
# Windows Terminal Tips & Tricks

## Installing Scoop
1. open the command prompt in windows and type powershell
``` cmd
powershell
```
2. Set the Execution policy 
``` cmd
Set-ExecutionPolicy RemoteSigned -scope CurrentUser
```

3. Install Scoop
``` cmd
Invoke-Expression (New-Object System.Net.WebClient).DownloadString('https://get.scoop.sh')
```
4. console theme
scoop install concfg pshazz
concfg import solarized-dark

5. Scoop Extras : Will recognize VSCODE etc
scoop bucket add extras

## Windows Terminal 
Install the windows terminal from the windows store


Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser

Install-Module -Name PSReadLine -AllowPrerelease -Scope CurrentUser -Force -SkipPublisherCheck
Then run "notepad $PROFILE" and add these lines to the end:


https://github.com/microsoft/cascadia-code/releases



