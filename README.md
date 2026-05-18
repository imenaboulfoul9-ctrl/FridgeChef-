# 🍳 FridgeChef

> **Découvre des recettes à partir des ingrédients de ton frigo.**  
> Application web monopage — HTML / CSS / JavaScript vanilla pur.

---

## 📋 Table des matières

- [Aperçu](#-aperçu)
- [Fonctionnalités](#-fonctionnalités)
- [Structure du projet](#-structure-du-projet)
- [Architecture du code JS](#-architecture-du-code-js)
- [API externe](#-api-externe--themealdb)
- [Stockage des données](#-stockage-des-données)
- [Critères pédagogiques couverts](#-critères-pédagogiques-couverts)
- [Lancement](#-lancement)
- [Compatibilité](#-compatibilité)

---

## 🌟 Aperçu

FridgeChef est une **SPA (Single Page Application)** sans framework ni dépendance.  
L'utilisateur entre les ingrédients disponibles, l'application interroge l'API **TheMealDB** en temps réel et affiche les recettes correspondantes triées par pertinence.

---

## ✨ Fonctionnalités

### Recherche par ingrédients
- Saisie libre d'ingrédients affichés sous forme de **chips** supprimables
- Validation : doublons bloqués, champ vide ignoré
- Déclenchement par clic ou touche **Entrée**
- Bouton de recherche désactivé tant qu'aucun ingrédient n'est saisi

### Résultats
- Requêtes parallèles (`Promise.all`) pour chaque ingrédient saisi
- **Algorithme de score** : les recettes contenant plusieurs ingrédients remontent en premier
- Badge 🔥 indiquant le nombre d'ingrédients correspondants
- Affichage limité à 20 résultats, animés au chargement

### Détail d'une recette (modale)
- Photo, catégorie, origine géographique
- Liste complète des ingrédients avec photos miniatures
- Instructions de préparation complètes
- **Zone de notes personnelles** persistée en IndexedDB

### Favoris
- Ajout / retrait depuis la carte ou depuis la modale
- Vue dédiée **❤️ Favoris** accessible via la navigation
- Persistance entre sessions via **localStorage**
- Compteur dynamique dans la barre de navigation

### UX
- Toast de notification pour chaque action (ajout, note sauvegardée, erreur)
- Design responsive (mobile ≥ 320px)
- Header sticky, animations CSS fluides

---

## 📁 Structure du projet

```
fridgechef.html          ← fichier unique, tout-en-un
```

Le fichier est organisé en trois blocs :

```
fridgechef.html
├── <head>               Métadonnées + Google Fonts (Playfair Display, DM Sans)
├── <style>              CSS complet (variables, composants, responsive)
├── <header>             Barre de navigation sticky
├── #heroSection         Zone de saisie des ingrédients
├── <main>
│   ├── #viewRecherche   Grille de résultats de recherche
│   └── #viewFavoris     Grille des recettes sauvegardées
├── .modal-overlay       Modale de détail d'une recette
├── .notif               Toast de notification
└── <script>             Logique JavaScript complète (~350 lignes)
```

---

## 🧩 Architecture du code JS

Le bloc `<script>` est divisé en **9 sections** délimitées par des commentaires :

| Section | Rôle | Fonctions |
|---|---|---|
| `STATE` | Variables globales | `ingredients[]`, `favorites[]`, `currentDetail` |
| `INDEXEDDB` | Persistance des notes | `initDB()`, `saveNoteToDB()`, `getNoteFromDB()` |
| `LOCALSTORAGE` | Persistance des favoris | `loadFavorites()`, `saveFavorites()`, `isFavorite()` |
| `VIEWS` | Gestion des vues | `showView()` |
| `INGREDIENTS` | Saisie & chips | `addIngredient()`, `removeIngredient()`, `renderChips()` |
| `API FETCH` | Appels réseau | `searchRecipes()`, `createRecipeCard()` |
| `DETAIL MODAL` | Modale recette | `openDetail()`, `closeModal()`, `saveNote()` |
| `FAVORIS` | Gestion favoris | `toggleFavorite()`, `renderFavorites()` |
| `NOTIFICATION` | Toast UI | `showNotif()` |

---

## 🌐 API externe — TheMealDB

API gratuite, sans clé, sans inscription.  
Base URL : `https://www.themealdb.com/api/json/v1/1/`

| Endpoint | Paramètre | Usage |
|---|---|---|
| `/filter.php` | `?i={ingredient}` | Lister les recettes par ingrédient |
| `/lookup.php` | `?i={id}` | Détail complet d'une recette |
| `/images/ingredients/{name}-Small.png` | — | Miniature d'un ingrédient |

---

## 💾 Stockage des données

### localStorage — Favoris

```
Clé   : "fridgechef_favorites"
Valeur: JSON (tableau d'objets recette)
```

```json
[
  {
    "idMeal": "52772",
    "strMeal": "Teriyaki Chicken",
    "strMealThumb": "https://...",
    "strArea": "Japanese",
    "strCategory": "Chicken",
    "count": 2
  }
]
```

### IndexedDB — Notes personnelles

```
Base de données : FridgeChefDB  (version 1)
Object store    : notes  (keyPath: "id")
```

```json
{ "id": "52772", "text": "Ajouter plus de gingembre la prochaine fois..." }
```

---

## ✅ Critères pédagogiques couverts

| Critère | Implémentation |
|---|---|
| **Manipulation du DOM** | Génération dynamique des chips, cartes, grilles et modale via JS pur |
| **Gestion des événements** | `click`, `keydown` (Entrée), délégation d'événements sur les cartes |
| **Structures de données JS** | `Array`, objets (`countMap`), `JSON`, spread operator |
| **Modularité du code** | 9 sections commentées, fonctions à responsabilité unique |
| **Validation des données** | Doublons bloqués, champ vide ignoré, bouton désactivé si liste vide |
| **localStorage** | Favoris persistants (`fridgechef_favorites`) |
| **IndexedDB** | Notes personnelles par recette (`FridgeChefDB`) |
| **`fetch()` + API REST** | TheMealDB : `/filter.php` et `/lookup.php` |
| **Données URL externes** | Photos de recettes et miniatures d'ingrédients |
| **`async/await` + `Promise`** | `Promise.all()` pour requêtes parallèles, `async/await` partout |

---

## 🚀 Lancement

Aucune installation requise.

```bash
# Option 1 — Ouvrir directement dans le navigateur
open fridgechef.html

# Option 2 — Serveur local (recommandé pour IndexedDB)
npx serve .
# ou
python -m http.server 8080
```

> ⚠️ Une **connexion internet** est nécessaire pour accéder à l'API TheMealDB et aux polices Google Fonts.

---

## 🖥️ Compatibilité

| Navigateur | Support |
|---|---|
| Chrome 90+ | ✅ |
| Firefox 88+ | ✅ |
| Edge 90+ | ✅ |
| Safari 15+ | ✅ |
| Internet Explorer | ❌ |

Requiert la prise en charge de : `ES2022`, `async/await`, `Promise.all`, `IndexedDB`, `localStorage`, `fetch`.

---

## 👤 Auteur

Projet réalisé dans le cadre d'un cours de **JavaScript / Développement Web**.
