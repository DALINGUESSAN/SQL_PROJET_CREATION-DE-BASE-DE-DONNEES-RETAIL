#  Projet d'Analyse des Ventes & Segmentation Clients (PostgreSQL)

![SQL](https://img.shields.io/badge/Language-SQL-blue?style=for-the-badge&logo=sqlite&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![VS Code](https://img.shields.io/badge/IDE-VS_Code-007ACC?style=for-the-badge&logo=visual-studio-code&logoColor=white)
![Data Analysis](https://img.shields.io/badge/Focus-Data_Analysis_&_RFM-orange?style=for-the-badge)

##  Vue d'Ensemble & Mission

Ce projet a pour objectif d'analyser les performances commerciales d'une entreprise omnicanale et de segmenter sa base clients. En traitant l'historique des **ventes**, des **produits**, des **paiements** et des **clients**, cette étude permet de dégager des indicateurs clés de performance (KPIs) métier et d'orienter les stratégies marketing et logistiques.

---

##  Plan d'Exécution & Méthodologie

* **Étape 1 :** Comprendre la mission et définir les objectifs business.
* **Étape 2 :** Découvrir les données (Exploration préliminaire).
* **Étape 3 :** Organiser l'espace de travail sous VS Code & PostgreSQL.
* **Étape 4 :** Vérification, filtrage des transactions valides et nettoyage des données.
* **Étape 5 :** Répondre aux questions business :
  *  KPIs de Ventes (Chiffre d'affaires global, temporel, canaux).
  *  Analyse de Produits (Top/Flop produits, panier moyen).
  *  Analyse Clients & Segmentation RFM (Récence, Fréquence, Montant).
* **Étape 6 :** Extraction des Insights & Recommandations stratégiques.

---

##  Modèle de Données & Schéma Relationnel

Le projet repose sur 4 tables relationnelles :

| Table | Rôle / Description |
| :--- | :--- |
| `ventes` | Historique des transactions (quantités, montants, canal, dates). |
| `produits` | Catalogue des articles (nom, catégorie, prix, marge, stock). |
| `clients` | Profils clients (nom, localisation, canal d'acquisition, date d'inscription). |
| `paiements` | Statut des règlements (méthode, statut de la transaction). |

---

##  Script SQL Complet par Étape

### 1. DDL : Création de la Base de Données

```sql
> * Creation de la table client *

CREATE TABLE clients (
    id_client INT PRIMARY KEY,
    nom VARCHAR(255),
    pays VARCHAR(255),
    ville VARCHAR(255),
    date_inscription DATE,
    canal_acquisition VARCHAR(255)
);
```


```sql

> * Creation de la table produits *
CREATE TABLE produits (
    id_produit INT PRIMARY KEY,
    nom_produit VARCHAR(255),
    categorie VARCHAR(255),
    prix NUMERIC(10,2),
    marge NUMERIC(10,2),
    stock_initial INT
);
```

```sql
> * Creation de la table vente *
CREATE TABLE ventes (
    id_vente INT PRIMARY KEY,
    id_client INT REFERENCES clients(id_client),
    id_produit INT REFERENCES produits(id_produit),
    date_vente DATE,
    quantite INT,
    montant_total NUMERIC(10,2),
    channel VARCHAR(255)
);
```

```sql
CREATE TABLE paiements (
    id_paiement INT PRIMARY KEY,
    id_vente INT REFERENCES ventes(id_vente),
    methode VARCHAR(255),
    statut VARCHAR(255)
);
```

/* l' ensembles des elements de la base de données est integrer via posgresql , via importation /*

```sql
 SELECT * FROM clients
      LIMIT 10;
```
```sql
      SELECT * FROM paiements
      LIMIT 10;
```
```sql

      SELECT * FROM produits
      LIMIT 10;
```
```sql
       CREATE TABLE produits(
        id_produit INT PRIMARY KEY,
        nom_produit VARCHAR(255),
        categorie VARCHAR(255),
        prix INT(10,2),
        marge NUMERIC(10,2),
        stock_initial INT
    );
```
    
     CREATE TABLE produits(
        id_produit INT PRIMARY KEY,
        nom_produit VARCHAR(255),
        categorie VARCHAR(255),
        prix INT,
        marge NUMERIC(10,2),
        stock_initial INT
    );
    
    SELECT * FROM produits
      LIMIT 10;SELECT COUNT(*) AS total_clients
       FROM clients;SELECT COUNT(*) AS total_produits
       FROM produits;SELECT COUNT(*) AS total_ventes
       FROM ventes;SELECT COUNT(*) AS total_paiements
       FROM paiements;SELECT COUNT(*) AS nombre_produits
       FROM produits;SELECT COUNT(*) AS nombre_ventes
       FROM ventes;SELECT COUNT(*) AS nombre_paiements
       FROM paiements;SELECT * FROM clients
       LIMIT 5;
       
       --- voir les 5 premiers produits


       SELECT * FROM produits
       LIMIT 5;
       
       --- voir les 5 premières ventes
       SELECT * FROM ventes
       LIMIT 5;
       
       --- voir les 5 premiers paiements
       SELECT * FROM paiements
       LIMIT 5;
       
       --- voir les pays des clients
       SELECT DISTINCT pays
       FROM clients;
       
       --- voir les categories des produits
       SELECT DISTINCT categorie
       FROM produits;  
       
        --- voir les methodes de paiement
       SELECT DISTINCT methode
       FROM paiements;
       
       --- voir les methodes de paiement
       SELECT DISTINCT méthode
       FROM paiements;
       
       --- voir les canaux d'acquisition des clients
       SELECT DISTINCT canal_acquisition
       FROM clients;
       
       --- voir le montant total des ventes
       SELECT SUM(montant_total) AS montant_total_ventes
       FROM ventes;
       
       SELECT AVG(montant_total) AS montant_moyen_ventes
       FROM ventes;
       
       --- voir le montant maximum des ventes : le montant maximum des ventes est de 10000.00 euros
      
       SELECT MAX(montant_total) AS montant_maximum_ventes
       FROM ventes;
       
       --- voir le montant minimum des ventes : le montant minimum des ventes est de 10.00 euros
       
       SELECT MIN(montant_total) AS montant_minimum_ventes
       FROM ventes;

       --- PARTIE 2 : NETTOYAGE DES DONNEES
       --- Step 1 : filtrer les paiements reussis

       SELECT v.*
         FROM ventes v
         JOIN paiements p ON v.id_vente = p.id_vente
         WHERE p.statut = 'Success'  

         --- Step 2 : verifier les doublons 

         --- verifier les doublons dans la table ventes

         SELECT id_vente, COUNT(*) AS nombre_occurrences
         FROM ventes
         GROUP BY id_vente
            HAVING COUNT(*) > 1;

         ---Step 3 : verifier les valeurs nul ou manquantes

         --- verifier les clients sans pays

         SELECT *
         FROM clients
         WHERE pays IS NULL OR pays = '';

         ---step 4: preparer les colonnes importantes  
         --- total des ventes par pays
         SELECT c.pays, COUNT(v.id_vente) AS nb_ventes
         FROM ventes v
         JOIN clients c ON v.id_client = c.id_client
            GROUP BY c.pays
            ORDER BY nb_ventes DESC;

            --- PARTIE 3 : QUESTIONS BUSINESS :

            --- Step 1 : Chiffre d'affaire total : le chiffe d'affaire total est de 23152245.00 euros
            
            SELECT SUM(montant_total) AS chiffre_affaire_total
            FROM ventes v
            JOIN paiements p ON v.id_vente = p.id_vente
            WHERE p.statut = 'Success'
            ;

           --- step 2 : Chiffre d'affaire par mois  
           
              SELECT DATE_TRUNC('month', v.date_vente) AS mois, SUM(v.montant_total) AS chiffre_affaire_mensuel
                FROM ventes v
                JOIN paiements p ON v.id_vente = p.id_vente
                WHERE p.statut = 'Success'
                GROUP BY mois
                ORDER BY mois;

          --- step 3 : chiffre d'affaire par jour de la semaine 

SELECT 
    EXTRACT(DOW FROM v.date_vente) AS num_jour,
    TO_CHAR(v.date_vente, 'TMDay') AS jour_semaine,
    SUM(v.montant_total) AS chiffre_affaire_jour
FROM ventes v
JOIN paiements p ON v.id_vente = p.id_vente
WHERE p.statut = 'Success'
GROUP BY num_jour, jour_semaine
ORDER BY num_jour;

        --- step 4 : chiffre d'affaire par methode de paiement
SELECT 
    p.méthode AS methode_paiement,
    SUM(v.montant_total) AS chiffre_affaire_methode
FROM ventes v
JOIN paiements p ON v.id_vente = p.id_vente
WHERE p.statut = 'Success'
GROUP BY p.méthode
ORDER BY chiffre_affaire_methode DESC;

--- PARTIE 5 : ANALYSE PRODUITS : best sellers,top

-- produits les plus vendus en volume

SELECT 
    p.nom_produit AS produit,
    SUM(v.quantite) AS quantite_totale_vendue
FROM ventes v
JOIN produits p ON v.id_produit = p.id_produit
GROUP BY p.nom_produit
ORDER BY quantite_totale_vendue DESC
LIMIT 10;

-- panier moyen par produit : le panier moyen est de  euros

SELECT 
    p.nom_produit AS produit,
    AVG(v.montant_total) AS panier_moyen
FROM ventes v
JOIN produits p ON v.id_produit = p.id_produit
GROUP BY p.nom_produit
ORDER BY panier_moyen DESC
LIMIT 10;


-- produits avec faible vente : 

SELECT
    p.nom_produit AS produit,
    SUM(v.quantite) AS quantite_totale_vendue
FROM ventes v
JOIN produits p ON v.id_produit = p.id_produit
GROUP BY p.nom_produit
HAVING SUM(v.quantite) < 130  ---> ou juste retirer cette ccondition
ORDER BY quantite_totale_vendue ASC
LIMIT 10;


-- PARTIE 6 : ANALYSE CLIENTS 

-- Chiffre d'affaire par clients 

SELECT 
    c.nom AS client,
    SUM(v.montant_total) AS chiffre_affaire_client  

-- nombre de vente  par ville

SELECT
    c.ville AS ville,
    COUNT(v.id_vente) AS nombre_ventes  

-- step: RFM simplifié 
 

--clients le plus recent 

SELECT
    c.nom AS client,
    MAX(v.date_vente) AS derniere_achat 

-- clients qui depense le plus

SELECT 
    c.nom AS client,
    SUM(v.montant_total) AS total_depense   





