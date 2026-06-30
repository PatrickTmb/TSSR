# EXAMEN BLANC 1 - CORRECTIONS ET BARÈME

**Examen** : TSSR - Technicien Supérieur Systèmes et Réseaux  
**Type** : Examen blanc complet (MSP + Questionnaire technique)  
**Durée** : 3h30 (210 minutes)  
**Total** : **200 points**  

---

## 🎯 RÉPARTITION GLOBALE

| Partie | Points | Pourcentage | Durée |
|--------|--------|-------------|-------|
| **MSP Écrite (3 incidents)** | 100 | 50% | 1h30 |
| **Questionnaire Technique (6 sections)** | 100 | 50% | 2h00 |
| **TOTAL** | **200** | **100%** | **3h30** |

---

# PARTIE 1 : MSP ÉCRITE (100 points)

---

## 🔧 INCIDENT 1 : PROBLÈME DHCP (30 points)

### Question 1.1 - Cause du problème (5 points)

**Réponse attendue** :

La cause du problème est le **manque d'adresses IP disponibles dans la plage DHCP**.

**Justification** :
- Plage actuelle : 192.168.20.100 à 192.168.20.150 = **51 adresses** (150-100+1)
- Postes existants : **28** (avec baux actifs)
- Nouveaux postes : **5**
- Total besoins : **28 + 5 = 33 postes**

**Calcul** : Il reste théoriquement 51-28 = 23 adresses libres, MAIS :
- Les baux DHCP ne sont pas libérés immédiatement (durée 8 jours par défaut)
- Certains postes peuvent avoir des adresses même s'ils sont éteints
- Le pool est probablement **saturé ou proche de la saturation**

Les nouveaux PC reçoivent **169.254.x.x** (APIPA = Automatic Private IP Addressing), ce qui confirme qu'ils ne reçoivent pas de réponse du serveur DHCP.

**Barème (5 points)** :

| Critère | Points | Détails |
|---------|--------|---------||
| **Identification correcte** | 3 pts | "Manque d'adresses IP" ou "Plage DHCP saturée" |
| **Justification calcul** | 2 pts | Calcul 150-100+1=51 adresses, postes existants vs besoins |
| **Mention APIPA 169.254.x.x** | +0.5 pts | Bonus si évoqué |
| **Explication baux actifs** | +0.5 pts | Bonus si évoqué |

**Erreurs pénalisantes** :
- ❌ "Serveur DHCP en panne" (-2 pts) : Incorrect, autres PCs fonctionnent
- ❌ "Problème réseau/câble" (-1 pt) : Trop vague

---

---

### ✔️ Question 1.2 (8 points) - Solution court terme

**Réponse attendue** :

**Solution immédiate** : Étendre temporairement la plage DHCP

**Étapes dans la console DHCP Windows Server** :

1. Ouvrir **Gestionnaire DHCP** (`dhcpmgmt.msc`)
2. Naviguer : `SRV-DHCP-LYON → IPv4 → Étendue [192.168.20.0]`
3. Clic droit sur **Pool d'adresses** → **Propriétés**
4. Modifier la **plage de fin** : `192.168.20.150` → `192.168.20.180`
   - Ajoute 30 adresses (150 à 180)
5. Cliquer **OK**
6. **Optionnel** : Clic droit sur l'étendue → **Actualiser**
7. Vérifier dans **Baux d'adresses** les nouvelles attributions

**Alternative rapide** :
- Libérer des baux non utilisés : Clic droit sur baux anciens/expirés → **Supprimer**
- Réduire la durée de bail : Propriétés de l'étendue → Durée du bail : 8 jours → 1 jour (temporaire)

**Sur les nouveaux PC** :
```cmd
ipconfig /renew
```

**Barème (8 points)** :

| Critère | Points | Détails |
|---------|--------|---------||
| **Ouvrir console DHCP** | 1 pt | `dhcpmgmt.msc` ou Gestionnaire DHCP |
| **Naviguer vers l'étendue** | 1 pt | IPv4 → Étendue [192.168.20.0] → Pool |
| **Modifier plage de fin** | 4 pts | Étendre .150 → .180 ou supérieur (valeur cohérente) |
| **Appliquer changements** | 1 pt | Valider, actualiser |
| **Renouveler IP clients** | 1 pt | `ipconfig /renew` mentionné |
| **Alternative libérer baux** | +1 pt | Bonus : Supprimer baux anciens |

**Erreurs pénalisantes** :
- ❌ "Redémarrer le serveur" (-2 pts) : Inutile, ne résout pas le problème
- ❌ Valeur incohérente (.250 dans un /24) (-2 pts)
- ❌ Modification du masque de sous-réseau (-3 pts) : Change toute l'architecture

---

### ✔️ Question 1.3 (7 points) - Solution moyen terme

**Réponse attendue** :

**Solution pérenne** : Redimensionner la plage DHCP pour anticiper la croissance

**Calcul des besoins** :
- Postes actuels : 28
- Nouveaux postes : 5
- Recrutements prévus (3 mois) : 10
- **Total** : 28 + 5 + 10 = **43 postes**
- **Marge sécurité (20%)** : 43 × 1.2 = **52 postes**
- **Recommandation** : Prévoir **60 adresses**

**Nouvelle plage recommandée** :
- **Début** : `192.168.20.100`
- **Fin** : `192.168.20.159` (60 adresses : 100 + 60 - 1)

**Configuration** :
1. Console DHCP → Propriétés de l'étendue
2. Plage de début : `192.168.20.100`
3. Plage de fin : `192.168.20.159`
4. Vérifier que le masque est `/24` (255.255.255.0)
5. Appliquer

**Alternative optimale** :
- Plage : `192.168.20.100` à `192.168.20.200` (101 adresses)
- Exclure `192.168.20.180-200` pour futurs serveurs/imprimantes
- Plage effective : 100-179 = **80 adresses disponibles**

**Barème (7 points)** :

| Critère | Points | Détails |
|---------|--------|---------||
| **Calcul besoins actuels** | 1 pt | 28 + 5 + 10 = 43 postes |
| **Marge de sécurité** | 2 pts | 20-30% supplémentaires (43 × 1.2 = 52) |
| **Nouvelle plage calculée** | 3 pts | Cohérente avec besoins + marge (ex: 100-159 = 60 adresses) |
| **Justification anticipation** | 1 pt | Expliquer pourquoi prévoir plus |
| **Alternative exclusions** | +1 pt | Bonus : Exclure plage pour IP statiques |

**Erreurs pénalisantes** :
- ❌ Pas de marge de sécurité (-2 pts)
- ❌ Plage trop juste (≤ 45 adresses) (-1 pt)
- ❌ Plage dépassant le /24 sans explication (-2 pts)

---

### ✔️ Question 1.4 (5 points) - Réservation DHCP vs IP statique

**Réponse attendue** :

**Avantages des réservations DHCP** :

1. **Gestion centralisée** :
   - Toutes les config IP sur le serveur DHCP
   - Modifications globales faciles (changer DNS, passerelle)
   - Pas besoin d'accéder physiquement à l'imprimante

2. **Cohérence des options** :
   - Les imprimantes reçoivent automatiquement DNS, passerelle, etc.
   - Évite les erreurs de configuration manuelle

3. **Traçabilité** :
   - Historique des baux dans les logs DHCP
   - Mapping IP ↔ MAC documenté
   - Facilite le dépannage

4. **Flexibilité** :
   - Changement d'IP facile (modifier réservation)
   - Pas de reconfiguration sur l'appareil

5. **Conformité avec les politiques réseau** :
   - Toutes les IP gérées par DHCP (audit, sécurité)

**Configuration réservation** :
```
Console DHCP → Réservations → Nouvelle réservation
- Nom : IMP-COMPTA-01
- Adresse IP : 192.168.20.50
- Adresse MAC : 00:1A:2B:3C:4D:5E
- Description : Imprimante service comptabilité
```

**Barème (5 points)** :

| Critère | Points | Détails |
|---------|--------|---------||
| **Avantage 1 : Gestion centralisée** | 2 pts | Toutes config sur serveur DHCP |
| **Avantage 2 : Cohérence options** | 2 pts | DNS, passerelle automatiques |
| **Avantage 3 : Traçabilité/Flexibilité** | 1 pt | Logs, historique, modification facile |
| **Clarté explication** | +0.5 pt | Bonus : Explication claire et structurée |

**Erreurs pénalisantes** :
- ❌ "Plus rapide" (-1 pt) : Faux, même performance
- ❌ Confusion réservation/bail dynamique (-1 pt)

---

### ✔️ Question 1.5 (5 points) - Options DHCP à vérifier

**Réponse attendue** :

**3 options DHCP essentielles** :

1. **Option 003 - Routeur (Passerelle par défaut)** :
   - Valeur : `192.168.20.1`
   - Permet l'accès Internet et routage inter-VLAN
   - Sans cette option : Pas d'accès hors du sous-réseau local

2. **Option 006 - Serveurs DNS** :
   - Valeur : `192.168.10.10` (DNS interne) + `8.8.8.8` (DNS public)
   - Permet la résolution de noms
   - Sans cette option : Impossible d'accéder aux ressources par nom (ex: serveur-fichiers)

3. **Option 015 - Nom de domaine DNS** :
   - Valeur : `techsolutions.local`
   - Suffixe DNS automatique
   - Facilite l'accès aux ressources internes

**Vérification** :
```
Console DHCP → Étendue → Options d'étendue → Configurer les options
Cocher : 003 Routeur, 006 Serveurs DNS, 015 Nom de domaine DNS
```

**Sur le client (vérifier réception)** :
```cmd
ipconfig /all
```
Vérifier : Passerelle par défaut, Serveurs DNS, Suffixe DNS

**Barème (5 points)** :

| Critère | Points | Détails |
|---------|--------|---------||
| **Option 003 - Routeur** | 2 pts | Code + nom + valeur (192.168.20.1) |
| **Option 006 - Serveurs DNS** | 2 pts | Code + nom + valeur IP DNS |
| **Option 015 - Domaine DNS** | 1 pt | Code + nom (ou autre option pertinente) |
| **Explication utilité** | +0.5 pt | Bonus : Expliquer l'usage de chaque option |

**Alternatives acceptées** :
- Option 042 (NTP servers)
- Option 044 (WINS servers)
- Option 046 (NetBIOS node type)

**Erreurs pénalisantes** :
- ❌ Options sans numéros (-1 pt)
- ❌ Valeurs IP incohérentes (-1 pt)
- ❌ Moins de 2 options correctes (-2 pts)

---

## 🔧 INCIDENT 2 : SCRIPT POWERSHELL - CORRECTIONS (30 points)

### ✔️ Question 2.1 (15 points) - Script complet

**Réponse attendue** :

```powershell
# Import du module Active Directory
Import-Module ActiveDirectory

# Chemin du fichier CSV
$CSVPath = "C:\Scripts\nouveaux_users.csv"

# Mot de passe initial (à changer à la première connexion)
$Password = ConvertTo-SecureString "P@ssw0rd2025!" -AsPlainText -Force

# Import des utilisateurs depuis le CSV
$Users = Import-Csv -Path $CSVPath -Delimiter ","

# Boucle sur chaque utilisateur
foreach ($User in $Users) {
    
    # Récupération des champs
    $Prenom = $User.Prenom
    $Nom = $User.Nom
    $Service = $User.Service
    $Fonction = $User.Fonction
    
    # Génération du SamAccountName (prenom.nom en minuscules)
    $Sam = "$($Prenom).$($Nom)".ToLower()
    
    # Génération de l'UPN
    $UPN = "$Sam@techsolutions.local"
    
    # Nom complet
    $DisplayName = "$Prenom $Nom"
    
    # Chemin de l'OU
    $OUPath = "OU=$Service,OU=Utilisateurs,DC=techsolutions,DC=local"
    
    # Création de l'utilisateur
    New-ADUser -Name $DisplayName `
               -GivenName $Prenom `
               -Surname $Nom `
               -SamAccountName $Sam `
               -UserPrincipalName $UPN `
               -Path $OUPath `
               -AccountPassword $Password `
               -Enabled $true `
               -ChangePasswordAtLogon $true `
               -Title $Fonction `
               -Description "Créé automatiquement le $(Get-Date -Format 'dd/MM/yyyy')"
    
    Write-Host "Utilisateur créé : $DisplayName ($Sam)" -ForegroundColor Green
}

Write-Host "`nCréation terminée ! Total : $($Users.Count) utilisateurs" -ForegroundColor Cyan
```

**Points clés du script** :
- Import module AD
- Lecture CSV
- Boucle foreach
- Génération Sam/UPN
- New-ADUser avec tous les paramètres
- ChangePasswordAtLogon = $true
- Enabled = $true

**Barème (15 points)** :

| Critère | Points | Détails |
|---------|--------|---------||
| **Import-Module ActiveDirectory** | 1 pt | Ou vérification module disponible |
| **Import-Csv correct** | 2 pts | Chemin, délimiteur, variable |
| **Boucle foreach structurée** | 2 pts | Syntaxe correcte, itération sur $Users |
| **Génération SamAccountName** | 2 pts | Prenom.Nom en minuscules (`.ToLower()`) |
| **Génération UPN** | 1 pt | sam@techsolutions.local |
| **Construction OUPath dynamique** | 2 pts | "OU=$Service,OU=Utilisateurs,DC=..." |
| **New-ADUser complet** | 5 pts | Tous paramètres essentiels (Name, GivenName, Surname, Sam, UPN, Path, Password, Enabled) |
| **ChangePasswordAtLogon** | 1 pt | -ChangePasswordAtLogon $true |
| **Affichage/Log basique** | 1 pt | Write-Host ou Out-File |
| **Syntaxe PowerShell** | -2 pts | Pénalité si erreurs syntaxe graves |

**Bonus** :
- +0.5 : SecureString pour mot de passe
- +0.5 : Description avec date création

**Erreurs pénalisantes** :
- ❌ Pas de boucle (-5 pts) : Script ne traite qu'un utilisateur
- ❌ SamAccountName avec majuscules/accents (-2 pts) : Non conforme
- ❌ Utilisateurs créés désactivés (-2 pts) : -Enabled $false ou manquant
- ❌ Mot de passe en clair (-1 pt) : Faille de sécurité
- ❌ OUPath en dur (pas dynamique) (-2 pts)

---

### ✔️ Question 2.2 (5 points) - Gestion erreurs et log

**Réponse attendue** :

```powershell
# Fichier de log
$LogFile = "C:\Scripts\creation_users.log"

# Initialisation du compteur
$SuccessCount = 0
$ErrorCount = 0

# En-tête du log
"=== Création utilisateurs - $(Get-Date -Format 'dd/MM/yyyy HH:mm:ss') ===" | Out-File -FilePath $LogFile

foreach ($User in $Users) {
    
    $Prenom = $User.Prenom
    $Nom = $User.Nom
    $Service = $User.Service
    $Sam = "$($Prenom).$($Nom)".ToLower()
    $UPN = "$Sam@techsolutions.local"
    $DisplayName = "$Prenom $Nom"
    $OUPath = "OU=$Service,OU=Utilisateurs,DC=techsolutions,DC=local"
    
    Try {
        # Vérifier si l'utilisateur existe déjà
        $ExistingUser = Get-ADUser -Filter "SamAccountName -eq '$Sam'" -ErrorAction SilentlyContinue
        
        if ($ExistingUser) {
            throw "L'utilisateur $Sam existe déjà"
        }
        
        # Vérifier si l'OU existe
        $OUExists = Get-ADOrganizationalUnit -Filter "DistinguishedName -eq '$OUPath'" -ErrorAction SilentlyContinue
        
        if (-not $OUExists) {
            throw "L'OU $OUPath n'existe pas"
        }
        
        # Création de l'utilisateur
        New-ADUser -Name $DisplayName `
                   -GivenName $Prenom `
                   -Surname $Nom `
                   -SamAccountName $Sam `
                   -UserPrincipalName $UPN `
                   -Path $OUPath `
                   -AccountPassword $Password `
                   -Enabled $true `
                   -ChangePasswordAtLogon $true `
                   -ErrorAction Stop
        
        # Log succès
        $Message = "[SUCCÈS] $(Get-Date -Format 'HH:mm:ss') - Utilisateur créé : $DisplayName ($Sam)"
        Write-Host $Message -ForegroundColor Green
        $Message | Out-File -FilePath $LogFile -Append
        $SuccessCount++
        
    } Catch {
        # Log erreur
        $ErrorMessage = "[ERREUR] $(Get-Date -Format 'HH:mm:ss') - $DisplayName ($Sam) : $($_.Exception.Message)"
        Write-Host $ErrorMessage -ForegroundColor Red
        $ErrorMessage | Out-File -FilePath $LogFile -Append
        $ErrorCount++
    }
}

# Résumé dans le log
$Summary = "`n=== RÉSUMÉ ===`nSuccès : $SuccessCount`nErreurs : $ErrorCount`nTotal : $($Users.Count)"
Write-Host $Summary -ForegroundColor Cyan
$Summary | Out-File -FilePath $LogFile -Append
```

**Barème** :
- Try/Catch correctement placé : **2 pts**
- Vérifications (user existe, OU existe) : **2 pts**
- Log avec horodatage : **1 pt**

---

### ✔️ Question 2.3 (5 points) - Envoi email récapitulatif

**Réponse attendue** :

```powershell
# À ajouter à la fin du script

# Paramètres email
$From = "admin@techsolutions.local"
$To = "rh@techsolutions.local"
$Subject = "Rapport création utilisateurs - $(Get-Date -Format 'dd/MM/yyyy')"
$SMTPServer = "smtp.techsolutions.local"  # ou "mail.techsolutions.local"
$SMTPPort = 25

# Corps de l'email
$Body = @"
Bonjour,

Le script de création d'utilisateurs s'est terminé.

=== RÉSUMÉ ===
- Utilisateurs créés avec succès : $SuccessCount
