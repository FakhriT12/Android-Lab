# 🔄 Convertir Excel à CSV - Guide Pratique

## Fichier Source

```
C:\Users\ASUS\Downloads\LAB\data collection\el_jem_quiz_dataset.xlsx
```

## Étapes de Conversion

### Méthode 1: Excel (Recommandé)

#### Step 1: Ouvrir le fichier
```
1. Localiser: data collection/el_jem_quiz_dataset.xlsx
2. Double-clic pour ouvrir dans Excel
3. Vérifier que les données sont là
```

#### Step 2: Prépa les données
```
Vérifier la structure:
- Colonne A: id (nombres)
- Colonne B: imageResId (eljam1, eljam2, ...)
- Colonne C: correctAnswer (texte)
- Colonne D: wrongAnswer1 (texte)
- Colonne E: wrongAnswer2 (texte)
- Colonne F: wrongAnswer3 (texte)
- Colonne G: historicalFact (texte)
- Colonne H: difficulty (EASY, MEDIUM, HARD)
```

#### Step 3: Sauvegarder en CSV
```
Excel:
1. Fichier → Enregistrer sous...
2. Format: CSV (séparé par virgules) (*.csv)
3. Nom: questions.csv
4. Emplacement: C:\Users\ASUS\Downloads\LAB\app\src\main\assets\
5. OK
```

#### Step 4: Encodage
```
Vérifier l'encodage UTF-8:
1. Si demandé, choisir: UTF-8
2. OK
```

### Méthode 2: LibreOffice Calc

#### Step 1-2: Idem Excel

#### Step 3: Sauvegarder
```
Fichier → Enregistrer Sous...
Format: CSV (Texte) (*.csv)
Nom: questions.csv
Encodage: UTF-8
OK
```

### Méthode 3: Google Sheets

#### Step 1: Importer
```
1. Aller à https://sheets.google.com
2. Nouveau → Importer → Télécharger
3. Sélectionner: el_jem_quiz_dataset.xlsx
4. Importer
```

#### Step 2: Télécharger
```
Fichier → Télécharger → CSV
```

#### Step 3: Placer
```
Copier le CSV:
C:\Users\ASUS\Downloads\LAB\app\src\main\assets\questions.csv
```

### Méthode 4: Python (Script)

```python
import pandas as pd
import os

# Lire le fichier Excel
excel_path = r"C:\Users\ASUS\Downloads\LAB\data collection\el_jem_quiz_dataset.xlsx"
df = pd.read_excel(excel_path)

# Sauvegarder en CSV
csv_path = r"C:\Users\ASUS\Downloads\LAB\app\src\main\assets\questions.csv"
os.makedirs(os.path.dirname(csv_path), exist_ok=True)
df.to_csv(csv_path, index=False, encoding='utf-8')

print(f"✅ Conversion réussie: {csv_path}")
print(f"Total de lignes: {len(df)}")
```

**Pour exécuter:**
```bash
pip install pandas openpyxl
python convert_excel.py
```

### Méthode 5: PowerShell Script

```powershell
# convert-excel-to-csv.ps1

$excelPath = "C:\Users\ASUS\Downloads\LAB\data collection\el_jem_quiz_dataset.xlsx"
$csvPath = "C:\Users\ASUS\Downloads\LAB\app\src\main\assets\questions.csv"

# Installer module si nécessaire
if (!(Get-Module -Name ImportExcel -ErrorAction SilentlyContinue)) {
    Install-Module -Name ImportExcel -Force
}

# Convertir
$data = Import-Excel -Path $excelPath
$data | Export-Csv -Path $csvPath -NoTypeInformation -Encoding UTF8 -Force

Write-Host "✅ Conversion réussie: $csvPath"
Write-Host "Total de lignes: $($data.Count)"
```

**Pour exécuter:**
```powershell
.\convert-excel-to-csv.ps1
```

---

## Structure Attendue du CSV

Après conversion, le fichier CSV doit ressembler à:

```
id,imageResId,correctAnswer,wrongAnswer1,wrongAnswer2,wrongAnswer3,historicalFact,difficulty
1,eljam1,El Djem Amphitheater,Dougga,Carthage,Sfax,Historical fact about El Djem,EASY
2,eljam2,Dougga Site,Tunis,Kairouan,Tozeur,Historical fact about Dougga,EASY
3,eljam3,Roman Theatre,Temple,Mosque,Palace,Historical fact about theatre,MEDIUM
...
```

### Points Importants:

1. **Toujours 8 colonnes** (pas plus, pas moins)
2. **En-têtes identiques** aux specs
3. **Pas d'espaces** avant/après les valeurs
4. **UTF-8 encoding** (pas ANSI)
5. **Virgule (,) comme séparateur** (pas point-virgule)

---

## Validation du CSV

### Vérifier le fichier

#### Option 1: Ouvrir dans Notepad
```
1. C:\Users\ASUS\Downloads\LAB\app\src\main\assets\questions.csv
2. Clique-droit → Ouvrir avec → Notepad
3. Vérifier la structure
```

#### Option 2: Vérifier dans Excel
```
1. Ouvrir: questions.csv
2. Vérifier colonnes et données
3. Aucune erreur d'encodage?
```

#### Option 3: Vérifier avec PowerShell
```powershell
$csv = "C:\Users\ASUS\Downloads\LAB\app\src\main\assets\questions.csv"
$data = Import-Csv -Path $csv
Write-Host "Total de lignes: $($data.Count)"
Write-Host "Colonnes: $($data[0].PSObject.Properties.Name)"
$data | Format-Table -AutoSize | Select-Object -First 5 | Out-Host
```

---

## Importer dans l'App

### Step 1: Fichier en place
```
✅ app/src/main/assets/questions.csv
```

### Step 2: Build
```bash
cd C:\Users\ASUS\Downloads\LAB
./gradlew clean build
```

### Step 3: Test
```bash
./gradlew test
./gradlew installDebug
```

---

## Vérifier que l'App Charge le CSV

### Via Logs
```bash
# Connecter appareil/émulateur
adb logcat | grep -i "question"
```

Vous devriez voir:
```
QuestionRepository: Loading questions from CSV...
QuestionRepository: Loaded 12 questions successfully!
```

### Via Code
```kotlin
val repo = QuestionRepository(context)
val questions = repo.getAllQuestions()
Log.d("Quiz", "Total questions: ${questions.size}")
```

---

## Problèmes Courants & Solutions

### ❌ "Column count mismatch"

**Cause:** Le CSV n'a pas exactement 8 colonnes

**Solution:**
```
1. Ouvrir dans Excel
2. Vérifier: A=id, B=imageResId, C=correctAnswer, D=wrongAnswer1, E=wrongAnswer2, F=wrongAnswer3, G=historicalFact, H=difficulty
3. Supprimer colonnes inutiles
4. Sauvegarder en CSV
```

### ❌ "Invalid character encoding"

**Cause:** Encoding n'est pas UTF-8

**Solution:**
```
1. Ouvrir CSV dans Notepad++
2. Encoding → UTF-8 (sans BOM)
3. File → Save
```

### ❌ "Image resource not found"

**Cause:** imageResId ne correspond pas à un fichier drawable

**Solution:**
```
Vérifier que:
- eljam1, eljam2, ... existent dans: app/src/main/res/drawable/
- Noms correspondent exactement (case sensitive)
```

### ❌ "Invalid difficulty"

**Cause:** Difficulté n'est pas EASY, MEDIUM, ou HARD

**Solution:**
```
Utiliser exactement:
- EASY (pas Easy, easy, ou autre)
- MEDIUM (pas Medium, medium)
- HARD (pas Hard, hard)
```

### ❌ "App still shows hardcoded questions"

**Cause:** CSV n'a pas pu être chargé

**Solutions:**
1. Vérifier chemin: app/src/main/assets/questions.csv
2. Vérifier encoding: UTF-8
3. Vérifier structure: 8 colonnes + headers
4. Voir logs pour erreur exacte

---

## Format CSV Minimal (Test)

Vous pouvez commencer avec juste 3 questions:

```csv
id,imageResId,correctAnswer,wrongAnswer1,wrongAnswer2,wrongAnswer3,historicalFact,difficulty
1,eljam1,Answer1,Wrong1,Wrong2,Wrong3,Test fact 1,EASY
2,eljam2,Answer2,Wrong1,Wrong2,Wrong3,Test fact 2,MEDIUM
3,eljam3,Answer3,Wrong1,Wrong2,Wrong3,Test fact 3,HARD
```

Puis complétez avec vos vraies données.

---

## Vérification Finale

Avant de soumettre:

```
✅ questions.csv existe dans app/src/main/assets/
✅ 8 colonnes exactement
✅ En-têtes corrects
✅ Pas d'espaces inutiles
✅ UTF-8 encoding
✅ ./gradlew build réussit
✅ ./gradlew test réussit
✅ App charge les questions (logs)
```

---

## Quick Reference

**Source:** `data collection/el_jem_quiz_dataset.xlsx`
**Destination:** `app/src/main/assets/questions.csv`

**Format:**
```
id, imageResId, correctAnswer, wrongAnswer1, wrongAnswer2, wrongAnswer3, historicalFact, difficulty
```

**Difficultés:** EASY, MEDIUM, HARD (majuscules)
**Encodage:** UTF-8
**Séparateur:** Virgule (,)

---

**Créé:** 2026-05-06
**Version:** 1.0
**Dernière mise à jour:** 2026-05-06

