# Feedback 1

## ÉTAPE 0

Bonne analyse du code, tu as bien structuré ton projet.

## ÉTAPE 1

Bonne utilisation des routes paramétrées, tu as bien compris le principe. La conversion en nombre est une bonne pratique.

Tu peux vérifier que la conversion s'est bien passée, une petite condition `if` peut être utile. La fonction `isNaN()` peut t'aider.

Un exemple d'utilisation de `isNaN()`:

```js
const id = Number(req.params.id);

if (isNaN(id)) {
  // id n'est pas un nombre
  return next();
}
```

[Explication de la fonction isNaN()](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/isNaN)

## ÉTAPE 2

Ta méthode `searchByElement` de ton datamapper est bien écrite, tu as bien compris le principe de la requête préparée. C'est super d'avoir simplifié ta méthode selon que l'élément soit `null` ou non.

Tu as une erreur dans le renvoi du titre de la page dans ta méthode `searchByElement` du `Searchcontroller`.

```js
res.render('main/cardList', {
  cards: searchedResults,
  title: 'Résultat de la recherche : ' + (searchedElement === 'null' ? ' sans élément' : +searchedElement),
});
```

Tu as mis un `+` devant `searchedElement` après le `:`, ce qui va convertir `searchedElement` en nombre. Or, `searchedElement` est une chaîne de caractères, donc le `+` va renvoyer `NaN`. Tu peux le vérifier en faisant un `console.log(typeof searchedElement)`.

Pour corriger l'erreur, il suffit de supprimer le `+` devant `searchedElement`.

```js
res.render('main/cardList', {
  cards: searchedResults,
  title: 'Résultat de la recherche : ' + (searchedElement === 'null' ? ' sans élément' : searchedElement),
});
```

Ou avec des backticks:

```js
res.render('main/cardList', {
  cards: searchedResults,
  title: `Résultat de la recherche : ${searchedElement === 'null' ? ' sans élément' : searchedElement}`,
});
```

Personnellement, je préfère la deuxième solution, je trouve que c'est plus lisible et ça t'évites de te poser la question de savoir si tu dois mettre un `+` ou pas.

## ÉTAPE 3

### 3.1 Activer les sessions

Tu as très bien fait de définir ta clé de session dans une variable d'environnement, c'est une bonne pratique de sécurité.

N'oublie pas de mettre à jour ton fichier ***.env.example*** pour que les autres personnes qui travaillent sur le projet puissent savoir quelles variables d'environnement sont nécessaires au bon fonctionnement du projet.

Tu as mis en place un middleware qui initialise un tableau de cartes dans la session. C'est une superbe idée.

De plus, tu as bien pensé à vérifier si le tableau de cartes existe déjà dans la session.

En positionnant le middleware avant la route dans le routeur, tu t'assures que le tableau de cartes est bien initialisé avant d'arriver sur la route. Rien à redire, c'est parfait.

Dans ce cas, tu n'as pas besoin de ta condition pour vérifier si le tableau de cartes existe déjà dans la session au niveau de la méthode `deckPage` du `Cardcontroller`.

```js
  deckPage: (req, res) => {
    // Initialisation des cartes du deck, pour l'instant vide
    let cards = [];
    if(req.session.cards) {
      // Ajout des cartes en session au deck
      cards = req.session.cards;
    }
    res.render('card/deck', { cards });
  },
```

peut être remplacé par:

```js
deckPage: (req, res) => {
  // Je suis sûr d'avoir au minimum un tableau vide car le middleware a été exécuté avant.
  res.render('card/deck', { cards: req.session.cards });
},
```

### 3.2 Ajouter une carte au deck

RAS

### 3.3 Une page pour visualiser le deck

RAS

### 3.4 Supprimer une carte du deck

RAS

## Conclusion

Excellent parcours. Tu es très à l'aise. J'ai beaucoup apprécié le fait que tu veuilles améliorer ton code (la refactorisation de la méthode `searchByElement` du `Searchcontroller`). Tu as d'abord fait en sorte que ton code fonctionne, puis tu as cherché à l'améliorer. C'est une très bonne démarche. Tu as clairement bien compris les notions vues en cours. Continue comme ça.
