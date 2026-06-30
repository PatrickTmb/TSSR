# 💾 CORRECTIONS - QUESTIONS OUVERTES SAUVEGARDES
## RTO/RPO, PRA/PCA, Stratégies de backup

> **Réponses modèles détaillées** pour les 15 questions sur les sauvegardes

---

## ✅ Question 1 - Types de sauvegardes

**Réponse modèle** :

**Sauvegarde COMPLÈTE (Full Backup)** :
- **Principe** : Copie TOUS les fichiers sélectionnés
- **Marquage** : Réinitialise l'attribut "archive" (bit A = 0)
- **Avantages** :
  - Restauration rapide et simple (1 seule sauvegarde)
  - Indépendante (pas besoin d'autres sauvegardes)
- **Inconvénients** :
  - Temps de sauvegarde le plus long
  - Espace disque important
  - Bande passante réseau élevée
- **Usage** : 1 fois par semaine (dimanche)

**Sauvegarde DIFFÉRENTIELLE** :
- **Principe** : Copie les fichiers modifiés depuis la dernière **complète**
- **Marquage** : NE réinitialise PAS l'attribut archive
- **Avantages** :
  - Restauration moyenne (complète + 1 différentielle)
  - Temps de sauvegarde moyen
- **Inconvénients** :
  - Taille augmente chaque jour (cumulative)
  - Plus lent qu'incrémentale
- **Usage** : Lundi à samedi
- **Restauration** : Complète + dernière différentielle

**Sauvegarde INCRÉMENTALE** :
- **Principe** : Copie uniquement les fichiers modifiés depuis la **dernière sauvegarde** (complète ou incrémentale)
- **Marquage** : Réinitialise l'attribut archive
- **Avantages** :
  - Temps de sauvegarde le plus rapide
  - Espace disque minimal
- **Inconvénients** :
  - Restauration la plus longue (complète + toutes les incrémentales)
  - Dépendances (chaîne de sauvegardes)
- **Usage** : Quotidien
- **Restauration** : Complète + TOUTES les incrémentales dans l'ordre

**Tableau comparatif** :

| Type | Temps backup | Espace | Temps restauration | Dépendances |
|------|--------------|--------|-------------------|-------------|
| Complète | ⏱️⏱️⏱️ | 💾💾💾 | ⚡ | Aucune |
| Différentielle | ⏱️⏱️ | 💾💾 | ⚡⚡ | Complète |
| Incrémentale | ⏱️ | 💾 | ⚡⚡⚡ | Complète + toutes incrémentales |

---

### ✔️ Question 2 - RTO vs RPO

**Réponse modèle** :

**RTO (Recovery Time Objective)** :
- **Définition** : Durée **maximale d'interruption acceptable**
- **Question** : "Combien de temps peut-on rester sans le système ?"
- **Mesure** : En heures ou minutes
- **Impact** : Temps d'arrêt tolérable pour l'activité

**RPO (Recovery Point Objective)** :
- **Définition** : Quantité **maximale de données qu'on peut perdre**
- **Question** : "Combien de données peut-on perdre sans conséquences graves ?"
- **Mesure** : En heures ou minutes (perte de données)
- **Impact** : Âge de la dernière sauvegarde acceptable

**Exemple concret** :

**Scénario 1 - Site e-commerce** :
- **RTO = 1 heure** : Le site ne peut pas rester down plus d'1h (perte de chiffre d'affaires)
- **RPO = 15 minutes** : On peut perdre max 15 min de commandes
- **Solution** : Sauvegarde toutes les 15 min, infrastructure redondante, basculement automatique

**Scénario 2 - Base de données RH** :
- **RTO = 4 heures** : On peut travailler 4h sans le système RH
- **RPO = 24 heures** : On peut perdre 1 jour de données (saisie manuelle possible)
- **Solution** : Sauvegarde quotidienne, restauration manuelle acceptable

**Schéma** :
```
Incident ← [Perte de données = RPO] → Dernière sauvegarde
         ← [Temps d'interruption = RTO] → Système restauré
```

**Relation coût** :
- RTO/RPO courts → Coût élevé (redondance, automatisation)
- RTO/RPO longs → Coût faible (sauvegardes simples)

---

### ✔️ Question 3 - PRA vs PCA

**Réponse modèle** :

**PRA (Plan de Reprise d'Activité)** :
- **Objectif** : **APRÈS** un sinistre majeur, remettre en route le SI
- **Contexte** : Catastrophe (incendie, inondation, cyberattaque grave)
- **Actions** :
  - Restaurer les sauvegardes sur un site de secours
  - Réinstaller l'infrastructure
  - Redémarrer les services critiques
- **Durée** : Plusieurs heures à jours (selon RTO)
- **Mesure** : RTO et RPO
- **Exemple** : Datacenter détruit → Basculer sur site de secours

**PCA (Plan de Continuité d'Activité)** :
- **Objectif** : **ÉVITER** l'interruption du SI (ou minimiser)
- **Contexte** : Maintenir l'activité PENDANT le sinistre
- **Actions** :
  - Redondance (serveurs, liens réseau)
  - Basculement automatique (failover)
  - Procédures de contournement
- **Durée** : Quelques minutes maximum (automatique)
- **Mesure** : Disponibilité (99.9%, 99.99%)
- **Exemple** : Serveur tombe → Cluster bascule automatiquement sur serveur 2

**Différences clés** :

| Critère | PRA | PCA |
|---------|-----|-----|
| **Moment** | APRÈS sinistre | PENDANT sinistre |
| **Objectif** | Reprendre | Continuer |
| **Durée** | Heures/jours | Minutes |
| **Mode** | Réactif | Proactif |
| **Technologies** | Sauvegardes, site secours | Cluster, réplication |

**Quand utiliser** :
- **PCA** : Services critiques 24/7 (e-commerce, hôpital, banque)
- **PRA** : Services moins critiques (RTO de plusieurs heures acceptable)

**En pratique** : On combine les deux !
- PCA pour éviter les interruptions
- PRA si le PCA échoue (sinistre majeur)

---

### ✔️ Question 4 - Règle 3-2-1

**Réponse modèle** :

**Règle 3-2-1 des sauvegardes** :

**3** : Avoir au moins **3 copies** des données
- 1 copie de production (données originales)
- 2 sauvegardes distinctes

**2** : Sur au moins **2 supports différents**
- Exemples : Disque dur + bande magnétique
- Évite la défaillance d'un type de support

**1** : Au moins **1 copie hors site** (off-site)
- Protège contre sinistres locaux (incendie, inondation)
- Exemples : Datacenter distant, cloud

**Pourquoi c'est important** :

**Protection contre** :
- **Défaillance matérielle** : Disque dur HS → Autre copie disponible
- **Erreur humaine** : Suppression accidentelle → Restauration possible
- **Ransomware** : Chiffrement des données → Copie hors ligne saine
- **Sinistre** : Incendie datacenter → Copie off-site sauve l'entreprise
- **Corruption** : Fichier corrompu → Plusieurs versions disponibles

**Exemple d'application** :
```
Copie 1 (Production) : Serveur principal en datacenter
Copie 2 (Backup 1)   : Disque NAS local (même datacenter)
Copie 3 (Backup 2)   : Cloud Azure (off-site)

Supports différents : SSD (prod) + HDD (NAS) + Cloud
```

**Évolution 3-2-1-1-0** :
- **3-2-1** : Base
- **+1** : 1 copie air-gap (déconnectée du réseau, anti-ransomware)
- **+0** : 0 erreur lors des tests de restauration

---

### ✔️ Question 5 - Snapshot vs Backup

**Réponse modèle** :

**Snapshot (Instantané)** :
- **Principe** : Image de l'état d'un système à un instant T
- **Technologie** : Pointeurs vers les blocs de données (COW - Copy on Write)
- **Vitesse** : Quasi-instantané (quelques secondes)
- **Stockage** : Même support que les données originales
- **Durée de vie** : Court terme (heures/jours)
- **Usage** : Avant mise à jour, tests, retour arrière rapide

**Sauvegarde (Backup)** :
- **Principe** : Copie physique complète des données
- **Technologie** : Copie réelle des fichiers
- **Vitesse** : Plus lent (minutes/heures)
- **Stockage** : Support différent (disque externe, cloud)
- **Durée de vie** : Long terme (semaines/mois/ans)
- **Usage** : Protection contre sinistres, archivage

**Différences clés** :

| Critère | Snapshot | Backup |
|---------|----------|--------|
| **Vitesse création** | Secondes | Heures |
| **Vitesse restauration** | Très rapide | Lent |
| **Espace** | Faible (delta) | Important (copie) |
| **Protection sinistre** | ❌ Non | ✅ Oui |
| **Rétention** | Court | Long |
| **Dépendance** | ✅ Oui (données source) | ❌ Non |

**Peut-on remplacer l'un par l'autre ?**

**NON !** Ils sont **complémentaires** :
- **Snapshot** : Retour arrière rapide (erreur de config, MAJ ratée)
- **Backup** : Protection réelle contre sinistres

**Risque du snapshot seul** :
- Si disque HS → Snapshot perdu aussi (même support)
- Snapshot corrompu → Données perdues
- Ransomware chiffre tout → Snapshot inutile

**Stratégie recommandée** :
```
Snapshot : Toutes les heures (VMware, Hyper-V)
Backup   : Quotidien (off-site)
```

**Exemple** :
- Mise à jour Windows → Snapshot avant (retour arrière si problème)
- Protection données → Backup quotidien sur NAS + cloud

---

### ✔️ Question 6 - Stratégie GFS

**Réponse modèle** :

**GFS (Grand-Père - Père - Fils)** :
Stratégie de rétention hiérarchique des sauvegardes.

**Fils (Quotidien)** :
- **Fréquence** : Tous les jours (lundi à vendredi)
- **Rétention** : 1 semaine
- **Type** : Incrémentale ou différentielle
- **Usage** : Restauration récente (fichier supprimé hier)

**Père (Hebdomadaire)** :
- **Fréquence** : 1 fois par semaine (vendredi ou dimanche)
- **Rétention** : 4 semaines (1 mois)
- **Type** : Complète
- **Usage** : Restauration semaine dernière

**Grand-Père (Mensuel)** :
- **Fréquence** : 1 fois par mois (dernier jour du mois)
- **Rétention** : 12 mois (1 an) ou plus
- **Type** : Complète
- **Usage** : Restauration ancienne, archivage, légal

**Exemple de rétention** :

**Semaine 1** :
- Dimanche : Complète (Père #1)
- Lundi-Vendredi : Incrémentales (Fils)

**Semaine 2-4** : Idem

**Fin du mois** :
- Dimanche : Complète mensuelle (Grand-Père)
- Conservation 12 mois

**Calendrier visuel** :
```
        Lun  Mar  Mer  Jeu  Ven  Sam  Dim
Sem 1    I    I    I    I    I    -    P1
Sem 2    I    I    I    I    I    -    P2
Sem 3    I    I    I    I    I    -    P3
Sem 4    I    I    I    I    I    -    GP1 (mensuel)

I = Incrémentale (Fils) → 7 jours
P = Complète hebdo (Père) → 4 semaines
GP = Complète mensuelle (Grand-Père) → 12 mois
```

**Avantages** :
- Optimise l'espace disque
- Restauration flexible (court/moyen/long terme)
- Respect des obligations légales

**Variante 3-2-1 avec GFS** :
- Fils : Disque local
- Père : NAS
- Grand-Père : Cloud ou bandes (off-site)

---

### ✔️ Question 7 - Restauration

**Réponse modèle** :

**Situation** :
- Incident : Vendredi soir
- Sauvegardes disponibles :
  - Complète : Dimanche soir
  - Incrémentales : Lundi, Mardi, Mercredi, Jeudi, Vendredi (soirs)

**Procédure de restauration** :

**Étape 1 : Restaurer la sauvegarde COMPLÈTE (dimanche)** :
- Restaure l'état du système dimanche soir
- Base de départ

**Étape 2 : Restaurer les incrémentales DANS L'ORDRE** :
- Lundi → Mardi → Mercredi → Jeudi → Vendredi
- **IMPORTANT** : Respecter l'ordre chronologique !
- Chaque incrémentale contient les modifications du jour

**Schéma** :
```
Dim (Complète) → Lun (Incr) → Mar (Incr) → Mer (Incr) → Jeu (Incr) → Ven (Incr)
    ↓              ↓            ↓            ↓            ↓            ↓
 Restaurer 1    Restaurer 2  Restaurer 3  Restaurer 4  Restaurer 5  Restaurer 6
```

**Résultat final** : Système dans l'état de vendredi soir

**Si c'était avec des DIFFÉRENTIELLES** :
```
Procédure simplifiée :
1. Restaurer la complète (dimanche)
2. Restaurer UNIQUEMENT la dernière différentielle (vendredi)
   → Contient déjà toutes les modifs lun-ven
```

**Temps estimé** :
- Incrémentales : 1-3 heures (6 sauvegardes à restaurer)
- Différentielles : 30 min - 1h (2 sauvegardes seulement)

**Vérifications post-restauration** :
- Intégrité des fichiers
- Cohérence base de données
- Test fonctionnel application
- Logs de restauration

---

### ✔️ Question 8 - Tests de restauration

**Réponse modèle** :

**Pourquoi tester régulièrement** :

**Risques sans tests** :
- **Sauvegardes corrompues** : Découvert lors du sinistre (trop tard !)
- **Procédure incorrecte** : Étapes manquantes, erreurs
- **Incompatibilités** : Version logiciel changée, restore impossible
- **Temps sous-estimé** : RTO non respecté
- **Compétences perdues** : Équipe ne sait plus restaurer

**Statistiques alarmantes** :
- 30% des sauvegardes échouent silencieusement
- 50% des restaurations n'ont jamais été testées
- 25% des entreprises découvrent que leur backup ne fonctionne pas... trop tard

**Quoi tester** :
1. **Intégrité technique** : Sauvegarde lisible ? Pas d'erreurs ?
2. **Restauration complète** : Remonter un serveur entier
3. **Restauration partielle** : Récupérer quelques fichiers
4. **Temps de restauration** : Mesurer le RTO réel
5. **Fonctionnalité** : Application fonctionne après restore ?

**Fréquence recommandée** :

| Type de données | Fréquence tests |
|-----------------|-----------------|
| **Critiques** (prod, BDD) | **Mensuel** |
| **Importantes** (fichiers, emails) | **Trimestriel** |
| **Standard** (archives) | **Semestriel** |
| **Test complet PRA** | **Annuel** |

**Procédure de test** :
1. Planifier (hors heures prod)
2. Restaurer sur environnement isolé (pas la prod !)
3. Vérifier intégrité et fonctionnalité
4. Chronométrer (valider RTO)
5. Documenter (rapport de test)
6. Corriger les problèmes identifiés

**Bonnes pratiques** :
- Automatiser les tests (scripts)
- Varier les scénarios (fichier, VM complète, BDD)
- Impliquer différentes personnes (pas que l'expert)
- Documenter les procédures (wiki interne)

**Exemple de rapport** :
```
Test du : 15/11/2024
Système : Serveur Web
Type : Restauration complète VM
Résultat : ✅ Succès
Durée : 45 min (RTO = 1h : OK)
Problèmes : Aucun
```

---

### ✔️ Question 9 - Site de secours

**Réponse modèle** :

**Site à CHAUD (Hot Site)** :
- **Infrastructure** : Serveurs allumés, données synchronisées en temps réel
- **Réseau** : Connexions actives, prêtes
- **Données** : Réplication continue (0 RPO possible)
- **Basculement** : Quasi-instantané (minutes)
- **RTO** : < 1 heure
- **Coût** : **Très élevé** (infrastructure doublée)
- **Usage** : Services critiques 24/7 (banques, hôpitaux, e-commerce)

**Site TIÈDE (Warm Site)** :
- **Infrastructure** : Serveurs installés mais éteints, réseau configuré
- **Données** : Sauvegardes régulières (RPO quelques heures)
- **Basculement** : Manuel (démarrer serveurs, restaurer données)
- **RTO** : Quelques heures (4-24h)
- **Coût** : **Moyen** (matériel présent mais pas doublé)
- **Usage** : Services importants non critiques

**Site à FROID (Cold Site)** :
- **Infrastructure** : Local vide avec électricité, climatisation, réseau
- **Données** : Sauvegardes hors site (bandes, cloud)
- **Basculement** : Très long (commander matériel, installer, configurer)
- **RTO** : Plusieurs jours à semaines
- **Coût** : **Faible** (juste le local)
- **Usage** : Données archivées, faible criticité

**Tableau comparatif** :

| Critère | Hot Site | Warm Site | Cold Site |
|---------|----------|-----------|-----------|
| **RTO** | < 1h | 4-24h | Jours/semaines |
| **RPO** | Minutes | Heures | Jours |
| **Coût** | €€€€€ | €€€ | € |
| **Complexité** | Très élevée | Moyenne | Faible |
| **Serveurs** | Actifs | Éteints | Absents |
| **Réseau** | Actif | Configuré | Présent |
| **Données** | Réplication | Sauvegardes | Bandes |

**Exemples concrets** :

**Hot Site** - Banque en ligne :
- Datacenter principal Paris
- Datacenter secours Lyon
- Réplication synchrone base de données
- Load balancer bascule automatiquement

**Warm Site** - PME (ERP)** :
- Serveurs achetés mais éteints
- Sauvegardes quotidiennes restaurées si besoin
- RTO = 8h acceptable

**Cold Site** - Archives légales :
- Local sécurisé avec électricité
- Bandes mensuelles stockées
- Restauration si audit ou litige (rare)

**Choix du site** : Dépend du **RTO/RPO** et du **budget**

---

### ✔️ Question 10 - Scénario RTO/RPO

**Réponse modèle** :

**Contraintes données** :
- **RTO = 4 heures maximum** (temps d'interruption)
- **RPO = 1 heure maximum** (perte de données)

**Analyse** :
- RTO 4h → Restauration manuelle acceptable (pas besoin de redondance coûteuse)
- RPO 1h → Sauvegarde **toutes les heures minimum**

**Stratégie de sauvegarde proposée** :

**1. Sauvegardes fréquentes** :
- **Snapshots toutes les heures** (RPO = 1h)
- **Incrémentales quotidiennes** à 20h
- **Complète hebdomadaire** dimanche 22h

**2. Stockage multi-niveaux** :
- **Local** : Snapshots horaires (7 jours) → Restauration rapide
- **NAS** : Sauvegardes quotidiennes (30 jours) → Protection locale
- **Cloud** : Sauvegardes hebdomadaires (90 jours) → Protection off-site

**3. Infrastructure** :
- **Pas de cluster** (RTO 4h accepté → Coût optimisé)
- **Serveur de secours** tiède (warm standby) → 2-3h de basculement
- **Documentation procédures** complètes

**4. Procédure de restauration documentée** :
```
Étape 1 (15 min) : Diagnostic incident
Étape 2 (30 min) : Démarrage serveur secours
Étape 3 (2h)     : Restauration dernière sauvegarde
Étape 4 (1h)     : Tests et mise en prod
Total : 3h45 → RTO respecté
```

**5. Tests réguliers** :
- **Mensuel** : Test restauration snapshot
- **Trimestriel** : Test restauration complète
- **Annuel** : Test PRA avec serveur secours

**Architecture** :
```
Production
    ↓
Snapshot horaire (RPO = 1h) → Stockage local (7 jours)
    ↓
Backup incrémental quotidien → NAS (30 jours)
    ↓
Backup complet hebdo → Cloud Azure (90 jours)

Serveur secours tiède → Basculement si incident (RTO = 4h)
```

**Coûts estimés** :
- Snapshots : Inclus (stockage local +20%)
- NAS : 2 000€ + disques
- Cloud : 50€/mois (stockage)
- Serveur secours : 3 000€
**Total** : ~7 000€ initial + 50€/mois

**Validation** :
- ✅ RPO 1h : Snapshots horaires
- ✅ RTO 4h : Procédure testée en 3h45
- ✅ Protection 3-2-1 : Local + NAS + Cloud
- ✅ Coût optimisé : Pas de redondance active coûteuse

---

### ✔️ Question 11 - Sauvegarde base de données

**Réponse modèle** :

**Pourquoi copier les fichiers .mdf n'est PAS recommandé** :

**Problèmes** :
1. **Incohérence des données** :
   - BDD écrit en continu (transactions actives)
   - Copie de fichiers peut capturer un état incohérent
   - Restauration = BDD corrompue

2. **Fichiers verrouillés** :
   - SQL Server verrouille les fichiers .mdf/.ldf
   - Impossible de copier tant que service actif

3. **Transactions non validées** :
   - Transactions en cours perdues
   - Logs de transactions (.ldf) non synchronisés

4. **Absence de point de reprise** :
   - Pas de garantie de cohérence
   - Restauration aléatoire

**Méthode CORRECTE** :

**SQL Server** :
```sql
-- 1. Sauvegarde complète
BACKUP DATABASE [NomBase]
TO DISK = 'D:\Backups\NomBase_FULL.bak'
WITH INIT, COMPRESSION;

-- 2. Sauvegarde différentielle
BACKUP DATABASE [NomBase]
TO DISK = 'D:\Backups\NomBase_DIFF.bak'
WITH DIFFERENTIAL, COMPRESSION;

-- 3. Sauvegarde des logs (transactions)
BACKUP LOG [NomBase]
TO DISK = 'D:\Backups\NomBase_LOG.trn'
WITH COMPRESSION;
```

**MySQL/MariaDB** :
```bash
# Avec mysqldump (cohérent)
mysqldump -u root -p --single-transaction --databases mydb > backup.sql

# Avec mysqlbackup (hot backup)
mysqlbackup --backup-dir=/backup --backup-image=backup.mbi backup
```

**PostgreSQL** :
```bash
# Avec pg_dump
pg_dump -U postgres -F c mydb > backup.dump

# Avec pg_basebackup (physique)
pg_basebackup -D /backup -F tar -z -P
```

**Avantages méthodes natives** :
- ✅ Cohérence garantie (point dans le temps)
- ✅ Fonctionne à chaud (BDD reste active)
- ✅ Compression possible
- ✅ Restauration fiable
- ✅ Récupération point dans le temps (PITR)

**Stratégie recommandée** :
- **Complète** : Dimanche 22h
- **Différentielle** : Lun-Sam 22h
- **Logs transactions** : Toutes les heures (RPO fin)

**Outils tiers** :
- Veeam Backup & Replication
- Commvault
- Rubrik
- Acronis

---

### ✔️ Question 12 - Chiffrement des sauvegardes

**Réponse modèle** :

**Pourquoi chiffrer les sauvegardes** :

**Risques SANS chiffrement** :

**1. Vol physique** :
- Bandes magnétiques volées/perdues
- Disques durs externes dérobés
- Accès direct aux données sensibles

**2. Accès non autorisé** :
- Technicien malveillant
- Prestataire externe (cloud, stockage)
- Intrusion système de backup

**3. Obligations légales** :
- **RGPD** : Données personnelles doivent être protégées
- Amendes jusqu'à 4% du CA mondial (20M€)
- Secteur santé : Données de santé très sensibles

**4. Espionnage industriel** :
- Secrets commerciaux exposés
- Propriété intellectuelle compromise
- Données stratégiques accessibles

**5. Ransomware** :
- Attaquants peuvent voler ET chiffrer les backups
- Double extorsion (chiffrement + menace publication)

**Conséquences réelles** :

**Exemple 1** : Hôpital
- Bandes de backup volées
- Contiennent dossiers médicaux 10 000 patients
- Sanction CNIL : 50 000€ + obligation notification patients

**Exemple 2** : Entreprise PME
- Disque dur backup perdu en transport
- Données clients et financières accessibles
- Perte confiance clients, impact réputationnel

**Ce qu'il faut chiffrer** :
- ✅ Sauvegardes complètes/différentielles/incrémentales
- ✅ Sauvegardes en transit (réseau)
- ✅ Sauvegardes au repos (stockage)
- ✅ Sauvegardes cloud
- ✅ Bandes magnétiques
- ✅ Disques externes

**Technologies** :
- **AES-256** : Standard (très sûr)
- **Chiffrement matériel** : Lecteurs bandes auto-chiffrantes
- **TLS/SSL** : Transit réseau
- **BitLocker/LUKS** : Chiffrement disque

**Gestion des clés** :
- ⚠️ **CRITIQUE** : Perte des clés = perte des données !
- Stocker clés séparément des backups
- Coffre-fort numérique (Azure Key Vault, AWS KMS)
- Procédure d'escrow (séquestre)

**Bonnes pratiques** :
1. Chiffrement par défaut (toujours)
2. Clés différentes par client/système
3. Rotation clés régulière
4. Documentation procédures
5. Tests restauration avec clés

---

### ✔️ Question 13 - Stockage des sauvegardes

**Réponse modèle** :

**Pour respecter la règle 3-2-1** :

**Emplacement 1 : Local (même site)** :
- **Quoi** : Disque dur interne serveur, baie de stockage, NAS
- **Avantages** :
  - Restauration très rapide (réseau local)
  - Pas de coût de bande passante
  - Accès immédiat
- **Inconvénients** :
  - Vulnérable sinistres locaux (incendie, inondation)
  - Ransomware peut y accéder
- **Usage** : Sauvegardes quotidiennes, snapshots
- **Exemple** : NAS Synology dans salle serveur

**Emplacement 2 : Distant même ville/région** :
- **Quoi** : Datacenter tiers, bureau secondaire, stockage partenaire
- **Avantages** :
  - Protection contre sinistre du site principal
  - Restauration relativement rapide (bande passante correcte)
- **Inconvénients** :
  - Sinistre régional (inondation zone) affecte les 2 sites
  - Coût hébergement
- **Usage** : Sauvegardes hebdomadaires
- **Exemple** : Bureau secondaire à 20 km avec serveur backup

**Emplacement 3 : Cloud/Off-site géographiquement distant** :
- **Quoi** : Cloud Azure, AWS S3, Google Cloud, datacenter distant >100km
- **Avantages** :
  - Protection maximale (sinistre géographique)
  - Réplication multi-régions possible
  - Scalabilité illimitée
- **Inconvénients** :
  - Restauration plus lente (bande passante Internet)
  - Coût mensuel récurrent
  - Dépendance connectivité
- **Usage** : Sauvegardes mensuelles, archives long terme
- **Exemple** : Azure Blob Storage région Nord Europe + Europe Ouest

**Schéma complet** :
```
Production (Paris)
    ↓
┌───────────────────────────────────────┐
│ Copie 1 (Local)                      │
│ NAS dans salle serveur               │
│ Quotidien, snapshots                 │
│ Restauration : Minutes               │
└───────────────────────────────────────┘
    ↓
┌───────────────────────────────────────┐
│ Copie 2 (Distant - même région)     │
│ Bureau secondaire (50 km)            │
│ Hebdomadaire                         │
│ Restauration : 1-2h                  │
└───────────────────────────────────────┘
    ↓
┌───────────────────────────────────────┐
│ Copie 3 (Off-site cloud)             │
│ Azure Storage (Amsterdam)            │
│ Mensuel + archives                   │
│ Restauration : 4-12h                 │
└───────────────────────────────────────┘
```

**Supports différents (règle 3-2-1)** :
- **Support 1** : SSD/HDD (production + local)
- **Support 2** : Bandes LTO ou HDD différent (distant)
- **Support 3** : Cloud (architecture différente)

**Bonnes pratiques** :
- ✅ Chiffrement sur tous les emplacements
- ✅ Vérification intégrité régulière
- ✅ Tests restauration depuis chaque emplacement
- ✅ Documentation accès (urgence)
- ✅ Monitoring espace disponible

**Coûts estimés (TPE/PME)** :
- Local (NAS 10 To) : 1 500€
- Distant (colocation) : 100€/mois
- Cloud (Azure/AWS) : 50-200€/mois

---

### ✔️ Question 14 - Réplication vs Sauvegarde

**Réponse modèle** :

**Réplication de données** :
- **Principe** : Copie **synchrone ou asynchrone** en temps réel ou quasi-réel
- **Objectif** : **Haute disponibilité** (HA), continuité de service
- **Fréquence** : Continue (secondes/minutes)
- **RPO** : Très faible (<1 minute possible)
- **Technologie** : Réplication bloc/fichier, clustering, base données
- **Versions** : 1 seule (état actuel) ou quelques versions récentes
- **Usage** : PCA, failover automatique

**Sauvegarde (Backup)** :
- **Principe** : Copie **ponctuelle et indépendante** des données
- **Objectif** : **Restauration après incident**, archivage
- **Fréquence** : Planifiée (horaire/quotidien/hebdo)
- **RPO** : Selon fréquence (heures/jours)
- **Technologie** : Copy, snapshot, backup logiciel
- **Versions** : Multiples (historique, rétention)
- **Usage** : Récupération données, erreur humaine, ransomware

**Différences clés** :

| Critère | Réplication | Sauvegarde |
|---------|-------------|------------|
| **Objectif** | Disponibilité | Récupération |
| **Fréquence** | Continue | Planifiée |
| **Versions** | 1 (actualisé) | Multiples (historique) |
| **RPO** | Secondes | Heures/jours |
| **RTO** | Secondes/minutes | Minutes/heures |
| **Automatisme** | Basculement auto | Restauration manuelle |
| **Erreur humaine** | ❌ Répliquée ! | ✅ Protégé (versions) |
| **Ransomware** | ❌ Chiffré aussi ! | ✅ Copie saine |

**Peut-on se passer de l'une si on a l'autre ?**

**NON ! Elles sont COMPLÉMENTAIRES** :

**Scénarios où la réplication SEULE est insuffisante** :

**1. Suppression accidentelle** :
- Admin supprime base de données à 10h
- Réplication propage la suppression immédiatement
- **Besoin backup** : Restaurer version 8h

**2. Ransomware** :
- Malware chiffre les données à 14h
- Réplication chiffre le réplica aussi
- **Besoin backup** : Restaurer version saine (hier)

**3. Corruption de données** :
- Base de données corrompue par bug applicatif
- Réplication propage la corruption
- **Besoin backup** : Restaurer version antérieure

**4. Obligations légales** :
- Archivage 7 ans requis
- Réplication ne garde pas l'historique
- **Besoin backup** : Sauvegardes mensuelles archivées

**Scénarios où la sauvegarde SEULE est insuffisante** :

**1. Panne serveur** :
- Serveur HS à 15h, RTO = 30 min
- Restauration backup prend 2h
- **Besoin réplication** : Failover automatique en 2 min

**2. Maintenance** :
- Mise à jour serveur principal
- Sauvegarde ne permet pas de maintenir le service
- **Besoin réplication** : Bascule sur réplica pendant MAJ

**Stratégie OPTIMALE** :

```
┌─────────────────────────────────────────────────────┐
│ Production (Paris)                                   │
└─────────────────────────────────────────────────────┘
         ↓                           ↓
    Réplication                 Sauvegarde
    (temps réel)                (quotidien)
         ↓                           ↓
┌──────────────────────┐    ┌──────────────────────┐
│ Réplica (Lyon)       │    │ Backup (Cloud)       │
│ Failover auto        │    │ Versions multiples   │
│ RPO < 1 min          │    │ Rétention 90 jours   │
│ RTO < 5 min          │    │ Protection ransomware│
└──────────────────────┘    └──────────────────────┘
```

**Exemple concret - E-commerce** :
- **Réplication** : Base de données Paris → Lyon (async 30s)
  - Si Paris tombe → Lyon prend le relais (RTO 2 min)
- **Sauvegarde** : Quotidien vers Azure (rétention 90 jours)
  - Si ransomware → Restaurer backup d'hier
  - Si erreur humaine → Restaurer version antérieure

**Conclusion** : **Les deux sont nécessaires** pour une stratégie complète !

---

### ✔️ Question 15 - Politique de rétention

**Réponse modèle** :

**Contrainte** : Conservation **7 ans** (légal)

**Problèmes à résoudre** :
- Coût stockage (7 ans × sauvegardes = énorme !)
- Performance restauration
- Conformité légale

**Politique de rétention optimisée** :

**Niveau 1 : Court terme (0-30 jours)** - Restauration fréquente
- **Type** : Incrémentales quotidiennes
- **Stockage** : Disque rapide (local/NAS)
- **Rétention** : 30 jours
- **Coût** : Moyen (10 To)
- **Usage** : Erreurs utilisateurs, fichiers supprimés

**Niveau 2 : Moyen terme (1-12 mois)** - Restauration occasionnelle
- **Type** : Complètes hebdomadaires
- **Stockage** : Disque standard (cloud tier cool)
- **Rétention** : 12 mois (52 sauvegardes hebdo)
- **Coût** : Faible (déduplication, compression)
- **Usage** : Projets terminés, audits internes

**Niveau 3 : Long terme (1-7 ans)** - Archivage légal
- **Type** : Complètes mensuelles
- **Stockage** : Bandes LTO ou cloud archive (Azure Archive, AWS Glacier)
- **Rétention** : 84 mois (7 ans × 12)
- **Coût** : Très faible (0,004€/Go/mois Azure Archive)
- **Usage** : Conformité légale, litiges

**Calendrier de rétention** :

| Période | Type | Fréquence | Nombre | Stockage | Coût/mois |
|---------|------|-----------|--------|----------|-----------|
| 0-30j | Incrémentale | Quotidien | 30 | NAS local | 100€ |
| 1-12 mois | Complète | Hebdo | 52 | Cloud Standard | 150€ |
| 1-7 ans | Complète | Mensuel | 84 | Cloud Archive | 50€ |
| **TOTAL** | | | **166** | | **300€/mois** |

**Optimisations techniques** :

**1. Déduplication** :
- Stocke les blocs uniques une seule fois
- Économie : 70-90% d'espace
- **Exemple** : 100 To réels → 15 To stockés

**2. Compression** :
- Réduit la taille des données (50-70%)
- **Exemple** : Fichiers texte, logs, BDD

**3. Archivage intelligent** :
- Données froides → Tier archive automatique
- Accès rare → Coût minimal

**4. Versioning intelligent** :
- **GFS** : Garder seulement dernier backup de chaque niveau
  - Dernier quotidien de chaque semaine → Hebdo
  - Dernier hebdo de chaque mois → Mensuel
  - Dernier mensuel de chaque année → Annuel (7 ans)

**Exemple de transformation** :
```
Au lieu de garder TOUTES les sauvegardes quotidiennes 7 ans :
- 7 ans × 365 jours = 2555 sauvegardes !!! (ingérable)

Avec GFS optimisé :
- Quotidiennes : 30 (1 mois)
- Hebdomadaires : 52 (1 an)
- Mensuelles : 84 (7 ans)
= 166 sauvegardes TOTAL (optimisé !)
```

**Tableau récapitulatif stockage** :

```
Années 1-7 : Mensuelles (cloud archive)
  ↓
Mois 1-12 : Hebdomadaires (cloud standard)
  ↓
Jours 1-30 : Quotidiennes (local/NAS)
  ↓
Production
```

**Coûts estimés (PME - 10 To données)** :

**Avec déduplication/compression (90% économie)** :
- Local 30j : 1 To × 10€/To = 10€
- Cloud 12 mois : 5 To × 20€/To = 100€
- Archive 7 ans : 50 To × 2€/To = 100€
**Total** : ~210€/mois pour 7 ans de rétention

**Vs sans optimisation** :
- 10 To × 2555 sauvegardes × 10€/To = **255 000€/mois** (impossible !)

**Conformité légale** :
- ✅ Rétention 7 ans respectée
- ✅ Intégrité vérifiable (checksums)
- ✅ Accès tracé (logs)
- ✅ Chiffrement (RGPD)
- ✅ Tests restauration trimestriels

**Technologies recommandées** :
- **Veeam Backup & Replication** : GFS, dédup, cloud tier
- **Azure Backup** : Rétention longue, tiers automatiques
- **Bandes LTO-9** : Archivage 7 ans (économique, pérenne)

---

## 📊 AUTO-ÉVALUATION

**Nombre de réponses correctes** : _____ / 15

| Score | Niveau |
|-------|--------|
| 14-15 | ⭐⭐⭐ Expert - Prêt pour l'examen ! |
| 11-13 | ⭐⭐ Très bien - Révise les détails |
| 8-10 | ⭐ Bien - Relis FICHE-03-Sauvegardes |
| 0-7 | 🔴 Insuffisant - Révision urgente |
