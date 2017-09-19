### git push failed without prompt to input username

On CentOS 6 ,git version is 1.7.1, when i want to push to remote, i need to type in username to login a git server with http, but there is no prompt.

```sh
$cat ~/.netrc
machine gitweb.abc.com
login YOURNAME
password YOURPWD
$git push origin //will get a prompt to type in username
```

