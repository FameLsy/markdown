# gitlab 服务器ssh端口非22

```
sudo vim /etc/gitlab/gitlab.rb
```
找到以下进行修改
```
gitlab_rails['gitlab_shell_ssh_port'] = 29972
```