# **Práctica 9 – Introducción a Sprites Animados en Pygame**

## **Objetivo**

El alumno aprenderá a:

* Cargar imágenes tipo sprite desde carpetas.
* Organizar frames de animación en listas.
* Mostrar un sprite animado en pantalla.
* Moverlo en las cuatro direcciones con W, A, S, D.
* Cambiar entre animación Idle y Walk.

Esta práctica busca que los estudiantes entiendan **los fundamentos más básicos** de cómo un videojuego mueve y anima un personaje.

---

## **Sprites que se usarán (descarga obligatoria)**

Descargar los sprites del Dino desde:

**[https://www.gameart2d.com/free-dino-sprites.html](https://www.gameart2d.com/free-dino-sprites.html)**

Pasos:

1. Entrar al enlace.
2. Descargar el paquete **Free Dino Sprites**.
3. Descomprimir el ZIP.
4. Buscar la carpeta llamada **png**.
5. Mover esa carpeta a la ruta EXACTA dentro del proyecto:

```
practica_9/assets/dino/png
```

Dentro de `png/` están mezcladas todas las imágenes:

```
Idle (1).png
Idle (2).png
Walk (1).png
Walk (2).png
…
```

Para no hacerlo manualmente, el **Bloque 0** del código se encargará de organizarlas automáticamente en carpetas:

```
Idle/
Walk/
```

---

# **BLOQUE 0 – Organización automática de sprites**

Primero ejecuta este bloque para que todas las imágenes queden ordenadas.
Esto permite cargar las animaciones más fácilmente.

```python
import os
import shutil

CARPETA_BASE_DEL_SCRIPT = os.path.dirname(os.path.abspath(__file__))
BASE_PATH = os.path.join(CARPETA_BASE_DEL_SCRIPT, "assets", "dino", "png")

if not os.path.exists(BASE_PATH):
    raise FileNotFoundError("No se encontró assets/dino/png.")

ANIMACIONES = ["Idle", "Walk"]

for anim in ANIMACIONES:
    os.makedirs(os.path.join(BASE_PATH, anim), exist_ok=True)

for archivo in os.listdir(BASE_PATH):
    ruta_origen = os.path.join(BASE_PATH, archivo)
    if os.path.isdir(ruta_origen):
        continue
    for anim in ANIMACIONES:
        if archivo.lower().startswith(anim.lower()):
            shutil.move(
                ruta_origen,
                os.path.join(BASE_PATH, anim, archivo)
            )
            break
```

---

# **BLOQUE 1 – Inicializar Pygame y crear ventana**

```python
import pygame
pygame.init()

ANCHO = 800
ALTO = 600
VENTANA = pygame.display.set_mode((ANCHO, ALTO))
pygame.display.set_caption("Práctica 9 – Sprites simples")
RELOJ = pygame.time.Clock()
```

### Explicación simple:

* `pygame.init()` activa todos los módulos necesarios.
* `set_mode()` crea la ventana.
* `RELOJ` sirve para controlar los FPS.

---

# **BLOQUE 2 – Cargar frames del sprite Dino**

Cargar imágenes desde una carpeta y meterlas en una lista.

```python
import os

def cargar_frames(ruta):
    archivos = os.listdir(ruta)
    archivos.sort(key=lambda nombre: int(nombre.split("(")[1].split(")")[0]))

    frames = []
    for archivo in archivos:
        imagen = pygame.image.load(os.path.join(ruta, archivo)).convert_alpha()
        frames.append(imagen)

    return frames

ANIM = {
    "Idle": cargar_frames(os.path.join(BASE_PATH, "Idle")),
    "Walk": cargar_frames(os.path.join(BASE_PATH, "Walk"))
}
```

---

# **BLOQUE 3 – Variables del jugador (sin clases)**

```python
x = 100        # posición horizontal
y = 350        # posición vertical
vel = 5        # velocidad de movimiento

estado = "Idle"      # animación inicial
frame_index = 0       # frame actual
frame_speed = 0.25    # avance de animación
```

---

# **BLOQUE 4 – Obtener el frame correcto**

```python
def obtener_frame(estado, frame_index):
    frames = ANIM[estado]
    if frame_index >= len(frames):
        frame_index = 0
    imagen = frames[int(frame_index)]
    return imagen, frame_index
```

---

# **BLOQUE 5 – Movimiento del jugador (WASD)**

```python
def actualizar_movimiento(teclas, x, y, estado):
    movio = False

    if teclas[pygame.K_w]:
        y -= vel
        movio = True

    if teclas[pygame.K_s]:
        y += vel
        movio = True

    if teclas[pygame.K_a]:
        x -= vel
        movio = True

    if teclas[pygame.K_d]:
        x += vel
        movio = True

    if movio:
        estado = "Walk"
    else:
        estado = "Idle"

    return x, y, estado
```

---

# **BLOQUE 6 – Ciclo principal del juego**

```python
corriendo = True
while corriendo:

    RELOJ.tick(30)  # Limita a 30 FPS

    for evento in pygame.event.get():
        if evento.type == pygame.QUIT:
            corriendo = False

    teclas = pygame.key.get_pressed()

    x, y, estado = actualizar_movimiento(teclas, x, y, estado)

    frame_index += frame_speed
    imagen, frame_index = obtener_frame(estado, frame_index)

    VENTANA.fill((50, 50, 50))
    VENTANA.blit(imagen, (x, y))

    pygame.display.update()

pygame.quit()
```

---

# **CÓDIGO COMPLETO FINAL (main.py)**

Copiar y pegar directamente:

```python
import os
import shutil
import pygame

# ---------------------------------------
# BLOQUE 0 – ORGANIZACIÓN DE SPRITES
# ---------------------------------------

CARPETA_BASE_DEL_SCRIPT = os.path.dirname(os.path.abspath(__file__))
BASE_PATH = os.path.join(CARPETA_BASE_DEL_SCRIPT, "assets", "dino", "png")

if not os.path.exists(BASE_PATH):
    raise FileNotFoundError("No se encontró assets/dino/png.")

ANIMACIONES = ["Idle", "Walk"]

for anim in ANIMACIONES:
    os.makedirs(os.path.join(BASE_PATH, anim), exist_ok=True)

for archivo in os.listdir(BASE_PATH):
    ruta_origen = os.path.join(BASE_PATH, archivo)
    if os.path.isdir(ruta_origen):
        continue
    for anim in ANIMACIONES:
        if archivo.lower().startswith(anim.lower()):
            shutil.move(
                ruta_origen,
                os.path.join(BASE_PATH, anim, archivo)
            )
            break

# ---------------------------------------
# BLOQUE 1 – PYGAME
# ---------------------------------------

pygame.init()
ANCHO, ALTO = 800, 600
VENTANA = pygame.display.set_mode((ANCHO, ALTO))
pygame.display.set_caption("Práctica 9 – Sprites simples")
RELOJ = pygame.time.Clock()

# ---------------------------------------
# BLOQUE 2 – Cargar sprites
# ---------------------------------------

def cargar_frames(ruta):
    archivos = os.listdir(ruta)
    archivos.sort(key=lambda nombre: int(nombre.split("(")[1].split(")")[0]))
    frames = []
    for archivo in archivos:
        imagen = pygame.image.load(os.path.join(ruta, archivo)).convert_alpha()
        frames.append(imagen)
    return frames

ANIM = {
    "Idle": cargar_frames(os.path.join(BASE_PATH, "Idle")),
    "Walk": cargar_frames(os.path.join(BASE_PATH, "Walk"))
}

# ---------------------------------------
# BLOQUE 3 – Variables del jugador
# ---------------------------------------

x = 100
y = 350
vel = 5

estado = "Idle"
frame_index = 0
frame_speed = 0.25

# ---------------------------------------
# BLOQUE 4 – Frame actual
# ---------------------------------------

def obtener_frame(estado, frame_index):
    frames = ANIM[estado]
    if frame_index >= len(frames):
        frame_index = 0
    return frames[int(frame_index)], frame_index

# ---------------------------------------
# BLOQUE 5 – Movimiento WASD
# ---------------------------------------

def actualizar_movimiento(teclas, x, y, estado):
    movio = False

    if teclas[pygame.K_w]:
        y -= vel
        movio = True

    if teclas[pygame.K_s]:
        y += vel
        movio = True

    if teclas[pygame.K_a]:
        x -= vel
        movio = True

    if teclas[pygame.K_d]:
        x += vel
        movio = True

    if movio:
        estado = "Walk"
    else:
        estado = "Idle"

    return x, y, estado

# ---------------------------------------
# BLOQUE 6 – Loop principal
# ---------------------------------------

corriendo = True
while corriendo:

    RELOJ.tick(30)

    for evento in pygame.event.get():
        if evento.type == pygame.QUIT:
            corriendo = False

    teclas = pygame.key.get_pressed()

    x, y, estado = actualizar_movimiento(teclas, x, y, estado)

    frame_index += frame_speed
    imagen, frame_index = obtener_frame(estado, frame_index)

    VENTANA.fill((50, 50, 50))
    VENTANA.blit(imagen, (x, y))

    pygame.display.update()

pygame.quit()
```
