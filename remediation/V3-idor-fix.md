# V3 — Remédiation IDOR

## 1. Vulnérabilité

La vulnérabilité V3 est une faille de type IDOR (Insecure Direct Object Reference).

Elle concerne l'accès à l'historique des commandes d'un utilisateur.

## 2. Cause

La vulnérabilité apparaît lorsque l'API récupère une commande uniquement à partir de son identifiant sans vérifier que cette commande appartient bien à l'utilisateur actuellement connecté.

Exemple de requête vulnérable :

```sql
SELECT * FROM orders
WHERE id = requestedId;
```

Dans ce cas, un utilisateur peut modifier l'identifiant de commande demandé et potentiellement accéder aux données d'une autre commande.

## 3. Correction proposée

La correction consiste à effectuer un contrôle d'autorisation côté serveur.

La requête doit vérifier simultanément :

* l'identifiant de la commande demandée ;
* l'identifiant de l'utilisateur connecté.

Exemple :

```sql
SELECT * FROM orders
WHERE userId = session.userId
AND id = requestedId;
```

Ainsi, l'API ne retourne la commande que si elle appartient réellement à l'utilisateur connecté.

## 4. Justification

Ce contrôle d'autorisation garantit qu'un utilisateur ne peut accéder qu'à ses propres commandes.

Même s'il devine ou modifie l'identifiant d'une commande dans l'URL, le serveur vérifie toujours que cette commande appartient à son compte.

Le contrôle doit être effectué côté serveur et ne doit pas uniquement reposer sur l'interface utilisateur.

## 5. Vérification

Pour vérifier la correction, il faut effectuer le test suivant :

1. Se connecter avec un utilisateur.
2. Identifier l'ID d'une commande appartenant à un autre utilisateur dans l'environnement de test.
3. Modifier l'ID de commande dans la requête ou dans l'URL.
4. Envoyer la requête.

### Résultat attendu

L'API doit refuser l'accès et retourner une erreur :

```text
HTTP 403 Forbidden
```

Les données de la commande appartenant à l'autre utilisateur ne doivent pas être retournées.

## 6. Cas particulier d'OWASP Juice Shop

Si le code source réel d'OWASP Juice Shop n'est pas modifié dans le cadre du TP, la correction peut être présentée comme une proposition de remédiation.

Le rapport doit préciser que la vulnérabilité a été analysée sur l'application vulnérable et que la correction proposée consiste à ajouter un contrôle d'autorisation côté serveur afin de vérifier que la ressource demandée appartient bien à l'utilisateur connecté.

## 7. Avant / Après

### Avant — requête vulnérable

```sql
SELECT * FROM orders
WHERE id = requestedId;
```

Le serveur vérifie uniquement l'identifiant de la commande.

### Après — requête sécurisée

```sql
SELECT * FROM orders
WHERE userId = session.userId
AND id = requestedId;
```

Le serveur vérifie à la fois l'identifiant de la commande et l'utilisateur connecté.

## 8. Conclusion

La vérification de propriété côté serveur permet d'empêcher un utilisateur d'accéder aux données d'un autre utilisateur en modifiant simplement un identifiant.

Cette mesure permet de corriger la vulnérabilité IDOR et garantit que les commandes retournées correspondent bien à l'utilisateur authentifié.
