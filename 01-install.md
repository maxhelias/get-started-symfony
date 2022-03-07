## L'environnement

Sur votre poste de travail, il faut vérifier que [composer](https://getcomposer.org/download/), php et le binaire [Symfony](https://symfony.com/download) sont installés

```bash
composer -v
php -v
symfony -v
```

## Installation

Nous allons ouvrir la console, et taper les lignes suivants dans notre dossier de travail :

```bash
# Pour une application web traditionnelle
symfony new blog --webapp

# Pour une application microservice, console ou API
symfony new blog

# Pour avoir l'application de démonstration Symfony
symfony new my_project_directory --demo
```

À partir de ce moment-là nous avons la structure de notre futur projet symfony.
Regardons cela de plus près:

* **assets** : On placera ici les fichiers de styles (css), les fichiers javascripts qui nous permettrons d'animer l'affichage et les images.

* **bin** : On y trouve le fichier console, nous reviendrons sur son utilisation.

* **config** : On y trouve tous les fichiers de configuration du framework quelque soit l'environnement.

* **migrations** : On placera ici des scripts pour modifier la base donnée (on pourra automatiquement les gérer).

* **public** : On y trouve tous vos fichiers publics, c’est-à-dire accessible par le navigateur. Il comporte un index.php qui va s’occuper de rooter vos données et les fichiers assets une fois compilé.

* **src** : Le répertoire contiendra vos controllers, vos entités et toute votre logique métier.

* **templates** : On y trouve tous les fichiers de template twig.

* **translations** : Tous fichiers de langue seront ici.

* **var** : On y trouve les fichiers de cache et vos logs.

* **vendor** : C'est le répertoire qui est géré par composer pour nos dépendances.

À la racine du projet nous allons nous intéresser à deux fichiers:

* **.env** : C'est dans ce fichier que seront nos données pour connecter la base de données pour dire si le projet est en prod ou en dev ainsi que différentes choses liées à la config des futurs bundles.

* **composer.json** : Tous les paquets dont nous avons besoin pour notre projet.

Nous allons ensuite démarrer notre serveur de développement fourni par le binaire Symfony :

```bash
symfony server:start
```

Nous accédons à une page qui nous prévient que le framework Symfony est bien installé et prêt à l'emploi.
