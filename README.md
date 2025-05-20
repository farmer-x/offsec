## Offsec HTB Setup

### Workstation Setup

##### Rufus (Make a bootable USB drive):
- https://rufus.ie/en/

For the Zbook Hacktop - be sure to enable persistent storage if you want to save files on the USB drive.
- **Partition scheme**: GPT
- **Target system**: UEFI (non CSM)
- **File system**: FAT32
- **Cluster size**: 4096 bytes
- **Volume label**: Kali

_Alternatively, you can look into using [Ventoy](https://www.ventoy.net/en/index.html) to create a bootable USB drive with multiple ISO files. This can be useful for testing different distributions or versions of Kali Linux without needing to reformat the USB drive each time._


##### Kali Live vs Installer:

Be sure to choose the Kali install file that works best for you.  Kali installer installs Kali on your hard drive. Kali live runs from a USB drive.

For the Zbook Hacktop, I recommend using the [Kali Installer](https://www.kali.org/get-kali/#kali-installer-images)  This will allow you to install Kali on the hard drive. 

#### Supporting tools


##### Workspace

   NOTE: Do not use pip install - [Kali-PIP](https://www.kali.org/docs/general-use/python3-external-packages/)

VS Code https://code.visualstudio.com/docs/setup/linux#_install-vs-code-on-linux

    10  sudo chmod 777 code_1.100.1-1746807090_amd64.deb
    11  sudo apt install ./code_1.100.1-1746807090_amd64.deb
```
Powerline fonts:  
`sudo apt install powerline`
`fc-cache -f -v` 

   NOTE: I prefer the Agnoster theme.  

##### Tools

Burpsuite:   
`sudo apt install burpsuite` 


Chrome (used to join MS Teams meetings)   
```
   36  wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
   37  sudo dpkg -i google-chrome-stable_current_amd64.deb
   38  sudo apt install -f
```

Gnome Keyring (supports co pilot)   
```
   24  sudo apt install gnome-keyring
   25  gnome-keyring-daemon --start --components=pkcs11,secrets,ssh,gpg
   26  sudo apt install libsecret-1-0 libsecret-1-dev
```



Transfer findings through SSH or other encrypted connection. 
I personally would record all my notes using Obsidian locally to my HT, and use SSH FS to access those MD files from my OPR. This way if I have asset info on my OPR in the onenote that Steven was talking about, I can easily trasnfer to HT & vice versa
