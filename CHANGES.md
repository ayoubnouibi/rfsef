# Redion Chat — Corrections UX/UI & responsive

Page corrigée : [`index.html`](./index.html) (statique, sans dépendance, logo dans `assets/`).
Elle reproduit l'écran d'accueil de https://chat.redion.com avec les correctifs décrits ci-dessous.

> **Périmètre :** écran d'accueil uniquement (l'envoi de message est une maquette).
> Le site réel est une app Angular dont le dépôt source n'était pas disponible ; les règles CSS ci-dessous sont à reporter dans les composants.

## Lancer en local

```bash
cd redion-fixed
python3 -m http.server 8080
# http://localhost:8080
```

## 1. Diagnostic initial (site d'origine)

Audit Chrome sur ~20 viewports (320 → 2560 px, portrait/paysage, zooms 75 %–150 % simulés).

| # | Problème constaté | Gravité |
|---|---|---|
| 1 | Un seul `@media (max-width:820px)` ; rien au-dessus → sur grands viewports / zoom 75 % le contenu reste petit (titre 48 px, colonne 760 px, input 690 px) et noyé dans le vide | Haute |
| 2 | Contenu centré verticalement + saisie collée en bas du viewport → grand vide au milieu, saisie hors de la zone visible quand la fenêtre est plus petite que le viewport | Haute |
| 3 | Titre coupé sur le trait d'union : « 2- / minute » | Moyenne |
| 4 | Bouton « New conversation » : coin inférieur droit plat (conteneur qui rogne) | Moyenne |
| 5 | Paysage / faible hauteur : barre latérale desktop conservée, notice passe derrière la saisie | Haute |
| 6 | 320 px : placeholder tronqué sur 2 lignes, notice coupée par la saisie | Moyenne |
| 7 | Cibles tactiles trop petites (langue 58×29, lien Privacy 83×16) | Moyenne (WCAG 2.5.8) |
| 8 | Contrastes faibles (gris `#64748b` en 11–12 px, bouton d'envoi rose pâle) | Moyenne |
| 9 | Barre latérale : 183 px quasi vide, tiroir mobile presque vide | Faible |

## 2. Changements effectués

### Layout & scroll (le cœur du problème)
| Avant | Après |
|---|---|
| `.app` en `100dvh` + `overflow:hidden`, zone scrollable interne | **Aucune hauteur forcée** : le document défile naturellement (`min-height:100dvh`) |
| Contenu centré verticalement | Contenu **aligné en haut** (`padding-top: clamp(.5rem,3vh,2rem)`) |
| Saisie collée au bas du viewport | Saisie **à la suite de la notice** (dans le flux) |
| Rien à scroller si le contenu tient dans le viewport → saisie inatteignable à 75 %/125 %/150 % dans DevTools | **Marge de défilement** `.content::after{height:max(6rem,65dvh)}` : la page dépasse toujours le viewport, on peut donc toujours scroller jusqu'à la saisie, quel que soit le zoom |
| — | `position: sticky` testé puis **retiré** (collé au bas du viewport = zone cachée) |

### Ordre du contenu
| Avant | Après |
|---|---|
| Notice légale → saisie | Idem, **garanti** : badge → titre → sous-titre → **legal notice** → saisie |
| (essai intermédiaire : notice repliable `<details>`) | Retiré : la notice est **toujours affichée** (valeur légale), avec `role="note"` |

### Unités & breakpoints
| Avant | Après |
|---|---|
| Valeurs en `px`, 1 breakpoint (820 px) | Tout en **`rem`** (seules les bordures de 1 px restent en px) ; tokens `--col`, `--h1`, `--sidebar-w`, `--gutter`, `--tap` |
| — | **Largeur** (mobile-first) : `30rem`, `48rem`, `64rem`, `90rem`, `120rem`, `160rem` |
| — | **Racine fluide** : 18 px dès `90rem`, 20 px dès `120rem`, 24 px dès `160rem` → grands écrans / zoom 75 % lisibles |
| — | Tiroir mobile à `51.25rem` (820 px) |
| — | **Hauteur** : `56.25rem`, `43.75rem`, `30rem` → compactage progressif (badge, sous-titre, mention du bas, carte « data is safe » masqués ; titre réduit) |

### Composants
| Élément | Avant | Après |
|---|---|---|
| Titre `h1` | 48 px fixe, coupure « 2- / minute » | `font-size` par token/breakpoint, `text-wrap:balance`, `2&#8209;minute` + `white-space:nowrap` |
| Bouton « New conversation » | Coin inférieur droit plat | `border-radius` complet, `white-space:nowrap`, `min-height:2.75rem` |
| Barre latérale | 183 px fixe | `14rem → 16rem` selon l'écran, `sticky` pleine hauteur |
| Tiroir mobile | 280 px, grand vide | `min(82vw,18.75rem)` + fond assombri, fermeture par clic/Échap, `aria-expanded` |
| Sélecteur de langue | 58×29 px | `min-height:2.75rem` (44 px) ; ≤ 30rem : drapeau seul |
| Saisie | Placeholder sur 2 lignes à 320 px | Placeholder raccourci ≤ 30rem (« Ask about your trip or insurance ») ; `textarea` auto-extensible (max 8rem) |
| Bouton d'envoi | Rose pâle, 36 px | 44 px ; désactivé `#d9a3a1` plus lisible, actif rouge Redion |
| Legal notice | 13 px sur grands écrans, texte long | Taille fluide ; ≤ 30rem : 13 px, marges réduites ; lien Privacy avec zone de clic agrandie |
| Texte secondaire | `#64748b` 11–12 px | `#475569`, ≥ 13 px |

### Accessibilité
- Cibles tactiles ≥ 44 px (`--tap`), `:focus-visible` visible (3 px), `aria-label` sur burger / langue / envoi / saisie.
- `prefers-reduced-motion` respecté, `safe-area-inset-bottom` pris en compte (iPhone), `viewport-fit=cover`.

## 3. Avant / après

| Viewport | Avant (site) | Après (page corrigée) |
|---|---|---|
| 1920×1152 (≈ 75 % d'un 1440) | ![](docs/before/1920x1152.png) | ![](docs/after/1920x1152.png) |
| 1280×720 | ![](docs/before/1280x720.png) | ![](docs/after/1280x720.png) |
| 844×390 (paysage) | ![](docs/before/844x390.png) | ![](docs/after/844x390.png) |
| 320×568 | ![](docs/before/320x568.png) | ![](docs/after/320x568.png) |

> Les captures « après » sont prises sans scroll : la marge de défilement ajoutée sous la saisie n'y est pas visible.

## 4. Vérifications effectuées (Chrome, émulation)

- 22 viewports de 320×568 à 3840×2160 (portrait et paysage) : aucun scroll horizontal, aucune erreur JS, saisie fonctionnelle (bouton d'envoi s'active).
- Legal notice située avant la saisie sur 17/17 tailles testées.
- Après scroll jusqu'en bas, le haut de la saisie se retrouve dans le tiers supérieur à moyen du viewport : ex. 440×956 → 243 px, 320×568 → 132 px, 1032×1376 → 391 px, ce qui reste visible même si DevTools n'affiche que ~65 % du viewport (zoom ≈ 150 %).

**Non testé :** appareils physiques, Safari, Firefox, états « conversation en cours » et paiement, lecteurs d'écran.

## 5. Compromis connus

- Sous la saisie, il reste de l'espace vide (`65dvh`) : c'est le prix de la garantie « on peut toujours scroller jusqu'à la saisie, quel que soit le zoom ». Sur site réel, on peut le réduire (ex. `max(2rem,25dvh)`) si ce besoin ne concerne que DevTools.
- La saisie n'est plus épinglée en bas : adapté à l'écran d'accueil, **pas** à la vue conversation, où une saisie `sticky`/fixe reste préférable (la liste de messages scrolle, la saisie reste visible).

## 6. Reporter dans l'app Angular

1. Variables `--col`, `--h1`, `--sidebar-w`, `--gutter`, `--tap` + les breakpoints `rem` dans `styles.css`.
2. Supprimer `height:100dvh; overflow:hidden` du conteneur racine du landing (garder pour la vue conversation avec une zone de messages scrollable).
3. Ordre du template : notice → saisie.
4. Corriger le conteneur du bouton « New conversation » (`overflow` / `border-radius`).
5. `h1` : `text-wrap:balance` + `2&#8209;minute` insécable.
6. Cibles ≥ 44 px, contrastes `#475569`, `focus-visible`.
