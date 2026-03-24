# Vérification de la signature de l'apk

## Vérification du sha256

Pour vérifier cette information, nous devons utiliser la commnde sha256sum sur notre fichier app.apk :

```
    sha256sum app.apk
```

le résultat de cette commande nous autentifie que le fichier est belle et bien signé en sha256 :

```
    f01f08c8b6e2fe4612e81bc7e3a3ba9440dad0d7a962f4e67640390dd721d528  app.apk
```