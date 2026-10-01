# Guide d’intégration — Bible ↔ brochures

## Ce paquet contient

- `references-croisees-validees.json` : 162 823 relations, sans extraits de preuve ni argumentation.
- `references-croisees-validees.csv` : même contenu pour tableur/ETL.
- `documents-index.json` : correspondance stable entre `document_key` et les 1 595 brochures.
- `bible-verse-index.json` : index des 31 102 versets et de leur identifiant interne.
- `REFERENCES-CROISEES-VALIDEES.pdf` : rendu lisible de **toutes** les relations.

## Clé de raccordement

Pour chaque relation JSON/CSV :

1. Identifier la brochure avec `document_key`. Ne jamais fusionner les éditions `shekinah` et `vgr`.
2. Identifier le verset de la nouvelle Bible avec `bible_osis` (ex. `Heb.11.1`). C’est la clé canonique ; `source_verse_id` est seulement l’identifiant interne de cette collection.
3. Relier le paragraphe avec `paragraph_number`. Les numéros de paragraphe doivent correspondre à la même édition de brochure.
4. Utiliser une clé unique `(document_key, paragraph_number, bible_osis)`.
5. Conserver les relations multiples : plusieurs versets peuvent pointer vers un paragraphe et un verset peut pointer vers plusieurs paragraphes.
6. Conserver les paragraphes numéro 0 s’ils existent.

## Schéma d’une relation

```json
{
  "source_document_id": 0,
  "document_key": "wmb:47-0412:shekinah",
  "paragraph_number": 13,
  "source_verse_id": 30173,
  "bible_osis": "Heb.11.1",
  "link_type_code": 0
}
```

`link_type_code` est conservé comme code brut de la source. Il ne faut pas lui attribuer une signification nouvelle sans la documentation de la plateforme cible.

## Exemple SQL

```sql
INSERT INTO cross_reference(document_key, paragraph_number, bible_osis, source_verse_id, link_type_code)
VALUES (:document_key, :paragraph_number, :bible_osis, :source_verse_id, :link_type_code)
ON CONFLICT(document_key, paragraph_number, bible_osis) DO NOTHING;
```

## Limite importante

Ce paquet de références permet d’intégrer les **liens**, mais il ne contient pas le texte intégral des brochures ni le texte d’une traduction biblique cible. Pour afficher les paragraphes, la plateforme doit déjà posséder les brochures correspondantes, avec la même numérotation et la même édition, ainsi qu’une Bible indexée par OSIS. Le HTML publié contient, lui, la Bible et les brochures complètes.
