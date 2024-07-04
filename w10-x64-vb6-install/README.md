# Install Visual Basic 6 Enterprise Edition on Windows 10 x64

This is a step by step guide to install Visual Basic 6 Enterprise Edition on Windows 10 x64.

## Prerequisites

- Windows 10 x64
- vse06e_01.zip

### Uncompress vse06e_01.zip

```sh
$ extract vse06e_01.zip
```

### Run once setup.exe as administrator

```sh
$ run setup.exe as administrator
```
- Click "Next" until you get id number
- Put id number in text box
- Click "Next" until you get reboot window
- Wait for reboot

### Install additional components
- Accept every additional component that your Windows 10 installation asks you to install
- Try to install Visual Studio 6 as usual
- Wait for error message
- Accept every error message

### Move files to installer directory root
- Rename vs98ent.stf to ACMSETUP.stf in Setup directory
```sh
$ mv vs98ent.stf ACMSETUP.stf
```
- Copy every file from Setup directory to root directory
```sh
$ cp * ..
```
- Run acmsetup.exe as administrator at root directory
```sh
$ run acmsetup.exe as administrator
```
- Install Visual Studio 6 Enterprise Edition as usual, unselect "Acceso a datos"
- Wait for finish installation

### Change file properties
- Go to Visual Basic 6 Enterprise Edition installation directory
```sh
$ cd C:\Program Files (x86)\Microsoft Visual Studio\VB98
```
- Change properties of VB6.exe to allow execute as administrator every time

