# TP 01 — compte rendu

Les configurations de ce TP sont reprises du corrigé officiel `checkpoint/01-ci` ; la branche part de `checkpoint/00-starter`.

## Contrôles

Chaque service exécute ses tests et sa qualité en parallèle avant la construction de son image. Les rapports de tests et Checkstyle sont conservés sept jours, y compris après un échec. Les images ne sont publiées que sur un push vers `main`. Le workflow de sécurité analyse tout l'historique avec Gitleaks. Les contrôles locaux sont disponibles avec `python scripts/course.py verify`.

Les PR ne sont pas filtrées par chemin : sinon un contrôle requis peut rester en attente lorsqu'un fichier hors du filtre est modifié, et bloquer la fusion.

Supprimer une ligne supprime seulement son contenu de la version courante ; un vrai secret exposé doit être révoqué ou renouvelé. Réécrire l'historique peut retirer les copies Git, mais ne remplace pas la révocation et exige une coordination avec les collaborateurs.

## À vérifier sur GitHub

- Ouvrir une PR brouillon et documenter le premier échec, la ligne responsable, le délai et l'artefact. Les deux commits d'échec et de restauration ont été créés ; localement, Pytest affiche `180.0 == 181.0` à la ligne 10.
- Vérifier la disponibilité d'un rapport lors d'un échec et tester Gitleaks avec la seule valeur factice fournie par l'enseignant.
- Après validation de l'enseignant, configurer les sept contrôles obligatoires et vérifier les PR rouge et verte.

Ces observations et réglages distants n'ont pas encore été effectués.
