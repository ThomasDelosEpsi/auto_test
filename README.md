# Cahier des charges – Application d’analyse de données (Régression linéaire) avec API Flask  
Thomas Delos

## Contexte et objectifs

Ce projet vise à construire une application Python complète avec une approche professionnelle combinant :

- Analyse statistique via la régression linéaire.
- Développement d'une API REST avec Flask.
- Écriture de tests unitaires et fonctionnels avec `pytest`.
- Automatisation des tests grâce à une pipeline CI/CD GitHub Actions.

L'objectif est double :
1. Proposer une API simple pour analyser des données.
2. Garantir la fiabilité du code grâce à des tests bien structurés.

## Structure du projet

```
mon_projet/
├── analyse.py
├── test_analyse.py
├── test_mock_analyse.py
├── app.py
├── tests_api/
│   ├── conftest.py
│   └── test_api.py
├── requirements.txt
└── README.md
```

- `analyse.py` : logique métier de traitement des données.
- `app.py` : API Flask pour exposer les résultats.
- `test_analyse.py` : tests unitaires des fonctions.
- `test_mock_analyse.py` : tests avec mocks.
- `tests_api/` : tests de l’API avec Flask test client.

## 1. Prérequis et installation

### Création de la structure du projet

```bash
mkdir mon_projet && cd mon_projet
touch analyse.py test_analyse.py test_mock_analyse.py app.py README.md
mkdir tests_api && touch tests_api/conftest.py tests_api/test_api.py
```

Création de tous les fichiers nécessaires à l’organisation du projet.

### Création de l'environnement virtuel

```bash
python3 -m venv venv
source venv/bin/activate    # Linux/macOS
venv\Scripts\activate       # Windows
pip install flask pytest pytest-mock
```

Création d’un environnement isolé et installation des bibliothèques nécessaires au projet.

### Fichier requirements.txt

```text
flask
pytest
pytest-mock
```

Liste des dépendances à installer automatiquement dans un environnement ou pipeline.

## 2. Module d’analyse (`analyse.py`)

Contient deux fonctions principales :

- `moyenne(valeurs)` : retourne la moyenne d’une liste.
- `regression_lineaire(x, y)` : calcule les coefficients a et b d’une régression linéaire.

En cas d’erreurs (listes vides, division par zéro), des exceptions sont levées pour éviter des calculs incorrects.

## 3. Tests ciblés avec mock (`test_mock_analyse.py`)

```python
def test_appel_regression_lineaire(mocker):
```

Ce test permet de simuler (`mocker.patch`) l'appel à la fonction `regression_lineaire` pour vérifier :

- Que l’appel a bien lieu.
- Que les bons arguments sont passés.
- Sans exécuter réellement l’algorithme de régression.

## 4. Tests unitaires (`test_analyse.py`)

Contient des tests directs sur la logique d’analyse :

- `test_moyenne_valide()` : teste la moyenne.
- `test_regression_valide()` : vérifie les résultats de la régression avec plusieurs cas.
- `test_regression_erreurs()` : vérifie la levée d’exceptions avec des entrées incorrectes.

Les tests sont écrits avec `pytest.mark.parametrize` pour couvrir plusieurs cas de figure automatiquement.

## 5. API Flask (`app.py`)

Deux routes sont exposées :

- `GET /ping` : renvoie `{ "message": "pong" }` pour tester la disponibilité.
- `POST /analyse` : reçoit deux listes `x` et `y`, retourne `{ "a": ..., "b": ... }`.

L’API gère également les erreurs avec un code HTTP 400 et un message explicite.

### Exemple d’appel via curl

```bash
curl http://127.0.0.1:5000/ping
curl -X POST http://127.0.0.1:5000/analyse \
     -H "Content-Type: application/json" \
     -d '{"x":[0,1],"y":[0,2]}'
```

## 6. Tests de l’API (`tests_api/`)

### conftest.py

Configure le `client` de test Flask pour les tests automatisés.

### test_api.py

Effectue les tests suivants :

- Vérifie la réponse de `/ping`.
- Vérifie que la régression retourne les bons résultats.
- Vérifie que les erreurs sont bien gérées (données invalides).

## 7. Lancement des tests et exécution

### En local

```bash
source venv/bin/activate      # ou venv\Scripts\activate sous Windows
pytest -v                     # Exécute tous les tests
python app.py                 # Lance le serveur Flask
```

## 8. Gestion de version et CI

### Git

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin <URL_DU_DEPOT>
git push -u origin main
```

Initialise un dépôt Git local et le connecte à un dépôt distant sur GitHub.

### CI avec GitHub Actions

Fichier `.github/workflows/python-ci.yml` :

```yaml
name: Python CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-python@v2
        with:
          python-version: '3.x'
      - run: pip install -r requirements.txt
      - run: pytest -v
```

Ce workflow automatise les tests à chaque push ou pull request sur la branche `main`.

### Utilisation d’un self-hosted runner (facultatif)

1. Depuis GitHub : *Settings > Actions > Runners > New self-hosted runner*.
2. Télécharger et configurer l’agent avec le token fourni.
3. Lancer le runner localement.
4. Adapter le fichier `.yml` comme suit :

```yaml
jobs:
  build:
    runs-on: self-hosted
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-python@v2
        with:
          python-version: '3.x'
      - run: pip install -r requirements.txt
      - run: pytest -v
```

## Conclusion

Ce projet montre comment combiner :

- Une logique analytique en Python.
- Une API web simple.
- Une suite de tests complète.
- Une pipeline CI/CD pour garantir qualité et maintenabilité.

Le tout avec une organisation modulaire et reproductible.
