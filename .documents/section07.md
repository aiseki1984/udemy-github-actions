# lesson52

```shell
$ rsync -v -e 'ssh -i udemy-github-actions-key.pem' backend/dist/index.js ubuntu@ubuntu@xxxxxxxxxxxx.ap-northeast-1.compute.amazonaws.com:~/index.js
$ ssh -i udemy-github-actions-key.pem ubuntu@ubuntu@xxxxxxxxxxxx.ap-northeast-1.compute.amazonaws.com ls -l
$ ssh -i udemy-github-actions-key.pem ubuntu@ubuntu@xxxxxxxxxxxx.ap-northeast-1.compute.amazonaws.com sudo systemctl restart backend
$ ssh -i udemy-github-actions-key.pem ubuntu@ubuntu@xxxxxxxxxxxx.ap-northeast-1.compute.amazonaws.com systemctl status backend
```
