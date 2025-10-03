# BlueTeamLab — Injection Series Part 4 — Writeup

**Challenge:** Injection Series Part 4  
**Author:** BTLO  
**Difficulty:** Easy  
**OS (analysis):** Windows / Linux  
**Notes:** reverse engineering statique/dynamique du binaire fourni pour comprendre son comportement (process/technique d'injection, téléchargement et exécution en mémoire).

---

## TL;DR
Le sample lance un processus (`notepad.exe`) via `CreateProcessA` en mode suspendu, télécharge un exécutable depuis un domaine contrôlé (`somec2.server`) vers `C:\Windows\Temp\exp.exe`, réalise des opérations bas-niveau NT (démappe/charge section), alloue de la mémoire, écrit du code puis met à jour l’entry point et reprend le thread (technique d’injection/reflective execution). Technique MITRE associée : `T1055.002` (Process Injection — Create Remote Thread / Suspension variant).

---

## Méthodologie (rapid)
- Charger le binaire dans un désassembleur (IDA / Ghidra).  
- Rechercher les appels API de création de processus, network/download, et fonctions NTDLL.  
- Identifier la séquence : CreateProcessA (CREATE_SUSPENDED), téléchargement via PowerShell `Invoke-WebRequest`, opérations ntdll (unmap), allocation/memory-write, SetThreadContext + ResumeThread.  
- Confirmer les chaînes et domaines embarqués (`somec2.server`) et le chemin fichier (`C:\Windows\Temp\exp.exe`).

---

## Questions & Answers (format requis)

1. **What is the process that would be first spawned by the sample? And what is the API used?**  
   **Answer:** `notepad.exe, CreateProcessA`

2. **The value 4 has been pushed as a parameter to this API, what does that denote?**  
   **Answer:** `Create_Suspended`

3. **What is the domain that the malware tries to connect?**  
   **Answer:** `somec2.server`

4. **What is the cmdlet used to download the file and what is the path of the file stored?**  
   **Answer:** `Invoke-Webrequest, c:\windows\temp\exp.exe`

5. **Just after the file download instructions, a function from ntdll has been loaded and invoked by the sample. What is the function name?**  
   **Answer:** `NtUnmapViewOfSection`

6. **After the allocation of memory and writing the data into the allocated memory. What are the 2 APIs used to update the entry point and resume the thread?**  
   **Answer:** `SetThreadContext, ResumeThread`

7. **What is the MITRE ID for this technique implemented in this sample?**  
   **Answer:** `T1055.002`

---

## IOCs & Artifacts
- Domain: `somec2.server`  
- Drop path: `C:\Windows\Temp\exp.exe`  
- Observed process creation: `CreateProcessA` creating `notepad.exe` in suspended state  
- NTDLL call: `NtUnmapViewOfSection`  
- Injection APIs: `SetThreadContext`, `ResumeThread`  
- MITRE: `T1055.002` (Process Injection — CreateRemoteThread/Thread-context manipulation variant)

---

## Detection & Hunting suggestions
- **Process creation monitoring (Event ID 4688 / ProcessCreate)**: alert when `CreateProcessA` spawns `notepad.exe` from unusual parents or when Notepad spawns with suspicious command-line or with associated file writes to `%TEMP%`.  
- **Monitor PowerShell / cmdline**: detect `Invoke-WebRequest` downloading to `%TEMP%` or `C:\Windows\Temp`.  
- **NTDLL / suspicious API patterns:** look for processes calling `NtUnmapViewOfSection` and then `SetThreadContext`/`ResumeThread`. These sequences are strong indicators of process hollowing / thread context injection.  
- **EDR rules:** flag `SetThreadContext`/`ResumeThread` usage that targets suspended processes created by userland programs (esp. `notepad.exe`) or when parent process is a document handler.  
- **Network:** block / sinkhole domain `somec2.server` and alert on any outbound HTTP(S) to it.

---

## Mitigations & Remediation
- Block known malicious domains at DNS/proxy.  
- Apply application allowlisting (AppLocker/WDAC) to prevent unsigned executables from launching or being loaded into protected processes.  
- Instrument EDR to detect process hollowing / thread context modification patterns.  
- Enforce least-privilege and monitor write activity to `C:\Windows\Temp` directories.  
- Educate teams about suspicious downloads and unexpected child processes.

---

## Final notes
Ce challenge illustre une technique d’injection courante : créer un processus en mode suspendu, démapper/remplacer son contenu, écrire du code en mémoire, réparer le contexte d’exécution puis reprendre le thread — permettant d’exécuter du code arbitraire sous le contexte d’un processus légitime (évasion de détection). Dans un environnement réel, corréler API/WinEvent logs avec trafic réseau et activités de fichiers est essentiel pour détecter ce type d’attaque.

---
