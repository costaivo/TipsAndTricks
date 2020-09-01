
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

