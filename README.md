doc.md# Installation et Configuration GnuWorld
Documentation sur l'installation et la configuration de GnuWorld sous 24.04.3 LTS.

## ⚠️ Cette doc fait suite à [Installation-Serveur-IRCU2](https://github.com/SpacyXyt/Installation-Serveur-IRCU2)

# 1. Prérequis

- Un acces root
- Ubuntu 24.02.3 LTS

```bash
root@mail:~# apt update
root@mail:~# apt upgrade
root@mail:~# apt-get install -y byacc flex screen make cmake zip perl automake git wget ufw net-tools gcc oidentd libpqxx-dev tcl-dev build-essential
```

# 2. Configuration du pare-feu avec UFW

## ⚠️ IMPORTANT

Cette partie n'est pas obligatoire, elle fait suite au tuto sur les firewalls (le tuto arrive bientôt).

Avant d'activer le pare-feu, assurez-vous d'avoir ouvert tous les ports nécessaires. Une mauvaise configuration peut bloquer l'accès à votre serveur.

## Ports à ouvrir

Voici la liste des ports à configurer selon vos besoins :

| Port        | Protocole | Utilisation                                                                      |
| ----------- | --------- | -------------------------------------------------------------------------------- |
| 6667 - 7000 | TCP/UDP   | Plage de ports pour les services spécifiques (ex : IRC, jeux, etc.).             |
| 4400        | TCP/UDP   | Port personnalisé (à adapter selon votre configuration)                          |
| 113         | TCP       | Service Ident (utilisé pour l'authentification sur certains protocoles).         |
| 53          | TCP/UDP   | DNS (résolution de noms de domaine).                                             |
| 443         | TCP       | HTTPS (connexions sécurisées pour les sites web).                                |
| 80          | TCP       | HTTP (trafic web non sécurisé).                                                  |
| 22          | TCP       | SSH (accès sécurisé à distance). À adapter si modifié dans /etc/ssh/sshd_config. |
| 25          | TCP       | SMTP (envoi d'emails non sécurisé). À remplacer par 465 ou 587 si possible.      |
| 110         | TCP       | POP3 (réception d'emails non sécurisé).                                          |
| 465         | TCP       | Secure SMTP (SMTP sécurisé avec SSL).                                            |
| 587         | TCP       | TLS SMTP (SMTP sécurisé avec TLS).                                               |

**Remarques importantes**

1. **SMTP sécurisé :**

- Si vous utilisez **Secure SSL SMTP** ou **TLS SMTP**, vous n'avez **pas besoin** d'ouvrir le port 25 (SMTP non sécurisé).
- Privilégiez les ports **465 (SSL)** ou **587 (TLS)** pour une sécurité optimale.

2. **SSH (Port 22) :**

- Par défaut, SSH utilise le port 22. Si vous modifiez ce port dans /etc/ssh/sshd_config, assurez-vous d'ouvrir le nouveau port dans UFW, sinon vous perdrez l'accès à distance.

3. **CService-Web :**

- Si vous n'installez pas CService-Web, certains ports (comme 4400) peuvent ne pas être nécessaires.

## Étapes de configuration avec UFW

## 1. Ouvrir les ports :

```bash
sudo ufw allow 22/tcp       # SSH
sudo ufw allow 80/tcp       # HTTP
sudo ufw allow 443/tcp      # HTTPS
sudo ufw allow 53/tcp      # DNS
sudo ufw allow 53/udp      # DNS
sudo ufw allow 113/tcp      # Ident
sudo ufw allow 465/tcp      # Secure SMTP
sudo ufw allow 587/tcp      # TLS SMTP
sudo ufw allow 110/tcp      # POP3
sudo ufw allow 6667:7000/tcp # Plage de ports
sudo ufw allow 6667:7000/udp
sudo ufw allow 4400/tcp     # Port personnalisé
```

## 2. Vérifier les règles :

```bash
sudo ufw status verbose
```

## 3. Activer le pare-feu (une fois tous les ports ouverts) :

```bash
sudo ufw enable
```

## Dépannage

- Erreur de connexion SSH ? Vérifiez que le port SSH est bien ouvert et correspond à celui configuré dans /etc/ssh/sshd_config. Courage !
- Problème avec les emails ? Assurez-vous que les ports 465 ou 587 sont ouverts si vous utilisez SMTP sécurisé.

## ❓ Besoin d'aide ?

Si vous avez des questions ou des doutes sur la configuration, n'hésitez pas à demander avant d'activer le pare-feu.

# 3. Installation de GnuWorld

**Création du nouvelle utilisateur afin de jail le service (Spécifier le mot de passe souhaiter)**

```bash
root@mail:~# adduser gnuworld
```

Entrer dans l'utilisateur nouvellement crée

```bash
root@mail:~# su - gnuworld
```

Installer le tar du repo github de GnuWorld

```bash
gnuworld@ircd:~# wget https://raw.githubusercontent.com/SpacyXyt/installation-gnuworld/refs/heads/Release/gnuworld-2025.zip
```

Extraire le tar installer depuis le repo

```bash
gnuworld@mail:~# unzip gnuworld-2025.zip
```

Acceder au dossier de GnuWorld

```bash
gnuworld@mail:~# cd gnuworld
```

Passer le binaire 'configure' en executable

```bash
gnuworld@mail:~# chmod +x configure
```

Configuration du projet GnuWorld pour la compilation

```bash
gnuworld@mail:~# ./configure --enable-modules=ccontrol,cservice,openchanfix --with-pgsql-home=/usr/local/pgsql --with-extra-includes=/usr/include/postgresql/
```

## 3.1 Installation de Postgresql

Avant de continuer, GnuWorld necessite une base de donnée Postgresql pour fonctionner nous allons alors l'installer.

### **⚠️Revenir en sudo avant de continuer**

Installer postgresql

```bash
root@mail:~# apt install postgresql
```

Une fois fait le lancer

```bash
root@mail:~# systemctl start postgresql
```

## 3.2 Compilation de GnuWorld

Compilation du projet

```bash
gnuworld@mail:~# make
```

Installation du projet nouvellement compiler

```bash
gnuworld@mail:~# make install
```

## 3.3 Création des bases de données

**NOTE**: Maintenant nous allons crée le SQL et importer les fichiers sql dans la DB!

**NOTE**: Dans cette étape nous serons toujours dans le dossier: /home/gnuworld/gnuworld maintenant nous allons rentrer dans le dossier DOC.

Revenir a la racine de l'utilisateur

```bash
gnuworld@mail:/gnuworld$ cd
```

Rentrer dans le dossier gnuworld/doc (ou ./gnuworld/doc)

```bash
gnuworld@mail:/gnuworld$ cd gnuworld/doc
```

Passer le script 'do-db.sh' en executable

```bash
gnuworld@mail:/gnuworld/doc$ chmod +x do-db.sh
```

Vous pouvez maintenant l'executer

```bash
gnuworld@mail:/gnuworld/doc$ ./do-db.sh
```

Une fois le script fini revenir dans le gnuworld

```bash
gnuworld@mail:/gnuworld/doc$ cd ..
```

## 5. Configuration de Ircd

### **_Nous allons avant de configurer le serveur gnuworld devoir ajouter une section dans le fichier ircd.conf_**

**_⚠️ Pour continuer dans ce tuto verifier que vous avez bien suivit le tuto: [Installation-Serveur-IRCU2](https://github.com/SpacyXyt/Installation-Serveur-IRCU2)_**

Retourner en root

```bash
root@ircd:~$
```

Passer en utilisateur ircd

```bash
ircd@ircd:~$ su - ircd
```

Aller dans le repertoire contenant le ircd (lib)

```bash
ircd@ircd:~$ cd ircd/lib/
```

Ouvrir alors le fichier de config ircd.conf

```bash
ircd@ircd:~$ nano ircd.conf
```

Grace au raccourci ctrl + w, rechercher alors '[crule]' juste au dessus de la ligne '# [crule]' ajouter:

```bash
Connect {
 name = "service.[pays].votre.domaine"; # example: service.frankfurt.irc.devlynx.fr
 host = "votre.interface"; # Si vous avez un domaine renseignez le, sinon renseigner votre ip publique ex: irc.devlynx.fr |or| 8.8.8.8
 password = "un_mot_de_passe_sur"; # Ici renseigner un mot de passe solide, sinon n'importe qui pourra connecter son serveur à votre service
 port = 4400; # Ici renseigner un port unique pas encore utiliser par un autre service ou une autre section 'connect'
 class = "Server";
 hub;
};
```

## 5.1 Relancer le serveur irc

**Une fois le serveur avec la nouvelle config, il faut le relancer.**

### 5.1.1. Arreter le serveur qui tourne

Trouver le pid du serveur ircd

```bash
ircd@mail:~$ ps x
```

Vous verrez alors:

```bash
ircd@mail:~$ ps x
PID TTY      STAT   TIME COMMAND
368324 ?        Ss     0:10 /home/ircd/ircd/bin/ircd -f /home/ircd/ircd/lib/ircd.conf
```

Vous pouvez alors kill le process:

⚠️ Le PID est a changer !

```bash
ircd@mail:~$ kill 368324
```

### 5.1.2. Relancer le serveur

Retourner dans le dossier bin

```bash
ircd@mail:~$ cd ircd/bin/
```

Lancer le serveur

```bash
ircd@mail:~/ircd/bin$ ./ircd -f ../lib/ircd.conf
```

## 6. Configuration de Gnuworld

**_Nous allons maintenant editer la config de GnuWorld (Seul les parametres essentiellle seront vu)_**

Entrer dans le dossier 'bin' du projet

```bash
gnuworld@ircd:/gnuworld$ cd bin
```

Ouvrir le premier fichier config de Gnuworld ('GNUWorld.conf')

**_Vous pouvez l'ouvrire avec l'editeur de texte de votre choix (un tuto sur nano et vim arrive bientôt)_**

```bash
gnuworld@ircd:/gnuworld/bin$ nano GNUWorld.conf
```

Vous verrez alors les parametres suivant:

```bash
uplink = 192.168.181.111
port = 4400
password = 54321
name = services.undernet.org
description = UnderNet Services
numeric = 51
```

`uplink`: Ici vous renseignerez l'ip de votre serveur sur lequel vous avez installer le serveur ircu du tuto: [Installation Serveur IRCU2](https://github.com/SpacyXyt/Installation-Serveur-IRCU2).

**_NOTE: Si vous renseignez l'ip publique penser bien à autoriser le port dans le firewall (cours sur le firewall bientôt)_**

`port`: Ici vous renseignerez le port configurer precedement dans le ircd.conf

`password`: Ici vous renseignerez le mot de passe définit precedement dans le ircd.conf

`name`: Ici vous renseignerez le nom que portera votre serveur gnuworld sur le réseau irc

**_⚠️ Attention celui-ci doit etre unique deux serveur ne peuvent pas avoir le meme nom sur un même réseau irc, sinon vous aurez un conflit lors du lancement de celui-ci.._**

`description`: Une description que portera votre serveur

`numeric`: Le champ numeric est un nombre decimal compris entre 1 et 1023.

**⚠️ Il doit etre unique sur le réseau !**

# 7. Lancement du serveur GnuWorld

**Maintenant que le serveur est configurer nous allons le lancer**

Lancer le serveur
```bash
gnuworld@ircd:/gnuworld/bin$ ./gnuworld -f GNUWorld.conf -c
```

# 8. Pour finir
## Note personnelle
Cette documentation est une documentation non officielle
