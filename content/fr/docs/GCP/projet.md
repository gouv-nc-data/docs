---
title: Projet GCP
description: ressources crées avec un projet
date: 2023-10-25
weight: 1
---

# Module terraform de création de projet

![Image alt](projet/base_project.png)

[Sources sur github](https://github.com/gouv-nc-data/data-gitops/tree/main/modules/bigquery_project)

## Déclaration d'un projet

{{< highlight terraform "linenos=table,linenostart=1" >}}
locals {
  davar_group_name = "grp-gcp-davar-bq"
  davar_name       = "davar"
}

module "davar-datawarehouse" {
  source                  = "./modules/bigquery_project"
  project_name            = "prj-davar-p-bq"
  dataset_name            = "bmfl"
  primary_contact         = "thomas.guyader@gouv.nc"
  secondary_contact       = "antoine.bugeia@gouv.nc"
  direction               = local.davar_name
  group_name              = local.davar_group_name
  default_billing_account = var.default_billing_account
  org_id                  = var.org_id
}
{{< / highlight >}}

## Resources crées
- Un projet gcp dont le nom doit respecter la convention :
Exemple : prj-davar-p-bq
/!\ 30 caractères max - 14 pour la structure (incluant 5 caractères aléatoires en fin) soit **16 disponibles**.
- Un groupe workspace dans lequel sont ajouté les primary et secondary contact
Ce groupe a des privilèges, il peut notamment supprimer des jeux de données.