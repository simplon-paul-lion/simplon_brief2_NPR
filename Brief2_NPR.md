

# Présentation de l'infrastructure  
L'infrastructure doit être composée de trois VM.  
1. VM d'administration qui doit être la seule accessile depuis l'extérieur.  
Elle sert de rebond pour pouvoir : administrer les 2 autres VM, rendre accessible l'application hébergée sur la VM applicative.
2. VM applicative : elle héberge la'application "Nextcloud", elle est reliée à la VM qui héberge le serveur base de données.
3. La VM Base de données : elle distribue son service à destination de la VM applicative.  

# Description des éléments de l'infrastructure  
  
  ## table d'adressage ip  
  |nom de la VM|ip privée|ip public| Fonction |
  |------------|---------|---------|----------|
  |Ls-grp2-admin| 10.0.2.10| 20.231.84.180 | Administration |
  |LifeNC-grp2 | 10.0.2.11 |  --- | NextCloud |
  |LifeBDD | 10.0.2.12 | ---- | SGBDR |
  
  
  ## les VM
  ### VM administration  
  ||Ls-grp2-admin||
  |---|---|-------|
  | OS |Débian 11 "bullseye"|
  |HDD| 30 Go| |
  |CPU| 2 | |
  | RAM | 8 Go | |
  | Filesystem| ext4 | |
  
  ### VM Nextcloud  
  ||LifeNC-grp2||
  |---|---|-------|
  | OS |Débian 11 "bullseye"|
  |HDD| 30 Go| |
  |CPU| 2 | |
  | RAM | 8 Go | |
  | Filesystem| ext4 | |
  
  ### VM SGBDR  
  ||LifeBDD||
  |---|---|-------|
  | OS |Débian 11 "bullseye"|
  |HDD| 30 Go| |
  |CPU| 2 | |
  | RAM | 8 Go | |
  | Filesystem| ext4 | |
    
