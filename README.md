# SOSIDE — site Next.js

Portage du site vitrine SOSIDE en Next.js 16 (App Router) + TypeScript.
Deux langues (FR à `/`, EN à `/en`) et quatre pages sectorielles
(`/pme`, `/ong`, `/ecoles`, `/cliniques`), en français uniquement pour l'instant.

## Installer et lancer en local

Prérequis : Node.js 20.9 ou plus récent.

```bash
npm install
npm run dev
```

Ouvrez http://localhost:3000. `npm run build && npm run start` lance la
version de production en local.

## Déployer

Le plus simple est Vercel (créateur de Next.js, déjà utilisé pour le site du
fondateur) : poussez ce dossier sur un dépôt GitHub, importez-le sur
vercel.com, puis attachez votre nom de domaine.

## À faire avant la mise en ligne

1. **Nom de domaine réel.** `lib/site.ts` contient `SITE_URL =
   "https://sosside.vercel.app"`, une hypothèse posée pendant la conversation
   qui a servi à générer ce site, jamais confirmée. Changez cette seule
   constante pour votre vrai domaine : elle alimente les URLs canoniques, le
   plan du site (`app/sitemap.ts`) et les données structurées
   (`lib/schema.ts`).
2. **SOSIDO vs SOSIDE.** Les statuts SAS déposés utilisent « SOSIDO », le logo
   et ce site utilisent « SOSIDE ». À harmoniser avant tout dépôt RCCM.
3. **Références clients.** HMS Elite, UMS et Maago (section Références de la
   page d'accueil) sont des réalisations menées chez Aumsoft Technology, pas
   des projets SOSIDE : vérifiez que vous pouvez les présenter ainsi.
4. **Prix.** Les tarifs affichés sont indicatifs (venant du rapport
   stratégique), à confirmer.

## Où modifier le contenu

- `lib/content.ts` — tous les textes FR/EN de la page d'accueil (accroche,
  offres, références, méthode, FAQ, formulaire de diagnostic).
- `lib/sectors.ts` — les 4 pages sectorielles.
- `lib/site.ts` — domaine, téléphone, email.
- `app/globals.css` — couleurs, typographie, espacements (variables CSS en
  haut de fichier).
- `public/` — logo, icône, image de partage (`og.png`) et vidéos.

## Structure

```
app/
  (fr)/            groupe de routes FR — a son propre <html lang="fr">
    layout.tsx
    page.tsx       → /
    pme/page.tsx   → /pme
    ong/, ecoles/, cliniques/
  (en)/            groupe de routes EN — a son propre <html lang="en">
    layout.tsx
    en/page.tsx    → /en
  sitemap.ts, robots.ts, globals.css
components/        Header, Footer, HomePage, SectorPage, Stats, DiagnosticForm…
lib/               contenu, constantes, données structurées (JSON-LD)
public/            logo, icône, og.png, vidéos
```

Les dossiers entre parenthèses, `(fr)` et `(en)`, sont des « route groups » :
ils organisent le code sans apparaître dans l'URL. On les utilise ici parce
que Next.js n'autorise qu'un seul `<html>` par arbre de mise en page ; deux
groupes racines permettent d'avoir `lang="fr"` et `lang="en"` correctement
posés selon la page, sans bascule côté client. Le contenu partagé (police,
schéma d'organisation) est factorisé dans `lib/fonts.ts` et `lib/schema.ts`
pour éviter la duplication.

## Notes techniques

- Pas de Tailwind : une seule feuille `app/globals.css`, avec variables CSS
  pour les couleurs (thème sombre automatique via `prefers-color-scheme`).
- Les sections qui apparaissent au défilement (`components/Reveal.tsx`)
  utilisent `IntersectionObserver` et respectent `prefers-reduced-motion` ;
  elles restent visibles sans JavaScript (dégradation progressive via la
  classe `.js` posée par un petit script dans `<body>`).
- Pas de configuration ESLint incluse : `next lint` a été retiré dans
  Next.js 16 au profit d'ESLint en configuration « flat » autonome. Ajoutez
  la vôtre si vous en avez besoin (`npm install -D eslint
  eslint-config-next` puis un `eslint.config.mjs`).
- Images et vidéos sont servies depuis `public/` (pas de CDN externe) ; elles
  pèsent environ 1,3 Mo au total, ce qui reste léger pour de la vidéo.
