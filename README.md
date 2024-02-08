# Génération de métriques réseau au sein d'une VM Freebox

Ce repo contient des informations de base sur l'utilisation des VM dans la Freebox Delta.

## Création d'une clé d'authentification

- Sous Windows 10, on peut générer une clé RSA avec la commande `ssh-keygen -t rsa -b 4096`.
- On obtient alors deux fichiers :
  - `SSHKey` est la clé privée, que vous ne divulguerez jamais ;
  - `SSHKey.pub` est la clé publique, que vous pouvez communiquer à des tiers pour qu'ils encyptent une information que vous seul pourrez décrypter.

## Création de la VM sur le site intégré à la Freebox

- Dans la clé SSH sur la Freebox à la création de la VM, je colle le contenu du fichier SSHKey.pub (pas la deuxième ligne qui est vide)
- Nom de la VM : `VinceAttempt4`
- Pour s'y connecter : `ssh -i "C:\BackedUp\DocsBV\Synchronized\SkyDrive\Vincent\Clé SSH\SSHKey" vince@192.168.1.128`

## Installation du CLI de SpeedTest

- [CLI de SpeedTest](https://www.speedtest.net/fr/apps/cli)

## Installation d'un client samba : pas sûr de l'utilité

`sudo apt install smbclient`

## Montage d'un répertoire de la Freebox dans la VM

- On utilise CIFS, pour l'installer : `sudo apt install cifs-utils`
- Free ne supporte que SMBv1.
- `sudo mkdir -p /mnt/freebox`
- `sudo mount -t cifs -o guest,vers=1.0,uid=1000,gid=1000 //192.168.1.254/Freebox /mnt/freebox`
- Chemin réseau sur intranet : `\\freebox\Freebox\VMs`

## Faire tourner SpeedTest avec envoi de la sortie dans le point de montage

Une première fois pour générer avec la ligne de titres de colonnes
`speedtest --format=csv --output-header >> /mnt/freebox/VMs/speedtest.csv`
Puis modifier ce fichier à la main pour insérer dans la première ligne une colonne "date", et supprimer la deuxième ligne

## Faire tourner SpeedTest régulièrement

- Installation de Cron: `apt-get install cron`
- pour éditer le crontab: `crontab -e`
- Executer speedtest toutes les 5 minutes, mettre ça dans le crontab : `*/5 * * * * echo $(date '+\%Y-\%m-\%d \%H:\%M:\%S'),"$(speedtest --server-id=26387 --format=csv)" >> /mnt/freebox/VMs/speedtest.csv`
- Il m'est arrivé de perdre le montage du répertoire, donc je mets aussi ça dans le crontab : `*/5 * * * * sudo mount -t cifs -o guest,vers=1.0,uid=1000,gid=1000 //192.168.1.254/Freebox /mnt/freebox`
- Si besoin de débugger les exécutions de cron, ajouter ça au début du fichier crontab : `MAILTO=mahonv@gmail.com` (semble ne pas marcher)
