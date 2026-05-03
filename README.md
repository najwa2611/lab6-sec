# README – Lab d’analyse statique Android avec MobSF

## Objectif du lab
Comprendre et mettre en œuvre une analyse statique de sécurité sur une application Android (APK) à l’aide de **MobSF (Mobile Security Framework)** dans un environnement isolé (VM Mobexler).

---

## Prérequis techniques
- Mobexler VM (basée sur Ubuntu/Debian)
- APK pédagogique (`app-debug.apk` généré depuis Android Studio)
- Accès réseau à la VM (NAT ou dossier partagé)
- Docker (optionnel mais recommandé pour éviter les conflits de dépendances)

---

## Arborescence attendue du lab
```bash
~/apk_analysis/2026-05-03/
├── app-debug.apk
├── analyse_info.txt
├── apk_hash.txt
├── permissions.txt
├── composants_exportes.txt
├── config_reseau.txt
├── endpoints.txt
├── vulnerabilites.txt
├── correlation_masvs.txt
├── rapport_final.md
└── mobsf_report.pdf
```

---

## Tasks réalisées (récapitulatif)

### Task 1 – Préparation de l’environnement
```bash
mkdir -p ~/apk_analysis/$(date +%Y-%m-%d)
cd ~/apk_analysis/2026-05-03
sha256sum app-debug.apk > apk_hash.txt
```

### Task 2 – Lancement de MobSF
```bash
cd ~/tools/Mobile-Security-Framework-MobSF
./run.sh 127.0.0.1:8000
# Accès : http://127.0.0.1:8000
```

### Task 3 – Import et analyse de l’APK
- Upload de `app-debug.apk`
- Analyse automatique (2 à 10 minutes)

### Task 4 – Analyse du manifeste et des permissions
**Fichier généré :** `permissions.txt`

| Permission | Niveau | Risque |
|------------|--------|--------|
| `READ_CONTACTS` | dangerous | Élevé |
| `INTERNET` | normal | Faible |
| `ACCESS_NETWORK_STATE` | normal | Faible |

**Composants exportés :** aucun explicitement dangereux

### Task 5 – Configuration réseau
**Fichier généré :** `config_reseau.txt`

| Paramètre | Valeur | Sévérité |
|-----------|--------|-----------|
| `usesCleartextTraffic` | `true` | HIGH |
| `network_security_config.xml` | absent | Risque HTTP |
| `debuggable` | `true` | HIGH |

### Task 6 – Analyse du code
**Fichier généré :** `vulnerabilites.txt`

| Découverte | Sévérité | Référence |
|------------|----------|------------|
| IP Address disclosure | warning | CWE-200 |
| SSL Certificate Pinning | secure | MSTG-NETWORK-4 |

### Task 7 – Corrélation OWASP MASVS
**Fichier généré :** `correlation_masvs.txt`

| Vulnérabilité | Référence MASVS |
|---------------|----------------|
| Debug certificate | MSTG-CODE-8 |
| READ_CONTACTS | MSTG-STORAGE-12 |
| Cleartext traffic | MSTG-NETWORK-1 |
| IP disclosure | MSTG-CODE-2 |

## Résultats marquants de l’analyse

| Critère | État |
|---------|------|
| **Score de sécurité MobSF** | 48/100 |
| `android:debuggable` | true  HIGH |
| `usesCleartextTraffic` | true  HIGH |
| `READ_CONTACTS` | Dangereuse |
| Certificat debug | HIGH |
| IP disclosure | Warning |
| SSL Pinning | Secure |

---

## Problèmes rencontrés et solutions

| Problème | Solution |
|----------|----------|
| Python 3.7 incompatible avec MobSF | Installation manuelle de Python 3.12.13 |
| SQLite 2.27 < 3.31 | Docker recommandé (sinon recompilation SQLite + Python) |
| `./setup.sh` échoue avec Python 3.12 | Utiliser `python3.12 setup.py` |
| Erreur 500 MobSF | Configurer `DEBUG = True` dans `~/.MobSF/config.py` |

---

## Commandes utiles utilisées dans le lab

```bash
# Hash et vérification
sha256sum app-debug.apk

# Lancer MobSF (version locale)
./run.sh 127.0.0.1:8000

# Lancer MobSF avec Docker (recommandé)
docker run -it --rm -p 8000:8000 opensecurity/mobile-security-framework-mobsf:latest

# Chercher un APK
find ~ -name "*.apk" 2>/dev/null

# Vérifier la version de Python
python3.12 --version

# Vérifier SQLite dans Python
python3.12 -c "import sqlite3; print(sqlite3.sqlite_version)"
```

---

## Bonnes pratiques rappelées par le lab
- Ne jamais signer une application de production avec un certificat debug
- Désactiver `debuggable` et `usesCleartextTraffic` en release
- Limiter les permissions au strict nécessaire
- Documenter la traçabilité (hash, date, version outil)
- Toujours valider manuellement les faux positifs

---

## Références officielles utilisées
- [MobSF GitHub](https://github.com/MobSF/Mobile-Security-Framework-MobSF)
- [OWASP MASVS](https://mas.owasp.org/MASVS/)
- [OWASP MASTG](https://mas.owasp.org/MASTG/)
- [Android Security Docs](https://source.android.com/docs/security)

---

## Conclusion du lab
Le lab a permis de :
- Configurer un environnement d’analyse statique
- Identifier des vulnérabilités critiques (debug, HTTP, permissions)
- Produire un rapport d’audit structuré selon OWASP MASVS


---

Souhaitez-vous que je transforme ce README en fichier texte prêt à être copié dans votre VM ?
