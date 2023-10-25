---
title: Transfert de données de base à Big Query
description: ressources crées avec un projet
date: 2023-10-25
weight: 5
---

# Module de transfert de données depuis une base vers Big Query

[Sources sur github](https://github.com/gouv-nc-data/data-gitops/tree/main/modules/dataproc_to_bigquery)

## Déclaration du spoke
Si la base de données source est en interne, il faut alors y accéder par le vpn et nécessite donc la création d'un VPC Spoke,.
Ce VPC a un subnet spécifique qui est autorisé à se connecter à la base

{{< highlight terraform "linenos=table,linenostart=1" >}}
module "spoke-vpc-iceo" {
  source     = "./modules/spoke_project"
  project_id = module.dam-datawarehouse.project_id
  subnet     = "172.24.147.0/25"
}{{< / highlight >}}

## Déclaration de l'url jdbc
L'url JBDC utilisée par dataproc doit être déclarée en tant que secret dans le projet (prj-dinum-p-secret-mgnt)[https://github.com/gouv-nc/secret-management-gitops]

## Déclaration d'une migration

{{< highlight terraform "linenos=table,linenostart=1" >}}
module "iceo-dataproc" {
  source                = "./modules/dataproc_to_bigquery"
  project_id            = module.dam-datawarehouse.project_id
  group_name            = local.denc_group_name
  region                = var.region
  dataset_name          = "iceo"
  schedule              = "0 0 1 1,7 *" # le premier janvier et le premier juillet
  type_database         = "oracle"
  notification_channels = module.dam-datawarehouse.notification_channels
  jdbc-url-secret-name  = "iceo-jdbc-url-secret"
  queries = {
    "sailors" = {
      bigquery_location = "sailors"
      query             = <<EOT
SELECT *
        FROM
            tcrew c 
EOT
    },
    "ships" = {
      bigquery_location = "ships"
      query             = <<EOT
SELECT *
             FROM "ICEO"."TSHIP" 
EOT
    }
  }
}
{{< / highlight >}}
