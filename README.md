# Merise

[🔗 Application en ligne](https://merise-liard.vercel.app/)

Application de modélisation entité-relation inspirée de Looping, pensée pour être modulaire et extensible à d’autres types de diagrammes (Merise, mind map, etc.).

## Fonctionnalités

- **Création d’entités et d’associations** via une interface drag-and-drop
- **Définition de relations** avec cardinalités et contraintes personnalisables
- **Validation automatique** entre Flow et Domain pour garantir la cohérence des données
- **Sauvegardes locales**

## Stack technique

- **Frontend** React 19 et TypeScript avec Vite
- **Diagrammes** XYFlow React Flow
- **Styles** SCSS et approche BEM
- **State** Context API et Managers dédiés
- **Qualité** ESLint + Prettier + TypeScript strict

## Licence

Ce projet est distribué sous licence MIT.  
Vous êtes libre de l'utiliser, le modifier et le redistribuer comme vous le souhaitez.

## Installation

```bash
git clone git@github.com:Wagle-Studio/Merise.git wagle-studio-merise
cd wagle-studio-merise

npm install

npm run dev

npm run build
npm run preview
```

## Documentations

L’application repose sur une architecture en couches distinctes et complémentaires :

| Couche   | Rôle                                                                                                                                                                                   |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Kernel   | Socle central de l’application, il gère les services transverses (sauvegardes, paramètres, dialogues, notifications, erreurs) et expose des opérations cohérentes au reste du système. |
| Domain   | Couche métier spécialisée, construite au-dessus du Kernel pour modéliser un type de diagramme précis (Merise, mind map, etc.).                                                         |
| Managers | Responsables d’un périmètre fonctionnel (flow, merise, settings, save, toast…), ils encapsulent leurs états et exposent des méthodes publiques sûres.                                  |
| DTO      | Objets de transfert immutables représentant l’état courant, produits et validés par les managers pour garantir cohérence et traçabilité.                                               |
| Libs     | Bibliothèques internes fournissant la logique et les modèles propres à chaque diagramme.                                                                                               |
| UI       | Interface utilisateur connectée au Domain et au Kernel via des contextes clairs, utilisant un design system modulaire.                                                                 |

---

### Bénéfice

Cette organisation permet de **brancher différents Domain selon le besoin**.

Aujourd’hui, le projet embarque un **Domain Merise** (schémas MCD pour bases de données), mais rien n’empêche d’ajouter un **Domain Mind Map** ou un autre type de Domain pour de nouveaux usages.

👉 Résultat : la base applicative reste stable et commune, seuls les Domain évoluent.

## Cycle de vie de l’app

### Kernel + schéma

Socle central de l’application, il gère les services transverses (sauvegardes, paramètres, dialogues, notifications, erreurs) et expose des opérations cohérentes au reste du système.

---

Le hook d’initialisation instancie les managers du Kernel, chacun spécialisé dans une responsabilité (dialogues, gestion des erreurs, sauvegardes, paramètres…) ainsi que les états globaux associés.

Ces managers sont ensuite orchestrés par le Kernel Manager, qui agrège leurs fonctionnalités pour proposer des opérations plus complexes et transversales.

La Kernel Factory se charge de construire et d’exposer vers le reste de l’application les **opérations** et **dépendances** issues de ce cœur, garantissant un point d’accès unique et cohérent.

### Schéma de l'initialisation du Kernel

![Schéma du Kernel](./public/draw_kernel.jpg)

---

### Domains + schéma

Couche métier spécialisée, construite au-dessus du Kernel pour modéliser un type de diagramme précis (Merise, mind map, etc.).

---

Le hook d’initialisation crée les managers spécifiques au type de diagramme ciblé (Merise, mind map, etc.) ainsi que les états globaux nécessaires à leur fonctionnement.

Chaque manager provient d’une librairie interne autonome, dédiée à la gestion de sa logique métier.

Le Domain Manager orchestre ces managers pour combiner leurs fonctionnalités et proposer des opérations adaptées au contexte du diagramme.

Enfin, la Domain Factory expose les **opérations** et **dépendances** construites à partir du Domain, fournissant une interface claire et réutilisable pour le reste de l’application.

### Schéma de l'initialisation du Domain Merise

![Schéma du Domain Merise](./public/draw_domain_merise.jpg)

### Schéma de l'initialisation du Domain exemple

![Schéma du Domain Example](./public/draw_domain_example.jpg)

---

### Managers

Responsables d’un périmètre fonctionnel (flow, merise, settings, save, toast…), ils encapsulent leurs états et exposent des méthodes publiques sûres.

---

Les états globaux du Kernel, ainsi que ceux requis par un Domain spécifique, sont initialisés dans leurs hooks respectifs.

Toutefois, ces états ne sont jamais manipulés directement : ils sont encapsulés et pilotés exclusivement par le **manager** correspondant.

Chaque manager est responsable d’un pan fonctionnel de l’application (dialogues, sauvegardes, paramètres, logique métier…) et agit comme **gardien de cohérence** en appliquant les règles associées à son domaine.

Il expose ensuite un ensemble de méthodes publiques, permettant à l’orchestrateur (Kernel Manager ou Domain Manager) de composer des opérations plus complexes tout en préservant l’intégrité des données et la séparation des responsabilités.

---

### DTO

Objets de transfert immutables représentant l’état courant, produits et validés par les managers pour garantir cohérence et traçabilité.

---

Les **Data Transfer Objects (DTO)** sont au cœur de la communication entre les managers et le reste de l’application.

Ils assurent une représentation **immutable** de l’état courant (Flow, Merise, Settings, Save, etc.) et garantissent que les données partagées sont toujours cohérentes et validées.

Chaque mutation passe par un manager, qui produit un nouveau DTO plutôt que de modifier l’existant.  
Ce mécanisme favorise la **traçabilité**, simplifie le **debug**, et renforce la robustesse de l’application en éliminant les effets de bord.

Les DTO constituent ainsi le **contrat stable** entre la logique métier et l’interface utilisateur.

## Architecture

```txt
src/
├── core/
│   ├── kernel/                # Orchestrateur du cœur de l’app
│   ├── domain/                # Couche métier
│   └── libs/                  # Services transverses (dialog, error, save, toast...)
├── libs/
│   ├── flow/                  # Logique et modèles pour la gestion du diagramme React Flow
│   └── merise/                # Logique et modèles pour la partie métier Merise
└── ui/
    ├── system/                # Design system et formulaires
    └── libs/                  # Composants UI Kernel et Domain
```
