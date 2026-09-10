# V2 — Remédiation XSS

## 1. Vulnérabilité

La vulnérabilité V2 est une attaque Cross-Site Scripting (XSS) présente au niveau de la fonctionnalité de recherche.

## 2. Cause

La vulnérabilité apparaît lorsque le texte saisi par l'utilisateur est réinjecté directement dans le HTML de la page sans être correctement encodé ou échappé.

Par exemple, si une application réinjecte directement la valeur saisie dans le HTML, un attaquant peut fournir du contenu contenant des balises HTML ou du JavaScript.

Exemple de donnée malveillante :

```html
<iframe src="javascript:alert('XSS')">
```

Si cette donnée est interprétée comme du HTML au lieu d'être considérée comme du texte, le navigateur peut tenter d'exécuter le code.

## 3. Correction proposée

La correction consiste à encoder ou échapper toute donnée provenant de l'utilisateur avant de l'afficher dans la page HTML.

Les caractères spéciaux tels que `<`, `>`, `"`, `'` et `&` doivent être correctement encodés lorsqu'ils sont affichés comme du contenu.

Il faut également éviter d'insérer directement des données utilisateur avec des mécanismes tels que `innerHTML` lorsque cela n'est pas nécessaire.

Dans un framework front-end comme Angular, l'échappement est généralement effectué automatiquement lors de l'affichage des données. Il faut cependant être particulièrement vigilant lors de l'utilisation de `[innerHTML]` ou de mécanismes permettant d'insérer du HTML dynamique.

## 4. Justification

L'encodage des sorties permet d'empêcher le navigateur d'interpréter les caractères spéciaux comme du code HTML ou JavaScript.

Par exemple, au lieu d'interpréter :

```html
<iframe src="javascript:alert('XSS')">
```

comme une balise HTML, l'application doit l'afficher comme du texte.

Ainsi, le contenu fourni par l'utilisateur n'est pas exécuté par le navigateur.

## 5. Vérification

Le test suivant doit être effectué dans le champ de recherche :

```html
<iframe src="javascript:alert('XSS')">
```

### Résultat attendu

Le contenu doit être affiché comme du texte brut.

Aucune fenêtre `alert` ne doit apparaître et aucun code JavaScript ne doit être exécuté.

Une capture d'écran du résultat doit être enregistrée dans :

```text
screenshots/v2-fix-verification.png
```

## 6. Cas particulier d'OWASP Juice Shop

Si le code source réel d'OWASP Juice Shop n'est pas modifié dans le cadre du TP, cette correction peut être présentée comme une proposition de remédiation.

Le rapport doit alors préciser que la vulnérabilité a été identifiée lors du test de l'application vulnérable et que la correction proposée consiste à encoder les sorties utilisateur et à éviter l'insertion non sécurisée de HTML dynamique.

## 7. Avant / Après

### Avant — comportement vulnérable

La donnée utilisateur est réinjectée directement dans le HTML :

```javascript
element.innerHTML = recherche;
```

### Après — approche sécurisée

La donnée doit être affichée comme du texte :

```javascript
element.textContent = recherche;
```

Cette approche empêche le navigateur d'interpréter la valeur utilisateur comme du HTML.

## 8. Conclusion

L'encodage des sorties utilisateur et l'utilisation de mécanismes d'affichage sûrs permettent d'empêcher l'interprétation de données utilisateur comme du code HTML ou JavaScript et réduisent ainsi le risque d'attaque XSS.
