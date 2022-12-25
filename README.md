# Docker 環境で Laravel の検証環境を構築

## 技術スタック

* Laravel 9
* PHP 8
* Nginx
* MySQL 5.7

## 構築

1. クローン
   * `git clone https://github.com/ysato-theyou/PoC-laravel-docker.git`
2. ビルド
   * `docker-compose build`
3. コンテナ群起動 ( Nginx, MySQL, PHP )
   * `docker-compose up`
4. コンテナへログイン
   * `docker-compose exec app bash`
5. Laravel 9 環境構築
   * `composer create-project --prefer-dist laravel/laravel laravel-project "9.*"`
6. Laravel 実行アカウント ID へローカルマウントしているアプリケーションの権限を調整
   * `sudo chown -R www-data: laravel-project/`
7. 疎通テスト
   * ブラウザで http://localhost:8000 へアクセス
    ![image](https://user-images.githubusercontent.com/108514223/209458663-b29aabd4-e209-43d0-a0a9-4d9746165e91.png)


## Docker Hub へ登録

登録済み Docker Hub アカウントへログインしている状態で、イメージを push する

- タグを `{username}/{image}` に変更後に push する
```shell
$ docker tag c2b506847dcb ysofficellc/laravel_docker_app
$ docker image ls
REPOSITORY                       TAG           IMAGE ID       CREATED         SIZE
ysofficellc/laravel_docker_app   latest        c2b506847dcb   2 hours ago     548MB
$ docker push ysofficellc/laravel_docker_app
Using default tag: latest
The push refers to repository [docker.io/ysofficellc/laravel_docker_app]
3be18a56c2b2: Preparing 
8a70d251b653: Pushed 
latest: digest: sha256:b045a7479fccc172342e5dd46e0a731e912d0404c55d9a4741370aad2e538992 size: 4081
```

## 参考資料
* https://www.torat.jp/laravel-docker-lemp/
 
