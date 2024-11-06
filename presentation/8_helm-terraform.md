class: inverse, center, middle

# Helm és Terraform Alapok

---

class: inverse, center, middle

# Helm - A Kubernetes Package Manager

---

## Mi a Helm?

* "A Kubernetes csomagkezelője"
* Package management és deployment tool
* Leegyszerűsíti a komplex alkalmazások telepítését
* Template engine és verziókezelés
* Újrahasználható konfiguráció csomagok
* Package repository kezelés

---

## Helm architektúra

* Helm CLI - kliens oldali eszköz
* Charts - alkalmazás csomagok
* Repositories - chart tárolók
* Releases - telepített chart példányok
* Values - testreszabható konfigurációk
* Templates - sablonok a K8s erőforrásokhoz

---

## Helm telepítés

* Platformonként eltérő telepítési módok:
```bash
# Linux
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash

# Windows (Chocolatey)
choco install kubernetes-helm

# MacOS
brew install helm
```

* Helm inicializálás és verifikáció:
```bash
helm version
helm repo add stable https://charts.helm.sh/stable
```

---

## Helm Charts

* Alkalmazás definíciós csomag
* Struktúra:
```
mychart/
  Chart.yaml          # Metaadatok
  values.yaml         # Alap konfiguráció
  charts/             # Függőségek
  templates/          # K8s manifest sablonok
  README.md          # Dokumentáció
```

* Verziókezelés és függőségek

---

## Chart Repository kezelés

* Repository műveletek:
```bash
helm repo list
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

* Chart keresés:
```bash
helm search repo wordpress
helm show values bitnami/wordpress
```

---

## Helm Package telepítés

* Alkalmazás telepítés:
```bash
helm install my-release bitnami/wordpress
```

* Release kezelés:
```bash
helm list
helm status my-release
helm upgrade my-release bitnami/wordpress
helm rollback my-release 1
```

---

class: inverse, center, middle

# Terraform - Infrastructure as Code

---

## Mi a Terraform?

* Infrastructure as Code (IaC) eszköz
* Deklaratív konfigurációs nyelv
* Multi-cloud támogatás
* Verziókezelt infrastruktúra
* State management
* Resource graph és függőségek

---

## Terraform telepítés

* Platformfüggő telepítési opciók:
```bash
# Linux
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform

# Windows
choco install terraform

# MacOS
brew install terraform
```

---

## Terraform konfigurációs fájl

* HCL (HashiCorp Configuration Language)
* Alapvető elemek:
```hcl
# Provider konfiguráció
provider "kubernetes" {
  config_path = "~/.kube/config"
}

# Resource definíció
resource "kubernetes_namespace" "example" {
  metadata {
    name = "example-namespace"
  }
}
```

---

## Terraform Workflow

* Alap parancsok:
```bash
terraform init        # Inicializálás
terraform plan       # Tervezett változások
terraform apply      # Végrehajtás
terraform destroy    # Törlés
```

* State management:
  * Local state
  * Remote state (pl. S3)
  * State locking

---

## Terraform Minikube integráció

* Minikube provider beállítás:
```hcl
provider "kubernetes" {
  config_context_cluster = "minikube"
}
```

* Erőforrás példa:
```hcl
resource "kubernetes_deployment" "example" {
  metadata {
    name = "example"
    labels = {
      app = "example"
    }
  }
  spec {
    replicas = 3
    // ...
  }
}
```

---

## Nginx példa Terraform-mal

```hcl
resource "kubernetes_deployment" "nginx" {
  metadata {
    name = "nginx-example"
  }
  spec {
    replicas = 2
    selector {
      match_labels = {
        app = "nginx"
      }
    }
    template {
      metadata {
        labels = {
          app = "nginx"
        }
      }
      spec {
        container {
          image = "nginx:latest"
          name  = "nginx"
          port {
            container_port = 80
          }
        }
      }
    }
  }
}
```

---

## Python Web App konfiguráció

* Komplett alkalmazás struktúra:
```
project/
├── main.tf              # Fő konfig
├── variables.tf         # Változók
├── outputs.tf          # Kimenetek
├── helm/
│   └── webapp/         # Helm chart
└── terraform/
    ├── app.tf          # App specifikus
    └── kubernetes.tf    # K8s erőforrások
```

* Terraform + Helm integráció:
```hcl
resource "helm_release" "webapp" {
  name       = "python-webapp"
  chart      = "./helm/webapp"
  namespace  = "default"
  
  set {
    name  = "image.tag"
    value = "latest"
  }
}
```

---

## Összefoglalás

* Helm és Terraform alapok
* Package management Helm-mel
* Infrastruktúra mint kód
* Best practices:
  * Verziókezelés
  * Modularizáció
  * State management
  * Biztonság
* További témák és források

---

class: inverse, center, middle

# Kérdések?

