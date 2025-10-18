# 🔧 Résolution du Problème Matières et Notes

## 🚨 Problème Identifié

L'erreur `TypeError: Net.connect is not a function` indique que vous essayez d'utiliser `mysql2` côté client (navigateur) alors que c'est une bibliothèque côté serveur uniquement.

## ✅ Solution Implémentée

### 1. **Tables Manquantes**

Exécutez le script SQL pour créer toutes les tables nécessaires :

```bash
# Exécuter le script SQL
mysql -u root -p scolapp < src/db/migrations/add_missing_tables.sql
```

Ou copiez-collez le contenu de `src/db/migrations/add_missing_tables.sql` dans votre client MySQL.

### 2. **Architecture API Correcte**

J'ai créé une architecture client-serveur appropriée :

#### **Côté Serveur (API Routes)**
- `src/pages/api/subjects/` - Gestion des matières
- `src/pages/api/grading-settings/` - Paramètres de notation
- Utilise `mysql2` côté serveur uniquement

#### **Côté Client (Services API)**
- `src/services/subjectApiService.js` - Service client pour les matières
- `src/services/gradingSettingsApiService.js` - Service client pour les paramètres
- Utilise `fetch()` pour communiquer avec les API routes

### 3. **Variables d'Environnement**

Créez ou mettez à jour votre fichier `.env.local` :

```env
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=votre_mot_de_passe
DB_NAME=scolapp
DB_PORT=3306
```

## 📋 Tables Créées

Le script crée les tables suivantes :

| Table | Description |
|-------|-------------|
| `subjects` | Matières scolaires |
| `subject_coefficients` | Coefficients par classe |
| `evaluation_types` | Types d'évaluation |
| `evaluation_periods` | Périodes d'évaluation |
| `grades` | Notes des élèves |
| `period_averages` | Moyennes par période |
| `general_averages` | Moyennes générales |
| `grading_settings` | Paramètres configurables |
| `report_cards` | Bulletins scolaires |

## 🎯 Utilisation

### **Récupérer les matières**
```javascript
import subjectApiService from '@/services/subjectApiService';

// Récupérer toutes les matières
const subjects = await subjectApiService.getAllSubjects();

// Récupérer une matière par ID
const subject = await subjectApiService.getSubjectById('subject-math');
```

### **Gérer les paramètres de notation**
```javascript
import gradingSettingsApiService from '@/services/gradingSettingsApiService';

// Récupérer tous les paramètres
const settings = await gradingSettingsApiService.getAllSettings();

// Mettre à jour un paramètre
await gradingSettingsApiService.updateSetting('default_max_score', '25');

// Récupérer la note maximale par défaut
const maxScore = await gradingSettingsApiService.getDefaultMaxScore();
```

### **Valider une note**
```javascript
// Valider une note selon les paramètres configurés
await gradingSettingsApiService.validateGrade(15, 20);

// Convertir une note en lettre
const letter = await gradingSettingsApiService.convertScoreToLetter(16); // Retourne 'A'
```

## 🔧 API Endpoints Disponibles

### **Matières**
```
GET    /api/subjects              - Récupérer toutes les matières
POST   /api/subjects              - Créer une matière
GET    /api/subjects/[id]         - Récupérer une matière
PUT    /api/subjects/[id]         - Mettre à jour une matière
DELETE /api/subjects/[id]         - Supprimer une matière
```

### **Paramètres de Notation**
```
GET    /api/grading-settings              - Récupérer tous les paramètres
POST   /api/grading-settings              - Ajouter un paramètre
GET    /api/grading-settings/[key]        - Récupérer un paramètre
PUT    /api/grading-settings/[key]        - Mettre à jour un paramètre
```

## 📊 Données par Défaut

Le script insère automatiquement :

### **Matières**
- Mathématiques (MATH)
- Français (FR)
- Anglais (EN)
- Histoire (HIST)
- SVT
- Physique (PHYS)
- Économie (ECO)
- Philosophie (PHILO)
- Arts plastiques (ART)
- Éducation physique (SPORT)

### **Types d'Évaluation**
- Contrôle (weight: 1.00)
- Devoir (weight: 1.00)
- Composition (weight: 2.00)
- Oral (weight: 0.50)
- Travaux pratiques (weight: 0.75)
- Examen (weight: 3.00)

### **Périodes d'Évaluation**
- 1er Trimestre (sept-déc)
- 2ème Trimestre (jan-mars)
- 3ème Trimestre (avril-juin)

### **Paramètres de Notation**
- Note maximale : 20
- Note de passage : 10
- Moyennes pondérées : activées
- Classement : activé
- Précision décimale : 2
- Validation : stricte

## 🚀 Prochaines Étapes

1. **Exécuter le script SQL** pour créer les tables
2. **Configurer les variables d'environnement**
3. **Tester les API routes** avec Postman ou curl
4. **Intégrer les services** dans vos composants React
5. **Créer les interfaces utilisateur** pour la gestion des matières et notes

## 🔍 Vérification

Après avoir exécuté le script, vérifiez que les tables sont créées :

```sql
USE scolapp;
SHOW TABLES LIKE '%subject%';
SHOW TABLES LIKE '%grade%';
SHOW TABLES LIKE '%evaluation%';
```

Vous devriez voir toutes les tables listées dans la section "Tables Créées" ci-dessus.

## 📝 Notes Importantes

- **Ne jamais utiliser `mysql2` côté client** - utilisez toujours les API routes
- **Toujours gérer les erreurs** dans les services côté client
- **Utiliser les variables d'environnement** pour la configuration de la base de données
- **Tester les API routes** avant d'intégrer dans l'interface utilisateur

Cette architecture résout le problème d'origine et fournit une base solide pour la gestion des matières et notes dans ScolApp. 