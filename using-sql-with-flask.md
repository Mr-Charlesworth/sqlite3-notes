# Using SQLite with Flask

- This tutorial assumes you have created a .db file and placed it at the root of a flask project (alongside your app.py or main.py)
- It is also assuming the following table:

```sql
create table users (id integer primary key autoincrement, name text, location text);
```

- If you have not done this, please see [Getting Started with Sqlite](./getting-started-with-sqlite.md)

## Setting up the Database Connection

- We want connections to the database to be opened for a request if it is needed and closed when the request has finished
- Flask helps us manage a connection to a database by using the 'g' object which we can use to hold data in an app context ([read more here](https://flask.palletsprojects.com/en/2.3.x/appcontext/)). This needs to be added to our imports:

```python
from flask import Flask, request, render_template, g
```

- Let's first create a function to create a connection to the sqlite database and return an object that represents that connection

```python
def connect_db():
    sql = sqlite3.connect('demo.db') # Assuming your database file is named 'demo.db'
    sql.row_factory = sqlite3.Row # Some setup that allows our query results to be returned as dictionaries
    return sql
```

- Now we can write a function that requests can use to get a connection to the database that either already exists in the context or needs to be made

```python
def get_db():
    if not hasattr(g, 'sqlite_db'):
        g.sqlite_db = connect_db()
    return g.sqlite_db
```

- The last thing we need to do is manage closing the connection when it is no longer needed by a request (referred to as the 'teardown')

```python
@app.teardown_appcontext
def close_db(error):
    if hasattr(g, 'sqlite_db'):
        g.sqlite_db.close()
```

## Adding data

- Lets create a form to add people
- First, we need a form template, create a 'form.html' file in your 'templates' folder and add the following code:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css">
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Add a Person!</title>
</head>
<body class="container p-3">
    <h1>Enter your details</h1>
    <form method="POST" action="/form">
        <div class="form-group">
            <label for="name">Name</label>
            <input class="form-control" type="text" id="name" name="name">
        </div>
        <div class="form-group">
            <label for="location">Location</label>
            <input class="form-control" type="text" id="location" name="location">
        </div>
        <button type="submit" class="btn btn-primary mt-1">Submit</button>
    </form>
</body>
</html>
```

> Note: The above uses bootstrap which is a CSS library, I'm using some of bootstraps form components which you can read about [here](https://getbootstrap.com/docs/5.3/forms/overview/)

- Beautiful! The important parts in the template we created are:
  - The form 'method' is 'POST', this is the HTTP method our flask app needs to handle to receive the form data
  - The form 'action' is '/form', this is the HTTP URL (or 'endpoint') our flask app needs to receive the form data
  - The 'name' attributes for our inputs are 'name' and 'location', these are the keys which will allow us access to the form data in flask
- Now we can write a route handler function in the flask app.
- The handler we will create will handle both serving the form template AND receiving and processing the data
- To do this we can accept both GET and POST methods ahndle each separately:

```python
@app.route('/form', methods=['GET', 'POST'])
def form():
    if request.method == 'GET':
        return render_template('form.html')
    else:
        name = request.form['name']
        location = request.form['location']
        db = get_db() # Use the function we made earlier
        db.execute('insert into users (name, location) values (?, ?)', [name, location]) # Here we use an array of values whose order matches the order of the '?, ?' placeholders. Using placeholders binds Python values to SQL statements and avoids SQL injection attacks
        db.commit()
        return render_template('form.html')
```

- We will create a route and template for querying and viewing data in a moment, but it is worth checking using the sqlite CLI utility:

```bash
$ /path/to/sqlite/sqlite3 /path/to/db-file/demo.db
```

- Then simply enter the SQL at the 'sqlite > ' prompt:

```sql
SELECT * from users;
```

- Verify you can see the details entered in to your app.

## Viewing / Querying Data

- It's much easier!
- Create a 'results.html' file inside your '/tempaltes' folder and enter the following code:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css">
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body class="container p-3">
    {% for result in results %}
   
    <div class="card mt-2" style="width: 18rem;">
        <div class="card-body">
          <h5 class="card-title">{{ result.id }}: {{ result.name }}</h5>
          <p class="card-text">From: {{ result.location }}</p>
        </div>
    </div>

    {% endfor %}
</body>
</html>
```

> Note: The above uses the bootstrap card component, read about it [here](https://getbootstrap.com/docs/4.0/components/card/)

- You will notice that we are using a 'result' variable, this will be the list of results from the database where each element is a python dictionary
- Great! Let's make the route handler:

```python
@app.route('/results')
def view_results():
    db = get_db()
    cur = db.execute('select id, name, location from users') # 'cur' for cursor
    results = cur.fetchall() # fetchall knows to return the results as a list of python dictionaries thanks to our 'sql.row_factory = sqlite3.Row' in the connect_db function
    return render_template('results.html', results=results)
```

- Test it!

## Big Brain Tasks

1. In the results view, give each result a 'Delete' button that deletes the user from the database and shows the updated results page
2. In the results view, give each result an 'Edit' button that takes you to a pre-populated form that can be used to update the name and location of the selected user.

> Hints:
>
> - Use the id field in each result to refer to that entity/user.
> - You did [path params](https://pythonbasics.org/flask-tutorial-routes/#flask-route-params) on Monday, can you use them here?

## Solutions

### 1. Deleting Users

- Create a flask route for deleting users.
- Use the route to delete the user with the id of the passed path parameter.
- Redirect to the results page

```python
@app.route('/delete-user/<id>', methods=['GET'])
def delete_user(id):
    db = get_db()
    db.execute('DELETE from users where id = ?', [id])
    db.commit()
    return redirect('/results')
```

- Add a form to each user card in the results template
- The form only needs a submit button but ensure that the method and action matches the route you just made

```jinja
<form method="GET" action="/delete-user/{{ result.id }}">
    <input type="submit" value="Delete"/>
</form>
```

### 2. Editing Users

- Create a route that handles both
  - GET to show the edit form, accepting an id parameter to find the user to edit.
  - POST to accept the updated information from the submitted form

```python
@app.route('/edit-user/<id>', methods=['GET', 'POST'])
def edit_user(id):
    if request.method == 'GET':
        db = get_db()
        cur = db.execute('SELECT * from users WHERE id=?', [id])
        result = cur.fetchone()
        return render_template('edit.html', result=result)
    name = request.form['name']
    location = request.form['location']
    db = get_db()
    db.execute('UPDATE users SET name = ?, location = ? WHERE id = ?', [name, location, id])
    db.commit()
    return redirect('/results')
```

- Note that although the form is very similar to creating a user, this is using a separate template for editing, this is my preference but arguments could also be made for using the templating engine to handle both creating and editing from the same template
- Create the template (mostly copied from form.html template)

```jinja
{% extends 'base.html' %}

{% block title %}Edit User{% endblock %}

{% block content %}

    <h1>Edit User</h1>
    <form method="POST" action="/edit-user/{{ result.id }}">
        <div class="form-group">
            <label for="name">Name</label>
            <input class="form-control" type="text" id="name" name="name" value="{{ result.name }}">
        </div>
        <div class="form-group">
            <label for="location">Location</label>
            <input class="form-control" type="text" id="location" name="location" value="{{ result.location }}">
        </div>
        <button type="submit" class="btn btn-primary mt-1">Submit</button>
    </form>

{% endblock %}
```