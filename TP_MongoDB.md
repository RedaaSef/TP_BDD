
# **MongoDB avec Docker**

---
## Contexte
MongoDB est une base de données NoSQL orientée documents, conçue pour offrir flexibilité et performance. Contrairement aux bases de données relationnelles, MongoDB stocke les données sous forme de documents JSON (ou BSON en interne), ce qui permet une structure plus dynamique et facile à adapter. Cela en fait un choix idéal pour les applications modernes nécessitant une grande évolutivité et des schémas flexibles.

MongoDB est particulièrement utilisé dans des scénarios où la gestion de grands volumes de données et la rapidité des requêtes sont essentielles, comme :

    Les applications web et mobiles nécessitant des données dynamiques.
    Le stockage des journaux et des événements (logs).
    Les systèmes de gestion de contenu (CMS).
    Les analyses de données en temps réel et les tableaux de bord.
    Les plateformes de commerce en ligne pour gérer les catalogues produits.


## Démarrage


## **1. Installation de Docker**

### **Vérifier si Docker est installé :**
```bash
docker --version
```

## **2. Lancer MongoDB dans un conteneur Docker**

### **Exécuter MongoDB avec Docker :**
```bash
docker run --name mymongodb -d -p 28017:27017 -v $(pwd)/mongo_data:/data/db mongo:latest
```

- **`--name mymongodb`** : Nom du conteneur (ici, `mymongodb`).
- **`-d`** : Exécute le conteneur en arrière-plan.
- **`-p 28017:27017`** : Associe le port 28017 de l'hôte au port 27017 du conteneur.
- **`-v $(pwd)/mongo_data:/data/db`** : Persiste les données MongoDB localement.

### **Vérifier si MongoDB est actif :**
```bash
docker ps
```

---

## **3. Préparation des données**

1. **Créer un fichier `books.json` :**
   Exemple de contenu simplifié :
   ```json
   [
     { "title": "MongoDB Basics", "author": "John Doe", "year": 2021 },
     { "title": "Docker Essentials", "author": "Jane Smith", "year": 2022 }
   ]
   ```

2. **Placez ce fichier dans votre répertoire de travail.**

---

## **4. Importer les données dans MongoDB**

### **Copier le fichier dans le conteneur :**
```bash
docker cp books.json mymongodb:/books.json
```

### **Accéder au conteneur :**
```bash
docker exec -it mymongodb bash
```

### **Importer les données avec `mongoimport` :**
```bash
mongoimport --db library --collection books --file /books.json --jsonArray
```

- **`--db library`** : Nom de la base de données (`library`).
- **`--collection books`** : Nom de la collection (`books`).
- **`--file /books.json`** : Chemin du fichier JSON.
- **`--jsonArray`** : Pour un tableau JSON.

---

## **5. Accéder à MongoDB**

### **Ouvrir le shell MongoDB :**
```bash
docker exec -it mymongodb mongosh
```

### **Utiliser la base de données `library` :**
```javascript
use library
```

---

## **6. Exécuter des requêtes MongoDB**

### **1. Afficher tous les documents :**
```javascript
db.books.find()
```

### **2. Compter le nombre de livres :**
```javascript
db.books.countDocuments()
```

### **3. Afficher les livres publiés après 2021 :**
```javascript
db.books.find({ year: { $gt: 2021 } })
```

### **4. Trouver un livre avec un titre précis :**
```javascript
db.books.find({ title: "MongoDB Basics" })
```

### **5. Exclure le champ `_id` des résultats :**
```javascript
db.books.find({}, { _id: 0 })
```

### **6. Afficher uniquement les titres des livres :**
```javascript
db.books.find({}, { _id: 0, title: 1 })
```

### **7. Rechercher les livres par auteur :**
```javascript
db.books.find({ author: "Jane Smith" })
```

### **8. Compter les livres d’un auteur donné :**
```javascript
db.books.countDocuments({ author: "John Doe" })
```

### **9. Trouver les livres sans année spécifiée :**
```javascript
db.books.find({ year: { $exists: false } })
```

### **10. Rechercher les livres où l’auteur est "Jane Smith" ou l’année est 2022 :**
```javascript
db.books.find({ $or: [{ author: "Jane Smith" }, { year: 2022 }] })
```

---

## **7. Arrêter et nettoyer MongoDB**

### **Arrêter le conteneur :**
```bash
docker stop mymongodb
```

### **Supprimer le conteneur :**
```bash
docker rm mymongodb
```

---

## **Résumé des principales commandes**

- **Démarrage de MongoDB :** `docker run`
- **Importation des données :** `mongoimport`
- **Requêtes dans MongoDB :** `db.collection.find()`
- **Arrêter MongoDB :** `docker stop` et `docker rm`

Ce guide permet de configurer MongoDB avec Docker, importer des données JSON simples et exécuter les requêtes MongoDB les plus courantes.
