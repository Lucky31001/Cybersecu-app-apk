Rebuild un APK
----------
Pour signer un apk nous sommes passer par deux étapes étapes.


### Première étape:
Il a fallu créer une clé pour notre apk. Pour ce faire nous avons utilisé l'outil keytool disponible avec le JDE Java et nous avons entrer la commande suivante:

```keytool -genkey -v -keystore CodingFactory -alias alias_name -keyalg RSA -keysize 2048 -validity 10000```

Ce qui nous a donner un fichier nommé "CodingFactory" disponible dans le dossier story-08

## Deuxième étape:

Pour signer l'application maintenant nous avons mis l'apk rebuild ainsi que le fichier de la clé dans le meme dossier et executé la commande suivante:

```keytool -genkey -v -keystore [your keystore name] -alias alias_name -keyalg RSA -keysize 2048 -validity 10000```

Ce qui nous a donner le fichier d'apk signé disponible dans le dossier story-08

Nous avons ensuite executé la commande:

```apksigner verify --verbose --print-certs apk_rebuild_signed.apk```

Pour vérifier si le fichier était bien signé. Le résultat est disponible dans un screenshot dans le dossier story-08