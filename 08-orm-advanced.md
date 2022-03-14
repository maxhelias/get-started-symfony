## Relier Article à Author

Nous allons relier les entité Article & Author.

Pour cela, nous allons mettre à jour les entités en réutilisant la commande :

```bash
php bin/console make:entity
```

Et en lui passant cette fois le nom d'une entité déjà existante.

```
 Class name of the entity to create or update (e.g. GrumpyPuppy):
 > Article

 Your entity already exists! So let's add some new fields!

 New property name (press <return> to stop adding fields):
 > author

 Field type (enter ? to see all types) [string]:
 > relation

 What class should this entity be related to?:
 > Author

What type of relationship is this?
 ------------ ------------------------------------------------------------------ 
  Type         Description                                                       
 ------------ ------------------------------------------------------------------ 
  ManyToOne    Each Article relates to (has) one Category.                       
               Each Author can relate/has to (have) many Article objects       
                                                                                 
  OneToMany    Each Article relates can relate to (have) many Author objects.  
               Each Author relates to (has) one Article                        
                                                                                 
  ManyToMany   Each Article relates can relate to (have) many Author objects.  
               Each Author can also relate to (have) many Article objects      
                                                                                 
  OneToOne     Each Article relates to (has) exactly one Author.               
               Each Author also relates to (has) exactly one Article.          
 ------------ ------------------------------------------------------------------ 

 Relation type? [ManyToOne, OneToMany, ManyToMany, OneToOne]:
 > ManyToOne

 Is the Article.author property allowed to be null (nullable)? (yes/no) [yes]:
 > 

 Do you want to add a new property to Category so that you can access/update Article objects from it - e.g. $category->getArticles()? (yes/no) [yes]:
 > 

 A new property will also be added to the Author class so that you can access the related Article objects from it.

 New field name inside Author [articles]:
 > 
 
 updated: src/Entity/Article.php
 updated: src/Entity/Author.php

 Add another property? Enter the property name (or press <return> to stop adding fields):
 > 
           
  Success! 
```

Nous n'avons plus qu'à mettre à jour notre base de données

```bash
php bin/console make:migration
php bin/console doctrine:migrations:migrate 
```

ou

```bash
php bin/console doctrine:schema:update --force
```

On va créer le CRUD pour l'entité `Author` :

```bash
php bin/console make:crud
```

Et il ne nous reste plus qu'à mettre à jour notre formulaire, rendons-nous sur le `Form/ArticleType.php`

```php
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder
            ->add('title')
            ->add('content')
            ->add('author', EntityType::class, [
                'class' => Author::class,
                'choice_label' => 'name',
            ])
            ->add('save', SubmitType::class)
        ;
    }
```
Nous rajoutons author qui est du Type Entity, nous allons voir dans la documentation pour connaitre les différents paramètres à lui fournir : https://symfony.com/doc/current/reference/forms/types/entity.html

Nous pouvons voir qu'il faut lui donner le paramètre `class` pour le lié à notre entité Category, puis lui donner `choice_label` pour l'affichage dans notre select. Ce dernier paramètre est optionel si vous avez configuré l'appelle __toString() dans votre entité.
