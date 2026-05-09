# 📊 CSV/Excel Integration - Update Summary

## What's New

L'application a été mise à jour pour supporter le chargement dynamique des questions depuis des fichiers CSV ou Excel, en plus de la structure hardcodée existante.

### Version: 1.1.0 (CSV/Excel Support)

---

## 🎯 Nouvelles Fonctionnalités

### 1. **QuestionLoader** 
Nouvelle classe pour charger les questions depuis des fichiers externes :

```
data/loader/QuestionLoader.kt
```

**Fonctionnalités:**
- ✅ Lecture CSV depuis assets
- ✅ Conversion automática vers objets Question
- ✅ Validation des données
- ✅ Fallback automatique

### 2. **CSV Support**
Format CSV structuré pour les questions :

```
app/src/main/assets/questions.csv
```

**Structure:**
```
id,imageResId,correctAnswer,wrongAnswer1,wrongAnswer2,wrongAnswer3,historicalFact,difficulty
1,eljam1,Réponse correcte,Mauvaise 1,Mauvaise 2,Mauvaise 3,Fait historique,EASY
```

### 3. **Excel File**
Fichier Excel source du dataset :

```
app/src/main/assets/el_jem_quiz_dataset.xlsx
```

### 4. **Hilt DI Module**
Configuration Dependency Injection :

```
di/DataModule.kt
```

---

## 📁 Fichiers Modifiés

### Core Changes

| Fichier | Changement | Type |
|---------|-----------|------|
| `QuestionRepository.kt` | Ajout support CSV | ✏️ Modifié |
| `build.gradle.kts` | Ajout dépendance CSV parser | ✏️ Modifié |
| `libs.versions.toml` | Ajout version CSV parser | ✏️ Modifié |

### Nouveaux Fichiers

| Fichier | Description | Type |
|---------|-------------|------|
| `data/loader/QuestionLoader.kt` | Classe CSV loader | 🆕 Nouveau |
| `di/DataModule.kt` | Hilt configuration | 🆕 Nouveau |
| `app/src/main/assets/questions.csv` | Exemple CSV questions | 🆕 Nouveau |
| `data/loader/QuestionLoaderTest.kt` | Tests loader | 🆕 Nouveau |

### Tests Mis à Jour

| Fichier | Changement | Type |
|---------|-----------|------|
| `QuestionRepositoryTest.kt` | Mockage du Context | ✏️ Modifié |

### Documentation

| Fichier | Description | Type |
|---------|-------------|------|
| `CSV_EXCEL_GUIDE.md` | Guide complet CSV/Excel | 🆕 Nouveau |
| `CSV_EXCEL_INTEGRATION.md` | Ce fichier | 🆕 Nouveau |

---

## 🔄 Processus de Chargement

### Ancien Processus (v1.0)
```
App Start
    ↓
QuestionRepository.init()
    ↓
Questions hardcodées chargées
```

### Nouveau Processus (v1.1)
```
App Start
    ↓
QuestionRepository.init()
    ↓
QuestionLoader.loadQuestionsFromCsv("questions.csv")
    ↓
CSV trouvé?
    ├─ OUI → Parsing CSV
    │         ↓
    │    Questions dynamiques
    │
    └─ NON → Fallback questions hardcodées
```

---

## 📦 Dépendances Ajoutées

### CSV Parser
```gradle
implementation("de.siegmar:fastcsv:1.9.1")
```

**Raison:** Haute performance et light-weight pour parser CSV en Android

---

## 🚀 Comment Utiliser

### Option 1: Utiliser les Questions Hardcodées (Default)

Aucune action requise - l'app fonctionne comme avant.

```kotlin
val repository = QuestionRepository(context)
val questions = repository.getAllQuestions()  // Returns 12 hardcoded questions
```

### Option 2: Charger depuis CSV

1. **Convertir votre Excel** (`el_jem_quiz_dataset.xlsx`) en CSV
2. **Nommer le fichier** `questions.csv`
3. **Copier dans assets** :
   ```bash
   cp questions.csv app/src/main/assets/
   ```
4. **Build et test** :
   ```bash
   ./gradlew clean build
   ```

L'app chargera automatiquement le CSV au premier appel !

---

## 📊 Exemple d'Intégration

### Fichier CSV complet (5 questions)

```csv
id,imageResId,correctAnswer,wrongAnswer1,wrongAnswer2,wrongAnswer3,historicalFact,difficulty
1,eljam1,El Djem Amphitheater,Dougga Theater,Carthage Ruins,Sfax Fortress,Amphitheater with 35000 capacity,EASY
2,eljam2,Dougga,Carthage,Kairouan,Sfax,Impressive Roman site on hilltop,EASY
3,eljam3,Roman Theatre,Temple,Mosque,Palace,Used for theatrical performances,MEDIUM
4,eljam4,Capitol Temple,Bath Complex,Market,Residential Block,Dedicated to Jupiter Juno and Minerva,MEDIUM
5,eljam5,Roman Mosaic,Marble Sculpture,Fresco Painting,Pottery Artifact,Colored stone or glass tiles arrangement,HARD
```

---

## ✅ Validation

### Tests Incluent:
- ✅ Chargement CSV valide
- ✅ Gestion d'erreurs (fichier manquant)
- ✅ Parsing données
- ✅ Fallback automatique
- ✅ Structure données

### Pour Vérifier:
```bash
./gradlew test
```

---

## 🔧 Configuration Avancée

### Charger depuis Fichier Personnalisé

```kotlin
// Charger depuis n'importe quel CSV dans assets
val loader = QuestionLoader(context)
val customQuestions = loader.loadQuestionsFromCsv("my_custom_questions.csv")
```

### Ajouter Support Multilingues

```csv
id,imageResId,correctAnswer_en,correctAnswer_fr,wrongAnswer1_en,wrongAnswer1_fr,...,difficulty
```

### Charger depuis URL (Futur)

```kotlin
fun loadQuestionsFromUrl(url: String): List<Question> {
    // À implémenter
}
```

---

## 📋 Checklist Intégration

### Pour Utiliser Votre Dataset Excel:

- [ ] Ouvrir `el_jem_quiz_dataset.xlsx`
- [ ] Vérifier structure (8 colonnes + headers)
- [ ] Convertir en CSV
- [ ] Nommer `questions.csv`
- [ ] Copier dans `app/src/main/assets/`
- [ ] Exécuter `./gradlew clean build`
- [ ] Tester l'application
- [ ] Vérifier que les questions chargent

---

## 🐛 Troubleshooting

### ❌ "CSV not loading"
- Vérifier le fichier est dans `app/src/main/assets/questions.csv`
- Vérifier l'encodage est UTF-8
- Voir les logs: `adb logcat | grep Questions`

### ❌ "Resource not found"
- Vérifier imageResId correspond à un fichier dans `drawable/`
- Exécuter: `./gradlew clean build`

### ❌ "Invalid difficulty"
- Utiliser EASY, MEDIUM, ou HARD (majuscules)

### ❌ "App shows hardcoded questions"
- CSV n'a pas pu être chargé
- Vérifier les logs pour l'erreur exacte
- Fallback automatique vers questions hardcodées

---

## 📈 Performance Impact

- ✅ **Chargement Lazy** - Effectué au premier accès seulement
- ✅ **Caching** - Questions stockées en mémoire après chargement
- ✅ **Optimisé** - Utilise fastcsv pour performance
- ✅ **Léger** - Aucun impact sur startup time

---

## 🎓 Architecture Update

### Avant (v1.0)
```
QuestionRepository
    └── Hardcoded Questions
```

### Après (v1.1)
```
QuestionRepository
    ├── QuestionLoader
    │   └── CSV from Assets
    └── Fallback: Hardcoded Questions
```

### Hilt DI
```
DataModule
    └── Provides QuestionRepository
        └── With: ApplicationContext
```

---

## 🔐 Sécurité

- ✅ Validation de toutes les données CSV
- ✅ Gestion d'erreurs gracieuse
- ✅ Pas d'accès au système de fichiers
- ✅ Assets statiques uniquement

---

## 📞 Support

Pour questions spécifiques sur l'intégration:

1. **Consultez:** `CSV_EXCEL_GUIDE.md`
2. **Tests:** Voir `QuestionLoaderTest.kt`
3. **Logs:** `adb logcat | grep QuestionLoader`

---

## 🎯 Prochaines Étapes

### Immédiat:
- [ ] Convertir Excel en CSV
- [ ] Copier CSV dans assets
- [ ] Rebuild et test

### Optionnel:
- [ ] Ajouter plus de questions depuis Excel
- [ ] Support autre format (JSON, XML)
- [ ] API backend future

---

**Mise à jour:** 2026-05-06  
**Version:** 1.1.0  
**Status:** ✅ Prêt à utiliser

