🔒 Sécurisation SSH

Guide complet pour sécuriser l’accès SSH sur un serveur Linux : utilisation de clés SSH, désactivation de la connexion root et des mots de passe, et durcissement avancé.

📌 Sommaire

Prérequis

1️⃣ Génération d’une paire de clés SSH

2️⃣ Gestion des permissions du dossier .ssh

3️⃣ Test de la connexion SSH

4️⃣ Création d’un nouvel utilisateur avec accès SSH

5️⃣ Modification de la configuration SSH

6️⃣ Vérification de la désactivation des mots de passe

7️⃣ Durcissement supplémentaire (optionnel)

8️⃣ Vérification finale

⚡ Prérequis

Serveur Linux avec ufw installé.

Accès root ou sudo.

Port SSH ouvert :

<details> <summary>Voir la commande</summary>
sudo ufw allow 22/tcp
</details>
1️⃣ Génération d’une paire de clés SSH
<details> <summary>Instructions</summary>

Sur votre machine locale :

ssh-keygen -t ed25519 -C "admin@monserveur"

Copier la clé publique vers le serveur :

ssh-copy-id -i ~/.ssh/id_ed25519.pub root@<IP_DU_SERVEUR>
</details>
2️⃣ Gestion des permissions du dossier .ssh
<details> <summary>Instructions</summary>

Sur le serveur, vérifiez et corrigez les permissions :

ls -ld ~/.ssh
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/id_ed25519
</details>
3️⃣ Test de la connexion SSH
<details> <summary>Instructions</summary>
ssh -o PubkeyAuthentication=yes root@<IP_DU_SERVEUR>

La connexion doit fonctionner uniquement avec la clé publique.

</details>
4️⃣ Création d’un nouvel utilisateur avec accès SSH
<details> <summary>Instructions</summary>

Créez un nouvel utilisateur et ajoutez-le au groupe sudo :

sudo adduser admin
sudo usermod -aG sudo admin

Copiez la clé publique vers le nouvel utilisateur :

sudo mkdir -p /home/admin/.ssh
sudo touch /home/admin/.ssh/authorized_keys
sudo cp ~/.ssh/authorized_keys /home/admin/.ssh/authorized_keys
sudo chown -R admin:admin /home/admin/.ssh
sudo chmod 700 /home/admin/.ssh
sudo chmod 600 /home/admin/.ssh/authorized_keys
</details>
5️⃣ Modification de la configuration SSH
<details> <summary>Instructions</summary>

Éditez le fichier SSH :

sudo nano /etc/ssh/sshd_config

Exemple de configuration recommandée :

PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AllowUsers admin

Redémarrez le service SSH :

sudo systemctl restart ssh
</details>
6️⃣ Vérification de la désactivation des mots de passe
<details> <summary>Instructions</summary>
ssh -o PubkeyAuthentication=no admin@<IP_DU_SERVEUR>
# Devrait renvoyer : Permission denied (publickey).
</details>
7️⃣ Durcissement supplémentaire (optionnel)
<details> <summary>Instructions</summary>

Dans /etc/ssh/sshd_config :

# Limitation des tentatives de connexion
MaxAuthTries 3
LoginGraceTime 30

# Changer le port SSH
Port 2222

# Timeout pour les connexions inactives
ClientAliveInterval 300
ClientAliveCountMax 0

# Désactiver les méthodes inutiles
ChallengeResponseAuthentication no
UsePAM no
X11Forwarding no
AllowTcpForwarding no

Mettre à jour le pare-feu si le port change :

sudo ufw allow 2222/tcp

Redémarrez le serveur pour appliquer les modifications :

sudo reboot
</details>
8️⃣ Vérification finale
<details> <summary>Instructions</summary>

Vérifiez la syntaxe du fichier de configuration SSH avant de redémarrer :

sudo sshd -t
sudo systemctl restart ssh
</details>
