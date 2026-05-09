# CSV & Excel Dataset Integration

## Overview

L'application **Tunisia Heritage Quest** supporte maintenant le chargement des questions depuis :

1. **Fichier CSV** (`questions.csv`) dans le dossier assets
2. **Fichier Excel** (`el_jem_quiz_dataset.xlsx`) dans le dossier assets
3. **Questions hardcodées** (fallback si aucun fichier CSV disponible)

---

## Format du CSV

Les questions doivent être dans un fichier `questions.csv` avec la structure suivante :

### En-têtes (Header Row)
```
id,imageResId,correctAnswer,wrongAnswer1,wrongAnswer2,wrongAnswer3,historicalFact,difficulty
```

### Structure des colonnes

| Colonne | Type | Description | Exemple |
|---------|------|-------------|---------|
| `id` | Int | Identifiant unique | 1 |
| `imageResId` | String | Nom de la ressource drawable | eljam1 |
| `correctAnswer` | String | Réponse correcte | El Djem Amphitheater |
| `wrongAnswer1` | String | 1ère mauvaise réponse | Dougga Theater |
| `wrongAnswer2` | String | 2ème mauvaise réponse | Carthage Ruins |
| `wrongAnswer3` | String | 3ème mauvaise réponse | Sfax Fortress |
| `historicalFact` | String | Fait historique (feedback) | El Djem's amphitheater... |
| `difficulty` | String | Difficulté : EASY, MEDIUM, HARD | EASY |

---

## Format du Fichier CSV Complet

```csv
id,imageResId,correctAnswer,wrongAnswer1,wrongAnswer2,wrongAnswer3,historicalFact,difficulty
1,eljam1,El Djem Amphitheater,Dougga Theater,Carthage Ruins,Sfax Fortress,El Djem's amphitheater is one of the largest built outside Rome with capacity of 35000 spectators,EASY
2,eljam2,Dougga,Carthage,Kairouan,Sfax,Dougga is one of the most impressive Roman archaeological sites in North Africa preserved on a hilltop,EASY
3,eljam3,Roman Theatre,Temple,Mosque,Palace,Roman theatres were used for theatrical performances and public gatherings,EASY
```

---

## Comment Convertir Excel en CSV

### Méthode 1: Via Excel/LibreOffice

1. Ouvrir le fichier `el_jem_quiz_dataset.xlsx` dans Excel
2. Vérifier que la structure correspond au format ci-dessus
3. **Sauvegarder sous** → Format CSV (séparé par virgules)
4. Nommer le fichier `questions.csv`
5. Copier dans `app/src/main/assets/questions.csv`

### Méthode 2: Via Python

```python
import pandas as pd

# Lire le fichier Excel
df = pd.read_excel('el_jem_quiz_dataset.xlsx', sheet_name=0)

# Exporter en CSV
df.to_csv('questions.csv', index=False)
```

### Méthode 3: Via Script PowerShell

```powershell
# Installer si nécessaire
Install-Module -Name ImportExcel

# Lire et convertir
$excel = Import-Excel -Path 'el_jem_quiz_dataset.xlsx'
$excel | Export-Csv -Path 'questions.csv' -NoTypeInformation
```

---

## Intégration dans l'Application

### Fichiers Clés

```
app/src/main/assets/
├── questions.csv              # 📄 Fichier CSV avec questions
└── el_jem_quiz_dataset.xlsx   # 📊 Fichier Excel source

app/src/main/java/com/example/tunisiaheritage/
└── data/
    ├── loader/
    │   └── QuestionLoader.kt  # Classe qui charge les questions
    └── repository/
        └── QuestionRepository.kt  # Utilise QuestionLoader
```

### Code - Comment ça Fonctionne

```kotlin
// QuestionRepository.kt
@Singleton
class QuestionRepository @Inject constructor(private val context: Context) {
    
    private val allQuestions: List<Question> by lazy {
        val loader = QuestionLoader(context)
        val csvQuestions = loader.loadQuestionsFromCsv("questions.csv")
        
        // Utilise CSV si disponible, sinon les questions hardcodées
        if (csvQuestions.isNotEmpty()) {
            csvQuestions
        } else {
            getHardcodedQuestions()
        }
    }
    
    // ...
}
```

### Processus de Chargement

```
App Start
    ↓
QuestionRepository créé
    ↓
QuestionLoader.loadQuestionsFromCsv("questions.csv")
    ↓
Fichier trouvé? 
    ├─ OUI → Lecture CSV et parsing
    │         ↓
    │    Questions créées
    │         ↓
    │    Retour au Repository
    │
    └─ NON → Utiliser questions hardcodées
```

---

## Ressources Drawable

L'attribut `imageResId` du CSV doit correspondre à un fichier dans `app/src/main/res/drawable/` :

```
Valeur CSV    →    Fichier Drawable
─────────────────────────────────────
eljam1        →    eljam1.webp
eljam2        →    eljam2.jpg
eljam3        →    eljam3.jpg
...
eljam11       →    eljam11.jpg
```

**Note:** Les tirets dans les noms sont remplacés par des underscores en Android.

---

## Difficultés Supportées

- `EASY` - 5 secondes recommandé de réflexion
- `MEDIUM` - 10 secondes recommandé
- `HARD` - 15 secondes ou plus

La difficulté doit être en **MAJUSCULES** dans le CSV.

---

## Exemple Complet - 3 Questions

```csv
id,imageResId,correctAnswer,wrongAnswer1,wrongAnswer2,wrongAnswer3,historicalFact,difficulty
1,eljam1,El Djem Amphitheater,Dougga Theater,Carthage Ruins,Sfax Fortress,El Djem amphitheater built outside Rome held 35000 spectators,EASY
2,eljam2,Dougga,Carthage,Kairouan,Sfax,Dougga is most impressive Roman site in North Africa on a hilltop,MEDIUM
3,eljam3,Roman Theatre,Temple,Mosque,Palace,Theatres used for performances and public gatherings in Roman times,HARD
```

---

## Étapes pour Utiliser Votre Dataset

### 1. Prépa du Fichier Excel

Ouvrez `data collection/el_jem_quiz_dataset.xlsx` et :
- ✅ Vérifiez que chaque ligne a 8 colonnes
- ✅ Assurez-vous que les difficultés sont EASY, MEDIUM ou HARD
- ✅ Vérifiez que `imageResId` correspond aux fichiers dans `drawable/`

### 2. Conversion en CSV

Convertissez le fichier Excel en CSV (voir méthodes ci-dessus)

### 3. Copie des Fichiers

```bash
# Copier le CSV
cp questions.csv C:\Users\ASUS\Downloads\LAB\app\src\main\assets\

# Vérifier
dir C:\Users\ASUS\Downloads\LAB\app\src\main\assets\
```

### 4. Test

```bash
# Build et test
./gradlew clean build
./gradlew test
./gradlew installDebug
```

---

## Validation

Pour vérifier que vos données CSV sont correctes :

```kotlin
// Dans un test
@Test
fun testCsvQuestions() {
    val loader = QuestionLoader(context)
    val questions = loader.loadQuestionsFromCsv("questions.csv")
    
    // Vérifications
    assert(questions.isNotEmpty()) { "CSV ne doit pas être vide" }
    questions.forEach { q ->
        assert(q.wrongAnswers.size == 3) { "Exactement 3 mauvaises réponses" }
        assert(q.correctAnswer.isNotEmpty()) { "Réponse correcte ne doit pas être vide" }
        assert(q.imageResId != 0) { "Image ressource invalide" }
    }
}
```

---

## Dépannage

### ❌ "CSV file not found"
**Solution:** Vérifiez que `questions.csv` est dans `app/src/main/assets/`

### ❌ "Image resource not found"
**Solution:** Vérifiez que l'imageResId du CSV correspond à un fichier dans `drawable/`

### ❌ "Invalid difficulty"
**Solution:** Les difficultés doivent être EASY, MEDIUM ou HARD (majuscules)

### ❌ "Wrong number of columns"
**Solution:** Vérifiez que le CSV a exactement 8 colonnes avec les headers corrects

### ❌ "App uses hardcoded questions"
**Solution:** Le CSV n'a pas pu être chargé. Vérifiez les logs pour voir l'erreur exacte.

---

## Format du Fichier Assets

Le fichier CSV doit être encodé en **UTF-8** sans BOM.

Si vous utilisez Excel :
1. Fichier → Options → Avancé
2. Encoding: **UTF-8**
3. Sauvegarder

---

## Performance

- ✅ Chargement **lazy** (première utilisation seulement)
- ✅ Questions mises en cache en mémoire
- ✅ Parsing CSV optimisé avec fastcsv
- ✅ Fallback automatique si CSV indisponible

---

## Extension Future

Pour ajouter d'autres formats :

```kotlin
class QuestionLoader(private val context: Context) {
    
    fun loadQuestionsFromJson(filename: String): List<Question> {
        // À implémenter
    }
    
    fun loadQuestionsFromXml(filename: String): List<Question> {
        // À implémenter
    }
}
```

---

**Dernière mise à jour:** 2026-05-06
**Version:** 1.1.0 (Avec support CSV/Excel)

