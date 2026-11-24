# TP 01 — compte rendu

Les configurations de ce TP sont reprises du corrigé officiel `checkpoint/01-ci` ; la branche part de `checkpoint/00-starter`.

## Contrôles

Chaque service exécute ses tests et sa qualité en parallèle avant la construction de son image. Les rapports de tests et Checkstyle sont conservés sept jours, y compris après un échec. Les images ne sont publiées que sur un push vers `main`. Le workflow de sécurité analyse tout l'historique avec Gitleaks. Les contrôles locaux sont disponibles avec `python scripts/course.py verify`.

Les PR ne sont pas filtrées par chemin : sinon un contrôle requis peut rester en attente lorsqu'un fichier hors du filtre est modifié, et bloquer la fusion.

Supprimer une ligne supprime seulement son contenu de la version courante ; un vrai secret exposé doit être révoqué ou renouvelé. Réécrire l'historique peut retirer les copies Git, mais ne remplace pas la révocation et exige une coordination avec les collaborateurs.

## Expérience GitHub

La [PR brouillon d'expérience](https://github.com/hadjadjilyas/rental-devops-tp/pull/3) a déclenché une [exécution volontairement rouge](https://github.com/hadjadjilyas/rental-devops-tp/actions/runs/45404549653) : Python quality a fini en échec après 8 s et Python tests après 17 s ; les deux s'exécutent en parallèle, et la construction Docker a été ignorée. Le test signale `assert 180.0 == 181.0` à `tests/test_pricing.py:10`. L'artefact structuré `python-test-reports` est resté disponible après échec. La durée totale affichée est 20 s. Le commit suivant restaure l'assertion.

Les sept contrôles de la [PR de rendu](https://github.com/hadjadjilyas/rental-devops-tp/pull/1) ont réussi sur l'exécution initiale. La protection de branche reste conditionnée à la validation de l'enseignant et l'expérience Gitleaks à sa valeur factice.

## Analyse des jobs du point de départ

Les coûts ci-dessous sont des ordres de grandeur, variables selon les caches et les runners ; ils ne sont pas présentés comme des mesures de l'état initial.

| Job initial | Risque couvert | Entrées | Sortie | Coût approximatif |
|---|---|---|---|---|
| Java tests | Régressions fonctionnelles | Sources, JDK 17, dépendances Gradle | Résultats JUnit | 1 à plusieurs minutes |
| Java qualité et sécurité | Erreurs de compilation et secrets présents dans l'historique | Sources, Gradle, historique Git | Statut du build et Gitleaks | 1 à plusieurs minutes |
| Java image | Image non constructible et vulnérabilités de l'image publiée | Sources, Dockerfile, registre, résultats précédents | Image et SARIF Trivy sur main | Plusieurs minutes |
| Pipeline Status | Échec des jobs précédents passé inaperçu | Statuts des trois jobs Java | Statut agrégé | Quelques secondes |
| Python tests | Régressions fonctionnelles | Sources, Python 3.12, dépendances | JUnit Pytest | Quelques dizaines de secondes |
| Python image | Image non constructible et vulnérabilités de l'image publiée | Sources, Dockerfile, registre, tests | Image et SARIF Trivy sur main | 1 à plusieurs minutes |

Le résumé Java initial ne contrôle explicitement que le statut `failure` : un job annulé ou ignoré peut échapper à ce test. Le scan initial de `latest` ne garantit pas l'identité de l'image analysée si le tag bouge.
