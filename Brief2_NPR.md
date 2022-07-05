

# Présentation de l'infrastructure  
L'infrastructure doit être composée de trois VM.  
1. VM d'administration qui doit être la seule accessile depuis l'extérieur.  
Elle sert de rebond pour pouvoir : administrer les 2 autres VM, rendre accessible l'application hébergée sur la VM applicative.
2. VM applicative : elle héberge la'application "Nextcloud", elle est reliée à la VM qui héberge le serveur base de données.
3. La VM Base de données : elle distribue son service à destination de la VM applicative.  

# Description des éléments de l'infrastructure  

  ## Topologie réseau  

  ![Topologie](Plan réseau.png)
  
  ## table d'adressage ip  
  |nom de la VM|ip privée|ip public| Fonction |
  |------------|---------|---------|----------|
  |Ls-grp2-admin| 10.0.2.10| 20.231.84.180 | Administration |
  |LifeNC-grp2 | 10.0.2.11 |  20.106.150.0 | NextCloud |
  |LifeBDD | 10.0.2.12 | ---- | SGBDR |
    
  |Passerelle | ip privée | DNS |
  |-----------|-----------|-----|
  | fournit par azure| 10.0.2.1| 168.63.129.16|
  
  ## les VM
  ( vérifier le thin provisionning de AZURE STACK HCI)
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
  | soft 1 | Apache V 2.4 | serveur web |
  | soft 2| NextCloud | |
  
  ### VM SGBDR  
  ||LifeBDD||
  |---|---|-------|
  | OS |Débian 11 "bullseye"|
  |HDD| 30 Go| |
  |CPU| 2 | |
  | RAM | 8 Go | |
  | Filesystem| ext4 | |
  | soft 1| Postgresql | SGBR |
  
  ## Liste des ressources AZURE  
  1. créer le groupe de ressource  
  2. créer le réseau virtuel
   2.1 définir la plage d'adressage selon le réseau 10.0.2.0/24 ( ne pas oublier de supprimer la plage d'adressage par défaut de AZURE pour éviter le cheveauchement)
  3. création de la VM admin selon spécification ci-dessus 
  4. création de la VM BDD selon spécification ci-dessus avec déployement Postgresql
  5. création de la VM NextCloud selon spécification ci-dessus, installation Apache v 2.4, installation Nextcloud
  6. 
 
  
    
