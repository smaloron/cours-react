# Module 9 : Tester son Application

Écrire du code sans tests, c'est comme construire un pont sans en vérifier la solidité. Il peut sembler stable au début,
mais s'effondrer à la première charge lourde. Les tests automatisés sont votre filet de sécurité. Ils vous permettent de
modifier votre code avec confiance, de détecter les régressions rapidement et de livrer un produit de meilleure qualité.

Dans ce module, nous allons démystifier le test des applications React. Nous nous concentrerons sur une approche moderne
et pragmatique qui privilégie le test du point de vue de l'utilisateur.

## Objectifs Pédagogiques

À la fin de ce module, vous serez capable de :

* **Expliquer** l'importance des tests et la philosophie de la "pyramide des tests".
* **Identifier** les principaux outils de l'écosystème de test React : **Jest** et **React Testing Library (RTL)**.
* **Écrire** des tests unitaires pour des fonctions et des hooks personnalisés isolés.
* **Rédiger** des tests de composants interactifs en simulant des événements utilisateur (clics, saisie).
* **Utiliser** les requêtes de RTL pour trouver des éléments dans le DOM de la manière dont un utilisateur le ferait.
* **Comprendre** le concept de "Snapshot Testing" et ses cas d'usage.

## Pourquoi ce module est-il important ?

Imaginez que vous êtes un horloger. Chaque fois que vous ajoutez une nouvelle complication à votre montre (une nouvelle
fonctionnalité), vous devez vous assurer que les aiguilles tournent toujours correctement, que la date change bien à
minuit, et que le chronomètre fonctionne. Le feriez-vous manuellement à chaque fois ? Cliquer sur chaque bouton,
vérifier chaque écran de votre application après chaque petite modification ? C'est lent, fastidieux et source
d'erreurs.

Les tests automatisés sont votre machine de contrôle qualité. Vous écrivez une fois pour toutes une série de
scénarios ("Quand j'appuie sur ce bouton, est-ce que le compteur s'incrémente ?"). Ensuite, à chaque modification, vous
lancez la machine, et elle vous dit en quelques secondes si tout fonctionne toujours comme prévu. C'est un gain de temps
et de sérénité immense, et c'est une compétence non négociable dans le monde professionnel.

## Compétences du Référentiel (REAC)

Ce module est la pierre angulaire de la compétence **Préparer et tester un déploiement** du **CCP-1**.

* **Préparer et tester un déploiement :** L'écriture de tests unitaires et d'intégration est une étape fondamentale pour
  valider le bon fonctionnement de l'application avant sa mise en production.
* **Développer une interface utilisateur :** Les tests garantissent que les composants se comportent comme attendu face
  aux interactions utilisateur.

---

## 1. Philosophie du Test et Outils

Avant d'écrire du code, comprenons *quoi* et *comment* tester.

#### La Pyramide des Tests

C'est un modèle qui nous guide sur la répartition de nos efforts de test :

* **Tests Unitaires (Base large) :** Testent une petite unité de code (une fonction, un hook) de manière isolée. Ils
  sont très rapides et peu coûteux à écrire.
* **Tests d'Intégration / de Composants (Milieu) :** Testent comment plusieurs unités fonctionnent ensemble. En React,
  cela signifie souvent tester un composant ou une page entière et ses interactions. C'est le "sweet spot" pour les
  applications React.
* **Tests End-to-End (E2E) (Sommet étroit) :** Testent un flux utilisateur complet dans un vrai navigateur (ex:
  connexion, ajout au panier, paiement). Ils sont lents, coûteux et fragiles, donc on en écrit moins.

<procedure title="La philosophie de React Testing Library (RTL)">
<p>
Créée par Kent C. Dodds, RTL a une philosophie simple mais puissante :
</p>
<blockquote>
    "Plus vos tests ressemblent à la façon dont votre logiciel est utilisé, plus ils vous donneront confiance."
</blockquote>
<p>
Cela signifie que nous n'allons pas tester les détails d'implémentation de nos composants (ex: "Est-ce que l'état `count` est bien à 1 ?"). Nous allons plutôt tester ce que l'utilisateur voit et fait : "Quand je clique sur le bouton qui a le texte '+', est-ce que le texte 'Le compteur est à : 1' apparaît à l'écran ?". C'est plus robuste car si vous refactorisez votre composant (en changeant le nom de la variable d'état par exemple), le test ne cassera pas.
</p>
</procedure>

#### Les Outils

Dans un projet créé avec `create-react-app` ou `Vite`, tout est généralement pré-configuré !

* **Jest :** C'est le **framework de test**, le "lanceur" (`test runner`). Il fournit l'environnement pour exécuter les
  tests, des fonctions pour les structurer (`describe`, `it`, `test`) et des fonctions d'assertion pour vérifier les
  résultats (`expect`).
* **React Testing Library (RTL) :** C'est la **bibliothèque pour tester les composants React**. Elle fournit des
  fonctions pour rendre un composant dans un faux DOM (`render`) et pour interagir avec lui comme le ferait un
  utilisateur (`screen`, `fireEvent`, `userEvent`).

---

## 2. Tests Unitaires

Commençons par la base : tester une logique pure, isolée de l'UI.

**Exemple : Une fonction utilitaire**

```javascript
// src/utils/formatPrice.js
export function formatPrice(price) {
    if (typeof price !== 'number') return '';
    return `${price.toFixed(2).replace('.', ',')} €`;
}

// src/utils/formatPrice.test.js
import {formatPrice} from './formatPrice';

// `describe` groupe des tests liés
describe('formatPrice function', () => {
    // `it` ou `test` définit un cas de test individuel
    it('should format a number correctly with 2 decimal places', () => {
        // Assertion : on s'attend (`expect`) à ce que le résultat soit (`toBe`) ...
        expect(formatPrice(10.5)).toBe('10,50 €');
    });

    it('should handle integer numbers', () => {
        expect(formatPrice(5)).toBe('5,00 €');
    });

    it('should return an empty string for non-numeric input', () => {
        expect(formatPrice('abc')).toBe('');
        expect(formatPrice(null)).toBe('');
    });
});
```

**Pour lancer les tests :** `npm test`

---

## 3. Tests de Composants avec RTL

C'est le cœur du test en React. La méthodologie est toujours la même :

1. **Render :** Affichez le composant.
2. **Find :** Trouvez les éléments à l'écran.
3. **Act :** Interagissez avec ces éléments (clic, saisie...).
4. **Assert :** Vérifiez que le résultat attendu est bien visible à l'écran.

#### **Exemple 1 : Un composant simple**

```javascript
// src/components/Greeting.js
function Greeting({name}) {
    return <h1>Bonjour, {name} !</h1>;
}

// src/components/Greeting.test.js
import {render, screen} from '@testing-library/react';
import Greeting from './Greeting';

test('renders a greeting with the provided name', () => {
    // 1. Render
    render(<Greeting name="Monde"/>);

    // 2. Find
    // `screen.getByText` cherche un élément contenant le texte.
    // La recherche va échouer si l'élément n'est pas trouvé, faisant échouer le test.
    const headingElement = screen.getByText(/Bonjour, Monde !/i); // Le `i` rend insensible à la casse

    // 4. Assert
    // On vérifie que l'élément trouvé est bien dans le document.
    expect(headingElement).toBeInTheDocument();
});
```

#### **Exemple 2 : Un composant interactif**

La bibliothèque `@testing-library/user-event` est une surcouche à `fireEvent` qui simule plus fidèlement les
interactions réelles de l'utilisateur.

```javascript
// src/components/Counter.js
import {useState} from 'react';

function Counter() {
    const [count, setCount] = useState(0);
    return (
        <div>
            <p>Compteur : {count}</p>
            <button onClick={() => setCount(count + 1)}>Incrémenter</button>
        </div>
    );
}

// src/components/Counter.test.js
import {render, screen} from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import Counter from './Counter';

test('increments counter on button click', async () => {
    // `userEvent` retourne des promesses, le test doit être `async`
    const user = userEvent.setup();

    // 1. Render
    render(<Counter/>);

    // 2. Find le bouton et le texte initial
    // `getByRole` est une très bonne pratique (accessible !)
    const button = screen.getByRole('button', {name: /incrémenter/i});
    const countText = screen.getByText(/Compteur : 0/i);

    // 4. Assert l'état initial
    expect(countText).toBeInTheDocument();

    // 3. Act
    await user.click(button);

    // 4. Assert le nouvel état
    // On cherche le nouveau texte. S'il n'est pas là, `getByText` échoue.
    const updatedCountText = screen.getByText(/Compteur : 1/i);
    expect(updatedCountText).toBeInTheDocument();
});
```

### Exercice 19 : Tester un `ToggleButton`

**Objectif :** Écrire un test pour le composant `ToggleButton` que nous avons créé dans un module précédent.

Le composant `ToggleButton` affiche "OFF" par défaut. Au clic, il doit afficher "ON". Au second clic, il doit revenir
à "OFF".

1. Rendez le composant `ToggleButton`.
2. Vérifiez que le bouton avec le texte "OFF" est bien présent à l'écran.
3. Simulez un clic sur ce bouton.
4. Vérifiez que le bouton affiche maintenant le texte "ON".
5. Simulez un autre clic.
6. Vérifiez que le bouton affiche à nouveau "OFF".

#### Correction exercice 19 {collapsible="true"}

```javascript
// src/components/ToggleButton.js (pour rappel)
import React, {useState} from 'react';

function ToggleButton() {
    const [isOn, setIsOn] = useState(false);
    return <button onClick={() => setIsOn(!isOn)}>{isOn ? 'ON' : 'OFF'}</button>;
}

// src/components/ToggleButton.test.js
import {render, screen} from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import ToggleButton from './ToggleButton';

describe('ToggleButton', () => {
    test('should display OFF by default and toggle to ON and back to OFF', async () => {
        const user = userEvent.setup();
        render(<ToggleButton/>);

        // 1. Vérifier l'état initial
        let button = screen.getByRole('button', {name: 'OFF'});
        expect(button).toBeInTheDocument();

        // 2. Simuler le premier clic
        await user.click(button);

        // 3. Vérifier le nouvel état
        // On doit "récupérer" le bouton car la référence a pu changer.
        button = screen.getByRole('button', {name: 'ON'});
        expect(button).toBeInTheDocument();
        // On s'assure que l'ancien texte n'est plus là.
        // `queryBy` retourne `null` si non trouvé, au lieu de lever une erreur.
        expect(screen.queryByRole('button', {name: 'OFF'})).toBeNull();

        // 4. Simuler le second clic
        await user.click(button);

        // 5. Vérifier le retour à l'état initial
        button = screen.getByRole('button', {name: 'OFF'});
        expect(button).toBeInTheDocument();
        expect(screen.queryByRole('button', {name: 'ON'})).toBeNull();
    });
});
```

---

## 4. Snapshot Testing

Le "snapshot testing" est une technique où Jest prend une "photo" de votre composant rendu la première fois et la
sauvegarde dans un fichier. Lors des tests suivants, il prend une nouvelle photo et la compare à l'ancienne. Si elles
sont différentes, le test échoue.

<procedure title="L'analogie du jeu des 7 erreurs">
<p>
Le premier test, vous donnez à Jest une image de référence. Ensuite, chaque fois que vous lancez les tests, c'est comme si vous lui donniez une nouvelle version de l'image en lui demandant de jouer au jeu des 7 erreurs. S'il trouve une différence, il vous alerte : "Attention, l'UI a changé ! Est-ce intentionnel ?". Si c'est le cas, vous lui dites "OK, mets à jour l'image de référence". Sinon, vous avez trouvé un bug (une régression visuelle).
</p>
</procedure>

**Cas d'usage :** Idéal pour des composants purement présentationnels qui ne devraient pas changer souvent.
**Attention :** À utiliser avec parcimonie. Les snapshots peuvent devenir difficiles à maintenir s'ils changent trop
souvent.

```javascript
// src/components/UserInfo.test.js
import {render} from '@testing-library/react';
import UserInfo from './UserInfo';

test('renders correctly', () => {
    const user = {name: 'John Doe', email: 'john@doe.com'};
    const {asFragment} = render(<UserInfo user={user}/>);

    // `toMatchSnapshot()` va créer un fichier .snap ou comparer avec l'existant.
    expect(asFragment()).toMatchSnapshot();
});
```

La première fois, un fichier `UserInfo.test.js.snap` sera créé. Si le JSX de `UserInfo` change, le test échouera jusqu'à
ce que vous mettiez à jour le snapshot (en pressant `u` dans la console de test).

---

## TP : Tester un formulaire de contact

Reprenons le formulaire de contact créé avec React Hook Form (Exercice 17) et écrivons une suite de tests pour lui.

### Étape 1 : Test du rendu initial

* Écrivez un test qui rend le formulaire `ContactForm`.
* Vérifiez que les champs "Nom", "Email", "Message" et le bouton "Envoyer" sont bien présents à l'écran. Utilisez
  `getByLabelText` et `getByRole`.

### Étape 2 : Test de la validation et des messages d'erreur

* Écrivez un test qui simule un clic sur le bouton "Envoyer" sans remplir aucun champ.
* Vérifiez que les messages d'erreur attendus apparaissent à l'écran (ex: "Le nom est obligatoire."). Utilisez
  `findByText` (qui est asynchrone) car l'affichage des erreurs peut être asynchrone avec React Hook Form.

### Étape 3 : Test de la soumission réussie

* Écrivez un test qui remplit tous les champs avec des données valides en utilisant `userEvent.type`.
* Simulez un clic sur le bouton "Envoyer".
* Vérifiez que les messages d'erreur n'apparaissent PAS.
* **Bonus :** "Mockez" la fonction `console.log` pour vérifier qu'elle a bien été appelée avec les bonnes données.

### Correction du TP {collapsible="true"}

```javascript
// src/components/ContactForm.test.js
import {render, screen, waitFor} from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import ContactForm from './ContactForm';

describe('ContactForm', () => {
    const setup = () => {
        const user = userEvent.setup();
        render(<ContactForm/>);
        return {user};
    };

    test('renders all fields and the submit button', () => {
        setup();
        expect(screen.getByLabelText(/nom/i)).toBeInTheDocument();
        expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
        expect(screen.getByLabelText(/message/i)).toBeInTheDocument();
        expect(screen.getByRole('button', {name: /envoyer/i})).toBeInTheDocument();
    });

    test('shows validation errors when submitted empty', async () => {
        const {user} = setup();
        const submitButton = screen.getByRole('button', {name: /envoyer/i});

        await user.click(submitButton);

        // findBy* est asynchrone, il attend que l'élément apparaisse
        expect(await screen.findByText(/le nom est obligatoire/i)).toBeInTheDocument();
        expect(screen.getByText(/l'email est obligatoire/i)).toBeInTheDocument();
        expect(screen.getByText(/le message ne peut pas être vide/i)).toBeInTheDocument();
    });

    test('does not show errors when fields are valid', async () => {
        const {user} = setup();

        await user.type(screen.getByLabelText(/nom/i), 'John Doe');
        await user.type(screen.getByLabelText(/email/i), 'john@example.com');
        await user.type(screen.getByLabelText(/message/i), 'Ceci est un message de test suffisamment long.');

        const submitButton = screen.getByRole('button', {name: /envoyer/i});
        await user.click(submitButton);

        // On vérifie que les messages d'erreur ne sont PAS là
        expect(screen.queryByText(/le nom est obligatoire/i)).toBeNull();
        expect(screen.queryByText(/l'email est obligatoire/i)).toBeNull();
        expect(screen.queryByText(/le message ne peut pas être vide/i)).toBeNull();
    });

    test('calls onSubmit with form data when submission is successful', async () => {
        // Mock de la fonction alert
        const alertMock = jest.spyOn(window, 'alert').mockImplementation(() => {
        });
        const {user} = setup();

        await user.type(screen.getByLabelText(/nom/i), 'Jane Doe');
        await user.type(screen.getByLabelText(/email/i), 'jane@example.com');
        await user.type(screen.getByLabelText(/message/i), 'Un autre message de test valide.');

        const submitButton = screen.getByRole('button', {name: /envoyer/i});
        await user.click(submitButton);

        // On attend que l'alerte soit appelée (indique que onSubmit a été exécuté)
        await waitFor(() => {
            expect(alertMock).toHaveBeenCalledWith('Formulaire envoyé !');
        });

        // Nettoyage du mock
        alertMock.mockRestore();
    });
});
```

---

## Auto-évaluation

Testez vos connaissances ! Les réponses se trouvent à la toute fin du support de cours.

#### Questions à Choix Multiple (QCM)

**1. Quelle est la philosophie principale de React Testing Library (RTL) ?**

1. Tester les détails d'implémentation, comme l'état interne d'un composant.
2. Tester les composants en se concentrant sur ce que l'utilisateur voit et fait.
3. Écrire des tests qui s'exécutent le plus rapidement possible, même s'ils sont moins fiables.
4. Remplacer complètement les tests End-to-End.

**2. Vous voulez trouver un bouton dans votre test. Quelle est la meilleure requête RTL à utiliser du point de vue de
l'accessibilité ?**

1. `getByTestId('submit-button')`
2. `getByText('Envoyer')`
3. `getByRole('button', { name: /envoyer/i })`
4. `container.querySelector('button')`

**3. Quelle est la différence entre `getByText` et `findByText` ?**

1. Il n'y a aucune différence.
2. `getByText` est pour le texte court, `findByText` pour le texte long.
3. `getByText` est synchrone (échoue immédiatement si non trouvé), `findByText` est asynchrone (attend un peu que
   l'élément apparaisse).
4. `findByText` est une ancienne version de `getByText`.

#### Questions Ouvertes

**4. Expliquez pourquoi il est généralement déconseillé de tester les détails d'implémentation d'un composant.**

**5. Vous testez un composant qui fait un appel API avec `axios` dans un `useEffect`. Comment feriez-vous pour tester ce
composant sans faire de véritable appel réseau ? (Donnez le nom de la technique).**

---

## Conclusion de ce module

Vous avez ajouté une corde essentielle à votre arc : la capacité de garantir la qualité et la robustesse de votre code.
Vous savez désormais que les tests ne sont pas une corvée, mais un outil puissant pour développer plus vite et avec plus
de confiance.

Vous avez découvert l'écosystème de test moderne de React avec **Jest** et **React Testing Library**. Vous avez appris à
penser comme un utilisateur, à écrire des tests qui sont à la fois significatifs et résilients au changement. Vous savez
tester la logique pure avec des **tests unitaires** et les interactions complexes avec des **tests de composants**.

Nous approchons de la fin de notre parcours. Nous savons concevoir, développer, architecturer et tester. Il ne nous
reste plus qu'à polir notre travail et à le mettre au monde. Le prochain et dernier module couvrira les **outils, la
qualité du code et le déploiement** de votre application React.

## Suggestions de projets pour pratiquer

1. **Niveau Débutant : Tester une TodoList**
    * **Description :** Prenez une application TodoList simple. Écrivez des tests pour :
        1. Ajouter une nouvelle tâche et vérifier qu'elle apparaît dans la liste.
        2. Cocher une tâche et vérifier qu'elle a bien le style "terminé".
        3. Supprimer une tâche et vérifier qu'elle disparaît de la liste.
    * **Piste technique :** C'est un excellent exercice pour pratiquer `userEvent.type`, `userEvent.click` et les
      assertions sur la présence/absence d'éléments.

2. **Niveau Intermédiaire : Tester un composant avec appel API**
    * **Description :** Prenez le composant `RandomCatFact` et testez ses différents états.
    * **Piste technique :** Vous devrez "mocker" `axios` (ou `fetch`) avec `jest.mock`. Créez des tests pour trois
      scénarios :
        1. L'API répond avec succès : vérifiez que le message de chargement disparaît et que le fait est affiché.
        2. L'API retourne une erreur : vérifiez que le message d'erreur est affiché.
        3. Pendant le chargement : vérifiez que le message de chargement est bien affiché initialement.

3. **Niveau Avancé : Atteindre 100% de couverture de test sur un hook personnalisé**
    * **Description :** Prenez votre hook `useFetch`. Installez un outil pour mesurer la couverture de test (
      `npm test -- --coverage`). Écrivez des tests unitaires pour ce hook (en utilisant `@testing-library/react-hooks`
      ou une approche similaire) pour couvrir tous les cas (succès, erreur, changement d'URL, etc.) jusqu'à atteindre
      100% de couverture sur ce fichier.
    * **Piste technique :** C'est un exercice avancé qui vous apprendra à tester la logique de hook de manière isolée et
      à penser à tous les cas limites.