# Resumé des vulnérabilité haute

 - App can be installed on a vulnerable unpatched Android version 4.4-4.4.4
 - Clear text traffic is Enabled For App[ android:usesCleartextTraffic=true]
 - The App uses the encryption mode CBC with PKCS5/PKCS7 padding. This configuration is vulnerable to padding oracle attacks.

# Analyse de vulnérabilités – Application Android

## 1. Support des versions Android obsolètes (4.4 – 4.4.4)

### Description
L'application peut être installée sur des versions anciennes d’Android (API 19 – KitKat), qui ne sont plus maintenues ni sécurisées par le fournisseur.

### Risques
Ces versions contiennent de nombreuses vulnérabilités connues, notamment :
- Failles d’élévation de privilèges
- Contournement du sandbox Android
- WebView vulnérable
- Implémentations TLS obsolètes

Un attaquant peut exploiter ces failles pour compromettre l’environnement d’exécution de l’application.

### Impact
- Compromission des données de l’application
- Exécution de code malveillant
- Vol de tokens ou de sessions

### Recommandations
- Définir une version minimale sécurisée :
  ```gradle
  minSdkVersion 21
  ```
- Idéalement :
  ```gradle
  minSdkVersion 23
  ```

---

## 2. Autorisation du trafic en clair (Cleartext Traffic)

### Description
L’attribut suivant est activé dans le manifeste :
```xml
android:usesCleartextTraffic="true"
```

Cela autorise l’application à envoyer et recevoir des données via HTTP non sécurisé.

### Risques
Les communications ne sont pas chiffrées, ce qui permet :
- Attaques Man-in-the-Middle (MITM)
- Interception de données sensibles
- Modification des requêtes/réponses

### Impact
- Vol de credentials (login, mot de passe)
- Exposition de tokens de session
- Altération des données échangées

### Exemple d’exploitation
Sur un réseau Wi-Fi public, un attaquant peut intercepter le trafic HTTP et récupérer ou modifier les données échangées entre l’application et le serveur.

### Recommandations
- Désactiver le trafic en clair :
  ```xml
  android:usesCleartextTraffic="false"
  ```
- Forcer HTTPS pour toutes les communications
- Mettre en place une configuration de sécurité réseau (`networkSecurityConfig`)
- Implémenter le certificate pinning

---

## 3. Utilisation de AES-CBC avec padding PKCS5/PKCS7

### Description
L’application utilise un mode de chiffrement AES en mode CBC avec un padding PKCS5/PKCS7.

### Risques
Cette configuration est vulnérable à des attaques de type **Padding Oracle** si :
- Les erreurs de padding sont observables
- Le système retourne des réponses différentes selon le type d’erreur

### Impact
Un attaquant peut :
- Déchiffrer des données sensibles sans connaître la clé
- Manipuler des messages chiffrés
- Extraire des informations sensibles (tokens, données utilisateur)

### Exemple d’exploitation
L’attaquant modifie un message chiffré et observe les réponses du serveur :
- Padding valide → comportement différent
- Padding invalide → erreur

Cela permet de reconstruire les données bloc par bloc.

### Recommandations

#### Solution recommandée
Utiliser un mode de chiffrement authentifié :
```text
AES-GCM
```

#### Alternatives (si CBC est obligatoire)
- Implémenter un schéma **Encrypt-then-MAC (HMAC)**
- Uniformiser les messages d’erreur
- Ne jamais exposer d’informations sur les erreurs de padding

---

## Conclusion

Ces vulnérabilités exposent l’application à des risques significatifs, notamment :
- Interception de données
- Compromission de la confidentialité
- Attaques cryptographiques

Il est fortement recommandé de corriger ces points afin d’assurer un niveau de sécurité conforme aux standards actuels.