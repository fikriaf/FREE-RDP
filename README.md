# Tutorial Membuat RDP Gratis Menggunakan GitHub Actions

## Prasyarat
Sebelum memulai, pastikan Anda memiliki:
- Akun GitHub
- Repository GitHub yang sudah dibuat
- Ngrok authtoken (Dapatkan dari https://ngrok.com/)

## Langkah-langkah

### 1. Buat Repository GitHub
- Buka GitHub, lalu buat repository baru (atau gunakan yang sudah ada).
- Tambahkan file `.yml` untuk konfigurasi GitHub Actions.

### 2. Menyiapkan Secret Ngrok
- Masuk ke repository GitHub Anda.
- Klik **Settings** > **Secrets and variables** > **Actions**.
- Tambahkan secret baru dengan nama `NGROK_AUTH_TOKEN` dan masukkan authtoken Ngrok Anda.

### 3. Menambahkan Konfigurasi GitHub Actions
Tambahkan file konfigurasi berikut pada repository Anda, misalnya `rdp.yml`, dalam direktori `.github/workflows/`:

```yml
name: CI

on: [push, workflow_dispatch]

jobs:
  build:
    runs-on: windows-latest

    steps:
    # Step untuk mengunduh dan menginstal ngrok seperti yang sudah ada
    - name: Download
      run: Invoke-WebRequest https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-windows-amd64.zip -OutFile ngrok.zip
    - name: Extract
      run: Expand-Archive ngrok.zip
    - name: Auth
      run: .\ngrok\ngrok.exe authtoken $Env:NGROK_AUTH_TOKEN
      env:
        NGROK_AUTH_TOKEN: ${{ secrets.NGROK_AUTH_TOKEN }}
    - name: Enable TS
      run: Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
    - run: Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
    - run: Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1
    - run: Set-LocalUser -Name "runneradmin" -Password (ConvertTo-SecureString -AsPlainText "P@ssw0rd!" -Force)

    - name: Install Git
      shell: cmd
      run: choco install git -y
      
    - name: Install Python 3.10
      shell: cmd
      run: |
        choco install python --version=3.10 -y
        refreshenv
    - name: Install Python Modules
      shell: cmd
      run: |
        python -m pip install selenium webdriver-manager pyautogui keyboard
    # Step untuk menginstal VSCode (opsional jika hanya perlu Python dan pip)
    - name: Install Visual Studio Code
      run: |
        choco install vscode -y
    - name: RDP SUCCESSFUL RUNNING...
      run: .\ngrok\ngrok.exe tcp 3389
```
## 4. Menjalankan Workflow
- Setelah konfigurasi di atas selesai, commit dan push perubahan ke repository Anda.
- Buka tab Actions pada repository Anda, lalu jalankan workflow dengan memilih Run workflow (jika menggunakan workflow_dispatch).
## 5. Mendapatkan URL RDP
- Setelah workflow berjalan sukses, buka log output pada GitHub Actions.
- Cari URL dari Ngrok (misalnya: tcp://0.tcp.ngrok.io:xxxxx).
- Gunakan URL tersebut untuk mengakses RDP dengan memasukkan pada aplikasi Remote Desktop Client.
## 6. Login ke RDP
- Gunakan username runneradmin.
- Password default adalah P@ssw0rd!. Pastikan untuk mengganti password ini setelah berhasil login.

# Selesai!
## Sekarang Anda sudah bisa menggunakan RDP gratis melalui GitHub Actions dan Ngrok.

---
---

# Tutorial on Creating Free RDP Using GitHub Actions

## Prerequisites
Before you start, make sure you have:
- GitHub account
- Created GitHub repository
- Ngrok authtoken (Get it from https://ngrok.com/)

## Steps

### 1. Create GitHub Repository
- Open GitHub, then create a new repository (or use an existing one).
- Add a `.yml` file for GitHub Actions configuration.

### 2. Setting Up Ngrok Secret
- Log in to your GitHub repository.
- Click **Settings** > **Secrets and variables** > **Actions**.
- Add a new secret named `NGROK_AUTH_TOKEN` and enter your Ngrok authtoken.

### 3. Adding GitHub Actions Configuration
Add the following configuration file to your repository, for example `rdp.yml`, in the `.github/workflows/` directory:

```yml
name: CI

on: [push, workflow_dispatch]

jobs:
build:
runs-on: windows-latest

steps:
# Steps to download and install ngrok as is
- name: Download
run: Invoke-WebRequest https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-windows-amd64.zip -OutFile ngrok.zip
- name: Extract
run: Expand-Archive ngrok.zip
- name: Auth
run: .\ngrok\ngrok.exe authtoken $Env:NGROK_AUTH_TOKEN
env: 
NGROK_AUTH_TOKEN: ${{ secrets.NGROK_AUTH_TOKEN }}
 - name: Enable TS
 run: Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
 - run: Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
 - run: Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1
 - run: Set-LocalUser -Name "runneradmin" -Password (ConvertTo-SecureString -AsPlainText "P@ssw0rd!" -Force)

 - name: Install Git
 shell: cmd
 run: choco install git -y

 - name: Install Python 3.10
shell: cmd
run: |
choco install python --version=3.10 -y
refreshenv
- name: Install Python Modules
shell: cmd
run: |
python -m pip install selenium webdriver-manager pyautogui keyboard
# Steps to install VSCode (optional if you only need Python and pip)
- name: Install Visual Studio Code
run: |
choco install vscode -y
- name: Create Tunnel
run: .\ngrok\ngrok.exe tcp 3389
```
## 4. Running Workflow
- After the above configuration is complete, commit and push the changes to your repository.
- Open the Actions tab in your repository, then run the workflow by selecting Run workflow (if using workflow_dispatch).
## 5. Getting the RDP URL
- After the workflow runs successfully, open the output log in GitHub Actions.
- Find the URL from Ngrok (for example: tcp://0.tcp.ngrok.io:xxxxx).
- Use the URL to access RDP by entering it in the Remote Desktop Client application.
## 6. Login to RDP
- Use the username runneradmin.
- The default password is P@ssw0rd!. Make sure to change this password after successfully logging in.

# Done!
## Now you can use free RDP via GitHub Actions and Ngrok.
