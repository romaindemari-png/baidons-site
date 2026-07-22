# BAIDONS.md — contexte projet

Site vitrine de The Baïdon's (reprises rock/garage/funk/psyché, Marseille). Réalisé par LeStud.
Repo romaindemari-png/baidons-site → baidons-site.netlify.app

## Stack & contraintes
- HTML/CSS/JS vanilla, un seul index.html. Pas de framework, PAS de lib (ni GSAP ni Lenis).
- Animations : CSS natif + IntersectionObserver + requestAnimationFrame uniquement.
- prefers-reduced-motion respecté sur TOUT effet.
- Mobile-first, HTML léger : images WebP dans assets/, audio externalisé, vidéo sur Cloudinary.
- Netlify (deploy auto sur push main) + Netlify Forms pour le booking (form `name="booking"`, `data-netlify`). Le build/publish n'est PAS versionné (pas de netlify.toml ni _redirects dans le repo) : « build vide, publish "." » est réglé dans l'UI Netlify.
- Cloudinary (vidéo hero uniquement, les images restent dans le repo) : cloud `dcwfwfgg`. Qualité FIXE volontaire (q_auto donnait un rendu variable/baveux). URLs exactes du fichier :
  - Vidéo desktop : `res.cloudinary.com/dcwfwfgg/video/upload/q_auto,f_auto/v1784597597/video_hero_baidons_aanmfu.mp4`
  - Vidéo mobile : `res.cloudinary.com/dcwfwfgg/video/upload/q_75,f_auto,w_960/v1784597597/video_hero_baidons_aanmfu.mp4`
  - Poster OG (1200×630) : `res.cloudinary.com/dcwfwfgg/video/upload/so_0,q_auto,f_jpg,w_1200,h_630,c_fill/v1784597597/video_hero_baidons_aanmfu.jpg`
  - Poster vidéo : `res.cloudinary.com/dcwfwfgg/video/upload/so_0,q_auto,f_jpg/v1784597597/video_hero_baidons_aanmfu.jpg`
  - Note : desktop = q_auto,f_auto ; mobile = q_75,f_auto,w_960 (c'est le mobile qui porte la qualité fixe q_75).

## Direction artistique
- Fond #161220 (violet-noir). Palette : corail #ff4f4e (signature), rose-corail #ff5a7d, violet #9b4dff, périwinkle #7890f0.
- Typos : Barlow Condensed (display/logo), DM Mono (labels/tickers), Overpass (corps).
- Grain SVG global + effet bitmap (feTurbulence fractalNoise, `body::after` + `.grain`, mix-blend-mode:overlay).
- Images : duotone violet vif → corail via filtre SVG `#duo-corail` + dérive de teinte lente (hue-rotate animé via `--duo-hue`). Filtre appliqué sur `.vidph`, `.gcar .slide`, `.spread .ph`. tableValues réelles (feComponentTransfer, `color-interpolation-filters="sRGB"`) :
  - `feFuncR type="table" tableValues="0.227 1"`
  - `feFuncG type="table" tableValues="0.102 0.42"`
  - `feFuncB type="table" tableValues="0.376 0.30"`
  - → ombres (0.227, 0.102, 0.376) = violet ; hautes lumières (1, 0.42, 0.30) = corail.
- Cards concerts : aplats pleins, texte noir, tag pilule, gros chiffre fantôme, format 1:1. Corail dominant, rose-corail une seule fois, jamais deux couleurs identiques adjacentes. Pas de dégradé sur les aplats, pas de bordure intérieure.
- Structure : sas d'entrée → hero → concerts (#concerts) → bio (#groupe) → psyché « Fuzz. Groove. Canaille. » → écouter/cassette (#ecouter) → live (#live) → galerie (#galerie) → booking (#booking) → footer.

## Pièges connus (ne pas re-découvrir)
- transform sur `.hero .photo` est RÉSERVÉ à l'animation d'entrée hUp et à la parallaxe. Pour positionner : width/left/bottom, jamais transform.
- object-fit:cover interdit sur la photo du groupe (détourage transparent → coupe les personnages). Pour la faire descendre sur grand écran : bottom négatif.
- Filtres SVG duotone : les feFuncR/G/B doivent avoir `type="table"`, sinon tableValues est ignoré (le filtre devient un simple grayscale).
- mix-blend-mode : à poser sur le calque parent (pas sur le texte) + `isolation:isolate` sur le conteneur. (Actuellement mix-blend n'est utilisé que sur le grain ; règle à appliquer si on réintroduit un blend sur du contenu.)
- iOS ignore audio.volume (lecture seule) → fade-in impossible sur iPhone ; Web Audio n'y change rien et réintroduit des clics/pops. Décision actée : fondu desktop uniquement, démarrage direct et propre sur mobile. NE PAS rouvrir ce chantier.
- box-shadow animé sur élément à border-radius élevé = coutures parasites sur Safari iOS (repaint par frame) → utiliser un pseudo-élément animé en transform/opacity.
- content-visibility:auto sur le carrousel galerie faisait sauter le paint sur mobile → retiré.
- Débordement horizontal mobile : venait de `.gcar` (margin:0 -22px) non clippé → `overflow-x:hidden` sur html + `overflow-x:clip` sur `#galerie`.
- Deux parallaxes ne doivent jamais piloter la même propriété (translate) sur un même élément : elles s'écrasent.

## Méthode de travail
- Diagnostiquer avant de coder : montrer la cause réelle (mesurée) avant tout correctif.
- Un diff montré avant chaque commit, commits séparés par sujet.
- DA, placements et copy se décident en chat avec Romain, jamais devinés par le code. Le calage visuel se valide à l'œil.
- Ne pas sur-ingénierer : site vitrine, pas plateforme.

## Backlog
### Bloquant avant toute promo
- Les 6 dates des cards sont FICTIVES (exemples de maquette) → à remplacer par les vraies. Le JSON-LD MusicEvent ne couvre que 4 events (les 2 dernières cards ne sont pas décrites).
- Domaine thebaidons.fr : OG/canonical/og:url pointent DÉJÀ sur `baidons-site.netlify.app` (pas de placeholder à ce niveau). `booking@thebaidons.fr` dans le JSON-LD est un placeholder inventé — le groupe n'a pas de domaine. Adresse fausse et indexable par Google : à remplacer par la vraie adresse de booking, ou à retirer du JSON-LD en attendant.

### En attente du contenu du groupe
- Vrai audio dans « Écouter » + branchement des pistes de la tracklist
- Vraies URLs Spotify/Deezer/YouTube/Bandcamp + Instagram/Facebook (les liens morts sont masqués auto, réapparaissent dès qu'une vraie URL est mise)
- Vraies photos de galerie, vraie vidéo « Plein les yeux » (actuellement un poster), email de booking

### Optionnel (non prioritaire, validé)
- Nettoyer le #ancre de l'URL après clic de nav
- Scrollytelling épinglé sur la section psyché (en remplacement de la parallaxe actuelle, pas en plus)
- Transition clip-path sur une seule charnière
- Passe conversion booking : CTA sticky, .ics, états sold-out, réassurance
- Tokens OKLCH — seulement si templatisation pour d'autres groupes

### Écarté volontairement
- Curseur custom · cassette sound-reactive (rouvrirait le Web Audio) · nom géant mix-blend dans le hero (hero déjà chargé) · design-system complet · scroll-spy
