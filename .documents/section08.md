# lesson 60 S3 へのフロントエンドのアップロード

CLI を使うので、前もってセットアップしておく

```shell
$ aws s3 ls
2023-02-21 18:19:03 udemy-github-actions-s3-oshima
# アップロード
$ cd frontend/
$ aws s3 sync . s3://udemy-github-actions-s3-oshima

# ファイルの容量が変わっていなくても、タイムスタンプが違えばアップロードする
$ aws s3 sync . s3://udemy-github-actions-s3-oshima --exact-timestamps
```

# lesson63 CORS への設定
