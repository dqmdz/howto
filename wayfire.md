# Guía Definitiva: Instalar y Configurar Wayfire en Arch Linux Minimal

Este tutorial detalla el proceso completo para instalar un escritorio Wayfire funcional y moderno en una instalación mínima de Arch Linux. La guía está "probada en batalla" e incluye soluciones a problemas comunes (y no tan comunes) relacionados con repositorios, permisos y configuración.

## Requisitos Previos

* Una instalación base de Arch Linux con conexión a internet.
* Un usuario estándar con privilegios de `sudo`.
* Acceso a la terminal de texto (TTY).

## Paso 1: Preparación del Sistema y Controladores

Primero, prepararemos el sistema base y nos aseguraremos de tener los controladores gráficos correctos.

### 1.1. Actualización del Sistema

Como siempre en Arch, lo primero es asegurarse de que todo esté actualizado.

```bash
sudo pacman -Syu
```

### 1.2. Instalación de Controladores de Vídeo

Wayland depende críticamente de los drivers correctos.

* **Para Gráficos Intel (como en nuestro caso):**
    ```bash
    sudo pacman -S mesa libva-intel-driver vulkan-intel
    ```
* **Para Gráficos AMD:**
    ```bash
    sudo pacman -S mesa xf86-video-amdgpu vulkan-radeon
    ```
## Paso 2: La Batalla con `pacman` - Repositorios y Mirrors

A menudo, el primer gran obstáculo en una instalación mínima es que `pacman` no encuentra paquetes como `wayfire`. Esto se debe a dos posibles causas que solucionaremos ahora.

### 2.1. Habilitar el Repositorio `[extra]`

El repositorio `[extra]` contiene la mayoría de paquetes de escritorio, pero puede venir desactivado.

1.  Edita el archivo de configuración de `pacman`:
    ```bash
    sudo nano /etc/pacman.conf
    ```
2.  Busca la sección `[extra]` y asegúrate de que no tenga almohadillas (`#`) delante. Debe verse así:
    ```ini
    [extra]
    Include = /etc/pacman.d/mirrorlist
    ```
3.  Guarda y cierra el archivo (`Ctrl+X`, `Y`, `Enter`).

### 2.2. Actualizar la Lista de Servidores (Mirrors)

Una lista de mirrors desactualizada puede causar errores. La mejor forma de solucionarlo es con `reflector`.

1.  Instala `reflector`:
    ```bash
    sudo pacman -S reflector
    ```
2.  Genera una nueva lista de mirrors optimizada para tu región (ejemplo para Argentina y países vecinos):
    ```bash
    sudo reflector --verbose --country 'Argentina','Chile','Brazil' --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
    ```
3.  Fuerza una resincronización completa de la base de datos de `pacman`:
    ```bash
    sudo pacman -Syyu
    ```
## Paso 3: Instalación del Entorno Gráfico

Con `pacman` ya sano, instalamos Wayfire y todas las herramientas necesarias de una sola vez. Incluimos `seatd` e `imagemagick` que aprendimos que son necesarios para evitar problemas más adelante.

```bash
sudo pacman -S wayfire waybar wofi foot swaybg swaylock mako grim slurp polkit-kde-agent pipewire pipewire-pulse wireplumber ttf-nerd-fonts-symbols ttf-dejavu noto-fonts xorg-xwayland seatd imagemagick
```
## Paso 4: Gestión de Permisos (La Clave del Éxito)

Este es el paso más crítico. Para que Wayfire pueda controlar tu pantalla y teclado desde la TTY, necesita permisos. Usaremos `seatd`.

1.  **Habilita el servicio `seatd`** para que se inicie con el sistema:
    ```bash
    sudo systemctl enable --now seatd.service
    ```
2.  **Añade tu usuario a los grupos `seat` y `video`**. Esto le dará los permisos necesarios. Reemplaza `tusuario` con tu nombre de usuario real.
    ```bash
    sudo usermod -aG seat,video tusuario
    ```
3.  **¡REINICIA EL SISTEMA!** Este paso es **OBLIGATORIO**. Los cambios de grupo no se aplican hasta que cierras sesión y vuelves a entrar. Reiniciar es la forma más segura de hacerlo.
    ```bash
    reboot
    ```
## Paso 5: Configuración del Escritorio

Después de reiniciar, inicia sesión en tu TTY y procede a crear los archivos de configuración.

### 5.1. Crear Directorios
```bash
mkdir -p ~/.config/wayfire ~/.config/waybar ~/Pictures
```

### 5.2. Crear Fondo de Pantalla Localmente
Para evitar problemas de red, crearemos nuestra propia imagen de fondo.
```bash
convert -size 1366x768 canvas:'#333333' ~/Pictures/wallpaper.png
```

### 5.3. Crear el Archivo `wayfire.ini`
Crea el archivo `~/.config/wayfire.ini` y pega el siguiente contenido.

```bash
nano ~/.config/wayfire.ini
```

```ini
[core]
# IMPORTANTE: Hemos quitado "command" de esta lista de plugins
plugins = grid move resize expo scale vswitch place autostart
outputs = eDP-1

[autostart]
bar = waybar
background = swaybg -i ~/Pictures/wallpaper.png
polkit = /usr/lib/polkit-kde-authentication-agent-1
notifications = mako

[input]
xkb_layout = es # Cambia a "us" si tienes teclado en inglés
repeat_rate = 25
repeat_delay = 600

[input-device]
# Atajo para cerrar Wayfire y volver a la terminal
kb_combo_super_shift_q = activate
super_key_action_super_shift_q = execute killall -w wayfire

# Atajo para la terminal
kb_combo_super_key_enter = activate
super_key_action_super_key_enter = execute foot

# Atajo para el lanzador de aplicaciones
kb_combo_super_d = activate
super_key_action_super_d = execute wofi --show drun

[grid]
slot_up = <super> KEY_UP
slot_down = <super> KEY_DOWN
slot_left = <super> KEY_LEFT
slot_right = <super> KEY_RIGHT

[move]
activate = <super> BTN_LEFT

[resize]
activate = <super> BTN_RIGHT

[expo]
toggle = <super> KEY_E

[scale]
toggle = <super> KEY_S

[vswitch]
binding_left = <super> <ctrl> KEY_LEFT
binding_right = <super> <ctrl> KEY_RIGHT
with_wheel = 1

[close]
binding = <super> KEY_Q
```

### 5.4. Crear la Configuración de `waybar`
Crea los dos archivos para la barra de estado.

1.  **`~/.config/waybar/config`**:
    ```bash
    nano ~/.config/waybar/config
    ```
```json
{
        "layer": "top",
        "position": "top",
        "modules-left": ["wayfire/workspaces"],
        "modules-center": ["clock"],
        "modules-right": ["pulseaudio", "network", "cpu", "memory", "tray"],
        "wayfire/workspaces": { "format": "{name}" },
        "tray": { "icon-size": 18, "spacing": 10 },
        "clock": { "format": "{:%d/%m %H:%M}" },
        "cpu": { "format": "CPU {usage}% " },
        "memory": { "format": "MEM {}% " },
        "network": {
            "format-wifi": "{essid} ({signalStrength}%) ",
            "format-disconnected": "Desconectado ⚠"
        },
        "pulseaudio": { "format": "{volume}% {icon}", "format-muted": "Mute " }
    }
```
2.  **`~/.config/waybar/style.css`**:
    ```bash
    nano ~/.config/waybar/style.css
    ```
    ```css
    * {
        border: none;
        border-radius: 0;
        font-family: DejaVu Sans, Nerd Fonts;
        font-size: 14px;
    }
    window#waybar {
        background: rgba(40, 40, 40, 0.6);
        color: #d8dee9;
    }
    #workspaces button {
        padding: 0 10px;
        background: transparent;
        border-bottom: 3px solid transparent;
    }
    #workspaces button.active {
        color: #ffffff;
        border-bottom: 3px solid #88c0d0;
    }
    #pulseaudio, #network, #cpu, #memory, #tray, #clock { padding: 0 10px; }
    ```

## Paso 6: El Lanzamiento y Uso Básico

¡Todo está listo!

1.  **Para iniciar Wayfire**, ejecuta desde la TTY:
    ```bash
    dbus-launch wayfire
    ```
    *(Recuerda: `wayfire` en minúsculas)*.

2.  **Atajos Esenciales:**
    * `Super + Enter`: Abrir terminal (`foot`)
    * `Super + D`: Abrir lanzador de aplicaciones (`wofi`)
    * `Super + Q`: Cerrar la ventana activa

3.  **Para salir de Wayfire** y volver a la terminal:
    * `Super + Shift + Q`

## Conclusión

¡Felicidades! Ahora tienes un escritorio Wayfire rápido, moderno y altamente personalizable, construido desde la base. Has superado los obstáculos más comunes y ahora tienes un sistema sólido. ¡Disfrútalo!
