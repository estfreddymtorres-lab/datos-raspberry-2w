# datos-raspberry-2w
---

## 📌 Proyecto 3: Análisis en Capa Física y Protocolo Serial UART/RS232 con Osciloscopio

Este proyecto aborda la caracterización física, temporal y de protocolo de la comunicación serial asíncrona **UART/RS232** emulada con niveles lógicos LVTTL (3.3 V) mediante una **Raspberry Pi Pico 2W**[cite: 20, 21], empleando un osciloscopio digital para la medición de tramas y el desarrollo de un enlace **Half-Duplex** con visualización en pantalla OLED y transferencia de archivos entre dos placas[cite: 20, 30, 31].

### 📝 Descripción
El estudio contrasta el modelo matemático del protocolo serial con mediciones experimentales en el dominio del tiempo y el voltaje[cite: 20]. Se evaluó la relación inversa entre la tasa de baudios (de **600 a 115200 bps**) y el tiempo de bit ($t_b$)[cite: 20, 29], la estructura física de las tramas (Start, datos LSB-First, paridad y Stop)[cite: 21], el impacto de los esquemas de paridad en la eficiencia del canal[cite: 21, 29] y un sistema bidireccional de transferencia de archivos con control de flujo por confirmación (`ACK`)[cite: 31].

### 🛠️ Tecnologías y Hardware
* **Microcontroladores:** Dos placas **Raspberry Pi Pico 2W** (chip RP2350) interconectadas por UART0 (`GP0/TX` y `GP1/RX` cruzados con referencia común `GND`)[cite: 21, 31].
* **Periférico de Visualización:** Pantalla OLED SSD1306 ($128 \times 64$ píxeles) acoplada al nodo receptor mediante el bus **SPI0** (`GP17` a `GP21`)[cite: 31].
* **Lenguaje:** MicroPython (uso de `machine.UART`, `machine.SPI` y `utime`)[cite: 21, 31].
* **Instrumentación:** Osciloscopio digital **Tektronix TDS 2012B** (configurado con *trigger* por flanco de bajada y cursores de tiempo/amplitud)[cite: 22].

### 🚀 Metodología y Pruebas Realizadas
1. **Caracterización de Velocidades:** Barrido de baudios estándar (**1200, 4800, 9600, 38400, 57600 y 115200 bps**) midiendo ancho de bit y estabilidad de niveles lógicos (~3.36 V pico a pico)[cite: 22, 23, 24].
2. **Anatomía de Trama y Paridad:** Captura individual de los caracteres `"@"`, `"E"`, `"p"`, `"x"` y `"$"` bajo esquemas sin paridad, paridad par e impar[cite: 25, 26], además de ráfagas continuas de 60 caracteres a 600 baudios y la frase institucional de 45 caracteres a 57600 baudios (7E2)[cite: 29, 30].
3. **Reto Half-Duplex y Transferencia de Archivos:** Implementación de scripts en MicroPython (`TX.py`, `RX.py` / `recibir.py`) para transmitir mensajes con confirmación `ACK` y transferir un archivo estructurado (`alfabetos.txt` $\rightarrow$ `recibido.txt`) de **70 líneas (2310 bytes)** entre ambas placas[cite: 31, 32].

### 📊 Conclusiones Principales
* **Eficiencia vs. Detección de Errores:** Eliminar el bit de paridad reduce la trama de 11 a 10 bits, logrando un ahorro cercano al **9 %** en el tiempo de ocupación del canal (100 ms menos en bloques de 60 caracteres a 600 bps)[cite: 29, 30], mientras que usar 2 bits de parada en tramas de 7 bits introduce una sobrecarga de control (*overhead*) del **36,3 %**[cite: 30].
* **Rendimiento en Transferencia de Archivos:** La transferencia completa de 70 líneas (2310 bytes) tomó **34.533 s** logrando un *throughput* real de **66.89 bytes/s**[cite: 32]. Se comprobó que transmitir sin retardo entre líneas provoca desbordamiento del *buffer* UART (*Buffer Overflow*) debido a la latencia de escritura física en la memoria Flash[cite: 32].
