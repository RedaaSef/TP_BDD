### **1. Contexte**  
CouchDB est une base de données NoSQL **orientée documents** développée par **Apache Software Foundation**. Elle est conçue pour faciliter la gestion, la réplication et la synchronisation de données dans des environnements distribués. CouchDB utilise le format **JSON** pour stocker les données, propose une **API RESTful** pour interagir avec les bases et repose sur des principes robustes tels que **ACID** et le paradigme **MapReduce** pour le traitement des requêtes.  

Cette base de données est particulièrement adaptée aux cas d'utilisation suivants :  
- Les applications **hors-ligne** nécessitant une synchronisation entre le client et le serveur.  
- Le traitement des **données IoT** provenant de plusieurs sources.  
- La gestion des systèmes **décentralisés** nécessitant la résolution automatique des conflits.  
- Le développement rapide d'applications **web et mobiles** grâce à son schéma flexible.  

---

### **2. Modèle de Données Documentaire**  

Contrairement aux bases SQL traditionnelles, CouchDB adopte une structure basée sur des documents **JSON**. Chaque document est autonome et possède :  
- Un identifiant unique (`_id`).  
- Un champ de révision (`_rev`) pour gérer les mises à jour.  
- Des paires **clé-valeur** permettant de stocker des informations sans structure rigide.  

#### **Exemple de document dans CouchDB** :  
```json
{
  "_id": "12345",
  "nom": "Bob Martin",
  "role": "Développeur",
  "skills": ["JavaScript", "Node.js", "CouchDB"],
  "projets": {
    "nom": "Gestion de tâches",
    "statut": "Terminé"
  }
}
```

Chaque document est indépendant, ce qui permet une grande flexibilité dans l’ajout ou la modification de champs.

---

### **3. Fonctionnement de CouchDB**

CouchDB repose sur les principes suivants :  

#### **3.1 API RESTful**  
Les opérations sur les bases CouchDB s'effectuent via des requêtes **HTTP** :  
- **GET** : Lire des documents ou des bases.  
- **POST** : Ajouter des documents.  
- **PUT** : Modifier des documents.  
- **DELETE** : Supprimer des documents.  

#### **3.2 Réplication**  
CouchDB permet une **réplication maître-maître**, idéale pour synchroniser des instances distantes. Cela garantit la cohérence des données sur plusieurs serveurs, même en cas de déconnexion temporaire.  

#### **3.3 MapReduce**  
CouchDB utilise **MapReduce** pour indexer et interroger les données efficacement.  
- La **fonction Map** extrait les informations en générant des paires clé-valeur.  
- La **fonction Reduce** agrège ces résultats pour produire une sortie finale.  

---

### **4. MapReduce avec CouchDB**

#### **4.1 Présentation de MapReduce**  
Le modèle **MapReduce** est une approche pour traiter de grandes quantités de données :  
1. **Map** : Génère des paires clé-valeur à partir des documents.  
2. **Reduce** : Agrège les clés pour obtenir des résultats consolidés.

---

#### **4.2 Exemple Général**  

Supposons une base de données contenant des documents de ventes de produits :  
```json
[
  { "produit": "Ordinateur", "quantite": 5 },
  { "produit": "Souris", "quantite": 10 },
  { "produit": "Ordinateur", "quantite": 3 }
]
```

##### **Fonction Map**  
La fonction Map extrait les ventes par produit :  
```javascript
function(doc) {
  emit(doc.produit, doc.quantite);
}
```

**Sortie intermédiaire** :  
```
Ordinateur: 5  
Souris: 10  
Ordinateur: 3  
```

##### **Fonction Reduce**  
La fonction Reduce additionne les quantités :  
```javascript
function(keys, values) {
  return sum(values);
}
```

**Sortie finale** :  
```
Ordinateur: 8  
Souris: 10  
```

---

#### **4.3 Création d'une Vue CouchDB**  

Les vues MapReduce sont définies dans un **design document** :  
```json
{
  "_id": "_design/produits_vue",
  "views": {
    "total_quantites": {
      "map": "function(doc) { emit(doc.produit, doc.quantite); }",
      "reduce": "_sum"
    }
  }
}
```

---

#### **4.4 Exemple 1 : Compter les Employés par Département**  

##### **Données**  
```json
[
  { "nom": "Alice", "departement": "Informatique" },
  { "nom": "Bob", "departement": "Ressources Humaines" },
  { "nom": "Charlie", "departement": "Informatique" }
]
```

##### **Fonction Map**  
```javascript
function(doc) {
  emit(doc.departement, 1);
}
```

##### **Fonction Reduce**  
```javascript
function(keys, values) {
  return sum(values);
}
```

**Résultat** :  
```
Informatique: 2  
Ressources Humaines: 1  
```

---

#### **4.5 Exemple 2 : Obtenir les Salaires Totaux par Département**  

##### **Données**  
```json
[
  { "nom": "Alice", "departement": "Finance", "salaire": 4000 },
  { "nom": "Bob", "departement": "Finance", "salaire": 3500 },
  { "nom": "Charlie", "departement": "IT", "salaire": 5000 }
]
```

##### **Fonction Map**  
```javascript
function(doc) {
  if (doc.salaire) {
    emit(doc.departement, doc.salaire);
  }
}
```

##### **Fonction Reduce**  
```javascript
function(keys, values) {
  return sum(values);
}
```

**Résultat** :  
```
Finance: 7500  
IT: 5000  
```

---

### **5. Déploiement CouchDB avec Docker**

CouchDB peut être facilement déployé avec Docker.

#### **5.1 Démarrer CouchDB**  
```bash
docker run -d --name couchdb -p 5984:5984 -e COUCHDB_USER=admin -e COUCHDB_PASSWORD=secret couchdb
```

#### **5.2 Accéder à l'interface Web**  
Ouvrez votre navigateur et accédez à :  
```http
http://localhost:5984/_utils/
```

---

### **6. Requêtes REST CouchDB**

#### **Créer une base de données**  
```bash
curl -X PUT http://admin:secret@localhost:5984/employes
```

#### **Ajouter un document**  
```bash
curl -X POST http://admin:secret@localhost:5984/employes -H "Content-Type: application/json" -d '{"nom":"Alice","departement":"IT"}'
```

#### **Récupérer tous les documents**  
```bash
curl -X GET http://admin:secret@localhost:5984/employes/_all_docs?include_docs=true
```

---

### **7. Cas d'Utilisation de CouchDB**

CouchDB est adapté pour :  
- **Applications mobiles** : Synchronisation offline/online.  
- **IoT** : Stockage distribué des données capteurs.  
- **Big Data** : Agrégation et indexation via MapReduce.  
- **Systèmes collaboratifs** : Gestion de conflits grâce à la réplication.  

---

### **8. Conclusion**  
