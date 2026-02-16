# RamcoGame
---

# 🎮 RamcoGame — Videojuego Isométrico en JavaScript

## 📌 Descripción general

**RamcoGame** es un videojuego 2D con proyección isométrica desarrollado en **HTML5, CSS y JavaScript utilizando la API Canvas**.

El proyecto parte de un motor isométrico básico trabajado en clase y evoluciona hasta convertirse en un videojuego completo con:

* Sistema de niveles progresivos
* Enemigos con movimiento autónomo
* Sistema de vidas
* Invulnerabilidad temporal tras impacto
* Monedas coleccionables
* HUD dinámico
* Estados del juego (pausa, nivel superado, game over)
* Pantalla completa responsive

El objetivo del proyecto es aplicar de forma práctica todos los conocimientos adquiridos en el bloque de desarrollo de videojuegos 2D.

---

## 🎯 Objetivos del proyecto

Este proyecto demuestra:

* Implementación de un **motor de proyección isométrica**
* Uso avanzado de **Canvas 2D**
* Programación orientada a objetos en JavaScript
* Gestión de estados del juego
* Detección de colisiones por tile
* Progresión dinámica de dificultad
* Organización modular del código
* Control del bucle principal con `requestAnimationFrame`
* Diseño de HUD interactivo
* Adaptación responsive a pantalla completa

---

## 🛠 Tecnologías utilizadas

* HTML5
* CSS3
* JavaScript
* API Canvas 2D

No se utilizan librerías externas.

---

## 🕹 Mecánicas del juego

### 🎯 Objetivo

Recoger todas las monedas del nivel sin perder todas las vidas.

---

### 🪙 Sistema de monedas

* Nivel 1 → 4 monedas
* Cada nivel añade **+1 moneda**
* El nivel se completa al recoger todas las monedas

---

### 👾 Enemigos

* Movimiento autónomo aleatorio por el mapa
* Colisión por tile
* Nivel 1 → 3 enemigos
* Cada nivel añade **+1 enemigo**
* Aumento progresivo de dificultad

---

### ❤️ Sistema de vidas

* El jugador comienza con 3 vidas
* Al colisionar con un enemigo:

  * Pierde 1 vida
  * Activa invulnerabilidad temporal
* Si las vidas llegan a 0:

  * Se muestra pantalla de **Game Over**
  * ENTER reinicia desde nivel 1

---

### ⏸ Sistema de pausa

* Tecla **P**
* Congela movimiento y colisiones
* Muestra overlay de pausa
* Permite reanudar sin reiniciar el nivel

---

## 🧠 Arquitectura del juego

El proyecto está estructurado en:

* Clase `Jugador`
* Clase `Enemigo`
* Clase `Moneda`
* Generador procedural de mapa
* Generador dinámico de enemigos y monedas
* Sistema centralizado de estados:

  * `juegoTerminado`
  * `juegoPausado`
  * `tiempoInvulnerable`

---

## 📐 Proyección Isométrica

La conversión de coordenadas de grid a pantalla se realiza mediante:

```
x = (i - j) * TILE
y = (i + j) * (TILE / 2)
```

Este sistema permite simular profundidad en un entorno 2D sin utilizar WebGL.

---

## 📈 Sistema de niveles

Cada vez que se supera un nivel:

* Se incrementa el número de monedas
* Se incrementa el número de enemigos
* Se genera un nuevo mapa
* Se reinician las posiciones
* Se mantiene la progresión acumulativa

Esto genera una curva de dificultad progresiva y controlada.

---

## 🎮 Estados del juego

El motor distingue tres estados principales:

1. Juego activo
2. Juego pausado
3. Juego terminado (nivel superado o game over)

Esto permite separar claramente la lógica del juego y el renderizado.

---

## ⌨ Controles

| Tecla | Acción                         |
| ----- | ------------------------------ |
| W     | Mover arriba                   |
| S     | Mover abajo                    |
| A     | Mover izquierda                |
| D     | Mover derecha                  |
| P     | Pausar / Reanudar              |
| ENTER | Continuar tras finalizar nivel |

---

## ▶ Ejecución

No requiere instalación.

Abrir el archivo:

```
RamcoGame.html
```

en cualquier navegador moderno.

---

## 🎨 Diseño visual

El juego incluye:

* Sombras dinámicas bajo entidades
* Diferenciación visual entre suelo y paredes
* HUD superior con información en tiempo real
* Overlays para pausa y fin de partida
* Canvas adaptable a pantalla completa

---

## 🔄 Evolución del proyecto

El desarrollo ha seguido una evolución incremental:

1. Motor isométrico base
2. Mapa por tiles con colisiones
3. Sistema de enemigos
4. Sistema de vidas
5. HUD dinámico
6. Sistema de monedas y niveles
7. Escalado progresivo de dificultad
8. Pantalla completa responsive
9. Sistema de pausa

El historial de commits refleja esta evolución estructurada.

---

## 🚀 Posibles mejoras futuras

* Animaciones con spritesheet
* Efectos de sonido y música
* Guardado de progreso con `localStorage`
* Transiciones animadas entre niveles
* Menú principal
* Sistema de puntuación acumulativa
* IA más avanzada para enemigos

---

## 👨‍💻 Autoría

Proyecto desarrollado como práctica final del bloque de desarrollo de videojuegos 2D.

---
