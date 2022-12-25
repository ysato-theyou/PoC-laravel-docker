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

## AWS EKS へデプロイ

1. Laravel プロジェクトを作成
   `docker-compose exec app composer create-project --prefer-dist laravel/laravel laravel-aws-eks`
2. 権限修正（環境に依るので本来は環境変数で調整するほうが良い）
   `sudo chown -R www-data: laravel-aws-eks/`
3. イメージビルド
    `docker build -f docker/php/apache.Dockerfile laravel-aws-eks -t ysofficellc/laravel-aws-eks:v2`
4. イメージプッシュ
    `docker push ysofficellc/laravel-aws-eks:v2`

### クラスター作成

`eksctl create cluster --name=laravel-kube-cluster --nodes=2 --node-type=t2.micro`
結構時間かかる

### Kubernetes へ kubectl を用いてデプロイする

1. Deployments 投入
   `kubectl apply -f laravel-mysql-kubernetes.yaml`
2. Service 投入
    `kubectl apply -f laravel-service.yaml`
3. 動作確認
  - `kubectl get nodes`
  - `kubectl get pods`
  - `kubectl get services`
    - 参考
    ```shell
    $ kubectl get nodes 
    NAME                                               STATUS   ROLES    AGE   VERSION
    ip-192-168-10-69.ap-northeast-1.compute.internal   Ready    <none>   28m   v1.23.13-eks-fb459a0
    ip-192-168-83-17.ap-northeast-1.compute.internal   Ready    <none>   28m   v1.23.13-eks-fb459a0
    $ kubectl get pods 
    NAME                       READY   STATUS    RESTARTS   AGE
    laravel-5f85c5c975-gd586   2/2     Running   0          3m
    $ kubectl get services
    NAME              TYPE           CLUSTER-IP      EXTERNAL-IP                                                                    PORT(S)        AGE
    kubernetes        ClusterIP      10.100.0.1      <none>                                                                         443/TCP        38m
    laravel-service   LoadBalancer   10.100.110.29   [MASK].ap-northeast-1.elb.amazonaws.com   80:32096/TCP   25m
    ```
![image](https://user-images.githubusercontent.com/108514223/209470824-66456a53-650c-4efb-a3cf-667ea90763b8.png)


## 参考資料
* [Laravel開発環境をDockerを使って構築する（LEMP環境)](https://www.torat.jp/laravel-docker-lemp/)
* [Deploy a Laravel App to Amazon EKS in 5 minutes](https://gbengaoni.com/blog/Deploy-a-Laravel-App-to-Amazon-EKS-in-5-minutes-a94a41436157)
