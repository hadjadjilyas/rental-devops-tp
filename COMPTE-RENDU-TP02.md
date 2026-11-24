# TP 02 — compte rendu

Les configurations de ce TP sont reprises du corrigé officiel `checkpoint/02-supply-chain` ; la branche part de `checkpoint/01-ci`.

## Risques et contrôles

- Un tag d'action peut être déplacé vers un code malveillant : le SHA complet identifie le commit exécuté ; Dependabot propose les mises à jour en PR.
- Un tag d'image de base peut pointer vers un autre manifeste : le digest SHA-256 fixe le contenu attendu.
- Le tag `latest` d'une image produite peut changer entre publication et analyse : signature, attestations, SBOM et scan utilisent le digest retourné par Buildx.

La commande `python scripts/course.py supply-chain-policy` contrôle localement les références des workflows et Dockerfiles. La signature Cosign utilise l'identité OIDC du workflow sans clé privée persistante ; les attestations documentent la provenance et le SBOM SPDX.

Dependency review examine les dépendances ajoutées ou modifiées dans une PR ; Trivy analyse le contenu final de l'image publiée, y compris ses composants système. Le premier ne remplace donc pas le second.

## Preuves observées sur main

Le graphe de dépendances et les alertes sont activés. La [revue des dépendances](https://github.com/hadjadjilyas/rental-devops-tp/actions/runs/45401970927) a réussi après relance. La politique locale rejette bien `actions/checkout@v7` (code 1) et réussit après restauration du SHA.

Les deux exécutions sur `main` ont réussi : [Java](https://github.com/hadjadjilyas/rental-devops-tp/actions/runs/45405161671) et [Python](https://github.com/hadjadjilyas/rental-devops-tp/actions/runs/45405161611). Chaque workflow publie son image, produit son SBOM, signe et atteste le digest, vérifie les preuves puis transmet les résultats Trivy.

- Digest Python vérifié : `sha256:f415db10d9ff045ef75a720457769396f5d06e9bd00f5a8d0aa2efe9f2ea6e61`.
- Identité Cosign vérifiée : `https://github.com/hadjadjilyas/rental-devops-tp/.github/workflows/ci-pricing.yml@refs/heads/main`.
- Provenance Python : dépôt `hadjadjilyas/rental-devops-tp`, commit `8c93142018bb5c0cb06b4394a34a5719bb982291`.
- Dans le [SBOM Python](https://github.com/hadjadjilyas/rental-devops-tp/actions/runs/45405161611/artifacts/14926340947) : `libc6 2.41-12+deb13u4` (système) et `fastapi 0.162.7` (application).
- Digest Java vérifié : `sha256:af539aecb660de889c448b5bed351700b1b809c3714a828763d04f81fb5b4b93`. Son identité est le workflow `ci-car-rental.yml` sur `main` ; le [SBOM Java](https://github.com/hadjadjilyas/rental-devops-tp/actions/runs/45405161671/artifacts/14925296370) est téléchargeable.
- [Résultats Trivy sur main](https://github.com/hadjadjilyas/rental-devops-tp/security/code-scanning?query=is%3Aopen+branch%3Amain+tool%3ATrivy) : des vulnérabilités ont été signalées, dont des alertes élevées sur Tomcat. La réussite du job signifie que l'analyse a été transmise ; elle ne signifie pas que les images sont exemptes de vulnérabilités.