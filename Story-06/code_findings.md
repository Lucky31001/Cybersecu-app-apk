#  Rapport d’analyse de sécurité — Application Android


##  Méthodologie


Afin d’effectuer une analyse approfondie du code, une phase de 
rétro-ingénierie a été mise en place.


### 1. Extraction du bytecode


L’APK a été décompilé afin d’extraire les fichiers `.dex` (Dalvik Executable), contenant le bytecode de l’application.


### 2. Conversion en Smali


Les fichiers `.dex` ont été convertis en code Smali à l’aide de l’outil **Backsmali**, permettant une lecture bas niveau du fonctionnement de l’application.

> Le Smali est une représentation lisible du bytecode Dalvik, proche de l’assembleur, offrant une vision précise des instructions exécutées.


### 3. Reconstruction en Java


Le code Smali a ensuite été analysé et partiellement reconstruit en code Java via **JADX**, facilitant la compréhension de la logique applicative.

> Cette étape permet d’identifier plus rapidement les vulnérabilités liées à la logique métier et aux mauvaises pratiques de développement.


##  Objectifs de l’analyse


- Identifier des vulnérabilités logiques (authentification, autorisation, flux applicatif)
- Détecter des fuites de données sensibles
- Analyser les mécanismes de chiffrement et stockage sécurisé
- Mettre en évidence les mauvaises pratiques de développement
- Proposer des recommandations de correction


##  Structure du rapport


Chaque vulnérabilité identifiée sera documentée dans ce fichier


# Faille #1:


Donnée non encryptée dans le stockage Locale

```java
public static abstract class CipherResult<T> {
        public final T password;
        public final T username;

        public CipherResult(T t, T t2) {
            this.username = t;
            this.password = t2;
        }
    }
```
Une personne ayant accés au stockage du téléphone pourra récupérer les credentials de l'utilisateur !

Cette Faille est Critique (CVSS = 7,8/10 selon recherche de failles equivalente)

## 🔧 Patch — Faille #1

- Chiffrer les données sensibles avant stockage (ex: AES-256)
- Utiliser le Android Keystore System pour stocker les clés de chiffrement
- Ne jamais stocker de credentials en clair
- Utiliser des types sécurisés (ex: `char[]`) au lieu de `String`
- Implémenter un effacement mémoire après utilisation des données sensibles


# Faille #2:


Version Android

```java
package android.support.v4.app;
```

Certain aspect de l'app sont sous android V4 qui est vulnerable au stagefright.

Cette Faille est Critique (CVSS = 9/10 selon recherche de failles equivalente)


## 🔧 Patch — Faille #2

- Migrer de `android.support.*` vers AndroidX (`androidx.*`)
- Mettre à jour le `targetSdkVersion` vers une version récente (API 30+ minimum)
- Vérifier et corriger les dépendances vulnérables
- Réduire la surface d’attaque (désactiver composants inutiles)
- Appliquer les patchs de sécurité Android récents (OS + librairies)


# Faille #3:


Utilisation de l’authentification Basic (HTTP)

```java
public static String basic(String str, String str2, Charset 
charset) {
    return "Basic " + ByteString.encodeString(str + ":" + str2, 
    charset).base64();
}
```

L'application utilise un mécanisme d’authentification Basic, 
qui consiste à encoder les identifiants (username:password) en 
Base64.

Ce procédé n’est pas sécurisé, car le Base64 est facilement 
décodable et ne constitue pas un chiffrement.
Si la communication n’est pas strictement protégée (ex: HTTP ou 
TLS mal configuré), un attaquant peut intercepter et récupérer 
les identifiants en clair.

De plus, les identifiants sont manipulés directement sous forme 
de String, ce qui les laisse en mémoire et augmente le risque 
en cas de dump mémoire ou reverse engineering.

Cette Faille est Élevée (CVSS ≈ 7.5–8.5 selon le contexte 
d’exploitation)


## 🔧 Patch — Faille #3

- Interdire totalement les communications en HTTP (forcer HTTPS)
- Implémenter TLS avec certificate pinning
- Remplacer Basic Auth par OAuth2 ou JWT
- Ne jamais stocker ou manipuler les credentials en clair
- Implémenter une rotation de tokens avec expiration courte
