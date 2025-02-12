# Blog Minimaliste NS (Application Mobile)

Une application mobile minimaliste de blog développée avec NativeScript-Vue pour le TP3 de la matière Programmation Mobile Multiplataforme.

## Description

Blog Minimaliste NS est une application mobile qui permet aux utilisateurs de créer et partager des posts, commenter et interagir avec d'autres utilisateurs. L'application est développée avec NativeScript-Vue et communique avec une API REST développée en Node.js.

## Fonctionnalités

- 🌐 Support multilingue (Français/Anglais)
- 👤 Système d'authentification
- ✏️ Création et suppression de posts
- 💬 Système de commentaires
- 👥 Gestion de profil utilisateur
- 🎨 Interface minimaliste et moderne

## Futures Fonctionnalités

- 👍 Système de likes pour les posts
- ❤️ Système de likes pour les commentaires
- 📨 Invitation de nouveaux membres
- 📤 Partage de posts et commentaires

## Technologies Utilisées

- NativeScript-Vue
- Axios pour les requêtes HTTP
- Vue.js pour la réactivité
- Node.js pour l'API (backend séparé)

## Installation

```bash
# Cloner le repository
git clone https://github.com/raphaelsrcunha/blog-minimaliste-ns.git

# Accéder au dossier
cd blog-minimaliste-ns

# Installer les dépendances
npm install

# Lancer l'application sur Android
ns run android

```

## Prérequis

- Node.js
- NativeScript CLI
- Android Studio (pour Android)

## Structure du Projet

```
blog-minimaliste-ns/
├── app/
│   ├── components/      # Composants Vue
│   ├── i18n/           # Fichiers de traduction
│   └── app.js          # Point d'entrée
├── package.json
└── README.md
```

## Configuration de l'API

L'application communique avec une API REST. Assurez-vous de configurer l'URL de l'API dans les fichiers de configuration.

URL par défaut : `http://10.0.2.2:3000`

## Développement

Ce projet a été développé dans le cadre du cours de Programmation Mobile Multiplateforme au Collège André-Grasset.

## Licence

MIT

## Contact

Raphael Cunha - [GitHub](https://github.com/raphaelsrcunha)

Lien du projet: [https://github.com/raphaelsrcunha/blog-minimaliste-ns](https://github.com/raphaelsrcunha/blog-minimaliste-ns)
