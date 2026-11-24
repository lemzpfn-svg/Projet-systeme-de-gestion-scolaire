# Système de gestion scolaire

Projet base de données de la prépa intégrée CESI × ISJA (Dakar, 2026-2027), mené en 5 semaines par une équipe de trois étudiants : Lamine BA, Edouard Domingo et Abdoulaye Diack.

Une école rurale de trois classes gérait ses élèves, ses notes et ses emplois du temps dans des documents séparés. Nous avons conçu son système d'information avec MERISE, créé la base en SQL, puis l'avons fait évoluer pour accueillir les spécialités. Une preuve de concept montre ensuite comment l'IA peut aider la direction à repérer les élèves en difficulté.

Page de présentation : https://github.com/lemzpfn-svg/Projet-systeme-de-gestion-scolaire

## Démarche

Enquêter → analyser → modéliser → créer → tester → faire évoluer → intégrer l'IA → démontrer.

- **Enquêtes :** deux questionnaires, l'un à l'ISJA (qui utilise PRONOTE), l'autre auprès de l'école cliente.
- **Modélisation :** 34 règles de gestion, 46 données au dictionnaire, dépendances fonctionnelles, modèle en 3FN. Le MCD est centré sur le COURS ; notes, absences et emploi du temps sont des associations (ÉVALUER, S'ABSENTER, PLANIFIER).
- **Base :** 12 tables, 16 clés étrangères, contraintes CHECK et déclencheurs. Jeu de test de 288 notes, 23 tests de rejet.
- **Évolution (spécialités) :** entité GROUPE et association ÊTRE INSCRIT, ajoutées par un script de migration sans recréer la base. Les 288 notes sont conservées.
- **IA :** une requête d'alerte, puis un score de risque explicable sur 100.

## Contenu du dépôt

| Dossier | Contenu |
| --- | --- |
| `sql/mysql/` | Scripts MySQL 8 : création (MPD), jeu de test, tests de contraintes, évolution spécialités, tests de l'évolution |
| `sql/sqlite-cloud/` | Les mêmes scripts adaptés à SQLite Cloud |
| `ia/` | Requête d'alerte, requête d'indicateurs, script Python du score, base de test SQLite |
| `ia/resultats/` | Scores calculés (CSV) et graphiques |
| `docs/` | Page de présentation du projet (`index.html`) et diapositives de la soutenance, publiée avec GitHub Pages |

## Exécuter les scripts

**SQLite Cloud :** exécuter dans l'ordre les fichiers de `sql/sqlite-cloud/`, de `01` à `05`. Le fichier `04` fait passer la base en version avec spécialités.

**MySQL 8 :** même ordre avec les fichiers de `sql/mysql/`.

**Preuve de concept IA** (Python 3, pandas, matplotlib) :

```bash
cd ia
python3 poc_risque.py ecole.db --demo --sortie resultats
```

Le script affiche les élèves à rencontrer et à surveiller, avec la raison de chaque alerte, puis rejoue un scénario de démonstration : un élève manque deux jours pendant les récoltes et passe en alerte.

## Règles du score

| Critère | Points |
| --- | --- |
| Moyenne générale basse (maximum à 8 ou moins) | 35 |
| Matières sous 10 (5 points par matière) | 15 |
| Baisse des notes plus forte que celle de la classe | 20 |
| Absences et retards non justifiés | 30 |
| 3 absences non justifiées ou plus | alerte immédiate |

Moins de 25 : pas d'alerte. De 25 à 44 : à surveiller. 45 et plus : à rencontrer.

Avec 24 élèves sur un trimestre, un modèle d'apprentissage ne serait pas fiable : les règles restent lisibles et vérifiables. Les élèves du jeu de test sont fictifs.

