# Compte-rendu du TP Quiz en images

Nom : Saa Emile Basile Tolno
Classe : L2DSI1 G2
Thème : les formes géométriques

## 1. L'interface

Mon application tient dans une seule page, le fichier `App.vue`. Je n'ai pas utilisé Vue Router. Le quiz a 5 questions avec une image chacune.

Éléments HTML que j'ai utilisés :
- `h1` pour le titre et un `p` pour le score, en haut à droite
- `progress` pour la barre de progression
- `h2` pour "Question 1 sur 5"
- `img` pour l'image de la question
- `button` pour les 3 choix, pour "Question suivante" et pour "Rejouer"
- `p` pour le texte de la question et le message de correction

Directives Vue que j'ai utilisées :
- `{{ }}` pour afficher le score, la question et les choix
- `:src` et `:alt` pour l'image et son texte alternatif
- `v-for` avec `:key` pour créer les 3 boutons à partir du tableau `choix`
- `@click` pour lancer les fonctions `repondre`, `avancer` et `rejouer`
- `:disabled` pour bloquer les boutons après une réponse
- `:class` pour mettre la bonne réponse en vert et la mauvaise en rouge
- `:value` et `:max` pour la barre `progress`
- `v-if` et `v-else` pour choisir entre le quiz et l'écran final

## 2. Le tableau des questions

Les 5 questions sont dans un tableau `questions`, dans la partie `<script setup>` de `App.vue`. Chaque question est un objet avec 5 champs :
- `texte` : la question
- `image` : le chemin de l'image, par exemple `/images/triangle.svg`
- `choix` : un tableau de 3 réponses
- `bonneReponse` : la position de la bonne réponse dans `choix` (0, 1 ou 2)
- `descriptionImage` : le texte pour l'attribut `alt`

Exemple :

```js
{
  texte: 'Combien de côtés a cette forme ?',
  image: '/images/triangle.svg',
  choix: ['4 côtés', '3 côtés', '5 côtés'],
  bonneReponse: 1,
  descriptionImage: 'Un triangle rouge'
}
```

Les images sont dans le dossier `public/images/`. Dans le code, j'écris `/images/triangle.svg` et pas `public/images/triangle.svg`.

- Question 1 : triangle.svg
- Question 2 : carre.svg
- Question 3 : cercle.svg
- Question 4 : etoile.svg
- Question 5 : rectangle.svg

Pour que la bonne réponse ne soit pas toujours au même endroit, je l'ai mise aux positions 1, 0, 2, 1 et 0.

## 3. Les événements, le score et la progression

J'ai 4 variables réactives :
- `indexQuestion` : le numéro de la question en cours (0 à 4)
- `score` : le nombre de bonnes réponses
- `reponseChoisie` : le bouton cliqué, ou `null` si on n'a pas encore répondu
- `termine` : passe à `true` à la fin du quiz

Quand on clique sur un choix, la fonction `repondre(i)` est appelée. Elle garde le choix dans `reponseChoisie`. Si le choix est la bonne réponse, elle ajoute 1 au score.

Le bouton "Question suivante" appelle `avancer()`. Cette fonction passe à la question d'après et remet `reponseChoisie` à `null`. Sur la dernière question, le bouton s'appelle "Voir le résultat" et `avancer()` met `termine` à `true`.

Le bouton "Rejouer" appelle `rejouer()`, qui remet tout à zéro.

Pour la progression, j'ai fait un `computed` qui compte les réponses données. Si la question en cours a déjà une réponse, il renvoie `indexQuestion + 1`. Sinon il renvoie `indexQuestion`.

## 4. Les tests

Je les ai faits dans le navigateur, sur `http://localhost:5173`.

Test 1, nouvelle partie
- Attendu : la question 1 avec 3 choix, un score de 0/5 et une progression de 0/5.
- Obtenu : la première question se presente avec le score à 0/5 et la progression à 0/5

Test 2, bonne réponse
- Attendu : le score augmente de 1 et la progression augmente de 1.
- Obtenu : si la reponse est correcte le score augmente d'un point sur 5.

Test 3, mauvaise réponse
- Attendu : le score reste pareil et la progression augmente de 1.
- Obtenu : le score n'a pas bougé mais la progression augmente

Test 4, plusieurs clics sur un choix
- Attendu : pas de point en plus et les 3 boutons sont désactivés.
- Obtenu : au premier click tout les boutons sont desactivés et la reponse est prise en compte

Test 5, question suivante
- Attendu : nouvelle image, nouveau texte, nouveaux choix, et le score est gardé.
- Obtenu : lorsqu'on clique sur question suivante la forme change, la question change et les choix de reponses changent aussi

Test 6, 5 bonnes réponses
- Attendu : score de 5/5 et progression de 5/5.
- Obtenu : le qcm se termine avec un score de 5/5

Test 7, 5 mauvaises réponses
- Attendu : score de 0/5 et progression de 5/5.
- Obtenu : le qcm se termine avec un score de 0/5

Test 8, réponses mélangées
- Attendu : le score est égal au nombre exact de bonnes réponses.
- Obtenu : le score varie en fonction des reponses correctes et celle incorrectes

Test 9, bouton Rejouer
- Attendu : retour à la question 1, avec le score et la progression à 0.
- Obtenu : le qcm est repris et le score remis à 0 ainsi que la progression

Test 10, petit écran
- Attendu : tous les choix restent cliquables et les textes sont lisibles.
- Obtenu : peut importe la taille de l'ecran les textes sont lisibles et les choix cliquables

### Difficultés rencontrées

le score ne changeait pas. Dans la fonction `repondre`, j'avais écrit `score++` au lieu de `score.value++`. le score restait à 0 dans la page. J'ai compris qu'avec `ref`, dans la partie script, il faut toujours écrire `.value`.

## 5. Réponses aux questions

Comment je représente une question et sa bonne réponse ?
Chaque question est un objet. Il a un tableau `choix` avec les 3 réponses, et un champ `bonneReponse` qui donne la position de la bonne réponse dans ce tableau.

Comment je sais quel bouton a été cliqué ?
Les boutons sont faits avec `v-for`, qui me donne la position `i` de chaque choix. Chaque bouton appelle `repondre(i)`. La fonction reçoit donc la position du bouton cliqué et la compare à `bonneReponse`.

Comment j'évite de donner plusieurs points pour une question ?
Je fais deux choses. Les boutons ont `:disabled`, donc on ne peut plus cliquer après la première réponse. Et la fonction `repondre` commence par `if (reponseChoisie.value !== null) return`, donc elle s'arrête si on a déjà répondu.

Quelle est la différence entre le score et la progression ?
Le score compte seulement les bonnes réponses. La progression compte toutes les réponses, bonnes ou fausses. Une mauvaise réponse fait avancer la barre mais pas le score.

Comment je détecte la dernière question ?
Je compare `indexQuestion` avec `questions.length - 1`. Si c'est égal, c'est la dernière question. Le bouton affiche alors "Voir le résultat" et `termine` passe à `true`. Grâce à `v-if` et `v-else`, la page affiche l'écran final au lieu du quiz.

## 6. Captures d'écran

Elles sont dans le dossier `captures/` :
- `Capture1.png`
- `Capture2.png`
- `Capture3.png`
- `Capture4.png`