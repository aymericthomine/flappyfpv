# Flappy FPV

Flappy Bird en 3D, à la première personne. Une seule page, aucune dépendance.

Tape (ou espace) pour battre des ailes et passer entre les tuyaux.
Le meilleur score est gardé en local dans le navigateur.

## Lancer

Ouvre `index.html` dans un navigateur, ou sers le dossier :

```
python3 -m http.server 8000
```

Puis va sur http://localhost:8000 — c'est prévu pour le format mobile (portrait).

## Comment ça marche

Tout tient dans `index.html` : un mini moteur 3D écrit sur un canvas 2D.

- projection perspective maison, avec tangage et roulis de la caméra
- découpage des polygones sur le plan proche (`clipNear`), pour le sol et le plafond
  qui passent derrière la caméra
- tuyaux dessinés comme des boîtes, faces arrière éliminées, algorithme du peintre
- brouillard de distance séparé pour le couloir (qui se fond dans le violet)
  et pour les tuyaux (qui restent lisibles)
