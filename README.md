# Série de Travaux Pratiques — Infrastructure as Code avec Terraform

Cette série de TPs a pour objectif de guider progressivement les étudiants dans la **prise en main de Terraform**, de la découverte de l’outil jusqu’à la gestion d’environnements complexes avec modules et backends distants.

---

## 🧱 Organisation des TPs

| TP      | Thématique principale       | Objectifs                                                                                                                              | Lien                                                                   |
| ------- | --------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| **TP1** | Premiers pas avec Terraform | Installation, configuration du provider et du backend local, création d’un compte de service et déploiement d’une première ressource   | [TP1_terraform_premiers_pas.md](./TP1_terraform_premiers_pas.md)       |
| **TP2** | Variables, Outputs et State | Gestion des variables et des outputs, manipulation du state, migration vers un backend distant, et exploration des commandes Terraform | [TP2_terraform_variables_state.md](./TP2_terraform_variables_state.md) |
| **TP3** | Modules et Environnements   | Structuration du projet avec des modules, utilisation des workspaces et gestion multi-environnements (dev, staging, prod)              | [TP3_terraform_modules_envs.md](./TP3_terraform_modules_envs.md)       |

---

## ⚙️ Prérequis

* Terraform installé sur la machine locale (`terraform -version`)
* Un compte cloud actif (AWS, GCP ou Azure)
* Un éditeur de texte ou IDE (VSCode recommandé)
* Accès internet pour télécharger les providers et modules

---

## 🧩 Progression pédagogique

1. **TP1 — Découverte et premiers déploiements :** installation, configuration et création d’une première ressource cloud.
2. **TP2 — Gestion du state et des variables :** approfondir la logique Terraform, les fichiers `tfvars`, outputs et commandes de gestion du state.
3. **TP3 — Structuration avancée :** mise en place de modules réutilisables et d’environnements multiples à l’aide des workspaces.

---

## 🪄 Commandes Terraform de base

| Commande                           | Description                                              |
| ---------------------------------- | -------------------------------------------------------- |
| `terraform init`                   | Initialise le projet et télécharge les providers         |
| `terraform plan`                   | Affiche le plan d’exécution avant application            |
| `terraform apply`                  | Applique les changements définis dans les fichiers `.tf` |
| `terraform destroy`                | Supprime les ressources gérées par Terraform             |
| `terraform state list`             | Liste les ressources suivies par Terraform               |
| `terraform state show <ressource>` | Affiche les détails d’une ressource dans le state        |
| `terraform output`                 | Affiche les valeurs exportées par les outputs            |
| `terraform workspace list`         | Liste les environnements actifs                          |

---

## 🚀 Pour aller plus loin

* [Documentation officielle Terraform](https://developer.hashicorp.com/terraform)
* [Terraform Registry (Modules officiels)](https://registry.terraform.io/)
* [Bonnes pratiques HashiCorp](https://developer.hashicorp.com/terraform/docs/cloud/guides/recommended-practices)

---

*Auteur : Matthieu Minet — IAxLab Inside*
