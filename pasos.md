# 0. Preparación

## 0.1. Ir a la carpeta de tareas

Desde la terminal, nos dirigimos a la carpeta donde almacenamos las tareas:

```bash
cd tareas
```

## 0.2. Crear una carpeta para la Tarea 3

Creamos una nueva carpeta llamada `tarea3`:

```bash
mkdir tarea3
```

Luego ingresamos al nuevo directorio:

```bash
cd tarea3
```

## 0.3. Agregar el archivo de la tarea

Arrastramos el archivo correspondiente a la Tarea 3 dentro del directorio `tarea3`.

## 0.4. Crear una carpeta para las imágenes

Dentro del directorio `tarea3`, creamos una carpeta llamada `imagenes`:

```bash
mkdir imagenes
```

## 0.5. Agregar las imágenes necesarias

Arrastramos las imágenes necesarias para desarrollar la tarea dentro de la carpeta `imagenes`.

# 1. Ejercicio 1: Convolución 2D Separable y Operador Sobel

En este ejercicio se implementará manualmente, utilizando únicamente NumPy, una convolución unidimensional y posteriormente el operador Sobel separable.

La idea principal es aprovechar que el operador Sobel de $3\times3$ puede descomponerse en dos operaciones unidimensionales consecutivas.

Para $G_x$:

$$
W_{Sx}
=
\begin{bmatrix}
-1 & 0 & 1\\
-2 & 0 & 2\\
-1 & 0 & 1
\end{bmatrix}
=
\begin{bmatrix}
1\\
2\\
1
\end{bmatrix}
\begin{bmatrix}
-1 & 0 & 1
\end{bmatrix}
$$

Para $G_y$:

$$
W_{Sy}
=
\begin{bmatrix}
-1 & -2 & -1\\
0 & 0 & 0\\
1 & 2 & 1
\end{bmatrix}
=
\begin{bmatrix}
-1\\
0\\
1
\end{bmatrix}
\begin{bmatrix}
1 & 2 & 1
\end{bmatrix}
$$

Por tanto, no necesitamos aplicar directamente un kernel bidimensional de $3\times3$. Podemos realizar dos convoluciones 1D consecutivas.

---

## 1.1. Importar las bibliotecas

Primero importamos las bibliotecas necesarias para realizar el ejercicio.

```python
import os
import cv2
import numpy as np
import matplotlib.pyplot as plt

from skimage.metrics import peak_signal_noise_ratio as psnr
from skimage.metrics import mean_squared_error as mse
```

---

## 1.2. Cargar la imagen

La estructura de nuestro directorio es:

```text
tarea3/
├── 03_Tarea.ipynb
└── imagenes/
    ├── resonancia_cerebro_mri.png
    ├── retinografia_microaneurismas.png
    └── mamografia.png
```

La función `leer_img()` proporcionada por la tarea permite cargar una imagen utilizando OpenCV.

```python
def leer_img(ruta, flags=cv2.IMREAD_GRAYSCALE):
    candidatas = [
        ruta,
        os.path.join("..", "..", "Imagenes_Ejemplo", os.path.basename(ruta)),
        os.path.join("..", "Imagenes_Ejemplo", os.path.basename(ruta)),
        os.path.join("Imagenes_Ejemplo", os.path.basename(ruta)),
        os.path.join("2026-II", "Imagenes_Ejemplo", os.path.basename(ruta))
    ]

    for c in candidatas:
        if os.path.exists(c):
            data = np.fromfile(c, dtype=np.uint8)
            img = cv2.imdecode(data, flags)

            if img is not None:
                return img

    raise FileNotFoundError(f"No se encontró la imagen: {ruta}")
```

Como la imagen se encuentra dentro del directorio `imagenes`, modificamos la ruta utilizada para cargarla:

```python
img_mri = leer_img("imagenes/resonancia_cerebro_mri.png")
```

---

# 1.3. Implementación de la convolución 1D

La convolución discreta 1D puede escribirse como:

$$
g(x)
=
\sum_{s=-a}^{a}
w(s)f(x-s)
$$

El término $x-s$ implica que, para realizar una convolución propiamente dicha, debemos invertir espacialmente el kernel.

Por ejemplo, si tenemos:

$$
w=
[-1,0,1]
$$

el kernel utilizado durante la convolución será:

$$
w_{\text{flip}}
=
[1,0,-1]
$$

También debemos resolver el problema de los bordes. Para ello utilizamos padding reflejado mediante:

```python
np.pad(..., mode="reflect")
```

Este comportamiento corresponde al padding reflejado solicitado en el ejercicio.

Implementamos ahora la función `convolucion_1d_numpy()`.

```python
def convolucion_1d_numpy(img, kernel_1d, eje):
    """
    Aplica convolución 1D sobre una imagen 2D a lo largo de un eje específico.

    Args:
        img (np.ndarray):
            Imagen 2D de entrada.

        kernel_1d (np.ndarray):
            Vector unidimensional de coeficientes.

        eje (int):
            1 para convolución horizontal.
            0 para convolución vertical.

    Returns:
        np.ndarray:
            Imagen filtrada del mismo tamaño que la original en float64.
    """

    # Trabajamos en coma flotante para evitar problemas
    # de saturación o pérdida de signo.
    img = img.astype(np.float64)

    # Convertimos el kernel a un vector NumPy.
    kernel = np.asarray(kernel_1d, dtype=np.float64).ravel()

    # Tamaño del kernel.
    K = kernel.size

    # El ejercicio utiliza kernels con tamaño impar.
    if K % 2 == 0:
        raise ValueError("El kernel debe tener un tamaño impar.")

    # Radio del kernel.
    radio = K // 2

    # En una convolución el kernel debe invertirse.
    kernel_flip = kernel[::-1]

    # ----------------------------------------------------------
    # Convolución horizontal
    # ----------------------------------------------------------
    if eje == 1:

        # Padding solamente sobre las columnas.
        img_pad = np.pad(
            img,
            ((0, 0), (radio, radio)),
            mode="reflect"
        )

        # Construimos todas las ventanas horizontales.
        ventanas = np.lib.stride_tricks.sliding_window_view(
            img_pad,
            window_shape=K,
            axis=1
        )

        # Multiplicación por el kernel y acumulación.
        resultado = np.sum(
            ventanas * kernel_flip,
            axis=-1
        )

    # ----------------------------------------------------------
    # Convolución vertical
    # ----------------------------------------------------------
    elif eje == 0:

        # Padding solamente sobre las filas.
        img_pad = np.pad(
            img,
            ((radio, radio), (0, 0)),
            mode="reflect"
        )

        # Construimos todas las ventanas verticales.
        ventanas = np.lib.stride_tricks.sliding_window_view(
            img_pad,
            window_shape=K,
            axis=0
        )

        # Multiplicación por el kernel y acumulación.
        resultado = np.sum(
            ventanas * kernel_flip,
            axis=-1
        )

    else:
        raise ValueError("El eje debe ser 0 (vertical) o 1 (horizontal).")

    return resultado
```

---

# 1.4. Operador Sobel separable

Una vez implementada nuestra convolución 1D, podemos construir el operador Sobel utilizando dos vectores:

```python
k_smooth = np.array([1.0, 2.0, 1.0])
k_diff   = np.array([-1.0, 0.0, 1.0])
```

El vector

$$
[1,2,1]
$$

realiza un suavizado, mientras que

$$
[-1,0,1]
$$

aproxima una derivada espacial.

Para calcular $G_x$ realizamos:

1. Derivación horizontal.
2. Suavizado vertical.

Para calcular $G_y$ realizamos:

1. Suavizado horizontal.
2. Derivación vertical.

Finalmente calculamos la magnitud del gradiente como:

$$
M(x,y)
=
\sqrt{
G_x(x,y)^2+
G_y(x,y)^2
}
$$

Implementamos entonces `sobel_separable_numpy()`:

```python
def sobel_separable_numpy(img):
    """
    Calcula Gx, Gy y la magnitud del gradiente
    utilizando la descomposición separable de Sobel.

    Args:
        img (np.ndarray):
            Imagen en escala de grises.

    Returns:
        tuple:
            gx, gy y magnitud del gradiente.
    """

    # Vectores base de Sobel.
    k_smooth = np.array([1.0, 2.0, 1.0])
    k_diff   = np.array([-1.0, 0.0, 1.0])

    # ----------------------------------------------------------
    # Gradiente en X
    # ----------------------------------------------------------

    # Primero derivamos horizontalmente.
    gx_horizontal = convolucion_1d_numpy(
        img,
        k_diff,
        eje=1
    )

    # Después suavizamos verticalmente.
    gx = convolucion_1d_numpy(
        gx_horizontal,
        k_smooth,
        eje=0
    )

    # ----------------------------------------------------------
    # Gradiente en Y
    # ----------------------------------------------------------

    # Primero suavizamos horizontalmente.
    gy_horizontal = convolucion_1d_numpy(
        img,
        k_smooth,
        eje=1
    )

    # Después derivamos verticalmente.
    gy = convolucion_1d_numpy(
        gy_horizontal,
        k_diff,
        eje=0
    )

    # ----------------------------------------------------------
    # Magnitud del gradiente
    # ----------------------------------------------------------

    mag = np.sqrt(gx**2 + gy**2)

    return gx, gy, mag
```

---

# 1.5. Ejecutar nuestra implementación

Aplicamos ahora el operador Sobel que acabamos de implementar a la imagen de resonancia magnética.

```python
gx_man, gy_man, mag_man = sobel_separable_numpy(img_mri)
```

La variable `mag_man` contiene la magnitud del gradiente calculada únicamente mediante nuestra implementación en NumPy.

---

# 1.6. Validación contra OpenCV

El ejercicio solicita verificar numéricamente nuestra implementación utilizando como referencia el operador Sobel de OpenCV.

Para esto calculamos:

```python
gx_cv = cv2.Sobel(
    img_mri,
    cv2.CV_64F,
    1,
    0,
    ksize=3,
    borderType=cv2.BORDER_REFLECT_101
)

gy_cv = cv2.Sobel(
    img_mri,
    cv2.CV_64F,
    0,
    1,
    ksize=3,
    borderType=cv2.BORDER_REFLECT_101
)

mag_cv = cv2.magnitude(gx_cv, gy_cv)
```

Aquí `cv2.Sobel()` no se utiliza para resolver el ejercicio, sino únicamente como referencia para verificar nuestra implementación manual.

---

# 1.7. MSE y PSNR

Para cuantificar qué tan similares son ambos resultados utilizamos dos métricas.

El Error Cuadrático Medio o MSE mide el error promedio entre las dos imágenes.

La meta establecida por la tarea es:

$$
\mathrm{MSE}<10^{-4}
$$

También utilizamos el PSNR, cuya meta es:

$$
\mathrm{PSNR}>50\text{ dB}
$$

Calculamos ambas métricas:

```python
val_mse = mse(mag_cv, mag_man)

rango = np.ptp(mag_cv) if np.ptp(mag_cv) > 0 else 1.0

val_psnr = (
    100.0
    if val_mse < 1e-12
    else psnr(
        mag_cv,
        mag_man,
        data_range=rango
    )
)

print(f"MSE  : {val_mse:.4e} (Meta: < 1e-4)")
print(f"PSNR : {val_psnr:.2f} dB (Meta: > 50 dB)")
```

Si nuestra implementación es correcta, el MSE debe ser prácticamente cero y el PSNR debe superar ampliamente los $50$ dB.

---

# 1.8. Comparación visual

Finalmente visualizamos:

1. La imagen MRI original.
2. La magnitud del gradiente obtenida con nuestra implementación.
3. La diferencia absoluta entre nuestra implementación y OpenCV.

```python
fig, axes = plt.subplots(1, 3, figsize=(16, 5))

axes[0].imshow(
    img_mri,
    cmap="gray"
)
axes[0].set_title("Original (MRI Cerebral)")
axes[0].axis("off")

axes[1].imshow(
    mag_man,
    cmap="magma"
)
axes[1].set_title("Sobel Manual NumPy")
axes[1].axis("off")

axes[2].imshow(
    np.abs(mag_cv - mag_man),
    cmap="inferno"
)
axes[2].set_title("Diferencia Absoluta vs OpenCV")
axes[2].axis("off")

plt.tight_layout()
plt.show()
```

La imagen de diferencia nos permite observar directamente cualquier discrepancia entre nuestra implementación manual y la implementación de referencia de OpenCV.