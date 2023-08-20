# Feedback 4

## ÉTAPE 1

### Router

```js
router.get('/card', mainController.cardPage);
```

Tu as déclaré une route `/card`. L'exercice demande d'accéder au détail de chaque carte.
Le problème c'est que tu n'as pas de paramètre dans ta route. Donc tu ne peux pas accéder à une carte en particulier.

Comment tu fais pour accéder à une carte en particulier ? Tu as une liste de carte, et tu veux accéder à une carte en particulier.

Il y a un moyen de savoir quel carte l'utilisateur veut voir. C'est de passer l'id de la carte dans l'url. Par exemple, si je veux voir la carte 4, je vais faire `/card/4`. Si je veux voir la carte 5, je vais faire `/card/5`.

Bien sûr je ne vais pas faire une route pour chaque carte. Sinon j'aurais une infinité de routes. Je peux par contre faire une route qui va récupérer la valeur de la carte demandée et qui va la passer au controller.

Un peu comme avec les variables, on peux passer des paramètres à une route. Par exemple, si je veux passer l'id de la carte, je peux faire `/card/:id`. Le `:` indique que c'est un paramètre. Je peux donc faire `/card/4` et récupérer la valeur 4 dans le controller.

`:id` est un paramètre, tu peux lui donner le nom que tu veux, le plus important c'est les `:`.
Bien sûr on essai de rester cohérent, donc si on parle d'id, on va mettre `:id`, si on parle de nom, on va mettre `:nom`.
Ainsi toutes les urls qui ressemblent à `/card/quelquechose` vont passer par cette route. Peu importe la valeur de `quelquechose`, elle sera récupérée par le controller.

### Controller

```js
  cardPage: async (req, res) =>{
    try {
      const card = await dataMapper.getCard();
      res.render('card',{
      
      });
    } catch(error) {
      console.error(error);
      res.status(500).send(`An error occured with the database :\n${error.message}`);
    }
  }
```

Dans ton controller tu veux maintenant afficher la carte demandée. Je ne reviens pas sur le fait que tu n'as pas de paramètre dans ta route, donc tu ne peux pas savoir quelle carte l'utilisateur veut voir.

Mais si tu avais un paramètre, tu pourrais le récupérer. Les paramètres sont stockés dans `req.params`.
ce n'est en faite qu'un objet qui contient les paramètres de la route. Donc si tu as une route `/card/:id`, tu peux récupérer la valeur de `id` dans `req.params.id`.

Le nom que tu as donné dans ta route est important, car c'est le même que celui que tu vas utiliser dans `req.params`.

```js
console.log(req.params); // { id: 4 } par exemple
```

dans ton render, tu veux renvoyer vers la vue `card` et tu lui passe un objet vide. Tu as peux être oublié de passer la variable `card` qui contient les données de la carte depuis ton dataMapper.

En lisant ta méthode `getCard` dans le dataMapper, on se dit que tu vas récupérer la même carte à chaque fois. Car tu ne précises pas quelle carte tu veux récupérer. Tu as oublié de passer l'id de la carte en argument de ta méthode.

### DataMapper

```js
const { request } = require('express');
const client = require('./database');
const database = require('./database');
```

Si tu regardes bien, tu déclares la variable `client` qui récupère le module `database` alors que la variable `database` le fait déjà.

```js
const x = 5;
const y = 5;
```

Voilà ce que tu fais en gros. Si `y` existe déjà pas besoin de le déclarer à nouveau pour lui assigner la même valeur.

```js
  getCard: async (id) => {
    //const targetId = Number(request.params.id);
    const queryResult = await client.query(
      `SELECT * 
      FROM card 
      WHERE card.id = $1;
      `);
     if (resultQuery.rowCount === 1) {
      return queryResult.rows[0] 
      } 
      return null;
  },

```

Tu as déclaré `id` en paramètre de ta fonction mais tu ne l'utilises pas. Tu as commenté la ligne ou tu déclare targetId en le récupérant de la request.

Le role du dataMapper est de faire appel à la base de données. Il ne doit pas faire de logique métier.
c'est le rôle du controller.

Lorsque un utilisateur veux accéder à une route. Le controller est appelé c'est lui qui a accès à la requête et à la réponse. Il va donc faire appel au dataMapper pour récupérer les données en base de données. Une fois que le dataMapper a récupéré les données, il les renvoie au controller qui va les traiter et les renvoyer à l'utilisateur. Le dataMapper n'as pas accès à la requête.

VScode te fais des suggestions, je pense que en tapant `request` tu as du cliquer sur la suggestion et ça a ajouté la ligne du require en haut de ton fichier.

Ici ta méthode `getCard` attend un paramètre `id` qui est censé correspondre à la carte démandée, jusqu'ici c'est bon.

Mais si tu regardes ta requête sql, tu fais `WHERE CARD.id = $1`. `$1` correspond au premier paramètre de la requête. Donc si tu fais `SELECT * FROM card WHERE card.id = $1` et que tu ne mets pas de paramètre, tu auras une erreur.

Une comparaison de ce que tu as écrit en langage humain serait : 

"Donne moi toutes les cartes qui ont un id égal à ça". Sans préciser à quoi correspond "ça".

```js
const queryResult = await client.query(
        `SELECT * FROM player WHERE player.id = $1;`, // $1 correspond au premier paramètre de la requête
        [4]); // Ici tu précises que $1 correspond à 4, donc tu auras tout les joueurs qui ont un id égal à 4, si tu mets id, alors tu auras toutes les cartes qui ont un id égal à id.
```

c'est intéressant parce que en voyant le cheminement complet. Je vois bien que tu as compris le principe. Mais tu as fait des erreurs de syntaxe qui t'ont fait perdre le fil. Prend le temps de faire ton cheminement. En te posant les questions au fur et à mesure.

Je suis dans mon router. Je veux pouvoir récupérer une carte en particulier. Comment ? En passant l'id de la carte dans l'url. Comment je fais ça ? En ajoutant un paramètre à ma route. Comment je fais ça ? En ajoutant `/:id` à ma route. Comment je récupère la valeur de l'id ? En regardant dans `req.params`.

D'accord maintenant je suis dans mon controller. Comment je récupère la valeur de l'id ? En regardant dans `req.params`.

Je fais quoi avec cette valeur ? Est-ce que je peux la passer directement au dataMapper ? Suis-je sûr que la valeur est bien un nombre ? Est-ce que je peux faire une requête sql avec cette valeur ?

Je passe cette valeur au dataMapper. Pourquoi faire ? Pour récupérer la carte en base de données.

Je fais quoi avec cette carte ? Je l'a renvoi au controller.

Le controller va décider d'appeler quelle vue et lui donner la carte pour qu'il l'affiche.

Et là tu vois que cette valeur a traversé toute ton application.

## Conclusion

Je vois que tu as l'idée global de ce qui est demandée. N'ai pas peur de prendre le temps de faire ton cheminement. De te poser les questions au fur et à mesure. De faire des recherches sur internet.

  Fait simple au début, ce n'est pas grave si tu ne fait pas de requête préparée au début. Le fait d'avoir un résultat va te motiver à continuer. Et tu pourras toujours revenir sur ton code pour l'améliorer. Vous allez continuer à faire des requêtes SQL encore et encore. Donc tu auras l'occasion de t'améliorer. Pareil pour les routes. Par contre c'est important de comprendre le cheminement, de savoir d'où vient la valeur que tu utilises. Et de savoir où elle va.

 revoit le cours sur les routes, les fonctions fléchées, les paramètres, les requêtes préparées. Et n'hésite pas à me poser des questions si tu as besoin.

 La correction va beacoup t'aider. Fait le chemin route -> controller -> dataMapper -> controller -> views. Et regarde ce que tu as fait et ce que tu aurais du faire. Et surtout pourquoi tu aurais du le faire.

 On lâche rien ! Tu vas y arriver ! C'est en forgeant qu'on devient forgeron !
 