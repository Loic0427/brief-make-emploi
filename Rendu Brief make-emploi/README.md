# Automatisation de la Recherche d'Emploi Data Analyst avec Make & l'API JSearch

**Auteur** : Loïc  
**Projet** : Brief Simplon — Automatisation No-Code d'une veille d'emploi Data Analyst  
**Outils** : Make.com · API JSearch (OpenWeb Ninja) · Google Sheets · Discord Webhooks  
**Lien Google Sheet (Lecture seule)** : [Google Sheet — Suivi candidatures Data Analyst](https://docs.google.com/spreadsheets/d/1fN_T5-ckxZtmThjbgxEAyIvLhVMCMxNGJciKwQqqbn0/edit?usp=sharing)  
**Dépôt GitHub** : `recherche-emploi-make-loic`

---

## 1. Objectif du projet et Contexte métier

Dans le cadre de la formation Data Analyst, la recherche d'un stage ou d'une alternance est une démarche stratégique qui débute plusieurs mois avant la fin du cursus. Traditionnellement, ce processus est manuel, chronophage et répétitif : consultation hebdomadaire de multiples plateformes (LinkedIn, Indeed, France Travail, Apec, etc.), ressaisie manuelle dans des tableaux disparates et risque fréquent d'oubli ou de doublon.

L'objectif de ce projet est d'**appliquer les compétences fondamentales de la data (collecte, nettoyage, structuration et diffusion)** à sa propre recherche d'emploi grâce à une solution automatisée no-code :
- **Automatiser** la collecte hebdomadaire des offres d'emploi ciblées ("Data Analyst" sur le bassin d'emploi de Lille et métropole) ;
- **Nettoyer et homogénéiser** les données issues du format brut JSON ;
- **Centraliser et historiser** les annonces dans un tableau de bord Google Sheets prêt à l'emploi ;
- **Diffuser instantanément** une alerte synthétique et actionnable dans un salon privé Discord ;
- **Garantir un coût de fonctionnement nul (0 €)** en respectant strictement les paliers gratuits des services (Make et JSearch) et les bonnes pratiques de sécurité (aucun secret exposé).

---

## 2. Outils et Technologies utilisés

| Outil / Service | Rôle dans l'architecture | Justification |
| :--- | :--- | :--- |
| **Make (Make.com)** | Orchestrateur d'automatisation no-code | Permet de connecter des briques hétérogènes (API REST, tableur, messagerie), de planifier l'exécution et de manipuler les structures de données (boucles, filtres, transformations). |
| **API JSearch (OpenWeb Ninja)** | Source de données (Collecte d'offres) | Agrège les offres indexées par le moteur Google pour l'emploi (Indeed, LinkedIn, Glassdoor, Welcome to the Jungle, Apec, etc.). Offre gratuite de 200 requêtes/mois sans carte bancaire. |
| **Google Sheets** | Entrepôt de stockage et suivi | Base de données tabulaire accessible, permettant de séparer les données injectées automatiquement par Make (à gauche) des colonnes de gestion manuelle du candidat (statut, relances, notes à droite). |
| **Discord (Webhooks)** | Canal d'alerte et notification push | Diffusion en temps réel d'un flux d'alertes enrichies sur smartphone ou ordinateur, avec liens cliquables directs vers les formulaires de candidature. |

---

## 3. Architecture et Rôle de chaque module du scénario Make

Le flux Make (`Offre_emploi_data`) s'articule autour de 4 modules séquentiels :

```text
[1. HTTP: Make a request]  --->  [2. Flow Control: Iterator]  --->  [4. Google Sheets: Add a Row]  --->  [6. HTTP: Discord Webhook]
   (GET API JSearch)               (Boucle sur jobs[])                (Insertion ligne tableau)            (POST Message alerte)
```

### Module 1 : HTTP — Make a request (Appel API JSearch)
- **Rôle** : Interroge le guichet de l'API REST JSearch pour récupérer la liste des offres d'emploi répondant aux critères configurés.
- **Paramètres techniques** :
  - *Method* : `GET`
  - *URL* : `https://api.openwebninja.com/jsearch/search-v2`
  - *Authentication* : `No authentication`
  - *Headers* : `x-api-key: ***` (la clé personnelle est passée dans l'en-tête HTTP afin de ne jamais l'exposer dans l'URL).
  - *Parse response* : `Yes` (permet à Make de transformer immédiatement le flux texte JSON en pastilles de données utilisables).

### Module 2 : Flow Control — Iterator (builtin:BasicFeeder)
- **Rôle** : Décompose le tableau global renvoyé par l'API en unités de traitement individuelles (bundles).
- **Entrée** : `{{1.data.data.jobs}}`
- **Fonctionnement** : L'API renvoie un bundle unique contenant un tableau `jobs[]` de 10 annonces. L'Iterator transforme ce tableau en 10 exécutions distinctes pour les modules suivants, évitant tout codage manuel de boucle `for`.

### Module 4 : Google Sheets — Add a Row
- **Rôle** : Insère chaque offre traitée par l'Iterator dans une nouvelle ligne de la feuille de calcul `Suivi candidatures Data Analyst`.
- **Nettoyage et formatage appliqués** :
  - *Date de publication* (Col A) : `{{formatDate(2.job_posted_at_datetime_utc; "DD/MM/YYYY")}}` pour un affichage standardisé jour/mois/année.
  - *Intitulé du poste* (Col B) : `{{2.job_title}}`
  - *Entreprise* (Col C) : `{{ifempty(2.employer_name; "Non communiqué")}}` (gestion des valeurs manquantes).
  - *Lieu* (Col D) : `{{ifempty(2.job_location; "Non communiqué")}}` (gestion des valeurs manquantes).
  - *Type de contrat* (Col E) : `{{2.job_employment_type}}` (CDI, CDD, Stage, etc.).
  - *Site d'origine* (Col F) : `{{2.job_google_link}}` (page source Google Jobs).
  - *Lien direct pour postuler* (Col G) : `{{2.job_apply_link}}` (lien d'accès direct au formulaire de candidature).
  - *Colonnes de suivi manuel* (Col H à J) : `statut`, `date de candidature`, `Retour` (laissées intentionnellement vides dans Make pour être complétées par le candidat).

### Module 6 : HTTP — Make a request (Notification Discord)
- **Rôle** : Envoie une notification instantanée dans le salon Discord `#offres-data` pour chaque nouvelle offre insérée.
- **Paramètres techniques** :
  - *Method* : `POST`
  - *URL* : `***` (URL sécurisée du webhook Discord).
  - *Body content type* : `multipart/form-data`
  - *Champ textuel `content`* : message formaté reprenant la date, le titre, la ville et le lien direct vers l'annonce.
  - *Exemple de contenu transmis* :  
    `{{formatDate(2.job_posted_at_datetime_utc; "DD/MM/YYYY")}} --- {{2.job_title}} ---- {{2.job_location}} --- {{2.job_apply_link}}`

---

## 4. Critères de recherche choisis et Justification

Les paramètres de la requête HTTP vers JSearch ont été calibrés de façon précise :

| Paramètre Query | Valeur retenue | Justification métier et technique |
| :--- | :--- | :--- |
| `query` | `data analyst lille` | Cible explicitement le poste visé (Data Analyst) et le bassin d'emploi régional immédiat (Lille et métropole lilloise). Peut être étendu si besoin à `alternance data analyst lille` ou `stage data analyst lille`. |
| `country` | `fr` | Filtre les offres situées sur le territoire français afin d'exclure les annonces homonymes à l'international. |
| `language` | `fr` | Retient en priorité les annonces rédigées en français. |
| `date_posted` | `week` | Restreint la collecte aux annonces publiées au cours des 7 derniers jours. Combiné à une planification hebdomadaire, ce paramètre assure une fraîcheur optimale et limite naturellement l'apparition de doublons. |
| `num_pages` | `1` | Récupère la première page de résultats (environ 10 offres pertinentes). Permet de maîtriser la consommation des crédits Make par exécution tout en captant l'essentiel du flux hebdomadaire. |

---

## 5. Nettoyage et Préparation des Données (Compétence C7)

Les données brutes retournées par une API externe ne sont jamais directement exploitables telles quelles. Un travail de préparation a été implémenté au niveau du mapping :

1. **Standardisation des dates** :  
   L'API JSearch renvoie un horodatage ISO/UTC brut (ex. `2026-11-08T14:30:00.000Z`). L'application de la fonction Make `{{formatDate(job_posted_at_datetime_utc; "DD/MM/YYYY")}}` garantit un affichage lisible au format français. De plus, la colonne A du Google Sheet a été typée en *Texte brut* (Format > Nombre > Texte brut) afin d'interdire au tableur toute conversion automatique qui inverserait le jour et le mois (confusion fréquente avec le format US MM/DD/YYYY).
2. **Traitement des valeurs absentes (`ifempty`)** :  
   Certaines offres ne renseignent pas explicitement l'employeur (ex. cabinets de recrutement confidentiels) ou la localisation exacte. L'utilisation de `{{ifempty(employer_name; "Non communiqué")}}` et `{{ifempty(job_location; "Non communiqué")}}` prévient les cellules vides inélégantes et garantit l'intégrité visuelle du tableau de bord.
3. **Typologie de contrat et traçabilité de la source** :  
   L'extraction des champs `job_employment_type` et `job_google_link` permet au candidat de savoir immédiatement s'il s'agit d'un stage, d'un CDD, d'une alternance ou d'un CDI, et de vérifier la plateforme d'agrégation d'origine.
4. **Liens directs cliquables** :  
   Le champ `job_apply_link` fournit l'accès direct vers le formulaire de recrutement de l'entreprise, évitant toute recherche manuelle sur les moteurs.

---

## 6. Structure du tableau de bord Google Sheets (Compétence C8)

Le fichier Google Sheet `Suivi candidatures Data Analyst` a été conçu selon une séparation nette entre les données automatisées et le suivi manuel :

```text
[ Colonnes automatisées par Make (A à G) ]            [ Colonnes de suivi manuel (H à J) ]
A: date de publication (JJ/MM/AAAA)                   H: statut (À postuler / En attente / Entretien / Refus)
B: Intitulé du poste                                  I: date de candidature
C: Entreprise                                         J: Retour / Notes
D: Lieu
E: Type de contrat
F: site d'origine (lien Google Jobs)
G: Lien direct de candidature
```

- **Règle d'or respectée** : Les colonnes H, I et J sont totalement ignorées lors de l'insertion dans Make (`values` laissées vides), ce qui évite d'écraser les annotations ultérieures de l'utilisateur.
- **Accès de partage** : Le lien de consultation a été configuré avec les droits "Lecteur" pour tout utilisateur disposant du lien :  
  `https://docs.google.com/spreadsheets/d/1fN_T5-ckxZtmThjbgxEAyIvLhVMCMxNGJciKwQqqbn0/edit?usp=sharing`

---

## 7. Estimation de la Consommation (Quotas Make & API JSearch)

L'un des prérequis stricts du brief est de garantir un fonctionnement pérenne et 100 % gratuit.

### A. Consommation par exécution (pour 10 offres traitées)
- Module 1 (HTTP GET JSearch) : **1 crédit**
- Module 2 (Iterator) : **1 crédit**
- Module 4 (Google Sheets - Add a Row) : **10 crédits** (1 opération par offre)
- Module 6 (Discord Webhook - Make a request) : **10 crédits** (1 message par offre)
- **Total par exécution** : **22 crédits** Make et **1 requête** JSearch.

### B. Consommation mensuelle estimée (Planification hebdomadaire le lundi à 8h00)
- Nombre d'exécutions par mois : **4 exécutions** (4 lundis)
- **Crédits Make mensuels** : 4 × 22 = **88 crédits / mois**
  - *Quota gratuit Make* : 1 000 crédits / mois
  - *Taux d'utilisation* : **8,8 %** (marge de sécurité de plus de 91 %).
- **Requêtes API JSearch mensuelles** : 4 × 1 = **4 requêtes / mois**
  - *Quota gratuit JSearch Basic* : 200 requêtes / mois
  - *Taux d'utilisation* : **2 %** (marge de sécurité de 98 %).

### C. Perspectives d'évolution sans surcoût
Grâce à cette faible consommation, il est possible d'enrichir le scénario :
- Lancer 2 recherches distinctes (ex. `data analyst lille` et `stage data analyst lille`) : consommation estimée à ~176 crédits Make et 8 requêtes JSearch par mois, restant très largement sous les seuils gratuits.

---

## 8. Difficultés rencontrées et Solutions apportées

1. **Sécurisation des identifiants et tokens secrets** :  
   - *Défi* : Ne pas exposer la clé JSearch (`ak_...`) ni le webhook Discord publiquement lors du partage du blueprint et de la documentation.
   - *Solution* : Utilisation d'un header HTTP standard (`x-api-key`) plutôt qu'un paramètre d'URL (qui reste tracé dans l'historique), et nettoyage systématique du fichier blueprint exporté en remplaçant les valeurs sensibles par `***`.
2. **Compréhension de la structure imbriquée du JSON** :  
   - *Défi* : L'API JSearch renvoie les données sous `data.data.jobs[]`. Lors du paramétrage initial, l'Iterator ne voyait pas les champs car aucun test préalable n'avait été exécuté.
   - *Solution* : Exécution unitaire préalable du module 1 (*Run this module only*) afin que Make inspecte le schéma de réponse et expose les pastilles de données au reste du scénario.
3. **Format des dates et interprétation par Google Sheets** :  
   - *Défi* : Inversion jour/mois lors de l'insertion dans le tableur (ex. 04/11 interprété comme le 11 avril).
   - *Solution* : Combinaison de la fonction `formatDate(date; "DD/MM/YYYY")` dans Make et du formatage de la colonne en *Texte brut* dans Google Sheets.
4. **Prévention du Rate Limiting Discord (erreur 429)** :  
   - *Défi* : L'envoi ultra-rapide de 10 requêtes POST consécutives vers l'API de Discord peut temporairement déclencher un code HTTP 429 ("Too Many Requests").
   - *Solution* : En cas de volume élevé, insertion d'un module temporisateur *Tools > Sleep* réglé sur 1 seconde entre l'insertion Sheet et l'alerte Discord.
