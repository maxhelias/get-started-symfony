# Assets

Si dans un template nous avons besoin d'un lien vers une ressource statique (par exemple une image), Symfony fournit une fonction Twig `asset()` qui va nous aider à générer le bonne URL.

Exemple :
```twig
{# Une image situé dans "public/images/logo.png" #}
<img src="{{ asset('images/logo.png') }}" alt="Symfony!"/>

{# Un fichier CSS dans "public/css/blog.css" #}
<link href="{{ asset('css/blog.css') }}" rel="stylesheet"/>

{# Un fichier JS dans "public/bundles/acme/js/loader.js" #}
<script src="{{ asset('bundles/acme/js/loader.js') }}"></script>
```

Si vous avez besoin d'une URL absolue, utilisez la fonction twig `absolute_url()` :
```twig
<img src="{{ absolute_url(asset('images/logo.png')) }}" alt="Symfony!"/>
```

# Webpack Encore

Symfony est livré avec une bibliothèque javaScript - Webpack Encore - qui rend l'utilisation des CSS & JS plus simplement.
Webpack Encore est une façon plus simple d'intégrer Webpack dans votre application qui lui va vous permettre de regrouper les fichiers CSS & JS, les pré-traiter et les compiler.

Avec Webpack, nous allons utiliser un gestionnaire de dépendance "fron-end" `npm` ou `yarn` et inclure le style de bootstrap sur notre site.

En installant `symfony/webpack-encore-bundle`, Flex va nous pré-configurer :
 - Un dossier `assets/`, ou nous mettrons toutes nos sources front-end.
 - Un fichier `webpack.config.js`, qui sera la base de la configuration Webpack.
 - Un fichier `package.json`, qui sera notre fichier de gestion de dépendance.

Comme vu précédemment, nous avons utilisé le CDN de Bootstrap pour ajouter du style à notre site et configurer un thème Twig avec "bootstrap_5_layout.html.twig" inclus dans le package `symfony/twig-bridge`.
Nous allons maintenant remplacer le CDN par les fichiers sources que nous allons récupérer avec notre gestionnaire `npm`.

- On installe le package `bootstrap` :
```bash
npm install boostrap
```
- Puis dans le fichier `assets/app.js`, on importe les sources JS comme ceux-ci : `import 'bootstrap';`
- Pareil dans le fichier `assets/style/app.css`, on importe les sources CSS comme ceux-ci : `@import 'bootstrap';`
- Ensuite nous allons supprimer la balise CDN de bootstrap ajouté dans une leçon précédente de notre template `base.html.twig` et définir les fonctions twig `{{ encore_entry_link_tags('app') }}` pour le CSS et `{{ encore_entry_script_tags('app') }}` pour le JS.
Note : `app` utilisé comme paramètre est défini dans la configuration `webpack.config.js`.

Maintenant, nous pouvons compiler les sources avec la commande `npm run watch` qui est un script défini dans le fichier `package.json`.

Félicitations, maintenant vous avez des fichiers dans le dossier `public/build/` qui sont vos assets finaux optimisé par Webpack.

Documentation :
 - https://symfony.com/doc/current/templates.html#linking-to-css-javascript-and-image-assets
 - https://symfony.com/doc/current/frontend.html
 - https://symfony.com/doc/current/frontend/encore/installation.html

Aller encore plus loin : 
 - SASS / LESS : https://symfony.com/doc/current/frontend/encore/simple-example.html#using-sass-less-stylus
 - Stimulus : https://symfony.com/doc/current/frontend/encore/simple-example.html#stimulus-symfony-ux
 - Symfony UX : https://symfony.com/doc/current/frontend.html#symfony-ux-components
