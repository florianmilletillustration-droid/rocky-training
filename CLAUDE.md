# CLAUDE.md — Rocky Training

Mémoire permanente du projet. À lire en entier avant toute modification.
Si une demande entre en conflit avec ce fichier, signale le conflit au lieu de trancher seul.

---

## 1. C'est quoi Rocky Training

Une application web personnelle qui accompagne **Florian** dans sa pratique artistique et
physique. Elle fait trois choses :

1. **Elle porte le Programme Rocky** — 12 semaines (84 jours) de fondamentaux du dessin
   d'imagination.
2. **Elle enregistre la pratique** — temps, discipline, médium, et qualité de la session.
3. **Elle affiche une constellation de compétences** — une carte visuelle du niveau atteint
   dans chaque branche.

Ce n'est pas un produit commercial. C'est un outil personnel utilisé quotidiennement, où la
fiabilité et l'honnêteté des données comptent plus que les fonctionnalités.

---

## 2. Philosophie d'apprentissage

Cette section prime sur les décisions de design. Chaque fonctionnalité doit la servir.

**Le temps n'est pas la compétence.** L'app mesure deux choses séparément : le temps investi
(objectif, automatique) et le niveau atteint (subjectif, auto-évalué périodiquement). La
constellation affiche le **niveau**. Le temps apparaît en dessous, comme contexte.

**La pratique délibérée compte plus que le volume.** Référence : les travaux d'Anders
Ericsson, et non la vulgarisation « 10 000 heures » qu'il a lui-même passé sa carrière à
corriger. La méta-analyse de Macnamara montre que le volume de pratique n'explique qu'une
part modeste des écarts de performance. **Le nombre 10 000 ne doit jamais apparaître comme
objectif dans l'interface.**

**Chaque session porte un marqueur de qualité**, choisi en un geste :
- **Délibéré** — c'était difficile, j'étais à ma limite, je travaillais une faiblesse précise.
- **Kilométrage** — je pratiquais du connu, c'était agréable et utile.

Les deux sont légitimes et comptent dans le temps total. Mais **seule la pratique délibérée
fait progresser une branche.** Si ce marqueur devient une formalité qu'on remplit sans y
penser, tout le reste du système perd son sens.

**L'apprentissage durable exige de l'espacement et de l'entrelacement.** Travailler la même
notion cinq jours de suite donne une sensation de maîtrise qui ne tient pas. D'où la session
d'entrelacement du dimanche (voir §3) et la dormance des branches (voir §6).

**Deux traditions distinctes, assumées comme telles :**
- *Atelier classique* (Watts Atelier) — répétition structurée, geste, anatomie, du
  fondamental vers l'illustration.
- *Dessin d'imagination* (Peter Han, Sorie Kim, Artwood) — construction, exploration, design.

Elles ne tirent pas dans la même direction et ne se pratiquent pas pareil. Elles restent des
branches séparées dans la constellation. Ne jamais les fusionner en un continuum unique.

---

## 3. Le Programme Rocky

**Structure**
- 12 semaines, 84 jours, en 3 mois de progression.
- Un exercice par semaine, travaillé du lundi au vendredi.
- **Samedi : repos automatique.** Non négociable.
- **Dimanche : session d'entrelacement.** Reprendre au hasard deux ou trois notions des
  semaines précédentes plutôt que l'exercice de la semaine courante.
- Compteur affiché : `Semaine X/12` et `N/84` jours.

**Format d'une journée**
- Bloc **45 min — Obligatoire** (jaune).
- Bloc **⭐ Boni 15 min** (vert), clairement séparé et explicitement optionnel.
- Ne jamais fusionner les deux ni rendre le boni obligatoire.

**Contenu**
Géométries dans l'espace, perspectives, warping, construction par primitives, transitions de
formes, texture wrapping, silhouette, analyse de structure, croquis dynamique, design de
masse.

Le détail semaine par semaine vit dans le code. Si tu le modifies, mets ce fichier à jour.

---

## 4. Taxonomie des pratiques

Un seul modèle à trois niveaux, pour tout :

**Domaine → Discipline → Médium / Modalité**

| Domaine          | Discipline        | Médium / Modalité                            |
|------------------|-------------------|----------------------------------------------|
| Pratique visuelle| Dessin            | fusain, graphite, encre, digital             |
| Pratique visuelle| Peinture          | gouache, huile, acrylique, digital           |
| Pratique visuelle| Théorie           | vidéos, cours, lecture                       |
| Corps            | Course            | —                                            |
| Corps            | Gym               | —                                            |
| Corps            | Randonnée         | —                                            |
| Corps            | Autre             | —                                            |

Règles :
- Ajouter une discipline ou un médium est permis.
- **Supprimer ou renommer sans migration testée est interdit.**
- Le domaine **Corps** ne track que du temps. Pas de niveau, pas de constellation, pas de
  jalons, pas de marqueur délibéré. Discipline + durée, point. Ne pas l'enrichir.

---

## 5. Le tracking

Trois méthodes d'entrée, toutes les trois doivent rester disponibles :

1. **Ajout rapide** — boutons +15 / +30 / +45 / +60 / +90 min / +2h. Méthode principale.
2. **Session Training (chrono)** — avec l'avertissement que le chrono se met en pause si on
   change d'onglet.
3. **Ajout manuel** — durée libre + note.

Chaque entrée du domaine *Pratique visuelle* porte : discipline, médium, durée, et le
marqueur **Délibéré / Kilométrage**. Le marqueur doit se choisir en un seul geste, sans
écran supplémentaire. **Toute friction ajoutée ici est une régression.**

---

## 6. Le sphérier

Plateau de nœuds reliés entre eux, inspiré du sphérier de Final Fantasy X. Il montre non
seulement où on en est, mais aussi où on pourrait aller, et encode les prérequis réels du
dessin par l'adjacence — ce qu'une carte à branches ne fait pas.

- **Il affiche le niveau, pas le temps.** Le temps investi apparaît en dessous, comme
  contexte. Il n'achète jamais l'accès à un nœud.
- **Un nœud se débloque uniquement par un jalon par preuve, jamais par un seuil d'heures.**
  « Nature morte au fusain complète en une séance », « mélange de peau à l'huile sans le
  boueux ». Jamais à un total d'heures atteint — sans quoi ce ne serait qu'une barre
  d'expérience déguisée.
- **L'adjacence encode les prérequis.** Un nœud n'est accessible que depuis un nœud voisin
  déjà débloqué.
- **Régions distinctes par discipline** — Dessin, Peinture, Encrage, Anatomie — reliées entre
  elles à quelques points seulement. Les deux traditions du §2 restent des régions séparées,
  jamais fusionnées.
- **Les régions sont fixes, les nœuds poussent.** Chaque rituel de fin de cycle greffe les
  nouveaux jalons à la frontière atteinte. Le plateau n'est jamais complet.
- **Auto-évaluation mensuelle.** Florian note lui-même chaque branche. C'est ce qui alimente
  le registre des faiblesses et les jalons à venir.
- **Registre des faiblesses.** Chaque branche porte une courte liste de ce qui bloque
  actuellement. C'est la cible des sessions délibérées et la matière du Coach. Elle se révise
  à l'auto-évaluation mensuelle.
- **Dormance.** Un nœud non retravaillé depuis plusieurs semaines se ternit visuellement,
  mais reste débloqué. C'est une information sur l'oubli, jamais une réprimande ni un retrait
  d'accès. Ton neutre, pas culpabilisant.
- **Instantané mensuel.** L'état du plateau est archivé chaque mois et consultable. C'est la
  vraie mesure de progrès sur un an.

---

## 7. Le Coach Rocky

Assistant IA intégré, appelé via l'API Anthropic.

- **Tutoie toujours Florian** et l'appelle par son prénom.
- Ton : expert, enthousiaste, direct. Pas corporate, pas condescendant.
- Connaît le contexte du programme et les deux traditions de §2.
- **Boucle de feedback :** après une session marquée *Délibéré*, il pose deux questions
  courtes — quelle était la difficulté précise, et qu'est-ce qui sera tenté différemment la
  prochaine fois. Deux phrases attendues, pas un journal. Il s'appuie sur le registre des
  faiblesses de la branche concernée.
- Il ne félicite pas pour du volume. Il s'intéresse à la difficulté rencontrée.

---

## 8. Architecture technique

- **Un seul fichier HTML standalone** (`index.html`) : HTML + CSS + JS ensemble. Choix
  délibéré, pas une dette. Pas de découpage en modules, pas de build step, pas de framework,
  pas de npm.
- **Firebase** : authentification Google + Firestore.
- **Hébergement** : GitHub Pages, servi depuis le repo.
- La config Firebase web est publique par design, protégée par les règles Firestore et non
  par le secret. Ne pas tenter de l'obfusquer.
- **Règles Firestore** : un utilisateur ne lit/écrit que son propre document
  (`request.auth.uid == userId`). Ne pas assouplir.
- **Versionnement du schéma** : chaque document porte un champ `schemaVersion`. Toute
  migration l'incrémente et sait lire les versions antérieures.

---

## 9. Règles intouchables

1. **Ne jamais détruire de données de pratique.** Le temps déjà enregistré est irremplaçable.
   Toute modification du schéma est rétrocompatible ou accompagnée d'une migration testée,
   précédée d'un export JSON complet.
2. **Ne jamais casser l'app en ligne.** Branche + pull request. Jamais de push direct sur
   `main`.
3. Samedi = repos. Dimanche = entrelacement.
4. Le boni reste optionnel et visuellement séparé.
5. Pas de build step. Le fichier doit rester ouvrable tel quel dans un navigateur.
6. Pas de dépendance nouvelle sans justification écrite dans `DECISIONS.md`.
7. Le marqueur Délibéré/Kilométrage reste à un seul geste.

---

## 10. Anti-objectifs

Refusés délibérément. Ne pas proposer, même en amélioration :

- **Les streaks** (jours consécutifs). Ils font venir pour le compteur, pas pour le travail,
  et poussent à mentir les jours de maladie.
- **Un score global unique** résumant le niveau. Aucun sens pédagogique, blessant les jours
  creux.
- **Le seuil des 10 000 heures** comme objectif affiché.
- **Des niveaux déclenchés par un total d'heures.**
- **Badges, points, classements, comparaison sociale.**
- **Notifications et courriels.**
- Enrichir le domaine Corps au-delà du temps.
- Réécrire l'app « au propre » de sa propre initiative.
- Changer palette, mise en page ou ton sans demande explicite.
- Toucher aux règles de sécurité Firestore.
- Merger sur `main` sans revue humaine.

---

## 11. Comment travailler sur ce projet

1. Lire `CLAUDE.md`, `ROADMAP.md`, `DECISIONS.md`.
2. Prendre le premier item non coché de la roadmap, en respectant l'ordre des phases.
3. Travailler sur une branche `claude/<description-courte>`.
4. Vérifier que la page charge sans erreur console avant de proposer quoi que ce soit.
5. Ouvrir une pull request avec : ce qui a changé, pourquoi, ce qui a été testé, ce qui reste
   incertain.
6. Consigner toute décision de design prise en route dans `DECISIONS.md`, même PR.
7. Cocher l'item dans `ROADMAP.md`, même PR.

**En cas de doute, faire moins et l'expliquer.** Une petite PR claire vaut mieux qu'une
refonte non demandée.

---

## 12. État du projet

`ROADMAP.md` pour ce qui est en cours. `DECISIONS.md` pour l'historique des choix.
