# Installation de WSL + Ubuntu sur Windows 11

> Guide complet d'installation de WSL avec Ubuntu et Miniconda, basé sur un cas réel de résolution d'erreurs courantes (504 Gateway Timeout, `0x8000000d`).
>
> Complete guide to installing WSL with Ubuntu and Miniconda, based on a real-world case of resolving common errors (504 Gateway Timeout, `0x8000000d`).

---

## 🇫🇷 Version française

### Contexte

Tentative d'installation de WSL via la commande standard, échec avec une erreur **504 (Dépassement du délai de la passerelle)** :

```powershell
wsl --install
```

Erreur retournée :
```
Dépassement du délai de la passerelle (504).
```

Cette erreur indique que Windows n'a pas réussi à télécharger les composants WSL depuis les serveurs Microsoft. La solution consiste à contourner en installant les composants manuellement.

### Étape 1 — Activer les fonctionnalités Windows nécessaires

Ouvrir **PowerShell en administrateur** et exécuter :

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

Puis **redémarrer le PC**.

### Étape 2 — Installer WSL via le Microsoft Store

Plutôt que `wsl --install` (qui échoue), passer par le Store qui utilise un autre canal de téléchargement.

Ouvrir directement la page WSL dans le Store avec `Windows + R` :

```text
ms-windows-store://pdp/?productid=9P9TQF7MRM4R
```

Cliquer sur **Obtenir / Installer**.

### Étape 3 — Installer Ubuntu

Toujours via `Windows + R` :

```text
ms-windows-store://pdp/?productid=9PDXGNCFSCZV
```

Cliquer sur **Obtenir / Installer**.

### Étape 4 — Résoudre l'erreur `0x8000000d` au premier lancement

Si Ubuntu affiche au premier lancement :

```text
WslRegisterDistribution failed with error: 0x8000000d
```

#### 4.1 Vérifier la virtualisation

Ouvrir le **Gestionnaire des tâches** (`Ctrl + Maj + Échap`) → onglet **Performances** → **Processeur**. La ligne **Virtualisation** doit indiquer **Activé**. Si elle est désactivée, l'activer dans le BIOS/UEFI (option Intel VT-x, AMD-V ou SVM Mode).

#### 4.2 Mettre à jour WSL

```powershell
wsl --update
```

```powershell
wsl --set-default-version 2
```

#### 4.3 Faire un arrêt complet

Maintenir **Maj** + cliquer sur **Arrêter** depuis le menu Démarrer (un simple redémarrage ne réinitialise pas l'hyperviseur). Attendre l'extinction complète, puis rallumer.

#### 4.4 Relancer Ubuntu

Clic droit sur l'icône Ubuntu → **Exécuter en tant qu'administrateur** pour ce premier lancement uniquement.

### Étape 5 — Créer un utilisateur normal (pas root)

Une fois Ubuntu lancé, on se retrouve en `root@hostname:~#`. Il faut créer un utilisateur normal.

```bash
adduser draissa
```

(remplacer `draissa` par le nom souhaité — en minuscules, sans espace)

Saisir un mot de passe (invisible à la frappe, c'est normal) et valider les champs optionnels par **Entrée**.

### Étape 6 — Donner les droits sudo à l'utilisateur

```bash
usermod -aG sudo draissa
```

### Étape 7 — Définir l'utilisateur par défaut

Sortir de WSL :

```bash
exit
```

Dans PowerShell :

```powershell
ubuntu config --default-user draissa
```

Relancer Ubuntu — le prompt doit maintenant afficher `draissa@hostname:~$` (le `$` au lieu du `#` confirme qu'on n'est plus root).

### Étape 8 — Mettre à jour le système

```bash
sudo apt update && sudo apt upgrade -y
```

### Étape 9 (optionnel) — Installer Miniconda pour la data science

Miniconda est préférable à Anaconda complet : ~400 Mo au lieu de ~3 Go, et on installe seulement ce dont on a besoin.

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

```bash
bash Miniconda3-latest-Linux-x86_64.sh
```

Pendant l'installation : **Entrée** pour faire défiler la licence, **q** pour sortir du texte, **yes** pour accepter, **Entrée** pour l'emplacement par défaut, **yes** pour initialiser conda.

Fermer et rouvrir le terminal — le prompt doit afficher `(base)` au début.

Créer un environnement de travail :

```bash
conda create -n datascience python=3.12 numpy pandas matplotlib scikit-learn jupyter
```

```bash
conda activate datascience
```

### Lancer WSL au quotidien

Depuis PowerShell ou l'invite de commandes :

```powershell
ubuntu
```

Ou via le menu Démarrer, ou (recommandé) via **Windows Terminal** installable depuis le Microsoft Store.

### Accéder à ses fichiers

- **Fichiers Windows depuis WSL** : `/mnt/c/Users/votrenom`
- **Fichiers WSL depuis Windows** : `\\wsl$` dans l'Explorateur, ou `explorer.exe .` depuis le terminal Ubuntu

---

## 🇬🇧 English version

### Context

Attempted standard WSL install, failed with a **504 Gateway Timeout** error:

```powershell
wsl --install
```

Returned:
```
Gateway timeout (504).
```

This error means Windows couldn't download WSL components from Microsoft servers. The workaround is to install components manually.

### Step 1 — Enable required Windows features

Open **PowerShell as administrator** and run:

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

Then **restart the PC**.

### Step 2 — Install WSL via Microsoft Store

Instead of `wsl --install` (which fails), use the Store which goes through a different download channel.

Open the WSL Store page directly with `Windows + R`:

```text
ms-windows-store://pdp/?productid=9P9TQF7MRM4R
```

Click **Get / Install**.

### Step 3 — Install Ubuntu

Still with `Windows + R`:

```text
ms-windows-store://pdp/?productid=9PDXGNCFSCZV
```

Click **Get / Install**.

### Step 4 — Fix error `0x8000000d` on first launch

If Ubuntu shows on first launch:

```text
WslRegisterDistribution failed with error: 0x8000000d
```

#### 4.1 Check virtualization

Open **Task Manager** (`Ctrl + Shift + Esc`) → **Performance** tab → **CPU**. The **Virtualization** line must say **Enabled**. If disabled, enable it in BIOS/UEFI (Intel VT-x, AMD-V or SVM Mode option).

#### 4.2 Update WSL

```powershell
wsl --update
```

```powershell
wsl --set-default-version 2
```

#### 4.3 Perform a full shutdown

Hold **Shift** + click **Shut down** from Start menu (a regular restart does not reset the hypervisor). Wait until fully off, then power back on.

#### 4.4 Relaunch Ubuntu

Right-click Ubuntu icon → **Run as administrator** for this first launch only.

### Step 5 — Create a regular user (not root)

Once Ubuntu launches, you land in `root@hostname:~#`. Create a normal user.

```bash
adduser draissa
```

(replace `draissa` with your chosen name — lowercase, no spaces)

Enter a password (invisible while typing, this is normal) and press **Enter** to skip optional fields.

### Step 6 — Grant sudo rights to the user

```bash
usermod -aG sudo draissa
```

### Step 7 — Set as default user

Exit WSL:

```bash
exit
```

In PowerShell:

```powershell
ubuntu config --default-user draissa
```

Relaunch Ubuntu — the prompt should now show `draissa@hostname:~$` (the `$` instead of `#` confirms you're no longer root).

### Step 8 — Update the system

```bash
sudo apt update && sudo apt upgrade -y
```

### Step 9 (optional) — Install Miniconda for data science

Miniconda is preferable to full Anaconda: ~400 MB vs ~3 GB, and you only install what you need.

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

```bash
bash Miniconda3-latest-Linux-x86_64.sh
```

During installation: **Enter** to scroll the license, **q** to exit the text, **yes** to accept, **Enter** for default location, **yes** to initialize conda.

Close and reopen the terminal — the prompt should show `(base)` at the start.

Create a working environment:

```bash
conda create -n datascience python=3.12 numpy pandas matplotlib scikit-learn jupyter
```

```bash
conda activate datascience
```

### Daily WSL usage

From PowerShell or Command Prompt:

```powershell
ubuntu
```

Or via Start menu, or (recommended) via **Windows Terminal** installable from Microsoft Store.

### Accessing files

- **Windows files from WSL**: `/mnt/c/Users/yourname`
- **WSL files from Windows**: `\\wsl$` in Explorer, or `explorer.exe .` from Ubuntu terminal

---

## 📝 Notes

- The proxy warning `WSL en mode NAT ne prend pas en charge les proxys localhost` is informational only and does not block usage.
- GitHub automatically renders **copy buttons** on every code block in this README — no extra JavaScript needed.
- Tested on Windows 11, May 2026.# install-WSL-Ubuntu-conda
