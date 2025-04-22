# Limpieza y Preparación de Datos

## Problemas Identificados en el Dataset

Al realizar una inspección inicial del archivo `afluencia-metro.csv`, se han identificado varios problemas relacionados con la codificación de caracteres y la calidad de los datos:

### 1. Problemas de Codificación de Caracteres

En el dataset se observan caracteres mal codificados, especialmente en los nombres de las estaciones. Por ejemplo:
- "Isabel la CatÃ²lica" en lugar de "Isabel la Católica"
- "VelÃ³dromo" en lugar de "Velódromo"
- "BasÃ­lica" en lugar de "Basílica"
- "PantitlÃ¡n" en lugar de "Pantitlán"

Estos problemas son típicos cuando los datos se han codificado en UTF-8 pero se leen con una codificación diferente, o viceversa.

### 2. Necesidad de Estandarización

Los datos requieren estandarización en varios aspectos:
- Nombres de estaciones con formato consistente
- Eliminación de caracteres especiales no deseados
- Normalización de acentos y caracteres especiales

## Plan de Limpieza

1. **Corrección de Codificación**
   - Implementar la lectura correcta del archivo usando la codificación adecuada
   - Utilizar funciones de Spark para manejar la codificación UTF-8
   - Aplicar transformaciones para corregir caracteres especiales

2. **Estandarización de Datos**
   ```python
    import pandas as pd
    import unicodedata
    import re

    def eliminar_acentos(texto):
        # Convertir el texto a formato NFD donde los caracteres acentuados
        # se separan en la letra base y el acento
        texto_nfd = unicodedata.normalize('NFD', str(texto))
        
        # Eliminar todos los caracteres que son marcas diacríticas (acentos)
        texto_sin_acentos = ''.join([c for c in texto_nfd if not unicodedata.combining(c)])
        
        # Para el caso específico de la ñ, la reemplazamos por n
        texto_sin_enye = re.sub(r'[Ññ]', 'n', texto_sin_acentos)
        
        return texto_sin_enye

    # Leer el archivo CSV
    # Reemplaza 'tu_archivo.csv' con el nombre de tu archivo
    df = pd.read_csv('tu_archivo.csv')

    # Aplicar la función a todas las columnas de tipo string
    for columna in df.select_dtypes(include=['object']).columns:
        df[columna] = df[columna].apply(eliminar_acentos)

    # Guardar el resultado en un nuevo archivo CSV
    df.to_csv('archivo_limpio.csv', index=False)

    print("Proceso completado. Archivo limpio guardado como 'archivo_limpio.csv'")
   ```

3. **Validación de Datos**
   - Verificar que todos los nombres de estaciones sean legibles
   - Confirmar que los valores numéricos sean coherentes
   - Asegurar la consistencia en el formato de fechas

## Beneficios de la Limpieza

1. **Mejora en la Calidad del Análisis**
   - Datos más precisos para el análisis estadístico
   - Mejor interpretación de resultados
   - Visualizaciones más claras y comprensibles

2. **Facilidad de Uso**
   - Mejor legibilidad de los datos
   - Reducción de errores en el procesamiento
   - Mayor facilidad para realizar consultas y filtros

3. **Consistencia en los Resultados**
   - Garantía de que los nombres de estaciones son únicos y correctos
   - Eliminación de duplicados causados por problemas de codificación
   - Mayor confiabilidad en los análisis agregados

## Pasos Siguientes

1. Implementar las transformaciones necesarias usando PySpark
2. Validar los resultados de la limpieza
3. Documentar las transformaciones realizadas
4. Crear un conjunto de datos limpio para el análisis posterior

## Consideraciones Adicionales

- Mantener una copia del dataset original como respaldo
- Documentar todos los cambios realizados
- Implementar pruebas para verificar la calidad de los datos limpios
- Considerar la automatización del proceso para futuros datasets similares
