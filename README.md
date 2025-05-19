## offsec

### Workstation Setup

Rufus

Kali Live vs Installer

#### Supporting tools

VS Code https://code.visualstudio.com/docs/setup/linux#_install-vs-code-on-linux

    10  sudo chmod 777 code_1.100.1-1746807090_amd64.deb
    11  sudo apt install ./code_1.100.1-1746807090_amd64.deb



Burpsuite: sudo apt install burpsuite

Chrome/Teams

   36  wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
   37  sudo dpkg -i google-chrome-stable_current_amd64.deb
   38  sudo apt install -f

Gnome Keyring (supports co pilot)
   24  sudo apt install gnome-keyring
   25  gnome-keyring-daemon --start --components=pkcs11,secrets,ssh,gpg
   26  sudo apt install libsecret-1-0 libsecret-1-dev




Transfer findings through SSH or other encrypted connection. 
I personally would record all my notes using Obsidian locally to my HT, and use SSH FS to access those MD files from my OPR. This way if I have asset info on my OPR in the onenote that Steven was talking about, I can easily trasnfer to HT & vice versa
