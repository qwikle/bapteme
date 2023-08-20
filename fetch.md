# Fetch

La fonction `fetch` permet de récupérer les données d'une URL. Elle prend en paramètre l'URL et retourne un objet `Response` qui contient les données de la réponse.

C'est un peu comme avec un navigateur web, quand tu vas sur une page, ton navigateur envoie une requête au serveur et le serveur te renvoie la page. Avec `fetch`, c'est pareil, sauf que c'est ton code qui envoie la requête et qui reçoit la réponse.

Tu t'en doutes, comme avec ton navigateur, quand tu fait la demande, tu ne la récupères pas instantanément. Il faut attendre que le serveur réponde. C'est pour ça que `fetch` est une fonction asynchrone.

On va prendre par exemple notre api des cartes de monstre. On va récupérer la carte de monstre avec l'id 1.

```js
fetch('http://localhost:3000/card/1').then((response) => {
    // On récupère la réponse
    console.log(response);
})
```

`response` est un objet qui contient les données de la réponse. On peut récupérer le corps de la réponse.

Voir si la requête a réussi ou non.

```js
fetch('http://localhost:3000/card/1').then((response) => {
    // On vérifie si le serveur a bien répondu à notre requête
    if(response.ok){
          // On récupère le corps de la réponse en tant que texte vu que notre Deck builder renvoi du html
  response.text().then((text) => {
    console.log(text);
  });
    } else { // si par exemple la carte n'existe pas
       // on pourrai aussi lire le corps de la réponse avec response.text() pour voir le message d'erreur
        console.log('La requête a échoué');
    }
})
```

response.text() est une fonction asynchrone qui retourne une promesse. On peut donc utiliser `then` pour récupérer le texte de la réponse.

En ouvrant la console, tu devrais voir le code html de la carte de monstre.

Je te laisse consulter la documentation de `fetch` pour voir toutes les méthodes disponibles sur l'objet `response`.

[Documentation de fetch](https://developer.mozilla.org/fr/docs/Web/API/Fetch_API/Using_Fetch)
