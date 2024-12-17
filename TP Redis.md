# Redis: Introduction et Manipulations de Base (TP - NOSQL ) 

## Contexte
Redis est une base de données NoSQL rapide et performante, principalement utilisée pour stocker des paires clé-valeur. Contrairement aux bases de données relationnelles, Redis opère principalement en mémoire, ce qui en fait un choix idéal pour les systèmes nécessitant des temps de réponse faibles, comme le cache, les files d'attente ou encore les systèmes de publication/souscription.

Redis est souvent utilisé dans des scénarios où la rapidité est cruciale, comme :
- Les systèmes de cache.
- Les compteurs en temps réel (par exemple, le nombre de visiteurs).
- Les files d'attente pour la gestion des tâches asynchrones.
- Les systèmes de messagerie et notifications en temps réel.


---

## Démarrage

### Lancer le serveur Redis
Pour utiliser Redis, démarrez le serveur avec la commande suivante dans votre terminal :
```bash
redis-server
```

### Connexion au serveur avec Redis CLI

Redis est livré avec un outil CLI (Command Line Interface) qui permet d'interagir avec le serveur Redis. Pour vous connecter :
```bash
redis-cli
```
Une fois connecté, vous pouvez exécuter des commandes directement dans la console.

## Manipulations de Base

Redis offre une série de commandes simples pour manipuler des paires clé-valeur. Ces commandes sont les fondations de toute interaction avec Redis.

### Définir une clé

Pour définir une clé et lui attribuer une valeur, utilisez la commande `SET`. C'est l'opération la plus fondamentale dans Redis.

**Exemple :**
```bash
SET demo "Bonjour"
```
Redis répondra avec :
```
OK
```

La clé `demo` est maintenant stockée en mémoire avec la valeur `"Bonjour"`.

---

### Lire une clé

Pour récupérer la valeur associée à une clé, utilisez la commande `GET`. Cette commande lit directement la valeur stockée dans Redis.

**Exemple :**
```bash
GET demo
```
Résultat attendu :
```
"Bonjour"
```

---

### Supprimer une clé

Vous pouvez supprimer une clé et sa valeur associée avec la commande `DEL`. Une fois supprimée, la clé n'existe plus en mémoire.

**Exemple :**
```bash
DEL demo
```
Redis retourne :
```
(integer) 1
```

Cela signifie que la suppression a réussi. Si la clé n'existe pas, Redis retournera `0`.

---

### Manipulation des valeurs numériques

Redis simplifie la manipulation des valeurs numériques grâce aux commandes `INCR` et `DECR`. Ces commandes sont pratiques pour gérer des compteurs, comme le nombre de visiteurs d'un site.

- **Incrémenter une valeur :** Si la clé n'existe pas encore, Redis initialise sa valeur à `0` avant d'appliquer l'incrémentation.
```bash
INCR visitors
```

- **Décrémenter une valeur :**
```bash
DECR visitors
```

**Exemple complet :**
```bash
SET visitors "0"
INCR visitors
INCR visitors
GET visitors
```
Résultat attendu :
```
"2"
```

---

## Structures de Données

Redis prend en charge plusieurs structures de données avancées pour répondre à différents besoins. Voici les plus couramment utilisées.

### Listes

Les listes dans Redis sont des séquences ordonnées de valeurs. Elles sont parfaites pour gérer des files d'attente ou des journaux.

- **Ajouter des éléments :** Vous pouvez ajouter des éléments à gauche avec `LPUSH` ou à droite avec `RPUSH`.
- **Lire une liste :** La commande `LRANGE` permet de récupérer les éléments d'une liste.

**Exemple :**
```bash
RPUSH mylist "Base de données"
RPUSH mylist "Services Web"
LRANGE mylist 0 -1
```
Résultat attendu :
```
1) "Base de données"
2) "Services Web"
```

---

### Sets

Les sets sont des collections d'éléments uniques, non ordonnés. Ils sont utiles lorsque vous devez éviter les doublons.

- **Ajouter un élément :**
```bash
SADD myset "Augustin"
SADD myset "Inès"
```

- **Lire tous les éléments d'un set :**
```bash
SMEMBERS myset
```

**Exemple :**
```bash
SADD myset "Augustin"
SADD myset "Inès"
SADD myset "Augustin"  # Ignoré car déjà présent
SMEMBERS myset
```
Résultat attendu :
```
1) "Augustin"
2) "Inès"
```

---

### Sets Ordonnés (Sorted Sets)

Les sets ordonnés associent un score numérique à chaque élément, permettant de les classer.

- **Ajouter un élément avec un score :**
```bash
ZADD scores 19 "Augustin"
ZADD scores 18 "Inès"
```

- **Lire les éléments par ordre croissant :**
```bash
ZRANGE scores 0 -1
```

- **Lire les éléments par ordre décroissant :**
```bash
ZREVRANGE scores 0 -1
```

**Exemple :**
```bash
ZADD scores 19 "Augustin"
ZADD scores 18 "Inès"
ZRANGE scores 0 -1
```
Résultat attendu :
```
1) "Inès"
2) "Augustin"
```

---

### Hashs

Les hashs sont des structures de type objet où chaque champ est associé à une valeur. Ils sont idéaux pour représenter des entités complexes, comme des profils utilisateur.

- **Ajouter un champ dans un hash :**
```bash
HSET user:1 username "Augustin"
HSET user:1 age "25"
```

- **Lire tous les champs et valeurs :**
```bash
HGETALL user:1
```

**Exemple :**
```bash
HSET user:1 username "Augustin"
HSET user:1 age "25"
HSET user:1 email "augustin@gmail.com"
HGETALL user:1
```
Résultat attendu :
```
1) "username"
2) "Augustin"
3) "age"
4) "25"
5) "email"
6) "augustin@gmail.com"
```

---

### Publication/Souscription (Pub/Sub)

Redis prend en charge la communication en temps réel grâce à son système Pub/Sub (Publication/Souscription). Cela permet à plusieurs clients de communiquer via des canaux.

- **S'abonner à un canal :**
```bash
SUBSCRIBE mychannel
```

- **Publier un message sur un canal :**
```bash
PUBLISH mychannel "Bienvenue sur Redis Pub/Sub"
```

**Exemple :**
1. **Client 1 (abonné) :**
   ```bash
   SUBSCRIBE mychannel
   ```

2. **Client 2 (éditeur) :**
   ```bash
   PUBLISH mychannel "Un nouveau cours est disponible !"
   ```

**Résultat attendu (Client 1) :**
```
1) "mychannel"
2) "Un nouveau cours est disponible !"
```

---

## Persistance des Données

Par défaut, Redis stocke ses données en mémoire (RAM), ce qui signifie qu'elles sont volatiles. Si le serveur s'arrête, les données sont perdues. Pour garantir la persistance, configurez Redis pour sauvegarder les données sur disque via le fichier `redis.conf`.# TP_BDD
