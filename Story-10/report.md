# Rapport final de securite mobile - Story-10

## 1. Contexte et objectif

Ce rapport presente une synthese securite mobile basee sur l'analyse documentaire des artefacts techniques du projet Android.
Objectif: fournir une vision exploitable des risques avec constats, preuves, et recommandations priorisees.

## 2. Methodologie

Cette synthese est basee sur l'analyse des fichiers suivants:

- `Story-01/report.md`
- `Story-02/report.md`
- `Story-03/report.md`
- `Story-04/Story4.md`
- `Story-05/strings_findings.md`
- `Story-06/code_findings.md`
- `Story-07/Story7.md`
- `Story-08/Story8.md`
- `Story-09/firebase_check.md`

Niveaux de severite utilises: Critique, Elevee, Moyenne, Faible.

## 3. Findings

### F-01 - Trafic en clair autorise (`usesCleartextTraffic=true`)
- Severite: Elevee
- Source: `Story-02/report.md`
- Description: Le rapport indique que l'application autorise HTTP non chiffre.
- Risque: MITM, interception/modification de donnees sensibles, vol de session.

### F-02 - Compatibilite Android obsolete (API 19)
- Severite: Moyenne
- Source: `Story-02/report.md`
- Description: L'application peut tourner sur Android 4.4-4.4.4 non maintenu.
- Risque: exploitation de failles OS connues, affaiblissement des garanties de securite.

### F-03 - Usage de AES-CBC + PKCS5/PKCS7
- Severite: Moyenne
- Source: `Story-02/report.md`
- Description: Mode de chiffrement potentiellement exposable aux attaques de type padding oracle selon implementation.
- Risque: decryption partielle de messages, manipulation de donnees chiffrees.

### F-04 - Exposition d'endpoints backend et d'identifiants Firebase/Google dans l'APK
- Severite: Moyenne
- Source: `Story-05/strings_findings.md`, `Story-09/firebase_check.md`
- Description: URLs d'API, URL Firebase et cle Google API visibles dans les ressources.
- Risque: facilitation de la reconnaissance, abus d'API si restrictions insuffisantes.

### F-05 - Gestion de signature/keystore a risque operationnel
- Severite: Moyenne
- Source: `Story-08/Story8.md`
- Description: Le fichier de keystore (`CodingFactory`) est conserve dans le dossier de travail et les commandes de signature sont documentees de facon incoherente.
- Risque: mauvaise pratique de gestion des secrets de signature, erreur de release, exposition de cle de signature.

### F-06 - Cohesion documentaire et preuves inegales
- Severite: Faible
- Source: `Story-01/report.md`, `Story-03/report.md`, `Story-04/Story4.md`, `Story-07/Story7.md`, `Story-08/Story8.md`
- Description: Plusieurs rapports contiennent des commandes heterogenes (Windows/Linux), formulations approximatives et preuves parfois indirectes (captures au lieu de sorties textuelles).
- Risque: difficulte d'audit, reproductibilite reduite, ambiguite sur les resultats.

### F-07 - Stockage potentiel de credentials non proteges en memoire/stockage local
- Severite: Critique
- Source: `Story-06/code_findings.md`
- Description: Le code presente une structure manipulant `username` et `password` en clair, avec un risque d'exposition locale et en memoire.
- Risque: compromission de credentials utilisateur, usurpation de compte.

### F-08 - Mecanisme Basic Auth observe
- Severite: Elevee
- Source: `Story-06/code_findings.md`
- Description: Presence d'un mecanisme `Basic` avec concat `username:password` puis Base64.
- Risque: fuite d'identifiants si transport non strictement securise, exposition accrue en cas d'interception.

### F-09 - Dependance legacy `android.support.v4.*`
- Severite: Moyenne
- Source: `Story-06/code_findings.md`
- Description: Usage d'imports legacy `android.support.*`, indiquant une base potentiellement ancienne.
- Risque: dette technique, durcissement securite plus difficile et risque de dependances obsoletes.

## 4. Preuves

### P-01 - Cleartext traffic active
Extrait de `Story-02/report.md`:

```xml
android:usesCleartextTraffic="true"
```

### P-02 - API minimale obsolete rapportee
Extrait de `Story-02/report.md`:

```text
App can be installed on a vulnerable unpatched Android version 4.4-4.4.4
```

### P-03 - Crypto CBC/PKCS mentionnee
Extrait de `Story-02/report.md`:

```text
The App uses the encryption mode CBC with PKCS5/PKCS7 padding.
```

### P-04 - Endpoints/Firebase/Google API key exposes
Extraits de `Story-05/strings_findings.md`:

```xml
<string name="ACCOUNT_ENDPOINT">https://api.nickel.eu/customer-banking-api</string>
<string name="firebase_database_url">https://application-client-nickel.firebaseio.com</string>
<string name="google_api_key">AIzaSyBTgztvImsUfMWDa41PCrDWAj7dmyIDhUg</string>
```

### P-05 - Verification Firebase restrictive
Extrait de `Story-09/firebase_check.md`:

```json
{
  "error" : "Permission denied"
}
```

### P-06 - Presence d'un keystore dans le depot de travail
Observation de l'arborescence: `Story-08/CodingFactory`.
Contexte associe: generation et usage du keystore decrits dans `Story-08/Story8.md`.

### P-07 - Manipulation de credentials en clair dans une structure applicative
Extrait de `Story-06/code_findings.md`:

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

### P-08 - Presence d'un schema Basic Auth
Extrait de `Story-06/code_findings.md`:

```java
public static String basic(String str, String str2, Charset 
charset) {
    return "Basic " + ByteString.encodeString(str + ":" + str2, 
    charset).base64();
}
```

### P-09 - Trace de dependance legacy Android support
Extrait de `Story-06/code_findings.md`:

```java
package android.support.v4.app;
```

## 5. Recommandations

### 5.1 Recommandations explicitement presentes dans les sources

1. Desactiver le trafic en clair.
   - Positionner `android:usesCleartextTraffic="false"`.
   - Source: `Story-02/report.md`

2. Forcer HTTPS pour toutes les communications.
   - Source: `Story-02/report.md`

3. Mettre en place une configuration de securite reseau.
   - Utiliser `networkSecurityConfig`.
   - Source: `Story-02/report.md`

4. Mettre en place le certificate pinning.
   - Source: `Story-02/report.md`, `Story-06/code_findings.md`

5. Rehausser la version minimale Android.
   - `minSdkVersion 21` minimum recommande.
   - `minSdkVersion 23` ideal.
   - Source: `Story-02/report.md`

6. Remplacer AES-CBC/PKCS par un chiffrement authentifie.
   - Recommandation principale: `AES-GCM`.
   - Si CBC obligatoire: `Encrypt-then-MAC (HMAC)` + erreurs uniformes + aucune fuite d'information sur padding.
   - Source: `Story-02/report.md`

7. Durcir la securite des endpoints/API exposes.
   - Authentification et autorisation strictes.
   - Protection contre les appels abusifs.
   - Ne pas exposer d'endpoints inutiles.
   - Mettre en place des logs.
   - Source: `Story-05/strings_findings.md`

8. Durcir la configuration Firebase.
   - Authentification robuste.
   - Acces restreint par utilisateur.
   - Validation des donnees cote Firebase.
   - Audit regulier de la configuration.
   - Source: `Story-05/strings_findings.md`
   - Verification associee: `Story-09/firebase_check.md` indique actuellement `Permission denied` pour un acces anonyme.

9. Durcir l'usage de la cle API Google exposee.
   - Mettre en place des quotas d'appel API.
   - Surveiller l'utilisation via logs.
   - Regenerer regulierement les cles API.
   - Source: `Story-05/strings_findings.md`

10. Proteger les credentials en memoire et au stockage.
   - Chiffrer les donnees sensibles avant stockage.
   - Utiliser Android Keystore pour les cles.
   - Ne jamais stocker les credentials en clair.
   - Preferer des types plus securises que `String` pour les secrets quand possible.
   - Source: `Story-06/code_findings.md`

11. Remplacer Basic Auth par un mecanisme moderne.
   - Migrer vers OAuth2 ou JWT.
   - Ajouter rotation de tokens et expiration courte.
   - Source: `Story-06/code_findings.md`

12. Moderniser les composants legacy Android.
   - Migrer `android.support.*` vers AndroidX.
   - Mettre a jour SDK cible et dependances.
   - Source: `Story-06/code_findings.md`

### 5.2 Recommandations complementaires (non explicites dans les sources)

Les points ci-dessous ne sont pas explicitement recommandes dans les `.md` sources, mais restent des bonnes pratiques a considerer:

1. Retirer le keystore des dossiers projet et le stocker dans un coffre-fort de secrets.
2. Mettre une verification CI de secrets (pre-commit + scan CI).
3. Renforcer la protection backend via WAF/API gateway.
4. Ajouter un cycle trimestriel formalise de revue securite mobile (SAST/DAST/MAST).

## 6. Matrice de priorisation

| ID | Finding | Severite | Probabilite | Priorite |
|---|---|---|---|---|
| F-01 | Cleartext traffic actif | Elevee | Elevee | P1 |
| F-05 | Gestion keystore risquee | Moyenne | Elevee | P1 |
| F-07 | Credentials en clair (memoire/stockage) | Critique | Moyenne | P1 |
| F-08 | Basic Auth observe | Elevee | Moyenne | P1 |
| F-02 | Support Android obsolete | Moyenne | Moyenne | P2 |
| F-03 | AES-CBC/PKCS | Moyenne | Moyenne | P2 |
| F-04 | Endpoints/keys exposes | Moyenne | Moyenne | P2 |
| F-09 | Dependances legacy Android support | Moyenne | Moyenne | P2 |
| F-06 | Qualite de preuve/documentation | Faible | Moyenne | P3 |

## 7. Conclusion

Le niveau de risque global est eleve en raison de faiblesses techniques touchant le transport, l'authentification et la protection des donnees sensibles.
La correction des priorites P1 reduit significativement l'exposition immediate.

## 8. Livrable DoD

Le livrable attendu pour cloturer la story est:
- `rapport_final.pdf`

Commande suggeree pour generer le PDF depuis ce fichier Markdown:

```bash
pandoc report.md -o rapport_final.pdf
```
