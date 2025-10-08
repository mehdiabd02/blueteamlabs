# TryHackMe — Phishing Analysis 2 — Writeup

**Challenge:** Phishing Analysis 2  
**Outils utiles:** Thunderbird, éditeur de texte, navigateur, URL2PNG, CyberChef (ou tout outil base64)  
**Objectif:** trier un email de phishing, extraire IOCs et preuves textuelles pour le triage.

---

## Résumé rapide (méthode)
1. Ouvrir le fichier `.eml` dans un éditeur de texte ou client mail (Thunderbird) pour voir en clair en-têtes et corps.  
2. Inspecter les en-têtes pour l’adresse expéditeur, destinataire, date et sujet.  
3. Extraire les URLs (bouton CTA, images), puis analyser leur cible (URL2PNG / navigateur en bac à sable).  
4. Si le corps est encodé (ex. base64), le décoder (CyberChef / base64 -d) pour lire le HTML/texte.  
5. Récupérer IOCs : domaines, chemins, images, comptes sociaux liés.

---

## Questions & Réponses (FAQ du challenge)

- **What is the sending email address?**  
  **Answer:** `amazon@zyevantoby.cn`

- **What is the recipient email address?**  
  **Answer:** `saintington73@outlook.com`

- **What is the subject line of the email?**  
  **Answer:** `Your Account has been locked`

- **What company is the attacker trying to imitate?**  
  **Answer:** `Amazon`

- **What is the date and time the email was sent? (As copied from a text editor)**  
  **Answer:** `Wed, 14 Jul 2021 01:40:32 +0900`

- **What is the URL of the main call-to-action button?**  
  **Answer:**  
https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Famaozn.zzyuchengzhika.cn%2F%3Fmailtoken%3Dsaintington73%40outlook.com&data=04%7C01%7C%7C70072381ba6e49d1d12d08d94632811e%7C84df9e7fe9f640afb435aaaaaaaaaaaa%7C1%7C0%7C637618004988892053%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=oPvTW08ASiViZTLfMECsvwDvguT6ODYKPQZNK3203m0%3D&reserved=0

- **Look at the URL using URL2PNG. What is the first sentence (heading) displayed on this site?**  
**Answer:** `the page that you are trying to access cannot be loaded.`

- **When looking at the main body content in a text editor, what encoding scheme is being used?**  
**Answer:** `base64`

- **What is the URL used to retrieve the company's logo in the email?**  
**Answer:**  https://images.squarespace-cdn.com/content/52e2b6d3e4b06446e8bf13ed/1500584238342-OX2L298XVSKF8AO6I3SV/amazon-logo?format=750w&content-type=image%2Fpng
  
- **One of the URLs contains a Facebook profile URL. What is the username of this account?**  
**Answer:** `amir.boyka.7`

---

## IOCs & recommandations rapides
- **Domains / Hosts to block / monitor:** `zyevantoby.cn`, `amaozn.zzyuchengzhika.cn`, any suspicious safelinks redirectors in context.  
- **Suspicious sender:** `amazon@zyevantoby.cn` (imitation Amazon).  
- **User awareness:** message urgent (account locked) — classique technique de phishing.  
- **Technicals:** corpuses encodés en base64 → décodage nécessaire pour inspection.  
- **Actions recommandées:** signaler & bloquer les domaines, ajouter règles de filtrage pour messages contenant safelinks vers these domains, sensibiliser l’utilisateur visé.

---

## Commandes / outils rapides
- Décoder base64 (CLI) :
```bash
# exemple pour décoder un bloc base64 copié dans body.b64
base64 -d body.b64 > body.html
