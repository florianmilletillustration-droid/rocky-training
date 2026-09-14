# DECISIONS — Rocky Training

Journal des décisions de design et d'architecture. La plus récente en haut.
Format : **date — décision — raison**.

Ne jamais défaire une décision listée ici sans en ajouter une nouvelle qui l'annule
explicitement, avec sa raison.

---

## 2026-09-14 — Le cycle 2 porte sur l'anatomie, en trois blocs
Cinq semaines en parallèle d'un cours externe qui livre le savoir, trois semaines
de reprise sans le cours pour transformer le savoir en compétence, quatre semaines
d'application de mémoire. Un cours s'arrête quand son contenu s'arrête ; la
rétention se joue après, d'où le bloc de reprise.

## 2026-09-14 — Ordre du bloc 2 : torse, bras, bassin
Ordre choisi par Florian selon ce qui gâche le plus un dessin quand ça rate. Le
bassin remonte avant les jambes parce qu'il porte la jambe et reçoit le torse :
un socle incertain fait chercher le problème au mauvais endroit.

## 2026-09-14 — Les jambes sortent du bloc 2 plutôt que d'ajouter une semaine
Douze semaines est une décision, pas une contrainte. Ajouter une semaine « juste
cette fois » dilue le format. Couvrir moins en profondeur bat effleurer tout.

## 2026-09-14 — Le boni porte les zones écartées du programme principal
Le bloc vert reçoit un thème persistant plutôt qu'un contenu aléatoire : mains,
puis jambes. Rien de ce qui tombe du programme principal n'est complètement
abandonné. Le boni reste optionnel et ne porte aucun jalon — quinze minutes par
jour entretiennent, elles ne prouvent pas.

## 2026-09-14 — « Jours complétés » compte aussi les sessions, pas seulement le Check-in
Le compteur `completedDays.length` n'était incrémenté que par la confirmation explicite du
Check-in (case « exercice principal fait »). Les deux autres méthodes de tracking — ajout
rapide et chrono — n'y touchaient jamais, alors qu'elles sont d'usage courant (§5,
CLAUDE.md : l'ajout rapide est la méthode principale). Résultat concret chez Florian :
13/84 affiché contre 91 jours actifs et 248 h réellement enregistrés. Le compteur affiché
est désormais l'union des jours confirmés par Check-in et des jours où au moins une session
existe, plafonnée à 84. Rien n'est écrit dans Firestore : correction d'affichage seule,
`completedDays` reste tel quel en base.

## 2026-09-13 — Le streak est retiré au profit des jours actifs sur 30 jours
L'app contredisait son propre fichier de règles. Un compteur de jours consécutifs
tombe à zéro pour une maladie et pousse à venir pour le compteur. Les jours actifs
sur une fenêtre glissante mesurent la même régularité sans punir un imprévu.

## 2026-09-13 — Le sphérier remplace la constellation
Une constellation montre où on est ; un sphérier montre aussi où on pourrait aller,
ce qui soutient mieux une pratique sur plusieurs années. Il encode en plus les
prérequis par l'adjacence, ce qui est réel en dessin. Monnaie de déblocage : le
jalon par preuve, jamais l'heure — sans quoi ce ne serait qu'une barre
d'expérience déguisée.

## 2026-09-13 — Le programme boucle en cycles de 12 semaines plutôt que de s'allonger
Un programme plus long ne se commence pas et n'offre aucune fin à célébrer. Douze
semaines restent la bonne unité : assez court pour s'y engager, assez long pour
produire un changement. La pratique ne s'arrête jamais, donc le programme se
relance au lieu de s'étirer.

## 2026-09-13 — Le contenu du cycle suivant naît du registre des faiblesses
La pratique délibérée vise une faiblesse précise. Écrire le cycle 2 d'avance
reviendrait à concevoir le programme d'un dessinateur qui n'existe pas encore.
Le contenu se compose au rituel de fin de cycle, à partir de ce qui bloque
réellement à ce moment-là.

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
