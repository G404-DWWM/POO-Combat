# TP Combat POO

## Résumé des compétences visées

- Vous allez utiliser les connaissances acquises sur PDO pour gérer une base de données de héros de jeu.
- Vous allez créer des classes PHP qui ont des rôles bien déterminés et séparés.
- Vous allez en apprendre un peu plus sur l’intérêt des classes.
- Vous allez découvrir le principe de l’auto loading.
- Vous allez découvrir le principe de l’hydratation et l’imbrication de la PDO avec les classes.
- Vous allez utiliser les principes fondamentaux de la POO : l’encapsulation, l’héritage, le polymorphisme.

## Instructions générales

Nous allons découper ce TP en 2 sprints, Une correction sera faite entre les 2.

Pour réaliser ce TP, vous <ins>pouvez</ins> travailler par équipe de deux.

Vous devrez soigner l'apparence de l'application ainsi que l'arborescence du projet.

Vous devrez élaborer une base de données en lien avec les différentes fonctionnalités.

**Au fur et à mesure de la progression, de nouvelles itérations du projet s'ajouteront, vous devez préparer votre code à la maintenabilité et la scalabilité (mise à l'échelle) en découpant vos fonctionnalités le plus possible dans vos objets.**

Un pas à pas est disponible pour vous aider à démarrer le projet. Ce qui vous donnera ensuite les bases pour le deuxième sprint.


## 🏆 Objectifs sprint 1

L'objectif de ce TP est de créer un jeu de combat en tour par tour entre des héros que l'on crée et des monstres automatiquement générés. Voici le déroulé attendu :

1. **Création d'un Héros :** 🦸
   - Chaque visiteur peut créer un héros en saisissant uniquement un nom, ce qui a pour effet de créer un héros en base de données.

   <img src="./assets/AccueilCreateHero.png" alt="accueil-create-hero"/>

2. **Choix du Héros :** 🤔
   - L'utilisateur peut également choisir l'un des héros déjà créés avec lequel se battre, à condition que le héros soit toujours en vie (HP > 0).

   <img src="./assets/HeroChoice.png" alt="hero-choice"/>

3. **Lancement d'un Combat :** ⚔️ 
   - L'utilisateur peut lancer un nouveau combat en cliquant sur le bouton "Choisir un Héros".

4. **Automatisation du Combat :** 🤖
   - Après que le combat a été lancé, un monstre est automatiquement créé. Le déroulé du combat est enregistré dans un tableau PHP utilisé pour afficher les échanges de dégâts entre le monstre et le héros.

   <img src="./assets/Fight.png" alt="hero-choice"/>

5. **Résultat du Combat :** 🏆
   - L'utilisateur voit son héros survivre ou mourir au combat.
   - Lorsqu'un joueur ou le monstre n'a plus de points de vie, le combat est terminé.

6. **Rejouer :** 🔁
   - Donner la possibilité de rejouer. Si le héros survit, il devient sélectionnable à nouveau dans l'accueil.

## Structure du projet

Le projet doit être organisé avec les fichiers suivants en PHP :

1. **config/autoload.php :** 🔄
   - Permet le chargement automatique des classes et configure PHP pour avoir de meilleurs messages d’erreurs.

2. **config/db.php :** 🗃️
   - Gère la connexion à la base de données avec une instance de PDO, similaire à la correction du QCM.

3. **classes/Hero.php :** 🦸
   - Définit la classe `Hero` avec :
     - 2 propriétés :
       - Son nom (unique).
       - Ses points de vie.
     - 1 méthode :
       - `frapper un monstre`.

4. **classes/Monster.php :** 🦹
   - Définit la classe `Monster` avec :
     - 2 propriétés :
       - Son nom.
       - Ses points de vie.

5. **classes/HeroesManager.php :** 🧙‍♂️
   - Définit la classe `HeroesManager` qui contient tout le CRUD d’un héros :
     - Enregistrer un nouveau héros en base de données.
     - Modifier un héros.
     - Sélectionner un héros.
     - Récupérer une liste de plusieurs héros vivants.
     - Savoir si un héros existe.

6. **classes/FightsManager.php :** 🥋
   - Définit la classe `FightsManager` qui stocke les données du combat et comporte ces fonctionnalités :
     - Créer automatiquement un monstre.
     - Déclencher un combat et obtenir les résultats du combat.

7. **index.php :** 🎮
   - Affiche l'interface du mini-jeu de combat.
     - Le joueur peut créer un héros.
     - Le joueur peut sélectionner un héros existant.

8. **fight.php :** ⚔️
   - Utilise les classes instanciées et les méthodes souhaitées sur les objets.
     - Une instance de `HeroesManager` doit être créée.


## Pas à pas Sprint 1

### Préparation de la base de données

- Dans un premier temps, préparez votre base de données. Créez une table `heroes` avec les colonnes suivantes :
  - `id` (en auto-increment),
  - `name` (non nullable),
  - `health_point` (de type int avec une valeur par défaut à 100).

### Préparation des classes et de la connexion à la base de données

1. **config/autoload.php :** 🔄
   - Copiez le code ci-dessous dans le fichier `config/autoload.php` pour avoir de meilleurs messages d’erreurs et charger automatiquement toutes vos classes :
     ```php
     <?php
     // Strict
     declare(strict_types=1);

     // Enable all PHP errors
     ini_set('display_errors', 1);
     ini_set('display_startup_errors', 1);
     error_reporting(E_ALL);

     // Pretty errors
     ini_set("html_errors", "1");
     ini_set("error_prepend_string", "<pre style='color: #333; font-face:monospace; white-space: pre-wrap;font-size: 17px;color:#880808'>");
     ini_set("error_append_string ", "</pre>");

     // Autoload logic
     function chargerClasse($classname)
     {
         require __DIR__ . '/../class/' . $classname . '.php';
     }
     spl_autoload_register('chargerClasse');

     // Session
     session_start();
     ```

### Hero.php et HeroesManager.php

#### Classe Hero

- Créez la classe `Hero` avec ses propriétés, son constructeur, ses getters et setters. Préparez également la méthode `hit()` en la laissant vide.

#### Classe HeroesManager

- Créez la classe `HeroesManager` qui a, pour le moment, une propriété privée `$db`. Dans le constructeur, instanciez PDO dans la propriété privée `$db`.

### index.php

- Maintenant que les bases sont prêtes, commencez le traitement. Créez un formulaire dans `index.php` sans action pour que le traitement de création de héros reste sur cette page. Utilisez un seul input : "name".
- Dans une condition, si des données `POST['name']` existent, créez une instance de `HeroesManager($db)` en lui fournissant la connexion à la base de données. Ensuite, appelez la méthode `add()` du manager en lui donnant comme argument une nouvelle instance de la classe `Hero`.

### HeroesManager.php

- Dans la classe `HeroesManager`, créez la première fonction du CRUD en la nommant simplement `add()`. Cette méthode prendra comme argument un objet `Hero`.
- La méthode `add` doit d'abord préparer et exécuter une requête INSERT avec le `name` récupéré dans l'objet `Hero`. `$hero->getName();`
- À ce moment-là du code, un nouveau Hero existe en base de données. Utilisez une méthode native de PDO pour récupérer le dernier id enregistré dans la base de données : `$id = $this->bd->lastInsertId();`, `$hero->setId($id);`
- Indiquez également à l'objet `Hero` ses points de vie.
- Maintenant que vous pouvez insérer des héros en BDD, il faut les afficher. Créez la méthode `findAllAlive()` qui contient une requête PDO SELECT et qui récupère tous les héros qui ont encore leurs points de vie (`pv > 0`). Cette méthode doit ensuite stocker dans un tableau des nouvelles instances de la classe `Hero` pour chaque ligne récupérée dans la base de données. À la fin, la méthode `return` le tableau.

