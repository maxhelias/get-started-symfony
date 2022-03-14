# Security

Symfony fourni plusieurs outils afin de sécuriser notre site. Par défaut, nous trouverons une sécurité sur les cookies de session et une protection [CSRF](https://en.wikipedia.org/wiki/Cross-site_request_forgery).

## Register & Authentication

Nous allons créer la partie authentification de notre site, qui va comporter les étapes suivantes :
    1. Création de l'entité `User` : https://symfony.com/doc/current/security.html#the-user
    2. Configurer le chargement de l'utilisateur (providers).
    3. Configurer la hashing des mots de passes (password_hashers).
    4. La partie d'enregistrement qui va permettre aux utilisateurs de s'enregistrer.
    5. La partie d'authentification.

1 - On crée l'entité `User` avec la commande make :

```bash
php bin/console make:user
```

2 / 3 - Dans `security.yaml` le make:user a rajouté un provider et un password_hashers

```yaml
    password_hashers:
        App\Entity\User:
            algorithm: auto
    providers:
        app_user_provider:
            entity:
                class: App\Entity\User
                property: email
```

Nous allons modifier notre entité `User` pour qu'il puisse avoir par defaut le role `ROLE_USER`

Nous allons rajouter en haut de l'entité deux constantes, puis plus bas dans le construct initialisé la variable `roles` :

```php
class User implements UserInterface, PasswordAuthenticatedUserInterface
{
    public const ROLE_USER = 'ROLE_USER';
    public const ROLE_ADMIN = 'ROLE_ADMIN';
    
    ...
        
    public function __construct()
    {
        $this->roles = [self::ROLE_USER];
    
```

Note : On peut voir que notre entité User implémente des interfaces (qui définit un "contrat" avec cette dernière) des choses à implémenter au minimun.

Note 2 : vous pouvez également hash un mot de passe avec la commande : `php bin/console security:hash-password`

4 - Nous allons créer un formulaire pour créer nos utilisateurs avec la commande :
```bash
php bin/console make:registration-form 
```

5 - Nous allons créer maintenant la partie pour l'authentification avec la commande :
```bash
php bin/console make:auth
```

Nous allons finir en mettant la redirection dans notre authenticator quand la requête est fini :
```php
return new RedirectResponse($this->urlGenerator->generate('app_blog'));
```
Ici, c'est un Service donc nous n'étendons pas de l'AbstractController, on ne peut pas utiliser le raccourci `$this->redirectToRoute('nomdelaroute')`.

Nous pouvons copier/coller la ligne commenter juste au-dessus `return new RedirectResponse($this->router->generate('some_route'));`

Dans le fichier de configuration `security.yaml`, la commande à déjà fait la configuration pour nous.

Maintenant que nous pouvons nous connectez, et nous déconnecter si vous avez choisi l'option.

Note : Vous également choisir d'utiliser un authenticator déjà fourni par Symfony : https://symfony.com/doc/current/security.html#authenticating-users

Grace à ces commandes, symfony nous a permis d'initialiser un système d'authentification et de l'utiliser presque clé en main, à nous d'analyser ce dernier pour voir ce qu'il y a dedans. **Le fait d'utiliser des outils qui nous facilitent la vie, n'exclut pas de comprendre ces derniers.**

---
Pour continuer dans notre projet, nous allons laisser la possibilité d'attacher un utilisateur (User) à un article.

Il suffira de mettre à jour notre entité `Article` comme nous l'avons fait avec `make:entity`, rajouter le champ `User` et lui dire que c'est une relation ``ManyToOne` avec l'entité `User`

Mettre à jour votre base de donnée.

Puis nous allons rajouter une ligne dans notre controlleur `Article` sur la méthode `new`, nous allons utiliser `$this->getUser()` pour nous permettre d'ajouter l'utilisateur connecter à l'article.

```php
 		if ($form->isSubmitted() && $form->isValid()) {
 				/** @var null|User $user */
            $user = $this->getUser();
            $article->setUser($user);

            $articleRepository->add($article);
            return $this->redirectToRoute('app_article_index', [], Response::HTTP_SEE_OTHER);
        }
```

Par la suite nous allons restreindre les accès aux méthodes `new`,`edit` à l'aide de l'attribut `#[isGranted]`. Rien ne plus simple nous allons le rajouter en annotations.

```php
use Sensio\Bundle\FrameworkExtraBundle\Configuration\IsGranted;

...
     #[Route('/new', name: 'app_article_new', methods: ['GET', 'POST'])]
     #[IsGranted('ROLE_USER')]
```

Si jamais nous ne sommes pas connectés avec un utilisateur qui à dans son tableau de role `ROLE_USER`, j'aurais automatiquement un accès denied ou une redirection vers le formulaire de login si je ne suis pas connecté.

Pour les besoins de la suite nous allons faire un crud sur l'entité `User`.

Nous souhaitons pouvoir modifier les roles d'un utilisateur pour ce faire nous allons éditer le formulaire `Form\UserType.php` qui aura été générer via la commande `make:crud`. Par default c'est un champs text, nous souhaitons que ce soit une select multiple.

Nous allons supprimer le champs password et modifier le champ roles

```php
	->add('roles', ChoiceType::class, [
        'choices' => [
            'Administrateur' => User::ROLE_ADMIN,
            'Utilisateur' => User::ROLE_USER,
        ],
        'multiple' => true,
        'required' => true,
    ])
```

Si nous retournons sur l'interface, nous pouvons observer la possibilité d'attribué plusieurs ROLE à un utilisateur.

## Récupérer l'utilisateur connecté

### Depuis un controller
```php
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;

class ProfileController extends AbstractController
{
    public function index(): Response
    {
        // Permet de vérifier si l'utilisateur est authentifié
        $this->denyAccessUnlessGranted('IS_AUTHENTICATED_FULLY');

        // Retourne l'utilisateur ou null s'il n'est pas authentifié
        /** @var \App\Entity\User $user */
        $user = $this->getUser();

        return new Response('Bonjour '.$user->getFirstName());
    }
}
```

### Depuis un service
```php
// src/Service/ExampleService.php
// ...
use Symfony\Component\Security\Core\Security;

class ExampleService
{
    private $security;

    public function __construct(Security $security)
    {
        $this->security = $security;
    }

    public function someMethod()
    {
        $user = $this->security->getUser();
    }
}
```

### Depuis un template Twig
```twig
{% if is_granted('IS_AUTHENTICATED_FULLY') %}
    <p>Email: {{ app.user.email }}</p>
{% endif %}
```

Documentation : https://symfony.com/doc/current/security.html
