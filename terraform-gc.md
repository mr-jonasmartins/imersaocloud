# Terraform com Google Cloud na Prática

# Passos

- Criar terraform config files

variables.tf:

```
variable "gcp_project_id" {
  default = "dotted-rookery-321921"
}

variable "gcp_region" {
  default     = "us-east4"
}

```

main.tf:

```
terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
      version = "~> 4.37.0"
    }
  }
}

provider "google" {
    project = var.gcp_project_id
}

resource "google_container_cluster" "primary" {
  name               = "kubernetes-cluster-001"
  location           = var.gcp_region
  initial_node_count = 1
  enable_autopilot = true
  ip_allocation_policy {
  }
}

resource "google_sql_database_instance" "instance" {
  name             = "my-database-instancexxxx"
  region           = var.gcp_region
  database_version = "MYSQL_8_0"
  settings {
    tier = "db-f1-micro"
  }

  deletion_protection  = "true"
}

resource "google_sql_database" "database" {
  name     = "my-database"
  instance = google_sql_database_instance.instance.name
}
```

- Subi-los para o Cloud Shell na Google Cloud
- Executar os comandos terraform para provisionar

```
terraform init
terraform plan
terraform apply

```

- Executar comando terraform para destruir a infra
