# Refactoring with Templates

- In the previous tutorials, we did a fair bit of repetition, let's stay [DRY!](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)

## Adding a Base template

- The base template will contain all the HTML head elements and will wrap our body element
- It will also contain a title variable that we can pass in
- Create 'base.html' inside '/templates' and add the code:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link
      href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css"
      rel="stylesheet"
      integrity="sha384-T3c6CoIi6uLrA9TneNEoa7RxnatzjcDSCmG1MXxSR1GAsXEV/Dwwykc2MPK8M2HN"
      crossorigin="anonymous"
    />
    <title>{% block title %}{% endblock %}</title>
  </head>
  <body class="container p-3">
    {% block content %}{% endblock %}
  </body>
</html>
```

- The 'block' statements allow us to define where we are going to inject blocks from a parent template
- The main content is going to use the "content" block  class of "container" ("container" is a useful bootstrap class that allows us to use bootstrap's layour tools, [read more here](https://getbootstrap.com/docs/5.0/layout/containers/))


## Extending The Base Template

- Modify form.html by removing everything that is NOT inside the body element, including the body element itself (as we already have it in the base class)
- Add an extends statement to the top of the file and define a title block

```html
{% extends 'base.html' %}

{% block title %}A Form to Add a New User{% endblock %}
```

- Wrap the remaining form html in a content block

```html
{% block content %}

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

{% endblock %}
```

- Test your new template (try different titles to verify it is working, you may need to restart your flask app)
- Now do the same for the results.html template.

## Navigation

- Have a look at [bootstrap's navigation components](https://getbootstrap.com/docs/4.0/components/navbar/)
- Note that some of the bootstrap navbar functions may require script tags with links to the bootstrap JS CDNs (they can be added in the base template at the bottom of the body, [have a look here](https://getbootstrap.com/docs/4.0/getting-started/introduction/))
- You <em>could</em> put the navbar in the base template, but think about separation of concerns, it would be better to use a template.