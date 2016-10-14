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
app.route is a decorator to register the function to the URL. 

@app.route('/<aid>', methods=['GET', 'POST']) is equivalent to

```python
app.rounte('/<aid>',methods=['GET','POST'])(author_view)
```
---
we can use decorator to check whether user is logged in

```python
def sessionCheck():
	def decorator(f):
		if 'user' not in session or session.get('user') is not None:
			return redirect(url_for('login'))
		return f
	return decorator
```
However there is one issue behind decorator, the nested function is hidden and its function information is not available for outside. What does it mean for example:

```python
def logged(func):
    def with_logging(*args, **kwargs):
        print func.__name__ + " was called"
        return func(*args, **kwargs)
    return with_logging
if __name__ == "__main__":
	@logged
	def printHello():
		print("Hello")
	print(printHello.__name__)
```
The output is:
with_logging

We need the printHello instead. It is because from printHello in fact we get the function with_logging. we get decorated function called only bacause decorator function return the the original function. How to solve it?
we can use the library ***functools.wraps*** and change the function like this

```
def logged(func):
	@wraps(func)
    def with_logging(*args, **kwargs):
        print func.__name__ + " was called"
        return func(*args, **kwargs)
    return with_logging
```
The wraps is also a decorator and it will keep the ```__module__, __name__, __doc__``` information for wrapped function. How does python do that?
[https://hg.python.org/cpython/file/2.7/Lib/functools.py](https://hg.python.org/cpython/file/2.7/Lib/functools.py)

```python
# update_wrapper() and wraps() are tools to help write
# wrapper functions that can handle naive introspection
WRAPPER_ASSIGNMENTS = ('__module__', '__name__', '__doc__')
WRAPPER_UPDATES = ('__dict__',)
def update_wrapper(wrapper,
                   wrapped,
                   assigned = WRAPPER_ASSIGNMENTS,
                   updated = WRAPPER_UPDATES):
    """Update a wrapper function to look like the wrapped function

       wrapper is the function to be updated
       wrapped is the original function
       assigned is a tuple naming the attributes assigned directly
       from the wrapped function to the wrapper function (defaults to
       functools.WRAPPER_ASSIGNMENTS)
       updated is a tuple naming the attributes of the wrapper that
       are updated with the corresponding attribute from the wrapped
       function (defaults to functools.WRAPPER_UPDATES)
    """
    for attr in assigned:
        setattr(wrapper, attr, getattr(wrapped, attr))
    for attr in updated:
        getattr(wrapper, attr).update(getattr(wrapped, attr, {}))
    # Return the wrapper so this can be used as a decorator via partial()
    return wrapper
```

It uses setattr and getattr function to update the attribute informtion from wrapped function to wrapper function. 

