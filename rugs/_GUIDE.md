# Comment ajouter un design au configurateur Rugier

Aucun code à toucher. Tu travailles uniquement dans ce dépôt GitHub.

## Principe

Chaque design = un dossier dans `/rugs/` contenant :
- `config.json` — les infos du tapis (nom, tailles, palettes…)
- soit `preview.svg` (tapis classique étiré)
- soit `module.svg` (tapis modulaire Optical Palazzo, le carré qui se répète)

Le configurateur lit `index.json` à la racine de `/rugs/`, qui liste tous les designs disponibles.

## Étape par étape — ajouter un tapis classique (Moiré, Highland, Pintura, Fodio…)

### 1. Préparer le SVG
Dans Illustrator, ton tapis doit utiliser un **nombre limité de couleurs à plat** (chaque couleur = une zone modifiable). Exporte en SVG (Fichier > Exporter > Exporter sous > SVG), en RGB.

Pas besoin de nommer les calques : le configurateur détecte automatiquement les couleurs, classées de la plus utilisée à la moins utilisée. La 1ʳᵉ couleur = "Zone 1", etc.

### 2. Créer le dossier
Sur github.com, dans ce dépôt : `Add file` > `Create new file`. Tape comme nom de fichier :
```
rugs/MON-TAPIS/config.json
```
(remplace MON-TAPIS par l'identifiant, en minuscules sans espaces, ex : `aurora`)

GitHub crée le dossier automatiquement.

### 3. Remplir config.json
Copie-colle ce modèle et adapte :
```json
{
  "id": "aurora",
  "name": "Aurora",
  "author": "Manon du Merle",
  "collection": "Moiré",
  "type": "stretch",
  "shape": "rect",
  "transparent": false,
  "sizes": ["100x100", "170x240", "200x300", "250x350"],
  "finitions": ["Carving", "No carving", "Relief bas"],
  "zoneLabels": ["Fond", "Motif", "Accent"],
  "layerColors": ["#b5916d", "#d8b7b4", "#c9a3a3"],
  "palettes": [
    { "name": "Original", "colors": { "layer-1": "#b5916d", "layer-2": "#d8b7b4", "layer-3": "#c9a3a3" } },
    { "name": "Terracotta", "colors": { "layer-1": "#C46840", "layer-2": "#E8D0B8", "layer-3": "#D4A880" } }
  ]
}
```
**Important** : `layerColors` doit contenir les couleurs EXACTES (codes hex) telles qu'elles sont dans ton SVG, dans l'ordre. C'est ce qui permet au configurateur de les remplacer. Pour les trouver : ouvre le SVG dans un éditeur de texte et repère les `fill="#..."`.

- `shape` : `"rect"` (rectangulaire) ou `"square"` (carré → tailles 100x100/200x200/300x300)
- `transparent` : `true` pour les tapis à forme libre (Fodio), sinon `false`

### 4. Uploader le SVG
`Add file` > `Upload files`, glisse ton SVG, et renomme-le `preview.svg` dans le bon dossier (ou crée-le directement avec le bon chemin `rugs/aurora/preview.svg`).

### 5. Ajouter au catalogue
Édite `rugs/index.json` (clic sur le fichier puis l'icône crayon) et ajoute une entrée :
```json
{ "id": "aurora", "name": "Aurora", "collection": "Moiré", "author": "Manon du Merle", "type": "stretch" }
```

C'est tout. Le configurateur affichera Aurora au prochain chargement.

## Ajouter un Optical Palazzo (modulaire)

Pareil, mais :
- `"type": "modular"`
- fichier `module.svg` au lieu de `preview.svg` (le carré répétable de 30×30 cm)
- le `module.svg` doit avoir ses zones de couleur dans des groupes `<g id="layer-1" fill="#..." data-original="#...">` (un groupe par couleur)

Champs supplémentaires dans `config.json` :
```json
{
  "type": "modular",
  "moduleCm": 30,
  "borderCm": 10,
  "borderColors": ["#a03837", "#7d2527"],
  "gradient": ["#b05564", "#100809"],
  "grids": {
    "100x100": [3, 3],
    "170x240": [5, 7],
    "200x300": [6, 9],
    "250x350": [7, 10]
  }
}
```
- `moduleCm` : taille physique d'un module (30 cm)
- `borderCm` : épaisseur totale du contour (10 cm = les deux couches)
- `borderColors` : `[couleur extérieure, couleur intérieure]`. La couleur extérieure doit être une des couleurs de calque du module pour qu'elle se recolorise avec les palettes. La ligne intérieure est automatiquement assombrie.
- `gradient` : `[couleur haut, couleur bas]` du dégradé de fond continu
- `grids` : nombre de modules `[colonnes, rangées]` pour chaque taille

Demande-moi de te préparer un module si tu n'as que le tapis complet — je sais l'extraire.

## Notes
- Les couleurs trop fluo (non réalisables en laine) sont automatiquement signalées à l'utilisateur.
- Tailles : largeur 1m à 3,5m, longueur 1m à 2,5m.
- Après chaque modif sur GitHub, attends ~1 minute (cache) puis recharge le configurateur.

## Tapis uni à double bordure (type « solid », ex : Squadra)

Pour un tapis à fond uni avec deux bordures (5 cm + 5 cm) :
```json
{
  "type": "solid",
  "shape": "rect",
  "borderCm": 10,
  "borderColors": ["#7d5a3a", "#2a1d12"],
  "fieldColor": "#c9a36b",
  "layerColors": ["#c9a36b", "#7d5a3a", "#2a1d12"],
  "zoneLabels": ["Fond", "Bordure extérieure", "Bordure intérieure"],
  "sizes": ["200x200","300x300","170x240","200x300","250x350"],
  "palettes": [ ... ]
}
```
Pas besoin de SVG complexe : le tapis est dessiné automatiquement. Un `preview.svg` simple sert juste de vignette.

## Prix

L'estimation se calcule automatiquement : surface nominale (taille affichée) × tarif au m².
Les deux tarifs (avec / sans carving) sont réglables dans Admin > Apparence & paramètres.
Le prix est indiqué « environ … € TTC ».

## Sauvegarde des tapis clients (backend)

Sans backend, « Mes tapis » et le feed sont stockés uniquement dans le navigateur du visiteur.
Pour une sauvegarde partagée et persistante, installe le backend Google Apps Script
(fichier `backend-rugier.gs`) et colle son URL dans Admin > URL backend.
Voir les instructions en haut de ce fichier.
