# Feedback 3

## ÉTAPE 0

Il y a clairement l'intention de bien faire. Tu as voulu séparer tes vues en plusieurs fichiers. C'est une bonne idée, mais il faut faire attention à ne pas trop en faire. Séparer les fichiers et les laisser au même niveau pour un début c'est bien. Tu as voulu les ranger dans des dossiers différents, pourquoi pas mais c'est pas nécessaire. Surtout que derrière ça t'as posé des problèmes d'affichage.

## ÉTAPE 1

 Tu as bien utilisé une route paramétrée. C'est parfait. Tu as bien récupéré l'id de la carte.
 Dans ton datamapper tu as bien fait la requête pour récupérer la carte, de plus en utilisant une requête préparée. C'est parfait. La condition à la fin n'est pas vraiment nécessaire mais ce n'est pas très grave.

 On a deux petites choses à corriger. Car en l'état on a ejs qui pleure.

 Il est pas content car comme il le dit dans son message la propriété `card` n'est pas défini.

 c'est parce que dans ton controller tu lui renvoi autre chose.

 ```js
   cardPage: async (request, response, next) => {

    try {
      const id = Number(request.params.id);

      const oneCard = await dataMapper.getCard(id);

      if (oneCard){
        response.render('main/cardDetails', {oneCard}); // ici tu lui renvoi un objet avec une propriété oneCard
      }else{
        next();
      }
      
    } catch (error) {
      console.log(error);
      response.status(500).send('Oups, nous rencontrons un problème technique');
    }    
  },
```

Tu renvoi à ta vue `cardDetails` un objet contenant une propriété `oneCard`. Or dans ton fichier ejs tu fais une boucle sur la propriété `card` qui n'existe pas.

En javascript l'assignation  de valeur à un objet peux se faire de plusieurs façon. Soit on déclare la propriété et on lui assigne une valeur. Soit on lui passe une variable. Dans le dernier cas la propriété prendra le nom de la variable.

dans ton cas par exemple:

```js
 const oneCard = await dataMapper.getCard(id);
  response.render('main/cardDetails', {oneCard});
```

Tu auras un objet avec une propriété `oneCard` qui contiendra le résultat de ta requête SQL.

Si tu veux que ta propriété ce nomme `card` il faut que tu fasse comme ça:

```js
 const oneCard = await dataMapper.getCard(id);
  response.render('main/cardDetails', {card: oneCard});
```

Le second problème est dans ton fichier ejs.

```html
        <% for (const singleCard of card) { %>

            <div class="card">
                <a href="/card/<%= card.id %>"><%= card.name %>">
                    <img src="/visuals/<%= card.visual_name.toLowerCase() %>.jpg" alt="<%= card.name %> illustration">
                    <p class="cardName">
                        <%= card.name %>
                        <%= card.element %>
                        <%= card.level %>
                        <%= card.value_north %>
                        <%= card.value_east %>
                        <%= card.value_south %>
                        <%= card.value_west %>
                        <%= card.visual_name %>
                    </p>
                </a>
                <a class="link--addCard" title="Ajouter au deck" href="/card/<%= card.id %>">
                    <%= card.name %>[ + ]
                </a>
            </div>

            <% } %>
```

Comment fonctionne une boucle ? On déclare une variable qui va contenir les valeurs de notre tableau. Ensuite on fait une boucle sur ce tableau et à chaque tour de boucle la variable prendra la valeur de l'élément du tableau.

```js
  const tableau = [1, 2, 3, 4, 5];

  for (const element of tableau) {
    console.log(element); // à chaque tour de boucle element prendra la valeur de l'élément du tableau
  }
```

au premier tour de boucle element vaudra 1, au second tour element vaudra 2 etc...

Dans ton cas tu fais une boucle sur card qui n'est pas un tableau. Donc il ne peut pas fonctionner.

Mais surtout derrière tu utilise `card` pour l'affichage.

ça revient à faire ça :

```js

const tableau = [1, 2, 3, 4, 5];

for (const element of tableau) {
  console.log(tableau); // à chaque tour de boucle j'affiche le tableau
}
```

tu affiche le tableau à chaque tour de boucle. Donc tu affiche 5 fois le tableau.
ce n'est pas tableau que je veux afficher mais `element`.

## ÉTAPE 2

 Cette étape t'as posé pas mal de soucis. On va décomposer les problèmes.

### Router

 ```js
 router.get('/search/element', searchController.searchElement);
 ```

 Voici la route que tu as crée pour la recherche par élement. C'est nickel, jusqu'ici tout va bien.

### Controller et datamapper

#### Problème 1

```js
      const element = request.params.element
```

Tu veux récupérer l'élement demandé par l'utilisateur, mais tu le fait via `request.params.element`. Or tu n'as pas de paramètre dans ta route. Donc tu ne peux pas récupérer de paramètre.

```bash
/search/element?element=tonnerre
```

voilà l'url vers laquelle je suis redirigé quand je fais une recherche par élement.

`search/element` correspond à la route que tu a déclaré. Mais que peux bien vouloir dire `?element=tonnerre`?

Et surtout comment je peux récupérer cette valeur ?

Je peux récupérer les paramètres de la route via `request.params`. Mais je n'ai pas de paramètre dans ma route. Donc ce n'est pas la solution.

Les query string sont des paramètres qui sont passés dans l'url. Ils sont séparés de la route par un `?`. Ensuite ils sont séparés entre eux par un `&`. Et enfin ils sont composés d'une clé et d'une valeur séparé par un `=`.

Heureusement pour nous express nous permet de récupérer ces résultats via `request.query`.

#### Problème 2

```js
  const result = await dataMapper.cardElement(element);
```

Tu fais appel au dataMapper, mais as-tu pensé à le require dans ton controller ?
Javascript ne connait pas dataMapper. Il faut lui dire où le trouver.

#### Problème 3
  
  ```js
  // searchController.js
    const result = await dataMapper.cardElement(element);

    // dataMapper.js
      getCardByElement: async () => {
  
    const queryResult = await database.query(
      `
      SELECT *
      FROM "card"
      WHERE "element" IS NOT NULL;
      `, 
    
    );
    
      return queryResult.rows;
    }

  ```

tu fais appel à la méthode `cardElement` du dataMapper. Quand on va dans ton dataMapper on voit bien que cette méthode n'existe pas. Il y a bien une méthode `getCardByElement` mais pas `cardElement`.

#### Problème 4

 Même si tu fait appel à la bonne fonction tu te retrouves avec un autre problème. Tu lui passe un argument `element`. Mais tu ne lui a pas défini de paramètre. Donc il ne peut pas le récupérer.

 lorsque je déclare une fonction je peux lui passer des paramètres. Ces paramètres seront accessibles dans la fonction. Mais si je ne défini pas de paramètre, je ne peux pas lui passer d'argument.

 ```js
  function faireUnGateau(){
    // mon code
  }

  faireUnGateau('chocolat');
 ```

Quand j'ai défini ma fonction qui fait un gâteau, je n'ai pas dis que j'attendais des ingrédients. Soit je sait que j'ai tout ce qu'il me faut. Soit je me suis trompé et en faite je n'ai pas tout ce qu'il me faut.

Mais si je dis que je n'attend pas d'ingrédients, même si on me passe du chocolat je ne pourrais pas le récupérer.

```js
  function faireUnGateau(ingredient){
    // mon code
  }

  faireUnGateau('chocolat');
```

#### Problème 5

Tu as bien fait une requête SQL. Mais elle ne correspond pas à ce que tu veux faire. Tu veux récupérer les cartes qui ont un élément précis. Mais ta requête ne fait pas ça. Elle récupère toutes les cartes qui ont un élément. Ce n'est pas la même chose.

Tu recevras toutes les cartes qui ont un élément, mais tu ne pourras pas savoir lequel.

#### Problème 6

```js

// searchController.js
  const result = await dataMapper.cardElement(element);

  if (result){
    response.render('main/element/', {element});
  }else{
    next();
  }

```

Tu fais une condition pour savoir si tu vas avoir un résultat. Vu la requête que tu as fait, tu auras toujours un résultat. Et oui vu que tu récupères toutes les cartes qui ont un élément.

#### Problème 7

```js
  response.render('main/element/', {element});
```

 Tu veux render sur la vue `main/element/`. Mais tu n'as pas de vue qui s'appelle comme ça. Tu as une vue qui s'appelle `main/element`. C'est pas la même chose. Le `/` à la fin fait toute la différence.
  C'est le même problème que pour les dossiers. Tu as voulu trop en faire. Concentre toi sur ce qui est demandé.

 En laissant tout au même niveau tu n'aurais pas eu ce problème. Car il t'aurais suffit de faire `element` pour render sur la vue en question.

#### Problème 8

```js
  response.render('main/element/', {element});
```

Tu veux passer à ta vue la valeur de la variable `element`.

```js
      const element = request.params.element

      const result = await dataMapper.cardElement(element);
```

Mon tableau de carte est stocké dans quel variable ?

## ÉTAPE 3

### 3.1 Activer les sessions

Super tu as bien activé les sessions. Tu as bien compris le principe. C'est parfait.
Pense à bien mettre le `secret` dans le fichier `.env` pour le moment tu l'as mis en dur dans le fichier `index.js`.

```js
//router.js
const express = require('express');
const session = require('express-session');
const router = express.Router();
```

Tu as require `express-session` dans ton fichier `router.js` mais tu ne l'utilises pas. C'est bien de le laisser dans l'index. Car c'est dans l'index que tu initialise ton serveur. Et c'est là que tu dois initialiser les sessions.

## Conclusion

Il y a de bonne choses dans ce que tu as fait. Je pense en revanche que tu as voulu en faire trop.
Focalise toi sur ce qui est demandé pour éviter les confusions. L'idée de faire une page 404 et de la mettre à la fin était très bonne. Mais tu n'as pas le résultat obtenu car tu as fait une erreur dans l'ordre des choses.

```js
app.use(router);
app.use(mainController.cardPage);
app.use(mainController.notFound);
```

résultat si l'utilisateur fait une requête sur une route qui n'existe pas il tombera d'abord sur le controller `cardPage` qui elle cherche à afficher une carte. Et puis ce n'était pas demandé dans le parcours.

Ce n'est pas grave, c'est en faisant des erreurs qu'on apprend et ça tombe très bien vu que c'est le but de cette formation. Ne te décourage pas, tu as fait de bonnes choses. Il faut juste que tu te concentre sur ce qui est demandé et que tu ne t'éparpille pas. Tu as le temps de faire des choses plus compliquées plus tard. Pour le moment concentre toi sur les bases.

Souffle un peu, le parcours était long et il n'était pas facile. C'est tout à fait normal qu'il y ai des confusions.

Prend le temps de lire la correction et de comprendre ce qui n'allait pas. Si tu as des questions n'hésite pas à me les poser.
