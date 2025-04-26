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
    from pyspark.sql import SparkSession
    from pyspark.sql.functions import col, regexp_replace
    from pyspark.sql.types import StringType

    # Crear sesión de Spark
    spark = SparkSession.builder.appName("LimpiezaMetro").getOrCreate()

    # Leer el archivo CSV
    df = spark.read.csv("afluencia-metro.csv", header=True, inferSchema=True)

    # Función para corregir la codificación de caracteres
    def corregir_codificacion(df, column_name):
        return df.withColumn(column_name,
            regexp_replace(
                regexp_replace(
                    regexp_replace(
                        regexp_replace(
                            regexp_replace(
                                regexp_replace(
                                    regexp_replace(
                                        regexp_replace(
                                            regexp_replace(
                                                regexp_replace(
                                                    col(column_name),
                                                        "Ã³", "ó"),
                                                    "Ã¡", "á"),
                                                "Ã­", "í"),
                                            "Ã©", "é"),
                                        "Ãº", "ú"),
                                    "Ã±", "ñ"),
                                "Ã±", "ñ"),
                            "Ã", "í"),
                        "Ã", "á"),
                    "Ã", "ó")
        )

    # Aplicar la corrección a la columna de estaciones
    df_corregido = corregir_codificacion(df, "estacion")

    # Guardar el resultado
    df_corregido.write.csv("afluencia-metro-corregido.csv", header=True)
    ```

   Este código:
   1. Crea una sesión de Spark
   2. Lee el archivo CSV original
   3. Define una función que corrige los caracteres mal codificados
   4. Aplica la corrección a la columna de estaciones
   5. Guarda el resultado en un nuevo archivo

   Los caracteres que se corrigen son:
   - "Ã³" → "ó"
   - "Ã¡" → "á"
   - "Ã­" → "í"
   - "Ã©" → "é"
   - "Ãº" → "ú"
   - "Ã±" → "ñ"
   - "Ã" → "í", "á", "ó" (según el contexto)

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
