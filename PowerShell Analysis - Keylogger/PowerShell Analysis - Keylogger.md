# PowerShell Analysis — Keylogger — Writeup

**Challenge:** PowerShell Analysis - Keylogger  
**Outils utilisés :** éditeur de texte, PowerShell (lecture statique), strings/grep, VirusTotal (hash), YARA/EDR pour détection  
**Objectif :** analyser un script PowerShell suspect et extraire IOCs / comportement (sans exécution).

---

## TL;DR
Un script PowerShell malveillant agit comme keylogger : il importe `user32.dll` pour capter frappes, écrit les frappes dans un fichier texte dans un répertoire `temp`, et exfiltre/alerte via e‑mail en utilisant les identifiants inclus dans le script. Le script utilise SMTP sur le port `587` avec un compte Gmail.

---

## Méthodologie (brief)
1. Ouvrir le fichier PowerShell avec un éditeur (ne pas l’exécuter sur la machine hôte non isolée).  
2. Rechercher chaînes évidentes : adresses e‑mail, mots de passe, ports, appels `Add-Type`/`DllImport`, fichiers écrits (`.txt`), et fonctions de socket/SMTP.  
3. Calculer le hash SHA256 du fichier (`sha256sum`) et vérifier sur VirusTotal si besoin.  
4. Documenter IOCs et comportements observés.

---

## Questions & Réponses (challenge)

- **What is the SHA256 hash value for the PowerShell script file?**  
  **Answer:** `e0b7a2ad2320ac32c262aeb6fe2c6c0d75449c6e34d0d18a531157c827b9754e`

- **What email address is used to send and receive emails?**  
  **Answer:** `chaudhariparth454@gmail.com`

- **What is the password for this email account?**  
  **Answer:** `yjghfdafsd5464562!`

- **What port is used for SMTP?**  
  **Answer:** `587`

- **What DLL is imported to help record keystrokes?**  
  **Answer:** `user32.dll`

- **What directory is the generated txt file put in?**  
  **Answer:** `temp`
