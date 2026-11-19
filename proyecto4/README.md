**Organización de computadores**
**Proyecto 4 - Programación**
**2026-2**

# Integrantes

| Nombre | Usuario GitHub | Aportes principales |
|---|---|---|
| Thomas Serna Saldarriaga | ThomasSerna | Lógica de `Carrera`, modelo de obstáculos (`TipoObstaculo`, `Obstaculo`), `Grafico.dibujarCelda` y `Aleatorio.vm` |
| Juan Esteban Palacio Betancur | jepalaciob | Configuración del repositorio y estructura inicial; bucle del juego (`Juego`, `Main`), presentación (`Vista`, `Sprites`), lectura del teclado (`Teclado`), `Grafico.borrarCelda` y `Grafico.desplazarFila` |

# Compilación y ejecución

<!-- Pasos exactos para compilar con JackCompiler y ejecutar en el VMEmulator. -->

1. Compilar la carpeta `proyecto4/` con el compilador oficial de Nand2Tetris (`tools/`):
   - Windows: `tools/JackCompiler.bat RUTA/proyecto4`
   - Linux/macOS: `tools/JackCompiler.sh RUTA/proyecto4`
2. Abrir el **VM Emulator** (`tools/VMEmulator.bat` o `VMEmulator.sh`).
3. Ir a `File -> Load Program` y seleccionar la carpeta `proyecto4/`.
4. Seleccionar `No animation` y velocidad máxima, presionar `Run` y hacer clic sobre la pantalla del emulador para capturar el teclado.
5. En la pantalla inicial elegir la distancia a la meta con `1`, `2` o `3`.

Las pruebas públicas se corren desde Git Bash con `./correr_pruebas.sh RUTA/proyecto4 RUTA/nand2tetris/tools` (carpeta `pruebas/` del material de apoyo).

# Controles

| Tecla | Acción |
|---|---|
| `1`, `2`, `3` | En la pantalla inicial: elegir la meta (100, 200 o 300 pasos) |
| Flecha arriba / Flecha abajo | Cambiar de carril (una vez por pulsación) |
| `P` | Pausar y continuar |
| `R` | Reiniciar: volver a la pantalla inicial sin recargar el programa. Al terminar una carrera, jugar de nuevo |
| `ESC` | Salir del juego |

# Diseño de clases

<!-- Tabla con cada clase y su responsabilidad. Explique cómo especializan
     los tipos de obstáculo sin herencia y por qué la lógica no depende de
     la pantalla ni del teclado. -->

| Clase | Responsabilidad |
|---|---|
| `Main` | Punto de entrada; crea el juego, lo ejecuta y lo libera. |
| `Juego` | Flujo del juego: pantalla inicial, bucle de la carrera, pausa, reinicio, resultado y salida. Conecta `Carrera`, `Vista` y `Teclado`. |
| `Carrera` | Lógica pura del juego de tres carriles (separada de pantalla y teclado). |
| `Obstaculo` | Representa un obstáculo activo en la vía (carril, columna y detección de choques/salida). |
| `TipoObstaculo` | Almacena la configuración de cada tipo (`tipo`, `largo`, `velocidad`, `letal`, `puntos`). |
| `Vista` | Presentación: pantallas de texto, vía, carro, obstáculos, marcador y barra de progreso. Solo lee el estado de la `Carrera`. |
| `Sprites` | Dibujos de 16 × 16 píxeles (arreglos de 16 palabras) del carro, los obstáculos, la vía y la barra de progreso. |
| `Teclado` | Lee el teclado sin detener el juego y detecta pulsaciones nuevas: mantener una tecla cuenta una sola vez. |

```
Main ──► Juego ──┬──► Teclado                                   (entrada)
                 ├──► Vista ──► Sprites, Grafico.vm              (presentación)
                 └──► Carrera ──► Obstaculo ──► TipoObstaculo    (lógica)
                          └─────► Aleatorio.vm
```

Los 4 tipos de obstáculo (`0` cono, `1` camión, `2` moto, `3` moneda) se especializan mediante **composición y datos**: `Carrera` instancia cuatro objetos `TipoObstaculo` compartidos y cada `Obstaculo` recibe el suyo sin duplicar código ni requerir herencia. La lógica no depende de pantalla ni de teclado porque `Carrera` y sus clases asociadas no llaman a `Screen`, `Output`, `Keyboard`, `Grafico` ni `Sys.wait`.

La presentación también se especializa por datos: `Sprites.deObstaculo(tipo, parte)` elige el dibujo según el tipo del obstáculo y la parte que se dibuja, así el camión de 3 celdas usa una cabina adelante y la carga atrás sin una clase aparte.

Generación de obstáculos: además de la regla de paso libre del enunciado (columnas 26 a 31), `Carrera.generar` revisa en qué pasos tocaría al carro cada obstáculo letal (`Obstaculo.tocaCarroEntre`, calculado con `TipoObstaculo.pasoLlegada` y `pasoSalida`). Un letal nuevo solo se agrega si otro carril queda libre en esos pasos, con 2 pasos de margen para cambiar de carril. Así no se forman muros imposibles, aunque las motos (2 celdas por paso) alcancen a los demás obstáculos.

Flujo de un paso del juego (`Juego.jugarPaso`): `Vista.borrarMovibles` borra el carro y los obstáculos, `Carrera.avanzar` y `Carrera.generar` actualizan la lógica y `Vista.dibujarPaso` vuelve a dibujar solo lo que se mueve. El bucle nunca se detiene a esperar teclas: `Teclado.leerDurante` revisa `Keyboard.keyPressed()` mientras espera con `Sys.wait` el tiempo de cada paso (`milisegundosPorPaso` en `Juego`).

Memoria: cada partida crea su `Carrera` y la libera al terminar; los sprites se crean una sola vez; `Vista.escribir` libera cada cadena literal después de imprimirla, así se pueden jugar muchas partidas seguidas sin agotar el heap.

# Rutinas VM

<!-- Funciones escritas a mano en VM (Grafico.dibujarCelda y las de
     bonificación que hayan hecho) y dónde las usa el juego. -->

Todas están escritas a mano en `Grafico.vm` y `Aleatorio.vm`. Leen y escriben la memoria con `pointer`/`that` y multiplican por 512 con sumas (sin `Memory.poke`, `Memory.peek` ni `Math.multiply`).

| Función | Qué hace | Dónde la usa el juego |
|---|---|---|
| `Grafico.dibujarCelda(fila, col, sprite)` | Copia las 16 palabras del sprite a la memoria de video (`16384 + fila*512 + k*32 + col`). | `Vista`: carro, obstáculos, bordes, líneas divisorias y barra de progreso. |
| `Grafico.direccionCelda(fila, col)` | Auxiliar: dirección de la primera línea de la celda. | `dibujarCelda`, `borrarCelda` y `desplazarFila`. |
| `Grafico.borrarCelda(fila, col)` (bono) | Pone en 0 las 16 palabras de la celda. | `Vista.borrarMovibles`: borra el carro y los obstáculos antes de cada paso. |
| `Grafico.desplazarFila(fila)` (bono) | Rota una palabra a la izquierda las 32 palabras de cada una de las 16 líneas de la fila, con el auxiliar `Grafico.rotarLinea`. | `Vista.dibujarPaso`: corre las líneas divisorias (filas 6 y 8) para que la vía parezca moverse. |
| `Aleatorio.semilla(s)` y `Aleatorio.siguiente()` (bono) | Generador de 16 bits `x <- 5x + 3`. | `Carrera.new` y `Carrera.generar`; `Juego` cambia la semilla en cada partida. |

# Uso de IA

<!-- Herramienta, para qué la usaron y en qué partes del código; o la frase
     "No se usó IA". -->
