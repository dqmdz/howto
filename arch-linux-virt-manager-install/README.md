# Instalación y Configuración de Virt Manager en Arch Linux

Esta guía detalla los pasos necesarios para instalar y dejar funcionando Virt Manager en Arch Linux.

## 1. Instalar paquetes necesarios

```sh
sudo pacman -S archlinux-keyring
sudo pacman -S virt-manager virt-viewer qemu-full vde2 ebtables nftables dnsmasq bridge-utils ovmf swtpm
sudo pacman -S openbsd-netcat iptables libguestfs
```

## 2. Configurar libvirtd

Editar el archivo de configuración:

```sh
sudo nano /etc/libvirt/libvirtd.conf
```

Buscar y descomentar (quitar el `#`) y dejar así:

```ini
# Línea 85
unix_sock_group = "libvirt"
# Línea 108
unix_sock_rw_perms = "0770"
```

Agregar tu usuario a los grupos necesarios:

```sh
sudo usermod -aG kvm,libvirt $USER
newgrp libvirt
```

Habilitar y arrancar el servicio:

```sh
sudo systemctl enable --now libvirtd.service
```

## 3. Configurar QEMU (opcional, recomendado)

Editar el archivo:

```sh
sudo nano /etc/libvirt/qemu.conf
```

Buscar y descomentar (quitar el `#`) y dejar así, reemplazando `tu-nombre-de-usuario` por tu usuario:

```ini
# Línea 557
user = "tu-nombre-de-usuario"
# Línea 562
group = "tu-nombre-de-usuario"
```

Reiniciar el servicio:

```sh
sudo systemctl restart libvirtd
```

## 4. Habilitar red por defecto

```sh
sudo virsh net-autostart default
```

---

> **Nota:** Si tienes problemas con permisos, reinicia tu sesión o el sistema para que los cambios de grupo tengan efecto.

