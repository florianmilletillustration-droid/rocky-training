# DECISIONS — Rocky Training

Journal des décisions de design et d'architecture. La plus récente en haut.
Format : **date — décision — raison**.

Ne jamais défaire une décision listée ici sans en ajouter une nouvelle qui l'annule
explicitement, avec sa raison.

---

## 2026-09-13 — Le marqueur « jour validé » est indexé sur la date calendaire réelle
Le bug de réinitialisation hebdomadaire venait d'une clé `day{position}_week{numéro}` :
`numéro` était le compteur de semaine manuel (`_currentWeekNum`, avancé seulement au clic
sur « Semaine suivante »), tandis que l'affichage des boutons de jour calculait toujours le
lundi depuis la date du jour réel. Quand la semaine calendaire changeait avant que le
compteur manuel soit avancé, les anciennes coches de la semaine précédente réapparaissaient
en vert sur la nouvelle semaine. Correctif : le marqueur utilise désormais directement la
date du jour (`YYYY-MM-DD`, comme pour les sessions de pratique) au lieu de la position dans
le programme. Aucune entrée existante n'est supprimée de `completedDays` — les anciennes
clés au format `day{i}_week{w}` restent en base (comptent toujours dans le total `N/84`)
mais ne sont plus utilisées pour l'affichage. Pas de migration nécessaire : changement de
format d'écriture à partir de maintenant, sans effet sur les données déjà enregistrées.

## 2026-09-12 — Le temps et le niveau sont deux mesures séparées
La constellation affiche un niveau auto-évalué, pas un cumul d'heures. Remplir des branches à
l'heure aurait produit une carte des habitudes et non des compétences : on peut accumuler des
centaines d'heures en refaisant ce qu'on sait déjà faire.

## 2026-09-12 — Marqueur Délibéré / Kilométrage sur chaque session
La pratique délibérée prédit la progression bien mieux que le volume. Les deux types comptent
dans le temps total, mais seul le délibéré fait monter une branche. Contrainte : un seul
geste, sinon le marqueur devient une formalité et le système perd son sens.

## 2026-09-12 — Le seuil des 10 000 heures est écarté comme objectif
La formulation populaire est une déformation du travail d'Ericsson, qu'il a lui-même corrigée.
La méta-analyse de Macnamara montre que le volume n'explique qu'une part modeste des écarts de
performance. Afficher un objectif d'heures encouragerait exactement le mauvais comportement.

## 2026-09-12 — Dimanche devient une session d'entrelacement
Le programme est structuré en blocs d'une semaine, ce qui aide à installer la mécanique mais
nuit à la rétention à long terme. Reprendre au hasard des notions antérieures le dimanche
coûte une journée et améliore nettement ce qui reste six mois plus tard.

## 2026-09-12 — Taxonomie unique à trois niveaux : Domaine → Discipline → Médium
Une seule structure de données et une seule interface d'ajout pour le dessin, la peinture, la
théorie et le sport. Permet aussi le zoom de la constellation sans logique parallèle.

## 2026-09-12 — Le domaine Corps ne track que du temps
Pas de niveau, pas de jalons, pas de constellation. L'enrichir reviendrait à maintenir une
deuxième application à l'intérieur de la première.

## 2026-09-12 — Watts Atelier et le dessin d'imagination restent des branches distinctes
L'atelier classique et l'exploration d'imagination sont deux traditions valables mais
divergentes. Les fondre en un seul axe de progression donnerait une mesure incohérente.

## 2026-09-12 — Jalons par preuve plutôt que par heures
Une branche monte quand un résultat concret est atteint, pas à un seuil de temps. Seul ce
mécanisme rend la constellation crédible à celui qui la regarde.

## 2026-09-12 — Dormance plutôt que streaks
Les compteurs de jours consécutifs font venir pour le compteur et poussent à mentir un jour de
maladie. L'estompement d'une branche inutilisée est une information honnête sur l'oubli, sans
jugement.

## 2026-09-12 — Aucun score global unique
Un chiffre résumant le niveau n'a pas de valeur pédagogique et fait mal les jours creux. Des
branches lisibles séparément, oui ; une note d'ensemble, non.

## 2026-09-12 — Instantané mensuel de la constellation
Archiver la forme chaque mois donne une vision réelle du progrès sur un an, là où un compteur
qui monte reflète surtout le temps qui passe.

## 2026-09-12 — Registre des faiblesses par branche
La pratique délibérée exige une cible précise. Ce registre sert d'entrée aux sessions et de
matière au Coach, qui peut alors poser des questions utiles plutôt que féliciter du volume.

## 2026-09-12 — Ordre de chantier : données d'abord, visuel en dernier
Taxonomie et migration, puis qualité de pratique, puis évaluation, puis constellation.
Construire le visuel en premier reviendrait à décorer des données qui ne signifient pas encore
ce qu'on veut leur faire dire.

## 2026-09-12 — Export JSON et `schemaVersion` avant toute migration
La migration des catégories plates est l'opération la plus risquée du projet. Un export
téléchargeable et un schéma versionné sont exigés avant d'y toucher.

## 2026-09-12 — Mise en place de CLAUDE.md / ROADMAP.md / DECISIONS.md
Le projet vivait dans des conversations : le contexte se perdait entre les sessions et le
fichier HTML devait être repartagé chaque fois. La mémoire vit désormais dans le repo.

## 2026-09-12 — Branches + pull requests, jamais de push direct sur `main`
L'app sert de support à une pratique quotidienne. Une régression non détectée coûte des jours
d'entraînement. Toute modification est révisée avant d'être en ligne.

## 2026-05 — Passage de 72 jours à 12 semaines / 84 jours
Restructuration en 3 mois avec un exercice par semaine, plus facile à suivre et laissant le
temps d'assimiler chaque notion.

## 2026-05 — Samedi = jour de repos automatique
Un repos fixe évite la culpabilité et rend le programme tenable sur 12 semaines.

## 2026-05 — Séparation visuelle « 45 min obligatoire » / « boni 15 min »
Le minimum viable doit être évident et atteignable. Le boni reste optionnel pour ne pas
transformer un bon jour en jour raté.

## 2026-05 — Ajout de la catégorie « Vidéos/Cours »
Une partie de l'apprentissage passe par du visionnement et non par de la pratique active.
Contrainte associée : ajouter une catégorie ne doit jamais faire perdre du temps enregistré.

## 2026-05 — Firebase (auth Google + Firestore) plutôt que du stockage local
Les données doivent suivre d'un appareil à l'autre et survivre à un changement de navigateur.

## 2026-05 — Un seul fichier HTML standalone, sans build step
Le projet doit rester modifiable et déployable par une personne seule, sans outillage.

## 2026-05 — « Ajout rapide » comme méthode d'entrée principale
Le chrono se met en pause au changement d'onglet, ce qui le rend peu fiable en usage réel.
