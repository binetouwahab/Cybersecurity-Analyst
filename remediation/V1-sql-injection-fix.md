# V1 — Remédiation SQL Injection

## 1. Vulnérabilité

La vulnérabilité V1 est une injection SQL présente au niveau du formulaire de connexion.

## 2. Cause

La vulnérabilité apparaît lorsque le backend construit directement une requête SQL à partir des données saisies par l'utilisateur.

Exemple de code vulnérable :

```javascript
const query = "SELECT * FROM users WHERE email = '" + email + "'";
```

Dans ce cas, la valeur fournie par l'utilisateur est directement ajoutée à la requête SQL.

Un attaquant peut alors essayer d'injecter du SQL, par exemple :

```text
' OR 1=1--
```

La requête peut alors être interprétée différemment par la base de données.

## 3. Correction proposée

La correction consiste à utiliser des requêtes paramétrées ou un ORM afin de séparer les données utilisateur du code SQL.

Avec Sequelize, utilisé par OWASP Juice Shop, une recherche peut par exemple être effectuée ainsi :

```javascript
User.findOne({
    where: {
        email: email,
        password: hash(password)
    }
});
```

Cette approche évite de construire directement une requête SQL à partir de la saisie utilisateur.

## 4. Justification

Avec une requête paramétrée, la donnée saisie par l'utilisateur est considérée comme une valeur et non comme une partie du code SQL.

Ainsi, les caractères spéciaux contenus dans la saisie ne permettent pas de modifier la structure de la requête SQL.

Cette mesure permet donc de prévenir les injections SQL.

## 5. Vérification

Après correction, le test suivant doit être effectué sur le formulaire de connexion :

```text
' OR 1=1--
```

Le résultat attendu est que la connexion soit refusée normalement et que la saisie soit traitée comme une simple valeur.

Un nouveau scan de sécurité avec Semgrep ou OWASP ZAP peut également être réalisé afin de vérifier que l'alerte SQL Injection n'est plus détectée.

## 6. Cas particulier d'OWASP Juice Shop

Si le code source réel d'OWASP Juice Shop n'est pas modifié dans le cadre du TP, la correction peut être présentée sous forme de proposition de remédiation.

Dans ce cas, le rapport doit préciser que la vulnérabilité a été analysée sur l'application vulnérable et que la correction proposée consiste à remplacer les requêtes SQL construites par concaténation par des requêtes paramétrées ou par l'utilisation sécurisée de l'ORM.

## 7. Avant / Après

### Avant — code vulnérable

```javascript
const query = "SELECT * FROM users WHERE email = '" + email + "'";
```

### Après — approche sécurisée

```javascript
User.findOne({
    where: {
        email: email,
        password: hash(password)
    }
});
```

### Conclusion

L'utilisation de requêtes paramétrées ou d'un ORM avec des paramètres correctement gérés permet d'empêcher les données utilisateur d'être interprétées comme du code SQL et constitue une mesure essentielle contre les injections SQL.
