# MCD

Je vois que tu as mis des `id` dans tes entités. Dans un MCD, on ne met pas d'`id`. On met juste les propriétés qui définissent l'entité. L'`id` est une notion de base de données. On ne le met pas dans le MCD.

Pense à souligner tes discriminants. Par quoi on va pouvoir désigner de façon unique chaque instance de cette entité ? Dans le cas de l'entité `USER`, c'est l'`email` par exemple, donc il faut le souligner.

Dans ton MCD entre l'association `USER` et `PRODUCT`, tu as mis la cardinalité `1,1` de chaque côté.

Donc un utilisteur ne peut liker qu'un seul produit et un produit ne peut être liké que par un seul utilisateur ?

Un utilisateur ne peut pas liker plusieurs produits ? Voir aucun produit ?

Un produit ne peut pas être liké par plusieurs utilisateurs ? Voir aucun utilisateur ?

Tu as mis dans l'entité `PRODUCT` une propriété `mug_type`. J'en déduis qu'il y'a plusieurs types de mugs.

Est-ce que 2 mugs de types différents peuvent avoir le même nom ? Est-ce que 2 mugs de même type peuvent avoir un prix différent ?

Peut-être faudrait-il créer une entité spécifique pour les types de mugs ? Et créer une association entre `PRODUCT` et cette nouvelle entité ?

Dans l'entité `ORDER`, tu as une propriété `status`. Est-ce que tu peux avoir plusieurs status pour une même commande ? Est-ce que tu peux avoir plusieurs commandes avec le même status ? 

Peut-être faudrait-il créer une entité spécifique pour les status ? Et créer une association entre `ORDER` et cette nouvelle entité ?


Les associations dans le MCD sont généralement de forme ovale. Tu as mis des rectangles. C'est pas grave, mais c'est plus clair avec des ovales.

Je t'invite à consulter [le site mocodo](https://www.mocodo.net/) pour faire des MCD.

La syntaxe est un peu spécial, je te donnes rapidement des exemples :

pour déclarer une entité :

```
ENTITE: propriété1, propriété2, propriété3

ENTITE2: propriété1, propriété2, propriété3
```

La propriété 1 est soulignée, c'est le discriminant.


pour déclarer une association :

```
NOM_ASSOCIATION, 0N ENTITE1, 11 ENTITE2
```

Ici on met le nom de l'association suivi d'une virgule, la cardinalité de l'entité 1 suivi d'un espace, le nom de l'entité 1 suivi d'une `,` la cardinalité de l'entité 2 suivi d'un espace, le nom de l'entité 2.

En espérant que ça t'aide.