# Git Tricks & Tips

## Git alias shortcuts
[Git Alias](https://jonsuh.com/blog/git-command-line-shortcuts/)


## Git log 
[Better git log](https://coderwall.com/p/euwpig/a-better-git-log)

```cmd
git logline --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
```

## Using different Git accounts on same machine

### Set Config to enable saving credentials based on http path
```cmd 
git config --global credential.useHttPath true
git config --global -e
```


### Using Multiple Git accounts on same PC
https://www.youtube.com/watch?v=2MGGJtTH0bQ
