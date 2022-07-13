### *Brief 2*
Groupe 2 : Paul, Dunvael, Raja, Noa
___
<div id='top'/>

## Sommaire :

##### [1 - Présentation de l'infrastructure](#Structure)
##### [2 - Description des éléments de l'infrastructure](#Description)
&nbsp;&nbsp; &nbsp; a) Topologie réseau  
&nbsp;&nbsp; &nbsp; b) Table d'adressage ip  
&nbsp;&nbsp; &nbsp; c) Les Vm  
&nbsp;&nbsp; &nbsp; d) VM administration  
&nbsp;&nbsp; &nbsp; e) VM netxcloud  
&nbsp;&nbsp; &nbsp; f) VM SGBDR  
&nbsp;&nbsp; &nbsp; g) Liste des ressources AZURE
##### [3 - Création des clés SSH](#SSH)
##### [4 - Installation paramétrages](#Paramétrages)
&nbsp;4-1 - Installation de `ls2-admin`  
&nbsp;4-2 - Installation de `ls2-BDD`  
&nbsp;&nbsp; &nbsp; a) Installation de PostgreSQL  
&nbsp;&nbsp; &nbsp; b) Création de l'utilisateur "nextcloud"  
&nbsp;4-3 - Installation de `l2-NC`  
&nbsp;&nbsp; &nbsp; a) Installation d'Apache  
&nbsp;&nbsp; &nbsp; b) Installation de PHP  
&nbsp;&nbsp; &nbsp; c) Installation de Nextcloud  
##### [5 - Création d'un FQDN](#FQDN)
##### [*Bonus : installation de TLS*](#TLS)

___
<div id='Structure'/>

## 1- Présentation de l'infrastructure   
L'infrastructure doit être composée de trois VM. Elle sera déployé en utilisant la solution AZURE CLOUD  
1. VM d'administration qui doit être accessile depuis l'extérieur.  
Elle sert de rebond pour pouvoir : administrer les 2 autres VM.
1. VM applicative : elle héberge l'application "Nextcloud", elle est reliée à la VM qui héberge le serveur base de données. 
2. La VM Base de données : elle distribue son service à destination de la VM applicative.  

L'infrastructure sera déployé sur le réseau 10.0.2.0/24

[&#8679;](#top) 

<div id='Description'/>

## 2- Description des éléments de l'infrastructure  

  ## a) Topologie réseau  

![Topologie](plan_reseau.png)

  
  ## b) Table d'adressage ip  
  |nom de la VM|ip privée|ip public| Fonction |
  |------------|---------|---------|----------|
  |Ls2-admin| 10.0.2.10| 20.232.191.71 | Administration |
  |LifeNC-grp2 | 10.0.2.11 |  20.106.150.0 | NextCloud |
  |LifeBDD | 10.0.2.12 | ---- | SGBDR |
    
  |Passerelle | ip privée | DNS |
  |-----------|-----------|-----|
  | fournit par azure| 10.0.2.1| 168.63.129.16|
  
  ## c) Les VM
  (vérifier le thin provisionning de AZURE STACK HCI)

  ### d) VM administration  
  ||Ls-grp2-admin||
  |---|---|-------|
  | OS |Débian 11 "bullseye"|
  |HDD| 30 Go| |
  |CPU| 2 | |
  | RAM | 8 Go | |
  | Filesystem| ext4 | |
  
  ### e) VM Nextcloud  
  ||LifeNC-grp2||
  |---|---|-------|
  | OS |Débian 11 "bullseye"|
  |HDD| 30 Go| |
  |CPU| 2 | |
  | RAM | 8 Go | |
  | Filesystem| ext4 | |
  | soft 1 | Apache V 2.4 | serveur web |
  | soft 2| NextCloud | |
  
  ### f) VM SGBDR  
  ||LifeBDD||
  |---|---|-------|
  | OS |Débian 11 "bullseye"|
  |HDD| 30 Go| |
  |CPU| 2 | |
  | RAM | 8 Go | |
  | Filesystem| ext4 | |
  | soft 1| Postgresql | SGBR |
  
  ## g) Liste des ressources AZURE  
  1. créer le groupe de ressource  
  2. créer le réseau virtuel
   2.1 définir la plage d'adressage selon le réseau 10.0.2.0/24 ( ne pas oublier de supprimer la plage d'adressage par défaut de AZURE pour éviter le cheveauchement)
  3. création de la VM admin selon spécification ci-dessus 
  4. création de la VM BDD selon spécification ci-dessus avec déployement Postgresql
  5. création de la VM NextCloud selon spécification ci-dessus, installation Apache v 2.4, installation Nextcloud
  6. mise en place des règles de routage ( HTTP par le port 1080, SSH part le port 1022)

[&#8679;](#top) 

<div id='SSH'>

 ## 3 - Création des clés SSH   
 Lancer Windows PowerShell et taper la commande :  
 `sudo ssh-keygen`  
 Enregistrer la clé dans un sous-dossier du répertoire utilisateur nommé `.ssh`

[&#8679;](#top) 

<div id='Paramétrages'>

## 4 - Installation paramétrages

### 4.1 - Installation de `ls2-admin`

Ajouter sa clé publique sur la machine virtuelle
`ssh-copy-id grp2@20.25.9.8`

[&#8679;](#top) 

### 4.2 - Installation de `ls2-BDD`

#### a) Installation de PostgreSQL
`sudo apt update`
`sudo apt install postgresql postgresql-contrib`  
Modifier deux fichiers 

[&#8679;](#top) 

#### b) Création de l'utilisateur "nextcloud"
`sudo su - postgres psql`   
`postgres=> ALTER ROLE nextcloud WITH PASSWORD Lifesense123;`  
`postgres=> CREATE DATABASE nextclouddb;`  
`postgres=> GANT ALL PRIVILEGES ON DATABASE nextclouddb TO nextcloud;` 

[&#8679;](#top) 

### 4.3 - Installation de `l2-NC`

#### a) Installation d'Apache
`sudo apt-get install apache2`  

Changer le port d'écoute en 8080 : `vim /etc/apache2/ports.conf`   

Dans vim, modifier la ligne : `Listen 80` en `Listen 8080`   

Aller sur Azure et changer le port http 80 par 8080 en cliquant sur `mise en réseau > custom`

[&#8679;](#top) 

#### b) Installation de PHP
`sudo apt -y install php php-common php-zip php-gd php-mbstring php-curl php-xml php-pgsql`

`sudo systemctl restart apache2`

[&#8679;](#top) 

#### c) Installation de Nextcloud
* 1 -  Supprimer le dossier `/var/www/html`  
* 2 - Aller dans /var/www/ et y télécharger nextcloud 
`apt-get https://download.nextcloud.com/server/releases/latest.tar.bz2`  
* 3 - Une fois l'archive téléchargée, l'extraire : `tar xvf ./latest.tar.bz2`  
Renommer le nouveau dossier `nextcloud` en `html` avec la commande `mv nextcloud html`  
* 4 - Redéfinir les droits utilisateurs avec la commande `chmod 777 -R`  
* 5 - Modifier la configuration de nextcloud : `vim /var/www/html/conf/config.php`  
* 6 - Modifier le trust domain : dans le tableau mettre `0=> '20.120.12.97:8080`  
* 7 - Redémarrer apache : `systemctl restart apache2` puis vérifier qu'apache est actif `systemctl status apache2`
 
 [&#8679;](#top) 

 <div id='FQDN'>

### 5 - Création d'un FQDN

* 1 - Aller dans Microsoft Azure et taper "FQDN" dans la barre de recherche.
* 2 - Dans Microsoft Azure, aller sur la VM `ls2-admin`
* 3 - Dans le menu de gauche, aller dans Propriétés
* 4 - Séléctionner l'adresse IP sous Adresse IP publique\Etiquettes du nom DNS
* 5 - Sous Étiquette du nom DNS, entrer le préfixe à utiliser
* 6 - Sélectionner Enregistrer en haut de la page
* 7 - dans le menu de gauche, sélectionner" Vue d’ensemble" pour revenir à la vue d’ensemble de la machine virtuelle
* 8 - Vérifier que le nom DNS s’affiche correctement

[&#8679;](#top) 

 <div id='TLS'>

### *Bonus : installation de TLS*

Une fois connecté à la VM Nextcloud, nous avons utilisé les commandes suivantes:  
* 1 - Installation de Cerbot  
`sudo apt install python3-certbot-apache -y`  
* 2 - Création du certificat TLS `sudo certbot --apache --agree-tos --redirect --hsts --staple-ocsp -d lifesense2.eastus.cloudapp.azure.com` 
* 3 - Cerbot doit automatiquement passer par le port 80  
`vim /etc/apache2/config/ports.conf`
* 4 - Il faut ensuite changer le Trusted Domains  
`vim /etc/www/var/html/config/config.php`

[&#8679;](#top) 



autorisations réseau  
pghba liste des users et quelle ip  
postgres.conf * 0.0.0.0 
 
  

 
    
