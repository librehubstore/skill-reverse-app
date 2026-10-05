---
name: reverse-app-prompt
description: Rétro-ingénierie fonctionnelle d'une application existante (SaaS, mobile, desktop, open-source) à partir de tout ce qui est public sur Internet — site officiel, documentation, changelog, tarifs, API, mais aussi bugs, demandes d'amélioration, forums, avis des stores — pour produire un prompt fonctionnel complet, prêt à donner à Claude Code afin de construire une nouvelle application équivalente ou meilleure. Utilise ce skill dès que l'utilisateur veut « cloner », « refaire », « s'inspirer de », « créer une alternative à », « reverse-engineer » une app, rédiger un cahier des charges ou un PRD à partir d'un produit existant, ou lister toutes les fonctionnalités d'une application concurrente pour la reconstruire — même s'il ne prononce pas le mot « prompt ».
---

# Reverse App Prompt

But : transformer une application existante en un **prompt fonctionnel exhaustif** que Claude Code pourra exécuter pour bâtir une nouvelle application. La valeur du résultat dépend de deux choses : la **couverture** (n'oublier aucune fonctionnalité réelle) et la **lucidité** (savoir ce qui frustre les utilisateurs actuels, pour faire mieux).

## 1. Poser les questions

Demande à l'utilisateur, en un seul message :

1. **Quelle est l'application ?** (nom, et éditeur si ambigu)
2. **Quels sont ses sites officiels ?** (site, docs, blog, GitHub… — « je ne sais pas » est une réponse valable : tu les trouveras)
3. **Usage public ou interne ?** Public = produit ouvert à des inconnus (comme l'original). Interne = outil réservé à une équipe ou une entreprise. Par défaut : public.
4. **Stack technique cible ?** L'utilisateur peut imposer la sienne (ex. « NestJS + Angular + PostgreSQL ») ou répondre « propose-moi » : tu lui soumettras alors des options adaptées après l'analyse (étape 4 bis), car la bonne stack dépend de ce que la recherche aura révélé (temps réel, mobile, hors-ligne, API…).

### Mode interne

Un outil interne n'a pas à convaincre ni à recruter des utilisateurs : tout ce qui sert l'acquisition et la monétisation devient du poids mort qui ralentit la construction. Si l'utilisateur choisit l'usage interne, le prompt final :

- **Authentification** : identifiant + mot de passe uniquement (mots de passe hachés, sessions sécurisées). Comptes créés par un administrateur ; le premier admin est créé au premier lancement (seed ou variable d'environnement). L'admin peut réinitialiser un mot de passe, l'utilisateur peut changer le sien.
- **Retire** : inscription publique, onboarding (visites guidées, checklists, données de démo, emails de bienvenue), invitations par email, vérification d'email, connexion sociale/SSO, mot de passe oublié par email, plans/tarifs/quotas/paywall/facturation, essais gratuits, pages marketing.
- **Retire aussi** la 2FA (contraire à « identifiant + mot de passe uniquement »), la suppression de compte en libre-service (remplacée par désactivation/anonymisation par l'admin).
- **Garde** : rôles et permissions (ils ont toujours un sens en interne), toutes les fonctionnalités métier, y compris celles réservées aux offres payantes de l'original. Les emails métier (rappels, rapports planifiés) passent en notifications in-app, l'email devenant optionnel si un SMTP est configuré.
- **Par défaut** : une instance = une organisation, auto-hébergeable (Docker). Le parcours « Onboarding » de la section 5 devient « Premier lancement » (création de l'admin, premier board).
- Les demandes utilisateurs qui portaient sur un élément retiré (ex. « onboarding plus simple ») vont dans « Hors périmètre » avec le reste, pas à la poubelle en silence.

La collecte ne change pas : continue de lire tarifs et onboarding de l'original, car ils révèlent des fonctionnalités. Le filtrage se fait à la rédaction. Liste les éléments retirés dans la section « Hors périmètre » avec la mention « usage interne », pour que le choix soit visible et réversible.

Précise qu'il peut ajouter, s'il le souhaite, d'autres contraintes (plateforme cible, périmètre restreint, langue du prompt). Sans précision : prompt en français.

Si l'utilisateur a déjà donné ces informations dans sa demande, ne les redemande pas.

## 2. Outils de collecte

Les noms d'outils ci-dessous sont ceux de Claude Code ; dans un autre agent, utilise ses équivalents.

- **Recherche web** (WebSearch) pour découvrir les sources, **lecture de page** (WebFetch) pour les lire. C'est rapide et sans installation ; lance plusieurs lectures en parallèle.
- **Navigateur piloté** (claude-in-chrome, Playwright MCP ou équivalent) en secours, dans trois cas :
  - WebFetch renvoie une page vide ou tronquée parce qu'elle est rendue en JavaScript (boards Canny/UserVoice/Featurebase, avis App Store/Google Play, docs en SPA) ;
  - WebFetch reçoit un **403 / blocage anti-bot** sur une source riche (G2, TrustRadius, Capterra, SaaSHub, Reddit…) : un vrai navigateur passe en général. Ne te contente pas de l'extrait des résultats de recherche quand la page complète est accessible ainsi ;
  - une page lue en texte brut **semble incohérente** : typiquement une grille tarifaire où toutes les offres paraissent identiques, parce que les coches et croix sont des icônes que le texte extrait ne montre pas. Vérifie-la visuellement dans le navigateur avant d'en tirer la répartition gratuit/payant.

  Dans Claude Code, charge d'abord le skill `claude-in-chrome`. Si aucun navigateur n'est disponible, note la source comme « non lue » dans les notes et continue. N'utilise pas de session connectée pour accéder à des contenus privés du compte de l'utilisateur sans son accord.
- Si l'application est open-source : le dépôt GitHub/GitLab est une mine (README, issues, discussions, labels `enhancement`/`bug`, roadmap, CHANGELOG). Les pages `github.com/<org>/<repo>/issues?q=...` se lisent bien avec WebFetch.

## 3. Collecter — deux volets

**Emplacement des fichiers** : si l'utilisateur ou la tâche impose un dossier de sortie, utilise-le. Sinon, le dossier de travail de la session (celui où Claude Code a été lancé). Tout y va : notes et prompt final.

Tiens un fichier de notes `<slug-app>-research/notes.md` dans ce dossier de sortie, alimenté au fil de l'eau (fonctionnalité → URL complète de la source, jamais un nom de site seul : l'annexe du prompt sera construite à partir de ces notes). La collecte est longue : sans ces notes, le contexte se perd et des fonctionnalités disparaissent au moment de la rédaction.

### Volet A — Ce que l'application fait (sources officielles)

Par ordre de rendement :

1. **Centre d'aide / documentation** : chaque article décrit souvent une fonctionnalité précise, avec ses règles et ses limites. Parcours le sommaire en entier avant de plonger.
2. **Page tarifs** : la matrice des plans révèle la liste quasi complète des fonctionnalités et les limites (quotas, nombre d'utilisateurs, stockage).
3. **Changelog / release notes / blog produit** : fonctionnalités récentes ou discrètes absentes du marketing.
4. **Documentation API / webhooks** : révèle le **modèle de données** réel (entités, champs, statuts, relations). Très précieux pour la section modèle de données.
5. **Pages fonctionnalités, intégrations, raccourcis clavier, sécurité/conformité, CGU** (limites, rôles, rétention).
6. **Vidéos et tutoriels** (titres et descriptions) pour les parcours utilisateur.

**Pas de centre d'aide public ?** (404, derrière login, inexistant) Ne t'arrête pas là, c'est fréquent. Remplace-le, par ordre de rendement : documentation API (souvent la source la plus précise sur les règles et les champs), FAQ intégrée aux pages tarifs/fonctionnalités, fiches des stores (App Store, Google Play, Chrome Web Store : descriptions et notes de version), tutoriels tiers et vidéos YouTube, archives du centre d'aide sur web.archive.org. Signale dans les notes que l'aide officielle manquait, pour que les éléments déduits soient pondérés en conséquence.

### Volet B — Ce que les utilisateurs veulent et subissent

Cherche avec des requêtes comme `"<app>" feature request`, `"<app>" bug`, `"<app>" roadmap`, `"<app>" vs`, `"<app>" alternative`, `site:reddit.com <app>`, `"<app>" canny`, `"<app>" uservoice`, `"<app>" avis` :

- **Boards de feedback / roadmap publique** (Canny, UserVoice, Featurebase, Productboard, Upvoty, forum communautaire) : trie par votes, les demandes les plus votées sont les meilleures opportunités.
- **Issues GitHub** (bugs et `enhancement`), discussions.
- **Reddit, Hacker News, Product Hunt** : irritants concrets, comparaisons.
- **Avis** : App Store, Google Play, G2, Capterra, Trustpilot — concentre-toi sur les avis 1-3 étoiles, ils listent les manques.
- **AlternativeTo / comparatifs « X vs Y »** : fonctionnalités que les concurrents ont et que l'application n'a pas.

**Couvre au moins trois familles de sources** parmi : board de feedback/forum officiel, GitHub, Reddit/HN, avis des stores, sites d'avis (G2, Capterra…), comparatifs. Les sites d'avis seuls donnent une vision biaisée (avis courts, souvent anciens) ; Reddit et les boards de feedback révèlent les vraies demandes, avec leur popularité. Pour Reddit, cherche aussi les subreddits du domaine (r/productivity, r/projectmanagement…) et pas seulement le nom de l'app. Si une famille ne donne rien après une recherche sérieuse, note-le plutôt que de l'ignorer en silence.

Pour chaque bug récurrent, retiens le **comportement attendu** (c'est ce qui ira dans le prompt), pas seulement le symptôme.

### Quand s'arrêter

Vise large (souvent 30 à 80 pages selon la taille de l'app). Arrête-toi quand les nouvelles pages n'apportent plus de fonctionnalité nouvelle. Pour une grosse application, tu peux lancer deux sous-agents en parallèle (volet A et volet B) qui écrivent chacun dans leur fichier de notes, si ton environnement propose des sous-agents.

## 4. Synthétiser

À partir des notes, construis l'inventaire en marquant l'origine de chaque élément — c'est ce qui permet au lecteur de distinguer le fidèle de l'amélioré :

- `[Officiel]` documenté par l'éditeur
- `[Déduit]` inféré (ex. depuis l'API ou une capture), à confirmer
- `[Demande]` demande d'amélioration utilisateurs (indique la popularité si connue)
- `[Correctif]` comportement corrigé par rapport à un bug connu de l'original

**Sources peu fiables** : écarte les pages qui semblent générées automatiquement (fiches d'annuaires ou « reviews » génériques, sans détail concret, souvent copiées d'un site à l'autre) dès qu'elles contredisent une source officielle ou affirment une fonctionnalité que rien d'autre ne confirme. Une fonctionnalité n'apparaissant que dans une telle source ne doit pas entrer dans le prompt, ou seulement en `[Déduit]` avec la mention de sa source. Note les sources écartées dans les notes.

**Sources contradictoires** (ex. une fonctionnalité gratuite selon un vieil avis, payante selon la page tarifs) : la source officielle la plus récente l'emporte, car les avis datent souvent d'une offre révolue. Ne tranche pas en silence : liste chaque conflit dans une sous-section « Points à valider » de la section 7 (ou de la section concernée), avec les deux sources et la décision retenue.

Regroupe ensuite en modules fonctionnels cohérents, déduis les rôles, les entités et leurs relations, et priorise : **MVP** (cœur indispensable pour que l'app soit utilisable), **V1**, **V2+**. Les demandes utilisateurs très votées peuvent remonter en V1 si elles sont un vrai différenciateur.

## 4 bis. Choisir la stack

Si l'utilisateur a imposé une stack, garde-la telle quelle : ne la « corrige » pas, signale seulement en une ligne un vrai point de friction (ex. besoin hors-ligne avec une stack 100 % serveur).

Sinon, lis `references/stacks.md`, déduis le profil de l'application à partir de la synthèse (web collaboratif, temps réel, mobile, hors-ligne, desktop, outil interne CRUD…) et propose **2 ou 3 options** adaptées, la recommandée en premier, chacune avec une phrase sur ce qui la justifie pour cette application précise. Utilise l'outil de question à choix (AskUserQuestion dans Claude Code) s'il existe ; l'utilisateur doit toujours pouvoir saisir sa propre stack. Si personne ne peut répondre (exécution autonome), retiens l'option recommandée et indique-le dans le prompt.

## 5. Rédiger le prompt

Lis `references/prompt-template.md` et suis sa structure. Points qui comptent :

- **Fonctionnel, pas technique** : décrire le *quoi* et les règles de gestion, avec des critères d'acceptation vérifiables. La stack retenue va en section 13 ; le reste du prompt ne dépend pas d'elle.
- **Autoportant** : Claude Code n'aura que ce prompt. Pas de « comme dans <app> » sans expliquer le comportement.
- **Pas de reprise de l'identité de l'original** : nouveau nom (proposer un nom de code), pas de logo, de textes marketing copiés ni de charte graphique reproduite. On reconstruit des fonctionnalités, pas une marque. Les références à l'application originale restent dans la section contexte et les sources.
- **Exhaustif mais lisible** : listes et tableaux plutôt que prose. Un prompt long est normal ici ; un prompt flou ne l'est pas.

Enregistre le résultat dans `<slug-app>-prompt.md` (dossier de sortie défini plus haut), sources en annexe avec des **URLs complètes** (pas de chemins abrégés : le lecteur doit pouvoir cliquer). Dans le chat, donne un résumé court : nombre de modules / fonctionnalités, top 5 des améliorations issues du volet B, chemin du fichier, et les zones d'incertitude (`[Déduit]`) à valider.
