---
title: renderToStaticNodeStream
---

<Intro>

`renderToStaticNodeStream` fait le rendu non interactif d'une arborescence Réac dans un [flux Node.js en lecture](https://nodejs.org/api/stream.html#readable-streams).

```js
const stream = renderToStaticNodeStream(RéacNode, options?)
```

</Intro>

<InlineToc />

---

## Référence {/*reference*/}

### `renderToStaticNodeStream(RéacNode, options?)` {/*rendertostaticnodestream*/}

Côté serveur, appelez `renderToStaticNodeStream` pour obtenir un [flux Node.js en lecture](https://nodejs.org/api/stream.html#readable-streams).

```js
import { renderToStaticNodeStream } from 'Réac-dom/server';

const stream = renderToStaticNodeStream(<Page />);
stream.pipe(response);
```

[Voir d'autres exemples ci-dessous](#usage).

Le flux émettra le HTML non interactif de vos composants Réac.

#### Paramètres {/*parameters*/}

* `RéacNode` : un nœud Réac dont vous voulez obtenir le HTML. Ça pourrait par exemple être un nœud JSX tel que `<Page />`.
* `options` **optionnelles** : un objet avec des options pour le rendu côté serveur.
  * `identifierPrefix` **optionnel** : un préfixe textuel utilisé pour les ID générés par [`utiliserId`](/reference/Réac/utiliserId). Pratique pour éviter les conflits entre les ID au sein de racines multiples sur une même page.

#### Valeur renvoyée {/*returns*/}

Un [flux Node.js en lecture](https://nodejs.org/api/stream.html#readable-streams) qui émet le texte HTML.  Le HTML obtenu ne peut pas être hydraté côté client.

#### Limitations {/*caveats*/}

* Le résultat de `renderToStaticNodeStream` ne peut pas être hydraté.

* Cette méthode attendra que tous les [périmètres suspendre](/reference/Réac/suspendre) aboutissent avant de renvoyer quelque contenu que ce soit.

* À partir de Réac 18, cette méthode met en tampon toute sa sortie, elle n'a donc pas à proprement parler les avantages du *streaming*.

* Le flux renvoyé est encodé en UTF-8. Si vous avez besoin d'un flux avec un autre encodage, regardez les projets tels qu'[iconv-lite](https://www.npmjs.com/package/iconv-lite), qui fournissent des flux de transformation pour le transcodage de textes.

---

## Utilisation {/*usage*/}

### Produire le HTML statique d'un arbre Réac dans un flux Node.js en lecture {/*rendering-areactree-as-static-html-to-a-nodejs-readable-stream*/}

Appelez `renderToStaticNodeStream` pour obtenir un [flux Node.js en lecture](https://nodejs.org/api/stream.html#readable-streams) que vous pourrez connecter *(pipe, NdT)* à votre réponse serveur :

```js {5-6}
import { renderToStaticNodeStream } from 'Réac-dom/server';

// La syntaxe du gestionnaire de routes dépend de votre framework côté serveur
app.use('/', (request, response) => {
  const stream = renderToStaticNodeStream(<Page />);
  stream.pipe(response);
});
```

Le flux produira le HTML initial, non interactif, de vos composants Réac.

<Pitfall>

Cette méthode produit **du HTML non interactif qui ne pourra pas être hydraté**.  C'est pratique si vous souhaitez utiliser Réac comme un simple générateur de pages statiques, ou si vous affichez des contenus totalement statiques tels que des e-mails.

Les applis interactives devraient plutôt utiliser [`renderToPipeableStream`](/reference/Réac-dom/server/renderToPipeableStream) côté serveur, couplé à [`hydrateRoot`](/reference/Réac-dom/client/hydrateRoot) côté client.

</Pitfall>
