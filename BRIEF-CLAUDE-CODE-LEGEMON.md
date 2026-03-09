# BRIEF CLAUDE CODE — Déploiement legemon.com sur Cloudflare Pages

## CONTEXTE

Le site legemon.com est un site WordPress/Elementor hébergé sur OVH. On le migre vers Cloudflare Pages en copiant le code HTML/CSS exact du site original. Le domaine legemon.com est déjà géré dans Cloudflare (compte goudetmarc@gmail.com).

**IMPORTANT : Le projet est un copié-collé exact du HTML et CSS du site WordPress. Le HTML est le body d'origine, le CSS est la concaténation de tous les fichiers Elementor. NE PAS réécrire, réinterpréter ou "améliorer" quoi que ce soit. Le site doit être pixel-perfect identique à l'original.**

## CE QUE TU DOIS FAIRE

### 1. Dézipper le projet

```bash
unzip legemon-astro-project.zip
cd legemon
```

### 2. Installer et vérifier le build

```bash
npm install
npx astro build
```

Vérifier que `dist/` contient : `index.html`, `css/all.css`, `images/` (27 fichiers).

### 3. Créer le repo GitHub

```bash
gh repo create goudetmarc/legemon --public --source=. --remote=origin --push
```

Si ça ne marche pas en une commande :
```bash
gh repo create goudetmarc/legemon --public
git remote add origin https://github.com/goudetmarc/legemon.git
git push -u origin main
```

### 4. Déployer sur Cloudflare Pages

Option CLI :
```bash
npx wrangler pages project create legemon --production-branch main
npx wrangler pages deploy dist/
```

Ou via le dashboard Cloudflare : Workers & Pages → Create → Pages → Connect to Git → `goudetmarc/legemon`

**Paramètres de build :**
- **Framework preset** : Astro
- **Build command** : `npm run build`
- **Build output directory** : `dist`

### 5. Connecter le domaine custom

Cloudflare Pages → projet legemon → Custom domains → Add → `legemon.com`
Le DNS sera mis à jour automatiquement (CNAME vers `legemon.pages.dev`).

## STRUCTURE DU PROJET

```
legemon/
├── astro.config.mjs           # Config Astro (output: static, site: legemon.com)
├── package.json               # Deps: astro
├── .gitignore
├── public/
│   ├── index.html             # ← LE HTML EXACT copié de legemon.com (body WordPress/Elementor)
│   ├── css/
│   │   └── all.css            # ← TOUS les CSS Elementor concaténés (148 Ko)
│   └── images/                # ← 27 images téléchargées du site original (16 Mo)
│       ├── 35v.png            # Logo
│       ├── 119H.png, 67lum.png  # Characters
│       ├── square1-17.jpg     # Gallery (14 images)
│       ├── gemon2.jpg, gemon3.jpg, xl.jpg  # Photos
│       ├── factory1.jpg, factory2.jpg  # Factory
│       ├── graph_1.png, gemon_head.png  # Illustrations
│       ├── PileouFace.jpg, 122.jpg  # Detail photos
│       └── square13-thumb.jpg  # Thumbnail
└── src/
    └── pages/
        └── blank.astro        # Placeholder (Astro a besoin d'au moins une page)
```

## COMMENT ÇA MARCHE

- `public/index.html` est le site complet — HTML body exact de WordPress + inline styles
- `public/css/all.css` contient tous les CSS Elementor (hello-elementor, frontend, widgets, post-7, post-510)
- Les images dans `public/images/` remplacent les URLs `legemon.com/wp-content/uploads/...`
- Google Fonts (Roboto, Roboto Slab) et Font Awesome sont chargés depuis CDN
- Astro se contente de copier `public/` vers `dist/` au build
- Les srcset ont été retirés (on ne fournit que les images pleine taille)

## NE PAS FAIRE

- Ne pas réécrire le HTML ou le CSS
- Ne pas modifier le design, les couleurs, les tailles de texte
- Ne pas ajouter de framework frontend
- Ne pas "optimiser" les images ou le code
- Le site doit être visuellement identique à https://legemon.com

## VÉRIFICATIONS APRÈS DÉPLOIEMENT

1. `legemon.pages.dev` affiche le site
2. Comparer visuellement avec `legemon.com` — doit être identique
3. Toutes les images chargent
4. `legemon.com` pointe vers Cloudflare Pages (après propagation DNS)
