# TP 02 — compte rendu

Les configurations de ce TP sont reprises du corrigé officiel `checkpoint/02-supply-chain` ; la branche part de `checkpoint/01-ci`.

## Risques et contrôles

- Un tag d'action peut être déplacé vers un code malveillant : le SHA complet identifie le commit exécuté ; Dependabot propose les mises à jour en PR.
- Un tag d'image de base peut pointer vers un autre manifeste : le digest SHA-256 fixe le contenu attendu.
- Le tag `latest` d'une image produite peut changer entre publication et analyse : signature, attestations, SBOM et scan utilisent le digest retourné par Buildx.

La commande `python scripts/course.py supply-chain-policy` contrôle localement les références des workflows et Dockerfiles. La signature Cosign utilise l'identité OIDC du workflow sans clé privée persistante ; les attestations documentent la provenance et le SBOM SPDX.

Dependency review examine les dépendances ajoutées ou modifiées dans une PR ; Trivy analyse le contenu final de l'image publiée, y compris ses composants système. Le premier ne remplace donc pas le second.

## Preuves à relever sur GitHub

Après activation du graphe de dépendances et des alertes, puis une publication réussie sur `main`, relever le digest, l'identité du certificat, le dépôt et le commit de la provenance, deux composants du SBOM et le lien du rapport Trivy. Vérifier aussi que la PR déclenche Dependency review. Ces preuves distantes ne sont pas encore disponibles.
