---
title: GCP Cloud Run
description: Hébergement d'une fonction python avec schedule
date: 2023-10-25
weight: 5
---
# Module terraform de création de la fonction

[Sources sur github](https://github.com/gouv-nc-data/data-gitops/tree/main/modules/function_to_bigquery)

## Déclaration d'un projet

{{< highlight terraform "linenos=table,linenostart=1" >}}
module "bmfl-function" {
  source                = "./modules/function_to_bigquery"
  project_id            = module.davar-datawarehouse.project_id
  project_name          = "bmfl"
  group_name            = local.davar_group_name
  region                = var.region
  schedule              = "30 23 * * *"
  direction             = local.davar_name
  notification_channels = module.davar-datawarehouse.notification_channels
}
{{< / highlight >}}

## Resources crées
- Compte de service avec les droits d'éxecution de la fonction
- Un bucket si la variable create_bucket est à true
- Une instance cloud run non accessible hors réseau du projet
- Un workflow pour déclencher le cloud run
- Un job scheduler qui déclenche le workflow
- Un dépôt docker registry dans lequel sera poussé l'image Docker
- Un dépot github basé sur (le template)[https://github.com/gouv-nc-data/gcp-function-template]
- Une supervision de l'instance Cloud Run qui en cas d'erreur notifie le groupe workspace créé avec le projet

## Cas d'usages
* DBAF
  * Creation des prt 

Tous les mois des fichiers spreadsheet sont créés à partir d'une source de données dans un autre spreadsheet

* DAVAR
  *  Bulletin mensuelle des fruits et légumes

Intégration de plusieurs fichiers sources sur un drive vers Big Query

* DTEFPNC
  * Chômage partiel et Autorisation de travail

Export de projets jira liés à des téléservices vers Big Query
