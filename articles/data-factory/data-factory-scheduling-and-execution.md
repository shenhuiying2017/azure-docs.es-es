<properties 
	pageTitle="Programación y ejecución con Factoría de datos" 
	description="Obtenga información sobre los aspectos de programación y ejecución del modelo de aplicación de Factoría de datos de Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/22/2016" 
	ms.author="spelluru"/>

# Programación y ejecución con Factoría de datos
  
En este artículo se explican los aspectos de programación y ejecución del modelo de aplicación de Factoría de datos de Azure. Este artículo se basa en los artículos sobre la [creación de canalizaciones](data-factory-create-pipelines.md) y la [creación de conjuntos de datos](data-factory-create-datasets.md) y asume que se conocen los conceptos básicos del modelo de aplicación de Data Factory: actividad, canalizaciones, servicios vinculados y conjuntos de datos.

## Programación de actividades

Con la sección **scheduler** de JSON de la actividad, puede especificar una programación periódica de la actividad. Por ejemplo, puede programar una actividad cada hora como sigue:

	"scheduler": {
		"frequency": "Hour",
	    "interval": 1
	},  
    
![Ejemplo de programador](./media/data-factory-scheduling-and-execution/scheduler-example.png)

Como se muestra en el diagrama, al especificar una programación para la actividad se crea una serie de ventanas de saltos. Las ventanas de saltos son series de intervalos de tiempo de tamaño fijo, no superpuestos y contiguos. Estas ventanas de saltos lógicas para la actividad se denominan **ventanas de actividad**.
 
Para la ventana de actividad actualmente en ejecución, se puede acceder al intervalo de tiempo asociado a la ventana de actividad con las variables del sistema [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) y [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) del JSON de la actividad. Puede usar estas variables con distintos fines en el JSON de la actividad; por ejemplo, para seleccionar datos de los conjuntos de datos de entrada y salida que representan los datos de la serie temporal.

La propiedad **scheduler** admite las mismas subpropiedades que la propiedad **availability** en un conjunto de datos. Consulte el artículo [Disponibilidad del conjunto de datos](data-factory-create-datasets.md#Availability) para más información. Ejemplos: programar con un desplazamiento de tiempo específico, establecer el modo para alinear el procesamiento al principio del intervalo de la ventana de actividad o al final.

Especificar las propiedades del programador para una actividad es opcional. Si las especifica, deben coincidir con la cadencia que indique en la definición del conjunto de datos de salida. Actualmente, el conjunto de datos de salida es lo que controla la programación, por lo que debe crear un conjunto de datos de salida aunque la actividad no genere ninguna salida. Si la actividad no toma ninguna entrada, puede omitir la creación del conjunto de datos de entrada.

## Conjuntos de datos y segmentos de datos de series temporales

Los datos de series temporales son una secuencia continua de puntos de datos que consisten normalmente en mediciones sucesivas realizadas en un intervalo de tiempo. Ejemplos comunes de datos de series temporales incluyen datos de sensores y datos de telemetría de aplicación, entre otros.

Con Factoría de datos de Azure, puede procesar datos de series temporales por lotes con las ejecuciones de la actividad. Normalmente, hay un ritmo periódico en el que llegan los datos de entrada y los datos de salida tienen que producirse. Esta cadencia se modela especificando la sección **availability** en el conjunto de datos como sigue:

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

Cada unidad de datos consumida y producida por la ejecución de una actividad se denomina **segmento** de datos. El siguiente diagrama muestra un ejemplo de una actividad con un conjunto de datos de entrada y un conjunto de datos de salida cada uno con la disponibilidad establecida en una frecuencia de cada hora.

![Programador de disponibilidad](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Los segmentos de datos de cada hora para el conjunto de datos de entrada y salida se muestran en el diagrama. El diagrama muestra tres segmentos de entrada que están listos para su procesamiento y la ejecución de la actividad 10-11 AM en curso que produce el segmento de salida 10-11 AM.

Se puede acceder al intervalo de tiempo asociado al segmento actual que se está produciendo en el JSON del conjunto de datos con las variables [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) y [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables).

Actualmente, Data Factory requiere que el programa especificado en la actividad coincida exactamente con el programa especificado en la disponibilidad del conjunto de datos de salida. Por tanto, WindowStart, WindowEnd, SliceStart y SliceEnd siempre se asignan al mismo período y un segmento de salida único.

Para más información sobre las diferentes propiedades disponibles para la sección availability, consulte el artículo sobre la [creación de conjuntos de datos](data-factory-create-datasets.md).

## Ejemplo: Actividad de copia que mueve datos de SQL Azure a un blob de Azure

Reunamos todo y pongámoslo en marcha creando una canalización que copia datos de una tabla de SQL Azure en un blob de Azure cada hora.

**Entrada: Conjunto de datos de SQL Azure**

	{
	    "name": "AzureSqlInput",
	    "properties": {
	        "published": false,
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": true,
	        "policy": {}
	    }
	}


**frequency** está establecido en **Hour** e **interval** está establecido en **1** en la sección **availability**.

**Salida: conjunto de datos de blob de Azure**
	
	{
	    "name": "AzureBlobOutput",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
	            "format": {
	                "type": "TextFormat"
	            },
	            "partitionedBy": [
	                {
	                    "name": "Year",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "yyyy"
	                    }
	                },
	                {
	                    "name": "Month",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%M"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%d"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%H"
	                    }
	                }
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


**frequency** está establecido en **Hour** e **interval** está establecido en **1** en la sección **availability**.



**Actividad: Actividad de copia**

	{
	    "name": "SamplePipeline",
	    "properties": {
	        "description": "copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "name": "AzureSQLtoBlob",
	                "description": "copy activity",	
	                "typeProperties": {
	                    "source": {
	                        "type": "SqlSource",
	                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 100000,
	                        "writeBatchTimeout": "00:05:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "AzureSQLInput"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOutput"
	                    }
	                ],
	       			"scheduler": {
	          			"frequency": "Hour",
	          			"interval": 1
	        		}
	            }
	        ],
	        "start": "2015-01-01T08:00:00Z",
	        "end": "2015-01-01T11:00:00Z"
	    }
	}


El ejemplo anterior muestra las secciones de programación de la actividad y de disponibilidad del conjunto de datos establecidas en una frecuencia de cada hora. El ejemplo muestra cómo puede usar **WindowStart** y **WindowEnd** para seleccionar datos pertinentes para la ejecución de una actividad y copiarlos en un blob con el valor de **folderPath** adecuado. folderPath se parametriza para tener una carpeta independiente para cada hora.

Cuando se ejecutan tres de los segmentos entre las 8:00 y las 11:00 y los datos en SQL de Azure son los siguientes:

![Entrada de ejemplo](./media/data-factory-scheduling-and-execution/sample-input-data.png)

Al implementar la canalización, el blob de Azure se rellena como sigue:

1.	Archivo mypath/2015/1/1/8/Data.&lt;Guid&gt;.txt con datos:

			10002345,334,2,2015-01-01 08:24:00.3130000
			10002345,347,15,2015-01-01 08:24:00.6570000
			10991568,2,7,2015-01-01 08:56:34.5300000

	> [AZURE.NOTE] &lt;Guid&gt; se reemplaza con el identificador único global real. Nombre del archivo de ejemplo: Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
2.	Archivo mypath/2015/1/1/9/Data.&lt;Guid&gt;.txt con datos:

			10002345,334,1,2015-01-01 09:13:00.3900000
			24379245,569,23,2015-01-01 09:25:00.3130000
			16777799,21,115,2015-01-01 09:47:34.3130000
3.	Archivo mypath/2015/1/1/10/Data.&lt;Guid&gt;.txt sin datos.


## Segmentos de datos, período activo de canalización y ejecución de segmentos simultáneos

El artículo sobre la [creación de canalizaciones](data-factory-create-pipelines.md) introdujo el concepto de período activo para una canalización especificada mediante la configuración de las propiedades **start** y **end**.
 
Puede establecer la fecha de inicio para el período activo de la canalización en el pasado. En ese caso, Data Factory calcula automáticamente (rellena hacia atrás) todos los segmentos de datos en el pasado y empieza a procesarlos.

Con los segmentos de datos con el fondo relleno, es posible configurarlos para que se ejecuten en paralelo. Puede hacerlo estableciendo la propiedad [concurrency](data-factory-create-pipelines.md) en la sección **policy** del JSON de la actividad, tal como se muestra en el artículo de **creación de canalizaciones**.

## Error al volver a ejecutar segmentos de datos y el seguimiento de dependencias de datos automático

Puede supervisar la ejecución de los segmentos de manera visual enriquecida. Consulte **Supervisión y administración de canalizaciones mediante** [hojas del Portal de Azure](data-factory-monitor-manage-pipelines.md) (o) [Aplicación de supervisión y administración](data-factory-monitor-manage-app.md) para más información.

Observe el ejemplo siguiente, que muestra dos actividades. Activity1 produce un conjunto de datos de series temporales con segmentos de salida que se han consumido como entrada por Activity2 para generar el conjunto de datos de series temporales de salida final.

![Segmento con errores](./media/data-factory-scheduling-and-execution/failed-slice.png)

<br/>

El diagrama muestra que en tres segmentos recientes se produjo un error al generar el segmento 9-10 AM para **Dataset2**. Factoría de datos realiza automáticamente un seguimiento de la dependencia para el conjunto de datos de series temporales y, como resultado, retiene el comienzo de la ejecución de la actividad para el segmento de nivel inferior de 9-10 AM.


Las herramientas de administración y supervisión de Factoría de datos permiten profundizar en los registros de diagnóstico para que el segmento con error pueda encontrar la causa raíz del problema y solucionarlo. Una vez solucionado el problema, puede iniciar fácilmente la ejecución de la actividad para generar el segmento con error. Para más información sobre cómo repetir la ejecución y entender las transiciones de estado para segmentos de datos, consulte el artículo sobre la **supervisión y administración de canalizaciones mediante** [hojas del Portal de Azure](data-factory-monitor-manage-pipelines.md) (o) la [aplicación de supervisión y administración](data-factory-monitor-manage-app.md).

Una vez que haya repetido la ejecución del segmento 9-10 AM para dataset2 y esté preparado, Data Factory inicia la ejecución del segmento dependiente de 9-10 AM en el conjunto de datos final, como se muestra en el diagrama siguiente.

![Repetición de ejecución de un segmento con errores](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

Para un análisis más profundo de la especificación y el seguimiento de dependencias para una cadena de actividades, consulte las secciones siguientes.

## Encadenamiento de actividades
Puede encadenar dos actividades haciendo que el conjunto de datos de salida de una actividad sea el conjunto de datos de entrada de la otra actividad. Las actividades pueden estar en la misma canalización o en canalizaciones diferentes. La segunda actividad se ejecuta solo cuando la primera de ellas se completa correctamente.

Por ejemplo, considere el siguiente caso:
 
1.	La canalización P1 incluye la actividad A1 que requiere el conjunto de datos de entrada externo D1 y genera el conjunto de datos de **salida** **D2**.
2.	La canalización P2 incluye la actividad A2 que requiere una **entrada** del conjunto de datos **D2** y genera el conjunto de datos de salida D3.
 
En este escenario, la actividad A1 se ejecuta cuando los datos externos están disponibles y se alcanza la frecuencia de disponibilidad programada. La actividad A2 se ejecuta cuando están disponibles los segmentos programados de D2 y se alcanza la frecuencia de disponibilidad programada. Si se produce un error en uno de los segmentos del conjunto de datos D2, A2 no se ejecuta para ese segmento hasta que está disponible.

La Vista de diagrama tendría un aspecto similar al siguiente diagrama:

![Encadenamiento de las actividades de dos canalizaciones](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

La Vista de diagrama con ambas actividades en la misma canalización tendría un aspecto similar al siguiente diagrama:

![Encadenamiento de las actividades de la misma canalización](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### Copia ordenada
Es posible ejecutar varias operaciones de copia sucesivas de manera secuencial y ordenada. Supongamos que tiene dos actividades de copia en una canalización: ActividadCopia1 y ActividadCopia2 con los siguientes conjuntos de datos de entrada y salida.

ActividadCopia1: entrada: ConjuntoDatos1; salida: ConjuntoDatos2

ActividadCopia2: entrada: ConjuntoDatos2; salida: ConjuntoDatos4

ActividadCopia2 solo se ejecutaría si ActividadCopia1 se hubiera ejecutado correctamente y ConjuntoDatos2 estuviera disponible.

En el ejemplo, CopyActivity2 puede tener una entrada distinta, como Dataset3, pero también debe especificar Dataset2 como una entrada de CopyActivity2 para que la actividad no se ejecute hasta que se haya completado CopyActivity1. Por ejemplo:

ActividadCopia1: entrada: ConjuntoDatos1; salida: ConjuntoDatos2

ActividadCopia2: entrada: ConjuntoDatos3 y ConjuntoDatos2; salida: ConjuntoDatos4

Cuando se especifican varias entradas, solo se usa el primer conjunto de datos de entrada para copiar los datos. Sin embargo, los demás conjuntos de datos se usan como dependencias. ActividadCopia2 solo empezaría a ejecutarse cuando se cumplieran las siguientes condiciones:

- ActividadCopia1 se ha completado correctamente y ConjuntoDatos2 está disponible. Este conjunto de datos no se usa al copiar datos en Dataset4. Solo actúa como una dependencia de programación de ActividadCopia2.
- ConjuntoDatos3 está disponible. Este conjunto de datos representa los datos que se copian en el destino.



## Modelado de conjuntos de datos con distintas frecuencias

En los ejemplos, las frecuencias de los conjuntos de datos de entrada y salida y de la ventana de programación de actividad eran las mismas. Algunos escenarios requieren que se puedan producir resultados a una frecuencia diferente de las frecuencias de una o más entradas. Factoría de datos admite el modelado de estos escenarios.

### Ejemplo 1: Generar un informe de salida diario para los datos de entrada que está disponibles cada hora

Considere un escenario donde hemos introducido datos de medición desde sensores disponibles cada hora en el Blob de Azure. Desea generar un informe agregado diario con estadísticas como media, máx., mín., etc., para el día con la [Actividad de Hive](data-factory-hive-activity.md) de Data Factory.

A continuación, se muestra cómo puede modelar este escenario con Data Factory:

**Conjunto de datos del blob de Azure de entrada:**

Se quitan los archivos de entrada de cada hora en la carpeta para el día especificado. La disponibilidad para la entrada se establece cada hora (frecuencia: hora, intervalo: 1).

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Conjunto de datos de blob de Azure de salida**

Cada día se crea un archivo de salida en la carpeta del día. La disponibilidad de la salida se establece en diariamente (frecuencia: día e intervalo: 1).


	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Actividad: actividad de Hive en una canalización**

El script de Hive recibe la información de fecha y hora adecuada como parámetros que usan la variable **WindowStart** como se muestra a continuación. El script de Hive usa esta variable para cargar los datos de la carpeta correcta para el día y ejecutar la agregación para generar la salida.

		{  
		    "name":"SamplePipeline",
		    "properties":{  
		    "start":"2015-01-01T08:00:00",
		    "end":"2015-01-01T11:00:00",
		    "description":"hive activity",
		    "activities": [
		        {
		            "name": "SampleHiveActivity",
		            "inputs": [
		                {
		                    "name": "AzureBlobInput"
		                }
		            ],
		            "outputs": [
		                {
		                    "name": "AzureBlobOutput"
		                }
		            ],
		            "linkedServiceName": "HDInsightLinkedService",
		            "type": "HDInsightHive",
		            "typeProperties": {
		                "scriptPath": "adftutorial\\hivequery.hql",
		                "scriptLinkedService": "StorageLinkedService",
		                "defines": {
		                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
		                    "Month": "$$Text.Format('{0:%M}',WindowStart)",
		                    "Day": "$$Text.Format('{0:%d}',WindowStart)"
		                }
		            },
		            "scheduler": {
		                "frequency": "Day",
		                "interval": 1
		            },			
		            "policy": {
		                "concurrency": 1,
		                "executionPriorityOrder": "OldestFirst",
		                "retry": 2,
		                "timeout": "01:00:00"
		            }
	             }
		     ]
		   }
		}

El diagrama siguiente muestra el escenario desde el punto de vista de la dependencia de datos.

![Dependencia de los datos](./media/data-factory-scheduling-and-execution/data-dependency.png)

El segmento de salida para cada día depende de 24 segmentos por hora desde el conjunto de datos de entrada. Factoría de datos calcula automáticamente estas dependencias al determinar los segmentos de datos de entrada que se encuentran en el mismo período de tiempo que el segmento de salida que se va a producir. Si cualquiera de los 24 segmentos de entrada no está disponible, Data Factory espera a que el segmento de entrada esté listo antes de empezar la ejecución de la actividad diaria.


### Ejemplo 2: Especificar la dependencia con expresiones y funciones de Factoría de datos

Consideremos otro escenario. Supongamos que tiene una actividad de Hive que procesa dos conjuntos de datos de entrada, uno de ellos tiene nuevos datos diariamente pero el otro obtiene datos nuevos cada semana. Supongamos que desea combinar las dos entradas y producir una salida diariamente.
 
El enfoque sencillo, en el que Data Factory determina automáticamente los segmentos de entrada correctos que se van a procesar al alinearlos con el período de los segmentos de datos de salida, no funciona.

Necesita una manera de especificar para cada ejecución de actividad la Factoría de datos que debe usar el segmento de datos de la semana pasada para el conjunto de datos de entrada semanal. Puede hacerlo con ayuda de las funciones de Data Factory de Azure, como se muestra en el siguiente fragmento.

**Input1: blob de Azure**

La primera entrada es el blob de Azure actualizado **a diario**.
	
	{
	  "name": "AzureBlobInputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Input2: blob de Azure**

La segunda entrada es el blob de Azure actualizado **semanalmente**.

	{
	  "name": "AzureBlobInputWeekly",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 7
	    }
	  }
	}

**Salida: blob de Azure**

Cada día se crea un archivo de salida en la carpeta del día. La disponibilidad de la salida se establece en diariamente (frecuencia: día, intervalo: 1).
	
	{
	  "name": "AzureBlobOutputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Actividad: actividad de Hive en una canalización**

La actividad de Hive toma las dos entradas y genera un segmento de salida cada día. Puede especificar de la forma siguiente el segmento de salida de cada día para que dependa de segmento de entrada de la semana pasada para la entrada semanal.
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"hive activity",
	    "activities": [
	      {
	        "name": "SampleHiveActivity",
	        "inputs": [
	          {
	            "name": "AzureBlobInputDaily"
	          },
	          {
	            "name": "AzureBlobInputWeekly",
	            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
	            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutputDaily"
	          }
	        ],
	        "linkedServiceName": "HDInsightLinkedService",
	        "type": "HDInsightHive",
	        "typeProperties": {
	          "scriptPath": "adftutorial\\hivequery.hql",
	          "scriptLinkedService": "StorageLinkedService",
	          "defines": {
	            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
	            "Month": "$$Text.Format('{0:%M}',WindowStart)",
	            "Day": "$$Text.Format('{0:%d}',WindowStart)"
	          }
	        },
	        "scheduler": {
	          "frequency": "Day",
	          "interval": 1
	        },			
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 2,  
	          "timeout": "01:00:00"
	        }
		   } 
	     ]
	   }
	}


## Funciones y variables del sistema de Data Factory   

Para conocer la lista de funciones y variables del sistema que admite Data Factory de Azure, consulte el artículo [Data Factory de Azure: funciones y variables del sistema](data-factory-functions-variables.md).

## Profundización de la dependencia de datos

Con el fin de generar un segmento del conjunto de datos mediante la ejecución de una actividad, Data Factory usa el siguiente **modelo de dependencia** para determinar las relaciones entre los conjuntos de datos usados y los generados por una actividad.

El intervalo de tiempo de los conjuntos de datos de entrada necesario para generar el segmento del conjunto de datos de salida se denomina **período de dependencia**.

La ejecución de una actividad genera un segmento del conjunto de datos solo después de que estén disponibles los segmentos de datos de los conjuntos de datos de entrada dentro del período de dependencia. Significa que todos los segmentos de entrada que comprenden el período de dependencia deben tener el estado **Listo** en el segmento de los conjuntos de datos de salida para ser producidos por una ejecución de actividad.

Para generar el segmento de conjunto de datos [inicio, fin], se necesita una función para asignar el segmento de conjunto de datos a su período de dependencia. Esta función es básicamente una fórmula que convierte el principio y el final del segmento del conjunto de datos en el comienzo y final del período de dependencia. Dicho más formalmente,
	
	DatasetSlice = [start, end]
	DependecyPeriod = [f(start, end), g(start, end)]

donde f y g están asignando funciones que calculan el comienzo y el final del período de dependencia para cada entrada de la actividad.

Tal como se ve en los ejemplos, el período de dependencia es el mismo que el período en el que se va a producir el segmento de datos. En estos casos, la factoría de datos calcula automáticamente los segmentos de entrada que se encuentran en el período de dependencia.

Por ejemplo: en el ejemplo de agregación anterior, en el que la salida se produce diariamente y los datos de entrada están disponibles cada hora, el período del segmento de datos es de 24 horas. Factoría de datos busca los segmentos de entrada relevantes de cada hora para este período de tiempo y hace que el segmento de salida sea dependiente del segmento de entrada.

También puede proporcionar su propia asignación para el período de dependencia, como se muestra en el ejemplo, donde una de las entradas es semanal y el segmento de salida se produce diariamente.
   
## Dependencia y validación de datos

Un conjunto de datos puede tener una directiva de validación definida que especifique cómo se pueden validar los datos generados por la ejecución de un segmento antes de que esté listo para su uso. Consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md) para obtener más información.

En estos casos, cuando el segmento ha terminado de ejecutarse, el estado del mismo cambia a **En espera** con un subestado de **Validación**. Una vez validados los segmentos, el estado del segmento cambia a **Listo**.
   
Si se ha generado un segmento de datos, pero no ha pasado la validación, no se procesan las ejecuciones de actividad de los segmentos de nivel inferior, en función del segmento que no se pudo validar.

En el artículo [Supervisión y administración de canalizaciones](data-factory-monitor-manage-pipelines.md) se tratan los diversos estados de los segmentos de datos en Factoría de datos.

## Datos externos

Un conjunto de datos se puede marcar como externo (como se muestra en el fragmento de JSON), lo que implica que no se generó con Data Factory de Azure. En tal caso, la directiva de conjunto de datos puede tener un conjunto de parámetros adicional que describe la validación adicional y la directiva de reintento para el conjunto de datos. Consulte el artículo sobre la [creación de canalizaciones](data-factory-create-pipelines.md) para una descripción de todas las propiedades.

De forma similar a los conjuntos de datos que produce Factoría de datos, los segmentos de datos de datos externos deben estar preparados antes de que se puedan procesar los segmentos dependientes.

	{
		"name": "AzureSqlInput",
		"properties": 
		{
			"type": "AzureSqlTable",
			"linkedServiceName": "AzureSqlLinkedService",
			"typeProperties": 
			{
				"tableName": "MyTable"	
			},
			"availability": 
			{
				"frequency": "Hour",
				"interval": 1     
			},
			"external": true,
			"policy": 
			{
				"externalData": 
				{
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}  
		} 
	} 


## Canalización de una vez
Puede crear y programar una canalización que se ejecute periódicamente (por ejemplo, cada hora y diariamente) entre las horas de inicio y finalización que especifique en la definición de la canalización. Para más información, consulte [Programación de actividades](#scheduling-and-execution). También puede crear una canalización que se ejecute una sola vez. Para ello, establezca la propiedad **pipelineMode** en **onetime** en la definición de la canalización,como se muestra en el siguiente ejemplo de JSON. El valor predeterminado de esta propiedad es **scheduled**.

	{
	    "name": "CopyPipeline",
	    "properties": {
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource",
	                        "recursive": false
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "InputDataset"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "OutputDataset"
	                    }
	                ]
	                "name": "CopyActivity-0"
	            }
	        ]
	        "pipelineMode": "OneTime"
	    }
	}

Tenga en cuenta lo siguiente:
 
- No se especifican las horas de **inicio** y **finalización** de la canalización.
- La disponibilidad (**availability**) de los conjuntos de datos de entrada y salida se especifica (frecuencia e intervalo), incluso aunque Data Factory no use los valores.
- La vista Diagrama no muestra las canalizaciones de una vez. Este comportamiento es así por diseño.
- Las canalizaciones de una vez no se pueden actualizar. Puede clonar una canalización de una vez, cambiarle el nombre, actualizar las propiedades e implementarla para crear otra.

  




  









 
 












      

  

<!---HONumber=AcomDC_0824_2016-->