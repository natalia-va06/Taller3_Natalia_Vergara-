# Taller3_Natalia_Vergara-grupo2

# 1. Descripción del Proyecto
Este proyecto consiste en el desarrollo de una aplicación en Python orientada a la informática médica. Su objetivo principal es automatizar la lectura, extracción y estructuración de metadatos clínicos a partir de archivos en formato DICOM (Digital Imaging and Communications in Medicine), simulando parte del flujo de trabajo de un sistema de comunicación y archivado de imágenes (PACS). 

La aplicación escanea un directorio de forma organizada mediante Programación Orientada a Objetos (POO) a través de una clase denominada 'ProcesadorDICOM'. El sistema extrae la información demográfica y del estudio del paciente para estructurarla en un DataFrame de Pandas y exportarla a un archivo compatible (.csv). Adicionalmente, realiza análisis estadísticos de intensidad lumínica con NumPy y lleva a cabo un pipeline de preprocesamiento y análisis computacional de imágenes médicas empleando la librería de visión por computadora openCV (normalización, ecualización de histograma y detección de bordes mediante el algoritmo de Canny), almacenando los resultados visuales en formato .png.


# 2. Importancia de DICOM y HL7 en la Interoperabilidad en Salud

# ¿Por qué son cruciales?
En los entornos de salud modernos, los pacientes interactúan con múltiples especialistas, laboratorios y centros de diagnóstico que utilizan tecnologías de diferentes fabricantes. Sin estándares globales, los sistemas de información hospitalaria estarían fragmentados ya que no podrían comunicarse entre sí. DICOM y HL7 son cruciales porque actúan como lenguajes universales que garantizan la interoperabilidad, permitiendo que la información clínica fluya de manera segura, exacta y oportuna para dar soporte a las decisiones médicas.

# Diferencias Conceptuales
Aunque ambos estándares cooperan para optimizar el ecosistema de la salud digital, poseen un enfoque y un dominio de aplicación conceptualmente distintos:

**"Diferencia entre DICOM y HL7 (Interoperabilidad)"**

*Tipo de información que maneja*
**DICOM:** Manejo, almacenamiento y transmisión de imágenes médicas y datos fuertemente vinculados a ellas (geometría de adquisición, dosis de radiación, etc.).  
**HL7 (Health Level Seven):** Intercambio de información clínica y administrativa textual (historias clínicas, variables demográficas, órdenes de laboratorio, admisiones y altas). 

*Estructura de la información*
**DICOM:** Combina una cabecera binaria estructurada (tags) unida indisolublemente a una matriz de píxeles (imagen). 
**HL7 (Health Level Seven):** Estructuras de mensajes basados en texto plano u objetos dinámicos (como segmentos delimitados en HL7 v2 o recursos JSON/XML en HL7 FHIR). 

*Entidades que Comunica*
**DICOM:** Conecta principalmente modalidades de adquisición (CT, MR, RX) con servidores de almacenamiento (PACS) y estaciones de visualización médica. 
**HL7 (Health Level Seven):** Conecta sistemas de información global del hospital, como el HIS (Hospital Information System) y el RIS (Radiology Information System). 

# 3. Análisis de Técnicas de Procesamiento con OpenCV en Contextos Médicos

El preprocesamiento de imágenes biomédicas es una etapa crítica antes del diagnóstico asistido por computadora (CAD) o la inspección visual del radiólogo. A continuación se analizan las ventajas, limitaciones y escenarios clínicos de las técnicas implementadas:

# A. Ecualización de Histograma (`cv2.equalizeHist`)
**Ventajas:** Maximiza el contraste global de la imagen redistribuyendo uniformemente las densidades de los píxeles. Permite hacer visibles estructuras anatómicas o micro-calcificaciones sutiles que originalmente se encontraban ocultas en zonas subexpuestas (muy oscuras) o sobreexpuestas (muy claras) de una radiografía o tomografía.
**Limitaciones:** Al operar de manera global, puede disminuir el contraste de áreas que ya contaban con una iluminación óptima. Asimismo, tiende a amplificar de forma agresiva el ruido de fondo o los artefactos físicos inherentes a la captura médica.
**Escenario Útil:** Es altamente beneficioso en mamografías para resaltar nódulos densos o en radiografías de tórax con baja exposición técnica.
**Escenario Perjudicial:** Es contraproducente en resonancias magnéticas de cerebro de alta resolución donde las sutiles transiciones de la sustancia gris y blanca pueden distorsionarse o perder su fidelidad diagnóstica por una sobre-ecualización.

### B. Detección de Bordes con Canny (cv2.Canny)
**Ventajas:** Identifica discontinuidades abruptas en la intensidad de los píxeles, logrando aislar contornos estructurales con una excelente relación señal/ruido gracias a su paso previo de filtrado Gaussiano. Es una herramienta clave para la segmentación de órganos.
**Limitaciones:** Su éxito depende de que se elijan a mano los valores de corte (umbrales). Si se configuran mal, el programa puede cometer dos errores: no ver los bordes reales de los órganos si son muy borrosos, o inventarse líneas falsas debido al ruido o "grano" de la imagen.
**Escenario Útil:** Es ideal para la cardiología (por ejemplo, para dibujar de forma automática el contorno del corazón en una ecografía) o en ortopedia para resaltar la silueta exacta de un hueso largo en una radiografía. Sirve mucho aquí porque el cambio de color entre el hueso (blanco) y el fondo (oscuro) es muy marcado.
**Escenario Perjudicial:** No sirve en tomografías de zonas con tejidos blandos (como el abdomen o la barriga). En estos exámenes, los órganos vecinos tienen tonos de gris muy parecidos y no hay divisiones claras, sino que un tejido se va mezclando con el otro. Si se aplica Canny ahí, el código se confunde y dibuja un montón de líneas enredadas que no tienen ningún sentido para un médico.

## 4. Dificultades Encontradas y Conclusión

### Dificultades Encontradas
1. **Datos de Píxeles incompatibles:** Durante el procesamiento del banco de datos de prueba (pydicom-data), se identificaron excepciones en OpenCV debido a formatos raros. Esta situación requirió la implementación de bloques de control de excepciones try/except para prevenir el colapso total del programa ante archivos no-imagen (como Reportes Estructurados SR o estados de presentación PR).
2. **Tags Faltantes:** Múltiples archivos DICOM de prueba carecían de etiquetas (como Rows o Columns), reflejándose como "No disponible" en el DataFrame. Esto evidenció la variabilidad real de las cabeceras DICOM en entornos clínicos debido a políticas que vuelven anónimos los datos de los pacientes.

# Importancia de las Herramientas de Python
Python se ha consolidado como uno de los lenguajes de programación más importantes en la bioingeniería e informática médica debido al ecosistema de sus librerías de software libre. La combinación de pydicom para descodificar la complejidad del estándar médico, NumPy para el tratamiento de imágenes como matrices, Pandas para la gestión rápida de Big Data clínica, y OpenCV para el procesamiento en tiempo real, demuestra que es posible construir soluciones diagnósticas de nivel institucional dinamizando los procesos e impulsando la innovación y la investigación médica.


**Estudiante:** Natalia Carolina Vergara Álvarez
**Grupo info:** 2

