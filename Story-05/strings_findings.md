# 📌 Story 5

## 🔍 Informations critiques

### Exposition des endpoints API

```xml
<string name="ACCOUNT_ENDPOINT">https://api.nickel.eu/customer-banking-api</string>
<string name="CUSTOMER_AUTHENTICATION_ENDPOINT">https://api.nickel.eu/customer-authentication-api</string>
<string name="PERSONAL_SPACE_API_ENDPOINT">https://api.nickel.eu/personal-space-api</string>
```

Ces URLs permettent d’identifier les différents services backend :

- API bancaire
- API d’authentification
- Espace personnel utilisateur

La présence de ces endpoints en clair facilite fortement la phase de reconnaissance pour un attaquant.

Un attaquant pourrait :

- Cartographier l’API
- Tester les endpoints
- Rechercher des vulnérabilités
- Automatiser des attaques

Même si exposer des endpoints côté client est courant, la sécurité doit être assurée côté serveur.

il faudrait mettre en place :

- Une Authentification et autorisation strictes
- Protection contre les appels abusifs
- Ne pas exposer d’endpoints inutiles
- un système de Logs

### Configuration Firebase

```xml
<string name="GCM_PROJECT_NUMBER">246766419677</string>
<string name="gcm_defaultSenderId">717748501407</string>
<string name="firebase_database_url">https://application-client-nickel.firebaseio.com</string>
```

Bien que ces informations ne soient pas des secrets à elles seules, elles facilitent l’accès au backend Firebase.
Un attaquant pourrait :

- Accéder directement à la base Firebase
- Exfiltrer des données sensibles
- Modifier ou injecter des données
- Automatiser l’exploitation

La sécurité Firebase doit être configurée côté serveur

il faudrait mettre en place :

- une authentification robuste
- Limiter les accès aux données avec un accès restreint par utilisateur
- Activer les protections supplémentaires comme une validation des données côté Firebase
- Auditer régulièrement la configuration

### Clé API Google exposée

```xml
<string name="google_api_key">AIzaSyBTgztvImsUfMWDa41PCrDWAj7dmyIDhUg</string>
```

Bien que cette clé ne soit pas un secret critique à elle seule, un attaquant peut tenter de l’exploiter si elle est mal configurée.

Un attaquant pourrait :

- céder à certaines APIs Google
- Rechercher des failles de configuration
- Abuser des ressources Google
- Collecter des métadonnées sur le projet Google Cloud

il faudrait mettre en place :

- Mettre en place des quotas d'appel APIs
- Surveiller l'utilisation via des logs
- Régénérer régulièrement les clés API