# Guide de la paie sénégalaise dans Odoo

Destiné au gestionnaire de paie. Il couvre le cycle mensuel complet : préparer un salarié,
produire les bulletins, contrôler, et sortir les états à déposer.

---

## 1. Préparer un salarié

**Employés → Employés → Nouveau.** Renseigner l'identité, le département et le poste,
puis ouvrir l'onglet **Paie**.

![Onglet Paie](captures/10-onglet-paie.png)

Un mot sur ce que vous voyez en haut du formulaire : la date encadrée et le bouton **+**.
Odoo 19 n'a plus de contrat séparé — les conditions d'emploi sont une **version** datée du
salarié. Changer de salaire ou de structure au 1er janvier ne se fait pas en modifiant la
fiche, mais en créant une nouvelle version à cette date : l'historique reste intact et les
bulletins déjà émis continuent de se recalculer avec les conditions de leur époque.
Le bouton **Historique** liste ces versions.

Quatre informations conditionnent le calcul :

| Champ | Effet |
|---|---|
| **Structure de salaire** | *Salarié SN - cadre*, *non-cadre* ou *Stagiaire SN*. Le cadre déclenche le régime complémentaire IPRES. |
| **Catégorie professionnelle** | Doit concorder avec la structure choisie. |
| **Situation de famille** et **enfants à charge** | Déterminent le nombre de parts, donc l'impôt. Les parts se calculent seules et restent modifiables pour les cas particuliers. |
| **Matricules IPRES et CSS** | Sans eux, la déclaration nominative sera incomplète. |

Le salaire de base se saisit dans les informations de rémunération du contrat.

## 2. Produire les bulletins du mois

**Paie → Payslips Batches → Nouveau.** Nommer le lot (« Paie 09/2026 »), fixer la période,
puis générer les bulletins pour les salariés concernés.

Pour un bulletin isolé : **Paie → Employee Payslips → Nouveau**, choisir le salarié et la
période. La structure vient du contrat.

Les primes et retenues du mois se saisissent dans l'onglet **Worked Days & Inputs**, avec
les codes `SURSAL`, `ANC`, `PRIME`, `TRANSPORT` et `AVANCE`.

Cliquer sur **Compute Sheet** pour calculer, puis vérifier l'onglet **Salary Computation**.

![Détail du calcul](captures/03b-calcul.png)

Lecture d'un bulletin : les gains d'abord, puis le **Total brut**, puis les retenues
salariales en négatif — IPRES, IPM, impôt, TRIMF — et enfin le net. Les charges patronales
figurent plus bas ; elles ne diminuent pas le net.

**Confirmer le bulletin** est indispensable : un bulletin en brouillon n'entre ni dans les
états, ni dans les cumuls du mois suivant.

## 3. Comprendre l'impôt

L'impôt n'est pas recalculé de zéro chaque mois. Il est établi sur la **moyenne des bruts
depuis janvier**, projetée sur l'année, sous déduction de ce qui a déjà été retenu.

Conséquence pratique : le mois qui suit une prime exceptionnelle affiche un impôt plus
faible, parce que la moyenne annuelle redescend. C'est normal, et c'est ce qui évite la
régularisation brutale de décembre.

Une retenue **négative** est possible : c'est un remboursement au salarié.

## 4. Contrôler

**Paie → État de paie** ouvre un tableau croisé : salariés en lignes, rubriques en colonnes.

![État de paie](captures/01-etat-de-paie.png)

Il se filtre par période et par département, se regroupe autrement d'un clic, et chaque
montant se creuse jusqu'au bulletin. Le bouton de téléchargement exporte la vue en tableur.

C'est l'outil du contrôle quotidien : vérifier qu'aucune rubrique ne manque, qu'un total
n'a pas dérapé, comparer deux mois.

## 5. Sortir les états du mois

**Paie → États mensuels et bordereaux.** Trois documents, au choix.

![Assistant des états](captures/09-assistant-etats.png)

| Document | À quoi il sert | Échéance |
|---|---|---|
| **État global de paie** | Contrôle interne et archivage : une ligne par bulletin, toutes les rubriques, les totaux | Chaque mois |
| **Bordereau de versement au Trésor** | Accompagne le paiement de l'impôt, de la TRIMF et de la CFCE. Porte les mentions obligatoires et ventile les effectifs par nationalité | **15 premiers jours du mois suivant** |
| **État des cotisations sociales** | Récapitulatif IPRES, CSS et IPM avec les assiettes plafonnées, et la déclaration nominative à reporter sur NDAMLI | Selon l'échéance de la caisse |

Seuls les bulletins **confirmés** sont repris. Si un état paraît incomplet, vérifier qu'il
ne reste pas de brouillon.

## 6. Mettre à jour un barème

**Paie → Configuration → Barèmes Sénégal.**

![Taux et plafonds](captures/05-taux-plafonds.png)

Quatre tables : taux et plafonds, barème de l'impôt, réduction pour charges de famille,
tarifs de la TRIMF. On ne modifie jamais une valeur existante — on **ajoute** une ligne
portant la nouvelle date d'effet, ce qui préserve le recalcul des bulletins antérieurs.

La marche à suivre détaillée est dans `guide_mise_a_jour_baremes.md`.

## 7. Ce qui relève de l'administrateur

**Paie → Configuration → Paramètres** porte les options propres à l'entreprise : taux
d'accident du travail notifié par la CSS, numéros employeur IPRES et CSS, modalités de
l'IPM, exonération éventuelle de CFCE, méthode de calcul de l'impôt.

Ces valeurs ne se changent pas sans pièce justificative : elles s'appliquent à tous les
bulletins produits ensuite.

---

## En cas de doute

| Symptôme | Cause la plus fréquente |
|---|---|
| Un état sort vide | Bulletins restés en brouillon, ou période mal bornée |
| L'impôt paraît faux | Nombre de parts, ou bulletin d'un mois antérieur non confirmé — le cumul s'appuie dessus |
| Pas de ligne IPRES complémentaire | Le salarié n'est pas en catégorie *cadre*, ou la structure n'est pas celle des cadres |
| Le net ne correspond pas | Une prime saisie sans code, ou saisie après le calcul : recalculer le bulletin |
