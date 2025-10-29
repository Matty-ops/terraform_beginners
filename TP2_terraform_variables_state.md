# TP2 — Variables, Outputs et Gestion du State dans Terraform

## Objectifs

* Comprendre la gestion des variables et des outputs dans Terraform.
* Manipuler les commandes courantes (`init`, `plan`, `apply`, `destroy`, `state`, `show`).
* Configurer un **state distant** (ex : S3, GCS, Azure Blob Storage).

---

## 1. Structure du projet

Reprenez le projet du **TP1** et ajoutez les fichiers suivants :

```
terraform-demo/
├── main.tf
├── variables.tf
├── outputs.tf
└── terraform.tfvars
```

---

## 2. Définir des variables

### `variables.tf`

```hcl
variable "region" {
  description = "Région de déploiement"
  type        = string
  default     = "eu-west-3"
}

variable "instance_type" {
  description = "Type d'instance EC2"
  type        = string
  default     = "t2.micro"
}

variable "project" {
  description = "Nom du projet"
  type        = string
}
```

### `terraform.tfvars`

```hcl
project = "terraform-demo"
```

### Utilisation des variables dans `main.tf`

```hcl
provider "aws" {
  region = var.region
}

resource "aws_instance" "vm" {
  ami           = "ami-0c02fb55956c7d316"
  instance_type = var.instance_type

  tags = {
    Name = var.project
  }
}
```

---

## 3. Définir des outputs

### `outputs.tf`

```hcl
output "instance_id" {
  description = "ID de l'instance EC2 créée"
  value       = aws_instance.vm.id
}

output "public_ip" {
  description = "Adresse IP publique de la machine"
  value       = aws_instance.vm.public_ip
}
```

Afficher les outputs :

```bash
terraform output
```

---

## 4. Commandes de base Terraform

### Initialisation du projet

```bash
terraform init
```

* Télécharge les providers.
* Configure le backend local ou distant.

### Prévisualisation du plan

```bash
terraform plan
```

* Montre les actions à exécuter (ajout, modification, suppression).

### Application du plan

```bash
terraform apply
```

* Exécute les changements.

### Destruction de l’infrastructure

```bash
terraform destroy
```

* Supprime les ressources gérées par Terraform.

---

## 5. Commandes d’inspection du state

### Lister les ressources gérées

```bash
terraform state list
```

### Afficher les détails d’une ressource

```bash
terraform state show aws_instance.vm
```

### Vérifier le contenu du state

```bash
cat terraform.tfstate | jq .
```

### Supprimer une ressource du state (sans la détruire)

```bash
terraform state rm aws_instance.vm
```

> ⚠️ Cette commande retire la ressource du suivi Terraform, mais elle reste dans le cloud.

---

## 6. Configuration d’un backend distant

> Le backend distant permet de partager le **state** entre plusieurs utilisateurs et environnements.

### Exemple : Backend S3 (AWS)

```hcl
terraform {
  backend "s3" {
    bucket         = "terraform-state-demo"
    key            = "infra/terraform.tfstate"
    region         = "eu-west-3"
    encrypt        = true
  }
}
```

#### Étapes

1. Créer un bucket S3 depuis la console AWS.
2. Ajouter la configuration du backend dans `main.tf`.
3. Exécuter :

   ```bash
   terraform init -migrate-state
   ```
4. Vérifier que le fichier `terraform.tfstate` local a disparu (désormais stocké dans S3).

---

### Exemple : Backend GCS (GCP)

```hcl
terraform {
  backend "gcs" {
    bucket  = "terraform-state-demo"
    prefix  = "terraform/state"
  }
}
```

Créer le bucket :

```bash
gsutil mb -l europe-west1 gs://terraform-state-demo/
```

---

### Exemple : Backend Azure Blob

```hcl
terraform {
  backend "azurerm" {
    resource_group_name  = "terraform-backend"
    storage_account_name = "tfstateaccount"
    container_name       = "tfstate"
    key                  = "terraform.tfstate"
  }
}
```

---

## 7. Exercices

1. Modifier votre projet pour passer le **type d’instance** en variable.
2. Ajouter un **output** supplémentaire affichant le nom de la machine.
3. Lister les ressources avec `terraform state list` et vérifier qu’elles correspondent au cloud.
4. Migrer le **state** vers un backend distant (S3, GCS ou Azure).
5. Détruire les ressources puis vérifier que le **state distant** est vide.

---

## 8. Bonnes pratiques

* Toujours exécuter `terraform plan` avant `apply`.
* Sauvegarder et versionner vos fichiers `.tf`, **jamais** le fichier `terraform.tfstate`.
* Utiliser un **backend distant** pour les projets collaboratifs.
* Utiliser des **outputs** clairs et documentés pour faciliter l’intégration CI/CD.

---

*Auteur : Matthieu Minet *
