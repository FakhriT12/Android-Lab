# Getting Started - Tunisia Heritage Quest

## 🚀 Démarrage Rapide

### Prérequis

- ✅ Android Studio Arctic Fox+
- ✅ JDK 11+
- ✅ Android SDK 24+ 
- ✅ Gradle 9.0+

### 1️⃣ Build du Projet

```bash
cd C:\Users\ASUS\Downloads\LAB

# Nettoyer et reconstruire
./gradlew cleanBuildCache
./gradlew clean
./gradlew build -x test

# Ou simplement
./gradlew assembleDebug
```

### 2️⃣ Exécuter les Tests

```bash
# Tests unitaires (pas d'appareil nécessaire)
./gradlew test

# Tests instrumentalisés (appareil/émulateur requis)
./gradlew connectedAndroidTest

# Suite complète
./gradlew test connectedAndroidTest
```

### 3️⃣ Installer sur Appareil/Émulateur

```bash
# Installation
./gradlew installDebug

# Ou via Android Studio
# Run → Select Device → OK
```

### 4️⃣ Lancer l'Application

```bash
./gradlew installDebugAndRun

# Ou depuis Android Studio:
# Ctrl+F5 ou Run button
```

---

## 📱 Application en Action

### Flux Utilisateur

```
1. Splash Screen (2 secondes)
        ↓
2. Main Menu
   - Affiche stats (15 questions, 3 niveaux)
   - Bouton "START QUIZ"
        ↓
3. Category Selection
   - Grille 2 colonnes
   - Sélectionner "Roman Heritage"
        ↓
4. Difficulty Selection
   - 3 cartes (Easy/Medium/Hard)
   - Toggle Timer optional
   - Bouton "Start Quiz"
        ↓
5. Quiz Game
   - Question avec image locale
   - 4 boutons réponse
   - Timer compte à rebours
   - Score affiché en haut
        ↓
6. Feedback Overlay (1.5s)
   - ✅ Correct / ❌ Wrong
   - Fait historique
        ↓
7. Result Screen
   - Score final (X/Y)
   - Pourcentage
   - Message performance
   - Play Again / Back to Menu
```

---

## 🎬 Exemples de Gameplay

### Score Calculation
```
Question 1: ✅ Correct → +10 points (Total: 10)
Question 2: ❌ Wrong   → +0 points (Total: 10)
Question 3: ✅ Correct → +10 points (Total: 20)
...
Final Score: 20 / 30 = 67%
Message: "Good effort!"
```

### Timer Behavior
```
Timer Enabled:
- 15 secondes par question
- Barre rouge au-dessous de 5s
- Auto-submit après 0s

Timer Disabled:
- Pas de limite de temps
- Répondez à votre rythme
```

---

## 📁 Structure Fichiers Important

```
LAB/
├── app/
│   ├── build.gradle.kts          ← Config Gradle
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/example/tunisiaheritage/
│   │   │   │   ├── data/         ← Questions + Repo
│   │   │   │   ├── ui/
│   │   │   │   │   ├── screens/  ← 6 Screens
│   │   │   │   │   ├── theme/    ← Design System
│   │   │   │   │   ├── components/ ← Buttons, Overlays
│   │   │   │   │   └── navigation/ ← Routes + NavGraph
│   │   │   │   ├── util/         ← Helpers
│   │   │   │   ├── MainActivity.kt
│   │   │   │   └── HeritageApplication.kt
│   │   │   └── res/
│   │   │       ├── drawable/     ← 12 images❕
│   │   │       └── values/
│   │   ├── test/                 ← Unit tests
│   │   └── androidTest/          ← UI tests
│   └── proguard-rules.pro
├── gradle/
│   ├── libs.versions.toml        ← Versions centralisées
│   └── wrapper/
├── README.md                      ← Doc principale
├── LOCAL_DATA_GUIDE.md           ← Images & données
├── IMPLEMENTATION_SUMMARY.md     ← Résumé complet
├── CHECKLIST.md                  ← Checklist
└── GETTING_STARTED.md            ← Ce fichier
```

---

## 🔍 Troubleshooting

### ❌ Erreur : "java not found"

**Solution:**
```bash
# Windows: Définir JAVA_HOME
set JAVA_HOME=C:\Program Files\Java\jdk-11.0.x

# Ou installer JDK via
# https://www.oracle.com/java/technologies/downloads/
```

### ❌ Erreur : "Module not found"

**Solution:**
```bash
# Nettoyer cache Gradle
./gradlew clean
./gradlew --refresh-dependencies
./gradlew build
```

### ❌ Images not showing

**Solution:**
```bash
# Vérifier les images sont présentes
ls app/src/main/res/drawable/eljam*

# Ou depuis Windows:
dir app\src\main\res\drawable\eljam*
```

### ❌ Tests ne compilent pas

**Solution:**
```bash
# Full rebuild
./gradlew cleanBuildCache clean build

# Ou depuis Android Studio:
# File → Invalidate Caches → Restart
```

### ❌ Appareil/Émulateur non détecté

**Solution:**
```bash
# Lister appareils
adb devices

# Relancer daemon
adb kill-server
adb start-server
```

---

## ✨ Features à Tester

### 1. Navigation
- [ ] Splash → Menu automatique
- [ ] Menu → Category
- [ ] Category → Difficulty
- [ ] Difficulty → Quiz
- [ ] Quiz → Result → Menu
- [ ] Result → Play Again retourne à Difficulty

### 2. Quiz Logic
- [ ] 5 questions Easy
- [ ] 5 questions Medium
- [ ] 2 questions Hard
- [ ] Score augmente sur bonne réponse
- [ ] Feedback affichée 1.5s
- [ ] Options mélangées chaque fois

### 3. Timer
- [ ] Timer compte à rebours
- [ ] Timer passe au rouge < 5s
- [ ] Auto-submit à 0s
- [ ] Toggle timer fonctionne

### 4. Adaptive UI
- [ ] Layout sur téléphone
- [ ] Layout sur tablette
- [ ] Portrait/Landscape

### 5. Résultats
- [ ] Score correct (X/Y)
- [ ] Pourcentage correct
- [ ] Message performance approprié
- [ ] Emojis de célébration

---

## 📊 Debug Tips

### Voir les Logs

```bash
# Lire tous les logs
adb logcat | grep Tunisia

# Ou depuis Android Studio:
# View → Tool Windows → Logcat
# Filter: "Tunisia" ou "MainActivity"
```

### Voir l'État

Dans `MainActivity.kt`, vérifiez :
```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    Log.d(TAG, "onCreate called")  // Affiché dans Logcat
    ...
}
```

### Profiler l'App

```bash
# Via Android Studio:
# Profiler → Session → CPU/Memory/Network
```

---

## 🎯 Targets de Build

```gradle
./gradlew :app:assembleDebug       # Build APK debug
./gradlew :app:assembleRelease     # Build APK release
./gradlew :app:installDebug        # Install debug
./gradlew :app:uninstallDebug      # Uninstall debug
./gradlew :app:test                # Run unit tests
./gradlew :app:connectedAndroidTest # Run UI tests
```

---

## 💡 Pro Tips

### 1. Emulator Rapide
```bash
# Lancer émulateur avec GPU
emulator -avd Pixel_6_API_31 -gpu on

# Ou depuis Android Studio:
# AVD Manager → Play
```

### 2. Skipped Tests?
```kotlin
// Utiliser @Ignore si test bugue
@Ignore("WIP")
@Test
fun testSomething() { ... }
```

### 3. Architecture Test Layer
```
UI Tests (Compose)
    ↓
ViewModel Tests (Logic)
    ↓
Repository Tests (Data)
    ↓
Model Tests (Entities)
```

### 4. Debugging UI
```kotlin
// LazyColumn indexez
LazyColumn(
    modifier = Modifier
        .semantics { testTag = "questionList" }
) { ... }

// Puis test
.onNodeWithTag("questionList")
```

---

## 🚀 Production Checklist

Avant de soumettre:

- [ ] `./gradlew build` passe sans erreur
- [ ] `./gradlew test` tous les tests passent
- [ ] `./gradlew connectedAndroidTest` passe
- [ ] Pas de warnings Gradle
- [ ] Proguard rules correctes
- [ ] Signatures debug/release configurées
- [ ] Version code/name à jour
- [ ] Compilé en release mode
- [ ] Testé sur device réel
- [ ] Testé sur émulateur

---

## 📞 Support

Pour les questions:
1. Consultez les fichiers README/GUIDE
2. Vérifiez les logs via `adb logcat`
3. Regardez les commentaires KDoc dans le code
4. Vérifiez les tests pour les exemples d'utilisation

---

## 🎓 Apprentissage

Concepts clés pour cette app:

1. **MVVM** - ViewModel + StateFlow
2. **Compose** - Declarative UI
3. **Navigation** - RouteGraph
4. **Coroutines** - Async programming
5. **DI** - Hilt injection
6. **Testing** - Unit + Instrumentation
7. **State Management** - StateFlow patterns
8. **Material Design** - Color, Typography

Chaque concept est implémenté et testé dans cette app.

---

**Bon développement! 🚀**

Pour commencer:
```bash
cd C:\Users\ASUS\Downloads\LAB
./gradlew clean build
./gradlew installDebug
```

Puis ouvrez l'app sur votre appareil!

