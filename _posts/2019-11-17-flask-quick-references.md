---
layout: post
title: Flask cheat sheet
---

{{ page.title }}
================

<p class="meta">17 Nov 2019</p>

Writing our own decorators in Flask. Below is the Flask minimal application code from documentation.
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'
```
Flask uses decorators for its routing. `@app.route('/')` is the decorator in this minimal application. When writing apis we might want to protect some of our endpoints from anonymous users. If all of our endpoints are private we could use Flask's `before_request` to validate the request. When application has both public and private resource endpoints we need to ensure that no anonymous users get to access the private endpoints. This is the best use case to use python decorators with Flask.

```python
my_decorators.py

import json
from functools import wraps

from Flask import Response

def api_access_required():
    def _api_access_required(func):
        @wraps(func)
        def dec_function(*args, **kwargs):
            api_key = request.headers.get('api_key')
            valid_api_key = validate_api_key(api_key)
            if not valid_api_key:
                message = {
                    'message': 'You are not authorized to perform this action',
                    'status': 'failure',
                }
                resp = Response(json.dumps(message))
                resp.status_code = 403
                return resp
            return func(*args, **kwargs)
        return dec_function
    return _api_access_required
```
Now we can import `api_access_required` and attach it to the any endpoint which needs a authorization for its resources.

```python

from flask import Flask

import api_access_required from my_decorators

app = Flask(__name__)

@app.route('/my_private_endpoint')
@api_access_required
def my_private_endpoint():
    return 'This is very secret message bois!'
```

We can also add arguments our custom decorator just like `route` which accepts string.