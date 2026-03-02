# 📚 Évaluation — BookShelf

**Formation React Native & Expo — Évaluation finale**

| | |
|---|---|
| **Durée** | 2 heures |
| **Documents autorisés** | Documentation officielle uniquement (reactnative.dev, docs.expo.dev) et application CineTrack|
| **Rendu** | Merci de fork le projet comme ça je pourrais accèder à vos rendus  |
| **Barème** | /100 points (+5 bonus) |

---

## Contexte

Vous devez compléter **BookShelf**, une application de découverte de livres avec une **liste de lecture** à 3 statuts ("À lire", "En cours", "Terminé").
L'application est partiellement construite : la navigation, les couleurs et les données sont en place.

**Votre travail** : compléter les **fonctionnalités marquées `// TODO`** pour rendre l'application fonctionnelle et enrichie.

Chaque TODO est numéroté, localisé dans un fichier précis, et décrit ce qui est attendu.
Les points sont indiqués pour chaque TODO.

---

## Lancer le projet

```bash
npm install
npx expo start
```

---

## Architecture du projet

```
app/                           ← Navigation
├── (tabs)/
│   ├── _layout.js             ✅ Complet
│   ├── index.js               📝 Utilise TODO 1, 2, 5, 8, 9
│   ├── search.js              📝 Contient TODO 6
│   └── favorites.js           ✅ Complet (utilise le contexte)
├── detail/
│   └── [id].js                📝 Contient TODO 3, 11, 13 (bonus)
└── _layout.js                 ✅ Complet

components/
├── BookCard.js                📝 TODO 1
└── ReadingProgress.js         📝 TODO 13 (bonus)

services/
└── books.js                   📝 TODO 4

hooks/
├── useBooks.js                📝 TODO 5
└── useSearch.js               📝 TODO 6

contexts/
└── ReadingListContext.js       📝 TODO 7, 10

constants/                     ✅ Complet
data/books.json                ✅ Données fournies
utils/                         ✅ Complet
```

---

## Les TODO

### TODO 1 — Composant BookCard (12 pts)
📄 **Fichier** : `components/BookCard.js`

Créer un composant `BookCard` qui affiche une carte de livre avec :
- L'image de couverture (prop `cover`, affichée via `Image` avec une URI)
- Le titre du livre (prop `title`)
- L'auteur (prop `author`)
- La note (prop `rating`, affichée avec `★` et la valeur numérique)
- Un `Pressable` englobant avec feedback visuel (opacity) et un `onPress`
- Styles via `StyleSheet.create()` en utilisant les couleurs de `constants/colors.js`

**Props attendues** : `{ title, author, cover, rating, onPress }`

---

### TODO 2 — Sections horizontales par genre (10 pts)
📄 **Fichier** : `app/(tabs)/index.js`

Afficher les livres regroupés par genre avec un défilement horizontal par section (style Netflix) :
- Regrouper les livres par genre avec `useMemo` (format : `[{ genre, data }]`)
- Utiliser un `ScrollView` vertical contenant les sections
- Pour chaque section : un `<Text>` titre du genre + une `<FlatList horizontal>`
- Chaque `FlatList` : `horizontal`, `showsHorizontalScrollIndicator={false}`, `keyExtractor`, `contentContainerStyle`, `ItemSeparatorComponent` (gap 12)
- Les `BookCard` dans les listes horizontales ont une largeur fixe (ex: 160)

---

### TODO 3 — Navigation détail (8 pts)
📄 **Fichier** : `app/detail/[id].js`

Compléter l'écran de détail :
- Récupérer le paramètre `id` avec `useLocalSearchParams()`
- Trouver le livre correspondant dans les données
- Afficher : titre, auteur, description, couverture, note, nombre de pages
- Gérer le cas où le livre n'est pas trouvé (message d'erreur)

---

### TODO 4 — Service API (12 pts)
📄 **Fichier** : `services/books.js`

Compléter les 3 fonctions du service :
- `getPopularBooks()` : retourne la liste des livres depuis `data/books.json`
- `getBookById(id)` : retourne un livre par son id
- `searchBooks(query)` : filtre les livres dont le titre ou l'auteur contient la query

Chaque fonction doit :
- Être `async`
- Simuler un délai réseau avec le `delay()` fourni
- Utiliser le pattern `try/catch` avec gestion d'erreur

---

### TODO 5 — Custom Hook useBooks (12 pts)
📄 **Fichier** : `hooks/useBooks.js`

Créer un hook `useBooks` qui :
- Gère 3 states : `books`, `loading`, `error`
- Appelle `getPopularBooks()` du service au montage
- Expose une fonction `refresh` pour recharger les données
- Retourne `{ books, loading, error, refresh }`

---

### TODO 6 — Hook useSearch avec debounce (12 pts)
📄 **Fichier** : `hooks/useSearch.js`

Compléter le hook `useSearch` :
- Le state `query` et `setQuery` sont fournis
- Implémenter le debounce à 300ms avec `setTimeout`
- La cleanup function doit annuler le timer
- Si query est vide, vider les résultats sans appeler le service
- Gérer `loading` pendant la recherche

---

### TODO 7 — ReadingListContext (10 pts)
📄 **Fichier** : `contexts/ReadingListContext.js`

Compléter le système de liste de lecture avec 3 statuts (`to_read`, `reading`, `finished`) :

**Partie A — Le reducer (5 pts)**
- `LOAD` : remplace le state par `action.payload`
- `ADD_TO_LIST` : ajoute `{ book, status }` (si pas déjà présent)
- `UPDATE_STATUS` : change le statut d'une entrée existante
- `REMOVE_FROM_LIST` : retire un livre par son id

**Partie B — Le Provider (5 pts)**
- `useReducer` avec le reducer et `[]` comme état initial
- `addToList(book, status)`, `updateStatus(id, status)`, `removeFromList(id)`
- `getBookStatus(id)` → retourne le statut ou null
- `isInList(id)` → retourne true/false

---

### TODO 8 — Filtres par genre (5 pts)
📄 **Fichier** : `app/(tabs)/index.js`

Ajouter un système de filtrage par genre sur l'écran d'accueil :
- Extraire dynamiquement les genres disponibles depuis la liste de livres (avec `useMemo`)
- Afficher des chips horizontaux (`ScrollView` horizontal) permettant de sélectionner un genre
- Ajouter un chip "Tous" pour réinitialiser le filtre
- Filtrer les sections en fonction du genre sélectionné

---

### TODO 9 — Tri des livres (5 pts)
📄 **Fichier** : `app/(tabs)/index.js`

Ajouter un système de tri sur l'écran d'accueil :
- Proposer plusieurs options de tri : par défaut, note croissante, note décroissante, titre A-Z, titre Z-A
- Afficher les options sous forme de chips horizontaux
- Appliquer le tri sur les livres dans chaque section (compatible avec le filtre genre)
- Utiliser `useMemo` pour optimiser le calcul

---

### TODO 10 — Persistance de la liste de lecture (6 pts)
📄 **Fichier** : `contexts/ReadingListContext.js`

Persister la liste de lecture avec `AsyncStorage` :
- Au montage du Provider, charger la liste sauvegardée (action `LOAD` dans le reducer)
- À chaque changement de la liste (après chargement initial), sauvegarder dans `AsyncStorage`
- Gérer les erreurs silencieusement (ne pas bloquer l'app si le storage échoue)

---

### TODO 11 — Sélecteur de statut de lecture (8 pts)
📄 **Fichier** : `app/detail/[id].js`

Ajouter un sélecteur de statut sur l'écran de détail :
- Importer `useReadingList` et `READING_STATUSES` depuis `contexts/ReadingListContext`
- Afficher 3 boutons : "📖 À lire", "📚 En cours", "✅ Terminé"
- Le bouton du statut actuel doit être visuellement distinct (couleur différente)
- Au press : si pas dans la liste → `addToList(book, status)` / si déjà → `updateStatus(id, status)`
- Si dans la liste, afficher un bouton "Retirer de ma liste" → `removeFromList(id)`

---

### TODO 13 — BONUS : ReadingProgress (+5 pts)
📄 **Fichier** : `components/ReadingProgress.js` + `app/detail/[id].js`

Créer un composant barre de progression basé sur le statut de lecture :
- `to_read` → 0%, `reading` → 50%, `finished` → 100%
- Afficher un label, une barre colorée (rouge → orange → vert) et le pourcentage
- Intégrer le composant dans l'écran de détail sous le sélecteur de statut

---

## Barème récapitulatif

| TODO | Description | Points |
|------|-------------|--------|
| 1 | BookCard | 12 |
| 2 | Sections horizontales par genre | 10 |
| 3 | Navigation détail | 8 |
| 4 | Service API | 12 |
| 5 | Custom Hook useBooks | 12 |
| 6 | Hook useSearch + debounce | 12 |
| 7 | ReadingListContext (reducer + provider) | 10 |
| 8 | Filtres par genre | 5 |
| 9 | Tri des livres | 5 |
| 10 | Persistance AsyncStorage | 6 |
| 11 | Sélecteur de statut de lecture | 8 |
| **Total** | | **100** |
| 13 | BONUS : ReadingProgress | +5 |

---

## Critères de qualité (bonus, jusqu'à +5 pts)

- Code propre et lisible (nommage, indentation)
- Gestion complète des cas d'erreur
- Utilisation systématique de `StyleSheet.create()`
- Respect de l'architecture 3 couches
- Livres similaires (même genre) affichés sur l'écran de détail

---

**Bon courage ! 🚀**
