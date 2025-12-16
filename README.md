# Installation et Configuration GnuWorld
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

| Port | Protocole | Utilisation |
|-|-| - |
| 6667 - 7000 | TCP/UDP | Plage de ports pour les services spécifiques (ex : IRC, jeux, etc.). |
| 4400 | TCP/UDP | Port personnalisé (à adapter selon votre configuration) |
| 113 | TCP | Service Ident (utilisé pour l'authentification sur certains protocoles). |
| 53 | TCP/UDP | DNS (résolution de noms de domaine). |
| 443 | TCP | HTTPS (connexions sécurisées pour les sites web). |
| 80 | TCP | HTTP (trafic web non sécurisé). |
| 22 | TCP | SSH (accès sécurisé à distance). À adapter si modifié dans /etc/ssh/sshd_config. |
| 25 | TCP | SMTP (envoi d'emails non sécurisé). À remplacer par 465 ou 587 si possible. |
| 110 | TCP | POP3 (réception d'emails non sécurisé). |
| 465 | TCP | Secure SMTP (SMTP sécurisé avec SSL). |
| 587 | TCP | TLS SMTP (SMTP sécurisé avec TLS). |

**Remarques importantes**

1. **SMTP sécurisé :**
-  Si vous utilisez **Secure SSL SMTP** ou **TLS SMTP**, vous n'avez **pas besoin** d'ouvrir le port 25 (SMTP non sécurisé).
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
- Erreur de connexion SSH ? Vérifiez que le port SSH est bien ouvert et correspond à celui configuré dans /etc/ssh/sshd_config.
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
gnuworld@mail:~# unzip gnuworld-2025.zip -d ./gnuworld
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

## ⚠️ Avant de continuer

## Compilation de GnuWorld

Compilation du projet 

```bash
gnuworld@mail:~# make
```

Installation du projet nouvellement compiler

```bash
gnuworld@mail:~# make install
```

**NOTE**: Maintenant nous allons crée le SQL et importer les fichiers sql dans la DB!

**NOTE**: Dans cette étape nous serons toujours dans le dossier: /home/gnuworld/gnuworld maintenant nous allons rentrer dans le dossier DOC.

```bash
gnuworld@ircd:/gnuworld$ cd doc/ 
```

```bash
gnuworld@ircd:/gnuworld/doc$ <now we are here.
```

```bash
gnuworld@ircd:/gnuworld/doc$ chmod +x do-db.sh 
```

```bash
gnuworld@ircd:/gnuworld/doc$ ./do-db.sh 
```

```bash
gnuworld@ircd:/gnuworld/doc$ cd .. 
```

```bash
gnuworld@ircd:/gnuworld$/
```

