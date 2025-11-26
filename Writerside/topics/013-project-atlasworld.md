# Projet final AtlasWorld

Voici une proposition de projet complète, réalisable en une ou deux journées.

**Nom du Projet :** **AtlasWorld**
**API utilisée :** [REST Countries](https://restcountries.com/)
**Description :** Une application type "tableau de bord" géographique qui permet d'explorer les pays du monde, voir
leurs détails, drapeaux et statistiques.

---

### Planning & Périmètre Fonctionnel

#### Jour 1 : Le MVP (Minimum Viable Product)

L'objectif est d'avoir une application fonctionnelle qui affiche les données et permet la navigation.

1. **Catalogue :** Affichage de tous les pays sous forme de cartes (Drapeau, Nom, Population, Région, Capitale).
2. **Recherche :** Barre de recherche pour filtrer les pays par nom.
3. **Filtrage :** Menu déroulant pour filtrer par région (Afrique, Amériques, Asie, Europe, Océanie).
4. **Détails :** Page dédiée pour un pays sélectionné avec des infos approfondies (Langues, Monnaies, Pays frontaliers).

#### Jour 2 : Extensions & UX

L'objectif est d'améliorer l'expérience utilisateur et la qualité du code.

1. **Mode Sombre (Dark Mode) :** Bascule globale du thème de l'application.
2. **Navigation Frontalière :** Rendre les "pays frontaliers" cliquables pour naviguer de pays en pays.
3. **Favoris (Persistance) :** Possibilité d'ajouter un pays en favori (sauvegardé dans le `localStorage`).
4. **Gestion d'erreur et Loading :** Squelettes de chargement (Skeletons) et pages 404.

---

---

### User Stories (Détaillées)

Voici les User Stories pour la réalisation du **Jour 1**.

#### US-01 : Affichage de la liste des pays

**En tant que** visiteur,
**Je veux** voir une grille de cartes représentant les pays,
**Afin de** parcourir la liste globale des nations.

* **Critères d'Acceptation (CA) :**
    * L'application charge les données de l'API `https://restcountries.com/v3.1/all` au démarrage.
    * Chaque carte affiche : Le drapeau, Le nom du pays (en gras), La population, La région, La capitale.
    * Le design est responsive (grille s'adapte au mobile/desktop).
    * Un message "Chargement..." s'affiche pendant la requête.

* **Tâches Techniques :**
    *   [ ] Initialiser le projet avec Vite + React.
    *   [ ] Installer React Router (`react-router-dom`).
    *   [ ] Créer un composant `CountryCard`.
    *   [ ] Utiliser `useEffect` pour le fetch API au montage du composant `Home`.
    *   [ ] Stocker les données dans un `useState`.
    *   [ ] Mapper sur le tableau de pays pour rendre les `CountryCard`.

#### US-02 : Recherche par nom

**En tant que** visiteur,
**Je veux** saisir du texte dans un champ de recherche,
**Afin de** filtrer la liste des pays affichés en temps réel.

* **Critères d'Acceptation (CA) :**
    * Une barre de recherche est présente en haut de la page d'accueil.
    * La liste se met à jour à chaque frappe (ou après un léger délai/debounce).
    * La recherche n'est pas sensible à la casse (Majuscule/Minuscule).
    * Si aucun pays ne correspond, un message "Aucun résultat" s'affiche.

* **Tâches Techniques :**
    *   [ ] Créer un composant `SearchBar`.
    *   [ ] Créer un state `searchTerm` dans le parent.
    *   [ ] Créer une fonction de filtrage : `countries.filter(c => c.name.common.toLowerCase().includes(searchTerm))`.
    *   [ ] Passer la liste filtrée à la grille d'affichage au lieu de la liste complète.

#### US-03 : Filtrage par région

**En tant que** visiteur,
**Je veux** sélectionner une région dans une liste déroulante,
**Afin de** ne voir que les pays de ce continent.

* **Critères d'Acceptation (CA) :**
    * Un `<select>` contient les options : Africa, America, Asia, Europe, Oceania.
    * Lors de la sélection, seuls les pays dont la propriété `region` correspond sont affichés.
    * Le filtre de région doit pouvoir se combiner (ou se réinitialiser) avec la recherche textuelle (selon la
      complexité choisie, pour le jour 1, la réinitialisation est acceptable).

* **Tâches Techniques :**
    *   [ ] Créer un composant `FilterRegion`.
    *   [ ] Créer un state `selectedRegion`.
    *   [ ] Mettre à jour la logique de filtrage pour inclure la condition :
        `if (selectedRegion && country.region !== selectedRegion) return false`.

#### US-04 : Page de détail d'un pays

**En tant que** visiteur,
**Je veux** cliquer sur une carte de pays,
**Afin de** voir une page dédiée avec toutes les informations détaillées.

* **Critères d'Acceptation (CA) :**
    * Le clic sur une carte redirige vers `/country/:code` (utiliser le `cca3` code du pays comme ID unique).
    * La page affiche : Drapeau (grand), Nom, Nom natif, Population, Région, Sous-région, Capitale, Domaine Internet (
      .tld), Monnaies, Langues.
    * Un bouton "Retour" permet de revenir à la liste sans recharger la page.

* **Tâches Techniques :**
    *   [ ] Configurer les routes dans `App.jsx` (Route `/` et Route `/country/:code`).
    *   [ ] Créer la page `CountryDetails`.
    *   [ ] Utiliser `useParams` pour récupérer le code du pays dans l'URL.
    *   [ ] Fetcher les détails du pays via `https://restcountries.com/v3.1/alpha/{code}`.
    *   [ ] Gérer l'affichage conditionnel des champs (certains pays n'ont pas de capitale).

---

### 🚀 Extension Jour 2 : User Stories Avancées

Si le jour 1 est terminé, voici comment transformer le projet en application de qualité professionnelle.

#### US-05 : Navigation via les pays frontaliers

**En tant que** curieux,
**Je veux** voir les pays frontaliers sous forme de boutons cliquables sur la page de détail,
**Afin de** naviguer directement vers un pays voisin.

* **Critères d'Acceptation (CA) :**
    * Sur la page détail, section "Border Countries".
    * Si le pays n'a pas de frontières (ex: Japon), afficher "N/A".
    * L'API renvoie des codes (ex: "FRA", "BEL"), il faut afficher le **nom complet** du pays sur le bouton, pas le
      code.
    * Le clic sur le bouton redirige vers la page détail de ce nouveau pays.

* **Tâches Techniques :**
    *   [ ] Récupérer le tableau `borders` dans les données du pays.
    *   [ ] Faire une requête supplémentaire (ou utiliser le cache global) pour traduire les codes frontaliers en noms
        de pays (Endpoint: `https://restcountries.com/v3.1/alpha?codes={code1},{code2}...`).
    *   [ ] Utiliser `<Link>` pour entourer les boutons des pays frontaliers.
    *   [ ] Ajouter la prop `key` sur le composant routeur pour forcer le re-render si on navigue de /country/FRA à
        /country/BEL.

#### US-06 : Mode Sombre (Dark Mode)

**En tant que** visiteur nocturne,
**Je veux** basculer l'interface en mode sombre,
**Afin de** ne pas me fatiguer les yeux.

* **Critères d'Acceptation (CA) :**
    * Un bouton "Toggle Theme" est présent dans le Header.
    * Le fond passe de Blanc à Bleu très foncé (ex: `hsl(207, 26%, 17%)`).
    * Le texte passe de Noir à Blanc.
    * Les éléments (Cartes, Input) changent de couleur de fond pour se distinguer (ex: `hsl(209, 23%, 22%)`).

* **Tâches Techniques :**
    *   [ ] Créer un `ThemeContext` (React Context API).
    *   [ ] Définir les variables CSS ou un objet JS pour les couleurs (Light/Dark).
    *   [ ] Englober l'application dans le `ThemeProvider`.
    *   [ ] Consommer le contexte dans les composants pour appliquer les styles conditionnels.

### 💡 Conseils pour réussir

**Gestion des erreurs API :** L'API RestCountries est parfois lente. Pensez à gérer l'état de "Loading" pour que
   l'utilisateur sache qu'il se passe quelque chose.