# Local Data Integration Guide

## 📁 Structure des Données Locales

Cette application utilise **uniquement des données locales** - aucune API n'est requise.

### Images Locales

Les 12 images des sites patrimoniaux sont stockées en tant que ressources drawable :

```
app/src/main/res/drawable/
├── eljam1.webp         → Question Easy #1
├── eljam2.jpg          → Question Easy #2
├── eljam3.jpg          → Question Easy #3
├── eljam4.webp         → Question Easy #4
├── eljam5.jpg          → Question Easy #5
├── eljam5.webp         → Question Medium #5 (duplicate)
├── eljam6.jpg          → Question Medium #1
├── eljam7.jpg          → Question Medium #2
├── eljam8.jpg          → Question Medium #3
├── eljam9.jpg          → Question Medium #4
├── eljam10.jpg         → Question Medium #5
└── eljam11.jpg         → Question Hard #1
```

### Référence en Ressources

Dans le code Kotlin, les images sont référencées via `R.drawable.*` :

```kotlin
// Dans QuestionRepository.kt
Question(
    id = 1,
    imageResId = R.drawable.eljam1,  // Référence à l'image
    correctAnswer = "El Djem Amphitheater",
    ...
)
```

### Affichage des Images

Les images sont affichées avec **Coil** (bibliothèque de chargement d'images) :

```kotlin
// Dans QuizGameScreen.kt
AsyncImage(
    model = question.imageResId,
    contentDescription = "Heritage site image",
    modifier = Modifier
        .fillMaxSize()
        .clip(RoundedCornerShape(12.dp)),
    contentScale = ContentScale.Crop
)
```

---

## 📊 Structure des Questions

### Format de Question

```kotlin
data class Question(
    val id: Int,                          // ID unique
    @DrawableRes
    val imageResId: Int,                  // Ressource image (R.drawable.*)
    val correctAnswer: String,            // Réponse correcte
    val wrongAnswers: List<String>,       // 3 mauvaises réponses exactement
    val historicalFact: String,           // Fait historique pour le feedback
    val difficulty: Difficulty            // EASY, MEDIUM, ou HARD
)
```

### Distribution des Questions

| Difficulté | Nombre | Images |
|-----------|--------|--------|
| **EASY** | 5 questions | eljam1-5 |
| **MEDIUM** | 5 questions | eljam6-10 |
| **HARD** | 2 questions | eljam11, eljam5 |
| **TOTAL** | 12 questions | 12 images |

---

## 🔄 Flux de Données

### Initialisation du Jeu

```
MainActivity
    ↓
NavigationApp (remet à zéro le NavGraph)
    ↓
DifficultyScreen (utilisateur sélectionne difficulté + timer)
    ↓
QuizViewModel.startGame(difficulty, timerEnabled)
    ├── QuestionRepository.getQuestionsByDifficulty()
    ├── Filtre et mélange les questions
    └── Initialise QuizUiState
    ↓
QuizGameScreen (affiche question + options)
```

### Affichage d'une Question

```
QuizUiState.currentQuestion
    ├── Image: R.drawable.eljamX
    ├── Options: [correctAnswer] + wrongAnswers (mélangées)
    └── Fact: Affiché après soumission
```

### Soumission de Réponse

```
QuizGameScreen (bouton "Submit Answer")
    ↓
QuizViewModel.submitAnswer()
    ├── Compare selectedOption vs correctAnswer
    ├── Met à jour score si correct
    └── Affiche FeedbackOverlay (avec fact)
    ↓
Délai 1.5s → nextQuestion()
    ├── Avance currentIndex
    └── Réinitialise selectedOption
```

---

## 💾 Pas de Persistance

L'application **ne sauvegarde pas les données** :
- ❌ Pas de base de données Room
- ❌ Pas de SharedPreferences
- ❌ Les scores reset au redémarrage
- ✅ État géré uniquement pendant la session

---

## 🏷️ Énumération Difficulty

```kotlin
enum class Difficulty {
    EASY,    // 5-7 secondes par question
    MEDIUM,  // 10-15 secondes par question  
    HARD     // Très difficile, temps limité
}
```

### Utilisation

```kotlin
// Filtrer par difficulté
val easyQuestions = repository.getQuestionsByDifficulty(Difficulty.EASY)

// Comparer difficulté
if (state.difficulty == Difficulty.HARD) {
    // Actions spéciales pour mode difficile
}
```

---

## 🖼️ Formats d'Image Supportés

Les images sont au format `.jpg` ou `.webp` :

| Image | Format | Résolution Approx. |
|-------|--------|-----------------|
| eljam1 | .webp | 1920x1440 |
| eljam2-3, eljam5-11 | .jpg | variable |
| eljam4 | .webp | variable |

**Format WebP** : Plus léger et meilleure compression que JPEG.

---

## 🔧 Configuration Gradle

Pour que les images locales fonctionnent :

```gradle
android {
    // ...
    buildFeatures {
        compose = true  // ✅ Compose doit être activé
    }
}

dependencies {
    implementation(libs.coil)  // ✅ Pour AsyncImage
}
```

---

## 📖 Exemple Complet : Intégration d'une Nouvelle Image

Si vous ajoutez une nouvelle image `new_site.jpg` :

### 1. Copier l'image
```
app/src/main/res/drawable/new_site.jpg
```

### 2. Créer une Question dans QuestionRepository

```kotlin
Question(
    id = 13,
    imageResId = R.drawable.new_site,  // Référence auto-complétée
    correctAnswer = "New Heritage Site",
    wrongAnswers = listOf("Option 2", "Option 3", "Option 4"),
    historicalFact = "This is a fascinating Roman heritage site...",
    difficulty = Difficulty.EASY  // ou MEDIUM, HARD
)
```

### 3. Ajouter à la liste `allQuestions`

L'image s'affichera automatiquement dans QuizGameScreen via AsyncImage.

---

## 🎯 Avantages de la Donnée Locale

✅ **Pas de connexion Internet requise**
✅ **Temps de réponse instant** (pas de réseau)
✅ **Idéal pour les tests et développement**
✅ **Taille d'app légère** (images compressées en WebP)
✅ **Pas de coûts serveur ou API**

---

## ⚠️ Limitation Actuelle

- **12 questions seulement** (extensible en ajoutant des images)
- **1 catégorie** (Roman Heritage uniquement)
- **Pas de mise à jour dynamique** des questions

Pour une application production, vous pourriez :
1. Charger les données depuis un serveur
2. Utiliser Room pour la mise en cache local
3. Implémenter la synchronisation offline

---

## 🧪 Test avec Données Locales

```kotlin
@Test
fun testQuestionAccessibility() {
    val repo = QuestionRepository()
    val questions = repo.getQuestionsByDifficulty(Difficulty.EASY)
    
    // Vérifier que toutes les images sont accessibles
    questions.forEach { question ->
        assertNotEquals(0, question.imageResId)  // ID valide
        assertEquals(3, question.wrongAnswers.size)  // 3 mauvaises réponses
    }
}
```

---

**Dernière mise à jour** : 2026-05-06

