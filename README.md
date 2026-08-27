# Analysis Ultimate — Basalte-Web

**Version 4.0.0 ULTIMATE URBAN FUSION** — moteur local d’intelligence commerciale autonome pour les extractions TGM.

Cette version conserve toutes les fonctions Clients / Ventes / Catalogue, Autopilot, Geo Intelligence et Causal Context des versions précédentes. Elle ajoute une couche **Urban Fusion** : chaque hausse, baisse ou anomalie commerciale est automatiquement comparée à plusieurs facteurs externes utiles, tandis que les données clients restent dans le navigateur.

## Fonctionnement normal

L’utilisateur charge :

- `Clients.xlsx`, `Clients(3).xlsx`, etc. ;
- un ou plusieurs `Ventes.csv`, `Ventes(1).csv`, etc. ;
- `Catalogue.xlsx`, `Catalogue(14).xlsx`, etc.

Analysis Ultimate enchaîne automatiquement : validation → croisement → diagnostic → clients → produits → stock → fidélisation → géographie → contexte urbain → causes concurrentes → actions/priorités → historique.

Le nom du fichier ne suffit jamais : colonnes, dates, identifiants, cohérence financière, doublons et contradictions sont contrôlés avant analyse.

## Urban Fusion Engine

Le workflow GitHub Actions public collecte et historise uniquement des données non personnelles :

- **Clermont Auvergne Métropole Explore API** : catalogue Open Data, occupation des parkings, Base Adresse Locale, axes de voie et jeux opérationnels mobilité/voirie pertinents ;
- **pages officielles des travaux** : source complémentaire et de secours ;
- **Agenda de Clermont-Ferrand** : événements publics et dates ;
- **T2C GTFS-RT** : état du flux, trip updates, retards/annulations quand le flux peut être décodé ;
- **C.vélo** : vélos et places disponibles ;
- **ZELT** : comptages vélo quotidiens ;
- **Open-Meteo** : pluie et température ;
- **calendrier local** : vacances scolaires Zone A et jours fériés déjà traités dans le navigateur.

Le contexte public est écrit dans `data/public-context.json` et son historique synthétique dans `data/public-context-history.json`. Une source en panne n’empêche jamais l’analyse TGM.

## Ce que le moteur explique automatiquement

Pour chaque constat exploitable (CA, tickets, panier, marge, jour de semaine, rayon, famille, produit, clients, retours, zone…), le Causal Context Engine teste en parallèle :

1. fréquentation et panier ;
2. concentration géographique ;
3. travaux / fermeture / déviation / voirie ;
4. T2C ;
5. stationnement ;
6. agenda / événements ;
7. météo ;
8. activité urbaine ZELT / C.vélo lorsque l’historique le permet ;
9. stock / rupture ;
10. prix ;
11. vacances / saisonnalité / jours fériés ;
12. remises et mix de marge.

Le logiciel sépare toujours : **faits**, **calculs**, **corrélations**, **estimations** et **hypothèses**. Un facteur public peut être fortement compatible avec un mouvement commercial sans être présenté comme une preuve absolue de causalité.

## Geo Intelligence — fiabilisé en v4

Le problème intermittent où l’écran Geo Intelligence pouvait rester vide est traité par plusieurs garde-fous :

- rendu différé dans une frame dédiée pour éviter de bloquer la navigation ;
- annulation des rendus devenus obsolètes lorsqu’on change rapidement d’écran ;
- protection des valeurs manquantes/nulles ;
- limitation du tableau principal aux zones pertinentes afin d’éviter un DOM excessif ;
- dessin des graphiques seulement si leur canvas est encore connecté ;
- `try/catch` par vue : une erreur de rendu donne maintenant un message + **Réessayer l’affichage**, jamais un écran silencieusement vide.

L’écran Geo affiche également un **Urban Pressure Score** et les états T2C, parking, événements, C.vélo et ZELT.

## Anti-faux-positifs Open Data

Le moteur de découverte automatique exclut fortement les jeux dont les mots « travaux » correspondent en réalité à des marchés publics, budgets, subventions, patrimoine ou archéologie. Pour créer un événement de voirie depuis une donnée API, il exige maintenant des indices opérationnels comme circulation, fermeture, déviation, voie, route barrée ou une combinaison travaux + rue/route/avenue/boulevard.

## Historique et apprentissage dans le temps

Le workflow tourne quatre fois par jour et conserve jusqu’à **2 160 snapshots synthétiques**. Cela permet progressivement d’estimer, par exemple :

- relation entre occupation parking et fréquentation ;
- relation entre retards T2C et visites ;
- comportement avant / pendant / après un chantier ;
- rebond après réouverture ;
- sensibilité à la pluie ;
- effet d’événements locaux ;
- évolution de la mobilité urbaine.

Les conclusions historiques ne deviennent solides qu’après accumulation de suffisamment d’observations comparables.

## Confidentialité

Les fichiers Clients, Ventes et Catalogue sont traités localement. Le workflow public ne lit jamais IndexedDB, le navigateur de l’utilisateur, ni les extractions TGM. Il ne reçoit donc aucun nom, téléphone, e-mail, ticket ou détail d’achat.

La Base Adresse Locale est téléchargée/inspectée comme donnée publique de référence ; **les adresses clients ne lui sont pas envoyées**. La classification client reste locale.

## Installation GitHub Pages

1. Mettre **tout le contenu du ZIP** à la racine du dépôt, y compris `.github/`, `.gitignore` et `.nojekyll`.
2. Activer GitHub Pages sur la branche `main` / racine si nécessaire.
3. Dans **Actions**, lancer une première fois **Clermont API Sentinel**.
4. Si le workflow ne peut pas pousser ses deux JSON, activer `Settings → Actions → General → Workflow permissions → Read and write permissions`.
5. Attendre le redéploiement Pages puis faire `Ctrl + Shift + R` une fois.
6. Vérifier que l’application affiche **v4.0.0 ULTIMATE URBAN FUSION**.

Le workflow est ensuite planifié quatre fois par jour. GitHub peut décaler l’heure exacte des jobs planifiés.

## Fichiers importants

- `js/intelligence.js` : diagnostics autonomes ;
- `js/geo.js` : analyse des zones commerciales ;
- `js/causal-context.js` : causes concurrentes et chaînes explicatives ;
- `js/public-context.js` : fusion des données publiques avec le modèle local ;
- `scripts/update_public_context.py` : collecteur public multi-source ;
- `scripts/validate_public_context.py` : validation transactionnelle ;
- `.github/workflows/update-public-context.yml` : exécution automatique ;
- `docs/URBAN_FUSION_ENGINE.md` : architecture et règles de confiance.

## Limite fondamentale

Analysis Ultimate peut automatiser énormément d’observations et tester des explications, mais ne peut pas rendre vraie une donnée source erronée ni prouver à lui seul qu’un chantier « cause » une baisse. Il réduit ce risque avec zones témoins, chronologie, concurrence de plusieurs hypothèses et score de confiance.
