# 📊 Chicago Crime Analytics – Datamart & Modélisation en Étoile (Google BigQuery)

## 📌 Présentation du Projet
Ce projet consiste en la conception, la modélisation et la mise en œuvre d'un **Datamart décisionnel** sur les données publiques des crimes de la ville de Chicago (`bigquery-public-data.chicago_crime.crime`).

L'objectif principal est de transformer un jeu de données brut non normalisé en un **modèle en étoile (Star Schema)** sous **Google BigQuery**, optimisé pour l'analyse OLAP et le reporting BI.

---

## 🏗️ Architecture & Modélisation (Kimball)

Le modèle repose sur une table de faits centrale connectée à **4 tables de dimensions** via des clés de substitution deterministes (`Surrogate Keys`) générées par hachage (`FARM_FINGERPRINT`) :

* 📊 **Table de faits :**
  * `DM_FAIT_INCIDENT_REPORT` : contient l'ensemble des événements criminels, les mesures métiers (`is_arrest`, `is_domestic`, etc.) et les clés étrangères référençant les 4 dimensions.

* 🌐 **Tables de dimensions :**
  * **`DM_VW_POLICE_INFO`** (`SK_police_info`) : découpage administratif et sectorisation policière (`district`, `beat`).
  * **`DM_VW_INCIDENT_TYPE`** (`SK_incident_type`) : classification de haut niveau des incidents (`fbi_code`, `Primary_type`).
  * **`DM_VW_CRIME_DESCRIPTION`** (`SK_crime_descriprion`) : qualification détaillée du délit et codification IUCR (`iucr`, `description`).
  * **`DM_VW_AREA`** (`SK_area_id`) : dimension spatiale et géographique fine (`community_area`, `ward`, `block`, `location`, `x_coordinate`, `y_coordinate`).

---

## 🛠️ Stack Technique & Compétences Démontrées
* **Entrepôt de Données :** Google BigQuery (GCP)
* **Langage :** SQL ANSI / BigQuery SQL
* **Modélisation :** Modèle en Étoile (Dimensional Modeling - Ralph Kimball)
* **Data Quality & Integrity :**
  * Génération de clés de substitution (`SK_`) uniques et déterministes à l'aide de `CAST(ABS(FARM_FINGERPRINT(CONCAT(IFNULL(...)))) AS INT64)`.
  * Sécurisation des valeurs nulles dans la construction des hashs pour éviter la création de clés orphelines.
  * Validation à 100% de l'intégrité référentielle entre la table de faits et les 4 dimensions sur 8,6M+ d'enregistrements.

---

## 📚 Dictionnaire des Dimensions

| Nom de la Dimension | Clé Primaire (`SK_`) | Attributs Métier | Source |
| :--- | :--- | :--- | :--- |
| **`DM_VW_POLICE_INFO`** | `SK_police_info` | `district`, `beat` | `chicago_crime.crime` |
| **`DM_VW_INCIDENT_TYPE`** | `SK_incident_type` | `fbi_code`, `Primary_type` | `chicago_crime.crime` |
| **`DM_VW_CRIME_DESCRIPTION`** | `SK_crime_descriprion` | `iucr`, `description` | `chicago_crime.crime` |
| **`DM_VW_AREA`** | `SK_area_id` | `community_area`, `ward`, `block`, `location`, `x_coordinate`, `y_coordinate` | `chicago_crime.crime` |

---

## 📂 Structure du Repository

```text
├── README.md
├── docs/
│   └── architecture_schema_etoile.png
└── sql/
    ├── Dimension/      # Scripts DDL/DML des 4 dimensions (POLICE_INFO, INCIDENT_TYPE, CRIME_DESCRIPTION, AREA)
    ├── FAIT/           # Script DDL/DML de la table de faits (DM_FAIT_INCIDENT_REPORT)
    ├── 03_quality_tests/   # Requêtes de vérification d'intégrité (COUNT, JOIN integrity checks)
    └── 04_views/           # Vues analytiques OLAP pour la Business Intelligence