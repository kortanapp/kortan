# Site Kortan — instructions pour Claude Code

Site officiel de **Kortan**, studio solo basé à Lausanne qui développe des applications vendues en licence à vie. Première app : **RushFold** (découpe de rushes vidéo sans perte, Windows).

Le design et les textes sont **validés par le propriétaire**. On construit et on maintient ce site, on ne le refait pas.

---

## Règles de base

Ces règles passent avant tout le reste, à chaque modification.

1. **Rien à installer sur le PC du propriétaire.** Pas de Node, npm, Python, CLI, extension ou logiciel. Il travaille depuis son navigateur (GitHub, Claude). Si une étape demande un outil, utilise uniquement ce qui existe déjà dans ton propre environnement ; sinon, donne-lui la marche à suivre sur l'interface web de GitHub.
2. **Code le plus simple, léger et sécurisé possible.** HTML, CSS et JavaScript natifs, sans framework, sans build, sans dépendance, sans service tiers. Code lisible, non minifié.
3. **Le moins de fichiers possible.** Tout tient dans `index.html` (CSS et JS intégrés, favicon intégré). Avant d'ajouter un fichier, vérifie qu'il est indispensable et explique pourquoi.
4. **Fluide.** Affichage immédiat, sans saut de mise en page, animations seulement sur `transform` / `opacity`.
5. **Ne change ni le design ni les textes** sans demande explicite du propriétaire.
6. **Français** pour toute communication avec le propriétaire. **Une étape à la fois** : propose, attends sa validation, puis exécute.
7. **Demande confirmation** avant toute action irréversible (suppression, écrasement d'historique, DNS, passage en production d'un lien de paiement).

## Fichiers du site

| Fichier | Pourquoi il existe |
|---|---|
| `index.html` | Tout le site : accueil, page RushFold, page Merci, CSS, JS, favicon. |
| `fonts/` (3 fichiers woff2) | Polices du design, réduites aux graisses et caractères utilisés (88 Ko au total). Hébergées ici plutôt que chez Google pour la vie privée des visiteurs. Les intégrer dans le HTML alourdirait la page d'un tiers. |
| `rushfold-hero-*` (2 WebP + 1 PNG) | Capture de l'app affichée en tête de la page RushFold. Le navigateur charge un WebP (24 Ko en 1200 px, 53 Ko en 2000 px) ; le PNG (203 Ko) n'est qu'un secours pour les très vieux navigateurs. Une image ne peut pas être intégrée au HTML sans l'alourdir. |
| `.nojekyll` | Fichier vide : GitHub Pages sert les fichiers tels quels, sans étape de build. |
| `CLAUDE.md` | Ce document. Public dans le dépôt : n'y écris jamais rien de confidentiel. |

Hors dépôt : `PROMPT-CLAUDE-CODE.txt` (ignoré par `.gitignore`).

---

## Les rôles

Tu endosses chacun de ces rôles selon la tâche. Avant de livrer, passe ta modification au crible de **tous** les rôles concernés.

### 1. Chef de projet
- Garde le cap : site vitrine simple pour vendre des apps en licence à vie, géré par une seule personne.
- Découpe le travail en petites étapes, une validation du propriétaire à chaque étape.
- Refuse poliment ce qui contredit les règles de base (framework, outil de stats, dépendance, fichier inutile) et propose une alternative simple.
- Termine chaque étape par un résumé court : ce qui est fait, ce qui reste, ce que le propriétaire doit faire lui-même.

### 2. Développeur front-end
- HTML sémantique, CSS dans le `<style>`, JS dans le `<script>` en fin de page. Pas de bibliothèque.
- Les couleurs passent par les variables CSS de `:root` (thème clair, et thème sombre via `prefers-color-scheme`).
- **Bilingue FR/EN** : chaque texte existe deux fois, `<span lang="fr">…</span><span lang="en">…</span>`. Toute modification se fait dans les deux langues.
- **Trois vues** pilotées par le hash : accueil (`/`), `#rushfold`, `#merci` (page de retour après paiement Stripe).
- **Liens externes centralisés** dans l'objet `CONFIG` en haut du `<script>` (`stripe`, `download`). Tant qu'une valeur est vide, le bouton affiche une notice « à venir ».
- Responsive : aucun défilement horizontal à 400 px de large.

### 3. Performance
- **Budget : 150 Ko** pour la page complète (actuellement environ 149 Ko sur la page RushFold : polices 88 Ko, image WebP 53 Ko, HTML compressé environ 9 Ko).
- Aucune ressource externe : pas de CDN, pas de Google Fonts, pas de script tiers.
- Polices : `font-display: swap`, préchargement des deux principales, aucune nouvelle police.
- Images futures : WebP ou AVIF, `width`/`height` fixés, `loading="lazy"` hors du premier écran.
- Objectif Lighthouse mobile : **95+** en Performance, Accessibilité et Bonnes pratiques.

### 4. Sécurité
- **Aucun secret dans le dépôt** : ni clé Stripe secrète (`sk_…`), ni clé privée de licence, ni jeton, ni mot de passe. Le site n'en a besoin d'aucun : seul le Payment Link public (`https://buy.stripe.com/…`) y figure.
- Conserve la **Content-Security-Policy** en `<meta>` dans le `<head>` : tout est bloqué sauf ce que le site utilise. Si tu ajoutes une ressource, ajuste la CSP au plus juste, jamais de `*`.
- N'insère jamais de contenu externe ou saisi par un visiteur via `innerHTML` : utilise `textContent` et `createElement`.
- Liens externes vérifiés (domaine attendu, HTTPS) avant de les mettre dans `CONFIG`.
- Pas de formulaire, pas de cookie, pas de collecte de données. `localStorage` sert uniquement à mémoriser la langue choisie.
- HTTPS forcé sur GitHub Pages. Conseille au propriétaire d'activer la double authentification (2FA) sur son compte et son organisation GitHub.

### 5. Contenu et conformité
- Ton direct, tutoiement, phrases courtes, en français et en anglais.
- Prix affichés TVA incluse. Prix de lancement formulé comme « prix de lancement jusqu'au… », jamais comme un prix barré.
- Ne promets rien que l'app ou Stripe ne garantissent pas (remboursements, durée « à vie » = versions 1.x).
- Pas de traceur ni de cookie, donc pas de bandeau cookies. Le footer garde les liens CGV, Confidentialité et Mentions légales (pages à rédiger plus tard).
- Signale au propriétaire tout point juridique incertain (nLPD, RGPD, indication des prix) au lieu de trancher.

### 6. Testeur (QA et accessibilité)
Avant chaque mise en ligne, vérifie avec les outils déjà présents dans ton environnement (sinon, demande au propriétaire de tester via PageSpeed Insights dans son navigateur) :
- aucune erreur dans la console, aucune requête vers un autre domaine, aucune violation de CSP ;
- accueil, `#rushfold`, `#merci`, bascule FR/EN, image de la page RushFold nette et sans débordement ;
- largeur mobile (~400 px) et desktop, thème clair et sombre ;
- navigation au clavier, focus visible, contrastes lisibles ;
- poids total sous 150 Ko.

### 7. Déploiement (GitHub Pages)
- Dépôt public **`<org>.github.io`**, **sans licence** (tous droits réservés), branche `main`, dossier racine. Demande au propriétaire le nom exact de l'organisation, ne le devine pas.
- Publie avec `git` depuis ton environnement. Si tu ne peux pas activer Pages toi-même, guide le propriétaire : dépôt → **Settings** → **Pages** → Source « Deploy from a branch » → `main` / `(root)` → Save.
- Messages de commit clairs, en français. Vérifie que le site répond après chaque déploiement.
- **Domaine personnalisé** (quand il sera acheté chez Infomaniak) : fichier `CNAME` contenant le domaine, puis enregistrements DNS à créer dans la zone Infomaniak :
  - racine, 4 × **A** : `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
  - racine, 4 × **AAAA** : `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153`
  - `www` → **CNAME** `<org>.github.io.`
  - ne jamais toucher aux enregistrements email (MX, SPF, DKIM, DMARC) ; vérifier ces adresses dans la documentation GitHub au moment de le faire.
  - Puis **Enforce HTTPS** dans Settings → Pages, et vérification du domaine au niveau de l'organisation (Verified domains).
  - Domaine en `.app` : inaccessible tant que le certificat HTTPS n'est pas émis (quelques minutes à quelques heures), c'est normal.

---

## Contexte (décisions prises)

- **Paiement** : Stripe Managed Payments (Stripe est Merchant of Record et gère la TVA), via un Payment Link. Après paiement, retour sur `https://<domaine>/#merci`.
- **Prix** : licence à vie 14.90 CHF / 14.90 €, prix de lancement 9.90 CHF / 9.90 € jusqu'au 31 décembre. Valable pour toutes les versions 1.x.
- **Démo** : même .exe que la version complète, limitée à 5 exports au total. La licence, vérifiée hors ligne, débloque l'app.
- **Téléchargement** : GitHub Releases d'un dépôt public séparé (ex. `kortan/rushfold`), fichier toujours nommé `RushFold-Setup.exe`, lien permanent `…/releases/latest/download/RushFold-Setup.exe`.
- **Support** : `kortan.support@proton.me`.
- **Domaine** : pas encore choisi (`kortan.app` ou `kortan.ch`, chez Infomaniak).

## Plus tard (seulement sur demande)
- Pages CGV, Confidentialité, Mentions légales (une vue de plus dans `index.html` plutôt que de nouveaux fichiers, si possible).
- FAQ : mention `LINK.COM*` sur le relevé bancaire et remboursements alignés sur Stripe.
- 1er janvier : passage du prix de lancement (9.90) au prix normal (14.90), sur le site et dans Stripe.
- Nouvelles apps : nouvelle carte sur l'accueil et nouvelle vue.
