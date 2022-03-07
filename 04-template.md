## Twig

Nous allons passer au fichier précréé `blog/index.html.twig` par maker

```twig
{% extends 'base.html.twig' %}

{% block title %}Hello BlogController!{% endblock %}

{% block body %}
<style>
    .example-wrapper { margin: 1em auto; max-width: 800px; width: 95%; font: 18px/1.5 sans-serif; }
    .example-wrapper code { background: #F5F5F5; padding: 2px 6px; }
</style>

<div class="example-wrapper">
    <h1>Hello {{ controller_name }}! ✅</h1>

    This friendly message is coming from:
    <ul>
        <li>Your controller at <code><a href="{{ '/Users/maximehelias/www/sandbox/blog/src/Controller/BlogController.php'|file_link(0) }}">src/Controller/BlogController.php</a></code></li>
        <li>Your template at <code><a href="{{ '/Users/maximehelias/www/sandbox/blog/templates/blog/index.html.twig'|file_link(0) }}">templates/blog/index.html.twig</a></code></li>
    </ul>
</div>
{% endblock %}
```

Nous retrouvons notre variable qui est `controller_name` qui est entourée d'accolades.

Nous pouvons observer différents `block`, c'est ce que l'on va trouver dans le fichier qu'il extend `base.html.twig`. Dans ce fichier il y a tout simplement la structure de base de notre site.

Pour le blog, nous allons la remanier, nous allons y rajouter bootstrap, mais juste le css via le CDN (Content Delivery Network).

Ca donne ça

```twig
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>{% block title %}Welcome!{% endblock %}</title>
        <link rel="icon" href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 128 128%22><text y=%221.2em%22 font-size=%2296%22>⚫️</text></svg>">
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
        {# Run `composer require symfony/webpack-encore-bundle` to start using Symfony UX #}
        {% block stylesheets %}
            {{ encore_entry_link_tags('app') }}
        {% endblock %}

        {% block javascripts %}
            {{ encore_entry_script_tags('app') }}
        {% endblock %}
    </head>
    <body>
        {% block body %}{% endblock %}
    </body>
</html>

```

C'est dans ce fichier que nous allons créer nos blocks que l'on remplira par des données.

Plus tard nous pourrons avoir besoin des variables globales telles que `{{ app.request }}`, `{{ app.session }}`, `{{app.user }}`

Documentation Twig : https://twig.symfony.com/doc/3.x/
Documentation Symfony : https://symfony.com/doc/current/templates.html
