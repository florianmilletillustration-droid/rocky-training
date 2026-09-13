# ROADMAP — Rocky Training

Les phases se font **dans l'ordre**. Un seul item par pull request.
Cocher l'item dans la même PR que l'implémentation.

**Pourquoi cet ordre :** le visuel vient en dernier. Construire la constellation avant que les
données veuillent dire quelque chose reviendrait à décorer des chiffres faux.

---

## Phase 0 — Correctif et filet de sécurité

- [ ] **Export JSON complet des données de pratique.**
  Un bouton qui télécharge tout. À faire **avant** toute migration. C'est le filet.

- [ ] **Ajouter `schemaVersion` aux documents Firestore**, avec lecture rétrocompatible.

## Phase 1 — Taxonomie et migration

- [ ] **Implémenter le modèle Domaine → Discipline → Médium** (voir CLAUDE.md §4).

- [ ] **Migrer les catégories plates existantes** vers la nouvelle structure.
  Mapping explicite, écrit dans la PR, validé par Florian avant merge.
  *Aucune minute de pratique ne doit disparaître.* Tester sur une copie d'abord.

- [ ] **Adapter les trois méthodes d'entrée** (ajout rapide, chrono, manuel) à la nouvelle
  taxonomie, sans ajouter d'étape à l'ajout rapide.

- [ ] **Ajouter le domaine Corps** : Course, Gym, Randonnée, Autre. Temps uniquement.

## Phase 2 — Qualité de pratique

- [ ] **Marqueur Délibéré / Kilométrage** sur chaque session de Pratique visuelle.
  Un seul geste. Deux boutons, pas un écran.

- [ ] **Séparer les totaux** dans les stats : temps total, dont temps délibéré.

- [ ] **Boucle de feedback du Coach** après une session marquée Délibéré : deux questions
  courtes, deux phrases de réponse.

## Phase 3 — Évaluation

- [ ] **Auto-évaluation mensuelle** du niveau de chaque branche.

- [ ] **Registre des faiblesses** par branche : liste courte, éditable, révisée à
  l'auto-évaluation.

- [ ] **Jalons par preuve** : liste de jalons concrets cochables par branche.
  Les premiers jalons sont à rédiger **par Florian**, pas à inventer.

## Phase 4 — La constellation

- [ ] **Vue d'ensemble** par discipline, affichant le niveau auto-évalué.

- [ ] **Zoom** sur une discipline pour ouvrir ses médiums en sous-constellation.

- [ ] **Dormance** : estompement progressif d'une branche non travaillée. Ton neutre.

- [ ] **Instantané mensuel** archivé et consultable dans le temps.

- [ ] **Temps investi affiché sous la constellation**, jamais à la place du niveau.

## Phase 5 — Le cycle

- [ ] Le programme devient cyclique : après la semaine 12, un nouveau cycle de
      12 semaines démarre. Le compteur devient « Cycle N · Semaine X/12 ».

- [ ] Rituel de fin de cycle : auto-évaluation de toutes les branches, révision
      du registre des faiblesses, archivage de l'instantané de la constellation.

- [ ] Le contenu des semaines sort du code et devient une donnée modifiable,
      pour que chaque cycle puisse avoir son propre contenu.

- [ ] Le contenu d'un nouveau cycle se compose à partir du registre des
      faiblesses issu du rituel de fin de cycle. Ne jamais générer un cycle
      automatiquement : Florian le compose, l'app l'assiste.

## Idées non validées

À ne pas implémenter sans approbation explicite de Florian.

- [ ] Vue historique / graphique du temps par semaine.
- [ ] Import de références ou de photos de travaux dans les jalons.

## Fait

- [x] **Bug : les jours validés ne se réinitialisent pas chaque semaine.** *(2026-09-13)*
  Les jours cochés restaient verts d'une semaine à l'autre. Corrigé en indexant le marqueur
  de jour validé sur la date calendaire réelle plutôt que sur (position dans la semaine +
  numéro de semaine manuel). Voir DECISIONS.md.
