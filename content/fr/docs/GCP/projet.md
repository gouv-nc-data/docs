---
title: Projet GCP
description: ressources crées avec un projet
date: 2023-10-25
weight: 1
---

Lors de la création d'un nouveau projet "type bigquery" plusieurs ressources sont automatiquement instanciées :
- Un dataset
- Un monitoring
- 3 groupes google workspace (accessible au même endroit que les autres applications GWS : Drive, gmail, etc)

Les groupes crés (par Terraform) ont chacun des buts précis, d'autres groupes plus spécifiques peuvent également être créé de la même façon que les autres gropupe GWS afin d'affiner les rôles des intervenants.

**grp-gcp-{direction}-bq-admin**
Ce groupe comporte les administrateurs de Bigquery, les membres ont les niveaux de droit les plus élevés sur l'outil, peuvent notamment supprimer la donnée. Ils ont accès à toute la donnée Bigquery du projet.
Ils ont égalementun rôle "editor" au niveau du projet.
Le nombre de membre de ce groupe doit être le plus réduit possible.

**grp-gcp-{direction}-bq-editor**
Les membres de ce groupe ont la possibilité de créer des dataset, des tables ou des requêtes.
Ils ont accès à toute la donnée Bigquery du projet.

**grp-gcp-{direction}-bq-viewer**
Les membres peuvent voir les données de projet et exécuter des requêtes (si elles ne modifient pas la donnée).
Ils ont accès à toute la donnée Bigquery du projet.
Il est possible d'ajouter des membre hors GNC à ce groupe.

Pour toute gestion plus précise, par exemple si certains utilisateur de doivent pouvoir voir qu'une partie de la donnée du projet il faudra soit créer un nouveau groupe, partager chaque donnée nécessaire avec ce groupe puis ajouter des membres soit partager directement des données avec une adresse mail (plus simple mais maintenance plus complexe).

# Module terraform de création de projet

![Image alt](./GCP/base_project.png)  

{{ with .Resources.GetMatch "base_project.png" }}
  <img src="data:{{ .MediaType.Type }};base64,{{ .Content | base64Encode }}">
{{ end }}

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
Exemple : prj-davar-p
/!\ 30 caractères max - 11 pour la structure (incluant 5 caractères aléatoires en fin) soit **19 disponibles**.
