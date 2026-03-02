Sécurité SSH
Prérequis

Prendre la machine sur laquelle était déjà installé UFW et s'assurer que le port SSH est ouvert :

ufw allow 22/tcp

Générer une paire de clés SSH

ssh-keygen -t ed25519 -C "admin@monserveur"

Copier la clé publique sur le serveur

# Remplacer par l'IP ou le nom de votre serveur
ssh-copy-id -i ~/.ssh/id_ed25519.pub root@10.0.0.100

Gérer les permissions du dossier .ssh

# Sur le serveur, vérifier les permissions du dossier .ssh
ls -ld ~/.ssh
# Si nécessaire, corriger les permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/id_ed25519

Tester la connexion SSH

ssh -o PubkeyAuthentication=yes root@10.0.0.100

Création d'un nouvel utilisateur avec accès SSH

# Sur le serveur, créer un nouvel utilisateur
sudo adduser admin
sudo usermod -aG sudo admin
# Copier la clé publique pour le nouvel utilisateur
sudo mkdir -p /home/admin/.ssh
sudo touch /home/admin/.ssh/authorized_keys
sudo cp ~/.ssh/authorized_keys /home/admin/.ssh/authorized_keys
sudo chown -R admin:admin /home/admin/.ssh
sudo chmod 700 /home/admin/.ssh
sudo chmod 600 /home/admin/.ssh/authorized_keys

Modifier la configuration SSH

# Sur le serveur, éditer le fichier de configuration SSH
sudo nano /etc/ssh/sshd_config

# Exemple de modifications recommandées :
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AllowUsers admin

# Redémarrer le service SSH pour appliquer les changements
sudo systemctl restart ssh

Vérifier que le mot de passe est désactivé

ssh -o PubkeyAuthentication=no admin@10.0.0.100
# Permission denied (publickey).

Durcissement supplémentaire (optionnel)

# Limiter les tentatives de connexion pour prévenir les attaques par force brute
MaxAuthTries 3
LoginGraceTime 30
# Changer le port
Port 2222
# Timeout pour les connexions inactives
ClientAliveInterval 300
ClientAliveCountMax 0
# Désactiver les méthodes inutiles
ChallengeResponseAuthentication no
UsePAM no
X11Forwarding no
AllowTcpForwarding no

## Ne pas oublier de mettre à jour les règles de pare-feu si vous changez le port SSH
ufw allow 2222/tcp

## Redémarrer le poste après les modifications (changement du port SSH, etc.)
reboot

Vérifier la configuration

# Vérifier la syntaxe du fichier de configuration SSH
sudo sshd -t

# Redémarrer le service SSH après les modifications
sudo systemctl restart ssh
