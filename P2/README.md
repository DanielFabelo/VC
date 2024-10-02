# Auditoría
El desarrollo de la práctica 2 ha sido realizado por Anixia Di Gregorio y Daniel Fabelo Izquierdo. 

# Tarea 1: Conteo de Píxeles Blancos por Filas
El objetivo de esta tarea es realizar un análisis de la imagen cargada, centrándonos en la cuenta de los píxeles blancos por filas en lugar de por columnas. 
Se debe determinar el valor máximo de píxeles blancos para las filas, denominado maxfil, y mostrar el número de filas y sus respectivas posiciones donde el conteo de píxeles blancos es mayor o igual a 0.95 × maxfil

Se realiza un análisis de la imagen cargada en formato BGR. A través de la utilización del método de Canny, se procesan los bordes de la imagen para facilitar el conteo de píxeles blancos.
Se utiliza la función cv2.imread() para cargar la imagen desde un archivo específico. Luego, la imagen es convertida a escala de grises mediante cv2.cvtColor(), lo que simplifica el procesamiento posterior.
Se aplica el operador de Canny (cv2.Canny()) para obtener los contornos de la imagen. Esta técnica resalta las áreas de la imagen donde hay cambios abruptos en la intensidad de los píxeles, lo que es útil para identificar bordes.

Se utiliza cv2.reduce() con el parámetro cv2.REDUCE_SUM para contar el número total de píxeles blancos en cada fila del resultado de Canny. Este conteo se almacena en row_counts.
El número total de píxeles blancos en cada fila se normaliza dividiendo por el número total de píxeles en la fila (que es igual a la anchura de la imagen). El resultado se almacena en rows, donde cada elemento representa el porcentaje de píxeles blancos en cada fila de la imagen procesada.

Se utiliza matplotlib para mostrar los resultados. En la primera subgráfica se presenta la imagen procesada por el operador de Canny, mientras que en la segunda subgráfica se muestra un gráfico de líneas que representa el porcentaje de píxeles blancos por fila.

# Tarea 2: Umbralizado de la Imagen Sobel y Comparación con Canny
En esta tarea se busca aplicar un umbralizado a la imagen resultante del operador Sobel, que fue previamente convertido a 8 bits. Posteriormente, se llevará a cabo un conteo de píxeles no nulos por filas y columnas, similar al realizado en la tarea anterior con la salida del operador Canny. Se calcularán los valores máximos de estos conteos, así como las filas y columnas que superen el 
0.95 × máximo. Finalmente, se remarcárán gráficamente las filas y columnas relevantes en la imagen. La tarea también requiere una comparación entre los resultados obtenidos a partir de Sobel y Canny.

Se inicia cargando una imagen en color y convirtiéndola a escala de grises utilizando cv2.cvtColor(). Este paso es crucial para aplicar filtros y detectar bordes de manera más efectiva.
Para reducir el ruido y mejorar la detección de bordes, se aplica un filtro gaussiano a la imagen gris.
Se utilizan los operadores Sobel en direcciones X e Y para calcular los bordes de la imagen. Los resultados se combinan utilizando cv2.add() para obtener una representación conjunta de los bordes.
El resultado de Sobel se convierte a un formato de 8 bits mediante cv2.convertScaleAbs(). Se aplica un umbral definido para separar los bordes significativos del fondo.

Se realiza un conteo de píxeles no nulos (blancos) tanto por filas como por columnas utilizando cv2.reduce(). Este conteo se normaliza para obtener el porcentaje de píxeles blancos en cada fila y columna.
Se determinan los valores máximos en las filas y columnas y se identifican aquellos que superan el 0.95 x máximo
Se visualizan los resultados del umbralizado de Sobel y se marcan las filas y columnas significativas. Se utiliza cv2.line() para dibujar líneas sobre la imagen donde se encuentran estas filas y columnas.

Se repite el proceso de conteo de píxeles y visualización para el resultado del operador Canny. Se generan gráficas de las cuentas por filas y columnas para ambas técnicas, lo que permite una comparación visual y cuantitativa.
Se presentan histogramas que muestran la distribución de píxeles blancos por filas y columnas tanto para la imagen de Sobel como para la de Canny.

# Tarea 3: Proponer un demostrador que capture las imágenes de la cámara, y les permita exhibir lo aprendido en estas dos prácticas ante quienes no cursen la asignatura :). Es por ello que además de poder mostrar la imagen original de la webcam, incluya al menos dos usos diferentes de aplicar las funciones de OpenCV trabajadas hasta ahora.
El código inicia la captura de video desde la webcam mediante cv2.VideoCapture(0). Esto permite que el usuario vea en tiempo real lo que se está capturando.

Se utiliza el método cv2.createBackgroundSubtractorMOG2() para inicializar un sustractor de fondo. Esto permite eliminar el fondo de la imagen y centrarse en los objetos en movimiento en primer plano. Se aplica a cada fotograma capturado.
Se carga una imagen de fondo (myBg) que se redimensiona para que coincida con las dimensiones de los fotogramas de la webcam.
Se generan máscaras para los objetos en primer plano y el fondo. Se utiliza cv2.threshold() para crear la máscara de los objetos detectados y cv2.bitwise_not() para invertir la máscara.
Con las máscaras generadas, se utilizan operaciones bit a bit (cv2.bitwise_and()) para extraer los objetos en primer plano y el fondo.
Se convierte el fotograma original a escala de grises y se aplica el operador Canny para detectar los bordes. Este proceso permite resaltar las líneas y contornos presentes en la imagen.

Se muestran varias ventanas que exhiben diferentes resultados:

- La imagen original de la webcam.
- La imagen procesada para eliminar el fondo.
- El resultado que combina los objetos en primer plano con el fondo personalizado.
- La detección de bordes.
- Un resultado que combina la detección de bordes con el fondo personalizado.

El programa se ejecuta en un bucle continuo hasta que se presiona la tecla ESC, momento en el cual se liberan los recursos y se cierran todas las ventanas abiertas.

# Tarea 4: Tras ver los vídeos My little piece of privacy, Messa di voce y Virtual air guitar proponer un demostrador reinterpretando la parte de procesamiento de la imagen, tomando como punto de partida alguna de dichas instalaciones.
En este caso, se utilizará la detección de caras para aplicar un efecto de pixelado en tiempo real.

El código utiliza el clasificador en cascada de Haar para detectar caras en tiempo real a partir de la captura de video de la webcam. Esto se logra mediante cv2.CascadeClassifier, que permite identificar y localizar las caras en la imagen.
Se inicializa la captura de video utilizando cv2.VideoCapture(0), que permite acceder a la cámara. Un bucle continuo captura los frames y los procesa uno a uno.
Cada frame se convierte a escala de grises utilizando cv2.cvtColor, lo que simplifica el proceso de detección de caras.
 Para cada cara detectada, se aplica un efecto de pixelado. Se extrae la región de interés (ROI) de la cara y se reduce su tamaño a 16x16 píxeles, lo que genera un efecto pixelado. Luego, se vuelve a escalar la imagen pixelada al tamaño original de la cara usando interpolación de vecino más cercano (cv2.INTER_NEAREST).
El resultado, que muestra el video con las caras pixeladas, se muestra en una ventana utilizando cv2.imshow
El bucle se mantiene activo hasta que se presiona la tecla 'q', momento en el cual se liberan los recursos utilizados y se cierran las ventanas

# Referencias y Fuentes Utilizadas
El código base utilizado en el desarrollo de las tareas fue proporcionado a través del Moodle de la asignatura Visión por Computador. Este código sirvió como punto de partida para implementar las soluciones propuestas en las distintas tareas.

Para la última tarea, se recurrió a ChatGPT para poder llevarla acabo. 
 
