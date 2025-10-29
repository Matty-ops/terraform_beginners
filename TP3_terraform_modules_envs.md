# TP3 — Modules et Environnements dans Terraform

## Objectifs

* Comprendre la notion de **modules Terraform** pour réutiliser du code.
* Structurer un projet avec plusieurs **environnements** (dev, staging, prod).
* Paramétrer des **variables spécifiques** à chaque environnement.

---

## 1. Pourquoi utiliser des modules ?

Les **modules** permettent de factoriser et de réutiliser du code Terraform pour créer plusieurs instances d’une même infrastructure (par exemple : un réseau, une machine virtuelle, ou un bucket).

Un module est simplement un dossier contenant des fichiers `.tf` (resources, variables, outputs, etc.).

### Structure générale

```
terraform-demo/
├── modules/
│   └── instance/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
└── envs/
    ├── dev/
    │   └── main.tf
    ├── staging/
    │   └── main.tf
    └── prod/
        └── main.tf
```

---

## 2. Création d’un module simple

### `modules/instance/main.tf`

```hcl
resource "aws_instance" "vm" {
  ami           = var.ami_id
  instance_type = var.instance_type

  tags = {
    Name = "${var.project}-${var.environment}"
  }
}
```

### `modules/instance/variables.tf`

```hcl
variable "project" {
  description = "Nom du projet"
  type        = string
}

variable "environment" {
  description = "Environnement cible (dev/staging/prod)"
  type        = string
}

variable "ami_id" {
  description = "AMI à utiliser pour l'instance EC2"
  type        = string
}

variable "instance_type" {
  description = "Type d'instance EC2"
  type        = string
  default     = "t2.micro"
}
```

### `modules/instance/outputs.tf`

```hcl
output "instance_id" {
  description = "ID de l'instance créée"
  value       = aws_instance.vm.id
}
```

---

## 3. Définition des environnements

Chaque environnement réutilise le même module avec des variables spécifiques.

### `envs/dev/main.tf`

```hcl
module "instance" {
  source       = "../../modules/instance"
  project      = "demo-app"
  environment  = "dev"
  ami_id       = "ami-0c02fb55956c7d316"
  instance_type = "t2.micro"
}
```

### `envs/staging/main.tf`

```hcl
module "instance" {
  source       = "../../modules/instance"
  project      = "demo-app"
  environment  = "staging"
  ami_id       = "ami-0c02fb55956c7d316"
  instance_type = "t3.micro"
}
```

### `envs/prod/main.tf`

```hcl
module "instance" {
  source       = "../../modules/instance"
  project      = "demo-app"
  environment  = "prod"
  ami_id       = "ami-0c02fb55956c7d316"
  instance_type = "t3.small"
}
```

---

## 4. Utilisation des workspaces

Les **workspaces** Terraform permettent de gérer plusieurs environnements à partir d’une même configuration.

```bash
terraform workspace new dev
terraform workspace new prod
terraform workspace list
terraform workspace select dev
```

Chaque workspace aura son propre fichier d’état (`terraform.tfstate.d/dev/terraform.tfstate`).

---

## 5. Commandes utiles

| Commande              | Description                                        |
| --------------------- | -------------------------------------------------- |
| `terraform init`      | Initialise le répertoire et télécharge les modules |
| `terraform validate`  | Vérifie la syntaxe du code Terraform               |
| `terraform plan`      | Affiche les modifications à appliquer              |
| `terraform apply`     | Applique les changements                           |
| `terraform destroy`   | Supprime les ressources créées                     |
| `terraform output`    | Affiche les valeurs des outputs                    |
| `terraform workspace` | Gère les environnements multiples                  |

---

## 6. Exercices

1. Créez un module `bucket` dans `modules/bucket/` pour déployer un bucket S3 (ou équivalent GCP/Azure).
2. Ajoutez ce module à vos environnements `dev`, `staging` et `prod`.
3. Testez la création d’un workspace `staging` et déployez l’environnement correspondant.
4. Listez les instances et buckets déployés avec `terraform state list`.
5. Détruisez uniquement les ressources du workspace courant :

   ```bash
   terraform destroy
   ```

---

## 7. Bonnes pratiques

* Chaque environnement (dev, staging, prod) doit avoir ses propres variables.
* Centraliser les modules dans un répertoire unique (`modules/`).
* Utiliser les **outputs** pour faire communiquer plusieurs modules entre eux.
* Versionner les modules (Git tags) pour garantir la reproductibilité.
* Tester les modules indépendamment avant intégration.

---

## 8. Pour aller plus loin

* [Terraform Registry Modules](https://registry.terraform.io/browse/modules)
* [Workspaces Terraform](https://developer.hashicorp.com/terraform/language/state/workspaces)
* [Gestion des environnements avec Terraform](https://developer.hashicorp.com/terraform/tutorials)

---

*Auteur : Matthieu Minet*
