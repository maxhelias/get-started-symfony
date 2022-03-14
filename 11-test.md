# Tests

Symfony fourni par défaut une librairie "PHPUnit" qui est un framework adapté pour les tests.

Il existe plusieurs types de test automatisé :
 - Test Unitaire
 - Test d'integration (test basique avec accès au service Symfony)
 - Test applicatif (test scénarisé comme dans un navigateur mais sans JS)
 - Test End-To-End (test utilisant un navigateur réel)

Mais avant de faire des tests, il nous faut des données.

## Fixtures

Pour permettre à un nouveau développeur d'intégrer le projet vite et de lui permettre d'avoir des données de test nous allons utiliser un bundle qui est `doctrine-fixtures-bundle`.

Il va permettre de définir un jeu de donnée initiale

```bash
composer require --dev doctrine/doctrine-fixtures-bundle
```
Une fois installé nous allons l'initialisé avec make

```bash
php bin/console make:fixtures
```

Nous l'appellerons AppFixtures dedans nous allons mettre des données par default

```php
    public function load(ObjectManager $manager)
    {
        $user = new User();
        $user->setEmail('john@doe.fr');
        $user->setPassword('password');
        $user->setRoles(['ROLE_USER']);
        $manager->persist($user);

        $article = new Article();
        $article->setTitle('test');
        $article->setUser($user);
        $article->setContent('Proinde die funestis interrogationibus praestituto imaginarius iudex equitum resedit magister adhibitis aliis iam quae essent agenda praedoctis, et adsistebant hinc inde notarii, quid quaesitum esset, quidve responsum, cursim ad Caesarem perferentes, cuius imperio truci, stimulis reginae exsertantis aurem subinde per aulaeum, nec diluere obiecta permissi nec defensi periere conplures.');
        $manager->persist($article);
        $manager->flush();
    }
```

Nous utilisons l'ObjectManager, qui va nous servir à faire la liaison de l'objet avec la base.
Une fois notre fichier rempli nous allons charger ces données en base.

```bash
php bin/console doctrine:fixtures:load
```

Nous pouvons voir que notre base est remplie.

De ce fait il faudrait rajouter quelque chose dans notre `AppFixtures`, le password.

Pour encoder le password nous aurons besoin d'injecter `UserPasswordHasherInterface` puis d'utiliser `hashPassword`

Cela nous donnera:

```php
	public function __construct(private UserPasswordEncoderInterface $userPasswordHasher)
    {
    }

    public function load(ObjectManager $manager)
    {
        $user = new User();
        $user->setEmail('john@doe.fr');
        $password = $this->userPasswordHasher->hashPassword($user, 'password');
        $user->setPassword($password);
        $user->setName('John Doe');
        $manager->persist($user);
```

## PHPUnit

Nous allons commencer par install le pack fourni par Symfony pour utiliser PHPUnit :

```bash
composer require --dev symfony/test-pack
```

Flex va nous rapatrier la configuration par default de PHPUnit avec les fichiers `.env.test` & `phpunit.xml.dist` qui seront ajouté à notre projet.

Le fichier `.env.test` nous servira à configurer notre environment et qui surchargera toutes les valeurs present du fichier `.env` :

```text
# define your env variables for the test env here
KERNEL_CLASS='App\Kernel'
APP_SECRET='$ecretf0rt3st'
SYMFONY_DEPRECATIONS_HELPER=999999
PANTHER_APP_ENV=panther
PANTHER_ERROR_SCREENSHOT_DIR=./var/error-screenshots

###> symfony/framework-bundle ###
APP_ENV=test
APP_DEBUG=false
###< symfony/framework-bundle ###

###> doctrine/doctrine-bundle ###
DATABASE_URL="sqlite:///%kernel.project_dir%/var/data_test.db"
###< doctrine/doctrine-bundle ###
```

Et le fichier `phpunit.xml.dist` est le fichier de configuration de PHPUnit.

Comme vous pouvez le voir nous avons configuré une nouvelle base de donnée pour nos tests. Initialisons-la :
```bash
php bin/console --env=test doctrine:database:create
php bin/console --env=test doctrine:schema:create
```

Maintenant nous pouvons commencer nos tests, écrivons notre premier test :
```bash
php bin/console make:test
```

### Créer une requête
```php
$client = static::createClient();
$crawler = $client->request('GET', '/blog');
```

### Faire des assertions
```php
self::assertResponseIsSuccessful();
self::assertCount(1, $crawler->filter('.example-wrapper'));
```

Documentation : 
 - Symfony : https://symfony.com/doc/current/testing.html
 - DAMADoctrineTestBundle : https://symfony.com/doc/current/testing.html#resetting-the-database-automatically-before-each-test
 - Foundry : https://symfonycasts.com/screencast/symfony-doctrine/foundry
