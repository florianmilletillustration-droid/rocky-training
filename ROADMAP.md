# ROADMAP — Rocky Training

Les phases se font **dans l'ordre**. Un seul item par pull request.
Cocher l'item dans la même PR que l'implémentation.

**Pourquoi cet ordre :** évaluation et sphérier passent devant taxonomie et qualité de
pratique — changement de priorité demandé par Florian le 15 septembre 2026. Raison
détaillée dans DECISIONS.md.

---

## Phase 0 — Correctif et filet de sécurité

- [x] **Export JSON complet des données de pratique.** *(2026-09-15)*
  Un bouton (📦 Export JSON, dans l'en-tête) télécharge le document Firestore complet en un
  seul fichier JSON : sessions, temps, check-ins, niveaux auto-évalués, registres de
  faiblesses, jalons et leur état, instantanés du sphérier. Voir DECISIONS.md.

- [ ] **Ajouter `schemaVersion` aux documents Firestore**, avec lecture rétrocompatible.

## Phase 1 — Évaluation

- [x] **Auto-évaluation mensuelle** du niveau de chaque branche.

- [x] **Registre des faiblesses** par branche : liste courte, éditable, révisée à
  l'auto-évaluation.

- [x] **Jalons par preuve** : liste de jalons concrets cochables par branche.
  Les premiers jalons sont à rédiger **par Florian**, pas à inventer.

## Phase 2 — Le sphérier

- [x] Remplacer la constellation par un sphérier : un plateau de nœuds reliés,
      inspiré du sphérier de Final Fantasy X.
- [x] Un nœud se débloque UNIQUEMENT en cochant un jalon par preuve. Jamais par
      un seuil d'heures. Le temps investi s'affiche en contexte, il n'achète rien.
- [x] L'adjacence encode les prérequis : un nœud n'est accessible que depuis un
      nœud voisin déjà débloqué.
- [x] Régions distinctes par discipline (Dessin, Peinture, Encrage, Anatomie),
      reliées entre elles à quelques points seulement.
- [x] Les régions sont fixes, les nœuds poussent : chaque rituel de fin de cycle
      greffe les nouveaux jalons à la frontière atteinte. Le plateau n'est
      jamais complet.
- [x] Dormance : un nœud non retravaillé depuis longtemps se ternit, mais reste
      débloqué.
- [x] Instantané mensuel du plateau, archivé et consultable.
- [x] Le plateau est un réseau organique, jamais une liste ni un arbre linéaire.
      Les nœuds sont répartis dans un espace 2D avec des positions irrégulières,
      des courbes, des boucles qui se referment et des embranchements.
- [x] Chaque discipline forme une grappe distincte, reliée aux autres par
      quelques ponts seulement.
- [x] Rendu en SVG, avec des chemins courbes entre les nœuds, pas des segments
      droits.
- [x] Trois états visuels distincts : verrouillé (sombre, contour seulement),
      disponible (contour lumineux, pulsation légère), débloqué (rempli, halo).
- [x] Zoom et déplacement à la souris et au doigt.
- [x] Respecter la palette existante de l'app : fond sombre, accents jaune,
      vert et violet. Police Bebas Neue pour les titres, Space Mono pour les
      libellés.
- [x] Référence d'intention : le sphérier de Final Fantasy X. Ne pas copier son
      graphisme, s'en inspirer pour la structure en réseau.

## Phase 3 — Taxonomie et migration

- [ ] **Implémenter le modèle Domaine → Discipline → Médium** (voir CLAUDE.md §4).

- [ ] **Migrer les catégories plates existantes** vers la nouvelle structure.
  Mapping explicite, écrit dans la PR, validé par Florian avant merge.
  *Aucune minute de pratique ne doit disparaître.* Tester sur une copie d'abord.

- [ ] **Adapter les trois méthodes d'entrée** (ajout rapide, chrono, manuel) à la nouvelle
  taxonomie, sans ajouter d'étape à l'ajout rapide.

- [ ] **Ajouter le domaine Corps** : Course, Gym, Randonnée, Autre. Temps uniquement.

## Phase 4 — Qualité de pratique

- [ ] **Marqueur Délibéré / Kilométrage** sur chaque session de Pratique visuelle.
  Un seul geste. Deux boutons, pas un écran.

- [ ] **Séparer les totaux** dans les stats : temps total, dont temps délibéré.

- [ ] **Boucle de feedback du Coach** après une session marquée Délibéré : deux questions
  courtes, deux phrases de réponse.

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

- [ ] Le contenu du cycle 2 est défini dans CYCLE-2.md. L'intégrer à l'app
      lorsque le contenu des semaines sera sorti du code.

## Idées non validées

À ne pas implémenter sans approbation explicite de Florian.

- [ ] Vue historique / graphique du temps par semaine.
- [ ] Import de références ou de photos de travaux dans les jalons.

## Fait

- [x] **Import du registre des faiblesses de CYCLE-2.md dans l'app.** *(2026-09-15)*
  Les trois entrées du registre au 14 septembre 2026 (insertions musculaires, volume des
  masses, mains évitées) sont importées une seule fois dans le registre de la branche
  Anatomie. L'app est désormais la source de vérité de ce registre ; CYCLE-2.md garde les
  trois lignes comme instantané historique, avec une note à cet effet. Voir DECISIONS.md.

- [x] **Bug : le hub d'une région s'affichait « Débloqué » sans aucun jalon coché.**
  *(2026-09-15)*
  Le hub ANATOMIE apparaissait rempli et lumineux (aspect « Débloqué ») alors qu'aucun de ses
  7 jalons n'était coché, contredisant la règle affichée dans l'interface. Le hub n'avait
  aucune logique d'état ; il utilise désormais l'aspect « Disponible » tant qu'aucun jalon de
  sa branche n'est coché, et ne passe à « Débloqué » qu'une fois une preuve réelle enregistrée.
  Voir DECISIONS.md.

- [x] **Domaine personnalisé + dépendance de jalon.** *(2026-09-15)*
  Demande directe de Florian, hors ordre des phases. Un domaine peut être ajouté depuis
  l'Évaluation (nom + couleur d'accent), avec suppression confirmée qui prévient de la perte
  des jalons et de l'historique de niveau. Chaque jalon porte un champ optionnel « dépend de »
  (liste déroulante des jalons de sa branche) que le sphérier utilise pour l'adjacence, avec
  interdiction des dépendances circulaires. Voir DECISIONS.md.

- [x] **Bug : compteur « jours complétés N/84 » très en dessous de l'activité réelle.**
  *(2026-09-14)*
  Affichait 13/84 alors que l'app enregistrait 91 jours actifs et 248 h. Le compteur ne
  comptait que les confirmations explicites du Check-in, en ignorant les sessions ajoutées
  par les deux autres méthodes de tracking. Corrigé côté affichage uniquement, aucune donnée
  modifiée. Voir DECISIONS.md.

- [x] **Bug : les jours validés ne se réinitialisent pas chaque semaine.** *(2026-09-13)*
  Les jours cochés restaient verts d'une semaine à l'autre. Corrigé en indexant le marqueur
  de jour validé sur la date calendaire réelle plutôt que sur (position dans la semaine +
  numéro de semaine manuel). Voir DECISIONS.md.
