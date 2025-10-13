# BlueTeamLabs — Melissa — Writeup

**Challenge:** Melissa (W97M.Melissa.A)  
**Notes importantes :** Ce sample contient un vrai malware historique. **N'exécutez jamais** le document sur une machine non isolée. Faites l'analyse en VM hors ligne ou en environnement contrôlé (sandbox). Outils recommandés : `oledump.py`, éditeur texte, outils de hash & analysis statique.

---

## Contexte
Melissa est un macro-virus Word (mars 1999) qui se propage via Outlook en envoyant des emails contenant une copie du document infecté et un message social-engineering. L’objectif de ce challenge est d’extraire les détails du macro sans exécution (lecture statique des streams OLE).

---

## Méthode (sûre & rapide)
1. Ouvrir la VM d'analyse isolée.  
2. Extraire les streams OLE avec `oledump.py` :  
   ```bash
   oledump.py LIST.DOC
Findings & Answers

Stream number that contains the Melissa macro in LIST.DOC:
7

Version of Word that Melissa checks to enable macros from registry:
9.0

Email service targeted by Melissa:
Outlook

How many email addresses were collected:
50

String used by Melissa to identify whether a PC is infected (decides whether to collect emails):
... by Kwyjibo

Variable responsible for identifying the email username of the infected PC:
Application.UserName

Text in the email body used for spreading Melissa:
Here is that document you asked for ... don't show anyone else ;-)

Text that Melissa inserts in an open Word document:
Twenty-two points, plus triple-word-score, plus fifty points for using all my letters. Game's over. I'm outta here.
