# Integración de Transmisión de Video y Acceso a Cámara en Tiempo Real

## Descripción del Proyecto

### Resumen

Este proyecto consiste en el desarrollo de una aplicación distribuida para la reproducción de un video almacenado en Google Drive y la transmisión en tiempo real de imágenes desde una cámara conectada a USB. Utiliza Python y sus potentes bibliotecas para la gestión de video y comunicación en red.

### Detalles Técnicos

#### Componentes del proyecto

**Scripts de Python:** Dos scripts principales forman la base del proyecto:
- **Script Host (`host.py`):** Ejecuta en la PC receptora, gestionando la recepción de imágenes de la cámara en tiempo real.
- **Script Victim (`victim.py`):** Se ejecuta en la PC emisora, encargada de reproducir el video de Google Drive y transmitir imágenes desde la cámara.

**Bibliotecas de Python utilizadas:**
- `cv2` (OpenCV): Para el manejo de ventanas y operaciones de video.
- `threading`: Para manejar múltiples procesos y realizar multitarea.
- `socket`: Para comunicaciones en red.
- `struct`: Para manipular datos binarios.
- `pickle`: Para serializar y deserializar una estructura de objetos Python.
- `numpy`: Utilizada por OpenCV para manipulaciones de matrices y operaciones de imágenes.

#### Funcionalidades:

- **Reproducción de Video:** El script utiliza OpenCV para abrir y reproducir un video almacenado en Google Drive, mostrándolo en una ventana.
- **Transmisión en Tiempo Real:** Paralelamente, el script captura imágenes desde una cámara USB conectada, las codifica y las transmite a través de una conexión TCP a la PC host.

#### Empaquetado y Distribución:

- **PyInstaller:** Se utilizó para convertir los scripts de Python en un ejecutable `.exe` independiente de la consola.
- **Archivo Batch (`setup.bat`):** Un script batch prepara el entorno de ejecución instalando las dependencias necesarias mediante `pip` y luego ejecuta el archivo `.exe`.
- **NSIS (Nullsoft Scriptable Install System):** Se creó un script `.nsi` para generar un instalador que automatiza la instalación del software, incluyendo la extracción de los archivos y la ejecución del script batch.

#### Explicación del Código Emisor:

##### Código Python
El script de Python está diseñado para ejecutar dos tareas principales en paralelo: reproducir un vídeo desde una URL de Google Drive y transmitir imágenes de la cámara en tiempo real a un servidor. Estas tareas se gestionan mediante dos funciones diferentes ejecutadas en hilos separados para simultaneidad.

1. **Función `play_drive_video(url)`:**
   - **Objetivo:** Reproducir un vídeo desde una URL de Google Drive.
   - **Implementación:**
     - `cv2.VideoCapture(url)`: Inicializa la captura del vídeo desde la URL proporcionada.
     - `cv2.namedWindow(...)`: Crea una ventana para mostrar el vídeo.
     - Bucle `while True` que continúa leyendo el vídeo cuadro por cuadro:
       - `cap.read()`: Lee el siguiente cuadro del vídeo.
       - `cv2.imshow(...)`: Muestra el cuadro en la ventana.
       - `cv2.waitKey(10)`: Espera una tecla durante 10 milisegundos; si se presiona 'q', se rompe el bucle.
       - `cv2.getWindowProperty(...)`: Verifica si la ventana está cerrada; si lo está, rompe el bucle.
     - `cap.release()` y `cv2.destroyAllWindows()`: Limpieza final, liberando el objeto de captura y cerrando todas las ventanas.

2. **Función `send_camera_images(host, port)`:**
   - **Objetivo:** Capturar imágenes de una cámara USB conectada y enviarlas a un servidor.
   - **Implementación:**
     - `socket.socket(...)`: Crea un socket para la comunicación de red.
     - `clientsocket.connect((host, port))`: Conecta el socket al servidor especificado por host y puerto.
     - `cv2.VideoCapture(0)`: Inicia la captura de vídeo de la primera cámara conectada (usualmente la cámara web).
     - Bucle `while True` para la captura y transmisión continua:
       - `cap.read()`: Lee un cuadro de la cámara.
       - `cv2.imencode(...)`: Codifica el cuadro a formato JPG.
       - `pickle.dumps(...)`: Serializa el cuadro codificado para la transmisión.
       - `struct.pack(...)`: Empaqueta el tamaño de los datos seguido de los datos para enviar.
       - Manejo de `BrokenPipeError`: Atrapar y manejar el error si la conexión se cierra inesperadamente.
     - `cap.release()` y `clientsocket.close()`: Liberación de recursos al finalizar la transmisión.

##### Ejecución de Hilos:
Se crean y ejecutan dos hilos, `video_thread` y `camera_thread`, para correr las funciones `play_drive_video` y `send_camera_images` en paralelo, permitiendo que ambas operaciones ocurran simultáneamente sin bloquearse entre sí.

#### Código Batch
El script batch está diseñado para preparar el entorno en una máquina de Windows instalando las dependencias necesarias y luego ejecutar el programa principal.

- `@echo off`: Desactiva la impresión de cada comando en la consola.
- Instala las librerías necesarias (`numpy`, `opencv-python`, `opencv-python-headless`) usando `pip`.
- Ejecuta el archivo ejecutable `victim.exe` que contiene el script de Python empacado.

#### Script NSIS
El script NSIS crea un instalador para la aplicación, facilitando la instalación en máquinas Windows.

- `OutFile`: Define el nombre del archivo del instalador.
- `InstallDir`: Establece el directorio de instalación por defecto en el escritorio del usuario.
- `Page directory` y `Page instfiles`: Define las páginas que aparecen durante la instalación.
- **Sección de instalación:**
  - `SetOutPath`: Establece el directorio donde se extraerán los archivos.
  - `File`: Especifica los archivos a incluir en la instalación.
  - `Exec`: Ejecuta el script batch para instalar dependencias y ejecutar la aplicación.

Este enfoque modular y bien organizado para el desarrollo del software asegura que el sistema no solo es funcional sino también fácil de instalar y mantener, haciendo un uso efectivo de las capacidades concurrentes de Python y la facilidad de distribución proporcionada por NSIS y scripts batch.

#### Conclusión:
Este proyecto demuestra una integración avanzada de tecnologías para la gestión de medios y datos en tiempo real, destacando habilidades en programación, procesamiento de multimedia y comunicaciones en red. Es ideal para mostrar en un portafolio dirigido a roles técnicos en desarrollo de software, seguridad informática, o sistemas de información.
