# 🥃 Home Assistant Bar Manager (AI Powered)

Un gestionnaire de bar intelligent pour Home Assistant.

Ce projet transforme votre instance Home Assistant en un véritable **assistant barman piloté par IA**.  
Il permet d’identifier vos spiritueux, générer des fiches techniques complètes, gérer votre inventaire et proposer des cocktails intelligents.

> Projet inspiré du travail remarquable d’**Aldoushx** (Home Assistant Wine Cellar Manager).  
> Il ne s’agit pas d’un fork, mais d’une adaptation conceptuelle dédiée aux spiritueux et aux cocktails.

---

## ✨ Fonctionnalités

### 🧠 Identification par IA
À partir du nom et du type, le système interroge **Google Gemini**.

L’IA agit comme un expert en spiritueux en croisant des sources spécialisées :
- Difford’s Guide
- Liquor.com
- Whisky Advocate
- Fiches distilleries

---

### 📋 Fiches techniques complètes

**Identité**
- Nom exact
- Type (Rhum, Gin, Whisky…)
- Origine
- Distillerie
- Année

**Caractéristiques**
- ABV
- Volume
- Prix moyen estimé

**Analyse**
- Profil aromatique structuré
- Corps cocktail (léger / moyen / puissant)
- Suggestions adaptées

---

### 📦 Gestion intelligente du stock

- Ajout en un clic depuis la recherche
- Boutons +1 / -1
- Suppression simplifiée
- Gestion intelligente des doublons (#2, #3…)
- Distinction Maison / Industriel
- Valorisation estimée du bar
- Nombre total de bouteilles

---

# 🚀 Modules avancés

---

## 🏠 Mode Bouteille Maison

Création de spiritueux personnalisés :

- Calcul ABV estimé
- Calcul coût réel (base + ingrédients)
- Prix conseillé automatique
- Calcul marge
- Coût au litre

---

## 🍸 Accord du Barman (IA)

Proposition intelligente de cocktail selon :

- Bouteille sélectionnée (optionnel)
- Ingrédients disponibles
- Occasion / envie

Réponse structurée en JSON strict :

- Nom du cocktail
- Style
- Raison
- Ingrédients
- Recette
- Verre
- Garnish
- Niveau
- Variantes

---

## 📈 Analyse du Stock & Cohérence

Comparaison automatique entre :

- Inventaire théorique
- Plan de rangement
- Bouteilles non placées
- Suroccupation
- Inconnues dans le plan

---

## 🗺️ Visualisation & Rangement du Bar (Frontend)

Interface HTML dynamique :

- Visualisation graphique des bouteilles
- Placement manuel (E1-1, E1-2…)
- Mode automatique (Auto-fill)
- Vidage complet
- Synchronisation inventaire / plan
- Génération HTML automatique

---

# 🛠 Prérequis

1. **Clé API Google Gemini**
   - À créer sur Google AI Studio

2. **Intégration Google Generative AI**
   - Intégration native Home Assistant
   - Nom recommandé de l’action :
     ```
     google_ai_task
     ```

3. **system_log activé**

4. **python_scripts activé** dans `configuration.yaml`

---

# 🚀 Installation

### 1. Organisation des fichiers

Créez les dossiers suivants dans votre répertoire `/config/` (c'est la racine de HA dans File Editor, la ou se trouve le configuration.yaml) :
* `/config/python_scripts/` (moteurs de calcul)
* `/config/packages/` (configuration YAML)
* `/config/www/cave/` (stockage du rendu HTML)


### 2. Configuration du `configuration.yaml`

* Ajoutez les lignes suivantes pour activer le système et protéger votre base de données contre le gonflement inutile (l'IA est gourmande en log...) :

```yaml
homeassistant:
  packages: !include_dir_named packages

# Configuration du Recorder pour protéger votre stockage
recorder:
  purge_keep_days: 7
  exclude:
    entities:
      - sensor.vin_recherche # Donnée temporaire volumineuse
      - input_text.derniere_erreur_gemini
    event_types:
      - system_log_event # Évite d'historiser les erreurs système répétitives

```

Redémarrer Home Assistant après modification.

### 3. Emplacement des fichiers
| Fichier | Emplacement | Rôle |
| :--- | :--- | :--- |
| `bar_plan.json` | `/config/` | Base de données des emplacements |
| `generate_bar_plan.py` | `/config/` | Générateur du rendu HTML |
| `bar_cellier_ia_ultra.yaml` | `/config/packages/` | Configuration HA (Sensors, Scripts) |
| `analyze_bar_stock.py` | `/config/python_scripts/` | Calcul des écarts de stock |
| `bar_ranger.py` | `/config/python_scripts/` | Script de rangement manuel |
| `bar_autofill.py` | `/config/python_scripts/` | Script de rangement automatique |
| `bar_empty.py` | `/config/python_scripts/` | Script de vidage complet |
| `bar_plan.html` (généré auto) | `/config/www/` | Fichier de rendu final |

---

### 🔐 Configuration du Token API
Vous devez insérer un **TOKEN HA longue durée** dans la variable `TOKEN = "..."` située au début des fichiers suivants :
* `generate_cave.py`
  
Fichiers concernés :
* `generate_bar_plan.py`
* `analyze_bar_stock.py`
* `bar_autofill.py`
* `bar_empty.py`
* 
Pour créer un jeton longue durée, cliquez sur votre nom en bas du menu à gauche dans HA => onglet Securité => (en bas) Jetons d'accès longue durée / créer un jeton.

---

## 📖 Utilisation

1. **Saisie** : Tapez le nom, la couleur et l'année dans l'interface du dashboard. Cliquez sur **Rechercher le vin**.
2. **Analyse** : Pendant que l'IA travaille, vous pouvez observer que la demande est bien prise en compte avec un indicateur d'opération. La requête prend environ 10s.
3. **Stockage** : Quand la recherche est terminée, le vin trouvé est décrit sommairement. Si sa description correspond à l'attendu, cliquez sur **Ajouter le vin à la cave** pour basculer le vin de la recherche vers votre inventaire permanent.
4. **Gestion** : Déployez "Afficher la gestion de la cave" pour ajuster vos stocks, à l'aide de la liste déroulante et des boutons associés.
5. **Conseils du sommelier** : Entrez votre repas dans le champs texte et lancez la recherche IA. La réponse arrivera sous 15s. Idem pour les conseils d'achats et les opportunités, les champs sont suffisamment explicites !

---

🙏 Remerciements

Un immense merci à Aldoushx pour son projet
Home Assistant Wine Cellar Manager (AI Powered).

Son architecture, sa structuration des packages et son approche intelligente du supersensor ont été une source d’inspiration directe pour cette adaptation dédiée aux spiritueux.
