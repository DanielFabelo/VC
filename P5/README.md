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

El último enfoque utiliza MediaPipe para la detección de rostros. Esto permite obtener un buen equilibrio entre rendimiento y precisión.

### Detección de rostros
```
frame_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
results = face_detection.process(frame_rgb)

if results.detections:
    for detection in results.detections:
        bboxC = detection.location_data.relative_bounding_box
        ih, iw, _ = frame.shape
        x, y, w, h = int(bboxC.xmin * iw), int(bboxC.ymin * ih), int(bboxC.width * iw), int(bboxC.height * ih)

        # Applica il filtro in base al genere rilevato
        if gender == 'Man' and lipstick_image is not None:
            overlay_img = cv2.resize(lipstick_image, (w // 2, h // 5))
            frame = overlay_image(frame, overlay_img, (x + w // 4, y + int(h * 0.6)))
        elif gender == 'Woman' and mustache_image is not None:
            overlay_img = cv2.resize(mustache_image, (w // 2, h // 6))
            frame = overlay_image(frame, overlay_img, (x + w // 4, y + int(h * 0.55)))
```

### Ventajas e Desventajas
Ventajas: 
* Buena precisión y rendimiento equilibrado, incluso en dispositivos menos potentes.

Desventajas: 
* Más ligero que el primer enfoque, pero menos preciso en algunas circunstancias.

## Resultado
![Resultado](./images/demo.gif)

## Conclusiones

Para concluir, podemos decir que cada uno de estos algoritmos puede ser útil dependiendo de los recursos disponibles y las necesidades específicas, en particular: 
* DeepFace y RetinaFace se recomiendan para entornos de alto rendimiento donde se requiere una alta precisión. 
* Haar Cascade es adecuado para aplicaciones rápidas en dispositivos menos potentes, pero con una mayor tolerancia a los errores. 
* MediaPipe representa un compromiso entre las dos soluciones, adecuado para escenarios donde se necesita un buen equilibrio entre precisión y velocidad.

