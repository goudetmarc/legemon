# BRIEF CLAUDE CODE — Déploiement legemon.com sur Astro + Cloudflare Pages

## CONTEXTE

Le site legemon.com est actuellement un site WordPress/Elementor hébergé sur un serveur OVH (54.36.91.62). On migre vers **Astro (static) + Cloudflare Pages**. Le domaine legemon.com est déjà géré dans Cloudflare (compte goudetmarc@gmail.com).

Un projet Astro complet et fonctionnel a été préparé. Le build est validé (`npx astro build` → succès). Il contient toutes les images et tout le contenu du site original.

## CE QUE TU DOIS FAIRE (dans l'ordre)

### 1. Dézipper le projet
Le projet se trouve dans le fichier `legemon-astro-project.zip`. Dézippe-le dans ton workspace.

```bash
unzip legemon-astro-project.zip
cd legemon
```

### 2. Installer les dépendances et vérifier le build
```bash
npm install
npx astro build
```
Le build doit produire un dossier `dist/` avec `index.html` et un dossier `images/`.

### 3. Créer le repo GitHub
Créer un repo **public** nommé `legemon` sous le compte `goudetmarc` sur GitHub.

```bash
gh repo create goudetmarc/legemon --public --source=. --remote=origin
```

### 4. Pousser le code
```bash
git init
git branch -m main
git add -A
git commit -m "Initial commit: Le Gemon site migrated to Astro"
git remote add origin https://github.com/goudetmarc/legemon.git
git push -u origin main
```

### 5. Déployer sur Cloudflare Pages
Utiliser Wrangler CLI pour créer le projet Cloudflare Pages connecté au repo GitHub :

```bash
npx wrangler pages project create legemon --production-branch main
```

Ou bien, aller sur https://dash.cloudflare.com → Workers & Pages → Create → Pages → Connect to Git → sélectionner `goudetmarc/legemon`.

**Paramètres de build Cloudflare Pages :**
- **Framework preset** : Astro
- **Build command** : `npm run build`
- **Build output directory** : `dist`
- **Root directory** : `/`

### 6. Connecter le domaine custom
Une fois le projet Pages créé, ajouter le custom domain `legemon.com` :
- Cloudflare Pages → projet `legemon` → Custom domains → Add → `legemon.com`
- Cloudflare mettra à jour le DNS automatiquement (CNAME vers `legemon.pages.dev`)
- L'ancien A record (54.36.91.62) sera remplacé

## STRUCTURE DU PROJET

```
legemon/
├── astro.config.mjs          # Config Astro (output: static)
├── package.json               # Deps: astro ^4.16.0
├── tsconfig.json
├── public/
│   └── images/                # 27 images (16 Mo total)
│       ├── 35v.png            # Logo Gemon
│       ├── 119H.png           # Character illustration
│       ├── 67lum.png          # 3D character
│       ├── square1-17.jpg     # Gallery (14 squares)
│       ├── gemon2.jpg         # Todiebook photo
│       ├── gemon3.jpg         # Spread photo
│       ├── xl.jpg             # XL art
│       ├── factory1.jpg       # Factory photo 1
│       ├── factory2.jpg       # Factory photo 2
│       ├── graph_1.png        # Geometry graph
│       ├── gemon_head.png     # Head illustration
│       ├── PileouFace.jpg     # Pile ou Face
│       ├── 122.jpg            # Detail photo
│       └── square13-thumb.jpg # Thumbnail
└── src/
    ├── layouts/
    │   └── Layout.astro       # Layout principal (CSS global intégré)
    └── pages/
        └── index.astro        # Page unique (tout le contenu)
```

## DESIGN & STYLE

- **Fonts** : Roboto + Roboto Slab (Google Fonts)
- **Palette** : Fond noir (#000), texte blanc, paragraphes gris (#ccc)
- **Layout** : Sections alternées full-width et boxed (max 1140px)
- **Gallery** : Grille 5 colonnes, 14 items avec captions
- **Responsive** : Breakpoint 768px, colonnes passent en vertical

## VÉRIFICATIONS APRÈS DÉPLOIEMENT

1. `legemon.pages.dev` doit afficher le site correctement
2. Toutes les images doivent charger
3. `legemon.com` doit pointer vers le site (après propagation DNS)
4. Le certificat SSL doit être automatiquement provisionné par Cloudflare

## NOTES IMPORTANTES

- Le projet est **100% statique** (output: static dans astro.config.mjs)
- Pas de SSR, pas de Cloudflare adapter nécessaire
- Les images sont dans `public/images/` (servies telles quelles, pas de processing Astro)
- Le site est une single-page (pas de routing multi-pages)
