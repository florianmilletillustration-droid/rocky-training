# DECISIONS — Rocky Training

Journal des décisions de design et d'architecture. La plus récente en haut.
Format : **date — décision — raison**.

Ne jamais défaire une décision listée ici sans en ajouter une nouvelle qui l'annule
explicitement, avec sa raison.

---

## 2026-09-15 — Le rituel de fin de cycle est implémenté hors ordre des phases, sur demande directe
Phase 5 (Le cycle) vient après les phases 3 (taxonomie) et 4 (qualité de pratique) dans
ROADMAP.md, toutes deux encore entièrement décochées. Florian a demandé cet item précisément,
comme pour les domaines personnalisés/dépendances de jalons du 2026-09-15 plus haut : une
demande directe passe devant l'ordre des phases, qui sert à choisir quoi faire en l'absence
d'instruction explicite, pas à la remplacer.

## 2026-09-15 — Le rituel de fin de cycle réutilise les cartes de domaine de l'Évaluation, avec un préfixe d'id
« Auto-évaluation de tous les domaines » et « révision du registre des faiblesses de chacun »
sont exactement ce que `renderBranchCard` affiche déjà dans l'onglet Évaluation (curseur de
niveau, liste de faiblesses). Plutôt que dupliquer cette UI, le rituel réutilise la même
fonction. Problème concret : les onglets ne sont jamais retirés du DOM (juste masqués en CSS),
donc si le rituel générait des éléments avec les mêmes id que l'onglet Évaluation
(`level-slider-Anatomie`, etc.), les deux copies coexisteraient et
`document.getElementById` renverrait systématiquement la première trouvée — un curseur modifié
dans l'écran du rituel aurait pu silencieusement enregistrer la valeur restée dans l'onglet
Évaluation. `renderBranchCard` accepte donc un `idPrefix` optionnel (vide par défaut, `'ritual-'`
pour l'écran du rituel), et les trois fonctions qui lisent un champ par son id
(`saveLevelAssessment`, `addWeakness`, `addMilestone`) le reçoivent en second argument. Vérifié
par un test qui donne des valeurs différentes aux deux curseurs et confirme que chaque écran
enregistre bien la sienne.

## 2026-09-15 — L'instantané de fin de cycle est forcé, distinct de l'instantané mensuel automatique, jamais un remplacement
`maybeArchiveMonthlySnapshot` n'archive qu'une fois par mois calendaire (déduplication sur
`month`). Le rituel de fin de cycle doit pouvoir archiver un instantané à la demande, y compris
le même mois qu'un instantané automatique déjà pris — les deux racontent des choses
différentes (un relevé de routine vs la clôture délibérée d'un cycle) et aucun ne doit
supprimer ou écraser l'autre. `spherierSnapshots` reste un tableau à qui l'on ajoute
seulement : chaque entrée porte désormais un `type` (`'monthly'` ou `'cycle-end'`, absent sur
les entrées créées avant ce changement — traitées comme `'monthly'` à l'affichage), et les
entrées `cycle-end` portent en plus le numéro du cycle qui vient de se terminer. La liste des
instantanés trie maintenant sur la date exacte plutôt que sur le mois, pour ordonner
correctement deux entrées tombées le même mois, et affiche un libellé « 🎓 Fin du cycle N »
distinct pour les secondes.

## 2026-09-15 — Le numéro de cycle s'affiche en petit sous « Semaine actuelle », sans changer son format
ROADMAP.md prévoit séparément que « le compteur devient « Cycle N · Semaine X/12 » » (item non
coché de Phase 5, toujours en attente). Cette PR introduit `cycleNumber` comme donnée — il faut
bien qu'il soit visible quelque part, sans quoi ce serait un champ suivi mais jamais montré —
mais reformater le compteur principal est un changement de mise en page distinct, non demandé
ici (CLAUDE.md, anti-objectifs : « changer la mise en page sans demande explicite »). Une
simple ligne « Cycle N » ajoutée sous la valeur existante rend le nombre visible sans toucher
au format ni à la disposition de la carte.

## 2026-09-15 — « Jours complétés N/84 » renommé « Jours pratiqués » : pas de bug, deux notions fusionnées
Diagnostic demandé sur le compteur corrigé le 2026-09-14. Ce correctif avait remplacé
`completedDays.length` par `size(completedDays ∪ dates(allSessions))`, plafonné à 84, pour
arrêter d'ignorer les trois quarts des méthodes de tracking. Résultat du diagnostic : la
formule n'a pas de bug arithmétique, mais elle mélange maintenant deux notions distinctes qui
étaient jusque-là séparées à raison :

1. **Jour du programme confirmé** — `completedDays` seul, alimenté uniquement par la case
   Check-in (« exercice principal fait ») ou le bouton « Marquer ce jour comme complété » :
   un signal explicite et volontaire, indépendant de toute session enregistrée.
2. **Jour actif** — une date où *n'importe quelle* session existe, dans *n'importe quelle*
   catégorie, y compris sans rapport avec le programme (ex. une session Gouache un jour où
   l'exercice du programme portait sur la perspective). C'est exactement ce que mesurent déjà,
   séparément, « Jours actifs (30 derniers) » et « Jours actifs total ».

Le correctif du 09-14 a uni ces deux notions pour corriger un sous-comptage réel, mais le
libellé « Jours complétés » n'a pas suivi : il continue d'affirmer que le jour précis du
programme a été honoré, ce qui n'est plus garanti (un jour peut désormais compter comme
« complété » sur la seule foi d'une pratique complètement étrangère à l'exercice du jour).

Revenir à `completedDays` seul réintroduirait le sous-comptage du 09-14 (13/84 alors que 91
jours actifs et 248 h étaient déjà enregistrés). Restreindre l'union à une catégorie
« Programme Rocky » ne réglerait rien non plus : le contenu du programme (perspective, croquis,
anatomie...) est en pratique loggé sous ces catégories précises, pas sous une étiquette
dédiée — une session du programme est donc, la plupart du temps, indiscernable d'une session
libre par sa seule catégorie. Il n'existe aujourd'hui aucun signal fiable pour isoler « c'est
l'exercice précis du jour » en dehors du Check-in lui-même, et en ajouter un (marqueur par
session, étape supplémentaire à l'ajout rapide) irait contre CLAUDE.md §5 (« Toute friction
ajoutée ici est une régression »).

**Correction retenue : le libellé, pas la logique.** La carte passe de « Jours complétés » à
« Jours pratiqués », avec une infobulle (`title`) qui précise ce qui est compté et ce qui ne
l'est pas. Le calcul et le plafond à 84 restent inchangés — il continue à avoir un sens propre
(distinct de « Jours actifs total », non plafonné) : une fois le total d'activité réelle au-delà
de 84 jours, les deux compteurs divergent et « Jours pratiqués » se fige à 84/84, marquant que
l'ampleur d'activité du programme a été atteinte au moins une fois par jour sur toute sa durée.

## 2026-09-15 — L'export JSON relit le document Firestore brut plutôt que de le recomposer depuis les variables JS
`exportAllData()` refait un `get()` sur le document Firestore de l'utilisateur au moment du
clic, et télécharge son contenu quasi tel quel (seuls les champs `lastUpdated`, des `Timestamp`
Firestore non sérialisables en JSON, sont convertis en ISO 8601). L'alternative — reconstituer
l'export à partir des variables JS déjà en mémoire (`allSessions`, `window._branches`,
`window._checkins`...) — a été écartée : plusieurs champs du document (`completedDays`,
`weekStartDate`, `totalMinutes`, `streak`) ne sont mirroités dans aucune variable globale, et
toute donnée future ajoutée au document (schemaVersion à venir, Phase 0 aussi) serait invisible
à l'export tant que cette fonction n'aurait pas été mise à jour à la main — exactement le genre
d'angle mort qu'un filet de sécurité avant migration ne peut pas se permettre (CLAUDE.md §9).
Un bouton unique (📦 Export JSON, dans l'en-tête, visible sur tous les onglets puisqu'il
n'appartient à aucun domaine particulier) télécharge un seul fichier
`rocky-training-export-YYYY-MM-DD.json`, horodaté et associé à l'email du compte connecté.

## 2026-09-15 — Le registre des faiblesses de CYCLE-2.md est importé dans l'app, qui en devient la source de vérité
Les trois entrées du « Registre des faiblesses au 14 septembre 2026 » de CYCLE-2.md
(insertions musculaires, volume des masses, mains évitées) vivaient dans un fichier Markdown
alors que CLAUDE.md §6 décrit déjà ce registre comme une donnée de l'app, révisée à
l'auto-évaluation mensuelle et consommée par le Coach (§7). Elles sont importées telles
quelles dans `weaknesses` de la branche Anatomie via `importCycle2WeaknessesOnce`, sur le même
principe que l'import ponctuel des 7 jalons du cycle 2 (décision du 2026-09-15 plus bas) : un
marqueur (`cycle2WeaknessesImported`) empêche toute réimportation, pour qu'une suppression ou
modification faite ensuite par Florian dans l'app ne soit jamais écrasée au rechargement — et,
comme pour les jalons, pour que supprimer entièrement la branche Anatomie ne la ressuscite pas.
CYCLE-2.md garde ces trois lignes mais devient un instantané historique non maintenu ; une note
l'indique explicitement à l'endroit du registre, dans le fichier.

## 2026-09-15 — Le hub d'une région ne prend l'aspect « débloqué » que si un de ses jalons est coché
Bug signalé par Florian : le hub ANATOMIE s'affichait rempli et lumineux (l'aspect « Débloqué »
de la légende) alors qu'aucun de ses 7 jalons n'était coché — en contradiction directe avec la
règle affichée dans l'interface et CLAUDE.md §6 (« seul un jalon coché débloque un nœud »).
Cause : le hub n'avait aucune logique d'état ; son cercle utilisait toujours la couleur
d'accent de la branche comme contour vif (`stroke`), sans jamais distinguer « rien n'a encore
été prouvé » de « au moins un jalon prouvé ». Pour Anatomie, dont la couleur d'accent est
justement le vert utilisé par la légende pour « Débloqué », ce contour permanent se lisait à
tort comme un nœud débloqué.

État retenu pour un hub sans aucun jalon coché : **Disponible** (le même aspect que la
légende « Disponible » — fond sombre, contour et halo dans la couleur de la branche), jamais
Verrouillé ni Débloqué. Deux raisons :
- **Jamais Verrouillé** : le hub n'est pas un jalon (DECISIONS.md du 2026-09-15 ci-dessous) et
  n'a pas de prérequis — c'est le point d'entrée de la région, toujours accessible pour
  consulter la branche et cocher son premier jalon. Le griser comme un jalon verrouillé
  suggérerait à tort qu'il faut « débloquer » la région elle-même.
- **Jamais Débloqué par défaut** : lui donner cet aspect sans preuve romprait la même règle
  que le bug signalé, quelle que soit la couleur de la branche.

Un hub ne prend donc l'aspect Débloqué (rempli, halo, ou terni s'il est en dormance) qu'à
partir du moment où au moins un jalon de sa branche est coché — la dormance elle-même ne
s'applique plus qu'à cet état (rien coché = rien à oublier, CLAUDE.md §6). Le chiffre de
niveau affiché au centre passe en texte sombre sur fond rempli une fois débloqué, pour rester
lisible sur un cercle plein plutôt que sur un contour.

## 2026-09-15 — Le jalon « dépend de » remplace le verrouillage séquentiel implicite, avec migration rétrocompatible
Chaque jalon porte désormais un champ optionnel `dependsOn` (l'id d'un autre jalon de la même
branche) choisi dans une liste déroulante, plutôt que de dépendre toujours et uniquement du
jalon précédent dans la liste. Un jalon sans dépendance est disponible d'emblée — c'est la
règle demandée par Florian, et elle vaut pour tout nouveau jalon créé sans choisir de
dépendance. Problème : les jalons existants (les 7 d'Anatomie, et tout jalon déjà ajouté par
Florian dans une autre branche) reposaient jusqu'ici sur ce verrouillage séquentiel implicite
sans aucun champ explicite ; leur appliquer la nouvelle règle telle quelle aurait débloqué
d'un coup tous les jalons existants, une régression silencieuse du sphérier. `ensureBranchDefaults`
migre donc chaque jalon dont `dependsOn` est encore `undefined` (jamais migré) vers une
dépendance explicite sur le jalon précédent de sa branche — `null` pour le premier — ce qui
reproduit exactement l'ancien comportement. Une valeur `null` explicite n'est plus jamais
réécrite : c'est le signal « sans dépendance, disponible d'emblée » que la migration ne doit
pas toucher. Les dépendances circulaires (directes ou en remontant la chaîne) sont refusées
(`wouldCreateMilestoneCycle`), sans quoi deux jalons pourraient s'entre-verrouiller
définitivement. Le sphérier dessine désormais le lien de chaque nœud vers le nœud dont il
dépend (ou vers le hub de sa branche si aucune dépendance), au lieu de toujours relier au
nœud précédent dans l'ordre d'ajout — la position des nœuds, elle, ne change pas (toujours
en chaîne rayonnante depuis le hub), seul le lien affiché reflète la vraie dépendance.

## 2026-09-15 — Domaines ajoutés depuis l'Évaluation : ordre persisté séparément, couleur libre, suppression jamais réinjectée
Florian peut désormais ajouter un domaine (nom + couleur d'accent au choix, via un simple
`<input type="color">`) depuis l'onglet Évaluation ; il apparaît aussitôt dans l'Évaluation et
le sphérier comme les quatre domaines existants. L'ordre d'affichage vit dans un nouveau champ
`branchOrder` (tableau de noms) séparé de `branches` (l'objet clé→données), parce que l'ordre
des clés d'un objet Firestore n'est pas garanti — s'appuyer sur `Object.keys(data.branches)`
aurait pu réordonner les domaines de façon imprévisible d'une lecture à l'autre. Un document
antérieur à cette fonctionnalité (sans champ `branchOrder`) est initialisé avec les quatre
domaines par défaut, comme avant. Une fois `branchOrder` présent, il fait foi tel quel : si
Florian supprime un domaine (y compris un des quatre par défaut — la suppression demande une
confirmation explicite qui rappelle que les jalons et l'historique de niveau associés seront
perdus, CLAUDE.md §9), `ensureBranchDefaults` ne le réinjecte plus jamais automatiquement au
chargement suivant. Les instantanés mensuels déjà archivés listent toujours les domaines tels
qu'ils existaient au moment de l'instantané (`Object.keys(s.branches)`), pas la liste actuelle
— un domaine supprimé depuis reste visible dans son historique, cohérent avec « la vraie
mesure de progrès sur un an » (CLAUDE.md §6).

## 2026-09-15 — Les domaines ajoutés par Florian n'ont pas d'ancre de sphérier dessinée à la main
Les positions des quatre hubs de région (`BRANCH_HUBS`) sont réglées à la main pour former le
« cœur compact et asymétrique » voulu par les décisions précédentes. Un domaine créé depuis
l'Évaluation n'a pas ce traitement manuel : son hub est calculé en spirale (pas à pas selon
l'angle doré, rayon croissant) autour du centre du cœur existant, ce qui le place à l'écart des
quatre régions fixes sans jamais les recouvrir, quel que soit le nombre de domaines ajoutés.
Cette position n'est pas mise en cache — elle dépend du rang du domaine parmi les domaines
custom actuels, qui peut changer si un domaine est supprimé, et la recalculer à chaque rendu
est plus sûr que de risquer une position figée obsolète. Aucun cadrage manuel n'est nécessaire
non plus : `fitSpherierView` mesure déjà l'étendue réelle de tous les nœuds à chaque rendu
(décision du 2026-09-15 ci-dessous) et cadre la vue en conséquence, spirale comprise. Les ponts
entre régions (`BRANCH_BRIDGES`, statique) deviennent `computeBridges()` : chaque domaine est
relié au suivant dans l'ordre d'affichage, la boucle se refermant sur le premier — ce qui
reproduit exactement l'ancienne boucle Dessin—Peinture—Encrage—Anatomie—Dessin et s'étend
automatiquement à tout domaine ajouté ou retiré.

## 2026-09-15 — Le zoom par défaut du sphérier est calculé, pas une valeur fixe
Les nœuds poussent (nouveaux jalons à chaque cycle), donc l'espace qu'ils occupent
n'est pas une constante : des coordonnées de plateau figées auraient fini par
déborder du cadre dès qu'une branche gagne assez de jalons, comme c'est arrivé avec
les 7 jalons d'Anatomie qui dépassaient le cadre initial. Le sphérier mesure
maintenant à chaque rendu l'étendue réelle de tous les nœuds et choisit le zoom
arrière minimal qui les fait tenir entièrement au chargement, plutôt que de
recalculer à la main un cadre à chaque ajout de jalon. Les quatre régions ont aussi
été resserrées et les ponts raccourcis pour réduire le vide central signalé par
Florian.

## 2026-09-15 — Un nœud du sphérier, c'est un jalon ; les hubs de région ne sont pas des jalons
Chaque jalon par preuve d'une branche (Phase Évaluation) devient un nœud du sphérier. Les
quatre régions (Dessin, Peinture, Encrage, Anatomie) reçoivent en plus une ancre fixe — le
« hub » — qui porte le niveau auto-évalué et le nom de la branche, mais qui ne se
verrouille/débloque jamais lui-même : ce n'est pas un jalon, juste le point d'où la chaîne
de jalons de la région part et d'où partent les ponts vers les régions voisines.

## 2026-09-15 — La chaîne de jalons de chaque région suit l'ordre réel du cycle, pas une forme inventée
Les jalons d'Anatomie (S6 à S12) ont un ordre de prérequis réel posé dans CYCLE-2.md : le
torse avant le bassin, la perspective 2 points avant la 3 points, etc. Le sphérier respecte
cet ordre tel quel plutôt que d'inventer des embranchements artificiels pour faire joli —
inventer de faux prérequis aurait menti sur la vraie dépendance entre les exercices.
L'exigence « réseau organique, jamais une liste » est satisfaite autrement : les positions
des nœuds suivent une courbe irrégulière (jamais alignées en droite), le rendu est en
chemins SVG courbes, et le plateau complet boucle et branche à l'échelle des quatre
régions (chaque hub relie sa propre chaîne à deux ponts vers ses voisins, et les quatre
ponts referment une boucle Dessin—Peinture—Encrage—Anatomie—Dessin).

## 2026-09-15 — La dormance visuelle utilise le même mapping provisoire branche → catégories que l'heuristique de temps
Sans la taxonomie Domaine → Discipline → Médium (Phase 3, pas encore faite), il n'existe
aucun lien formel entre une branche du sphérier et les catégories de session existantes
(« Peinture », « Gouache », « Inking », etc.). Un mapping approximatif sert uniquement à
deux affichages cosmétiques sans conséquence sur le déblocage : ternir un nœud débloqué
non retravaillé depuis 28 jours, et afficher le temps investi en contexte au survol d'un
hub. Ce mapping sera remplacé par la vraie taxonomie à la Phase 3 ; en attendant, une
branche jamais pratiquée au sens de ce mapping est traitée comme dormante par défaut,
sans jugement (CLAUDE.md §6 : « ton neutre, pas culpabilisant »).

## 2026-09-15 — Le sphérier garde les couleurs déjà utilisées ailleurs dans l'app, dont le bleu
La consigne visuelle nomme jaune/vert/violet comme accents à respecter. L'app en a un
cinquième déjà en place partout ailleurs (bleu, utilisé par exemple pour les bordures de
session) : Peinture le reprend, en plus de jaune (Dessin), violet (Encrage) et vert
(Anatomie), pour que les quatre régions restent visuellement distinctes plutôt que de
recycler une couleur entre deux régions adjacentes. Lecture à confirmer avec Florian s'il
voulait strictement s'en tenir aux trois couleurs citées.

## 2026-09-15 — Évaluation et sphérier passent avant taxonomie et qualité de pratique
Changement de priorité demandé explicitement par Florian, qui inverse la règle « le
visuel vient en dernier » posée le 12 septembre. Phase 0 (filet de sécurité) reste en
tête et Phase 5 (le cycle) reste en fin de roadmap ; seul l'ordre entre les quatre
phases intermédiaires change. Les items ne sont pas supprimés, seulement renumérotés.

## 2026-09-15 — Les branches de l'évaluation reprennent les régions déjà nommées du sphérier
La Phase Évaluation arrive avant la taxonomie Domaine → Discipline → Médium (Phase 3),
qui n'existe donc pas encore. Plutôt qu'inventer une structure provisoire vouée à être
jetée, les quatre branches évaluées (Dessin, Peinture, Encrage, Anatomie) reprennent
telles quelles les régions déjà décrites en CLAUDE.md §6 et dans la Phase 2 (sphérier).
Quand la taxonomie arrivera, ces branches seront le point de départ des régions du
plateau plutôt qu'un système parallèle à réconcilier.

## 2026-09-15 — Le niveau par branche est une auto-évaluation 1-10, jamais calculé
Un curseur 1-10 rempli à la main par Florian, horodaté à chaque mise à jour. Aucun
calcul à partir du temps ou du nombre de sessions n'entre dans cette valeur — CLAUDE.md
l'interdit explicitement (§6, §9). Les jalons cochés ne modifient pas encore ce chiffre :
tant que le sphérier (Phase 2) n'existe pas, un jalon coché reste une preuve enregistrée
sur sa branche, sans mécanisme de déblocage à réconcilier avec le niveau affiché. Cette
réconciliation (jalon → nœud débloqué) est le travail de la Phase 2, pas de celle-ci.

## 2026-09-15 — Les jalons d'Anatomie sont importés tels quels depuis CYCLE-2.md, les autres branches partent vides
Les 7 jalons du cycle 2 (S6 à S12) sont rédigés par Florian dans CYCLE-2.md ; ils sont
recopiés mot pour mot comme jalons cochables de la branche Anatomie, importés une seule
fois à la première ouverture de l'app après ce changement (pas réimportés si Florian les
modifie ou en supprime ensuite). Dessin, Peinture et Encrage n'ont pas de jalons prédéfinis :
inventer un contenu pour ces branches violerait la règle « les premiers jalons sont à
rédiger par Florian, pas à inventer ». Chaque branche reçoit un champ de saisie pour qu'il
les écrive lui-même quand il sera prêt.

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
