# Services / DI

Lorsque l'on conçoit une application complexe on est souvent amené à faire communiquer plusieurs objets ensembles à travers un système d'injection de dépendance.
Les classes deviennent alors dépendantes les unes des autres rendant la phase d'initialisation un peu verbeuse.

Pour résoudre ce problème on peut utiliser un conteneur d'injection de dépendance qui va garder en mémoire les "recettes" qui vont servir à initialiser les objets.

Voyez le conteneur comme un grand tableau qui contient en clef le nom de la classe et en valeur une instance de cette classe avec ces dépendances et ça de manière récursive.

Dans Symfony chaque object utiles sont appelés services et chaque service vit à l'intérieur du *container*.
Il permet de centraliser la manière dont les objets sont construits. Il va vous facilite la vie.

## Recuperation d'un service

Dans votre controller, vous pouvez "demander" un service à partir du container en indiquant un argument avec le nom de la classe ou de l'interface du service.
```php
public function list(LoggerInterface $logger)
```

Quels autres services sont disponibles ? Utilisez la commande suivante pour le savoir :
```bash
php bin/console debug:autowiring
```

## Chargement automatique des services

Grâce à la configuration par défaut de Symfony via le fichier `config/service.yaml`, vous pouvez utiliser n'importe quel classe en tant que service sans avoir besoin de le configurer manuellement.

## Public vs Private

Chaque service est défini en "private" par défaut. Quand un service est privé, vous ne pouvez pas y accéder directement depuis le container en utilisant `$container->get()`. Les bonnes pratiques de Symfony recommande de créer les services en privée et de les injecter en dépendance au lieu de `$container->get()`.

## Configuration

TODO

Documentation : 
 - https://symfony.com/doc/current/service_container.html
 - https://symfony.com/doc/current/service_container.html#services-explicitly-configure-wire-services
 - https://symfonycasts.com/screencast/symfony-fundamentals/debug-container
