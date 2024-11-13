# Autoría
Este programa ha sido desarrollado por Anixia Di Gregorio y Daniel Fabelo 

# Reconocimiento de Género y Filtros de Superposición

El objetivo del trabajo es detectar el género de una persona a través de la webcam, utilizando diferentes métodos de detección y reconocimiento facial. En particular, según el género detectado, se aplica un filtro: si la persona detectada es una mujer, se aplican bigotes, mientras que si la persona detectada es un hombre, se aplican labios rojos.

## Código 1: DeepFace y RetinaFace

En esta versión se utiliza DeepFace y RetinaFace para el análisis de género y la detección facial. En particular, el análisis de género se realiza cada 2,5 segundos para reducir la carga de procesamiento, mientras que la detección facial es continua y las imágenes de los labios y los bigotes se redimensionan y superponen sobre el rostro.

### Carga de los filtros
```
lipstick_image = cv2.imread('../P5/images/labiosrojos.png', cv2.IMREAD_UNCHANGED)
mustache_image = cv2.imread('../P5/images/mostacho.png', cv2.IMREAD_UNCHANGED)

if lipstick_image is None:
    print("Error: No se pudo cargar la imagen de labios.")
if mustache_image is None:
    print("Error: No se pudo cargar la imagen de bigote.") 
    
```

### Análisis de género
```
obj = DeepFace.analyze(frame, actions=['gender'], enforce_detection=False)
gender = obj[0]['dominant_gender']
```

### Detección de rostros
```
faces = DeepFace.extract_faces(frame, detector_backend="retinaface", align=False)
if len(faces) > 0 and 'facial_area' in faces[0]:
    facial_area = faces[0]['facial_area']
    x, y, w, h = facial_area['x'], facial_area['y'], facial_area['w'], facial_area['h']
```

### Ventajas e Desventajas
Ventajas: 
* Alta precisión en la detección de rostros y en el análisis de género.

Desventajas: 
* Requiere mucha potencia computacional.

## Código 2: Haar Cascade

El segundo enfoque utiliza el algoritmo Haar Cascade de OpenCV para detectar rostros, mientras que DeepFace se utiliza para la detección de género.

### Análisis de género
```
resized_frame = cv2.resize(frame, (640, 480))
obj = DeepFace.analyze(resized_frame, actions=['gender'], enforce_detection=False)
gender = obj[0]['dominant_gender']
```

### Detección de rostros
```
gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
face_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + "haarcascade_frontalface_default.xml")
faces = face_cascade.detectMultiScale(gray, 1.1, 4)

for (x, y, w, h) in faces:
    # Applica il filtro in base al genere rilevato
    if gender == 'Man' and lipstick_image is not None:
        overlay_img = cv2.resize(lipstick_image, (w // 2, h // 5))
        frame = overlay_image(frame, overlay_img, (x + w // 4, y + int(h * 0.7)))
    elif gender == 'Woman' and mustache_image is not None:
        overlay_img = cv2.resize(mustache_image, (w // 2, h // 6))
        frame = overlay_image(frame, overlay_img, (x + w // 4, y + int(h * 0.65)))
```
### Ventajas e Desventajas
Ventajas: 
* Alta velocidad de detección con un uso limitado de recursos computacionales

Desventajas: 
* Menor precisión en la detección de rostros

## Código 3: MediaPipe

Este enfoque utiliza MediaPipe para la detección y mapeo de landmarks faciales, junto con DeepFace para el análisis de género. Con esta combinación, el código no solo detecta rostros, sino que también ubica puntos específicos en la cara, como los labios y las esquinas de la boca. Esto permite aplicar efectos de manera precisa.

Ahora a los hombres se les pintará los labios directamente, en lugar de superponer una imagen.

Para ejecutar esta versión, será necesario instalar mediapipe en nuestro environment. Se puede hacer mediante la siguiente orden:
```
pip install mediapipe

```

### Detección de rostros y Aplicación de filtros
```
frame_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
results = face_mesh.process(frame_rgb)

if results.multi_face_landmarks:
    for face_landmarks in results.multi_face_landmarks:
        ih, iw, _ = frame.shape

        if gender == 'Man':
            # Pintar labios de rojo utilizando landmarks
            lip_points = [
                61, 146, 91, 181, 84, 17, 314, 405, 321, 375, 291, 308,
                324, 318, 402, 317, 14, 87, 178,  95, 88, 178, 191, 80,
                81, 82, 13, 312, 311, 310, 415, 308
            ]
            lip_coords = [(int(face_landmarks.landmark[point].x * iw), int(face_landmarks.landmark[point].y * ih)) for point in lip_points]
            
            # Crear y aplicar una máscara roja sobre los labios
            lip_mask = np.zeros_like(frame)
            cv2.fillPoly(lip_mask, [np.array(lip_coords, np.int32)], (0, 0, 255))
            frame = cv2.addWeighted(frame, 1, lip_mask, 0.4, 0)

        elif gender == 'Woman' and mustache_image is not None:
            # Colocar el bigote sobre la boca utilizando puntos clave
            left_mouth_corner = face_landmarks.landmark[78]
            right_mouth_corner = face_landmarks.landmark[308]

            # Ajustar tamaño y posición del bigote
            x1, y1 = int(left_mouth_corner.x * iw), int(left_mouth_corner.y * ih)
            x2, y2 = int(right_mouth_corner.x * iw), int(right_mouth_corner.y * ih)
            mustache_width = x2 - x1
            mustache_height = int(mustache_width * mustache_image.shape[0] / mustache_image.shape[1])
            overlay_img = cv2.resize(mustache_image, (mustache_width, mustache_height))
            
            # Colocar el bigote en la posición calculada
            y_offset = int(y1 - mustache_height / 2)
            frame = overlay_image(frame, overlay_img, (x1, y_offset))

```

### Ventajas e Desventajas
Ventajas: 
* Buena precisión y rendimiento equilibrado, incluso en dispositivos menos potentes.

Desventajas: 
* Más ligero que el primer enfoque, pero menos preciso en algunas circunstancias.

## Resultado
![Resultado](images/resultado_gif.gif)

## Conclusiones

Para concluir, podemos decir que cada uno de estos algoritmos puede ser útil dependiendo de los recursos disponibles y las necesidades específicas, en particular: 
* DeepFace y RetinaFace se recomiendan para entornos de alto rendimiento donde se requiere una alta precisión. 
* Haar Cascade es adecuado para aplicaciones rápidas en dispositivos menos potentes, pero con una mayor tolerancia a los errores. 
* MediaPipe representa un compromiso entre las dos soluciones, adecuado para escenarios donde se necesita un buen equilibrio entre precisión y velocidad.

## Referencias

Para la ejecución de esta práctica, se ha usado el código de base proporcionado por los profesores y se ha empleado ChatGPT para aclarar las cuestiones relacionadas con mediapipe.