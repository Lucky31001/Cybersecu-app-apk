# Politique de securite mobile

## 1. Objectif

Definir les exigences minimales de securite pour concevoir, developper, tester, signer, publier et maintenir une application mobile securisee.

## 2. Principes directeurs

1. Security by design: la securite est integree des la conception.
2. Zero trust: aucune entree/client/reseau n'est implicitement fiable.
3. Defense in depth: controles cumulatifs client, transport, backend, monitoring.
4. Least privilege: permissions, acces API, acces donnees strictement minimaux.
5. Traceabilite: toute decision securite doit etre auditable.

## 3. Exigences de developpement

### 3.1 Secrets et credentials
- Interdit de stocker des credentials, tokens, mots de passe ou cles privees dans le code, les ressources, ou la documentation.
- Toute cle API client doit etre consideree comme potentiellement exposee et strictement restreinte cote fournisseur.
- Les secrets doivent etre geres via coffre-fort de secrets (CI/CD ou secret manager dedie).
- Rotation obligatoire des secrets au moins tous les 90 jours ou immediatement en cas d'exposition.

### 3.2 Communication reseau
- HTTPS obligatoire pour 100% des flux.
- `android:usesCleartextTraffic` doit etre `false` en production.
- Configuration `networkSecurityConfig` obligatoire avec interdiction explicite de HTTP.
- Certificate pinning requis pour domaines critiques (authentification, paiement, donnees sensibles).

### 3.3 Chiffrement et donnees sensibles
- Chiffrement authentifie obligatoire (AES-GCM ou equivalent).
- Interdiction des schemas de chiffrement sans integrite (ex: CBC sans MAC robuste).
- Les donnees sensibles au repos doivent etre chiffrees via Android Keystore.
- Les logs ne doivent jamais contenir de donnees sensibles (PII, tokens, secrets).

### 3.4 Durcissement applicatif
- `minSdkVersion` doit viser une base maintenue et securisee (cible >= 23 sauf exception approuvee).
- Obfuscation/minification activee en release (R8/ProGuard).
- Detection root/emulateur/tampering pour scenarios critiques (proportionnee au risque metier).
- Validation stricte des entrees cote client et cote serveur.

## 4. Exigences backend et API

- Authentification forte (OAuth2/OIDC/JWT signe) et autorisation fine par role/ressource.
- Rate limiting, anti-automation et protection contre abus d'API obligatoires.
- Validation serveur de toutes les donnees recues du mobile.
- Firebase et services cloud: acces anonyme interdit sauf justification metier approuvee.
- Journalisation de securite centralisee et alerting sur comportements anormaux.

## 5. Signature, build et release

- Keystore de signature stocke hors depot source, dans un stockage securise avec controle d'acces fort.
- Acces au keystore limite aux personnes autorisees et aux pipelines approuves.
- Signature release uniquement via pipeline CI/CD de confiance.
- Verification systematique de la signature et de l'integrite avant distribution.
- SBOM et scan de dependances obligatoires avant publication.

## 6. Gouvernance des dependencies

- Inventaire des dependencies maintenu et versionne.
- Scan CVE automatise a chaque merge vers branche principale.
- Correctifs critiques appliques sous 7 jours, eleves sous 30 jours.
- Dependances non maintenues interdites sauf derogation formelle.

## 7. Tests securite obligatoires

- SAST sur chaque Pull Request.
- Analyse statique mobile (MAST) a chaque release candidate.
- DAST/API tests sur environnements de preproduction.
- Pentest manuel cible au minimum 1 fois par an ou a chaque changement majeur.
- Toute vulnerabilite critique bloque la release jusqu'a correction ou mitigation approuvee.

## 8. Gestion des vulnerabilites

- Triage sous 48h apres detection.
- SLA de remediation:
  - Critique: 72h
  - Elevee: 14 jours
  - Moyenne: 45 jours
  - Faible: 90 jours
- Plan de mitigation documente si correction immediate impossible.
- Re-test obligatoire avant cloture.

## 9. Journalisation, supervision et reponse a incident

- Logs securite centralises, horodates et proteges contre alteration.
- Alertes temps quasi reel pour acces anormaux, echec auth massif, abus API.
- Procedure d'incident avec roles clairs (incident manager, tech lead, secops, communication).
- En cas de fuite de cle/secret: revocation immediate, rotation, analyse d'impact, communication interne.

## 10. Conformite et responsabilites

- Product Owner: priorisation des actions securite dans le backlog.
- Lead Dev Mobile: application technique de la politique.
- SecOps/AppSec: controles, revues, outillage et validation.
- Toute derogation doit etre documentee, datee, approuvee, et limitee dans le temps.

## 11. Checklist de conformite release (minimum)

- [ ] Pas de secret/credential dans code, ressources, docs.
- [ ] `usesCleartextTraffic=false` et HTTP bloque.
- [ ] Chiffrement sensible conforme (AES-GCM ou equivalent).
- [ ] Keystore protege hors depot, acces controle.
- [ ] Scans SAST/MAST/CVE sans finding critique ouvert.
- [ ] Journalisation et alerting operationnels.
- [ ] Preuves d'audit stockees (commandes, sorties, version, date).

## 12. Revision de la politique

Cette politique est revisee au minimum tous les 12 mois ou apres incident majeur, changement architectural significatif, ou evolution reglementaire.
