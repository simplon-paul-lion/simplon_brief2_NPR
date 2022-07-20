# *Brief 2*
Groupe 2 : Dunvael, Noa, Paul, Raja
___
<div id='top'/>

## Sommaire :

### [1 - Présentation de l'infrastructure](#Structure)

### [2 - Description des éléments de l'infrastructure](#Description)
&nbsp;&nbsp;&nbsp;[a) Topologie réseau](#Réseau) 
&nbsp;&nbsp;&nbsp;[b) Table d'adressage ip ](#Adressage) 
&nbsp;&nbsp;&nbsp;[c) Les machines virtuelles](#VM)
&nbsp;&nbsp;&nbsp;[d) VM administration](#Administration)
&nbsp;&nbsp;&nbsp;[e) VM nextcloud](#VMNextcloud) 
&nbsp;&nbsp;&nbsp;[f) VM SGBDR](#SGBDR)
&nbsp;&nbsp;&nbsp;[g) Liste des ressources](#ListeRessources)

### [3 - Création des clés SSH](#SSH)

### [4 - Installation paramétrages](#Paramétrages)

&nbsp;[4-1 - Installation de ls2-admin](#ls2-admin)

&nbsp;[4-2 - Installation de ls2-BDD](#ls2-BDD) 

&nbsp;&nbsp; &nbsp; [a) Installation de PostgreSQL](#PostgreSQL)

&nbsp;&nbsp; &nbsp; [b) Création de l'utilisateur "nextcloud"](#Utilisateur)

&nbsp;[4-3 - Installation de ls2-NC](#ls2-NC)

&nbsp;&nbsp; &nbsp; [a) Installation d'Apache2](#Apache)

&nbsp;&nbsp; &nbsp; [b) Installation de PHP](#PHP)

&nbsp;&nbsp; &nbsp; [c) Installation de Nextcloud](#Nextcloud)

### [5 - Création d'un FQDN](#FQDN)

### [*6 - Bonus : installation de TLS*](#TLS)


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

<div id='Réseau'/>

  ## a) Topologie réseau  

![plan_reseau](https://user-images.githubusercontent.com/108001918/179240652-f42f8f16-95e1-4d1c-8cb1-937da5150328.png)

[&#8679;](#top)

<div id='Adressage'/>
  
  ## b) Table d'adressage ip  
  |nom de la VM|ip privée|ip public| Fonction |
  |------------|---------|---------|----------|
  |Ls2-admin| 10.0.2.10| 20.232.191.71 | Administration |
  |LifeNC-grp2 | 10.0.2.11 |  20.106.150.0 | NextCloud |
  |LifeBDD | 10.0.2.12 | ---- | SGBDR |
    
  |Passerelle | ip privée | DNS |
  |-----------|-----------|-----|
  | fournit par azure| 10.0.2.1| 168.63.129.16|
 
 [&#8679;](#top)
 
 <div id='VM'/>
 
  ## c) Les VM
  (vérifier le thin provisionning de AZURE STACK HCI)

[&#8679;](#top)

<div id='Administration'/>

  ### d) VM administration  
  ||Ls-grp2-admin||
  |---|---|-------|
  | OS |Débian 11 "bullseye"|
  |HDD| 32 Go| |
  |CPU| 2 | |
  | RAM | 8 Go | |
  | Filesystem| ext4 | |
  
 [&#8679;](#top)
 
 <div id='VMNextcloud'/> 
 
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
 
 [&#8679;](#top)
 
 <div id='SGBDR'/> 
 
  ### f) VM SGBDR  
  ||LifeBDD||
  |---|---|-------|
  | OS |Débian 11 "bullseye"|
  |HDD| 64 Go| |
  |CPU| 2 | |
  | RAM | 8 Go | |
  | Filesystem| ext4 | |
  | soft 1| Postgresql | SGBR |
 
 [&#8679;](#top)
 
 <div id='ListeRessources'/> 
 
  ## g) Liste des ressources AZURE  
  ### *1. Créer le groupe de ressource* 

<img width="472" alt="groupederessources" src="https://user-images.githubusercontent.com/108053084/178955829-a1d0f347-2da6-49a0-bcd4-f673ab1b91f1.png">

---

<img width="471" alt="groupederessource1" src="https://user-images.githubusercontent.com/108053084/178954810-b875bccd-d7e5-40b2-af6c-d5189f70cbce.png">

---

<img width="359" alt="groupederessources3" src="https://user-images.githubusercontent.com/108053084/178957968-4ea2aa74-6a34-4098-91de-a1ebc9ee8928.png">

----

<img width="366" alt="groupederessources4" src="https://user-images.githubusercontent.com/108053084/178959858-a0e71049-1845-4d05-a9c0-90e74b2ba05d.png">

[&#8679;](#top)

  ### *2. créer le réseau virtuel*
   
<img width="473" alt="réseauvirtuelle1" src="https://user-images.githubusercontent.com/108053084/178960907-0b631e38-6e77-48e7-ba75-74f46a580e23.PNG">

---
<img width="480" alt="réseauvirtuelle2" src="https://user-images.githubusercontent.com/108053084/178962086-560e881f-33fc-47b3-9362-2682d82f5a53.png">

---
<img width="400" alt="réseauvirtuelle3" src="https://user-images.githubusercontent.com/108053084/178964278-5440ad0d-58de-4a4e-a93d-7bfd38a8a32d.png">

---

<img width="430" alt="réseauvirtuelle4" src="https://user-images.githubusercontent.com/108053084/179008086-810aeabd-cf80-4485-8c3d-bbfdf8cd63c6.png">

---
[&#8679;](#top)

 * 2.1 *Définir la plage d'adressage selon le réseau 10.0.2.0/24 ( ne pas oublier de supprimer la plage d'adressage par défaut de AZURE pour éviter le cheveauchement)*

<img width="478" alt="réseauvirtuelle5 1" src="https://user-images.githubusercontent.com/108053084/179009098-aa23568e-01fb-4018-a3a1-b7386f0b226e.png">

---

<img width="381" alt="réseauvirtuelle5" src="https://user-images.githubusercontent.com/108053084/178966266-9bdbc4a0-f441-448f-ac59-7848a2e08828.png">

---

<img width="411" alt="réseauvirtuelle7" src="https://user-images.githubusercontent.com/108053084/179009880-ed9fac64-1d7e-482c-a4d6-40756e8e6024.png">

---
[&#8679;](#top)

  * 2.2 - *Création de la VM admin selon spécification ci-dessus* 

  
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

[&#8679;](#top)

  * 2.3 *Création de la VM BDD selon spécification ci-dessus avec déployement Postgresql*

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
[&#8679;](#top)

  * 2.4 *Création de la VM NextCloud selon spécification ci-dessus, installation Apache v 2.4, installation Nextcloud*

<img width="276" alt="lsnc pgn" src="https://user-images.githubusercontent.com/108053084/179076891-e1f5b6b7-277d-4eb4-88df-3c39d19ed2a9.PNG">

---

<img width="399" alt="lsnc1 pgn" src="https://user-images.githubusercontent.com/108053084/179078030-00664eca-04ab-4787-8766-2cbd97921bc6.PNG">

---

<img width="398" alt="lsnc2 pgn" src="https://user-images.githubusercontent.com/108053084/179079066-71c5de35-bb9a-4c30-8c54-7e30ef9884fd.PNG">

<img width="442" alt="lsnc2 1 pgn" src="https://user-images.githubusercontent.com/108053084/179079669-d79fe47e-aaae-43ea-97e9-36f7959efa8f.PNG">

---

<img width="398" alt="lsnc3 pgn" src="https://user-images.githubusercontent.com/108053084/179084589-052e22a4-cfee-40af-9167-272ad4bc8bde.PNG">


[&#8679;](#top)

  * 3. *Mise en place des règles de routage ( HTTP par le port 1080, SSH part le port 1022)*

[&#8679;](#top)

<div id='SSH'>

 ## 3 - Création des clés SSH   
Se connecter à la VM à partir de Windows PowerShell.  
<img width="419" alt="Connection_VM" src="https://user-images.githubusercontent.com/108001918/179228110-7fbfb01b-b0d7-4b1a-8c9c-461110aac306.PNG">
Lancer Windows PowerShell et taper la commande :  
`sudo ssh-keygen`  
<img width="349" alt="SSH_keygen" src="https://user-images.githubusercontent.com/108001918/179228175-61c0853f-589f-42b3-a529-1f3611131cf9.PNG">
Il est important de ne pas utiliser le sudo afin de ne pas donner tous les droits à la clé publique de l'utilisateur, ce qui pourrait ensuite poser des conflits.  
Enregistrer la clé dans un sous-dossier du répertoire utilisateur nommé `.ssh`


[&#8679;](#top)

<div id='Paramétrages'>

## 4 - Installation paramétrages


<div id='ls2-admin'>


### 4.1 - Installation de `ls2-admin`

Ajouter sa clé publique sur la machine virtuelle
`ssh-copy-id grp2@20.25.9.8`

[&#8679;](#top)
    
<div id='ls2-BDD'/> 

### 4.2 - Installation de `ls2-BDD`

<div id='PostgreSQL'/>

#### a) Installation de PostgreSQL
`sudo apt update`
    
```
root@ls2-BDD:/home/grp2# apt-get install postgresql
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
postgresql is already the newest version (13+225).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

`sudo apt install postgresql postgresql-contrib` 

``` consol
    
root@ls2-BDD:/home/grp2# sudo apt install postgresql postgresql-contrib
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
postgresql is already the newest version (13+225).
The following NEW packages will be installed:
  postgresql-contrib
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 64.8 kB of archives.
Selecting previously unselected package postgresql-contrib. be used.
(Reading database ... 26618 files and directories currently installed.)
Preparing to unpack .../postgresql-contrib_13+225_all.deb ...
Unpacking postgresql-contrib (13+225) ...
Setting up postgresql-contrib (13+225) ...   

```
    
##### Configuration du PostgreSQL

Une partie de la configuration se trouve dans le fichier `/etc/postgresql/13/main/pg_hba.conf`.
    
```
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
host	all		nextcloud	10.0.2.11/32		md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     peer
host    replication     all             127.0.0.1/32            md5
host    replication     all             ::1/128                 md5 
```

[&#8679;](#top)
    
<div id='Utilisateur'/> 

#### b) Création de l'utilisateur "nextcloud"
`sudo su - postgres psql`   
`postgres=> ALTER ROLE nextcloud WITH PASSWORD Lifesense123;`  
`postgres=> CREATE DATABASE nextclouddb;`  
`postgres=> GRANT ALL PRIVILEGES ON DATABASE nextclouddb TO nextcloud;` 

[&#8679;](#top) 

<div id='nextcloud'/> 

<div id='ls2-NC'/>

### 4.3 - Installation de `ls2-NC`

<div id='Apache'/>

#### a) Installation d'Apache
`sudo apt-get install apache2`  
    
``` consol 

grp2@ls2-nextcloud:~$ sudo -s
root@ls2-nextcloud:/home/grp2# sudo apt-get install apache2
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
apache2 is already the newest version (2.4.54-1~deb11u1).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
    
```

Mettre à jour la liste des paquets apache disponibles : `sudo apt-get update`
    
``` consol 
    
root@ls2-nextcloud:/home/grp2# sudo apt-get update
Hit:1 http://debian-archive.trafficmanager.net/debian bullseye InRelease
Hit:2 http://debian-archive.trafficmanager.net/debian-security bullseye-security InRelease
Get:3 http://debian-archive.trafficmanager.net/debian bullseye-updates InRelease [39.4 kB]
Get:4 http://debian-archive.trafficmanager.net/debian bullseye-backports InRelease [44.2 kB]
Get:5 http://debian-archive.trafficmanager.net/debian bullseye-backports/main Sources.diff/Index [63.3 kB]
Get:6 http://debian-archive.trafficmanager.net/debian bullseye-backports/main amd64 Packages.diff/Index [63.3 kB]
Get:7 http://debian-archive.trafficmanager.net/debian bullseye-backports/main Sources T-2022-07-15-0209.58-F-2022-07-15-0209.58.pdiff [615 B]
Get:7 http://debian-archive.trafficmanager.net/debian bullseye-backports/main Sources T-2022-07-15-0209.58-F-2022-07-15-0209.58.pdiff [615 B]
Get:8 http://debian-archive.trafficmanager.net/debian bullseye-backports/main amd64 Packages T-2022-07-15-0806.17-F-2022-07-15-0209.58.pdiff [17.8 kB]
Get:8 http://debian-archive.trafficmanager.net/debian bullseye-backports/main amd64 Packages T-2022-07-15-0806.17-F-2022-07-15-0209.58.pdiff [17.8 kB]
Fetched 229 kB in 1s (447 kB/s)
Reading package lists... Done
        
```
  
Installer la dernière version des paquets d'apache : `sudo apt-get upgrade`

``` consol

root@ls2-nextcloud:/home/grp2# sudo apt-get upgrade
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
    
```

Changer le port d'écoute en 8080 : `vim /etc/apache2/ports.conf`   

Dans vim, modifier la ligne : `Listen 80` en `Listen 8080` 

 <img width="229" alt="8080" src="https://user-images.githubusercontent.com/108053084/179221942-9411ee12-8160-48da-befb-f6a6b93760c0.png">
  
---
    
Aller sur Azure et changer le port http 80 par 8080 en cliquant sur `mise en réseau > custom` 


<img width="794" alt="80801" src="https://user-images.githubusercontent.com/108053084/179229218-c069be4a-a692-4075-b794-964c500b4c59.png">
    
Puis taper `systemctl reload apache2`  
Enfin, vérifier le statut d'apache `systemctl status apache2`
    


[&#8679;](#top) 

<div id='PHP'/>
    
#### b) Installation de PHP
`sudo apt -y install php php-common php-zip php-gd php-mbstring php-curl php-xml php-pgsql`

`sudo systemctl reload apache2` 
`sudo systemctl status apache2`

[&#8679;](#top) 

<div id='Nextcloud'/>
    
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

## 5 - Création d'un FQDN

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


<img width="763" alt="Nextcloud_https_2" src="https://user-images.githubusercontent.com/108001918/179228463-fe06a617-cc34-4b8a-bcbe-3721e158e0c0.PNG">

* 8 - *Ajouter dans le fichier de configuration nextcloud (config.php), le nom de domaine en tant que trusted_domains.*  

[&#8679;](#top) 

 <div id='TLS'>

### *6 - Bonus : installation de TLS*

Une fois connecté à la VM Nextcloud, nous avons utilisé les commandes suivantes:  
* 1 - Installation de Cerbot  
`sudo apt install python3-certbot-apache -y`  
     
``` consol
     
grp2@ls2-nextcloud:~$ sudo apt install python3-certbot-apache -y
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
python3-certbot-apache is already the newest version (1.10.1-1).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.

```
     
* 2 - Création du certificat TLS   
`sudo certbot --apache --agree-tos --redirect --hsts --staple-ocsp -d lifesense2.eastus.cloudapp.azure.com`
     
* 3 - Cerbot impose l'utilisation du port 80  
`vim /etc/apache2/config/ports.conf`  
<img width="388" alt="Ports_80" src="https://user-images.githubusercontent.com/108001918/179229660-5fba37a2-4e71-468c-94df-22599f7ad9c2.PNG">

* 4 - Il faut ensuite changer le Trusted Domains  
`vim /etc/www/var/html/config/config.php`
<img width="345" alt="Trusted_domains" src="https://user-images.githubusercontent.com/108001918/179228634-11cc87d3-8b91-4658-b005-b5e2b124e371.PNG">

[&#8679;](#top) 
