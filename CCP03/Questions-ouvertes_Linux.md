# 🐧 QUESTIONS OUVERTES LINUX - 20 QUESTIONS
## Remobilisation mémoire avec réponses modèles

> **Pour l'examen** : Ces questions type examen écrit nécessitent des réponses structurées et détaillées.  
> **Méthode** : Réponds SANS regarder les corrections, puis compare avec les réponses modèles.

---

## 📋 MODE D'EMPLOI

1. **Lis la question**
2. **Note ta réponse complète** (3-10 lignes selon la question)
3. **Ne regarde PAS la correction**
4. **Quand tu as fini les 20**, compare avec les réponses modèles
5. **Identifie tes lacunes** et révise les fiches correspondantes

---

## ❓ QUESTIONS 1-20

### Question 1 - Utilisateurs et groupes

Expliquez la différence entre `useradd` et `adduser`. Laquelle est recommandée et pourquoi ?

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 2 - Permissions fichiers

Un fichier a les permissions suivantes : `-rw-r-----`  
Qui peut lire ce fichier ? Qui peut l'écrire ? Qui peut l'exécuter ? Justifiez.

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 3 - Commande chmod

Quelle commande utiliseriez-vous pour donner les droits suivants :
- Propriétaire : lecture, écriture, exécution
- Groupe : lecture, exécution
- Autres : aucun droit

Donnez la commande en mode octal ET symbolique.

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 4 - UMASK

Vous avez un umask de `0022`. Quelles seront les permissions par défaut d'un nouveau fichier ? D'un nouveau répertoire ? Expliquez le calcul.

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 5 - SUID/SGID/Sticky Bit

Expliquez le rôle du **SUID**, du **SGID** et du **Sticky Bit**. Donnez un exemple d'utilisation pour chacun.

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 6 - Commande find

Un utilisateur a besoin de trouver tous les fichiers `.log` modifiés dans les dernières 24 heures dans `/var/log`. Quelle commande proposez-vous ?

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 7 - Montage disque

Expliquez les étapes pour monter **de façon permanente** une nouvelle partition `/dev/sdb1` sur `/data` au démarrage du système.

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 8 - LVM

Quels sont les 3 composants principaux de LVM ? Expliquez brièvement le rôle de chacun.

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 9 - Gestion paquets Debian

Quelle est la différence entre `apt update` et `apt upgrade` ? Dans quel ordre doit-on les exécuter ?

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 10 - SSH sécurisé

Vous devez sécuriser un serveur SSH. Citez au moins 4 bonnes pratiques à appliquer dans le fichier `/etc/ssh/sshd_config`.

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 11 - Cron

Un utilisateur veut exécuter un script de backup tous les jours à 3h du matin. Quelle ligne ajouter dans sa crontab ?

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 12 - Systemd

Quelles commandes utilisez-vous pour :
a) Démarrer le service Apache
b) L'activer au démarrage
c) Vérifier son état
d) Voir ses logs

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 13 - Réseau - Configuration IP

Comment configurer **de façon permanente** l'adresse IP `192.168.1.100/24` avec la passerelle `192.168.1.1` sur Debian 11 ?

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 14 - Commande netstat

Un service web ne répond plus. Comment vérifier si le port 80 est bien en écoute sur le serveur ?

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 15 - Redirections

Expliquez la différence entre :
- `commande > fichier`
- `commande >> fichier`
- `commande 2> fichier`
- `commande > fichier 2>&1`

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 16 - Processus

Un processus consomme 100% du CPU. Comment l'identifier ? Comment le terminer proprement ? Et si ça ne fonctionne pas, que faire ?

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 17 - Fichiers de logs

Dans quel fichier de log regarderiez-vous pour :
a) Les tentatives de connexion SSH
b) Les messages du noyau
c) Les authentifications système

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 18 - Partitionnement

Expliquez la différence entre une partition **primaire**, une partition **étendue** et une partition **logique** dans un schéma MBR.

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 19 - Quotas disque

Un utilisateur `jdupont` doit être limité à 5 Go d'espace disque. Quelles sont les étapes principales pour mettre en place des quotas utilisateur ?

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

---

### Question 20 - Dépannage boot

Un serveur Linux ne démarre plus. Vous avez accès au mode rescue/recovery. Quelles sont les 3 premières vérifications à effectuer ?

**Ma réponse** :
```
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
____________________________________________________________________________
```

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

