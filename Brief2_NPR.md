### *Brief 2*
Groupe 2 : Paul, Dunvael, Raja, Noa
___
<div id='top'/>

## Sommaire :

### [1 - Présentation de l'infrastructure](#Structure)

### [2 - Description des éléments de l'infrastructure](#Description)

&nbsp;&nbsp; &nbsp; [a) Topologie réseau](#Réseau) 

&nbsp;&nbsp; &nbsp; [b) Table d'adressage ip ](#Ip) 

&nbsp;&nbsp; &nbsp; [c) Les Vm](#Vm)

&nbsp;&nbsp; &nbsp; [d) VM administration](#administration)

&nbsp;&nbsp; &nbsp; [e) VM netxcloud](#nextcloud) 

&nbsp;&nbsp; &nbsp; [f) VM SGBDR](#SGBDR)

&nbsp;&nbsp; &nbsp; [g) Liste des ressources](#ressources)
### [3 - Création des clés SSH](#SSH)

### [4 - Installation paramétrages](#Paramétrages)

&nbsp;[4-1 - Installation de ls2-admin](#ls2admin)

&nbsp;[4-2 - Installation de ls2-BDD](#ls2-BDD) 

&nbsp;&nbsp; &nbsp; [a) Installation de PostgreSQL](#PostgreSQL)

&nbsp;&nbsp; &nbsp; [b) Création de l'utilisateur "nextcloud"](#netcloud)

&nbsp;[4-3 - Installation de ls2-NC](#ls2-NC)

&nbsp;&nbsp; &nbsp; [a) Installation d'Apache2](#APACHE2)

&nbsp;&nbsp; &nbsp; [b) Installation de PHP](#PHP)

&nbsp;&nbsp; &nbsp; [c) Installation de Nextcloud](#Nextcloud)

### [5 - Création d'un FQDN](#FQDN)

### [*Bonus : installation de TLS*](#TLS)


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

[&#8679;](#top)

<div id='réseau'/>



  
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
  |HDD| 32 Go| |
  |CPU| 2 | |
  | RAM | 8 Go | |
  | Filesystem| ext4 | |
  
  ### e) VM Nextcloud  
  ||LifeNC-grp2||
  |---|---|-------|
  | OS |Débian 11 "bullseye"|
  |HDD| 128 Go| |
  |CPU| 2 | |
  | RAM | 8 Go | |
  | Filesystem| ext4 | |
  | soft 1 | Apache V 2.4 | serveur web |
  | soft 2| NextCloud | |
  
  ### f) VM SGBDR  
  ||LifeBDD||
  |---|---|-------|
  | OS |Débian 11 "bullseye"|
  |HDD| 64 Go| |
  |CPU| 2 | |
  | RAM | 8 Go | |
  | Filesystem| ext4 | |
  | soft 1| Postgresql | SGBR |
  
  ## g) Liste des ressources AZURE  
  ### *1. Créer le groupe de ressource* 

<img width="472" alt="groupederessources" src="https://user-images.githubusercontent.com/108053084/178955829-a1d0f347-2da6-49a0-bcd4-f673ab1b91f1.png">

---

<img width="471" alt="groupederessource1" src="https://user-images.githubusercontent.com/108053084/178954810-b875bccd-d7e5-40b2-af6c-d5189f70cbce.png">

---

<img width="359" alt="groupederessources3" src="https://user-images.githubusercontent.com/108053084/178957968-4ea2aa74-6a34-4098-91de-a1ebc9ee8928.png">

----

<img width="366" alt="groupederessources4" src="https://user-images.githubusercontent.com/108053084/178959858-a0e71049-1845-4d05-a9c0-90e74b2ba05d.png">


  ### *2. créer le réseau virtuel*
   
<img width="473" alt="réseauvirtuelle1" src="https://user-images.githubusercontent.com/108053084/178960907-0b631e38-6e77-48e7-ba75-74f46a580e23.PNG">

---
<img width="480" alt="réseauvirtuelle2" src="https://user-images.githubusercontent.com/108053084/178962086-560e881f-33fc-47b3-9362-2682d82f5a53.png">

---
<img width="400" alt="réseauvirtuelle3" src="https://user-images.githubusercontent.com/108053084/178964278-5440ad0d-58de-4a4e-a93d-7bfd38a8a32d.png">

---

<img width="430" alt="réseauvirtuelle4" src="https://user-images.githubusercontent.com/108053084/179008086-810aeabd-cf80-4485-8c3d-bbfdf8cd63c6.png">

---

 *2.1 définir la plage d'adressage selon le réseau 10.0.2.0/24 ( ne pas oublier de supprimer la plage d'adressage par défaut de AZURE pour éviter le cheveauchement)*

<img width="478" alt="réseauvirtuelle5 1" src="https://user-images.githubusercontent.com/108053084/179009098-aa23568e-01fb-4018-a3a1-b7386f0b226e.png">

---

<img width="381" alt="réseauvirtuelle5" src="https://user-images.githubusercontent.com/108053084/178966266-9bdbc4a0-f441-448f-ac59-7848a2e08828.png">

---

<img width="411" alt="réseauvirtuelle7" src="https://user-images.githubusercontent.com/108053084/179009880-ed9fac64-1d7e-482c-a4d6-40756e8e6024.png">

---
  
  *2.2 - création de la VM admin selon spécification ci-dessus* 

  
<img width="467" alt="mv" src="https://user-images.githubusercontent.com/108053084/179014203-f272b59e-3d71-403e-aa8e-aeecbe0e6fb6.png">

---

<img width="318" alt="mv 1" src="https://user-images.githubusercontent.com/108053084/179018049-6ce8e5c5-9aa6-4413-9df6-2237abc5b8e8.PNG">

---

<img width="375" alt="hdd" src="https://user-images.githubusercontent.com/108053084/179063039-0d98c301-c1fd-48cd-9f39-ea769b19e25b.png">

---

<img width="347" alt="disquedu png" src="https://user-images.githubusercontent.com/108053084/179064504-c31f3a27-1e42-4fad-82fc-28ffff035311.PNG">

---

<img width="327" alt="mvadmin4" src="https://user-images.githubusercontent.com/108053084/179064852-b015b3c5-c2f5-44fc-a802-5a82157ab696.png">

---

<img width="442" alt="mvadmin5" src="https://user-images.githubusercontent.com/108053084/179065123-c8952d9f-d209-4f80-9081-302a992b1d60.png">

---

  *2.3 Création de la VM BDD selon spécification ci-dessus avec déployement Postgresql*

  <img width="275" alt="lsbdd1" src="https://user-images.githubusercontent.com/108053084/179067414-515075b6-e228-49de-b656-12abf70357f3.png">

---

<img width="338" alt="lsbdd2" src="https://user-images.githubusercontent.com/108053084/179070934-1de69abc-04a2-4918-b4ad-164aa8cd1abe.png">

---

<img width="329" alt="lsbdd3" src="https://user-images.githubusercontent.com/108053084/179071829-bfdde545-3e88-42c1-a9ef-ae89c0b8454e.png">

---

<img width="484" alt="lsbdd3 1" src="https://user-images.githubusercontent.com/108053084/179072256-3f4272c4-7647-47bb-bbdf-1d097b954a62.png">

---

<img width="366" alt="lsbdd4" src="https://user-images.githubusercontent.com/108053084/179073249-31e94639-4754-47d1-a974-82d53d45deee.png">

---

<img width="510" alt="lsbdd5" src="https://user-images.githubusercontent.com/108053084/179074467-a8687c02-41da-4779-b16d-201157fd0765.png">

---

  *2.4 création de la VM NextCloud selon spécification ci-dessus, installation Apache v 2.4, installation Nextcloud*

<img width="276" alt="lsnc pgn" src="https://user-images.githubusercontent.com/108053084/179076891-e1f5b6b7-277d-4eb4-88df-3c39d19ed2a9.PNG">

---

<img width="399" alt="lsnc1 pgn" src="https://user-images.githubusercontent.com/108053084/179078030-00664eca-04ab-4787-8766-2cbd97921bc6.PNG">

---

<img width="398" alt="lsnc2 pgn" src="https://user-images.githubusercontent.com/108053084/179079066-71c5de35-bb9a-4c30-8c54-7e30ef9884fd.PNG">

<img width="442" alt="lsnc2 1 pgn" src="https://user-images.githubusercontent.com/108053084/179079669-d79fe47e-aaae-43ea-97e9-36f7959efa8f.PNG">

---

<img width="398" alt="lsnc3 pgn" src="https://user-images.githubusercontent.com/108053084/179084589-052e22a4-cfee-40af-9167-272ad4bc8bde.PNG">

  *4. mise en place des règles de routage ( HTTP par le port 1080, SSH part le port 1022)*


<div id='Azure'/> 

<div id='SSH'>

 ## 3 - Création des clés SSH   
 Lancer Windows PowerShell et taper la commande :  
 `sudo ssh-keygen`  
 Il est important de ne pas utiliser le sudo afin de ne pas donner tous les droits à la clé publique de l'utilisateur, ce qui pourrait ensuite poser des conflits.  
 Enregistrer la clé dans un sous-dossier du répertoire utilisateur nommé `.ssh`

<div id='SSH'/>

[&#8679;](#top)

## 4 - Installation paramétrages


<div id='Paramétrages'>


### 4.1 - Installation de `ls2-admin`

Ajouter sa clé publique sur la machine virtuelle
`ssh-copy-id grp2@20.25.9.8`

<div id='Paramétrages'/> 

### 4.2 - Installation de `ls2-BDD`

#### a) Installation de PostgreSQL
`sudo apt update`
`sudo apt install postgresql postgresql-contrib`  
Modifier deux fichiers 

<div id='ls2-BDD'/> 

#### b) Création de l'utilisateur "nextcloud"
`sudo su - postgres psql`   
`postgres=> ALTER ROLE nextcloud WITH PASSWORD Lifesense123;`  
`postgres=> CREATE DATABASE nextclouddb;`  
`postgres=> GRANT ALL PRIVILEGES ON DATABASE nextclouddb TO nextcloud;` 

[&#8679;](#top) 

<div id='nextcloud'/> 

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
 Procéder à l'installation en root.
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

* 1 - *Aller dans Microsoft Azure et taper "FQDN" dans la barre de recherche.*

  *Sélectionnez la machine virtuelle  "nextcloud"*

<img width="440" alt="fqdn" src="https://user-images.githubusercontent.com/108053084/179086423-8b4d5e5e-7fdd-4c84-94f5-1961d490033e.PNG">

* 2 - *Dans le menu de gauche, sélectionnez Propriétés*.

<img width="318" alt="fqdn2" src="https://user-images.githubusercontent.com/108053084/179089772-dd200f34-8df1-4ad3-bb6d-c03e1b5e4886.png">

* 3 - *Sous Adresse IP publique\Étiquette du nom DNS, sélectionnez notre adresse IP*

<img width="306" alt="fqdn2" src="https://user-images.githubusercontent.com/108053084/179089590-0321f5f2-6230-44e9-8de2-0aabe57d1acc.png">


* 4 - *Sous Étiquette du nom DNS, entrez le préfixe à utiliser*

<img width="438" alt="fqdn3" src="https://user-images.githubusercontent.com/108053084/179090204-bfe7f1b2-31e1-4831-bfc6-6b6de31202d8.png">

 * 5 - *En haut de la page, sélectionnez Enregistrer*

<img width="438" alt="fqdn3" src="https://user-images.githubusercontent.com/108053084/179090525-e4104ca5-813e-4ff8-8742-8ae7808a0465.png">

 * 6 - *Sélectionnez Vue d’ensemble dans le menu de gauche pour revenir à la vue d’ensemble de la machine virtuelle "nextcloud"*.

<img width="439" alt="fqdn4" src="https://user-images.githubusercontent.com/108053084/179090934-d42f97ee-41d1-4a7e-b5e5-ca57578915e9.png">

 * 7 - *Vérifiez que le nom DNS s’affiche correctement*.

<img width="437" alt="fqdn5" src="https://user-images.githubusercontent.com/108053084/179091587-975b58cf-4928-4ab3-996d-e1cfb9146b3e.png">


<img width="440" alt="fqdn6" src="https://user-images.githubusercontent.com/108053084/179097788-fec15aae-f8e3-47b8-b1f6-50a4025a1f6b.png">

* 8 - *Ajouter dans le fichier de configuration nextcloud (config.php), le nom de domaine en tant que trusted_domains.*

[&#8679;](#top) 

 <div id='TLS'>

### *Bonus : installation de TLS*

Une fois connecté à la VM Nextcloud, nous avons utilisé les commandes suivantes:  
* 1 - Installation de Cerbot  
`sudo apt install python3-certbot-apache -y`  
* 2 - Création du certificat TLS `sudo certbot --apache --agree-tos --redirect --hsts --staple-ocsp -d lifesense2.eastus.cloudapp.azure.com` 
* 3 - Cerbot impose l'utilisation du port 80  
`vim /etc/apache2/config/ports.conf`
* 4 - Il faut ensuite changer le Trusted Domains  
`vim /etc/www/var/html/config/config.php`

[&#8679;](#top) 

autorisations réseau  
pghba liste des users et quelle ip  
postgres.conf * 0.0.0.0 
 
  

 
    
 
  

 
     
 
  

 
    
