# Docker 環境で Laravel の検証環境を構築

## 技術スタック

* Laravel 9
* PHP 8
* Nginx
* MySQL 5.7

## 構築

1. クローン
    `git clone https://github.com/ysato-theyou/PoC-laravel-docker.git`
2. ビルド
    `docker-compose build`
3. コンテナ群起動 ( Nginx, MySQL, PHP )
    `docker-compose up`
4. コンテナへログイン
   `docker-compose exec app bash`
5. Laravel 9 環境構築
    `composer create-project --prefer-dist laravel/laravel laravel-project "9.*"`
6. Laravel 実行アカウント ID へローカルマウントしているアプリケーションの権限を調整
   `sudo chown -R www-data: laravel-project/`
7. 疎通テスト
    ブラウザで http://localhost:8000 へアクセス
    ![image](https://user-images.githubusercontent.com/108514223/209458663-b29aabd4-e209-43d0-a0a9-4d9746165e91.png)

## 参考資料
* https://www.torat.jp/laravel-docker-lemp/
 
