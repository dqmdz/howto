# Instalar y Configurar Zsh con Oh My Zsh en Xubuntu

## 1. Instalar Zsh

```sh
sudo apt update
sudo apt install zsh -y
```

## 2. Cambiar tu shell por defecto a Zsh

```sh
chsh -s $(which zsh)
```
> Cierra la sesión y vuelve a iniciarla para que el cambio tenga efecto.

## 3. Instalar Oh My Zsh

```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## 4. Instalar los plugins

### 4.1. zsh-autosuggestions

```sh
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

### 4.2. zsh-syntax-highlighting

```sh
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

## 5. Editar el archivo de configuración con nano

Para activar los plugins y el tema robbyrussell, debes editar el archivo `~/.zshrc`. Usa el siguiente comando para abrirlo con el editor nano:

```sh
nano ~/.zshrc
```

Si necesitas moverte a tu carpeta personal antes de editar, puedes usar:

```sh
cd
nano .zshrc
```

Dentro de nano, busca la línea que comienza con `plugins=` y modifícala así:


```sh
plugins=(
  git common-aliases colored-man-pages zsh-autosuggestions zsh-syntax-highlighting
)
```


Asegúrate de que la línea del tema sea:

```sh
ZSH_THEME="robbyrussell"
```

### Guardar y salir de nano

Para guardar los cambios y salir de nano:

- Pulsa <kbd>Ctrl</kbd> + <kbd>O</kbd> para guardar (te pedirá confirmar el nombre del archivo, presiona <kbd>Enter</kbd>).
- Luego pulsa <kbd>Ctrl</kbd> + <kbd>X</kbd> para salir del editor.


## 6. Recargar la configuración

```sh
source ~/.zshrc
```

---

¡Listo! Ahora tienes Zsh con Oh My Zsh, los plugins autosuggestions y syntax-highlighting, y el tema robbyrussell configurados en Xubuntu.
