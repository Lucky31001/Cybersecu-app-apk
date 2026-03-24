# Rapport de Pentest

## 1. Résumé exécutif

Lors de l'analyse de l'APK, une URL Firebase a été identifiée dans les ressources de l'application. Une tentative d'accès direct à la base de données via une requête HTTP a été effectuée.

Le test a confirmé que l'accès public à la base de données est restreint. Aucune fuite de données n'a été observée à ce stade.

---

## 2. Détail de la découverte

### 2.1 Extraction d'informations sensibles

Dans les fichiers de configuration de l'application :

```xml
<string name="firebase_database_url">https://application-client-nickel.firebaseio.com</string>
```

- **Type** : Information exposée dans l'APK
- **Risque potentiel** : Accès non autorisé à une base Firebase si mal configurée

---

### 2.2 Test d'accès à la base Firebase

Commande utilisée :

```bash
curl https://application-client-nickel.firebaseio.com/.json
```

Réponse obtenue :

```json
{
  "error" : "Permission denied"
}
```

---

## 3. Analyse de sécurité

- La base Firebase est **correctement sécurisée** contre les accès anonymes.
- Les règles de sécurité Firebase empêchent l'accès sans authentification.
- Aucune donnée sensible n'a été exposée via cet endpoint.

---

## 6. Impact

- **Impact actuel** : Faible
- **Risque potentiel** :
  - Si les règles Firebase sont modifiées (erreur de configuration), la base pourrait devenir accessible publiquement.
  - L'exposition de l'URL peut faciliter des attaques automatisées ou du brute-force sur les règles.

---