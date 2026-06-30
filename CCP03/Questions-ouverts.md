# 🐧 CORRECTIONS - QUESTIONS OUVERTES LINUX
## Remobilisation mémoire avec réponses modèles

> **Réponses modèles détaillées** pour les 20 questions Linux

---

## ✔️ Question 1 - useradd vs adduser

**Réponse modèle** :

**useradd** :
- Commande **bas niveau**, générique Unix/Linux
- Nécessite beaucoup d'options manuelles (`-m`, `-s`, `-G`, etc.)
- Ne crée PAS automatiquement le home ni ne demande le mot de passe

**adduser** :
- Script **interactif** Debian/Ubuntu
- Crée automatiquement le home, copie les fichiers de `/etc/skel`
- Demande le mot de passe et des informations (nom complet, etc.)
- **RECOMMANDÉE** car plus simple et conviviale

**Exemple** :
```bash
useradd -m -s /bin/bash -G sudo jdupont  # Nécessite ensuite passwd jdupont
adduser jdupont                           # Tout en un, interactif
```

---

### ✔️ Question 2 - Permissions `-rw-r-----`

**Réponse modèle** :

**Décomposition** :
- Propriétaire (User) : `rw-` → lecture + écriture
- Groupe (Group) : `r--` → lecture seule
- Autres (Others) : `---` → aucun droit

**Qui peut lire ?** Le propriétaire ET les membres du groupe  
**Qui peut écrire ?** Le propriétaire UNIQUEMENT  
**Qui peut exécuter ?** PERSONNE  

**Exemple** :
- Si le fichier appartient à `root:admin`
- `root` peut lire et écrire
- Les membres du groupe `admin` peuvent lire
- Les autres utilisateurs n'ont aucun accès

---

### ✔️ Question 3 - chmod rwxr-x---

**Réponse modèle** :

**Mode octal** :
```bash
chmod 750 fichier
```
- Calcul : rwx (7) + r-x (5) + --- (0) = **750**

**Mode symbolique** :
```bash
chmod u=rwx,g=rx,o= fichier
```
ou
```bash
chmod u+rwx,g+rx,o-rwx fichier
```

---

### ✔️ Question 4 - UMASK 0022

**Réponse modèle** :

**Principe** : umask **retire** des permissions par défaut

**Fichier** :
- Permissions max : `666` (rw-rw-rw-)
- umask : `022`
- Résultat : `666 - 022 = 644` → **rw-r--r--**

**Répertoire** :
- Permissions max : `777` (rwxrwxrwx)
- umask : `022`
- Résultat : `777 - 022 = 755` → **rwxr-xr-x**

**Conclusion** : Avec umask `0022`, les fichiers sont créés en `644` et les répertoires en `755`.

---

### ✔️ Question 5 - SUID/SGID/Sticky Bit

**Réponse modèle** :

**SUID (Set User ID)** - Bit 4000 :
- Le fichier s'exécute avec les droits du **propriétaire** (pas de l'utilisateur qui lance)
- **Exemple** : `/usr/bin/passwd` (SUID root) → un utilisateur peut changer son mot de passe

**SGID (Set Group ID)** - Bit 2000 :
- Sur fichier : s'exécute avec les droits du **groupe** propriétaire
- Sur répertoire : les nouveaux fichiers héritent du **groupe** du répertoire
- **Exemple** : Répertoire partagé `/projets` → tous les fichiers créés appartiennent au groupe `projets`

**Sticky Bit** - Bit 1000 :
- Sur répertoire : seul le propriétaire du fichier peut le supprimer (même si droits en écriture)
- **Exemple** : `/tmp` (sticky bit) → chacun peut créer, mais seul le propriétaire peut supprimer ses fichiers

**Notation** :
```bash
chmod 4755 fichier  # SUID
chmod 2755 dossier  # SGID
chmod 1777 /tmp     # Sticky bit
```

---

### ✔️ Question 6 - find fichiers .log

**Réponse modèle** :

```bash
find /var/log -name "*.log" -mtime -1
```

**Explication** :
- `/var/log` : répertoire de recherche
- `-name "*.log"` : fichiers se terminant par .log
- `-mtime -1` : modifiés dans les dernières 24 heures

**Variantes** :
```bash
find /var/log -name "*.log" -mtime -1 -type f      # Uniquement fichiers (pas répertoires)
find /var/log -name "*.log" -mmin -60              # Dernières 60 minutes
```

---

### ✔️ Question 7 - Montage permanent

**Réponse modèle** :

**Étapes** :

1. **Créer le point de montage** :
```bash
mkdir /data
```

2. **Formater la partition** (si nécessaire) :
```bash
mkfs.ext4 /dev/sdb1
```

3. **Identifier l'UUID** :
```bash
blkid /dev/sdb1
# Copier l'UUID
```

4. **Éditer `/etc/fstab`** :
```bash
nano /etc/fstab
```

5. **Ajouter la ligne** :
```
UUID=xxx-xxx-xxx-xxx  /data  ext4  defaults  0  2
```

6. **Tester le montage** :
```bash
mount -a          # Monte tout selon fstab
df -h             # Vérifier
```

**Note** : Utiliser l'UUID plutôt que `/dev/sdb1` pour éviter les problèmes si l'ordre des disques change.

---

### ✔️ Question 8 - LVM composants

**Réponse modèle** :

Les 3 composants principaux de LVM :

1. **PV (Physical Volume)** :
   - Partition ou disque physique préparé pour LVM
   - Commande : `pvcreate /dev/sdb1`

2. **VG (Volume Group)** :
   - Groupe de volumes physiques (pool de stockage)
   - Commande : `vgcreate vg_data /dev/sdb1 /dev/sdc1`

3. **LV (Logical Volume)** :
   - Volume logique créé dans un VG (comme une partition)
   - Commande : `lvcreate -L 10G -n lv_projets vg_data`

**Avantages** : Flexibilité (redimensionnement à chaud, snapshots, migration)

---

### ✔️ Question 9 - apt update vs upgrade

**Réponse modèle** :

**apt update** :
- Met à jour la **liste des paquets disponibles** depuis les dépôts
- Télécharge les informations sur les nouvelles versions
- N'installe RIEN

**apt upgrade** :
- **Installe les mises à jour** des paquets déjà installés
- Nécessite que `apt update` ait été fait avant

**Ordre correct** :
```bash
apt update          # 1. Rafraîchir la liste
apt upgrade         # 2. Installer les mises à jour
```

**Variante** :
```bash
apt full-upgrade    # Peut supprimer des paquets si nécessaire
```

---

### ✔️ Question 10 - Sécuriser SSH

**Réponse modèle** :

Éditer `/etc/ssh/sshd_config` :

**4 bonnes pratiques** :

1. **Changer le port par défaut** :
```
Port 2222
```

2. **Interdire la connexion root** :
```
PermitRootLogin no
```

3. **Désactiver l'authentification par mot de passe** (forcer clés SSH) :
```
PasswordAuthentication no
PubkeyAuthentication yes
```

4. **Limiter les utilisateurs autorisés** :
```
AllowUsers admin jdupont
```

**Autres bonnes pratiques** :
- `Protocol 2` (forcer SSHv2)
- `MaxAuthTries 3`
- `ClientAliveInterval 300` (timeout inactivité)

**Appliquer** :
```bash
systemctl restart sshd
```

---

### ✔️ Question 11 - Crontab backup 3h

**Réponse modèle** :

```bash
0 3 * * * /chemin/vers/script_backup.sh
```

**Explication du format cron** :
```
* * * * * commande
│ │ │ │ │
│ │ │ │ └─ Jour de la semaine (0-7, 0 et 7 = dimanche)
│ │ │ └─── Mois (1-12)
│ │ └───── Jour du mois (1-31)
│ └─────── Heure (0-23)
└───────── Minute (0-59)
```

**Pour éditer la crontab** :
```bash
crontab -e          # Éditer la crontab de l'utilisateur
crontab -l          # Lister les tâches
```

---

### ✔️ Question 12 - Systemd commandes

**Réponse modèle** :

a) **Démarrer Apache** :
```bash
systemctl start apache2
```

b) **Activer au démarrage** :
```bash
systemctl enable apache2
```

c) **Vérifier l'état** :
```bash
systemctl status apache2
```

d) **Voir les logs** :
```bash
journalctl -u apache2 -f        # -f = suivi en temps réel
journalctl -u apache2 --since "10 min ago"
```

**Autres commandes utiles** :
```bash
systemctl stop apache2          # Arrêter
systemctl restart apache2       # Redémarrer
systemctl reload apache2        # Recharger la config
systemctl disable apache2       # Désactiver au démarrage
```

---

### ✔️ Question 13 - Configuration IP permanente Debian

**Réponse modèle** :

**Sur Debian 11**, éditer `/etc/network/interfaces` :

```bash
nano /etc/network/interfaces
```

**Ajouter** :
```
auto ens18
iface ens18 inet static
    address 192.168.1.100/24
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8 1.1.1.1
```

**Redémarrer le réseau** :
```bash
systemctl restart networking
```

**Vérifier** :
```bash
ip addr show
ip route
```

**Alternative avec netplan (Ubuntu)** :
Éditer `/etc/netplan/00-installer-config.yaml`

---

### ✔️ Question 14 - Vérifier port 80

**Réponse modèle** :

**Méthodes** :

**1. Avec netstat** :
```bash
netstat -tulnp | grep :80
```

**2. Avec ss (recommandé)** :
```bash
ss -tulnp | grep :80
```

**3. Avec lsof** :
```bash
lsof -i :80
```

**Explication des options** :
- `-t` : Afficher les connexions TCP
- `-u` : Afficher les connexions UDP
- `-l` : Afficher uniquement les sockets en écoute (listening)
- `-n` : Afficher les adresses numériques (pas de résolution DNS)
- `-p` : Afficher le PID/nom du processus

**Résultat attendu** :
```
tcp  0  0  0.0.0.0:80  0.0.0.0:*  LISTEN  1234/apache2
```

---

### ✔️ Question 15 - Redirections

**Réponse modèle** :

**`commande > fichier`** :
- Redirige la **sortie standard (stdout)** vers le fichier
- **Écrase** le fichier s'il existe

**`commande >> fichier`** :
- Redirige la **sortie standard** vers le fichier
- **Ajoute** à la fin du fichier (append)

**`commande 2> fichier`** :
- Redirige la **sortie d'erreur (stderr)** vers le fichier
- Le `2` représente le descripteur de fichier stderr

**`commande > fichier 2>&1`** :
- Redirige **stdout ET stderr** vers le même fichier
- `2>&1` signifie "redirige stderr (2) vers stdout (1)"

**Exemples** :
```bash
ls > liste.txt                    # Sortie normale uniquement
ls >> liste.txt                   # Ajoute à la fin
ls /inexistant 2> erreurs.log     # Erreurs uniquement
ls /inexistant > all.log 2>&1     # Tout dans le même fichier
```

---

### ✔️ Question 16 - Processus qui consomme 100% CPU

**Réponse modèle** :

**1. Identifier le processus** :
```bash
top                 # Voir les processus en temps réel
htop                # Version améliorée (si installé)
ps aux --sort=-%cpu | head
```

**2. Terminer proprement (SIGTERM)** :
```bash
kill <PID>          # Signal 15 (TERM) par défaut
kill -15 <PID>      # Équivalent explicite
```

**3. Si ça ne fonctionne pas, forcer (SIGKILL)** :
```bash
kill -9 <PID>       # Signal 9 (KILL) - force l'arrêt immédiat
```

**Différence** :
- `kill` (SIGTERM 15) : Demande au processus de se terminer proprement
- `kill -9` (SIGKILL) : Force l'arrêt immédiat (pas de nettoyage)

**Par nom de processus** :
```bash
pkill nom_processus       # SIGTERM
pkill -9 nom_processus    # SIGKILL
killall nom_processus     # Tue tous les processus avec ce nom
```

---

### ✔️ Question 17 - Fichiers de logs

**Réponse modèle** :

a) **Tentatives de connexion SSH** :
```
/var/log/auth.log       # Debian/Ubuntu
/var/log/secure         # RedHat/CentOS
```

b) **Messages du noyau** :
```
/var/log/kern.log       # Noyau uniquement
/var/log/dmesg          # Messages boot du noyau
dmesg                   # Commande pour voir les messages
```

c) **Authentifications système** :
```
/var/log/auth.log       # Toutes les authentifications
```

**Autres logs importants** :
- `/var/log/syslog` : Logs système généraux
- `/var/log/messages` : Messages système (RedHat)
- `/var/log/apache2/` : Logs Apache
- `journalctl` : Logs systemd

---

### ✔️ Question 18 - Partitions MBR

**Réponse modèle** :

**Partition Primaire** :
- Directement accessible au système
- **Maximum 4 partitions primaires** sur un disque MBR
- Peut être bootable

**Partition Étendue** :
- Type spécial de partition primaire
- Ne contient PAS de données
- **Conteneur** pour les partitions logiques
- **Maximum 1 partition étendue** par disque
- Permet de contourner la limite de 4 partitions

**Partition Logique** :
- Créée **à l'intérieur** d'une partition étendue
- Nombre **illimité** (en pratique limité à 12-15 sur Linux)
- Utilisée pour stocker des données

**Exemple schéma MBR** :
```
/dev/sda1  → Primaire (bootable)
/dev/sda2  → Primaire
/dev/sda3  → Primaire
/dev/sda4  → Étendue
    /dev/sda5  → Logique
    /dev/sda6  → Logique
    /dev/sda7  → Logique
```

**Note** : GPT (GUID Partition Table) remplace MBR et supporte 128 partitions primaires.

---

### ✔️ Question 19 - Quotas disque

**Réponse modèle** :

**Étapes principales** :

1. **Installer les outils** :
```bash
apt install quota
```

2. **Activer les quotas dans `/etc/fstab`** :
```
/dev/sda1  /home  ext4  defaults,usrquota,grpquota  0  2
```

3. **Remonter la partition** :
```bash
mount -o remount /home
```

4. **Créer les fichiers de quotas** :
```bash
quotacheck -cum /home
```

5. **Activer les quotas** :
```bash
quotaon /home
```

6. **Définir le quota pour jdupont** :
```bash
edquota -u jdupont
```
Mettre : soft limit = 5000000 (5 Go en Ko), hard limit = 5242880

7. **Vérifier** :
```bash
quota -u jdupont
repquota /home
```

---

### ✔️ Question 20 - Dépannage boot

**Réponse modèle** :

**3 premières vérifications en mode rescue** :

**1. Vérifier les partitions et `/etc/fstab`** :
```bash
fdisk -l                    # Lister les partitions
mount /dev/sda1 /mnt        # Monter la partition root
cat /mnt/etc/fstab          # Vérifier fstab
```
Problème fréquent : Erreur dans fstab (UUID incorrect, partition inexistante)

**2. Vérifier GRUB** :
```bash
ls /mnt/boot/grub           # Vérifier que GRUB existe
grub-install /dev/sda       # Réinstaller GRUB si besoin
update-grub                 # Mettre à jour la config
```
Problème fréquent : GRUB corrompu ou mal configuré

**3. Vérifier les logs** :
```bash
journalctl -xb -1           # Logs du boot précédent
dmesg | tail -50            # Messages du noyau
cat /mnt/var/log/syslog | tail -100
```
Rechercher les messages d'erreur (kernel panic, failed to mount, etc.)

**Autres vérifications** :
- Espace disque plein (`df -h`)
- Problème de droits sur `/` ou `/boot`
- Noyau corrompu

---

## 📊 AUTO-ÉVALUATION

**Nombre de réponses correctes** : _____ / 20

| Score | Niveau |
|-------|--------|
| 18-20 | ⭐⭐⭐ Excellent - Maîtrise complète |
| 15-17 | ⭐⭐ Très bien - Quelques détails à revoir |
| 12-14 | ⭐ Bien - Révise les points faibles |
| 8-11 | ⚠️ Moyen - Révision approfondie nécessaire |
| 0-7 | 🔴 Insuffisant - Relis la fiche Linux |

---

## 🎯 THÉMATIQUES À REVOIR

Si tu as des erreurs sur ces questions, révise les thèmes :

- **Q1, Q2, Q3** → Fiche Linux : Gestion utilisateurs et permissions
- **Q4, Q5** → Fiche Linux : Permissions avancées
- **Q6** → Fiche Linux : Recherche de fichiers
- **Q7, Q8, Q18** → Fiche Linux : Stockage et partitionnement
- **Q9** → Fiche Linux : Gestion des paquets
- **Q10, Q13, Q14** → Fiche Linux : Réseau et SSH
- **Q11, Q12** → Fiche Linux : Services et automatisation
- **Q15** → Fiche Linux : Redirections shell
- **Q16, Q17** → Fiche Linux : Processus et logs
- **Q19, Q20** → Fiche Linux : Administration avancée

---

**💪 Continue avec les Questions Ouvertes Réseau !**
