# 🚀 Guía paso a paso: Instalar y configurar Git en Mac M1/M2 (para principiantes)

Esta guía te lleva **desde cero**: abrir la Terminal, instalar Homebrew, instalar/actualizar Git, configurar tu nombre/correo, **crear y registrar una llave SSH** para no escribir contraseñas, y **probarlo con un repositorio real**. Todo con explicaciones claras, qué mensajes esperar y qué hacer si algo no sale a la primera.

> **¿Para quién es esto?** Para personas que nunca han usado la Terminal y quieren instalar Git en un Mac con chip M1/M2 (Apple Silicon) sin frustraciones.

---

## 🧭 0) Abrir la Terminal (macOS)
1. Presiona **Command (⌘) + Barra espaciadora**.
2. Se abrirá **Spotlight** (buscador del Mac).
3. Escribe **Terminal** y presiona **Enter**.

Verás una ventana con algo como:
```
usuario@MacBook ~ %
```
Ese `%` indica que la Terminal está lista para recibir comandos.

**Tips útiles:**
- En la Terminal, cuando se te pida una **contraseña**, **no se verán caracteres** (ni puntos, ni asteriscos). Es normal: escribe y presiona **Enter**.
- Para **pegar** texto en la Terminal, usa **Command (⌘) + V**.

---

## 📦 1) Instalar Homebrew (gestor de paquetes)
Homebrew facilita instalar programas como Git.

Copia y pega este comando completo en la Terminal y presiona **Enter**:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Durante la instalación:
- Puede mostrar: `Checking for sudo access (which may request your password)...` → escribe la **contraseña de tu usuario de Mac** y presiona **Enter** (no se verá mientras escribes).
- Puede pedir: `Press RETURN/ENTER to continue or any other key to abort:` → presiona **Enter** para continuar.

### Verificar Homebrew
```bash
brew --version
```

Si sale algo como `Homebrew 4.x.x`, ya está ✅.  
Si obtienes `zsh: command not found: brew`, no te preocupes. Tu Mac todavía no sabe dónde buscar Homebrew.

Para enseñarle dónde está, sigue estos pasos:

1. Copia y pega este comando en la Terminal y presiona Enter:
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
```

2. Luego copia y pega este otro comando y presiona Enter:
```bash
eval "$(/opt/homebrew/bin/brew shellenv)"
```

Ahora tu Mac ya sabe dónde encontrar Homebrew.  
Prueba de nuevo:
```bash
brew --version
```

---

## 🧰 2) Instalar y actualizar Git
Instala Git con Homebrew:
```bash
brew install git
```

Actualízalo siempre para asegurarte de tener la última versión:
```bash
brew upgrade git
```

Confirma la versión instalada:
```bash
git --version
```

👉 Verás algo como:
```
git version 2.51.0
```
La versión exacta puede variar según el momento en que instales (por ejemplo, `2.52.1`, `2.53.0`, etc.).  
**No te preocupes por el número exacto**: lo importante es que aparezca un número de versión válido (2.xx.x o superior).

> **Nota:** macOS trae una versión propia de Git (Apple Git). Para usar la de Homebrew, asegúrate de que `/opt/homebrew/bin` tiene prioridad en tu configuración. Si no, agrega esto a tu archivo `~/.zprofile`:
```bash
echo 'export PATH="/opt/homebrew/bin:$PATH"' >> ~/.zprofile
source ~/.zprofile
which git   # Debe mostrar /opt/homebrew/bin/git
```

---

## ✍️ 3) Configurar tu identidad en Git
Esto es la "firma" que quedará en tus commits (cambios) y se verá en el historial.

```bash
git config --global user.name "Patricio Nicasio Moreno Brevis"
git config --global user.email "tysontecles@gmail.com"
```

**Recomendación:** usa tu **nombre real** para que en equipos sepan quién hizo qué, y un **correo registrado en tu cuenta de GitHub** para que tus commits se asocien a tu perfil.

Verifica:
```bash
git config --list
```

---

## 🔐 4) ¿Qué es una llave SSH y por qué usarla?
- **SSH** permite que tu Mac se autentique con GitHub **sin pedir usuario/contraseña** cada vez que haces `git push` o `git pull`.
- Generas un **par de llaves**: una **privada** (se queda en tu Mac) y una **pública** (la registras en GitHub). GitHub confía en tu Mac cuando la llave coincide.

### Generar tu llave SSH (ed25519)
```bash
ssh-keygen -t ed25519 -C "tysontecles@gmail.com"
```

- Cuando pregunte la ruta, presiona **Enter** para usar la predeterminada: `~/.ssh/id_ed25519`.
- Passphrase (opcional): puedes dejarla **vacía** (solo Enter) o definir una (añade seguridad, pero la pedirá en uso).

Inicia el agente SSH y agrega tu llave:
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

Copia tu **llave pública** al portapapeles (la que termina en `.pub`):
```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

### Registrar la llave en GitHub
1. Ve a **GitHub** → **Settings** → **SSH and GPG keys** → **New SSH key**.
2. **Title**: un nombre descriptivo para identificar el equipo (solo lo ves tú). Ejemplos: `MacBook Air M1`, `MacBook de Patricio`.
3. **Key type**: deja **Authentication Key**.
4. **Key**: pega lo que copiaste (empieza con `ssh-ed25519 AAAA...`).
5. Guarda con **Add SSH key**.

### Probar la conexión SSH
```bash
ssh -T git@github.com
```
- La primera vez puede preguntar: `Are you sure you want to continue connecting (yes/no/[fingerprint])?` → escribe **yes** y Enter.
- Deberías ver: `Hi <tu_usuario>! You've successfully authenticated, but GitHub does not provide shell access.` ✅

> **No necesitas cambiar la SSH key** si cambias tu `user.name`/`user.email`. La llave identifica **tu equipo**, no tu nombre visible en los commits.

---

## 🗂️ 5) ¿Dónde se guardan mis archivos en el Mac?
Git clona (descarga) el repositorio en **la carpeta donde estés parado** en la Terminal.

### Ver tu carpeta actual
```bash
pwd
```
Muestra la **ruta** (por ejemplo: `/Users/tuusuario`).

### Ver el contenido de la carpeta
```bash
ls
```

### Abrir la carpeta actual en Finder
```bash
open .
```

### Recomendación: crea una carpeta de proyectos y clona ahí
```bash
cd ~
mkdir -p Proyectos/Git
cd Proyectos/Git
pwd   # Verás la ruta completa donde quedarían tus repos
```

Cuando clones un repo aquí, quedará como subcarpeta dentro de `Proyectos/Git`.

---

## 🌱 6) Clonar un repositorio (usando SSH)
> Asegúrate de usar la **URL SSH** del repo (formato `git@github.com:usuario/repo.git`).

```bash
cd ~/Proyectos/Git    # o la carpeta que prefieras
git clone git@github.com:usuario/nombre-del-repo.git
cd nombre-del-repo
pwd                   # ruta local del repo
```

Verifica la **URL remota** y, si hiciera falta, cámbiala a SSH:
```bash
git remote -v
# Si ves https://... y quieres SSH:
git remote set-url origin git@github.com:usuario/nombre-del-repo.git
```

---

## ✏️ 7) Cambios de prueba (commit & push)
Crea un archivo y súbelo:
```bash
echo "Hola Git desde mi Mac" >> prueba.txt
git add .
git commit -m "Agregar archivo de prueba"

# Empuja a la rama principal (ajusta si es master)
git push origin main
# o
# git push origin master
```

Ve al repo en GitHub y confirma que **prueba.txt** apareció.

---

## 🧷 8) Chuleta rápida de comandos
```bash
# Estado e historial
git status
git log --oneline --graph --decorate

# Staging & commit
git add <archivo>    # o: git add .
git commit -m "Mensaje"

# Ramas
git branch
git checkout -b nueva-rama
git checkout main    # o master
git merge nueva-rama

# Remoto
git remote -v
git push origin main # o master
git pull origin main # o master

# Rutas y Finder
pwd
ls
open .
```

---

## 🛟 9) Solución de problemas frecuentes
- **`command not found: brew`** → usa los dos comandos de la sección 1 para enseñarle a tu Mac dónde está Homebrew.
- **Sigue saliendo Git de Apple** (`/usr/bin/git`) → prioriza Homebrew:
  ```bash
  echo 'export PATH="/opt/homebrew/bin:$PATH"' >> ~/.zprofile
  source ~/.zprofile
  which git  # Debe ser /opt/homebrew/bin/git
  ```
- **SSH pide usuario/clave** → seguramente la URL remota es HTTPS:
  ```bash
  git remote -v
  git remote set-url origin git@github.com:usuario/repo.git
  ```
- **No sé qué rama principal usa mi repo**:
  ```bash
  git branch -a   # mira si es main o master
  ```
- **Quiero cambiar mi nombre/correo para un solo repo** (local, no global):
  ```bash
  git config user.name "Otro Nombre"
  git config user.email "otro@correo.com"
  ```

---

## ✨ Autor
Guía escrita por **Patricio Nicasio Moreno Brevis**.  
📧 Contacto: **tysontecles@gmail.com**  

La comparto porque sé lo difícil que puede ser comenzar sin experiencia previa. Si esta guía te ayudó y quieres colaborar conmigo en proyectos donde la **experiencia de usuario clara y detallada** sea importante, no dudes en escribirme 🙌
