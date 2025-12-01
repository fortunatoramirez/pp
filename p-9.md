# **Práctica 9 – Concepto Fundamental de Animación con Sprites**

## **Objetivo**

Entender de manera clara cómo un personaje “se mueve” mostrando imágenes diferentes muy rápido.
Enetenderemos:

* Qué es un frame de sprite
* Cómo cargar varios frames en una lista
* Cómo avanzar frame por frame
* Cómo mover un sprite con el teclado
* Cómo se logra la ilusión de movimiento

---

## **Sprites para esta práctica**

Descargar desde aquí:

**[https://www.gameart2d.com/free-dino-sprites.html](https://www.gameart2d.com/free-dino-sprites.html)**

Después de descargar:

1. Extrae el ZIP.
2. Copia solo los archivos:

   * `Idle (1).png`, `Idle (2).png`, `Idle (3).png`
   * `Walk (1).png`, `Walk (2).png`, `Walk (3).png`, `Walk (4).png`
3. Colócalos en esta ruta:

```
practica_9/assets/
```

Tu carpeta quedará así:

```
practica_9/
  main.py
  assets/
      Idle (1).png
      Idle (2).png
      Idle (3).png
      Walk (1).png
      Walk (2).png
      Walk (3).png
      Walk (4).png
```

Nada más.

---

# **BLOQUE 1 – Inicializar Pygame y cargar la ventana**

```python
import pygame
import os

pygame.init()

ANCHO, ALTO = 800, 600
VENTANA = pygame.display.set_mode((ANCHO, ALTO))
pygame.display.set_caption("Práctica 9 – Animación Básica")
RELOJ = pygame.time.Clock()
```

### Explicación:

* Se crea la ventana
* Tendremos 30 FPS
* Aquí no pasa nada aún

---

# **BLOQUE 2 – Cargar manualmente los frames**

```python
# Ruta de la carpeta assets
BASE = os.path.join(os.path.dirname(__file__), "assets")

# Cargar frames de Idle y Walk manualmente
IDLE = [
    pygame.image.load(os.path.join(BASE, "Idle (1).png")).convert_alpha(),
    pygame.image.load(os.path.join(BASE, "Idle (2).png")).convert_alpha(),
    pygame.image.load(os.path.join(BASE, "Idle (3).png")).convert_alpha(),
]

WALK = [
    pygame.image.load(os.path.join(BASE, "Walk (1).png")).convert_alpha(),
    pygame.image.load(os.path.join(BASE, "Walk (2).png")).convert_alpha(),
    pygame.image.load(os.path.join(BASE, "Walk (3).png")).convert_alpha(),
    pygame.image.load(os.path.join(BASE, "Walk (4).png")).convert_alpha(),
]
```

### ¿Qué estamos viendo aquí?

* Una **animación no es más que una lista de imágenes**.
* `IDLE[0]` → la primera imagen
* `IDLE[1]` → la segunda
* Al mostrarlas rápidamente → *parece que se mueve*

Este es el corazón del concepto.

---

# **BLOQUE 3 – Variables del jugador**

```python
x = 100
y = 350

frame_index = 0          # En qué frame vamos
frame_speed = 0.2        # Qué tan rápido avanza la animación

estado = "Idle"          # Puede ser "Idle" o "Walk"
```

### Idea central:

* `frame_index` avanza poco a poco
* Cuando llega al final, regresa a 0
* Cada frame es una imagen diferente del Dino

---

# **BLOQUE 4 – Ciclo principal: animar y mover**

```python
corriendo = True
while corriendo:

    RELOJ.tick(30)

    for evento in pygame.event.get():
        if evento.type == pygame.QUIT:
            corriendo = False

    teclas = pygame.key.get_pressed()

    # Determinar si se mueve o está quieto
    if teclas[pygame.K_a] or teclas[pygame.K_d] or teclas[pygame.K_w] or teclas[pygame.K_s]:
        estado = "Walk"
    else:
        estado = "Idle"

    # Movimiento básico
    if teclas[pygame.K_a]: x -= 5
    if teclas[pygame.K_d]: x += 5
    if teclas[pygame.K_w]: y -= 5
    if teclas[pygame.K_s]: y += 5

    # Avanzar animación
    frame_index += frame_speed

    if estado == "Idle":
        if frame_index >= len(IDLE):
            frame_index = 0
        imagen = IDLE[int(frame_index)]

    elif estado == "Walk":
        if frame_index >= len(WALK):
            frame_index = 0
        imagen = WALK[int(frame_index)]

    # Dibujar
    VENTANA.fill((30, 30, 30))
    VENTANA.blit(imagen, (x, y))

    pygame.display.update()

pygame.quit()
```

---

# **Explicación del ciclo animación-movimiento**

Esto es lo que quiero que entiendan:

---

## **1. El personaje no “se mueve solo”:**

Su posición se cambia con:

```python
x += 5
```

Eso es movimiento real.

---

## **2. La animación NO mueve al personaje**

La animación solo cambia la imagen:

```python
imagen = WALK[int(frame_index)]
```

Mover ≠ animar.
Muy importante para los alumnos.

---

## **3. “Walk” se ve fluido porque avanza el índice en la lista de imágenes**

```python
frame_index += frame_speed
```

Si `frame_index` pasa de 3 (último frame), vuelve a 0.

---

## **4. El ojo humano conecta imágenes rápidas como movimiento**

Eso es un sprite:
**una secuencia de imágenes que muestran fases diferentes de un movimiento.**

---

# **CÓDIGO COMPLETO FINAL (versión ultra simplificada)**

```python
import pygame
import os
import sys

pygame.init()

# Ventana
ANCHO, ALTO = 800, 600
VENTANA = pygame.display.set_mode((ANCHO, ALTO))
pygame.display.set_caption("Práctica 9 – Animación Básica")
RELOJ = pygame.time.Clock()

# -------------------------
# Cargar sprites
# -------------------------
BASE = os.path.join(os.path.dirname(__file__), "assets")

IDLE = [
    pygame.image.load(os.path.join(BASE, "Idle (1).png")).convert_alpha(),
    pygame.image.load(os.path.join(BASE, "Idle (2).png")).convert_alpha(),
    pygame.image.load(os.path.join(BASE, "Idle (3).png")).convert_alpha(),
]

WALK = [
    pygame.image.load(os.path.join(BASE, "Walk (1).png")).convert_alpha(),
    pygame.image.load(os.path.join(BASE, "Walk (2).png")).convert_alpha(),
    pygame.image.load(os.path.join(BASE, "Walk (3).png")).convert_alpha(),
    pygame.image.load(os.path.join(BASE, "Walk (4).png")).convert_alpha(),
]

# -------------------------
# Variables del jugador
# -------------------------
x = 100
y = 350

frame_index = 0
frame_speed = 0.2
estado = "Idle"

# -------------------------
# Loop principal
# -------------------------
corriendo = True
while corriendo:

    RELOJ.tick(30)

    for evento in pygame.event.get():
        if evento.type == pygame.QUIT:
            corriendo = False

    teclas = pygame.key.get_pressed()

    # Determinar animación
    if teclas[pygame.K_a] or teclas[pygame.K_d] or teclas[pygame.K_w] or teclas[pygame.K_s]:
        estado = "Walk"
    else:
        estado = "Idle"

    # Movimiento
    if teclas[pygame.K_a]:
        x -= 5
    if teclas[pygame.K_d]:
        x += 5
    if teclas[pygame.K_w]:
        y -= 5
    if teclas[pygame.K_s]:
        y += 5

    # Animación
    frame_index += frame_speed

    if estado == "Idle":
        if frame_index >= len(IDLE):
            frame_index = 0
        imagen = IDLE[int(frame_index)]

    elif estado == "Walk":
        if frame_index >= len(WALK):
            frame_index = 0
        imagen = WALK[int(frame_index)]

    # Dibujar
    VENTANA.fill((30, 30, 30))
    VENTANA.blit(imagen, (x, y))
    pygame.display.update()

pygame.quit()
sys.exit()
```

---

¿La deseas?
