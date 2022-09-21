
# TodoListを作りました！

## はじめに

ベースは以下のサイトを参考にしています。

[Django入門 | 初心者でも1時間でWebアプリ(Todoアプリ)を作成するコース | テックダイアリー](https://tech-diary.net/django-todo-tutorial/)

実はこのままではなくて、　　
DBの部分をMYSQL、色んな人のローカルで少しでも動きやすいようにDockerで環境構築できるようにしてあります。

----
## 動かし方　お手元での動かし方

#### まずは、少し書き換えが必要です。

以下の部分を削除してください。

★config/settings.py

```python
import os
import environ

ENV_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))

env = environ.Env(DEBUG=(bool,False))
env.read_env(os.path.join(ENV_DIR,'.env'))

SECRET_KEY = env('SECRET_KEY')
DEBUG = env('DEBUG')

ALLOWED_HOSTS = env.list('ALLOWED_HOSTS')

# Build paths inside the project like this: BASE_DIR / 'subdir'.
BASE_DIR = Path(__file__).resolve().parent.parent

~~~~~中略~~~~~~~

DATABASES = {
    'default':env.db(),
}
```

以下の部分を追記してください

SECRET_KEYは

```ruby
python -c 'from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())'
```

↑これで生成できそうな気がする。

[Django: SECRET_KEY を再生成する - @kyanny's blog](https://blog.kyanny.me/entry/2021/01/27/033507)

★config/settings.py

```python
# SECURITY WARNING: keep the secret key used in production secret!
SECRET_KEY = '新たに生成したものをお使いください。'

# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = True

ALLOWED_HOSTS = [
     '0.0.0.0', '127.0.0.1'
 ]

~~~~中略~~~~

 DATABASES = {
      'default': {
         'ENGINE': 'django.db.backends.mysql',
         'NAME': 'django_db',
         'USER': 'root',
         'HOST': 'db',
         'PORT': 3306
      }
 }
```

たぶんこれで、準備は完了！

必要なファイルはあるはずなので、

`docker-compose build`

`docker-compose up -d db`

`docker-compose up -d web`

`docker-compose up`

と入力

`docker-compose exec web bash`

これでdockerコンテナ内に移動します。

`python manage.py makemigrations`

`python manage.py migrate`

これでマイグレートして、

`python manage.py runserver 0:8000`

これで、サーバーを起動します。

0:8000これをつけないと動きませんので注意！

[Djangoの開発サーバーをDockerコンテナで起動し、ホスト側からアクセスする方法](https://zukucode.com/2020/08/django-server-docker.html)

dockerコンテナ内で起動したものをホストからアクセスするはこの手順を踏む必要がありそう。

```python
http://0.0.0.0:8000/
```

↑これをブラウザに入力すると、画面がでると思います。
