# 🌳 SIG Arbres — Documentation technique

> **M. Jean-Marie Prévost**
> Mars 2026 · Un seul fichier HTML · Sans serveur requis · Mobile & desktop

---

## 📋 Sommaire

1. [Ce que fait l'application](#1--ce-que-fait-lapplication)
2. [La demande de départ](#2--la-demande-de-départ-et-comment-jy-ai-répondu)
3. [Les langages utilisés](#3--les-langages-de-code-utilisés)
4. [Les fonctions développées](#4--les-fonctions-développées--expliquées-simplement)
5. [Personnaliser pour Moissy-Cramayel](#5--personnaliser-pour-moissy-cramayel-et-le-bureau-détudes)
6. [Déploiement sur un serveur](#6--déployer-sur-un-serveur-web)
7. [Personnaliser les couleurs & l'apparence](#7--personnaliser-les-couleurs-et-lapparence)
8. [Pistes d'évolution](#8--pistes-dévolution-suggérées)

---

## 1 · Ce que fait l'application

L'application est un **outil d'inventaire d'arbres sur le terrain**, utilisable directement depuis un smartphone, une tablette ou un ordinateur. Son nom technique est *SIG Arbres* — SIG signifie *Système d'Information Géographique*.

En termes simples : vous êtes sur le terrain, face à un arbre, vous ouvrez l'application dans le navigateur de votre téléphone, vous touchez la carte à l'endroit de l'arbre (ou vous activez le GPS), vous remplissez un formulaire rapide, et le point est enregistré immédiatement sur la carte et dans la mémoire de votre appareil. Plus besoin de carnet papier ni de logiciel lourd.

**Fonctionnalités principales :**

| Côté carte | Côté saisie & données |
|---|---|
| 📍 Carte interactive (Leaflet) | 📋 Formulaire espèce / état / hauteur |
| 🛰️ Vue satellite Google | 📂 Liste de tous les arbres saisis |
| 🗺️ Vue OpenStreetMap | ✏️ Modification / suppression |
| ➕ Mode ajout point (clic ou GPS) | 📤 Export CSV (Excel) |
| 🌳 Marqueurs arbres personnalisés | 📷 Export image A4 paysage |

> **💡 Aucune installation requise** — L'application tient dans un seul fichier `index.html`. Il suffit de l'ouvrir dans un navigateur web (Chrome, Safari, Firefox…) — sur téléphone, tablette ou ordinateur.

---

## 2 · La demande de départ et comment j'y ai répondu

La demande initiale était de créer un outil de terrain léger, utilisable par le bureau d'études, pour géolocaliser et qualifier des arbres sans dépendre d'un logiciel SIG professionnel lourd (type QGIS ou ArcGIS).

| Besoin exprimé | Ce que j'ai développé |
|---|---|
| Travailler sur le terrain avec un smartphone | Interface responsive, adaptée portrait et paysage, avec des boutons suffisamment grands pour les doigts |
| Placer un arbre sur une carte | Clic sur la carte en « mode ajout » ou GPS automatique, marqueur rouge en attente de validation |
| Saisir les caractéristiques de l'arbre | Formulaire structuré : type, espèce latine, date de plantation, hauteur, état sanitaire, observations libres |
| Base d'espèces d'arbres française | Menu déroulant avec +80 espèces classées par famille (Chêne, Tilleul, Pin, Cèdre…) et possibilité de saisir une espèce non listée |
| Récupérer les données | Export CSV (compatible Excel), partage par e-mail ou WhatsApp, export image carte A4 paysage (JPEG) |
| Retravailler les données entre deux sessions | Sauvegarde automatique dans le navigateur (localStorage) — les arbres sont toujours là à la réouverture |
| Utiliser en déplacement sans connexion | Application 100 % autonome — seules les tuiles de carte nécessitent du réseau ; les données restent locales |
| Identité visuelle bureau d'études | Palette forêt/écorce, typographie soignée, copyright « Bureau d'Études — M. Prévost » en pied de page |

> **🏢 Contexte : Moissy-Cramayel, Seine-et-Marne** — La carte est centrée par défaut sur les coordonnées `48.6167° N, 2.5833° E`. Dès que vous ouvrez l'application, vous tombez sur votre territoire.

---

## 3 · Les langages de code utilisés

L'application repose sur trois langages qui constituent la base de toute page web, réunis dans un seul fichier.

| Langage | Rôle |
|---|---|
| 🏗️ **HTML5** | Structure de la page |
| 🎨 **CSS3** | Apparence visuelle |
| ⚙️ **JavaScript** | Fonctionnement interactif |

### HTML5 — la structure

C'est le squelette de la page. Il décrit tous les éléments visibles : l'en-tête, la carte, le panneau latéral, le formulaire, la liste des arbres, les modales d'édition et d'export. HTML décrit *quoi* afficher, mais pas *comment* ça ressemble.

### CSS3 — l'apparence

C'est la partie qui donne son allure à l'application : les couleurs forêt, les polices, les tailles, les animations, l'adaptation mobile. Une palette de couleurs nommées est définie (`--forest`, `--leaf`, `--amber`…) et réutilisée partout — si vous changez une couleur, elle se met à jour dans toute l'application automatiquement.

### JavaScript — le comportement

C'est le cerveau de l'application. Tout ce qui « fait quelque chose » est écrit en JavaScript : le clic sur la carte, la géolocalisation GPS, la sauvegarde, l'export CSV, la génération de l'image, etc. Du JavaScript pur, sans framework externe compliqué.

### Bibliothèques externes

| Bibliothèque | Version | Rôle |
|---|---|---|
| **Leaflet.js** | 1.9.4 | Carte interactive open source. Affiche les tuiles OSM et Google Satellite, gère le zoom, les marqueurs, les popups. Référence mondiale pour les cartes web légères. |
| **html2canvas** | 1.4.1 | Prend une « photo » de la carte telle qu'elle apparaît à l'écran et la transforme en fichier JPEG téléchargeable. Alimente la fonction d'export image A4. |

> **🌐 Ces bibliothèques sont chargées depuis un CDN.** Elles ne sont pas stockées dans le fichier lui-même mais téléchargées automatiquement depuis internet à l'ouverture. Si vous travaillez hors ligne, il faudra les intégrer localement (voir section Déploiement).

---

## 4 · Les fonctions développées — expliquées simplement

### 🗺️ Initialisation et gestion de la carte

| Fonction | Description |
|---|---|
| **Initialisation Leaflet** | Crée la carte centrée sur Moissy-Cramayel au zoom 14. Active les contrôles de zoom. Deux fonds disponibles : OpenStreetMap et Google Satellite. |
| **Sélecteur de fond de carte** | Les boutons OSM / Satellite en bas à gauche. Au clic, l'ancien fond est retiré, le nouveau chargé, une notification apparaît brièvement. |
| `makeIcon(color)` | Crée l'icône d'arbre verte dessinée en SVG : un feuillage en ellipses superposées et un tronc marron. S'adapte à n'importe quelle couleur. |
| `makeUserIcon()` | Crée le point vert représentant votre position GPS en temps réel : un cercle avec halo pulsant pour être facilement repérable. |

### 📍 Placement et géolocalisation

| Fonction | Description |
|---|---|
| **Mode ajout (clic carte)** | Quand vous activez « ➕ Ajouter point », le curseur passe en croix. Le prochain clic pose un marqueur rouge temporaire et affiche les coordonnées dans le formulaire. |
| `locateUser()` | Utilise le GPS du téléphone pour trouver votre position. La carte se centre sur vous, un marqueur vert apparaît, et la précision (ex. ±5 m) est affichée. Fonctionne en temps réel. |
| `makePendingMarker()` | Pose le cercle rouge sur la carte quand un point a été cliqué mais pas encore enregistré. Indicateur visuel : « ce point n'est pas encore sauvegardé ». |
| `setCoordDisplay()` | Met à jour les cases « Latitude » et « Longitude » en haut du formulaire avec les coordonnées du point cliqué ou GPS. |

### 📋 Formulaire et saisie

| Fonction | Description |
|---|---|
| `doSave()` | **Fonction centrale.** Lit toutes les valeurs du formulaire, crée un objet « arbre » avec un identifiant unique (horodatage), l'ajoute à la liste, le sauvegarde, place le marqueur sur la carte et met à jour les statistiques. |
| **Enregistrer & suivant** | Variante terrain : après enregistrement, le formulaire se vide automatiquement, le mode ajout se réactive — prêt pour le point suivant en un seul geste. |
| `clearForm()` | Vide tous les champs du formulaire et supprime le marqueur rouge temporaire. |
| `initDateSelects()` | Gère le sélecteur de date en trois parties (jour / mois / année) et le pré-remplit avec la date du jour. |

### 📂 Gestion des espèces (nomenclature botanique)

| Fonction | Description |
|---|---|
| `onVarieteSelect()` | Gère le menu déroulant d'espèces. Si « ✏ Autre — saisir manuellement » est sélectionné, un champ texte libre apparaît en dessous. |
| `getVarieteValue()` | Récupère la valeur saisie — soit le nom latin depuis le menu, soit le texte libre tapé manuellement. |
| `setVarieteValue()` | Utilisée lors de l'édition : remet la bonne espèce dans le formulaire, et active le champ libre si l'espèce ne figure pas dans la liste standard. |

### 📃 Liste et édition

| Fonction | Description |
|---|---|
| `renderList()` | Génère la liste de toutes les fiches d'arbres dans le panneau latéral. Chaque carte affiche l'emoji de l'espèce, le nom, les coordonnées, et les boutons ✏️ et ✕. |
| `selectTree(id)` | Au clic sur un arbre dans la liste, la carte se centre sur lui, son popup s'ouvre. |
| `openEditModal()` | Ouvre la fenêtre flottante d'édition pré-remplie avec toutes les données de l'arbre. |
| `saveEditModal()` | Sauvegarde les modifications, met à jour la carte et la liste, ferme la fenêtre. |
| `deleteTree()` | Supprime définitivement un arbre après confirmation. Retire le marqueur de la carte et met à jour la sauvegarde locale. |

### 💾 Sauvegarde et export

| Fonction | Description |
|---|---|
| `saveLocal()` | Sauvegarde tous les arbres dans la mémoire permanente du navigateur (localStorage). Les données restent disponibles même après fermeture de la page ou redémarrage. |
| `buildCSV()` | Génère un fichier structuré avec toutes les données : id, type, espèce, date de plantation, latitude, longitude, hauteur, état sanitaire, observations, date de saisie. S'ouvre directement dans Excel. |
| `buildTextSummary()` | Génère un résumé texte lisible de l'inventaire pour l'envoi par e-mail. |
| **Partage natif (Web Share API)** | Sur mobile, active le menu de partage natif du téléphone pour envoyer le fichier CSV par WhatsApp, AirDrop, e-mail, Google Drive, etc. |

### 📷 Export image A4

| Fonction | Description |
|---|---|
| `openPrintOverlay()` | Ouvre l'aperçu de l'export image. Une mini-carte Leaflet se crée dans une composition A4 paysage avec en-tête, légende, statistiques et pied de page. |
| `generateJpeg(mode)` | Utilise html2canvas pour capturer la composition A4, puis propose de télécharger le JPEG ou de le partager. Le fichier est nommé automatiquement avec la date du jour. |

### 📱 Interface mobile

| Fonction | Description |
|---|---|
| `togglePanel()` | Masque ou affiche le panneau latéral. Sur mobile portrait, il se glisse vers le bas comme un tiroir. |
| **Drag handle (tiroir mobile)** | Vous pouvez glisser le bord supérieur du panneau avec le doigt pour l'agrandir ou le réduire, comme un tiroir physique. |
| `showToast(msg)` | Affiche un message discret en bas de l'écran pendant 2,5 secondes (ex. « 🌳 Chêne enregistré ! »). Confirme une action sans interrompre le travail. |
| `updateStats()` | Met à jour en temps réel les deux compteurs en haut : nombre total d'arbres et nombre d'espèces différentes. |

### 📥 Import CSV

| Fonction | Description |
|---|---|
| **Importer un CSV** | Charge un fichier CSV précédemment exporté pour reprendre un inventaire sur un autre appareil. Recrée tous les arbres en mémoire et repose tous les marqueurs sur la carte. |

---

## 5 · Personnaliser pour Moissy-Cramayel et le bureau d'études

Ouvrez le fichier `index.html` avec un éditeur de texte (Notepad++, VS Code, ou le Bloc-notes de Windows) et effectuez ces 5 modifications :

### 1. Titre dans l'onglet du navigateur

Cherchez :
```html
<title>SIG Arbres — Inventaire de terrain</title>
```
Remplacez par :
```html
<title>SIG Arbres — Moissy-Cramayel — BET</title>
```

### 2. Sous-titre affiché dans l'en-tête

Cherchez :
```
Inventaire de terrain — Bureau d'Études
```
Remplacez par :
```
Ville de Moissy-Cramayel — Service Espaces Verts
```

### 3. Centre de la carte au démarrage

La carte est déjà centrée sur Moissy-Cramayel. Pour ajuster sur un parc ou un quartier précis, cherchez :
```javascript
center: [48.6167, 2.5833],
zoom: 14
```
Ajustez le zoom : **14** = vue de la commune, **16** = quartier, **18** = parcelle. Pour connaître les coordonnées d'un lieu, faites un clic droit sur Google Maps.

### 4. Copyright dans les exports image

Cherchez (deux occurrences) :
```
© 2026 Bureau d'Études — M. Prévost
```
Remplacez par :
```
© 2026 Ville de Moissy-Cramayel — Service Espaces Verts
```

### 5. Nom des fichiers exportés

Cherchez :
```
inventaire_BET_Prevost_
```
Remplacez par :
```
inventaire_MoissyCramayel_
```
Le fichier téléchargé s'appellera alors `inventaire_MoissyCramayel_2026-03-20.csv`.

---

## 6 · Déployer sur un serveur web

L'application ne nécessite qu'un **serveur web statique** — un serveur capable de distribuer des fichiers HTML. Pas besoin de base de données, pas besoin de PHP ni de Node.js.

### Prérequis serveur

| Élément | Requis | Détail |
|---|---|---|
| Type d'hébergement | ✅ Oui | Tout hébergement standard (OVH, PlanetHoster, Infomaniak, o2switch…) |
| PHP / MySQL | ❌ Non | Aucune base de données — les données sont stockées dans le navigateur |
| **HTTPS** | ⚠️ **Recommandé** | **Le GPS est bloqué sur HTTP non sécurisé par les navigateurs modernes.** Un certificat SSL gratuit (Let's Encrypt) est indispensable pour la géolocalisation. |
| Espace disque | ✅ Minimal | Le fichier `index.html` fait environ 250 ko |
| Bande passante | ✅ Faible | Seules les tuiles de carte consomment du réseau, côté navigateur |
| Nom de domaine | ✅ Optionnel | L'application peut vivre sur un sous-domaine ou dans un dossier existant |

### Étapes de déploiement

1. **Préparez votre fichier** — Faites les 5 personnalisations (section 5). Enregistrez sous le nom `index.html`.
2. **Connectez-vous à votre hébergement** — Via le gestionnaire de fichiers (cPanel, Plesk…) ou un logiciel FTP comme FileZilla.
3. **Déposez le fichier** — Copiez `index.html` dans le dossier `public_html` (ou `www`, ou un sous-dossier). **Un seul fichier suffit.**
4. **Activez HTTPS** — Dans le panneau de votre hébergeur, activez le certificat SSL gratuit Let's Encrypt (proposé en un clic par tous les hébergeurs modernes).
5. **Testez sur mobile** — Ouvrez l'adresse sur votre téléphone, autorisez la géolocalisation, et testez le cycle complet : GPS → formulaire → enregistrement → export CSV.

> **⚠️ Important : les données restent sur l'appareil.** Les arbres saisis sont stockés dans le navigateur de chaque appareil. Si deux agents de terrain utilisent l'application sur deux téléphones différents, leurs inventaires ne se synchronisent pas automatiquement. Pour partager les données, chacun exporte son CSV et vous les fusionnez dans Excel.

### Option : installation hors ligne complète (PWA)

Il est possible de transformer l'application en *Progressive Web App* (PWA), ce qui permet de l'installer sur l'écran d'accueil du téléphone et de la faire fonctionner entièrement hors connexion (y compris les tuiles de carte en cache). C'est une évolution possible sur demande.

---

## 7 · Personnaliser les couleurs et l'apparence

Toutes les couleurs sont définies en variables au début du CSS. Il suffit de modifier ce bloc :

```css
/* Cherchez ce bloc dans la balise <style>, au début du fichier */
:root {
  --forest:   #1a2e1a;  /* Fond principal (vert très sombre)    */
  --moss:     #2e4a2e;  /* Fond secondaire (vert mousse)        */
  --fern:     #3d6b3d;  /* Bordures et éléments discrets        */
  --leaf:     #5a9a4a;  /* Couleur principale (vert feuille)    */
  --sage:     #a8c897;  /* Textes sur fond sombre               */
  --cream:    #f5f0e8;  /* Texte principal clair                */
  --amber:    #d4891a;  /* Bouton export (orange ambre)         */
  --rust:     #c05a2a;  /* Bouton danger / suppression          */
}
```

### Exemple : thème bleu municipal

```css
--forest:   #1a2540;  /* bleu nuit  */
--moss:     #2a3a5e;  /* bleu marine */
--leaf:     #4a7fc1;  /* bleu vif   */
--sage:     #a0bcdd;  /* bleu ciel  */
```

### Changer le fond de carte par défaut

Par défaut la carte s'ouvre sur OpenStreetMap. Pour démarrer sur Google Satellite, cherchez :
```javascript
layerOSM.addTo(map);
let activeLayer = layerOSM;
```
Et remplacez par :
```javascript
layerIGN.addTo(map);
let activeLayer = layerIGN;
```

### Ajouter un champ dans le formulaire

Pour ajouter un champ (ex. « Numéro de patrimoine »), ajoutez dans le HTML du formulaire :
```html
<label for="field-patrimoine">N° Patrimoine</label>
<input type="text" id="field-patrimoine" placeholder="ex. ARB-0042" />
```
Puis dans la fonction `doSave()`, récupérez la valeur :
```javascript
const patrimoine = document.getElementById('field-patrimoine').value;
```
Et incluez-la dans l'objet `tree` et dans la fonction `buildCSV()`.

---

## 8 · Pistes d'évolution suggérées

| Évolution | Intérêt | Complexité |
|---|---|---|
| 📸 **Photo de l'arbre** | Prendre une photo directement depuis l'application, stockée avec la fiche | Moyenne |
| ☁️ **Synchronisation cloud** | Partager les données entre plusieurs agents de terrain en temps réel (via Firebase ou Supabase gratuits) | Élevée |
| 📏 **Diamètre / circonférence** | Ajouter un champ de mesure de tronc, courant dans les inventaires patrimoniaux | Faible |
| 🔴 **Couleur par état sanitaire** | Marqueurs verts / orange / rouges selon l'état (Bon / Moyen / Mauvais) | Faible |
| 📊 **Statistiques détaillées** | Tableau de bord avec répartition par espèce, par état, histogrammes | Moyenne |
| 🗂️ **Zones / parcelles** | Délimiter des zones sur la carte et affecter chaque arbre à une zone | Élevée |
| 📱 **Installation PWA** | Installer l'application sur l'écran d'accueil du téléphone, fonctionnement hors ligne complet | Moyenne |
| 🖨️ **Export PDF imprimable** | Rapport PDF mis en page avec liste complète, carte et tableau récapitulatif | Élevée |
| 🔢 **Numérotation automatique** | Attribuer un numéro de patrimoine incrémental à chaque arbre saisi | Faible |

---

## 🔑 Rappel des points clés

- **Un seul fichier HTML** — pas d'installation, pas de dépendance.
- **HTTPS obligatoire** pour le GPS en production.
- **Les données restent dans le navigateur** — pensez à exporter régulièrement en CSV.
- **5 lignes à modifier** pour personnaliser l'outil à Moissy-Cramayel.
- **Tout hébergement web standard** convient au déploiement.

---

*🌳 SIG Arbres — Documentation technique · Bureau d'Études · Moissy-Cramayel*
*Document généré en mars 2026 · Format Markdown · Hébergeable sur tout dépôt Git ou site web*
