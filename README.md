# docker-rails
docker を用いた rails アプリケーション。  
まずは立ち上げるところまで。

基本的に下記に乗っかる。  
https://docs.docker.com/compose/rails/

```
docker-machine start default
eval $(docker-machine env default)
```

.bundle が作成されないため、vendor/bundle に gem を見にいくことなく
`Could not find gem 'rails (= 5.0.0)'` とかで落ちてしまう。
```
# Dockerfile ruby v2.2(抜粋)
# don't create ".bundle" in all our apps
```

.dockerignore でコンテナ側でも無視するように設定した上で、
vendor/bundle を共有できるようにローカルで bundle install しておく。
```
bundle install --path=vendor/bundle --jobs=4
```

その上で
```
docker-compose run web rails new . --force --database=postgresql --skip-bundle
```

Gemfile が更新されているので、`gem 'therubyracer', platforms: :ruby
` のコメントアウトを外して再度 docker 経由で bundle install しておく。
```
docker-compose build
docker-compose run web rake db:create
docker-compose up
```

アクセス先の IP アドレスは下記のように調べる。
```bash
$ docker-machine ip
#=> 192.168.99.100
```

http://192.168.99.100:3000/ にアクセスすると、無事表示できた。
![finished](https://i.gyazo.com/a5fb8aff3dcaa878c5917c4201286764.png)
