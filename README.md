# Projet : Application Web de Service Vélib'

## Sommaire

1. [Description du Projet](#description)
   - [Fonctionnalités principales](#fonctionnalités-principales-de-notre-application)
   - [Défis rencontrés lors de la réalisation](#défis-rencontrés-lors-de-la-réalisation)
2. [Rôle du Script Python](#rôle-du-script-python)
3. [Prérequis](#prérequis)
4. [Installation](#installation)
5. [Explication du Fonctionnement du Script Python](#explication-du-fonctionnement-du-script-python)
   - [Étape 1 : Récupération des données et traitement](#1-récupération-des-données-et-traitement)
     - [Envoi de la requête à l'API](#11-envoi-de-la-requête-à-lapi)
     - [Vérification de la validité de la réponse](#12-vérification-de-la-validité-de-la-réponse)
     - [Extraction des données JSON](#13-extraction-des-données-json)
     - [Filtrage et validation des données](#14-filtrage-et-validation-des-données)
   - [Étape 2 : Retourner les données traitées pour utilisation dans l'application](#2-retourner-les-données-traitées-pour-utilisation-dans-lapplication)
     - [Renvoyer les données formatées](#21-renvoyer-les-données-formatées)
     - [Exposer les données via une API avec Flask](#22-exposer-les-données-via-une-api-avec-flask)
     - [Comment accéder à l'API exposée avec Flask](#23-comment-accéder-à-lapi-exposée-avec-flask)
6. [Usage](#usage)
7. [Informations Importantes à Savoir](#informations-importantes-à-savoir)
   - [Explication de CORS(app)](#explication-de-corsapp)
8. [Conclusion](#conclusion)

## Description

Ce projet consiste en une application web développée pour faciliter l'accès et l'utilisation du service de vélos en libre-service Vélib'. Vélib' est un réseau de stations de vélos électriques et mécaniques, disponible dans la ville de Paris et ses environs.

L'application permet aux utilisateurs de visualiser sur une carte interactive toutes les stations de vélos Vélib', avec des informations détaillées telles que le nom de la station, sa localisation, et sa capacité (nombre de vélos disponibles). L'utilisateur peut interagir avec la carte, consulter les informations spécifiques d'une station, réserver un vélo ou planifier un itinéraire vers une station.

Les données des stations Vélib' sont récupérées en temps réel à partir de l'API publique de Vélib'. Cette API fournit des informations détaillées pour chaque station, telles que l'ID, le nom, la géolocalisation (latitude et longitude) et la capacité. Ces données sont ensuite traitées et formatées pour être affichées dans l'application.

### Fonctionnalités principales de notre application :

- **Visualisation des stations Vélib'** : Une carte interactive affiche toutes les stations de vélos disponibles, avec des informations mises à jour en temps réel provenant de l'API publique de Vélib'.
- **Détails des stations** : En cliquant sur une station, l'utilisateur peut voir des informations comme le nom, la localisation (latitude et longitude), et la capacité de la station.
- **Réservation de vélos** : Les utilisateurs peuvent réserver un vélo à une station donnée.
- **Planification d'itinéraires** : L'utilisateur peut planifier un itinéraire vers une station.
- **Historique des réservations et recherches** : L'application permet aux utilisateurs de créer un compte et consulter leur historique de recherches et réservations.

### Défis rencontrés lors de la réalisation :

L'un des principaux défis lors de la création de cette application a été de récupérer et de traiter les données provenant de l'API Vélib'. En effet, l'API fournit une grande quantité de données pour chaque station, mais celles-ci sont parfois incomplètes ou mal structurées. Le défi consistait donc à comprendre comment extraire et valider les informations pertinentes de manière fiable.<br><br>
Après des recherches approfondies, nous avons opté pour Python comme langage de script pour résoudre ce problème.

## Rôle du script Python

Le script Python joue un rôle central dans le projet en effectuant les étapes suivantes :

1. **Envoi de la requête à l'API** pour récupérer les données des stations Vélib'.
2. **Traitement et validation des données** : Les informations récupérées sont filtrées et structurées selon les besoins de l'application (nom de la station, géolocalisation, capacité).
3. **Renvoi des données formatées** sous forme de JSON vers le frontend, permettant à l'application de les afficher sur la carte interactive.

## Prérequis

- **Python 3.8+** doit être installé.
- Bibliothèques Python nécessaires :
  - `Flask` pour le serveur web.
  - `Flask-CORS` pour la gestion des CORS.
  - `requests` pour les requêtes HTTP.

## Installation

1. **Télécharge le fichier ZIP** contenant le projet.
2. **Décompresse le fichier ZIP** dans un dossier de ton choix.
3. **Accéder au dossier du projet** dans ton terminal :
   ```bash
   cd chemin/vers/le/dossier/du/projet
   ```
4. **Installation des bibliothèques Python** dans ton terminal:
   ```bash
   pip install Flask Flask-CORS requests
   ```
   <br>

# Explication du Fonctionnement du Script Python

Dans cette section, nous allons vous expliquer en détail le fonctionnement du script ligne par ligne ,nous diviserons l'explication en deux étapes clés pour que vous puissiez comprendre facilement comment le code fonctionne.

## 1-Récupération des données et traitement

Pour La première étape il faut se concetrer sur la fonction `get_stations()` qui consiste à récupérer les données des stations Vélib' via une requête API, puis à traiter ces données pour les rendre exploitables dans notre application. Voici les différentes étapes du code :

### 1.1. Envoi de la requête à l'API:

```python
response = requests.get("https://velib-metropole-opendata.smovengo.cloud/opendata/Velib_Metropole/station_information.json")
```

- `requests.get()` : Cette méthode envoie une requête HTTP GET à l'URL de l'API de Vélib' pour récupérer les données des stations de vélos.
- `response` : La variable response contient la réponse du serveur à la requête. Cette réponse contient les informations demandées sous forme de JSON, ou un message d'erreur en cas d'échec.

### 1.2. Vérification de la validité de la réponse:

Après avoir envoyé la requête, nous devons vérifier si la réponse du serveur est correcte (code de statut 200) avant de continuer à traiter les données.

```python
if response.status_code == 200:
```

- `response.status_code` : C'est le code de statut HTTP renvoyé par le serveur. Un code 200 indique que la requête a réussi et que nous pouvons maintenant traiter les données.

### 1.3. Extraction des données JSON:

Une fois que nous avons validé la réponse, nous extrayons les données JSON de la réponse en utilisant la méthode `.json()` :

```python
data = response.json()
```

- `response.json()` : Cette méthode convertit la réponse de l'API (qui est un objet JSON) en un dictionnaire Python que nous pouvons facilement manipuler.

### 1.4. Filtrage et validation des données:

Les données extraites contiennent des informations sur chaque station de vélo. Nous devons les filtrer et nous assurer qu'elles sont dans un format valide avant de les utiliser. Cela se fait avec une boucle et des vérifications de type :

```python
formatted_data = []

        # Filtrage et structuration des données
        for station in data.get('data', {}).get('stations', []):
            # Validation des champs et types requis
            if (
                isinstance(station.get('station_id'), int) and
                isinstance(station.get('stationCode'), str) and
                isinstance(station.get('name'), str) and
                isinstance(station.get('lat'), float) and
                isinstance(station.get('lon'), float) and
                isinstance(station.get('capacity'), int)
            ):
                # Ajouter la station avec les champs formatés dans la liste finale
                formatted_station = {
                    "station_id": station['station_id'],
                    "stationCode": station['stationCode'],
                    "name": station['name'],
                    "lat": station['lat'],
                    "lon": station['lon'],
                    "capacity": station['capacity']
                }
                formatted_data.append(formatted_station)
```

- `data.get('data', {}).get('stations', [])` : Cela permet d'accéder de manière sécurisée aux données des stations dans la réponse JSON. Si les clés n'existent pas, un dictionnaire vide est retourné, évitant ainsi des erreurs.
- **Vérifications de type** : Nous nous assurons que chaque donnée (comme l'ID de station, le nom, la latitude, etc.) est du type attendu en utilisant `isinstance()`.
- **Filtrage**: Si une station est valide, nous extrayons ses informations et les ajoutons à la liste `formatted_data`.

## 2-Retourner les données traitées pour utilisation dans l'application

Jusqu'à présent, nous avons compris comment la fonction `get_stations()` récupère et filtre les données. La question qui se pose maintenant est : comment pouvons-nous récupérer ces données dans notre application web ?

C'est ici que la bibliothèque `Flask` entre en jeu. Flask est un micro-framework pour Python qui permet de créer des API REST.

Une API REST (Representational State Transfer) est un type d'interface qui permet à une application web d'interagir avec un serveur via des requêtes HTTP (GET, POST, PUT, DELETE).

Flask facilite la création de ces API en définissant des "routes" qui répondent à des requêtes spécifiques.

Dans notre cas, `Flask` permet de créer une API qui renvoie les données des stations Vélib' sous forme de JSON. Cela permet à notre application web de récupérer facilement ces données pour les afficher sur la carte interactive. Voici les différentes étapes du code :

### 2.1. Renvoyer les données formatées:

Nous utilisons la méthode `jsonify()` de Flask pour retourner les données traitées au format JSON :

```python
return jsonify(formatted_data)
```

- `jsonify(formatted_data)` : Cette fonction permet de convertir la liste formatted_data en un objet JSON. Ce format est compatible avec le frontend et peut être facilement interprété par JavaScript.

### 2.2. Exposer les données via une API avec Flask:

Une fois que les données sont récupérées et formatées, l'étape suivante consiste à exposer ces données via une API afin que l'application frontend puisse y accéder et les utiliser. Flask facilite cela en permettant de créer facilement des routes qui rendront l'API accessible.

Voici comment cela fonctionne :

```python
@app.route('/stations', methods=['GET'])
def get_stations():
    # Code
    return jsonify(formatted_data)

```

- `@app.route('/stations', methods=['GET'])` : Cette ligne définit une route dans Flask. Elle indique que chaque fois qu'une requête GET est envoyée à l'URL /stations, la fonction get_stations() sera appelée.
- `def get_stations()` : C'est la fonction qui sera exécutée lorsque la route /stations sera demandée. Elle récupère les données, les filtre et les formate avant de les renvoyer.

### 2.3. Comment accéder à l'API exposée avec Flask :

Une fois l'API exposée avec Flask, elle devient accessible sur le serveur local (localhost) à l'adresse suivante : http://localhost:5000.

- `:5000` est le port par défaut que Flask utilise pour servir l'API.\*

Pour récupérer les données exposées par l'API, le frontend utilise une requête HTTP GET.Dans un fichier JavaScript, on peut utiliser la fonction fetch() pour envoyer cette requête à l'URL définie par Flask (ici, /stations).

Voici un exemple de code JavaScript pour récupérer les données de l'API :

```javascript
fetch("http://localhost:5000/stations")
  .then((response) => response.json()) // Convertir la réponse en format JSON
  .then((data) => {
    console.log(data); // Afficher les données dans la console
  })
  .catch((error) => {
    console.error("Erreur lors de la récupération des données:", error);
  });
```

- `fetch('http://localhost:5000/stations')` : Cette ligne envoie une requête GET à l'URL http://localhost:5000/stations. Cela signifie que l'application frontend va chercher les données à l'adresse où l'API Flask est hébergée.
- `.then(response => response.json())` : Lorsque la requête est réussie, la réponse est reçue au format JSON, et nous la convertissons pour pouvoir l'utiliser dans notre application.
- `.then(data => { console.log(data); })` : Ensuite, nous pouvons utiliser les données dans notre application (par exemple, les afficher dans l'interface utilisateur).
- `.catch(error => { console.error('Erreur:', error); })` : Si quelque chose ne va pas avec la requête, une erreur est capturée et affichée dans la console.<br><br>

# Usage

Après avoir téléchargé et extrait le fichier ZIP, vous aurez un dossier structuré de la manière suivante :

```
projetSAE/
├── README.md
├── UsageTest
│   └── testScript.html
└── script.py
```

Pour comprendre comment utiliser le script Python, il vous suffit de vous rendre dans le dossier `UsageTest`, où vous trouverez un fichier `testScript.html`. Ce fichier récupère les données de l'API Flask et les affiche en frontend sous forme de tableau.

Ce test est conçu uniquement pour illustrer comment exécuter le fichier `script.py`.

L'utilisation est simple : il vous suffit d'exécuter la commande suivante dans votre terminal :

```bash
python script.py
```

- `python script.py`: Cette commande va exécuter le script Python et démarrer automatiquement Flask, qui exposera l'API pour que vous puissiez y accéder depuis le frontend.

Si tout fonctionne correctement, vous devriez voir le message suivant dans votre terminal :

![Capture d'écran du terminal](images/flask_python.png)

_Cela signifie que l'API est en cours d'exécution et prête à être utilisée._

- ### Ensuite, pour vérifier si une application web peut réellement accéder à cette API, il suffit de cliquer sur le fichier `testScript.html`. Cela ouvrira une page web qui affichera un tableau contenant les informations récupérées depuis l'API Flask.

- ### Alternativement, vous pouvez également accéder directement à l'API via un navigateur web en vous rendant à l'adresse : `http://localhost:5000/stations`, où vous verrez un fichier JSON affichant toutes les informations des stations.
<br>

# Informations Importantes à Savoir

### Explication de CORS(app) :

- CORS (Cross-Origin Resource Sharing) : Cette ligne est essentielle pour permettre à notre application Flask d'accepter des requêtes provenant de sources différentes (d'un autre domaine).CORS est une politique de sécurité qui, par défaut, empêche le partage de ressources entre différentes origines pour des raisons de sécurité. En activant CORS dans notre application, tu autorises ton API Flask à répondre à des requêtes provenant de différents domaines, comme un front-end hébergé sur un serveur différent de celui de l'API.

Sans CORS, une application front-end exécutée sur un autre domaine serait bloquée lorsqu'elle tente d'effectuer une requête vers ton API Flask.

# Conclusion

La clé de ce script réside dans l'utilisation de Flask, qui nous permet de créer une API flexible capable d'exécuter n'importe quel script Python que nous écrivons. De plus, avec Python, nous disposons de nombreuses fonctionnalités de filtrage et de traitement des données, ce qui facilite grandement le déroulement de notre projet d'application web.

Cette bibliothèque est extrêmement utile pour créer des API qui retournent des données extraites d'autres API publiques. Par exemple, si une API publique renvoie 10 données, mais que nous n'avons besoin que de 5, Flask nous permet de retourner uniquement ces 5 données spécifiques.

En outre, Flask nous permet d'ajouter facilement des entrées dans le frontend et de les intégrer dans le routeur de Flask. Voici un exemple :

```python
@app.route('/station/<int:station_id>', methods=['GET'])
def get_station_info(station_id):
    # Récupérer toutes les stations pour trouver celle qui correspond à station_id
    response = requests.get("https://velib-metropole-opendata.smovengo.cloud/opendata/Velib_Metropole/station_status.json")
    if response.status_code == 200:
    #.....Code
    # Pas de spoiler, on verra ça un peu plus tard ! 😉
```

- `/station/<int:station_id>'`: Ici, nous spécifions un paramètre de type int afin de l'utiliser dans la fonction `get_station_info(station_id)`.

### Flask offre de nombreuses autres fonctionnalités puissantes que nous explorerons plus en détail dans les prochaines étapes.
