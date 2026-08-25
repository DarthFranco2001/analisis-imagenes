# Guía para la reunión — Entorno colaborativo de Análisis de Imágenes

Esta guía prepara las computadoras de **Franco, Emily y Carolina** para trabajar sobre el mismo repositorio con VS Code, GitHub, `uv`, Python y Jupyter.

Repositorio del equipo:

[github.com/DarthFranco2001/analisis-imagenes](https://github.com/DarthFranco2001/analisis-imagenes)

## Resultado esperado

Al terminar la reunión:

- Los tres tendrán una cuenta de GitHub y acceso de escritura al repositorio.
- Los tres usarán VS Code, Git y `uv`.
- Los tres tendrán Python **3.13.15**.
- Las dependencias tendrán las versiones exactas registradas en `uv.lock`.
- VS Code ejecutará los notebooks con `.venv` como kernel.
- Emily y Carolina habrán creado, publicado y fusionado una contribución de prueba.
- Los tres conocerán el procedimiento para hacer Pull, Commit y Push sin sobrescribir el trabajo de otra persona.

## Versiones acordadas

```text
uv:      0.12.5
Python:  3.13.15
```

Dependencias iniciales ya registradas en `uv.lock`:

```text
Jupyter
IPyKernel
NumPy
Matplotlib
Pillow
OpenCV para Python
```

No es necesario que VS Code tenga exactamente la misma versión en las tres computadoras. Lo que afecta la ejecución está fijado mediante:

- `.python-version`: Python 3.13.15.
- `pyproject.toml`: versión requerida de Python, `uv` y dependencias directas.
- `uv.lock`: versiones exactas y resolución para macOS y Windows.

## Roles

### Franco — administrador

- Administra el repositorio y los permisos.
- Añade o elimina dependencias con `uv add` y `uv remove`.
- Revisa y fusiona las contribuciones.
- Resuelve conflictos de Git.

### Emily y Carolina — colaboradoras

- Pueden crear ramas, commits y contribuciones.
- Ejecutan `uv sync --locked` para instalar o actualizar su entorno.
- No usan `pip install` ni modifican manualmente `uv.lock`.
- Avisan a Franco cuando una tarea requiere una dependencia nueva.

## 0. Preparación de Franco antes de la reunión

### 0.1 Verificar el repositorio

Desde `/Users/castrofr/University/analisis-imagenes`:

```bash
git status
git log -2 --oneline
uv --version
uv lock --check
uv run python --version
```

Resultados esperados:

```text
nothing to commit, working tree clean
uv 0.12.5
Python 3.13.15
```

En GitHub deben existir:

```text
.python-version
pyproject.toml
uv.lock
.gitignore
.gitattributes
.vscode/extensions.json
.vscode/settings.json
README.md
```

No debe existir `.venv/` en GitHub.

### 0.2 Consideraciones del repositorio público

El repositorio es público:

- Cualquier persona puede verlo, clonarlo o hacer un fork.
- Solo Franco, Emily y Carolina podrán hacer Push después de ser añadidos como colaboradores.
- No publicar enlaces privados, tokens, contraseñas, datos personales ni el enlace/passcode de Zoom.
- No publicar material del profesor o soluciones si no existe autorización académica.
- Compartir los accesos del curso mediante un canal privado, no mediante `recursos.md` dentro de este repositorio.

Si otros grupos quieren reutilizar solamente la configuración, más adelante conviene crear un repositorio público separado llamado `analisis-imagenes-template`.

### 0.3 Pedir información antes de la reunión

Pedir a Emily y Carolina:

- Su nombre de usuario exacto de GitHub.
- Confirmación de que pueden acceder al correo asociado.
- Arquitectura de su computadora:
  - **Emily:** menú Apple → **Acerca de esta Mac** → comprobar si dice chip Apple o procesador Intel.
  - **Carolina:** Configuración → Sistema → Acerca de → comprobar si dice x64 o ARM64.

## 1. Crear y verificar las cuentas de GitHub

Si todavía no tienen cuenta:

1. Abrir [github.com/signup](https://github.com/signup).
2. Crear una cuenta personal gratuita.
3. Elegir un nombre de usuario reconocible.
4. Verificar la dirección de correo.
5. Recomendado: activar autenticación de dos factores y guardar los códigos de recuperación.

Cada persona debe usar su propia cuenta. No compartir contraseñas ni usar la cuenta de Franco.

## 2. Dar acceso de escritura a Emily y Carolina

Este bloque lo hace Franco:

1. Abrir el [repositorio](https://github.com/DarthFranco2001/analisis-imagenes).
2. Entrar a **Settings**.
3. En la sección de acceso, abrir **Collaborators**.
4. Presionar **Add people**.
5. Buscar el usuario exacto de Emily y enviar la invitación.
6. Repetir con Carolina.

Emily y Carolina deben:

1. Abrir el correo o las notificaciones de GitHub.
2. Abrir la invitación.
3. Presionar **Accept invitation**.

Aunque el repositorio sea público y puedan clonarlo sin invitación, necesitan aceptar esta invitación para poder publicar ramas y commits.

## 3. Instalar Visual Studio Code

Descarga oficial: [code.visualstudio.com/Download](https://code.visualstudio.com/Download)

### En la Mac de Emily

1. Descargar el `.dmg` **Universal**.
2. Abrirlo y arrastrar **Visual Studio Code.app** a **Aplicaciones**.
3. Abrir VS Code desde Aplicaciones.
4. Dentro de VS Code, presionar `Cmd+Shift+P`.
5. En el cuadro superior —no en Terminal— ejecutar:

   ```text
   Shell Command: Install 'code' command in PATH
   ```

6. Cerrar y abrir Terminal.

### En Windows de Carolina

1. Descargar **User Installer**, normalmente x64.
2. Ejecutar el instalador.
3. Conservar activada **Add to PATH**.
4. También se pueden activar **Open with Code** y el registro de tipos de archivo.
5. Finalizar y abrir un PowerShell nuevo.

### Verificación en ambas computadoras

```text
code --version
```

Debe mostrar una versión y la arquitectura. Si el comando no funciona en Mac, se puede abrir una carpeta con:

```bash
open -a "Visual Studio Code" .
```

## 4. Instalar Git

VS Code proporciona la interfaz visual, pero necesita una instalación local de Git.

### Mac — Terminal

Comprobar primero:

```bash
git --version
```

Si no está instalado:

```bash
xcode-select --install
```

Aceptar la ventana, esperar y abrir una Terminal nueva.

### Windows — PowerShell

```powershell
winget install --id Git.Git -e --source winget
```

Si `winget` no funciona, descargar Git desde [git-scm.com/install/windows](https://git-scm.com/install/windows). En la opción de `PATH`, elegir **Git from the command line and also from 3rd-party software**.

### Verificación

```text
git --version
```

### Configurar la identidad personal

Cada integrante ejecuta sus propios datos en Terminal o PowerShell:

```text
git config --global user.name "Nombre Apellido"
git config --global user.email "correo-verificado-en-github@ejemplo.com"
```

Verificar:

```text
git config --global --get user.name
git config --global --get user.email
```

No copiar el nombre o correo de Franco. El correo debe estar verificado en la cuenta correspondiente de GitHub.

## 5. Instalar `uv` 0.12.5

No instalar Anaconda, `pip` ni Homebrew para este proyecto. `uv` administrará el entorno.

### Mac — Terminal

```bash
curl -LsSf https://astral.sh/uv/0.12.5/install.sh | sh
```

### Windows — PowerShell

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/0.12.5/install.ps1 | iex"
```

Después de instalar, cerrar por completo VS Code y la terminal. Abrir una terminal nueva y ejecutar:

```text
uv --version
```

Debe mostrar:

```text
uv 0.12.5
```

No ejecutar `uv self update` individualmente. Las actualizaciones se acuerdan y realizan en las tres computadoras.

### Excepción: Mac Intel o Windows ARM64

`uv` 0.12.5 puede descargar Python 3.13.15 para Mac Apple Silicon y Windows x64. Si Emily tiene Mac Intel o Carolina tiene Windows ARM64:

1. Abrir [Python 3.13.15](https://www.python.org/downloads/release/python-31315/).
2. Descargar el instalador correspondiente:
   - Mac Intel: **macOS installer**.
   - Windows ARM64: **Windows installer (ARM64)**.
3. Instalar Python 3.13.15 con las opciones predeterminadas.

`uv` seguirá creando `.venv` y administrando todas las dependencias.

## 6. Instalar las extensiones de VS Code

Abrir VS Code → **Extensions** e instalar las extensiones de Microsoft:

- **Python**, identificador `ms-python.python`.
- **Jupyter**, identificador `ms-toolsai.jupyter`.

También se pueden instalar desde la terminal:

```text
code --install-extension ms-python.python
code --install-extension ms-toolsai.jupyter
```

Verificar:

- **Mac:**

  ```bash
  code --list-extensions | grep -E 'ms-python.python|ms-toolsai.jupyter'
  ```

- **Windows PowerShell:**

  ```powershell
  code --list-extensions | Select-String 'ms-python.python|ms-toolsai.jupyter'
  ```

El repositorio contiene `.vscode/extensions.json`, por lo que VS Code también puede mostrar una recomendación automática de instalación.

## 7. Clonar el repositorio

Cada integrante tendrá su propia copia local. No se comparte `.venv` ni se trabaja directamente sobre una carpeta de otra persona.

### Crear una carpeta padre

Crear con Finder o Explorador de archivos:

- Mac: `Documentos/University` o `Documentos/Universidad`.
- Windows: `Documentos\University` o `Documentos\Universidad`.

No crear manualmente una subcarpeta llamada `analisis-imagenes`; Git la creará.

### Clonar desde VS Code

1. Abrir VS Code sin una carpeta abierta.
2. Abrir la paleta:
   - Mac: `Cmd+Shift+P`.
   - Windows: `Ctrl+Shift+P`.
3. Ejecutar **Git: Clone**.
4. Pegar:

   ```text
   https://github.com/DarthFranco2001/analisis-imagenes.git
   ```

5. Elegir la carpeta padre creada anteriormente.
6. Presionar **Open**.
7. Confirmar **Yes, I trust the authors** después de comprobar que es el repositorio correcto.

Cuando VS Code pregunte si puede ejecutar periódicamente `git fetch`, seleccionar **Yes**. `fetch` consulta cambios remotos, pero no modifica los archivos locales.

### Verificar el clonado

Abrir **Terminal → New Terminal**:

```text
git status
git remote -v
```

Debe mostrar la rama `main` y el repositorio de `DarthFranco2001` como `origin`.

## 8. Construir el entorno idéntico

Emily y Carolina no ejecutan `uv init`, `uv python pin` ni `uv add`. Esos pasos ya fueron realizados por Franco y están registrados en GitHub.

Desde la raíz de `analisis-imagenes`:

```text
uv sync --locked
```

Este comando:

- Lee `.python-version`.
- Instala o localiza Python 3.13.15.
- Crea `.venv` localmente.
- Instala exactamente lo registrado en `uv.lock`.

Verificar:

```text
uv --version
uv run python --version
uv run python -c "import cv2, numpy, matplotlib, PIL; print('Entorno correcto')"
uv tree --depth 1
```

Resultados clave:

```text
uv 0.12.5
Python 3.13.15
Entorno correcto
```

Las dependencias directas deben coincidir con las del equipo. Algunas bibliotecas internas específicas del sistema operativo pueden diferir; `uv.lock` controla esas variantes.

Comprobar que `.venv` está ignorado:

```text
git check-ignore -v .venv/
git status
```

`.venv/` no debe aparecer como archivo nuevo.

## 9. Seleccionar el intérprete y el kernel

Estas acciones se realizan dentro de la interfaz de VS Code, no en la terminal.

### Seleccionar el intérprete

1. Abrir la paleta con `Cmd+Shift+P` o `Ctrl+Shift+P`.
2. Ejecutar **Python: Select Interpreter**.
3. Elegir la opción cuya ruta contenga:
   - Mac: `.venv/bin/python`.
   - Windows: `.venv\Scripts\python.exe`.

### Crear una prueba local

1. Crear `verificacion_local.ipynb` en la raíz.
2. Ese nombre está en `.gitignore`, por lo que no se publicará.
3. En la esquina superior derecha del notebook, seleccionar:

   ```text
   Select Kernel
   → Select Another Kernel, si aparece
   → Python Environments
   → .venv (Python 3.13.15)
   ```

4. Ejecutar esta celda:

```python
import sys
import cv2
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
import PIL

print("Python:", sys.version)
print("OpenCV:", cv2.__version__)
print("NumPy:", np.__version__)
print("Matplotlib:", matplotlib.__version__)
print("Pillow:", PIL.__version__)

x = np.linspace(0, 1, 256)
imagen = np.outer(x, x)
plt.imshow(imagen, cmap="gray")
plt.title("Entorno configurado correctamente")
plt.axis("off")
plt.show()
```

Debe mostrar las versiones y una imagen en escala de grises.

Después:

```text
git status
```

`verificacion_local.ipynb` no debe aparecer.

## 10. Primera contribución de Emily y Carolina

Esta prueba comprueba permisos, autenticación, ramas, commits y Push sin modificar una tarea real.

### Emily

1. En la esquina inferior izquierda de VS Code, presionar el nombre de la rama `main`.
2. Elegir **Create new branch**.
3. Escribir:

   ```text
   emily/configuracion-inicial
   ```

4. Crear la carpeta `equipo` y dentro el archivo `emily.md`:

   ```markdown
   # Emily

   Entorno de desarrollo verificado correctamente.
   ```

5. Guardar.
6. Abrir **Source Control**.
7. Revisar que el único archivo nuevo sea `equipo/emily.md`.
8. Escribir el mensaje:

   ```text
   Verificar entorno de Emily
   ```

9. Presionar **Commit** y confirmar el archivo.
10. Presionar **Publish Branch**.
11. Completar el inicio de sesión de GitHub en el navegador si VS Code lo solicita.

### Carolina

Repetir el mismo proceso con:

```text
Rama:   carolina/configuracion-inicial
Archivo: equipo/carolina.md
Commit: Verificar entorno de Carolina
```

Contenido:

```markdown
# Carolina

Entorno de desarrollo verificado correctamente.
```

### Franco

Franco también puede crear:

```text
Rama:   franco/configuracion-inicial
Archivo: equipo/franco.md
Commit: Verificar entorno de Franco
```

### Crear y fusionar las contribuciones

Después de publicar cada rama:

1. Abrir el repositorio en GitHub.
2. GitHub normalmente mostrará **Compare & pull request**.
3. Crear el Pull Request hacia `main`.
4. Franco revisa los archivos.
5. Franco presiona **Merge pull request** y confirma.
6. Después de fusionar ambas ramas, todos vuelven a `main` desde el selector inferior izquierdo de VS Code.
7. En Source Control → `…` → **Pull**.

Al terminar, los tres deben ver:

```text
equipo/emily.md
equipo/carolina.md
equipo/franco.md     # Si Franco hizo la prueba
```

## 11. Estructura recomendada del curso

```text
analisis-imagenes/
├── equipo/
├── Imagenes_Ejemplo/
├── tareas/
│   ├── tarea-01/
│   ├── tarea-02/
│   └── ...
├── proyectos/
├── recursos/                  # Solo recursos autorizados para publicación
├── datos_locales/             # Ignorado por Git
├── .vscode/
├── .gitattributes
├── .gitignore
├── .python-version
├── pyproject.toml
├── uv.lock
└── README.md
```

Git no conserva carpetas vacías. Crearlas cuando tengan su primer archivo o colocar un pequeño `README.md` dentro.

Para el notebook revisado, esta ubicación mantiene válida su ruta relativa:

```text
analisis-imagenes/
├── Imagenes_Ejemplo/
│   └── tejido_renal_fluorescencia.png
└── tareas/
    └── tarea-01/
        └── 02_Practica_Sincronica_Estudiantes.ipynb
```

Desde el notebook, `../../Imagenes_Ejemplo/tejido_renal_fluorescencia.png` conduce a la imagen correcta.

## 12. Flujo de trabajo para cada tarea

### Antes de trabajar

1. Guardar o confirmar cualquier trabajo anterior.
2. Cambiar a `main`.
3. Hacer **Pull**.
4. Si cambiaron `pyproject.toml` o `uv.lock`, ejecutar:

   ```text
   uv sync --locked
   ```

5. Crear una rama nueva, por ejemplo:

   ```text
   emily/tarea-01
   carolina/tarea-01
   franco/tarea-01
   ```

### Mientras trabajan

- Guardar frecuentemente.
- No editar el mismo notebook simultáneamente.
- Asignar una persona responsable por cada `.ipynb`.
- Si varias personas necesitan experimentar, usar notebooks separados y luego integrar conscientemente.
- No subir `.venv`, `datos_locales`, credenciales ni accesos del curso.

### Al terminar

1. Abrir Source Control.
2. Revisar todos los cambios, especialmente los outputs de notebooks.
3. Escribir un mensaje concreto.
4. Hacer Commit.
5. Hacer Push o **Publish Branch**.
6. Crear un Pull Request.
7. Franco revisa y fusiona.
8. Todos vuelven a `main` y hacen Pull.

## 13. Añadir dependencias nuevas

Solo Franco modifica dependencias compartidas.

```text
git switch main
git pull --ff-only
uv add NOMBRE-DEL-PAQUETE
uv run python -c "import NOMBRE_IMPORTABLE; print('OK')"
git add pyproject.toml uv.lock
git commit -m "Añadir PAQUETE para ..."
git push
```

Emily y Carolina actualizan:

```text
git switch main
git pull --ff-only
uv sync --locked
```

Reglas:

- No usar `pip install` para dependencias del curso.
- No editar `uv.lock` manualmente.
- Publicar siempre juntos `pyproject.toml` y `uv.lock`.
- Si `uv sync --locked` dice que el lock está desactualizado, no ejecutar `uv lock`; avisar a Franco.

## 14. Solución rápida de problemas

### `code: command not found` en Mac

Dentro de VS Code, no en Terminal:

```text
Cmd+Shift+P
→ Shell Command: Install 'code' command in PATH
```

Como alternativa:

```bash
open -a "Visual Studio Code" .
```

### `uv` no se reconoce

Cerrar completamente VS Code y la terminal y volver a abrir.

Mac:

```bash
"$HOME/.local/bin/uv" --version
```

Windows PowerShell:

```powershell
& "$env:USERPROFILE\.local\bin\uv.exe" --version
```

### `.venv` no aparece como intérprete o kernel

```text
uv sync --locked
```

Después, dentro de VS Code:

```text
Cmd/Ctrl+Shift+P
→ Developer: Reload Window
→ Python: Select Interpreter
```

Elegir `.venv` manualmente.

### El Push responde `permission denied`

1. Confirmar que la invitación de colaborador fue aceptada.
2. Confirmar que VS Code está conectado a la cuenta correcta.
3. Reintentar Push y completar el inicio de sesión del navegador.
4. No escribir la contraseña normal de GitHub como contraseña de Git.

### El notebook no encuentra la imagen

`cv2.imread()` devuelve `None` cuando la ruta es incorrecta. Comprobar que el notebook y `Imagenes_Ejemplo` respeten la estructura de la sección 11.

### Jupyter indica `qtconsole: not installed`

Es normal. QtConsole no es necesario para ejecutar notebooks dentro de VS Code.

### Conflicto en un `.ipynb`

No intentar aceptar bloques al azar. Detenerse y pedir a Franco que conserve una versión e integre manualmente los cambios útiles. Prevenirlo asignando una sola persona responsable por notebook.

## Lista final de la reunión

| Comprobación | Franco | Emily | Carolina |
|---|:---:|:---:|:---:|
| Cuenta de GitHub y correo verificado | ☐ | ☐ | ☐ |
| Invitación de colaborador aceptada | ☐ | ☐ | ☐ |
| `code --version` funciona | ☐ | ☐ | ☐ |
| `git --version` funciona | ☐ | ☐ | ☐ |
| Identidad personal de Git configurada | ☐ | ☐ | ☐ |
| `uv --version` muestra 0.12.5 | ☐ | ☐ | ☐ |
| Repositorio clonado correctamente | ☐ | ☐ | ☐ |
| `uv sync --locked` termina sin error | ☐ | ☐ | ☐ |
| Python muestra 3.13.15 | ☐ | ☐ | ☐ |
| Imports de OpenCV, NumPy, Matplotlib y Pillow funcionan | ☐ | ☐ | ☐ |
| VS Code usa `.venv` | ☐ | ☐ | ☐ |
| Notebook de verificación muestra la imagen | ☐ | ☐ | ☐ |
| Rama personal publicada | ☐ | ☐ | ☐ |
| Pull Request fusionado | ☐ | ☐ | ☐ |
| `git status` queda limpio | ☐ | ☐ | ☐ |

## Referencias oficiales

- [Instalar VS Code en macOS](https://code.visualstudio.com/docs/setup/mac)
- [Instalar VS Code en Windows](https://code.visualstudio.com/docs/setup/windows)
- [Trabajar con GitHub desde VS Code](https://code.visualstudio.com/docs/sourcecontrol/github)
- [Instalar Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [Instalar `uv`](https://docs.astral.sh/uv/getting-started/installation/)
- [Versiones de Python administradas por `uv`](https://docs.astral.sh/uv/concepts/python-versions/)
- [Estructura de proyectos y `uv.lock`](https://docs.astral.sh/uv/concepts/projects/layout/)
- [Crear una cuenta de GitHub](https://docs.github.com/en/account-and-profile/how-tos/account-management/creating-an-account-on-github)
- [Invitar colaboradores](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/repository-access-and-collaboration/inviting-collaborators-to-a-personal-repository)
- [Administrar kernels de Jupyter en VS Code](https://code.visualstudio.com/docs/datascience/jupyter-kernel-management)
