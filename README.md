# Proyecto de Big Data: Análisis de Salarios en Ciencia de Datos

## Tecnologías:

```
Apache Spark 3.5.5
Kafka 3.9.0
Python 3.8+
```

## Procesamiento Batch

Limpieza, transformación y EDA con Spark DataFrames.

### Archivo:

```
Procesamiento_en_batch_Spark_DF.ipynb
```

## Tecnologías:

```
Java: 8

Spark: 3.5.5

Python: 3.8+
```

## Ejecución en Google Colab:

### Instalar dependencias

!apt-get install openjdk-8-jdk-headless -qq > /dev/null
!pip install -q pyspark==3.5.5 kaggle

### Configurar entorno

```py
import os
os.environ["JAVA_HOME"] = "/usr/lib/jvm/java-8-openjdk-amd64"
```

### Descargar dataset de Kaggle

```py
from google.colab import files

files.upload()  # Subir kaggle.json

!mkdir -p ~/.kaggle && mv kaggle.json ~/.kaggle/ && chmod 600 ~/.kaggle/kaggle.json

!kaggle datasets download -d yusufdelikkaya/datascience-salaries-2024
!unzip datascience-salaries-2024.zip -d data
```

### Ejecutar notebook (EDA, limpieza, almacenamiento)

Ejecutar cada celda del cuaderno.

# Resultados Batch:

## Dataset procesado en formato cvs (data/processed_DataScience_salaries_2024_csv).

Visualizaciones

# Procesamiento en Tiempo Real

Streaming con Spark Structured Streaming y Kafka

## Archivo:

```
Procesamiento_en_tiempo_real_(Spark_Streaming_&_Kafka).ipynb
```

## Tecnologías:

```
Kafka: 3.9.0 (Scala 2.12)

Spark Streaming: 3.5.5

Java: 8
```

## Ejecución en Google Colab:

```py
# Instalar Kafka y dependencias
!apt-get install openjdk-8-jdk-headless -qq > /dev/null
!wget https://downloads.apache.org/kafka/3.9.0/kafka_2.12-3.9.0.tgz
!tar -xzf kafka_2.12-3.9.0.tgz

# Iniciar servicios Kafka
!kafka_2.12-3.9.0/bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
!kafka_2.12-3.9.0/bin/kafka-server-start.sh -daemon config/server.properties
import time; time.sleep(10)

# Crear topic
!kafka_2.12-3.9.0/bin/kafka-topics.sh --create --topic salaries-stream --bootstrap-server localhost:9092

# Generar datos fake (en segundo plano)
import threading
from kafka_producer import generate_fake_data
thread = threading.Thread(target=generate_fake_data)
thread.start()

# Spark Streaming (procesamiento en ventanas)
from pyspark.sql import SparkSession
spark = SparkSession.builder \
    .config("spark.jars.packages", "org.apache.spark:spark-sql-kafka-0-10_2.12:3.5.5") \
    .getOrCreate()

# ... resto del código de streaming ...

# Detener servicios
!kafka_2.12-3.9.0/bin/kafka-server-stop.sh
!kafka_2.12-3.9.0/bin/zookeeper-server-stop.sh
```

## Resultados Streaming:

```
-----------------------------------------------------------------------
|  Ventana Temporal      | Puesto         | Eventos | Salario Promedio |
-----------------------------------------------------------------------
| 2024-06-01 14:05:00   | Data Scientist | 15      | $98,450          |
-----------------------------------------------------------------------
| 2024-06-01 14:10:00   | Data Scientist | 15      | $87,450          |
```

# Estructura del Repositorio

```
├── batch_processing/
│   ├── Procesamiento_en_batch_Spark_DF.ipynb
│   └── data/
├── streaming_processing/
│   ├── Procesamiento_en_tiempo_real_(Spark_Streaming_&_Kafka).ipynb
│   ├── #
│   └── kafka_2.12-3.9.0/          # Binarios de Kafka
├── docs/ # Comming
│   ├── batch_results.png # Comming
│   └── streaming_dashboard.png # Comming
└── README.md
```

# Notas Clave

    1. Los notebooks están diseñados para ejecutarse en Google Colab en orden secuencial.
    2. No saltar celdas (especialmente en Kafka por dependencias de servicios).

# Requisitos de Memoria:

Kafka requiere >4GB RAM. Usa Colab Pro/Premium para streaming.

# Versiones Exactas:

```py
# Verificar en Colab:
!java -version  # Debe mostrar 1.8
!python --version  # 3.8+

from pyspark.sql import SparkSession; print(SparkSession.builder.getOrCreate().version)  # 3.5.5
```

# Solución de Problemas Comunes

```
------------------------------------------------------------------------------------------------------------
|           Error	                |                   Solución                                            |
------------------------------------------------------------------------------------------------------------
|java.lang.NoClassDefFoundError 	| Verificar versión de Scala (2.12 para Kafka 3.9.0)                    |
------------------------------------------------------------------------------------------------------------
|TopicExistsException	            | Eliminar topic: kafka-topics.sh --delete --topic salaries-stream      |
------------------------------------------------------------------------------------------------------------
|Spark Streaming no muestra datos   | Verificar producer: kafka-console-consumer.sh --topic salaries-stream |
------------------------------------------------------------------------------------------------------------
```
