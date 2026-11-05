**Organización de computadores**
**Proyecto 4 - Programación**
**2026-2**

# Integrantes

| Nombre | Usuario GitHub | Aportes principales |
|---|---|---|
| Thomas Serna Saldarriaga | ThomasSerna | Inicialización de la estructura del proyecto y plantilla |
| Juan Esteban Palacio | jepalaciob | Configuración del repositorio y estructura inicial |

# Compilación y ejecución

<!-- Pasos exactos para compilar con JackCompiler y ejecutar en el VMEmulator. -->

1. Compilar la carpeta `proyecto4/` con el compilador oficial de Nand2Tetris (`tools/`):
   - Windows: `tools/JackCompiler.bat RUTA/proyecto4`
   - Linux/macOS: `tools/JackCompiler.sh RUTA/proyecto4`
2. Abrir el **VM Emulator** (`tools/VMEmulator.bat` o `VMEmulator.sh`).
3. Ir a `File -> Load Program` y seleccionar la carpeta `proyecto4/`.
4. Seleccionar `No animation` y velocidad máxima, presionar `Run` y hacer clic sobre la pantalla del emulador para capturar el teclado.

# Controles

| Tecla | Acción |
|---|---|
| Flecha arriba / Flecha abajo | Cambiar de carril (una vez por pulsación) |
| `P` | Pausar y continuar |
| `R` | Reiniciar (volver a la pantalla inicial sin recargar el programa) |
| `ESC` | Salir del juego |
| Selección de distancia (por definir) | Elegir la distancia a la meta en la pantalla inicial (al menos 3 opciones) |

# Diseño de clases

<!-- Tabla con cada clase y su responsabilidad. Explique cómo especializan
     los tipos de obstáculo sin herencia y por qué la lógica no depende de
     la pantalla ni del teclado. -->

| Clase | Responsabilidad |
|---|---|
| `Main` | Punto de entrada; crea el juego, lo ejecuta y lo libera. |
| `Carrera` | Lógica pura del juego de tres carriles (separada de pantalla y teclado). |

*Por completar a medida que se diseñen e implementen las demás clases del juego.*

# Rutinas VM

<!-- Funciones escritas a mano en VM (Grafico.dibujarCelda y las de
     bonificación que hayan hecho) y dónde las usa el juego. -->

- `Grafico.dibujarCelda(fila, col, sprite)` en `Grafico.vm` *(en desarrollo)*.

# Uso de IA

<!-- Herramienta, para qué la usaron y en qué partes del código; o la frase
     "No se usó IA". -->
