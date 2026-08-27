# Sécurité et confidentialité — Analysis Ultimate 4.0

## Données TGM

Clients, Ventes et Catalogue sont lus localement dans le navigateur. Ils ne sont pas envoyés à GitHub, Basalte-Web, Clermont Auvergne Métropole, T2C, Open-Meteo ou une autre source publique.

Le `.gitignore` bloque CSV, XLS, XLSX, ODS et ZIP pour réduire le risque d’envoi accidentel d’une extraction.

## Urban Fusion Sentinel

GitHub Actions ne collecte que des données publiques : Open Data Clermont Métropole, pages officielles travaux, agenda municipal, T2C GTFS-RT, C.vélo/ZELT et météo.

Les données publiques sont validées avant publication. Si une source tombe ou renvoie une structure inattendue, le reste du contexte continue et l’état de la source est conservé dans le payload.

## Adresses clients

Aucune adresse client n’est envoyée vers la Base Adresse Locale ni vers un géocodeur. Les références publiques de voirie/adresse servent uniquement au contexte public ; le rattachement des clients aux zones reste local.

## T2C

Le GTFS-RT est traité comme un indicateur contextuel. Le flux peut être disponible tout en présentant des erreurs de validation ou des données partielles ; son état de décodage est enregistré et sa confiance est réduite lorsque nécessaire.

## Résilience

- contexte public chargé avec `cache: no-store` ;
- Service Worker network-first pour les JSON publics ;
- dernier contexte utilisable conservé en cas de panne ;
- rendu Geo Intelligence protégé contre les exceptions d’interface ;
- une panne externe ne bloque jamais l’analyse TGM locale.

## Stockage local

IndexedDB peut conserver la session importée et des snapshots synthétiques. Le bouton « Effacer les données locales » supprime ces éléments dans le navigateur concerné.

## Interprétation

Les scores contextuels ne sont pas des preuves causales. L’interface doit conserver les catégories faits / calculs / estimations / hypothèses et afficher les limites de couverture ou de fraîcheur.
