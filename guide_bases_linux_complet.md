# GUIDE ET EXERCICES PRATIQUES : ADMINISTRATEUR SYSTEME LINUX

Ce document rassemble tous les concepts clés, les commandes fondamentales, les explications des arguments (options) ainsi que les exercices pratiques commentés pour valider vos compétences d'administrateur Linux.

---

## NOTE SUR LA TERMINOLOGIE : LES FLAGS / OPTIONS (Les "-...")
Les éléments qui commencent par un tiret (ex: `-la`, `-p`, `-9`) s'appellent des **options**, des **arguments** ou des **flags** (drapeaux). 
* Ils servent à modifier le comportement d'une commande de base.
* Un seul tiret (`-`) introduit généralement des options courtes (une seule lettre, ex: `-f`). On peut souvent les combiner : `-la` équivaut à `-l -a`.
* Deux tirets (`--`) introduisent des options longues (un mot complet, ex: `--purge`, `--help`).

---

## SECTION 1 : NAVIGATION ET MANIPULATION DE FICHIERS/DOSSIERS

### Commande de base et arguments
* `cd` (Change Directory) : Permet de se déplacer dans les dossiers.
* `mkdir` (Make Directory) : Crée des dossiers.
  * `-p` (--parents) : Crée les dossiers parents s'ils n'existent pas (évite une erreur si la structure complète doit être générée d'un coup).
* `touch` : Crée un fichier vide ou met à jour sa date de modification.
* `cp` (Copy) : Copie des fichiers ou dossiers.
  * `-r` ou `-R` (--recursive) : Copie récursivement tout le contenu d'un dossier (fichiers et sous-dossiers).
* `ls` (List) : Liste le contenu d'un dossier.
  * `-l` : Utilise le format long (affiche les permissions, la taille, le propriétaire, la date).
  * `-a` (--all) : Affiche tous les fichiers, y compris les fichiers cachés (ceux qui commencent par un point `.`).

### Exercice Pratique Commenté
```bash
# 1. Se déplacer vers le dossier personnel de l'utilisateur actuel (symbole tilde ~)
cd ~

# 2. Créer une arborescence complète. L'option -p crée 'projet' puis 'src' à l'intérieur en une seule commande.
mkdir -p projet/src

# 3. Entrer dans le dossier 'src' nouvellement créé
cd projet/src

# 4. Créer un fichier vide nommé 'app.js'
touch app.js

# 5. Revenir au dossier parent (.. signifie le dossier juste au-dessus, ici 'projet')
cd ..

# 6. Copier le fichier 'app.js' situé dans 'src/' vers le dossier actuel (noté '.') sous un nouveau nom
cp src/app.js ./app_backup.js

# 7. Lister le contenu en format long (-l) en incluant les fichiers cachés (-a)
ls -la
```

---

## SECTION 2 : COMPRENDRE ET MODIFIER LES PERMISSIONS (chmod/chown)

### Commande de base et arguments
* `chmod` (Change Mode) : Modifie les permissions de lecture (r=4), écriture (w=2), et exécution (x=1).
  * `u+x` : `u` (User/Propriétaire), `+` (Ajouter), `x` (Exécution). Donne le droit d'exécuter le fichier au propriétaire.
  * `700` : Notation numérique. 7 (4+2+1 = rwx pour le propriétaire), 0 (aucun droit pour le groupe), 0 (aucun droit pour les autres).
* `chown` (Change Owner) : Modifie le propriétaire et/ou le groupe d'un fichier.
  * Syntaxe `utilisateur:groupe` : Permet de changer les deux éléments en même temps.

### Exercice Pratique Commenté
```bash
# Avant de commencer, créons un fichier de test
touch script.sh

# 1. Ajouter le droit d'exécution (+x) uniquement pour l'utilisateur propriétaire (u)
chmod u+x script.sh

# Équivalent strict en notation numérique : Propriétaire a Tout (7), Groupe a Rien (0), Autres ont Rien (0)
chmod 700 script.sh

# 2. Changer le propriétaire pour 'alice' et le groupe pour 'dev'
# (Nécessite 'sudo' car seul l'administrateur peut donner la propriété d'un fichier à quelqu'un d'autre)
sudo chown alice:dev script.sh
```

---

## SECTION 3 : LISTER, SURVEILLER ET TUER UN PROCESSUS

### Commande de base et arguments
* `ps` (Process Status) : Liste les processus en cours d'exécution.
  * `aux` : Option combinée (sans tiret dans sa version BSD classique) :
    * `a` : Affiche les processus de tous les utilisateurs.
    * `u` : Affiche le nom de l'utilisateur propriétaire et les détails de consommation (CPU, Mémoire).
    * `x` : Affiche les processus qui ne sont pas attachés à un terminal (services d'arrière-plan).
* `top` : Affiche en temps réel la liste dynamique des processus les plus gourmands.
* `kill` : Envoie un signal à un processus via son identifiant unique (PID).
  * `-9` : Envoie le signal SIGKILL. C'est un ordre d'arrêt immédiat et forcé qui ne peut pas être ignoré par le programme.

### Exercice Pratique Commenté
```bash
# 1. Chercher le processus de Firefox parmi tous les processus du système
# Le symbole '|' (pipe) envoie le résultat de 'ps aux' à la commande 'grep' qui filtre la ligne contenant 'firefox'
ps aux | grep firefox

# 2. Lancer la surveillance dynamique du système
top
# NOTE: Une fois dans top, observez les colonnes PID, %CPU et %MEM. Quittez en appuyant sur la touche 'q'.

# 3. Forcer l'arrêt du processus récalcitrant. Remplacer 1234 par le vrai numéro PID trouvé à l'étape 1
kill -9 1234
```

---

## SECTION 4 : INSTALLER / DÉSINSTALLER UN PAQUET (apt)

### Commande de base et arguments
* `apt` (Advanced Package Tool) : Gestionnaire de paquets d'Ubuntu.
  * `update` : Télécharge la liste mise à jour des logiciels disponibles sur les serveurs officiels. À faire avant d'installer.
  * `install` : Télécharge et installe un logiciel et ses dépendances.
    * `-y` (--yes) : Répond automatiquement "Oui" à toutes les questions durant l'installation (évite l'interruption).
  * `purge` : Supprime le logiciel ET tous ses fichiers de configuration globale.
  * `autoremove` : Supprime les paquets et dépendances qui ont été installés automatiquement mais qui ne sont plus nécessaires.

### Exercice Pratique Commenté
```bash
# 1. Mettre à jour la base de données locale des logiciels disponibles
sudo apt update

# 2. Installer l'outil réseau 'curl' de manière automatisée sans confirmation manuelle
sudo apt install -y curl

# 3. Supprimer proprement le logiciel 'curl' ainsi que toute sa configuration résiduelle
sudo apt purge curl

# 4. Nettoyer le système en enlevant les dépendances logicielles devenues orphelines et inutiles
sudo apt autoremove
```

---

## SECTION 5 : CRÉER UN UTILISATEUR ET GÉRER LES GROUPES

### Commande de base et arguments
* `useradd` : Crée un nouvel utilisateur de bas niveau.
  * `-m` (--create-home) : Crée automatiquement le répertoire personnel de l'utilisateur dans `/home/nom_utilisateur`.
* `passwd` : Modifie le mot de passe d'un utilisateur.
* `groupadd` : Crée un nouveau groupe d'utilisateurs sur le système.
* `usermod` (User Modify) : Modifie les paramètres d'un utilisateur existant.
  * `-aG` : Option combinée cruciale :
    * `-a` (--append) : Ajoute l'utilisateur aux nouveaux groupes *sans* le supprimer de ses groupes actuels.
    * `-G` (--groups) : Spécifie la liste des groupes additionnels.

### Exercice Pratique Commenté
```bash
# 1. Créer un utilisateur appelé 'bob' avec la création de son dossier de profil (/home/bob)
sudo useradd -m bob

# 2. Assigner un mot de passe sécurisé à bob (le système demandera de le taper deux fois de manière invisible)
sudo passwd bob

# 3. Créer un groupe de travail nommé 'sysadmin'
sudo groupadd sysadmin

# 4. Ajouter bob au groupe 'sysadmin' ET au groupe 'sudo' (qui donne les droits d'administration sur Ubuntu)
sudo usermod -aG sysadmin,sudo bob
```

---

## SECTION 6 : SE CONNECTER EN SSH ET VÉRIFIER LES PORTS OUVERTS

### Commande de base et arguments
* `ssh` (Secure Shell) : Outil de connexion chiffrée à distance.
  * Syntaxe `identifiant@adresse_ip` : Permet de spécifier avec quel compte se connecter sur la machine cible.
* `ss` (Socket Statistics) : Outil moderne pour inspecter les connexions réseau (remplace l'ancienne commande netstat).
  * `-tulpn` : Option combinée indispensable :
    * `-t` : Affiche les sockets TCP.
    * `-u` : Affiche les sockets UDP.
    * `-l` : Affiche uniquement les ports actuellement en écoute (Listening).
    * `-p` : Affiche le nom et le PID du processus qui utilise ce port.
    * `-n` : Affiche les numéros de ports et adresses au format numérique (ex: 22 au lieu de 'ssh') pour accélérer la commande.

### Exercice Pratique Commenté
```bash
# 1. Lancer une connexion terminal distante vers la machine 192.168.1.50 sous l'identité 'ubuntu'
ssh ubuntu@192.168.1.50
# NOTE : Tapez 'exit' pour fermer la session SSH une fois vos tests finis.

# 2. Vérifier quels programmes écoutent sur le réseau local ou internet, avec détails des processus (-tulpn)
sudo ss -tulpn
```

---

## SECTION 7 : GÉRER UN SERVICE AVEC systemctl ET LIRE SES LOGS

### Commande de base et arguments
* `systemctl` : Gère le gestionnaire de système et de services `systemd`.
  * `start` : Démarre un service immédiatement.
  * `status` : Affiche l'état détaillé actuel d'un service (actif, arrêté, en erreur, derniers logs).
  * `enable` : Configure le service pour qu'il démarre automatiquement à chaque mise en route de la machine.
* `journalctl` : Interroge les journaux de bord (logs) du système centralisé par systemd.
  * `-u` (--unit) : Filtre les journaux uniquement pour un service spécifique (ex: nginx).
  * `-f` (--follow) : Mode "temps réel". Le terminal reste ouvert et affiche les nouvelles lignes de log au fur et à mesure qu'elles s'écrivent.

### Exercice Pratique Commenté
```bash
# (Hypothèse : le serveur web Nginx est installé sur la machine)

# 1. Lancer immédiatement le serveur web Nginx et inspecter s'il tourne correctement
sudo systemctl start nginx
sudo systemctl status nginx

# 2. Programmer le service pour qu'il s'exécute automatiquement après chaque redémarrage système
sudo systemctl enable nginx

# 3. Suivre en direct (-f) les connexions et messages du service nginx (-u)
sudo journalctl -u nginx -f
# NOTE : Utilisez 'Ctrl + C' pour arrêter l'affichage en direct des logs.
```

---

## SECTION 8 : FILTRER DES LOGS ET ÉCRIRE UN SCRIPT BASH SIMPLE

### Commande de base et arguments
* `cat` (Concatenate) : Affiche l'intégralité du contenu d'un fichier texte dans le terminal.
* `grep` : Recherche et affiche les lignes contenant un mot précis.
* `awk` : Un langage de programmation complet en ligne de commande servant à manipuler des données textuelles structurées en colonnes.
  * `'{print $11}'` : Demande à awk de découper la ligne de texte reçue et d'afficher uniquement le contenu situé à la 11ème colonne.

### Exercice de Filtrage Pratique Commenté
```bash
# Extraire les tentatives d'accès refusées dans les logs de sécurité
# On lit le fichier, grep garde uniquement les échecs ("Failed"), et awk isole la 11ème colonne (souvent l'adresse IP de l'attaquant)
sudo cat /var/log/auth.log | grep "Failed" | awk '{print $11}'
```

### Exercice de Scripting Pratique Commenté
Voici le code complet d'un script d'automatisation. Il utilise l'en-tête obligatoire `#!/bin/bash` appelée un **Shebang**. Elle indique au système que ce fichier doit être interprété par le terminal Bash.

```bash
#!/bin/bash
# ==============================================================================
# SCRIPT : backup.sh
# DESCRIPTION : Automatise la sauvegarde du dossier de projet vers un dossier cible
# ==============================================================================

# Déclaration des variables de chemin (Plus propre et réutilisable)
SOURCE="/home/ubuntu/projet"
CIBLE="/home/ubuntu/sauvegarde_projet"

# Étape 1 : Affichage d'un message d'information dans le terminal
echo "Début de la sauvegarde automatique..."

# Étape 2 : Création du dossier cible si jamais il n'existe pas (-p évite l'erreur)
mkdir -p "$CIBLE"

# Étape 3 : Copie récursive (-r) de tous les fichiers (*) du dossier source vers la cible
cp -r "$SOURCE"/* "$CIBLE"

# Étape 4 : Confirmation incluant la date et l'heure système via la commande $(date)
echo "Sauvegarde terminée avec succès le $(date)"
```

### Comment exécuter ce script :
1. Créez le fichier : `touch backup.sh`
2. Ouvrez un éditeur (ex: `nano backup.sh`), collez le script ci-dessus et sauvegardez.
3. Donnez-vous le droit de l'exécuter : `chmod u+x backup.sh`
4. Lancez le script : `./backup.sh`


## 🛠️ 9. Guide de Survie Pratique : L'Éditeur Vim

**Vim** est un éditeur de texte modal ultra-rapide intégré au terminal. Il possède deux états principaux : le **Mode Normal** (pour naviguer et lancer des commandes) et le **Mode Insertion** (pour taper du texte).

### Les 4 étapes fondamentales

1.  **Ouvrir ou créer un fichier :**
    ```bash
    vim exercice.txt
    ```
2.  **Passer en mode Écriture :** Appuyez sur la touche **`i`** (la mention `-- INSERTION --` apparaît en bas). Vous pouvez maintenant taper vos textes ou codes.
3.  **Quitter le mode Écriture :** Appuyez sur la touche **`Échap`** (ou `Esc`). La mention `-- INSERTION --` disparaît. Vous revenez en Mode Normal.
4.  **Enregistrer et fermer :** En Mode Normal, tapez **`:wq`** puis appuyez sur `Entrée`.

### Raccourcis indispensables de l'Anti-sèche Vim

Toutes ces commandes doivent être tapées en **Mode Normal** (après avoir appuyé sur `Échap`).

| Commande | Action |
| :--- | :--- |
| **`i`** | Passer en mode écriture (Insertion) |
| **`Échap`** | Sortir du mode écriture pour exécuter des commandes |
| **`:wq`** | Sauvegarder les modifications et fermer le fichier (*write and quit*) |
| **`:q!`** | Fermer le fichier immédiatement **sans sauvegarder** (annuler tout) |

| :w | Sauvegarder le fichier sans le fermer |
| dd | Supprimer (couper) la ligne entière sous le curseur |
| x | Supprimer le caractère unique situé sous le curseur |
| u | Annuler la dernière action effectuée (undo) |
| Ctrl + r | Refaire l'action qui vient d'être annulée (redo) |
| gg | Déplacer instantanément le curseur à la toute première ligne du fichier |
| G | Déplacer instantanément le curseur à la toute dernière ligne du fichier |
| 0 | Placer le curseur au début de la ligne actuelle |
| $ | Placer le curseur à la fin de la ligne actuelle |
