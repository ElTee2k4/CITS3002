- In order to render templates from a template folder in a Python server, the simple mechanism to doing this is using the `render_template()` function.

```Python
@app.route('/')
def index():
	return render_template('index.html')

```

Template Inheritance:

- It simply means that a master HTML page is created that contains a skeleton for each page and every other page is inherited from this page.
	-You can then insert code where you need it.
- Thus, the following JINJA code can be inserted where you want to insert your data in the master.html file:

```HTML
<body>
	{% block body %}{% endblock %}
</body>
```

- What this does is now, you have the option to create the standard template for every single HTML file but can change the body and head to what you please. This is done in a child HTML file:

```HTML
{% extends 'base.html' %}

{% block body %}
	<h1>Template</h1>
{% endblock %}

```

- Now for the index HTML file, the body will be placed within the block for body.

JINJA Syntax:

- The `{% INPUT %}` formatting scheme is used for logical operations, such as if statements, inserting loops etc.
- `"{{ STRING }}"` is the formatting used for inputting strings.

Control statements in JINJA:

```HTML
{% for item in list %}
	<p>{{item.name}}</p>
{%endfor%}
```









```JavaScript
$(document).ready(function(){
	$.ajax({
		url: "whatever.com",
		type: "GET",
		dataType: "json",
	}).done(function(data){
		time_left = JSON.parse(data).time_left;
		div.replaceChild(data, child);
	})
})
```

