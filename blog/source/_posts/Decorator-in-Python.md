layout: blog
title: Decorator in Python
date: 2016-10-03 22:17:28
tags: Python, Design Pattarn
---
I used to use Flask as my CMPUT 410 Distributed Social Network framework. There is an example from flask.

```python
@app.route('/<aid>', methods=['GET', 'POST'])
def author_view(aid):
    """
    View the main page
    """

    if 'logged_in' in session and aid ==session['logged_id']:
        username = session['logged_in']
        msgCount = reController.getRequestCountByAid(aid)
        countnumber = json.loads(msgCount)['count']
        return render_template('header.html',msgCount = countnumber)
    else:
        return redirect(url_for('login'))
```
What is @app? From the [source code](https://github.com/pallets/flask/blob/master/flask/app.py)

```python
def route(self, rule, **options):
	def decorator(f):
		endpoint = options.pop('endpoint', None)
      	self.add_url_rule(rule, endpoint, f, **options)
      	return f
   return decorator
```
>app.route is a decorator to register the function to the URL
