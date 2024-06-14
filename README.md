# Projet chatbot keyce academie M1 info

## _Agent conversationnel LLM_

Cet chatbot LLM est conçu avec le framework **RASA** et **Python3**

# Version des différentes technologies

| Technologies                   | Version |
| ------------------------------ | ------- |
| **Rasa Version**               | 3.6.20  |
| **Minimum Compatible Version** | 3.5.0   |
| **Rasa SDK Version**           | 3.6.2   |
| **Python Version**             | 3.9.0   |

# Prérequis

Vous devez avoir python 3.9.0 installer en local afin de pouvoir utiliser cette version du chatbot

## installer python

Pour installer python sur windows, macos ou linux veillez, [cliquer ici](https://kinsta.com/knowledgebase/install-python/) pour les instructions à suivre

# Installation du chatbot en local

## 1- Creation d'un environnement virtuel python

Dans votre terminal, allez dans le reportoire du chatbot puis faites :

```bash
# macOS/Linux
python3 -m venv .venv

# Windows
# VOus pouvez aussi utiliser py -3 -m venv .venv
python -m venv .venv
```

>Notez que _\<environment name\>_  est un nom que vous choississez, mais par convention, nous utilisons env
>
### Activation de l'environnement virtuel

Après la création de l'environnement virtuel vous devez l'activer

#### sur windows

```cmd
env\Scripts\activate.bat
```

```powershell
env\Scripts\Activate
```

#### sur macos ou linux

```sh
source env/bin/activate
```

après l'activation de l'environnement virtuel dans votre projet, il s'affichera comme ça :

```sh
(env) ~/rasa_chatbot_m1info/
```

finalisez cette étape par la mise à jour de pip :

```sh
pip install -–upgrade pip
```

## 2- Installation du requirements.txt

Tous les paquets pythons nécessaires au bon fonctionnement de ce projet ont été répertoriés dans un fichier texte,
afin que vous n'ayez pas à les installer individuellement.
Vous pouvez les installer en tapant dans votre terminal, la commande suivante:

```sh
pip install -r requirements.txt
```

Bravo 👏, votre chatbot est maintenant près à l'emploi.

# Structure du projet

Le projet est structuré comme suit :

+ |--actions/
    +|-- **init**.py
  + |-- actions.py
+ |--data/
  + |-- nlu.yml
  + |--rules.yml
  + |--stories.yml
+ |--models/
  + |--_modele entrainé_
+ |--tests/
  + |--test_stories.yml
+ |--config.yml
+ |--credentials.yml
+ |--domain.yml
+ |--endpoints.yml
+ |--liste_apart.txt
+ |--requirements.txt

Pour en apprendre sur les différents fichiers, veuillez lire la documentation _Rasa_  [Doc Rasa](https://rasa.com/docs/rasa/chitchat-faqs)

# Comment ça marche

voici le **Workflow de développement de chatbot avec Rasa**:

## 1- preparation des données

La première étape consiste à collecter des données d'entraînement : des conversations, des exemples d'intentions, et des entités.
Ces données sont utilisées pour entraîner le modèle NLU et le modèle Core.

exemples:

**nlu.yml**:

```sh
version: "3.1"

nlu:
- intent: saluer
  examples: |
    - salut
    - bonjour
    - hey
    - bonjour là
    - bon matin
    - bonsoir
    - coucou
    - salut là
    - allons-y
    - salut mec
    - bonjour bon matin
    - bonsoir bonsoir
    - bon après-midi

- intent: au_revoir
  examples: |
    - à plus
    - au revoir
    - à plus tard
    - bonne nuit
    - bye
    - au revoir
    - passe une bonne journée
    - à la prochaine
    - bye bye
    - à plus tard

- intent: affirmer
  examples: |
    - oui
    - ouais
    - en effet
    - bien sûr
    - ça a l'air bien
    - correct

- intent: nier
  examples: |
    - non
    - non merci
    - jamais
    - je ne pense pas
    - je n'aime pas ça
    - pas question
    - pas vraiment

- intent: humeur_genial
  examples: |
    - parfait
    - génial
    - incroyable
    - je me sens comme un roi
    - merveilleux
    - je me sens très bien
    - je suis génial
    - je suis incroyable
    - je vais sauver le monde
    - super content
    - extrêmement bien
    - tellement parfait
    - tellement bien
    - tellement parfait

- intent: humeur_malheureux
  examples: |
    - ma journée a été horrible
    - je suis triste
    - je ne me sens pas très bien
    - je suis déçu
    - super triste
    - je suis tellement triste
    - triste
    - très triste
    - malheureux
    - pas bien
    - pas très bien
    - extrêmement triste
    - tellement triste
    - tellement triste

- intent: defis_bot
  examples: |
    - es-tu un bot ?
    - es-tu un humain ?
    - est-ce que je parle à un bot ?
    - est-ce que je parle à un humain ?

```

l'expression _intent_ désigne l'intention de l'utilisateur, pour chaque intention on définit un certain nombre d'exemples pour permettre au modèle de reconnaitre l'intention dans l'input de
l'utilisateur lors de la conversation.

**domain.yml***:

```sh
version: "3.1"

intents:
  - saluer
  - au_revoir
  - affirmer
  - nier
  - humeur_genial
  - humeur_malheureux
  - defis_bot

responses:
  utter_saluer:
  - text: "Salut ! Comment ça va ?"

  utter_remonter_moral:
  - text: "Voici quelque chose pour te remonter le moral :"
    image: "https://i.imgur.com/nGF1K8f.jpg"

  utter_est_ce_que_ca_aide:
  - text: "Est-ce que ça t'a aidé ?"

  utter_heureux:
  - text: "Super, continue comme ça !"

  utter_au_revoir:
  - text: "Au revoir"

  utter_je_suis_un_bot:
  - text: "Je suis un bot, propulsé par Rasa."

session_config:
  session_expiration_time: 60
  carry_over_slots_to_new_session: true


```

Dans le fichier domain.yml, on liste les différentes intentions, on a definit une entité pour stocker le numéro de l'appartement de l'utilisateur,
les différentes actions et enfin toutes les réponses que le chatbot devra fournir aux différentes intentions.

**stories.yml** :

```sh
version: "3.1"

stories:

- story: chemin heureux
  steps:
  - intent: saluer
  - action: utter_saluer
  - intent: humeur_genial
  - action: utter_heureux

- story: chemin triste 1
  steps:
  - intent: saluer
  - action: utter_saluer
  - intent: humeur_malheureux
  - action: utter_remonter_moral
  - action: utter_est_ce_que_ca_aide
  - intent: affirmer
  - action: utter_heureux

- story: chemin triste 2
  steps:
  - intent: saluer
  - action: utter_saluer
  - intent: humeur_malheureux
  - action: utter_remonter_moral
  - action: utter_est_ce_que_ca_aide
  - intent: nier
  - action: utter_au_revoir

```

Dans le fichier stories.yml on procède à la mise en place des étapes de machine learning afin de permettre au chatbot de contextualiser ses échanges avec l'utilisateur.

## 2- Entrainement du chatbot

Une fois les données de l'entrainement fournies, il faut dabord procéder à leur validation, puis à l’entraînement pour la création d'un model .

### Validation de données

```sh
   rasa data validate
```

### entrainement du chatbot

```sh
   rasa train
```

Après l'entrainement du chatbot vous pouvez tester le chatbot en interagissant avec lui depuis le terminal

```sh
   rasa shell
```
