# BlueTeamLabs — The Report — Writeup

**Challenge:** The Report  
**Author:** BTLO  
**Difficulty:** Easy  
**OS (analysis):** Windows / Linux  
**Scenario:**  
Vous travaillez dans un SOC récemment établi. L’objectif de ce challenge est d’étudier un rapport de menace publié en 2022 et de proposer des recommandations pour renforcer le SOC.

---

## TL;DR
Le rapport couvre plusieurs attaques majeures de 2021-2022, notamment la supply chain `log4j`, les vulnérabilités sur Exchange Servers (`ProxyLogon`, `ProxyShell`), un RCE via un driver (`CVE-2021-34527`), et des campagnes de ransomware et de crypto-mining. Les adversaires utilisent des techniques MITRE (`T1059`) et des méthodes d’accès initial variées, incluant SEO malveillant et modèles d’affiliation.

---

## Questions & Answers

1. **Name the supply chain attack related to Java logging library in the end of 2021**  
   **Answer:** `log4j`

2. **Mention the MITRE Technique ID which affected more than 50% of the customers**  
   **Answer:** `T1059`

3. **Submit the names of 2 vulnerabilities belonging to Exchange Servers**  
   **Answer:** `ProxyLogon,ProxyShell`

4. **Submit the CVE of the zero day vulnerability of a driver which led to RCE and gain SYSTEM privileges**  
   **Answer:** `CVE-2021-34527`

5. **Mention the 2 adversary groups that leverage SEO to gain initial access**  
   **Answer:** `Gootkit,Yellow Cockatoo`

6. **In the detection rule, what should be mentioned as parent process if we are looking for execution of malicious js files**  
   **Answer:** `wscript.exe`

7. **Ransomware gangs started using affiliate model to gain initial access. Name the precursors used by affiliates of Conti ransomware group**  
   **Answer:** `Bazar,IcedID,Qbot`

8. **The main target of coin miners was outdated software. Mention the 2 outdated software mentioned in the report**  
   **Answer:** `JBoss,WebLogic`

9. **Name the ransomware group which threatened to conduct DDoS if they didn't pay ransom**  
   **Answer:** `Fancy Lazarus`

10. **What is the security measure we need to enable for RDP connections in order to safeguard from ransomware attacks?**  
    **Answer:** `MFA`

---

## Key Takeaways / Recommendations for SOC
- **Supply Chain Attacks:** surveiller les bibliothèques critiques (ex: `log4j`) et appliquer rapidement les patchs.  
- **Exchange Servers:** implémenter les correctifs pour ProxyLogon et ProxyShell, restreindre les accès externes.  
- **Driver RCE:** monitorer les alertes Windows pour les vulnérabilités critiques (`CVE-2021-34527`) et les exploits de type SYSTEM.  
- **Malware Initial Access:** mettre en place des règles de détection pour l’exécution de scripts JS via `wscript.exe`.  
- **Ransomware:** suivre les tendances d’affiliation (Bazar/IcedID/Qbot) et activer MFA pour RDP.  
- **Crypto-miners:** détecter les vulnérabilités logicielles obsolètes (JBoss, WebLogic) et surveiller l’usage non autorisé de ressources système.  
- **Threat Intel:** corréler les informations de rapports avec les logs internes pour améliorer les playbooks SOC.

---

*Writeup préparé pour dépôt GitHub — fichier : `the-report-writeup.md`*
