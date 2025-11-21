Absolument ! Après avoir maîtrisé les briques de construction individuelles, il est temps de prendre de la hauteur et de
penser comme un architecte. Comment assembler toutes ces pièces pour construire un édifice solide, facile à entretenir
et capable de grandir ? C'est tout l'enjeu de ce module.

---

# Module 8 : Architecture et Bonnes Pratiques

Écrire du code qui fonctionne est une chose. Écrire du code que vous (ou vos collègues) pourrez comprendre, modifier et
étendre dans six mois en est une autre. Ce module ne porte pas sur de nouvelles fonctionnalités de React, mais sur la
sagesse accumulée par la communauté pour organiser des projets React de manière efficace et professionnelle.

Nous allons parler de structure, de principes de conception et de stratégies pour que votre base de code reste un
plaisir à travailler, même lorsqu'elle devient grande et complexe.

## Objectifs Pédagogiques

À la fin de ce module, vous serez capable de :

* **Organiser** les fichiers et dossiers de votre projet React selon des conventions établies.
* **Appliquer** le principe de responsabilité unique en séparant les composants de présentation des composants
  conteneurs (logique).
* **Maximiser** la réutilisabilité de votre code en créant des composants génériques et des hooks personnalisés.
* **Adopter** des conventions de nommage claires pour vos composants, props et variables.
* **Comparer** et choisir une stratégie de stylisation adaptée à vos besoins (CSS Modules, CSS-in-JS, Tailwind CSS).

## Pourquoi ce module est-il important ?

Imaginez que vous construisez une maison. Au début, vous pouvez laisser vos outils et matériaux un peu partout dans le
jardin. Ça fonctionne. Mais si vous construisez un immeuble de 20 étages, cette approche mène au chaos. Des outils sont
perdus, personne ne sait où trouver les plans, et le chantier devient dangereux et inefficace.

Un projet logiciel, c'est la même chose. Sans un plan clair (`architecture`), des règles de rangement (
`structure de dossiers`), et des outils bien identifiés (`conventions de nommage`), votre projet devient rapidement ce
qu'on appelle un "plat de spaghettis" : un enchevêtrement de code impossible à démêler. Ce module vous donne le plan et
les règles pour construire des "gratte-ciel" logiciels robustes et non des cabanes fragiles.

## Compétences du Référentiel (REAC)

Ce module est transversal et renforce la professionnalisation de toutes les compétences du **CCP-1 : Concevoir et
développer des composants d'interface utilisateur**.

* **Maquetter une application :** Une bonne architecture découle d'une bonne phase de conception.
* **Développer une interface utilisateur :** Les principes de ce module permettent de développer des UI maintenables.
* **Préparer et tester un déploiement :** Un code bien organisé est plus facile à tester, à déboguer et à déployer en
  toute confiance.

---

## 1. Organisation du Projet : Structure de Dossiers

La première chose qu'un développeur voit en arrivant sur un projet est sa structure de dossiers. Une structure claire et
prévisible est comme une carte : elle permet à n'importe qui de s'orienter rapidement.

<procedure title="L'analogie du supermarché">
<p>
Pensez à un supermarché bien organisé. Tous les fruits et légumes sont ensemble, les produits laitiers dans un autre rayon, la boulangerie ailleurs. Vous savez instinctivement où chercher. Une bonne structure de projet fait la même chose.
</p>
<p>
Il n'existe pas UNE seule structure parfaite, mais une approche très courante et efficace est l'<b>organisation par type de fichier</b>.
</p>
</procedure>

Voici une structure typique pour un projet de taille moyenne :


```
src/
├── api/             # Fonctions pour les appels API (ex: apiClient.js)
├── assets/          # Images, polices, etc.
├── components/      # Composants UI réutilisables (Button, Input, Card...)
│   ├── Button/
│   │   ├── Button.js
│   │   └── Button.module.css
│   └── ...
├── contexts/        # Définitions des contextes React (AuthContext, ThemeContext...)
├── features/        # (Pour Redux) Logique métier par fonctionnalité (user, cart...)
│   ├── user/
│   │   └── userSlice.js
│   └── ...
├── hooks/           # Hooks personnalisés (useFetch, useAuth...)
├── pages/           # Composants qui représentent une page (route) de l'app
│   ├── HomePage.js
│   └── ...
├── services/        # Logique métier non-React (calculs, formatage...)
├── styles/          # Fichiers CSS globaux (variables, reset...)
├── utils/           # Fonctions utilitaires pures (ex: formatDate.js)
├── App.js           # Composant racine
└── index.js         # Point d'entrée de l'application

```

**Principaux avantages :**

* **Prévisibilité :** Vous cherchez un hook ? C'est dans `/hooks`. Une page ? Dans `/pages`.
* **Scalabilité :** Facile d'ajouter de nouveaux éléments sans se poser de question.
* **Clarté :** Sépare clairement la logique (hooks, services) de la présentation (components, pages).

---

## 2. Découpage des Composants

La règle d'or du développement de composants est le **Principe de Responsabilité Unique (Single Responsibility
Principle)** : un composant ne devrait avoir qu'une seule raison de changer.

<procedure title="L'analogie de la trousse à outils">
<p>
Vous n'achetez pas un "marteau-tournevis-scie". Vous achetez un marteau, un tournevis, et une scie. Chaque outil excelle dans une seule tâche. Vos composants doivent être pareils. Un composant <code>Button</code> ne doit s'occuper que de l'apparence et du clic d'un bouton. Il ne doit pas savoir <i>pourquoi</i> on clique dessus.
</p>
</procedure>

Pour appliquer ce principe, on distingue souvent deux types de composants :

#### Composants de Présentation (ou "Dumb Components")

* **Leur rôle :** Comment les choses *apparaissent*.
* **Caractéristiques :**
    * Reçoivent leurs données uniquement via les `props`.
    * N'ont généralement pas de `state` interne (ou très peu, pour l'UI).
    * Ne connaissent pas l'origine des données ou la logique de l'application.
    * Sont très réutilisables.
* **Exemples :** `Button`, `Avatar`, `ProductCard`, `FormInput`.

#### Composants Conteneurs (ou "Smart Components")

* **Leur rôle :** Comment les choses *fonctionnent*.
* **Caractéristiques :**
    * Contiennent la logique métier.
    * Gèrent l'état (`useState`, `useReducer`, Redux).
    * Effectuent les appels API (`useEffect`).
    * Passent les données et les fonctions aux composants de présentation.
    * Ne contiennent que très peu de balisage direct (divs, structure).
* **Exemples :** `UserListPage`, `ProductPage`, `RegistrationFormContainer`.

**Exemple concret :**

<tabs>
<tab title="Composant de Présentation (`UserList.js`)">

```javascript
// Ce composant ne sait pas d'où viennent les utilisateurs.
// Il se contente de les afficher. Très réutilisable !
function UserList({ users, onUserClick }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id} onClick={() => onUserClick(user.id)}>
          {user.name}
        </li>
      ))}
    </ul>
  );
}

```
</tab>
<tab title="Composant Conteneur (`UserPage.js`)">

```javascript
import UserList from './UserList';
import useFetch from '../hooks/useFetch';

// Ce composant contient la logique : fetcher les données,
// gérer le chargement, les erreurs, et les actions.
function UserPage() {
const { data: users, loading, error } = useFetch('/api/users');

const handleUserClick = (userId) => {
console.log(`Utilisateur ${userId} cliqué !`);
};

if (loading) return <p>Chargement...</p>;
if (error) return <p>Erreur.</p>;

return (
<div>
<h1>Nos utilisateurs</h1>
<UserList users={users} onUserClick={handleUserClick} />
</div>
);
}

```
</tab>
</tabs>
> **Note :** Avec les hooks, la frontière est parfois plus floue, mais le principe de séparer la logique de la vue reste fondamental.

---

## 3. Réutilisabilité (DRY - Don't Repeat Yourself)

Le but ultime d'une bonne architecture est de ne jamais avoir à écrire la même logique deux fois.

<procedure title="L'analogie de la recette de cuisine">
<p>
Si vous faites souvent de la pâtisserie, vous n'allez pas réécrire la recette de la "pâte sablée" dans chaque recette de tarte. Vous allez la mettre sur une fiche à part et, dans vos recettes de tarte au citron ou de tarte aux fraises, vous écrirez simplement : "Préparer une pâte sablée (voir fiche P-01)".
</p>

<div>
En React :
<ul>
    <li>Une <b>fiche de recette pour un ingrédient visuel</b>, c'est un <b>composant réutilisable</b>.</li>
    <li>Une <b>fiche de recette pour une technique</b> (comme "monter les blancs en neige"), c'est un <b>hook personnalisé</b>.</li>
</ul>
</div>

</procedure>

**Comment identifier ce qui doit être réutilisable ?**

* **Pour les composants :** Vous vous surprenez à copier-coller du JSX avec de légères variations ? C'est le signe qu'il
  faut créer un composant générique qui prend ces variations en `props`.
* **Pour les hooks :** Vous vous surprenez à copier-coller de la logique (`useState` + `useEffect`) dans plusieurs
  composants ? Extrayez cette logique dans un hook personnalisé. Notre `useFetch` du module 5 en est l'exemple parfait.

---

## 4. Stratégies de Style

Comment appliquer du CSS à nos composants ? C'est un sujet avec de nombreuses approches en React. Voici les trois plus
populaires.

<tabs>
<tab title="CSS Modules">
<p>
<b>Principe :</b> Chaque fichier CSS est "scopé" localement au composant qui l'importe. Fini les conflits de noms de classes !
</p>

```javascript
// Button.js
import styles from './Button.module.css';

function Button() {
// `styles.button` sera transformé en un nom de classe unique
// comme `Button_button__a21eZ`
return <button className={styles.button}>Click Me</button>;
}
```

<p>
<b>Avantages :</b> CSS standard, pas de conflits, séparation claire du style et de la logique.
<br/>
<b>Inconvénients :</b> Peut être verbeux pour passer des styles dynamiques.
</p>
</tab>
<tab title="CSS-in-JS (Styled-Components)">
<p>
<b>Principe :</b> On écrit le CSS directement dans nos fichiers JavaScript en utilisant des "template literals". La bibliothèque crée des composants React avec des classes uniques.
</p>

```javascript
// npm install styled-components
import styled from 'styled-components';

// Crée un composant `<StyledButton>` qui est un `<button>` avec ces styles.
const StyledButton = styled.button`
  background-color: ${props => props.primary ? 'blue' : 'gray'};
  color: white;
  padding: 10px;
`;

// Utilisation
// <StyledButton primary>Primary</StyledButton>
// <StyledButton>Default</StyledButton>
```

<p>
<b>Avantages :</b> Colocation du style et du composant, stylisation dynamique très facile avec les props.
<br/>
<b>Inconvénients :</b> Peut avoir un léger coût en performance (runtime), courbe d'apprentissage.
</p>
</tab>
<tab title="Utility-First (Tailwind CSS)">
<p>
<b>Principe :</b> Au lieu d'écrire du CSS, on utilise une large palette de classes utilitaires directement dans le JSX.
</p>

```javascript
// On n'écrit presque plus de fichier CSS.
function Alert() {
  return (
    <div className="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded">
      Une erreur est survenue.
    </div>
  );
}
```

<p>
<b>Avantages :</b> Extrêmement rapide pour prototyper, design system cohérent, pas de changement de contexte entre JS et CSS.
<br/>
<b>Inconvénients :</b> Le JSX peut devenir très chargé, peut être difficile à prendre en main.
</p>
</tab>
</tabs>

**Quelle approche choisir ?**

* **CSS Modules :** Un choix solide, sûr et proche du CSS traditionnel. Idéal pour commencer.
* **Styled-Components :** Excellent si vous aimez la colocation et avez beaucoup de styles dynamiques.
* **Tailwind CSS :** Très populaire et productif une fois maîtrisé, surtout en équipe.

### Exercice 18 : Refactoriser un composant

**Objectif :** Appliquer le principe de séparation conteneur/présentation.

Reprenez le composant `RandomCatFact` de l'exercice 15.

1. Créez un composant de présentation `CatFactDisplay({ fact, loading, error })`. Ce composant ne contiendra que la
   logique d'affichage (le `if (loading)`, `if (error)` et le JSX final).
2. Le composant `RandomCatFact` devient un conteneur. Son seul rôle est d'appeler le hook `useFetch` et de passer les
   résultats en props à `CatFactDisplay`.

#### Correction exercice 18 {collapsible="true"}

<tabs>
<tab title="CatFactDisplay.js (Présentation)">

```javascript
// src/components/CatFactDisplay.js
import React from 'react';

// Ce composant est "bête". Il ne fait qu'afficher ce qu'on lui donne.
function CatFactDisplay({ fact, loading, error }) {
if (loading) {
return <p>Chargement d'un fait incroyable...</p>;
}

if (error) {
return <p>Impossible de contacter les chats pour le moment.</p>;
}

return (
<div>
<h2>Le saviez-vous ?</h2>
<p>{fact ? fact : 'Aucun fait trouvé.'}</p>
</div>
);
}

export default CatFactDisplay;


```
</tab>
<tab title="RandomCatFact.js (Conteneur)">

```javascript
// src/pages/RandomCatFactPage.js (On pourrait le renommer)
import React from 'react';
import useFetch from '../hooks/useFetch';
import CatFactDisplay from '../components/CatFactDisplay';

function RandomCatFact() {
const { data, loading, error } = useFetch('https://catfact.ninja/fact');

return (
<CatFactDisplay
fact={data ? data.fact : null}
loading={loading}
error={error}
/>
);
}

export default RandomCatFact;

```

</tab>
</tabs>

---

## TP : Refactoriser le blog

Reprenons le mini-blog du module sur le routage et appliquons nos nouveaux principes d'architecture.

### Étape 1 : Organiser les fichiers

* Créez une structure de dossiers `/pages`, `/components`, `/hooks`, `/layouts` (pour le `Layout.js`).
* Déplacez les fichiers existants (`BlogPage.js`, `PostPage.js`, `Layout.js`, `useFetch.js`, etc.) dans les dossiers
  appropriés. Mettez à jour les imports.

### Étape 2 : Créer des composants de présentation

* Dans `BlogPage.js`, la liste `<ul>` est de la présentation. Extrayez-la dans un composant `PostList({ posts })`.
  `BlogPage` devient un conteneur qui gère le fetch et passe `posts` au `PostList`.
* Dans `PostPage.js`, l'affichage de l'article (`<article>`) est de la présentation. Extrayez-le dans un composant
  `PostDetail({ post })`. `PostPage` devient le conteneur.

### Étape 3 : Créer un composant UI générique

* Créez un composant `Spinner.js` dans le dossier `/components`. Il affichera une simple animation ou un message de
  chargement.
* Créez un composant `ErrorMessage.js` qui prend une prop `message` et l'affiche dans un style d'erreur.
* Utilisez ces deux composants dans vos conteneurs `BlogPage` et `PostPage` pour gérer les états de `loading` et
  `error`, au lieu de simples balises `<p>`.

### Étape 4 (Bonus) : Adopter une stratégie de style

* Choisissez une des trois stratégies (CSS Modules est un bon choix pour commencer) et appliquez-la à quelques
  composants (par exemple, le `Layout` ou les `PostList` et `PostDetail`) pour styliser un peu l'application.

### Correction du TP {collapsible="true"}

*(Cette correction montre la structure des fichiers et le code refactorisé, en supposant que les fichiers ont été
déplacés comme demandé à l'étape 1.)*

<tabs>
<tab title="components/PostList.js (Présentation)">

```javascript
import React from 'react';
import { Link } from 'react-router-dom';

function PostList({ posts }) {
return (
<ul>
{posts.map(post => (
<li key={post.id}>
<Link to={`/blog/${post.id}`}>{post.title}</Link>
</li>
))}
</ul>
);
}

export default PostList;


```
</tab>
<tab title="pages/BlogPage.js (Conteneur)">

```javascript
import React from 'react';
import useFetch from '../hooks/useFetch';
import PostList from '../components/PostList';
import Spinner from '../components/Spinner';
import ErrorMessage from '../components/ErrorMessage';

function BlogPage() {
const { data: posts, loading, error } = useFetch(
'https://jsonplaceholder.typicode.com/posts'
);

if (loading) return <Spinner />;
if (error) return <ErrorMessage message="Erreur de chargement des articles." />;

return (
<div>
<h2>Tous les articles</h2>
{posts && <PostList posts={posts} />}
</div>
);
}

export default BlogPage;

```

</tab>
<tab title="components/PostDetail.js (Présentation)">

```javascript
import React from 'react';
import { Link } from 'react-router-dom';

function PostDetail({ post }) {
if (!post) return null;

return (
<article>
<h2>{post.title}</h2>
<p>{post.body}</p>
<Link to="/blog">Retour au blog</Link>
</article>
);
}

export default PostDetail;


```
</tab>
<tab title="pages/PostPage.js (Conteneur)">

```javascript
import React from 'react';
import { useParams } from 'react-router-dom';
import useFetch from '../hooks/useFetch';
import PostDetail from '../components/PostDetail';
import Spinner from '../components/Spinner';
import ErrorMessage from '../components/ErrorMessage';

function PostPage() {
const { postId } = useParams();
const { data: post, loading, error } = useFetch(
`https://jsonplaceholder.typicode.com/posts/${postId}`
);

if (loading) return <Spinner />;
if (error) return <ErrorMessage message="Erreur de chargement de l'article." />;

return <PostDetail post={post} />;
}

export default PostPage;

```

</tab>
</tabs>

---

## Auto-évaluation

Testez vos connaissances ! Les réponses se trouvent à la toute fin du support de cours.

#### Questions à Choix Multiple (QCM)

**1. Quel est l'objectif principal de la distinction entre composants Conteneur et Présentation ?**

1. Améliorer la performance de l'application.
2. Séparer la logique métier de l'interface utilisateur pour améliorer la réutilisabilité et la maintenabilité.
3. Permettre d'utiliser plus de hooks dans un seul composant.
4. Rendre le CSS plus facile à écrire.

**2. Vous écrivez un code qui gère la connexion/déconnexion d'un utilisateur, incluant l'état de connexion et la
communication avec une API d'authentification. Où cette logique devrait-elle idéalement résider ?**

1. Dans un composant réutilisable `<Button>`.
2. Dans un hook personnalisé `useAuth`.
3. Directement dans le composant de page `LoginPage`.
4. Dans un fichier CSS.

**3. Quel est le principal avantage des CSS Modules ?**

1. Ils permettent d'écrire du CSS en utilisant la syntaxe de JavaScript.
2. Ils évitent les conflits de noms de classes CSS en rendant les noms de classe uniques et locaux au composant.
3. Ils sont plus rapides à charger que les fichiers CSS classiques.
4. Ils n'ont pas besoin d'être importés dans les fichiers JavaScript.

#### Questions Ouvertes

**4. Décrivez le Principe de Responsabilité Unique (SRP) et donnez un exemple de comment vous l'appliqueriez en divisant
un composant React monolithique.**

**5. Votre application a besoin d'afficher des dates dans un format spécifique (`jj/mm/aaaa`) à de nombreux endroits.
Quelle est la meilleure façon d'architecturer cette logique pour éviter la répétition de code ?**

---

## Conclusion de ce module

Vous avez atteint un nouveau palier dans votre maîtrise de React. Vous ne pensez plus seulement en termes de composants
individuels, mais en termes de **systèmes de composants**. Vous avez appris à structurer vos projets de manière logique,
à séparer les préoccupations, à maximiser la réutilisabilité et à choisir une stratégie de style qui convient à votre
projet.

Ces compétences sont celles qui distinguent un développeur junior d'un développeur confirmé. Elles sont la clé pour
construire des applications qui non seulement fonctionnent aujourd'hui, mais qui seront aussi un plaisir à maintenir et
à faire évoluer demain.

Maintenant que nous savons construire des applications propres et bien architecturées, comment pouvons-nous être sûrs
qu'elles fonctionnent correctement et qu'une modification future ne cassera pas tout ? La réponse se trouve dans notre
prochain module : **Tester son Application**.

## Suggestions de projets pour pratiquer

1. **Niveau Débutant : Refactoriser un projet existant**
    * **Description :** Prenez l'un des projets que vous avez déjà réalisés (comme la TodoList ou le convertisseur) et
      réorganisez-le en suivant la structure de dossiers et les principes vus dans ce module.
    * **Piste technique :** Séparez les conteneurs des composants de présentation, extrayez la logique répétitive dans
      des hooks, et créez des composants UI génériques (`<Button>`, `<Input>`).

2. **Niveau Intermédiaire : Créer un kit de composants UI**
    * **Description :** Créez un nouveau projet React dont le seul but est de développer une petite bibliothèque de
      composants de présentation génériques : un `Button` (avec des props `variant`, `size`), un `Card`, un `Modal`, un
      `Input`.
    * **Piste technique :** Utilisez Storybook ou une simple page de démonstration pour afficher tous vos composants.
      Appliquez une stratégie de style cohérente (CSS Modules, Styled-Components...) à l'ensemble du kit.

3. **Niveau Avancé : Concevoir l'architecture d'une nouvelle application**
    * **Description :** Avant de coder, prenez le temps de concevoir l'architecture d'une application (par exemple, un
      clone de Twitter simple).
    * **Piste technique :** Sur papier ou avec un outil de diagramme, définissez la structure des dossiers, listez les
      composants (en distinguant présentation/conteneur), les hooks personnalisés dont vous aurez besoin, les pages (
      routes), et la forme de votre état global. C'est un excellent exercice pour penser comme un architecte logiciel.