# Gabarit du prompt final

Respecte cet ordre de sections. Supprime une section seulement si elle est réellement sans objet (ex. pas d'API pour une app 100 % locale) et dis-le en une ligne.

```markdown
# <Nom de code> — Spécification fonctionnelle pour Claude Code

## 0. Instructions pour Claude Code
- Lis l'intégralité de ce document avant d'écrire du code.
- Commence par proposer un plan de mise en œuvre par phases (MVP → V1 → V2) avec la stack de la section 13, puis attends validation.
- Implémente le MVP d'abord, de bout en bout, avec des tests couvrant les critères d'acceptation.
- En cas d'ambiguïté, applique l'hypothèse la plus simple et note-la dans un fichier DECISIONS.md.

## 1. Contexte et objectif
- Application de référence : <app> (<éditeur>) — <une phrase sur ce qu'elle fait>.
- Objectif : construire une application originale offrant ces fonctionnalités, en corrigeant ses défauts connus.
- Usage : <public | interne — outil réservé à une équipe, sans inscription ni onboarding, authentification identifiant/mot de passe, comptes créés par un admin>.
- Nouveau nom de code : <nom>. Ne reprendre ni marque, ni logo, ni textes, ni charte de l'original.
- Proposition de valeur différenciante : <2-3 puces issues des demandes / irritants utilisateurs>.

## 2. Utilisateurs, rôles et permissions
Tableau : rôle | description | peut faire | ne peut pas faire.

## 3. Périmètre et priorisation
Tableau récapitulatif : module | fonctionnalité | priorité (MVP/V1/V2) | origine ([Officiel]/[Déduit]/[Demande]/[Correctif]).

## 4. Modules fonctionnels
Pour chaque module :
### 4.x <Module>
- **But** : …
- **User stories** : « En tant que <rôle>, je veux … afin de … »
- **Règles de gestion** : validations, limites, états et transitions, cas limites.
- **Critères d'acceptation** : liste vérifiable (Given/When/Then ou puces testables).
- **Améliorations vs original** : [Demande]/[Correctif] concernés.

## 5. Parcours utilisateur clés
Onboarding (usage interne : « Premier lancement »), flux principal, flux de collaboration/partage, etc. — étapes numérotées.

## 6. Modèle de données
Entités, champs principaux (type, obligatoire), relations, statuts/énumérations. Diagramme mermaid `erDiagram` si utile.

## 7. Plans, quotas et limites
Usage interne : remplacer cette section par une ligne « Sans objet (usage interne) » et ne garder que la sous-section « Points à valider ».
Usage public, si l'original est freemium/payant : ce qui est limité et comment (à reproduire ou à simplifier — préciser).

### Points à valider
Conflits entre sources et éléments [Déduit] importants : élément | source A | source B | décision retenue.

## 8. Intégrations, import/export, API
Intégrations tierces, formats d'import/export, API publique et webhooks envisagés.

## 9. Notifications
Événements déclencheurs, canaux (in-app, email, push), préférences.

## 10. Exigences non fonctionnelles
Performance, sécurité (auth, RGPD), accessibilité (WCAG AA), i18n, responsive/mobile, hors-ligne, sauvegarde.

## 11. Irritants de l'original à éviter
Liste des bugs et frustrations récurrents relevés, avec le comportement attendu dans la nouvelle app.

## 12. Hors périmètre
Ce qu'on ne fait volontairement pas (et pourquoi).

## 13. Stack technique
Stack retenue (imposée par l'utilisateur, choisie parmi les propositions, ou recommandée par défaut — préciser lequel) : front, back, base de données, auth, temps réel/sync si besoin, hébergement/déploiement, tests. Une ligne de justification par choix structurant.

## Annexe — Sources
URLs complètes et cliquables, groupées : officielles / feedback & bugs / avis & comparatifs. Mentionner les sources non lues (403, login) et les familles de sources sans résultat.
```
