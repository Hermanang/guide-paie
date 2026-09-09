# Guide de la paie sénégalaise dans Odoo

Destiné au gestionnaire de paie. Il couvre le cycle mensuel complet : préparer un salarié,
produire les bulletins, contrôler, corriger, et sortir les états à déposer.

Les congés ne sont pas traités ici : ils fonctionnent comme dans n'importe quelle
installation d'Odoo, et la documentation officielle d'Odoo les couvre. Ce guide ne décrit
que ce qui est propre à la paie sénégalaise.

---

## 1. Préparer un salarié

**Employés → Employés → Nouveau.** Renseigner l'identité, le département et le poste,
puis ouvrir l'onglet **Paie**.

![Onglet Paie](captures/10-onglet-paie.png)

Un mot sur ce que vous voyez en haut du formulaire : la date encadrée et le bouton **+**.
Les conditions d'emploi d'un salarié sont datées. Augmenter un salaire au 1er janvier ne se
fait donc pas en corrigeant la fiche, mais en ouvrant de nouvelles conditions à cette
date : les bulletins déjà émis gardent celles de leur époque, et se recalculent toujours
juste. Le bouton **Historique** montre ces conditions successives.

Quatre informations conditionnent le calcul :

| Information | Ce qu'elle change |
|---|---|
| **Structure de salaire** | *Salarié SN - cadre*, *non-cadre* ou *Stagiaire SN*. Le cadre déclenche le régime complémentaire IPRES. |
| **Catégorie professionnelle** | Doit concorder avec la structure choisie. |
| **Situation de famille** et **enfants à charge** | Déterminent le nombre de parts, donc l'impôt. Les parts se calculent seules et restent modifiables pour les cas particuliers. |
| **Matricules IPRES et CSS** | Sans eux, la déclaration nominative sera incomplète. |

Le salaire de base se saisit dans les informations de rémunération du contrat.

**La date d'embauche n'est pas facultative.** Sans elle, le salarié n'a pas de contrat en
cours, et la section 3 montre ce qu'il advient alors de la paie du mois.

## 2. Le cycle du mois

| | Opération | Où |
|---|---|---|
| 1 | Créer le lot du mois et générer les bulletins | Paie → Lots de bulletins de paie |
| 2 | Saisir les éléments exceptionnels du mois | dans chaque bulletin concerné |
| 3 | Contrôler les montants | Paie → État de paie |
| 4 | Confirmer le lot entier | bouton **Mark As Done** du lot |
| 5 | Éditer les bulletins et sortir les états | Paie → États mensuels et bordereaux |

Les étapes 1 à 3 se refont autant de fois que nécessaire : tant que les bulletins sont en
brouillon, ils ne comptent nulle part. C'est l'étape 4 qui engage — elle confirme,
et elle écrit en comptabilité.

## 3. Produire les bulletins du mois

### La paie du mois, par lot

C'est la voie normale : un lot par mois, tous les salariés dedans.

**Paie → Lots de bulletins de paie → Nouveau.** Nommer le lot — « Paie 09/2026 » —, fixer
la période du 1er au dernier jour du mois, puis **Générer les bulletins de paie**.

Une fenêtre s'ouvre et demande les salariés. **Elle n'en propose aucun** : la liste
s'ouvre vide, rien n'est coché d'avance. Cochez la case en haut de la liste pour les
prendre tous, puis **Générer**.

Quatre choses à savoir, chacune constatée à l'usage :

**Un seul salarié sans contrat en cours fait échouer tout le lot.** Un message d'erreur
apparaît, nommant ce salarié, et rien n'est créé — pas même pour les autres. C'est le cas
d'une fiche sans date d'embauche, ou d'un salarié dont le contrat s'est terminé avant la
période. Retirez-le de la sélection, ou complétez sa fiche.

**Les éléments récurrents du contrat sont repris tout seuls** — sursalaire, prime
d'ancienneté, indemnité de transport. Vous n'avez à saisir que l'exceptionnel du mois.

**« Fermer » ne confirme rien.** Le bouton **Fermer** ferme le lot et laisse les bulletins
en brouillon : ils resteront absents des états, des cumuls, et du calcul de l'impôt du mois
suivant. Ce n'est pas l'action de fin de mois.

**« Mark As Done » est l'action de fin de mois.** Elle recalcule chaque bulletin, les
confirme tous, et **produit les écritures comptables dans la foulée** — une par bulletin.
D'où l'ordre : contrôler avant, jamais après. Ce bouton est le seul de l'écran resté en
anglais.

Un lot ne se supprime qu'en brouillon. Un lot confirmé se défait bulletin par bulletin.

### Un bulletin isolé

**Paie → Bulletins de l'employé → Nouveau**, choisir le salarié et la période. La structure
vient du contrat.

C'est la voie de l'exception : une embauche en cours de mois oubliée du lot, un solde de
tout compte, un rappel. Pour la paie courante, préférez le lot — il garantit que personne
n'est oublié et que tous les bulletins portent la même période.

### Saisir les éléments variables

Dans le bulletin, onglet **Jours travaillés et entrées**. Les codes :

| Code | Rubrique |
|---|---|
| `SURSAL` | Sursalaire |
| `ANC` | Prime d'ancienneté |
| `PRIME` | Primes et gratifications |
| `TRANSPORT` | Indemnité de transport |
| `AVANCE` | Avance sur salaire à retenir |

Après toute saisie, **Calculer la feuille** : une prime ajoutée sans recalcul n'apparaît
pas dans le net. La confirmation du lot recalcule elle aussi, mais contrôler un montant
qu'on n'a pas vu recalculé n'a pas de sens.

### Lire un bulletin

![Détail du calcul](captures/03b-calcul.png)

Onglet **Calcul de salaire** : les gains d'abord, puis le **Total brut**, puis les retenues
salariales en négatif — IPRES, IPM, impôt, TRIMF — et enfin le net. Les charges patronales
figurent plus bas ; elles ne diminuent pas le net.

## 4. Comprendre l'impôt

L'impôt n'est pas recalculé de zéro chaque mois. Il est établi sur la **moyenne des bruts
depuis janvier**, projetée sur l'année, sous déduction de ce qui a déjà été retenu.

Conséquence pratique : le mois qui suit une prime exceptionnelle affiche un impôt plus
faible, parce que la moyenne annuelle redescend. C'est normal, et c'est ce qui évite la
régularisation brutale de décembre.

Une retenue **négative** est possible : c'est un remboursement au salarié.

Ce mécanisme repose sur les mois précédents. Un bulletin de mars resté en brouillon fausse
l'impôt d'avril, sans qu'aucun message ne le signale.

## 5. Contrôler

**Paie → État de paie** ouvre un tableau croisé : salariés en lignes, rubriques en colonnes.

![État de paie](captures/01-etat-de-paie.png)

Il se filtre par période et par département, se regroupe autrement d'un clic, et chaque
montant se creuse jusqu'au bulletin. Le bouton de téléchargement exporte la vue en tableur.

C'est l'outil du contrôle quotidien : vérifier qu'aucune rubrique ne manque, qu'un total
n'a pas dérapé, comparer deux mois.

Le contrôle se fait **avant** de confirmer le lot. Après, il faut passer par la section 6.

## 6. Corriger un bulletin

Ce qui est possible dépend de l'état du bulletin.

| État | Correction |
|---|---|
| **Brouillon** | Modifier, puis **Calculer la feuille**. Rien n'a été engagé. |
| **En attente** | Idem : les boutons **Confirmer** et **Calculer la feuille** restent accessibles. C'est l'état des bulletins repris de l'ancien outil. |
| **Fait** | Le bulletin est confirmé et son écriture est passée. Deux voies, ci-dessous. |

**L'avoir** — bouton **Avoir** sur un bulletin confirmé. Il crée un second bulletin, copie
du premier avec tous les montants en sens inverse, et le confirme aussitôt. Les deux
bulletins subsistent, l'écriture d'origine est neutralisée par une écriture symétrique.
C'est la voie propre : elle laisse une trace, et c'est celle qu'attend un contrôle.

**L'annulation** — bouton **Annuler la fiche de paie**. Le bulletin passe en annulé et son
écriture est annulée. À réserver à l'erreur constatée immédiatement, sur un mois non encore
déclaré.

Dans les deux cas, l'impôt des mois suivants s'appuyant sur les cumuls, un bulletin corrigé
en cours d'année se répercute sur le mois d'après. C'est le comportement attendu.

## 7. Sortir les états du mois

**Paie → États mensuels et bordereaux.** Trois documents, au choix.

![Assistant des états](captures/09-assistant-etats.png)

| Document | À quoi il sert | Échéance |
|---|---|---|
| **État global de paie** | Contrôle interne et archivage : une ligne par bulletin, toutes les rubriques, les totaux | Chaque mois |
| **Bordereau de versement au Trésor** | Accompagne le paiement de l'impôt, de la TRIMF et de la CFCE. Porte les mentions obligatoires et ventile les effectifs par nationalité | **15 premiers jours du mois suivant** |
| **État des cotisations sociales** | Récapitulatif IPRES, CSS et IPM avec les assiettes plafonnées, et la déclaration nominative à reporter sur NDAMLI | Selon l'échéance de la caisse |

Seuls les bulletins **confirmés** sont repris. Si un état paraît incomplet, vérifier qu'il
ne reste pas de brouillon.

Le bulletin remis au salarié s'imprime depuis le bulletin lui-même, ou depuis une sélection
dans la liste : **Imprimer → Bulletin de paie (Sénégal)**. Il porte l'en-tête de
l'entreprise, le détail des rubriques, les cumuls annuels, le compteur de congés et les
cases d'émargement qu'exige l'article L.116 du Code du travail.

## 8. Mettre à jour un barème

**Paie → Configuration → Barèmes Sénégal.**

![Taux et plafonds](captures/05-taux-plafonds.png)

Quatre tables : taux et plafonds, barème de l'impôt, réduction pour charges de famille,
tarifs de la TRIMF. On ne modifie jamais une valeur existante — on **ajoute** une ligne
portant la nouvelle date d'effet, ce qui préserve le recalcul des bulletins antérieurs.

Une marche à suivre détaillée existe par ailleurs, dans le guide de mise à jour des
barèmes.

## 9. Ce qui relève de l'administrateur

**Paramètres → Paie Sénégal** porte les options propres à l'entreprise : taux d'accident du
travail notifié par la CSS, numéros employeur IPRES et CSS, modalités de l'IPM, exonération
éventuelle de CFCE, méthode de calcul de l'impôt, convention collective applicable et
barème de la prime d'ancienneté.

Ces valeurs ne se changent pas sans pièce justificative : elles s'appliquent à tous les
bulletins produits ensuite.

Deux réglages commandent la comptabilité :

**Journal de paie.** Le journal où s'écrivent les écritures. Il est renseigné à
l'installation avec le journal que l'entreprise utilisait déjà pour ses salaires, plutôt
que d'en ouvrir un second qui scinderait l'historique.

**Comptabiliser la paie à partir du.** Les bulletins dont la période s'achève **avant**
cette date sont confirmés sans produire d'écriture. C'est ce qui permet de reprendre
l'historique de l'année — dont la comptabilité est déjà passée dans l'ancien outil — sans
la passer une seconde fois.

> À renseigner **avant** la première confirmation. Le réglage n'agit qu'au moment où un
> bulletin est confirmé : il ne rattrape pas les écritures déjà produites.

Exemple : pour que la paie d'août 2026 soit la première comptabilisée, mettre `01/08/2026`.
Juillet et les mois antérieurs se clôtureront alors sans écriture.

---

## En cas de doute

| Symptôme | Cause la plus fréquente |
|---|---|
| Un état sort vide | Bulletins restés en brouillon, ou période mal bornée |
| L'impôt paraît faux | Nombre de parts, ou bulletin d'un mois antérieur non confirmé — le cumul s'appuie dessus |
| Pas de ligne IPRES complémentaire | Le salarié n'est pas en catégorie *cadre*, ou la structure n'est pas celle des cadres |
| Le net ne correspond pas | Une prime saisie sans code, ou saisie après le calcul : recalculer le bulletin |
| La génération du lot échoue en bloc | Un salarié sans date d'embauche, ou dont le contrat est terminé : le retirer de la sélection |
| Le lot est fermé mais les états sont vides | **Fermer** n'est pas **Mark As Done** : les bulletins sont restés en brouillon |
| Aucune écriture comptable après confirmation | La date « Comptabiliser la paie à partir du » est postérieure à la période du bulletin |
