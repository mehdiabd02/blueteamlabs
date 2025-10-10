# ATT&CK — Operationalisation (Writeup)

**Contexte :**  
Vous êtes membre d’une équipe Blue Team et devez operationaliser le framework MITRE ATT&CK pour produire du renseignement et des règles défensives. Ce writeup compile les réponses aux scénarios fournis et propose rapidement des actions défensives.

---

## TL;DR
Focalisez les efforts de mitigation sur les techniques ATT&CK pertinentes à votre environnement cloud (ex : Azure AD / O365), corrélez anomalies réseau (ports inhabituels) avec groupes APT connus, et instrumentez la détection pour techniques d’abus d’identifiants (ex : Pass the Hash). ATT&CK facilite le mapping de détections -> contre-mesures -> playbooks.

---

## Questions & Réponses

1. **Votre entreprise dépend fortement d’Azure AD et Office 365. Quelle technique faut-il prioriser pour empêcher un attaquant d’effectuer des activités de Discovery s’il a des identifiants valides ?**  
   **Réponse:** `T1538`  
   *Remarque :* se concentrer sur la réduction des permissions, la journalisation des appels d’API et la détection des usages anormaux de comptes privilégiés.

2. **Vous avez détecté un flux de données inhabituel sur le port 4050. Quel groupe APT cela pourrait-il être ?**  
   **Réponse:** `G0099`  

3. **La matrice contient une liste de 9 techniques qui relèvent de la même tactique visant à pénétrer votre réseau. Quel est l’ID de la tactique ?**  
   **Réponse:** `TA0001`  
   *Remarque :* TA0001 correspond à *Initial Access*.

4. **Un logiciel empêche les utilisateurs d’accéder à leur compte en supprimant/désactivant le compte, en changeant le mot de passe, etc. Quel ID de logiciel/documentation ATT&CK correspond à ce comportement ?**  
   **Réponse:** `S0372`

5. **Utilisation de la technique ‘Pass the Hash’ pour contrôler des systèmes distants. Comment la détecter en entreprise ?**  
   **Réponse (résumé):** *Surveiller les nouveaux logons et les credentials utilisés dans les événements, et vérifier les incohérences.*  
   *Action concrète :* corréler `Logon` events (4624/4625), `NewLogon` fields, examiner les hachages/NTLM usage, détecter logons anormaux (heure, geolocation, source host), et alerter quand un hash est réutilisé par différents comptes/hôtes.

---

## Recommandations opérationnelles (rapide)
- **Instrumenter Cloud (Azure/O365) :** activer l’audit/logging (Sign-in logs, Azure AD audit), surveiller usages d’API et tâches de découverte (T1538).  
- **Least Privilege & MFA :** réduire privilèges, appliquer MFA pour comptes sensibles.  
- **Hunting & Correlation :** construire requêtes SIEM pour détecter `Pass the Hash` patterns, réutilisation de credentials, et activités de discovery après authentification.  
- **Threat Intel Mapping :** indexer IOCs/indicators par technique ATT&CK afin de produire règles et playbooks réutilisables.  
- **Simulation & Tests :** exécuter exercices (tabletop / purple team) pour valider détections contre T1538, TA0001, et S0372.

---

## Fichiers suggérés à ajouter au repo
- `attack_answers.md` — Q&A (extrait)  
- `detection_queries/` — requêtes SIEM pour Pass-the-Hash, anomalous sign-ins, unusual API calls  
- `playbooks/` — playbook Initial Access / Discovery / Credential Abuse

---

**Fin du writeup — ATT&CK operationalisation**
