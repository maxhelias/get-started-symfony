## Les Formulaires

Nous pouvons créer des formulaires via le template ou bien nous pouvons laisser Symfony faire tout ça grâce à la commande :

```bash
# php bin/console make:form
```

Nous allons lui donner l’entity sur lequel elle va se calquer, puis ensuite nous choisirons de rajouter le bouton submit ou pas dans ce FormType.

```php
public function buildForm(FormBuilderInterface $builder, array $options): void
{
    $builder
        ->add('title')
        ->add('content')
        ->add('save', SubmitType::class)
    ;
}
```

Nous pourrions dans ce fichier personnaliser les labels ou autres en passant des paramètres.

```php
public function buildForm(FormBuilderInterface $builder, array $options)
{
    $builder
        ->add('title', TextType::class, [
            'label' => 'Notre titre',
        ])
        ->add('content')
        ->add('save', SubmitType::class)
    ;
}
```

Ainsi, nous allons appeler ce ArticleType dans notre controller:

```php
#[Route('/blog', name: 'app_blog')]
    public function index(Request $request, ArticleRepository $articleRepository): Response
    {
        $article = new Article();
        $form = $this->createForm(ArticleType::class, $article);
        $form->handleRequest($request);

        if ($form->isSubmitted() && $form->isValid()) {
            $articleRepository->add($article);

            $this->addFlash('success', 'Votre article est enregistré');

            return $this->redirectToRoute('home');
        }

        return $this->renderForm('blog/index.html.twig', [
            'controller_name' => 'BlogController',
            'form' => $form,
        ]);
    }
```

----
On initialise le formulaire qu'on a créé précédemment :
```php
$form = $this->createForm(ArticleType::class, $article);
```

----
On injecte les informaitons de request dans notre formulaire :
```php
$form->handleRequest($request);
```

Pour cela la class `Request $request` sera injecté dans la méthode de notre controller, c'est un pilier de `HttpFondation` tout comme `Response`, je vous invite à voir la documentation : https://symfony.com/doc/current/components/http_foundation.html
Il va nous servir à récupérer nos `$_GET`, `$_POST`, `$_SERVER` et bien plus encore.

----
On vérifie quand notre formulaire sera soumis et valid :
```php
if ($form->isSubmitted() && $form->isValid()) {
    $articleRepository->add($article);

    $this->addFlash('success', 'Votre article est enregistré');

    return $this->redirectToRoute('home');
}
```

*Comment enregistre-t-on en base ?*

Nous avons vu très vite l'entity manager quand nous avons chargé nos fixtures dans l'orm. Nous allons l'utiliser pour enregistrer en base notre object.

À quoi sert cette Entity Manger ? C'est très simple vous n'avez plus à vous soucier de faire un `INSERT` ou un `UPDATE` en base, l'orm va s'occuper de tout, vous n'avez qu'à charger le manager/repository (à partir de `EntityManagerInterface` ou votre repository directement pour de l'injection directe).

Pour ce faire, nous allons utiliser la fonction `add` du repository généré qui va appeler les méthodes : `persist` et `flush`

Je vous remet le lien vers la documentation Doctrine : https://symfony.com/doc/current/doctrine.html

----
On passe notre formulaire au template twig pour l'afficher :
```php
return $this->renderForm('blog/index.html.twig', [
    'controller_name' => 'BlogController',
    'form' => $form,
]);
```

Dans le twig pour afficher notre formulaire, on appelera les méthodes Twig :

```twig
{{ form(form) }}
```

Et voila !

On pourrait personnaliser les forms en détaillant le tout

```twig
{{ form_start(form) }}
{{ form_errors(form) }}
{{ form_widget(form.title) }}
{{ form_widget(form.content) }}
{{ form_widget(form.save) }}
{{ form_end(form) }}
```

Comme nous avons ajouté Bootstrap précédemment, nous pouvons indiquer à Symfony d'utiliser le thème "boostrap" des formulaires pour avoir un meilleur rendu :
```yaml
# config/packages/twig.yaml
twig:
    form_themes: ['bootstrap_5_layout.html.twig']
```

Aller plus loin avec les formulaires : https://symfony.com/doc/current/forms.html
List des contraintes : https://symfony.com/doc/current/validation.html#basic-constraints
