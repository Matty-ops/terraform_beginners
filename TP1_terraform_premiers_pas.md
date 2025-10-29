# TP1 — Premiers pas avec Terraform

## Objectifs

* Comprendre le rôle de Terraform dans l’Infrastructure as Code (IaC).
* S’inscrire sur un fournisseur de cloud public (AWS, GCP ou Azure).
* Configurer les identifiants et le backend à la main.
* Créer un compte de service et déployer une première ressource simple.

---

## 1. Introduction

Terraform est un outil open source développé par HashiCorp permettant de **décrire, déployer et gérer** des infrastructures via du code déclaratif.

### Concepts clés

* **Provider** : interface entre Terraform et un service cloud (AWS, GCP, Azure...)
* **Backend** : stockage de l’état (`terraform.tfstate`) partagé entre utilisateurs
* **Plan / Apply** : Terraform propose un plan d’exécution avant d’appliquer les changements

---

## 2. Installation de Terraform

### Sous Linux (Ubuntu/Debian)

```bash
sudo apt update && sudo apt install -y wget unzip
wget https://releases.hashicorp.com/terraform/1.9.8/terraform_1.9.8_linux_amd64.zip
unzip terraform_1.9.8_linux_amd64.zip
sudo mv terraform /usr/local/bin/
terraform -version
```

### Sous macOS

```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
terraform -version
```

---

## 3. Choix du fournisseur cloud

Terraform nécessite un **compte actif** sur un cloud public.

### Option 1 — AWS

* Créer un compte [https://aws.amazon.com/](https://aws.amazon.com/)
* Dans la console IAM :

  1. Créer un utilisateur `terraform-user`
  2. Lui attribuer la politique `AdministratorAccess`
  3. Récupérer la **clé d’accès** et la **clé secrète**

### Option 2 — GCP

* Créer un projet GCP.
* Créer un **compte de service Terraform** :

  ```bash
  gcloud iam service-accounts create terraform --display-name "Terraform Service Account"
  gcloud projects add-iam-policy-binding <PROJECT_ID> \
      --member="serviceAccount:terraform@<PROJECT_ID>.iam.gserviceaccount.com" \
      --role="roles/owner"
  gcloud iam service-accounts keys create key.json \
      --iam-account=terraform@<PROJECT_ID>.iam.gserviceaccount.com
  ```

### Option 3 — Azure

* Créer un compte [https://azure.microsoft.com/](https://azure.microsoft.com/)
* Installer Azure CLI et se connecter :

  ```bash
  az login
  ```
* Créer un **service principal** :

  ```bash
  az ad sp create-for-rbac --name terraform --role Contributor --scopes /subscriptions/<SUBSCRIPTION_ID>
  ```

  Cela retournera un JSON avec les credentials à enregistrer.

---

## 4. Configuration du backend

Terraform enregistre l’état de l’infrastructure dans un fichier `terraform.tfstate`. Pour le moment, on le stocke **localement**.

### Exemple de fichier de configuration `main.tf`

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
  backend "local" {
    path = "terraform.tfstate"
  }
}

provider "aws" {
  region     = "eu-west-3"
  access_key = "<VOTRE_ACCESS_KEY>"
  secret_key = "<VOTRE_SECRET_KEY>"
}
```

> 💡 Plus tard, le backend pourra être migré vers un stockage distant (ex : S3, GCS, Azure Blob).

---

## 5. Création de la première ressource

### Exemple : Instance EC2 (AWS)

```hcl
resource "aws_instance" "demo" {
  ami           = "ami-0c02fb55956c7d316" # Amazon Linux 2 (région Paris eu-west-3)
  instance_type = "t2.micro"
  tags = {
    Name = "demo-terraform"
  }
}
```

### Exemple : Bucket S3

```hcl
resource "aws_s3_bucket" "demo_bucket" {
  bucket = "mon-premier-bucket-terraform-${random_id.suffix.hex}"
  tags = {
    Environment = "Demo"
  }
}

resource "random_id" "suffix" {
  byte_length = 4
}
```

### Initialisation et déploiement

```bash
terraform init     # télécharge les plugins
terraform plan     # montre le plan d’exécution
terraform apply    # applique le plan
```

### Vérification

* Consulter la console du fournisseur (AWS / GCP / Azure)
* Supprimer la ressource :

  ```bash
  terraform destroy
  ```

---

## 6. Exercices pratiques

1. Créer un projet Terraform minimal pour déployer une VM ou un bucket selon le cloud choisi.
2. Modifier la ressource (ex : changer le nom ou le type d’instance) et relancer `terraform apply`.
3. Ajouter des tags pour identifier l’environnement (`dev`, `test`, `prod`).
4. Observer la mise à jour du fichier `terraform.tfstate`.

---

## 7. Bonnes pratiques

* Ne jamais **versionner** vos clés dans Git (utiliser des variables d’environnement ou un Vault).
* Nommer clairement vos ressources (`project-environment-role`).
* Documenter chaque ressource avec des commentaires.
* Toujours exécuter `terraform plan` avant `apply`.

---

*Auteur : Matthieu Minet*
