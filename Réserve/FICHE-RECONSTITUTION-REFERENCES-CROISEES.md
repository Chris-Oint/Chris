# Fiche de réserve — reconstitution Bible, brochures et références croisées

## Objectif

Le dépôt principal contient l’application publiée dans `index.html`. Cette application doit rester intacte. Le dossier `Réserve/` est une copie autonome des données nécessaires pour reconstruire une autre interface ou réintégrer les ressources dans une future version.

## Contenu extrait

- `bible/bible-complete.json` : les 66 livres et leurs chapitres/versets, dans l’ordre utilisé par l’application.
- `bible/bible-index.json` : index des livres, codes et offsets de zones.
- `brochures/zones/brochures_z1.json` à `brochures_z5.json` : textes complets des brochures par zone, métadonnées et index de recherche.
- `brochures/documents-index.json` : index lisible des brochures.
- `references-croisees/` : copie du paquet validé JSON/CSV/PDF, des index Bible/brochures et du ZIP d’intégration.
- `INVENTAIRE.json` : comptages et provenance de l’extraction.

## Modèle de référence croisée

Dans le moteur de l’application, le paquet de liaison suit ce modèle :

1. Un verset est identifié par son index global `verseIndex` (le fichier `bible-verse-index.json` aide à retrouver livre, chapitre et verset).
2. Une liaison de `D.links` est un tableau `[documentGlobal, numeroParagraphe, verseIndex, categorie]`.
3. `D.meta[documentGlobal]` fournit le code/date, le titre, la traduction/source, le nombre de paragraphes et la zone.
4. `D.zoff[zone]` permet de convertir `documentGlobal` en index local dans `brochures_zN.json`.
5. Dans le fichier de zone, chaque document contient ses paragraphes ; le paragraphe est retrouvé par son numéro.

Pseudo-code de reconstruction :

```text
pour chaque liaison [docGlobal, paragraphe, verseIndex, categorie] :
    meta = payload.meta[docGlobal]
    zone = meta.zone
    docLocal = docGlobal - payload.zoff[zone]
    brochure = brochures_z{zone}.docs[docLocal]
    afficher bibleVerse(verseIndex) <-> brochure.paragraph(paragraphe)
    conserver categorie pour le classement visuel
```

## Règles de sécurité des données

- Ne pas modifier `index.html` dans la branche d’application si l’objectif est de conserver la publication actuelle.
- Ne pas dédupliquer les brochures VGR/Shekinah uniquement sur le titre : le code, la source et la zone font partie de l’identité.
- Conserver les numéros de paragraphes et les index globaux tels quels ; les renuméroter casserait les références croisées.
- Utiliser UTF-8 et préserver les accents français.
- Après toute reconstruction, vérifier au minimum : nombre de livres, nombre de brochures, nombre de liens et quelques références Bible ↔ paragraphe dans chaque zone.

## Provenance

Extraction effectuée depuis les données embarquées de `index.html` et copie des fichiers validés déjà présents dans `cross-references/`. L’application publiée à la racine n’est ni supprimée ni réécrite par cette réserve.
