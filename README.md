# Brief — Automatiser sa recherche d'emploi Data Analyst avec Make

Brief Data Analyst d'une journée (7 h), individuel et guidé : l'apprenant apprend d'abord Make avec les tutos 1 et 2, puis construit seul un scénario qui récupère chaque semaine les offres « data analyst » de France Travail, les range dans un Google Sheet de suivi de candidatures et envoie une alerte sur Discord. Le tuto 3 sert de corrigé et n'est remis qu'après le rendu.

## Fiche Simplonline

**Titre** (79/100 car.) : Automatiser sa recherche d'emploi Data Analyst avec Make et l'API France Travail

**Description rapide** (~560/900 car.) : Chercher un stage, une alternance ou un emploi prend du temps : mêmes recherches, mêmes sites, chaque semaine. Dans ce brief, vous construisez avec Make un scénario no-code qui interroge l'API de France Travail, récupère les nouvelles offres de data analyst, les range dans un Google Sheet de suivi de candidatures et vous prévient sur Discord. Vous commencez par vous former à Make avec deux tutos guidés, puis vous réalisez le projet en suivant des étapes et des captures d'écran, en particulier pour les appels HTTP.

**Compétences visées** (à caler sur les libellés exacts du référentiel, voir note) :

- **C6 — Collecter des données depuis différentes sources** — Ce qu'elle couvre ici : Appel d'une API authentifiée (jeton OAuth2) avec le module HTTP de Make — Niveau : 2 — Adapter
- **C7 — Nettoyer et préparer les données** — Ce qu'elle couvre ici : Mise en forme des dates, valeurs manquantes, traduction des booléens, construction des liens — Niveau : 2 — Adapter
- **C8 — Stocker les données dans une structure adaptée** — Ce qu'elle couvre ici : Google Sheet de suivi structuré, alimenté automatiquement — Niveau : 2 — Adapter

Niveau 2 partout : les tutos 1 et 2 donnent un exemple complet (veille RSS) que l'apprenant adapte à une nouvelle source, une API, sans corrigé pendant le projet.

Note : le fichier de référence des compétences C1–C19 n'était pas disponible ; les numéros et libellés ci-dessus suivent le découpage du Bloc 2 (C6–C9) et sont à vérifier avant publication.

**Organisation :** individuel · 1 journée (7 h) · prérequis : aucun en programmation.

## Contexte

(~1 500/6 000 car.)

Vous approchez de la fin de votre formation de Data Analyst. Prochaine étape : décrocher un stage, une alternance ou un premier emploi.

Chaque semaine, c'est le même rituel : ouvrir les sites d'offres, taper « data analyst », repérer les nouveautés, noter quelque part celles qui vous intéressent… et oublier à qui vous avez déjà écrit. Vous perdez du temps sur la recherche au lieu de soigner vos candidatures.

Votre formatrice, Léa, vous lance un défi : **appliquer votre métier à votre propre recherche d'emploi**. Un data analyst sait aller chercher des données, les nettoyer et les ranger pour en tirer des décisions. Les offres d'emploi sont des données comme les autres.

Bonne nouvelle : France Travail met ses offres d'emploi à disposition gratuitement, grâce à une **API** accessible depuis le site francetravail.io. Et votre promo utilise déjà **Make**, un outil d'automatisation no-code, ainsi que **Google Sheets** et **Discord**.

Léa attend de vous un outil qui, chaque lundi matin, sans aucune action de votre part :

- récupère les offres « data analyst » publiées depuis une semaine ;
- les range dans un tableau de suivi de candidatures, avec des colonnes que vous compléterez vous-même (statut, date de candidature, notes) ;
- vous envoie une alerte par offre dans un salon Discord privé, avec le lien pour postuler.

Contraintes : pas de budget (offre gratuite de Make), aucun mot de passe ni clé secrète visible dans vos livrables, et un outil qu'une personne qui ne l'a pas construit peut comprendre en lisant votre README.

À la fin de la journée, vous présenterez votre automatisation en 5 minutes, avec un vrai tableau rempli d'offres réelles.

## Phase 0 — Apprendre Make avec les tutos 1 et 2 (2 h)

Avant le projet, vous apprenez Make en construisant une **veille data automatique** pas à pas. Suivez les deux tutos dans l'ordre, sans sauter d'étape : tout ce que vous y apprenez resservira dans le projet.

- **[Tuto 1 — Connecter Google Sheets à Make](https://github.com/gsoulat/brief-recherche-emploi-make/blob/main/ressources/tuto-1-connecter-google-sheets-a-make.pdf)** — Ce que vous apprenez : Créer une connexion (Credentials) entre Make et un service — Resservira dans le projet pour… : La connexion Google Sheets du module Add a Row
- **[Tuto 2 — Construire la veille RSS dans Make](https://github.com/gsoulat/brief-recherche-emploi-make/blob/main/ressources/tuto-2-construire-la-veille-rss-dans-make.pdf)** — Ce que vous apprenez : Créer un scénario, enchaîner des modules, Iterator, filtre, mapping vers Google Sheets, fonctions (`formatDate`, `ifempty`), Run once, planification — Resservira dans le projet pour… : Toute la chaîne : récupérer, boucler, nettoyer, stocker, planifier

**Point de contrôle :** votre scénario « Veille Data RSS » remplit le Google Sheet et il est planifié. Vous savez expliquer avec vos mots ce que sont un **module**, un **bundle**, un **Iterator** et un **mapping**.

> 💡 L'offre gratuite de Make autorise 2 scénarios actifs. Votre veille RSS peut rester active : le scénario du projet sera le deuxième.

## Modalités pédagogiques

(~4 800/6 000 car.)

Travail **individuel**, sur une journée : la phase 0 le matin (2 h), puis le projet en 8 étapes. Chaque étape se termine par un point de contrôle : ne passez pas à la suivante tant qu'il n'est pas atteint. Bloqué plus de 10 minutes ? Demandez à un voisin, puis à la formatrice. Sauvegardez votre scénario souvent (`Ctrl+S` / `Cmd+S`).

- **0. Apprendre Make (tutos 1 et 2)** — Durée : 2 h — Point de contrôle : Veille RSS fonctionnelle
- **1. Ouvrir l'accès à l'API France Travail** — Durée : 30 min — Point de contrôle : Application créée, API Offres d'emploi autorisée
- **2. Préparer Discord** — Durée : 15 min — Point de contrôle : Salon créé, URL du webhook copiée
- **3. Concevoir le Sheet de suivi** — Durée : 15 min — Point de contrôle : En-têtes en ligne 1
- **4. Modules HTTP 1 et 2 (jeton + recherche)** — Durée : 45 min — Point de contrôle : Run once : des offres reçues
- **5. Iterator + Google Sheets** — Durée : 30 min — Point de contrôle : Une ligne par offre dans le Sheet
- **6. Alerte Discord** — Durée : 30 min — Point de contrôle : Une alerte par offre dans le salon
- **7. Tester, planifier, compter** — Durée : 20 min — Point de contrôle : Scénario planifié, crédits estimés
- **8. Documenter et préparer la démo** — Durée : 40 min — Point de contrôle : README et captures floutées

**Étape 1 — Ouvrir l'accès à l'API France Travail**

1. Créez un compte sur **francetravail.io** (bouton « Mon espace », puis création de compte et validation par e-mail).
2. Dans Mon espace, cliquez sur **« Créer une application »** et décrivez votre projet (nom, description, URL : `https://www.make.com`).
3. Copiez immédiatement l'**identifiant client** et la **clé secrète** dans un fichier texte privé : la clé ne s'affiche qu'une fois.
4. Cliquez sur **« Ajouter ma première API »**, ajoutez **Offres d'emploi v2**, puis enregistrez.

Questions guidantes : pourquoi France Travail donne-t-il des clés à une « application » plutôt qu'à une personne ? Que risquez-vous si votre clé secrète se retrouve sur GitHub ?

**Étape 2 — Préparer Discord**

1. Créez un serveur privé à votre nom, puis un salon textuel `offres-data`.
2. Dans les paramètres du salon, onglet **Intégrations**, créez un **webhook** et copiez son URL dans votre fichier privé.

Question guidante : un webhook est une « adresse postale » du salon. Pourquoi faut-il la garder secrète ?

**Étape 3 — Concevoir le Sheet de suivi**

Créez un Google Sheet `Suivi candidatures Data`. Il doit contenir au minimum : la date de publication, l'intitulé, l'entreprise, le lieu, le type de contrat, l'information alternance oui/non, le lien vers l'offre et son identifiant (rempli par Make), puis un statut, une date de candidature et des notes (remplis par vous).

Questions guidantes : pourquoi garder l'identifiant de l'offre alors qu'il n'est pas lisible ? Quelles colonnes Make doit-il remplir, et lesquelles sont pour vous ?

**Étape 4 — Modules HTTP 1 et 2** → suivez le **Zoom sur les modules HTTP** ci-dessous, pas à pas.

**Étape 5 — Iterator + Google Sheets**

Comme dans le tuto 2 : un **Iterator** sur la liste des offres, puis **Google Sheets > Add a Row**. Pour le mapping, ouvrez la sortie du module 2 et repérez dans une offre les champs `dateCreation`, `intitule`, `entreprise > nom`, `lieuTravail > libelle`, `typeContratLibelle`, `alternance` et `id`.

Indices : `formatDate` met la date au format français ; `ifempty` remplace une valeur absente ; `if` transforme un vrai/faux en Oui/Non. Le lien d'une offre est `https://candidat.francetravail.fr/offres/recherche/detail/` suivi de son identifiant.

Questions guidantes : pourquoi certaines offres n'ont-elles pas de nom d'entreprise ? Que se passerait-il dans votre Sheet sans `ifempty` ?

**Étape 6 — Alerte Discord** → voir le Zoom HTTP, partie « Module 5 ».

**Étape 7 — Tester, planifier, compter**

1. Lancez **Run once** et vérifiez le Sheet et le salon Discord.
2. Planifiez le scénario **chaque lundi à 8 h**.
3. Relevez le nombre de crédits consommés par une exécution (historique du scénario) et estimez la consommation sur un mois.

Questions guidantes : pourquoi une exécution par semaine avec « offres des 7 derniers jours » évite-t-elle les doublons ? Combien d'offres par exécution pouvez-vous vous permettre avec 1 000 crédits par mois ?

**Étape 8 — Documenter et préparer la démo**

Rédigez le README, ajoutez des captures en **floutant** clé secrète, identifiant client, URL du webhook et données personnelles, puis préparez une démo de 5 minutes.

## Zoom sur les modules HTTP

Le module **HTTP > Make a request** est le plus délicat du projet : c'est lui qui « parle » directement à une API, sans assistant. Prenez le temps de lire cette partie avant de cliquer.

**Le principe, en une image.** Une API est un guichet. Pour y passer, il faut d'abord montrer sa carte d'identité (identifiant client + clé secrète) pour obtenir un **ticket** valable un moment : le **jeton d'accès**. Ensuite, on présente ce ticket à chaque demande. D'où deux modules HTTP à la suite.

- **URL** — Ce que c'est : L'adresse du guichet — En Python : `url = "https://..."`
- **Method** — Ce que c'est : Le type de demande : POST envoie des infos, GET en demande — En Python : `requests.post(...)` / `requests.get(...)`
- **Body** — Ce que c'est : Ce qu'on envoie avec un POST — En Python : `data={...}`
- **Header** — Ce que c'est : Une étiquette jointe à la demande, ici le jeton — En Python : `headers={"Authorization": ...}`
- **Query parameters** — Ce que c'est : Les critères de recherche, ajoutés à l'URL — En Python : `params={...}`
- **Parse response** — Ce que c'est : Make découpe la réponse en champs utilisables — En Python : `r.json()`

### Ajouter un module HTTP

Cliquez sur **« + »**, choisissez **HTTP**, puis le module **« Make a request »**.

![Choisir le module Make a request](https://raw.githubusercontent.com/gsoulat/brief-recherche-emploi-make/main/images/01-module-make-a-request.png)

### Module 1 — Obtenir le jeton

- **Authentication type** : `No authentication`
- **URL** : `https://entreprise.francetravail.fr/connexion/oauth2/access_token?realm=%2Fpartenaire`
- **Method** : `POST`
- **Body content type** : `application/x-www-form-urlencoded`
- **Parse response** : `Yes`

Commencez par **Authentication type** : on choisit « No authentication » car les identifiants voyagent dans le corps de la demande.

![Authentication type : No authentication](https://raw.githubusercontent.com/gsoulat/brief-recherche-emploi-make/main/images/02-authentication-type.png)

Renseignez ensuite l'URL et la méthode **POST**.

![URL du jeton et méthode POST](https://raw.githubusercontent.com/gsoulat/brief-recherche-emploi-make/main/images/03-url-jeton-post.png)

Dans **Body content**, cliquez quatre fois sur **« + Add a field »** :

- **Field 1** — Name : `grant_type` — Value : `client_credentials`
- **Field 2** — Name : `scope` — Value : `api_offresdemploiv2 o2dsoffre`
- **Field 3** — Name : `client_id` — Value : votre identifiant client (`PAR_…`)
- **Field 4** — Name : `client_secret` — Value : votre clé secrète

![Champs grant_type et scope](https://raw.githubusercontent.com/gsoulat/brief-recherche-emploi-make/main/images/04-champs-grant-type-scope.png)

![Champs client_id et client_secret](https://raw.githubusercontent.com/gsoulat/brief-recherche-emploi-make/main/images/05-champs-client-id-secret.png)

> ⚠️ Ne touchez jamais à la touche **Échap** dans un panneau de module : elle le ferme sans enregistrer. Cliquez sur **Save** en bas du panneau.

### Module 2 — Chercher les offres

- **Authentication type** : `No authentication`
- **URL** : `https://api.francetravail.io/partenaire/offresdemploi/v2/offres/search`
- **Method** : `GET`
- **Headers** : Name `Authorization`, Value `Bearer {{1.data.access_token}}`
- **Parse response** : `Yes`

`{{1.data.access_token}}` signifie « le jeton renvoyé par le module 1 » : tapez-le tel quel, Make le transforme en pastille. Attention à l'espace après `Bearer`.

![URL de recherche des offres](https://raw.githubusercontent.com/gsoulat/brief-recherche-emploi-make/main/images/06-url-recherche-offres.png)

![Header Authorization avec le jeton](https://raw.githubusercontent.com/gsoulat/brief-recherche-emploi-make/main/images/07-header-authorization.png)

Dans **Query parameters**, ajoutez vos critères avec **« + Add a parameter »** :

- **`motsCles`** — Exemple de valeur : `data analyst` — Rôle : Les mots recherchés
- **`departement`** — Exemple de valeur : `31` — Rôle : Le ou les départements visés, séparés par des virgules (ex. 31,81,82)
- **`publieeDepuis`** — Exemple de valeur : `7` — Rôle : Offres des 7 derniers jours
- **`sort`** — Exemple de valeur : `1` — Rôle : Les plus récentes d'abord
- **`range`** — Exemple de valeur : `0-14` — Rôle : Les 15 premières offres

![Paramètres motsCles et publieeDepuis](https://raw.githubusercontent.com/gsoulat/brief-recherche-emploi-make/main/images/08-parametres-motscles-publieedepuis.png)

![Paramètres sort, range et departement](https://raw.githubusercontent.com/gsoulat/brief-recherche-emploi-make/main/images/09-parametres-sort-range-departement.png)

### Tester les deux modules

Cliquez sur **Run once**. Une bulle apparaît au-dessus de chaque module.

![Run once sur les deux modules](https://raw.githubusercontent.com/gsoulat/brief-recherche-emploi-make/main/images/10-run-once-deux-modules.png)

Ouvrez la bulle du module 2 : un **Status Code 206** (ou 200) signifie que tout va bien, et `resultats` contient les offres.

![Sortie du module 2 : les offres reçues](https://raw.githubusercontent.com/gsoulat/brief-recherche-emploi-make/main/images/11-sortie-module-2.png)

- **400 ou 401 sur le module 1** : Identifiant ou clé mal collés (espace en trop), ou scope mal écrit
- **401 sur le module 2** : Header mal rédigé : il faut `Bearer`, un espace, puis la pastille du jeton
- **204 ou `resultats` vide** : Aucune offre pour vos critères : élargissez les mots-clés, les départements ou `publieeDepuis`

### Module 5 — L'alerte Discord

Après le module Google Sheets, ajoutez un troisième **HTTP > Make a request** :

- **Authentication type** : `No authentication`
- **URL** : l'URL de votre webhook Discord
- **Method** : `POST`
- **Body content type** : `multipart/form-data`
- **Field 1** : Name `content`, Field type `Text`, Value = votre message

À vous de composer le message avec les pastilles de l'offre (intitulé, entreprise, lieu, contrat, lien). Astuce : dans Discord, un texte entouré de `**` s'affiche en gras.

![Module Discord : multipart/form-data et champ content](https://raw.githubusercontent.com/gsoulat/brief-recherche-emploi-make/main/images/12-module-discord.png)

## Livrables attendus

(~1 100/3 000 car.)

Un **repo GitHub public** `recherche-emploi-make` contenant :

- `README.md` : description du projet, outils utilisés (Make, API France Travail, Google Sheets, Discord), schéma du scénario (module par module, avec le rôle de chacun), critères de recherche choisis (mots-clés, département(s)), estimation des crédits Make consommés par mois, instructions pour reproduire le projet, auteur ;
- `blueprint.json` : l'export du scénario Make (menu « … » du scénario > Export Blueprint), **après avoir vérifié qu'il ne contient ni clé secrète ni URL de webhook** ;
- un dossier `captures/` : le scénario complet, le Sheet rempli, le salon Discord avec les alertes, la planification, toutes **floutées** (clés, webhook, données personnelles).

Le lien du Google Sheet en lecture seule, à coller dans le README.

## Modalités d'évaluation

(~700/3 000 car.)

- **Démo individuelle de 5 minutes** en fin de journée : lancement du scénario en direct (Run once), puis visite du Sheet et du salon Discord.
- **3 minutes de questions** : rôle de chaque module, ce qu'est un jeton d'accès, ce qui se passerait sans `ifempty`, comment ajouter un département.
- **Revue du repo** par la formatrice avec la checklist de notation.
- Le corrigé (tuto 3) est remis **après** la démo, pour comparer sa solution.

Une compétence est validée quand **100 % de ses critères** sont atteints.

## Critères de performance

(~1 300/3 000 car.)

**C6 — Collecter**

- Le module 1 obtient un jeton d'accès (sortie avec `access_token`).
- Le module 2 renvoie des offres réelles filtrées sur le métier et le ou les départements choisis.
- Le scénario est planifié une fois par semaine.
- L'apprenant explique la différence entre le jeton et la clé secrète.

**C7 — Nettoyer et préparer**

- Les dates sont au format JJ/MM/AAAA.
- Aucune cellule vide pour l'entreprise : une valeur par défaut est utilisée.
- L'information alternance est lisible (Oui/Non), pas vrai/faux.
- Chaque offre a un lien cliquable qui ouvre la bonne annonce.

**C8 — Stocker**

- Le Sheet a une ligne d'en-têtes et une ligne par offre, sans décalage de colonnes.
- Les colonnes de suivi (statut, date de candidature, notes) sont présentes et séparées des colonnes remplies par Make.
- Une alerte Discord est envoyée pour chaque offre ajoutée.

**Transverse**

- Repo GitHub public avec README complet et blueprint.
- Aucune clé secrète, identifiant client ou URL de webhook visible dans le repo ou les captures.

## Ressources

- [Tuto 1 — Connecter Google Sheets à Make](https://github.com/gsoulat/brief-recherche-emploi-make/blob/main/ressources/tuto-1-connecter-google-sheets-a-make.pdf)
- [Tuto 2 — Construire la veille RSS dans Make](https://github.com/gsoulat/brief-recherche-emploi-make/blob/main/ressources/tuto-2-construire-la-veille-rss-dans-make.pdf)
- [francetravail.io](https://francetravail.io) : création du compte, de l'application et documentation de l'API Offres d'emploi
- [Make — documentation de l'app HTTP](https://apps.make.com/http)
- [Discord — Intro to Webhooks](https://support.discord.com/hc/en-us/articles/228383668-Intro-to-Webhooks)
- Corrigé : *Tuto 3 — Recherche de stage, d'alternance ou d'emploi en data avec Make*, remis après la démo.
