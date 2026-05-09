# Tunisia Heritage Quest - Roman Heritage Quiz Application

## 📱 Project Overview

**Tunisia Heritage Quest** est une application Android de quiz interactive utilisant **Jetpack Compose** et **MVVM Architecture**. L'application teste les connaissances des utilisateurs sur les sites du patrimoine romain en Tunisie avec 12 images locales et 3 niveaux de difficulté.

### ✨ Caractéristiques principales

- 🎮 **12 questions** avec images locales (pas d'API)
- 📊 **3 niveaux de difficulté** : Easy, Medium, Hard
- ⏱️ **Minuteur optionnel** : 15 secondes par question
- 🏆 **Système de scoring** : +10 points par bonne réponse
- 📱 **Interface adaptative** : Support phone/tablet
- 🎨 **Design Méditerranéen** : Couleurs inspirées de la Tunisie
- 🧪 **Tests complets** : Unit tests + UI tests

---

## 🏗️ Architecture

L'application suit l'architecture **MVVM** avec Dependency Injection via Hilt :

```
com.example.tunisiaheritage/
├── data/
│   ├── model/
│   │   └── Question.kt          # Modèle de données
│   └── repository/
│       └── QuestionRepository.kt # Source de données
├── ui/
│   ├── theme/
│   │   ├── Color.kt             # Palette Tunisienne
│   │   ├── Type.kt              # Typographie
│   │   └── Theme.kt             # Thème Material3
│   ├── screens/
│   │   ├── SplashScreen.kt
│   │   ├── MainMenuScreen.kt
│   │   ├── CategorySelectionScreen.kt
│   │   ├── DifficultyScreen.kt
│   │   ├── QuizGameScreen.kt
│   │   └── ResultScreen.kt
│   ├── components/
│   │   ├── QuizOptionButton.kt
│   │   └── FeedbackOverlay.kt
│   ├── viewmodel/
│   │   ├── QuizViewModel.kt     # Logique du jeu
│   │   └── QuizUiState.kt       # État UI
│   └── navigation/
│       ├── Routes.kt
│       └── NavGraph.kt
├── util/
│   └── AdaptiveScreen.kt        # Helpers adaptatifs
├── MainActivity.kt              # Point d'entrée
└── HeritageApplication.kt       # Classe Application + Hilt
```

### Couches Architecturales

```
UI Layer (Composables)
    ↓ Events (clicks, selections)
ViewModel (Logique métier + État)
    ↓ StateFlow<QuizUiState>
Data Layer (Repository + Local Data)
```

---

## 🎯 Screens (6 écrans)

### 1. **Splash Screen** (2s)
- Logo + Titre
- Navigation automatique vers le menu principal

### 2. **Main Menu Screen**
- Statistiques (Questions, Niveaux, Catégories)
- Bouton "START QUIZ"

### 3. **Category Selection Screen**
- Grille de catégories (6 catégories)
- Seule la catégorie "Roman Heritage" est active
- Les autres affichent "Coming soon"

### 4. **Difficulty Selection Screen**
- 3 cartes de difficulté (Easy, Medium, Hard)
- Toggle "Enable Timer"
- Affichage des descriptions et temps limite

### 5. **Quiz Game Screen**
- ✅ Image du site patrimonial
- ❓ Question d'identification
- 4 boutons de réponse
- Barre de progression
- Minuteur optionnel
- Score en temps réel
- Feedback immédiat après soumission

### 6. **Result Screen**
- Score final (X/Y)
- Pourcentage et message de performance
- Boutons "Play Again" / "Back to Menu"

---

## 📦 Données Locales

### Questions & Images

Les 12 images sont stockées dans `/app/src/main/res/drawable/` :
- `eljam1.webp` (Easy)
- `eljam2.jpg` à `eljam5.jpg` (Easy/Medium)
- `eljam4.webp`, `eljam5.webp` (Medium)
- `eljam6.jpg` à `eljam11.jpg` (Medium/Hard)

**Distance des données** :
- Aucune API n'est utilisée
- Toutes les questions sont en mémoire (via `QuestionRepository`)
- Les images sont des ressources drawable

### QuestionRepository

```kotlin
// Récupérer les questions par difficulté
val easyQuestions = repository.getQuestionsByDifficulty(Difficulty.EASY)  // 5 questions
val mediumQuestions = repository.getQuestionsByDifficulty(Difficulty.MEDIUM)  // 5 questions
val hardQuestions = repository.getQuestionsByDifficulty(Difficulty.HARD)  // 2 questions
```

---

## 🎮 ViewModel & État

### QuizUiState

```kotlin
data class QuizUiState(
    val questions: List<Question> = emptyList(),
    val currentIndex: Int = 0,
    val score: Int = 0,
    val selectedOption: String? = null,
    val isAnswerRevealed: Boolean = false,
    val isCorrect: Boolean = false,
    val timeLeft: Int = 15,
    val isTimerEnabled: Boolean = true,
    val isGameFinished: Boolean = false,
    val totalQuestions: Int = 10,
    val difficulty: Difficulty = Difficulty.EASY,
    val correctAnswer: String = ""
)
```

### QuizViewModel - Méthodes Principales

- `startGame(difficulty, timerEnabled)` - Initialiser une nouvelle partie
- `selectOption(option)` - Sélectionner une réponse
- `submitAnswer()` - Valider la réponse et afficher les retours
- `skipQuestion()` - Passer à la question suivante
- `resetGame()` - Réinitialiser
- `pauseTimer()` / `resumeTimer()` - Gestion du minuteur lors de pause/reprise

---

## 🎨 Design System

### Couleurs (Inspiré de la Tunisie)

| Couleur | Hex Code | Utilisation |
|---------|----------|-------------|
| Mediterranean Blue | #1A5276 | Primaire, textes titres |
| Ochre Brown | #C59B5F | Secondaire, accents |
| Sand Beige | #F5DEB3 | Arrière-plan clair |
| Olive Green | #8B9A46 | Tertiaire |
| Terracotta Orange | #CD7F32 | Accents supplémentaires |

### Typographie

- **Titres** : Serif élégante (Garamond-like)
- **Corps** : Sans-Serif claire (Roboto)
- **Labels** : Font poids variable

---

## 🧪 Tests

### Tests Unitaires (JUnit + MockK)

```
src/test/java/com/example/tunisiaheritage/
├── ui/viewmodel/
│   └── QuizViewModelTest.kt
├── data/repository/
│   └── QuestionRepositoryTest.kt
└── ui/state/
    └── QuizUiStateTest.kt
```

**Couverture** :
- ✅ Correct answer increases score
- ✅ Wrong answer doesn't increase score
- ✅ Game finishes after last question
- ✅ Question filtering by difficulty
- ✅ State transitions

### Tests d'Interface (Compose Testing)

```
src/androidTest/java/com/example/tunisiaheritage/ui/screens/
├── MainMenuScreenTest.kt
├── QuizGameScreenTest.kt
├── ResultScreenTest.kt
└── DifficultyScreenTest.kt
```

**Tests UI** :
- ✅ Affichage du titre et contenu
- ✅ Clicabilité des boutons
- ✅ Affichage des options de réponse
- ✅ Minuteur visible et fonctionnel
- ✅ Score affiché correctement

---

## 🔧 Configuration & Dépendances

### Gradle Dependencies

- **Jetpack Compose** : Material3, Navigation, Activity
- **Android Lifecycle** : ViewModel, LiveData, Coroutines
- **Hilt** : Dependency Injection
- **Coil** : Chargement d'images
- **Testing** : JUnit4, MockK, Turbine

### Build Configuration

- **minSdk** : 24 (Android 7.0)
- **targetSdk** : 36 (Android 15)
- **Java** : 11
- **Kotlin** : 2.0.21

---

## 🚀 Comment Utiliser

### 1. Cloner le Projet
```bash
cd C:\Users\ASUS\Downloads\LAB
```

### 2. Construire le Projet
```bash
./gradlew build
```

### 3. Exécuter l'Application
```bash
./gradlew installDebug
```

### 4. Exécuter les Tests
```bash
# Tests unitaires
./gradlew test

# Tests d'instrumentation
./gradlew connectedAndroidTest
```

---

## ♿ Accessibilité

- Descriptions de contenu pour les images
- Tailles de texte lisibles
- Contraste de couleurs suffisant
- Support RightToLeft pour les langues RTL

---

## 📱 Responsive Design

### Adaptive Layout

L'app s'adapte automatiquement avec `WindowSizeClass` :
- **Compact** (téléphone portrait) : Colonne simple
- **Medium** (téléphone paysage) : Colonnes multiples
- **Expanded** (tablette) : Layout en grille/2 colonnes

---

## 🔐 Authentification

❌ **Pas d'authentification requise**
- Pas de sign in / sign up
- Aucune gestion utilisateur
- Données locales uniquement

---

## 📝 Grille de Notation (Total: 100 pts)

| Critère | Points | Implémentation |
|---------|--------|-----------------|
| Architecture Components | 15 | MVVM, StateFlow, Repository, DI |
| Navigation | 10 | Compose Navigation, 6 écrans |
| Activity Lifecycle | 10 | Logging, lifecycle aware coroutines |
| Testing | 15 | Unit + UI tests |
| Adaptive UI | 15 | WindowSizeClass, responsive layouts |
| UI/UX | 15 | Material3, design système tunisien |
| Functionality | 10 | Quiz complet, timer, feedback |
| Code Quality | 10 | Clean code, KDoc, error handling |

---

## 📸 Ressources Images

Toutes les images patrimoine sont stockées dans le drawable :

```
app/src/main/res/drawable/
├── eljam1.webp
├── eljam2.jpg
├── eljam3.jpg
├── eljam4.webp
├── eljam5.jpg
├── eljam5.webp
├── eljam6.jpg
├── eljam7.jpg
├── eljam8.jpg
├── eljam9.jpg
├── eljam10.jpg
└── eljam11.jpg
```

---

## 🐛 Dépannage

### Problème : Images non affichées
**Solution** : Vérifier que les fichiers image sont dans `app/src/main/res/drawable/`

### Problème : Erreur build "java not found"
**Solution** : Configurer `JAVA_HOME` ou installer JDK 11+

### Problème : Tests ne compilent pas
**Solution** : `./gradlew clean build --refresh-dependencies`

---

## 📚 Ressources Supplémentaires

- [Jetpack Compose Documentation](https://developer.android.com/jetpack/compose)
- [Material Design 3](https://m3.material.io/)
- [Hilt Documentation](https://dagger.dev/hilt/)
- [Android Architecture](https://developer.android.com/guide/architecture)

---

## 👥 Contributeurs

Projet créé pour fins éducatives - Quiz interactif sur le patrimoine Romain tunisien.

---

**Dernière mise à jour** : 2026-05-06
**Version** : 1.0

