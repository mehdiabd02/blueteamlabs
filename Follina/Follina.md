# Blue Team Labs — Follina (Writeup)

**Challenge:** Follina  
**Auteur:** BTLO (Blue Team Labs)  
**Difficulty:** Easy  
**OS:** Windows / Linux (analysis)  
**Notes:** analyse statique & dynamique d’un échantillon exploitant la vulnérabilité connue « Follina » (CVE-2022-30190). Ce writeup est orienté détection / réponse (blue team).

---

## TL;DR
Un document Office (Office Open XML) contient une relation XML (`document.xml.rels`) qui référence un HTML externe. L’HTML charge une chaîne qui déclenche l’exécution via `msdt.exe` (pour MSDT). L’exploitation dépend de certains traitements HTML (taille du fichier > 4096 bytes). Détection recommandée : surveillance des créations de processus `msdt.exe` avec parent `WINWORD.EXE` (EventID 4688), blocage/filtrage des domaines malveillants et durcissement de la visualisation de documents.

---

## Récapitulatif des réponses (Challenge Q&A)
1. **SHA1 hash of the sample:** `06727ffda60359236a8029e0b3e8a0fd11c23313`  
2. **File type (VirusTotal):** `Office Open XML Document`  
3. **Extracted URL from sample:**  
   `https://www.xmlformats.com/office/word/2022/wordprocessingDrawing/RDF842l.html`  
4. **XML file storing the URL:** `document.xml.rels`  
5. **Byte threshold that avoids payload (< Number bytes):** `4096`  
6. **Process name that the sample will try to kill (if running):** `msdt.exe`  
7. **Detection rule (Windows Event ID 4688) — ProcessName, ParentProcessName:** `msdt.exe,WINWORD.EXE`  
8. **MITRE technique ID for Execution used by the sample:** `T1559`  
9. **Associated CVE:** `CVE-2022-30190`
