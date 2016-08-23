<properties
	pageTitle="Guía de optimización y rendimiento de la actividad de copia | Microsoft Azure"
	description="Conozca los factores más importantes que afectan al rendimiento del movimiento de datos en Factoría de datos de Azure mediante la actividad de copia."
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
	ms.date="08/09/2016"
	ms.author="spelluru"/>


# Guía de optimización y rendimiento de la actividad de copia
En este artículo se describen los factores más importantes que afectan al rendimiento del movimiento de datos (actividad de copia) en Factoría de datos de Azure. También muestra el rendimiento observado durante las pruebas internas y trata diversas maneras de optimizar el rendimiento de la actividad de copia.

Mediante la actividad de copia, puede obtener un elevado rendimiento de movimiento de datos tal como se muestra en los ejemplos siguientes:

- Introducir 1 TB de datos en Almacenamiento de blobs de Azure en el sistema de archivos local y Almacenamiento de blobs de Azure en menos de 3 horas (es decir, a 100 MBps)
- Introducir 1 TB de datos en el Almacén de Azure Data Lake en el sistema de archivos local y Almacenamiento de blobs de Azure en menos de 3 horas (es decir, a 100 MBps)
- Introducir 1 TB de datos en Almacenamiento de datos SQL de Azure desde Almacenamiento de blobs de Azure en menos de 3 horas (es decir, a 100 MBps)

Consulte las secciones siguientes para obtener más información sobre el rendimiento de la actividad de copia, así como sugerencias de optimización para mejorar aún más.

> [AZURE.NOTE] Si no está familiarizado con la actividad de copia en general, consulte [Actividades de movimiento de datos](data-factory-data-movement-activities.md) antes de leer este artículo.

## Pasos de optimización del rendimiento
A continuación se indican los pasos típicos sugeridos para optimizar el rendimiento de su solución de Factoría de datos de Azure con actividad de copia.

1.	**Establecimiento de una línea base.** Durante la fase de desarrollo, pruebe la canalización con la actividad de copia con los datos de una muestra representativa. Puede aprovechar el [modelo de segmentación](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices) de Factoría de datos de Azure para limitar la cantidad de datos con los que trabaja.

	Recopile el tiempo de ejecución y las características de rendimiento mediante la **aplicación de supervisión y administración**: haga clic en el icono **Supervisión y administración** en la página principal de su instancia de Data Factory, seleccione el **conjunto de datos de salida** en la vista de árbol y, después, seleccione la actividad de copia que se ejecuta en la lista de **ventanas de actividad**. Debería ver la duración de la actividad de copia en la lista de **ventanas de actividad** y el tamaño de los datos que se copian y el rendimiento en la ventana **Activity Window Explorer** (Explorador de ventanas de actividad) a la derecha. Consulte [Supervisión y administración de canalizaciones de Data Factory de Azure mediante la nueva Aplicación de supervisión y administración](data-factory-monitor-manage-app.md) para más información sobre la aplicación.
	
	![Detalles de ejecución de actividad](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

	Puede comparar el rendimiento y las configuraciones de su escenario con la [referencia de rendimiento](#performance-reference) de la actividad de copia publicada siguiente, que se basa en observaciones internas.
2. **Diagnóstico y optimización de rendimiento** Si el rendimiento que observe es inferior a sus expectativas, debe identificar cuellos de botella de rendimiento y realizar optimizaciones para eliminar o reducir el impacto de los cuellos de botella. La descripción completa de las pruebas de diagnóstico del rendimiento queda fuera del ámbito de este artículo, pero a continuación enumeramos algunas consideraciones habituales.
	- [Origen](#considerations-on-source)
	- [Sink](#considerations-on-sink)
	- [Serialización y deserialización](#considerations-on-serializationdeserialization)
	- [Compresión](#considerations-on-compression)
	- [Asignación de columnas](#considerations-on-column-mapping)
	- [Data Management Gateway](#considerations-on-data-management-gateway)
	- [Otras consideraciones](#other-considerations)
	- [Copia paralela](#parallel-copy)
	- [Unidades de movimiento de datos de nube](#cloud-data-movement-units)

3. **Expanda la configuración a todos los datos** Una vez que esté satisfecho con los resultados y el rendimiento de la ejecución, puede expandir la definición del conjunto de datos y el período activo de la canalización para incluir todos los datos en la imagen.

## Referencia de rendimiento
> [AZURE.IMPORTANT] **Aviso de declinación de responsabilidades**: los datos siguientes se publicaron únicamente con fines de orientación y planeación general. Se da por hecho que el ancho de banda, el hardware, la configuración, etc., son de los mejores de su clase. Use esto solo como referencia. La capacidad de proceso del movimiento de datos que observe se verá afectada por diversas variables. Consulte las secciones siguientes para obtener información acerca de cómo optimizar y lograr un mejor rendimiento para sus necesidades de movimiento de datos. Estos datos se actualizará a medida que se agreguen características y mejoras del rendimiento.

![Matriz de rendimiento](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

Puntos a tener en cuenta:

- La capacidad de proceso se calcula con la siguiente fórmula: [tamaño de los datos leídos del origen]/[duración de la ejecución de la actividad de copia]
- Se usó el conjunto de datos [TPC-H](http://www.tpc.org/tpch/) para calcular los números anteriores.
- En el caso de los almacenes de datos de Microsoft Azure, el origen y el receptor se encuentran en la misma región de Azure.
- **cloudDataMovementUnits** se establece en 1 y **parallelCopies** no se especifica.
- En el caso del movimiento de datos híbrido (local a nube o nube a local), Data Management Gateway (instancia única) se hospedó en una máquina diferente que el almacén de datos local, con la siguiente configuración. Tenga en cuenta que cuando se ejecuta una sola actividad en la puerta de enlace, la operación de copia solo consume una pequeña parte del ancho de banda de red y de los recursos de CPU y de memoria de esta máquina.
	<table>
	<tr>
		<td>CPU</td>
		<td>Intel Xeon ® E5-2660 v2 de 32 núcleos, 2,20 GHz </td>
	</tr>
	<tr>
		<td>Memoria</td>
		<td>128 GB</td>
	</tr>
	<tr>
		<td>Red</td>
		<td>Interfaz de Internet: 10 Gbps; interfaz de intranet: 40 Gbps</td>
	</tr>
	</table>

## Copia paralela
Una de las maneras de mejorar el rendimiento de una operación de copia y de reducir el tiempo de la transferencia de datos es leer los datos en el origen o escribir los datos en el destino **en paralelo dentro de una ejecución de la actividad de copia**.
 
Tenga en cuenta que esta configuración es diferente de la propiedad de **simultaneidad** en la definición de actividad. La propiedad de simultaneidad determina el número de **ejecuciones simultáneas de la actividad de copia** que se ejecutan a la vez en tiempo de ejecución para procesar los datos de los diferentes periodos de actividad (01:00-02:00, 02:00-03:00, 03:00-04:00, etc.). Esto es realmente útil al realizar una carga histórica. Sin embargo, la funcionalidad de copia en paralelo que se describe aquí se aplica a una **única ejecución de la actividad**.

Echemos un vistazo a un **escenario de ejemplo**: considere el ejemplo siguiente donde hay varios segmentos del pasado que deben procesarse. El servicio Data Factory ejecuta una instancia de la actividad de copia (ejecución de actividad) para cada segmento.

- segmento de datos del primer periodo de actividad (01:00-02:00) == > ejecución de actividad 1
- segmento de datos de la segunda ventana de actividad (2 a.m. a 3 a.m.) == > ejecución de actividad 2
- segmento de datos de la segunda ventana de actividad (3 a.m. a 4 a.m.) == > ejecución de actividad 3
- etc.

Establecer la configuración de **simultaneidad** en **2** en este ejemplo permite que la **ejecución de actividad 1** y **ejecución de actividad 2** copien datos desde dos ventanas de actividad **simultáneamente** para mejorar el rendimiento del movimiento de datos. Sin embargo, si hay varios archivos asociados con la actividad de ejecución 1, solo se copia un archivo desde el origen al destino a la vez.

### parallelCopies
Puede usar la propiedad **parallelCopies** para indicar el paralelismo que quiere que use la actividad de copia. Dicho sencillamente, considere esta propiedad como el número máximo de subprocesos dentro de una actividad de copia que leen desde el origen o que escriben en los almacenes de datos del receptor en paralelo.

Para cada ejecución de la actividad de copia, Data Factory de Azure determina de manera inteligente el número de copias en paralelo que se van a usar para copiar datos desde el almacén de datos de origen al almacén de datos de destino. El número predeterminado de copias en paralelo que utilice depende de los tipos de origen y receptor:

Origen y receptor |	Recuento predeterminado de copias en paralelo determinado por servicio
------------- | -------------------------------------------------
Copia de datos entre **almacenes basados en archivos** (Blob de Azure, Azure Data Lake, sistema de archivos local, HDFS local) | En cualquier lugar entre **1 a 32** basado en el **tamaño de los archivos** y el **número de unidades de movimiento de datos de nube** (consulte una definición en la siguiente sección) utilizado para copiar datos entre dos almacenes de datos de nube (o) la configuración física de la máquina de puerta de enlace utilizada para la copia híbrida (copiar datos desde o hacia un almacén de datos local)
Copiar de datos desde **cualquier almacén de datos de origen a Tabla de Azure** | 4
Todos los demás pares de origen y receptor | 1

En la mayoría de los casos, el comportamiento predeterminado debe proporcionar el máximo rendimiento. Sin embargo, puede reemplazar el valor predeterminado si especifica un valor para la propiedad **parallelCopies** con el fin de controlar la carga en las máquinas con los almacenes de datos o para ajustar el rendimiento de la copia. Debe encontrarse entre **1 y 32 (ambos inclusive)**. En tiempo de ejecución, la actividad de copia elegirá un valor que sea menor o igual al valor configurado para proporcionar el mejor rendimiento.

	"activities":[  
	    {
	        "name": "Sample copy activity",
	        "description": "",
	        "type": "Copy",
	        "inputs": [{ "name": "InputDataset" }],
	        "outputs": [{ "name": "OutputDataset" }],
	        "typeProperties": {
	            "source": {
	                "type": "BlobSource",
	            },
	            "sink": {
	                "type": "AzureDataLakeStoreSink"
	            },
	            "parallelCopies": 8
	        }
	    }
	]

Tenga en cuenta lo siguiente:

- Para copiar datos entre almacenes basados en archivos, se produce el paralelismo en el nivel de archivo; en otras palabras, no hay fragmentación dentro de un único archivo. El número real de copias paralelas utilizado para la operación de copia en tiempo de ejecución no será mayor que el número de archivos disponibles. Si el comportamiento de copia es la propiedad mergeFile, no se podrá aprovechar el paralelismo.
- Al especificar un valor para la propiedad parallelCopies, tenga en cuenta el aumento de carga que aportará a los almacenes de datos de origen y de receptor, así como a la puerta de enlace si es una copia híbrida, especialmente cuando hay varias actividades o ejecuciones simultáneas de las mismas actividades que se ejecutan en el mismo almacén de datos. Si observa que el almacén de datos o la puerta de enlace están sobrecargados con la carga, disminuya el valor de la propiedad parallelCopies para aliviar la carga.
- Cuando se copian datos desde almacenes no basados en archivos en otros sí basados en archivos, se omitirá la propiedad parallelCopies aunque se especifique, y no se utilizaría ningún paralelismo.

> [AZURE.NOTE] Para sacar partido a la característica parallelCopies al realizar una copia híbrida, debe utilizar Data Management Gateway, versión superior o igual a 1.11.

### Unidades de movimiento de datos de nube
La **unidad de movimiento de datos de nube** es una medida que representa la potencia (combinación de asignación de recursos de CPU, memoria y de red) de una sola unidad en el servicio Data Factory de Azure que se utiliza para realizar una operación de copia de nube a nube. No entra en juego cuando se realiza una copia híbrida. De forma predeterminada, el Data Factory de Azure utiliza una unidad de movimiento de datos de nube única para realizar una ejecución de la actividad de copia única. Puede invalidar esta configuración predeterminada especificando un valor para la propiedad **cloudDataMovementUnits**. En este momento, la configuración de cloudDataMovementUnits **solo se admite** al copiar datos **entre dos instancias de Almacenamiento de blobs de Azure** o desde un **Almacenamiento de blobs de Azure a un almacén de Azure Data Lake**, y surte efecto cuando hay varios archivos que se van a copiar y que tienen un tamaño mayor o igual que 16 MB individualmente.

Si copia un número de archivos relativamente grandes, es posible que al establecer un valor alto para la propiedad **parallelCopies** no se mejore el rendimiento debido a las limitaciones de recursos de una única unidad de movimiento de datos de nube. En estos casos, puede utilizar más unidades de movimiento de datos de nube para copiar la enorme cantidad de datos con un alto rendimiento. Para especificar el número de unidades de movimiento de datos de nube que desea que utilice la actividad de copia, establezca un valor para la propiedad **cloudDataMovementUnits** como se muestra a continuación:

	"activities":[  
	    {
	        "name": "Sample copy activity",
	        "description": "",
	        "type": "Copy",
	        "inputs": [{ "name": "InputDataset" }],
	        "outputs": [{ "name": "OutputDataset" }],
	        "typeProperties": {
	            "source": {
	                "type": "BlobSource",
	            },
	            "sink": {
	                "type": "AzureDataLakeStoreSink"
	            },
	            "cloudDataMovementUnits": 4
	        }
	    }
	]

Los **valores permitidos** para la propiedad cloudDataMovementUnits son: 1 (valor predeterminado), 2, 4 y 8. Si necesita más unidades de movimiento de datos de nube para un mayor rendimiento, póngase en contacto con el [soporte técnico de Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). El **número real de unidades de movimiento de datos de nube** utilizado para la operación de copia en tiempo de ejecución será igual o menor que el valor configurado, dependiendo del número de archivos que se copian del origen y que cumplen los criterios de tamaño.

> [AZURE.NOTE] La propiedad parallelCopies debe ser mayor o igual que la propiedad cloudDataMovementUnits si especifica; y cuando la propiedad cloudDataMovementUnits es mayor que 1, el movimiento de datos en paralelo se extiende por la propiedad cloudDataMovementUnits para esa ejecución de la actividad de copia, lo que incrementa el rendimiento.

Al copiar varios archivos grandes con la propiedad **cloudDataMovementUnits** configurada como 2, 4 y 8, el rendimiento puede alcanzar 2x (2 veces), 4x y 7x de las cifras de referencia mencionadas en la sección Referencia de rendimiento.

Consulte aquí los [ejemplos de casos de uso](#case-study---parallel-copy) para poder aprovechar mejor las dos propiedades anteriores y mejorar el rendimiento del movimiento de datos.
 
Es **importante** recordar que se le cobrará según el tiempo total de la operación de copia. Por tanto, si un trabajo de copia solía tardar 1 hora con 1 unidad en la nube y ahora tarda 15 minutos con 4 unidades en la nube, la factura total será prácticamente la misma. A continuación se expone otro escenario: imaginemos que está usando 4 unidades en la nube y la primera de ellas emplea 10 minutos, la segunda 10 minutos, la tercera 5 minutos y la cuarta 5 minutos en una ejecución de actividad de copia. En este caso se le cobrará por el tiempo de copia total (movimiento de datos), que es 10 + 10 + 5 + 5 = 30 minutos. El uso de **parallelCopies** no tiene ningún impacto en la facturación.

## Copias almacenadas provisionalmente
Al copiar datos de un almacén de datos de origen a uno de tipo receptor, se puede utilizar un almacenamiento de blobs de Azure como almacén provisional. Esta funcionalidad de almacenamiento provisional resulta especialmente útil en los siguientes casos:

1.	**A veces, el movimiento híbrido de datos (es decir, de un almacén de datos local a uno en la nube, o viceversa) dura bastante tiempo cuando se emplea una conexión de red lenta.** Para mejorar el rendimiento de este proceso, puede comprimir los datos de forma local para que se tarde menos en moverlos, a través de Internet, al almacén de datos provisional en la nube. Después, puede descomprimirlos en un almacén provisional antes de cargarlos en el almacén de datos de destino.
2.	**No quiere abrir los puertos 80 y 443 en el firewall debido a las directivas de TI.** Por ejemplo, al copiar datos de un almacén de datos local a un receptor de Base de datos SQL de Azure o de Almacenamiento de datos SQL de Azure, hay que habilitar la comunicación TCP saliente del puerto 1433 en el firewall corporativo y en el de Windows. En este caso, puede utilizar Data Management Gateway para, primero, copiar los datos en un almacenamiento de blobs de Azure provisional, lo que se realiza a través de HTTP o HTTPS —es decir, en el puerto 443— y, después, cargarlos en Base de datos SQL o Almacenamiento de datos SQL desde el almacenamiento de blobs provisional. En este tipo de flujo, no hay que habilitar el puerto 1433.
3.	**Introduce datos de varios almacenes de datos en Almacenamiento de datos SQL de Azure a través de PolyBase.** Almacenamiento de datos SQL de Azure ofrece PolyBase como mecanismo de alto rendimiento para cargar grandes cantidades de datos en este servicio de almacenamiento. Sin embargo, se precisa que los datos de origen estén en Almacenamiento de blobs de Azure, así como que se cumplan algunos criterios adicionales. Al cargar datos de un almacén de datos distinto al almacenamiento de blobs de Azure, puede habilitar la copia de datos a través de un almacenamiento de blobs de Azure provisional, en cuyo caso Data Factory de Azure realizará las transformaciones necesarias en los datos para asegurarse de que cumplen los requisitos de PolyBase y, luego, utilizará PolyBase para cargarlos en Almacenamiento de datos SQL. Consulte [Uso de PolyBase para cargar datos en el Almacenamiento de datos SQL](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obtener más información.

### Cómo funcionan las copias almacenadas provisionalmente
Al habilitar la característica de almacenamiento provisional, los datos se copian primero del almacén de datos de origen al provisional (el suyo propio) y, luego, se copian del provisional al receptor. Data Factory de Azure administrará automáticamente el flujo de 2 fases y también limpiará los datos temporales del almacenamiento provisional cuando se termine de realizar el movimiento de datos.

En el **escenario de copia en la nube**, donde los almacenes de datos de origen y receptor se encuentran en la nube y no utilizan Data Management Gateway, el **servicio Data Factory de Azure** se encarga de llevar a cabo las operaciones de copia.

![Copias almacenadas provisionalmente: escenario de modelo en la nube](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

En el **escenario de copia híbrida**, donde el almacén de origen se encuentra en un entorno local y el receptor está en la nube, el proceso de mover datos del almacén de datos de origen al provisional se realiza mediante **Data Management Gateway**, mientras que el movimiento de datos del almacén de datos provisional al receptor lo lleva a cabo el **servicio Data Factory de Azure**. Con el flujo invertido también se permite copiar datos de un almacén de datos en la nube a uno local a través de un almacenamiento provisional.

![Copias almacenadas provisionalmente: escenario de modelo híbrido](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Cuando se habilita el movimiento de datos mediante el almacenamiento provisional, puede especificar si desea que los datos se compriman antes de moverlos del almacén de datos de origen al provisional o al de almacenamiento provisional, y que se descompriman antes de moverlos del provisional o del de almacenamiento provisional al receptor.

En este momento, no se pueden copiar datos entre dos almacenes locales, aunque está previsto que pueda hacerse en breve.

### Configuración
Puede configurar el valor **enableStaging** de la actividad de copia para especificar si desea que los datos se almacenen provisionalmente en un almacenamiento de blobs de Azure antes de cargarlos en un almacén de datos de destino. Cuando el valor de enableStaging se establece en True, debe determinar otras propiedades que aparecen en la tabla siguiente. Además, tiene que crear un servicio vinculado de Almacenamiento de Azure o de SAS de Almacenamiento de Azure como almacenamiento provisional si aún no tiene ninguno.

Propiedad | Description | Valor predeterminado | Obligatorio
--------- | ----------- | ------------ | --------
enableStaging | Especifique si desea copiar los datos a través de un almacén provisional. | False | No
linkedServiceName | Especifique el nombre de un servicio vinculado [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) o [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service), que hace referencia al almacenamiento de Azure que se usará como almacén provisional. <br/><br/> Tenga en cuenta que no se puede usar un almacenamiento de Azure con SAS (firma de acceso compartido) para cargar datos en Almacenamiento de datos SQL de Azure a través de PolyBase. Sí que se puede utilizar en el resto de los casos. | N/D | Sí, cuando el valor de enableStaging es True. 
path | Especifique la ruta de acceso del almacenamiento de blobs de Azure que contendrá los datos almacenados provisionalmente. Si no se proporciona una ruta de acceso, el servicio creará un contenedor para almacenar los datos temporales. <br/><br/> No tiene que especificar la ruta de acceso, salvo que esté utilizando el almacenamiento de Azure con SAS o que tenga un requisito exigente respecto a la ubicación en la que deben residir los datos temporales. | N/D | No
enableCompression | Especifique si se deben comprimir los datos cuando se muevan del almacén de datos de origen al receptor con el fin de reducir el volumen de datos transferidos a través de Internet. | False | No

Aquí puede encontrar un ejemplo de definición de una actividad de copia con las propiedades anteriores:

	"activities":[  
	{
		"name": "Sample copy activity",
		"type": "Copy",
		"inputs": [{ "name": "OnpremisesSQLServerInput" }],
		"outputs": [{ "name": "AzureSQLDBOutput" }],
		"typeProperties": {
			"source": {
				"type": "SqlSource",
			},
			"sink": {
				"type": "SqlSink"
			},
	    	"enableStaging": true,
			"stagingSettings": {
				"linkedServiceName": "MyStagingBlob",
				"path": "stagingcontainer/path",
				"enableCompression": true
			}
		}
	}
	]


### Impacto en la facturación
Tenga en cuenta que se le cobrará según las dos fases de duración de la copia y su tipo de copia respectivamente, es decir:

- Al utilizar un almacenamiento provisional durante una copia de nube (copiando datos de un almacén de datos en la nube a otro de este tipo, por ejemplo, de Azure Data Lake a Almacenamiento de datos SQL de Azure), se le cobrará de la siguiente forma: [suma de la duración de la copia de los pasos 1 y 2] x [precio unitario de la copia de nube].
- Al utilizar un almacenamiento provisional durante una copia híbrida (copiando datos de un almacén de datos local a uno en la nube, por ejemplo, de la base de datos de SQL Server local a Almacenamiento de datos SQL de Azure), se le cobrará de la siguiente forma: [duración de la copia híbrida] x [precio unitario de la copia híbrida] + [duración de la copia de nube] x [precio unitario de la copia de nube].


## Consideraciones sobre el origen
### General
Asegúrese de que el almacén de datos subyacente no está desbordado por otras cargas de trabajo que se estén ejecutando en él o que lo estén usando para su ejecución, incluida la actividad de copia, entre otras.

Para los almacenes de datos de Microsoft, consulte los [temas sobre supervisión y optimización](#appendix-data-store-performance-tuning-reference) específicos del almacén de datos, que pueden ayudarle a comprender las características de rendimiento del almacén de datos, a minimizar los tiempos de respuesta y a maximizar la capacidad de proceso.

Si va a copiar datos de **Almacenamiento de blobs de Azure** a **Almacenamiento de datos SQL de Azure**, considere la posibilidad de habilitar **PolyBase** para mejorar el rendimiento. Consulte [Uso de PolyBase para cargar datos en el Almacenamiento de datos SQL](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse) para obtener más información.


### Almacenes de datos basados en archivos
*(Incluye Blob de Azure, Azure Data Lake, sistema de archivos local)*

- **Tamaño de archivo medio y número de archivos**: la actividad de copia transfiere datos archivo por archivo. Con la misma cantidad de datos para mover, la capacidad de proceso general será mayor si los datos se componen de un gran número de archivos pequeños en lugar de un número reducido de archivos de mayor tamaño debido a la fase de arranque de cada archivo. Por lo tanto, si es posible, combine archivos pequeños en archivos de mayor tamaño para obtener una capacidad de proceso mayor.
- **Formato de archivo y compresión**: consulte las secciones [Consideraciones sobre la serialización y deserialización](#considerations-on-serializationdeserialization) y [Consideraciones sobre la compresión](#considerations-on-compression) para conocer más formas de mejorar el rendimiento.
- Además, para el escenario de **sistema de archivos local**, en el que el uso de **Data Management Gateway** es necesario, consulte la sección [Consideraciones sobre la puerta de enlace](#considerations-on-data-management-gateway).

### Almacenes de datos relacionales
*(Incluye Base de datos SQL de Azure, Almacenamiento de datos SQL de Azure, Base de datos de SQL Server, Base de datos de Oracle, Base de datos MySQL, Base de datos DB2, Base de datos de Teradata, Base de datos de Sybase, Base de datos de PostgreSQL)*

- **Patrón de datos**: el esquema de la tabla afecta a la capacidad de proceso de la copia. Para copiar la misma cantidad de datos, un tamaño de fila grande proporcionará un mejor rendimiento que un tamaño de fila pequeño, ya que la base de datos recupera de forma más eficaz menos lotes de datos que contienen un menor número de filas.
- **Consulta o procedimiento almacenado**: optimice la lógica de la consulta o del procedimiento almacenado que se especifica en el origen de la actividad de copia para que capture los datos de forma más eficiente.
- Además, para el escenario de **bases de datos relacionales locales**, como SQL Server y Oracle, en el que el uso de **Data Management Gateway** es necesario, consulte la sección [Consideraciones sobre la puerta de enlace](#considerations-on-data-management-gateway).

## Consideraciones sobre el receptor

### General
Asegúrese de que el almacén de datos subyacente no está desbordado por otras cargas de trabajo que se estén ejecutando en él o que lo estén usando para su ejecución, incluida la actividad de copia, entre otras.

Para los almacenes de datos de Microsoft, consulte los [temas sobre supervisión y optimización](#appendix-data-store-performance-tuning-reference) específicos del almacén de datos, que pueden ayudarle a comprender las características de rendimiento del almacén de datos, a minimizar los tiempos de respuesta y a maximizar la capacidad de proceso.

Si va a copiar datos de **Almacenamiento de blobs de Azure** a **Almacenamiento de datos SQL de Azure**, considere la posibilidad de habilitar **PolyBase** para mejorar el rendimiento. Consulte [Uso de PolyBase para cargar datos en el Almacenamiento de datos SQL](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse) para obtener más información.


### Almacenes de datos basados en archivos
*(Incluye Blob de Azure, Azure Data Lake, sistema de archivos local)*

- **Comportamiento de copia**: si va a copiar datos desde otro almacén de datos basado en archivos, la actividad de copia proporciona tres tipos de comportamiento mediante la propiedad "copyBehavior": conservar la jerarquía, aplanar la jerarquía y combinar archivos. Conservar o aplanar la jerarquía supone poca o ninguna sobrecarga para el rendimiento, mientras que combinar archivos provoca una sobrecarga adicional en el rendimiento.
- **Formato de archivo y compresión**: consulte las secciones [Consideraciones sobre la serialización y deserialización](#considerations-on-serializationdeserialization) y [Consideraciones sobre la compresión](#considerations-on-compression) para conocer más formas de mejorar el rendimiento.
- Para **Blob de Azure**, actualmente solo se admiten blobs en bloques para obtener una transferencia de datos y una capacidad de proceso óptimas.
- Además, para los escenarios de **sistema de archivos local**, en el que el uso de **Data Management Gateway** es necesario, consulte la sección [Consideraciones sobre la puerta de enlace](#considerations-on-data-management-gateway).

### Almacenes de datos relacionales
*(Incluye Base de datos SQL de Azure, Almacenamiento de datos SQL de Azure, Base de datos de SQL Server)*

- **Comportamiento de copia**: según las propiedades configuradas para "sqlSink", la actividad de copia escribirá los datos en la base de datos de destino de maneras diferentes:
	- De forma predeterminada, el servicio de movimiento de datos usa la API de copia masiva para insertar datos en modo de anexión, que proporciona el mejor rendimiento.
	- Si configura un procedimiento almacenado en el receptor, la base de datos aplicará los datos fila por fila en lugar de realizar una carga masiva, por lo que el rendimiento se reducirá considerablemente. Si el tamaño de los datos es grande, cuando corresponda, considere la posibilidad de usar la propiedad "sqlWriterCleanupScript" (ver abajo) en su lugar.
	- Si configura la propiedad "sqlWriterCleanupScript", para cada actividad de copia que se ejecute, el servicio desencadenará primero el script y luego usará la API de copia masiva para insertar los datos. Por ejemplo, para sobrescribir toda la tabla con los datos más recientes, puede especificar un script para eliminar primero todos los registros antes de realizar la carga masiva de los nuevos datos desde el origen.
- **Patrón de datos y tamaño de lote**:
	- el esquema de la tabla afectará a la capacidad de proceso de la copia. Para copiar la misma cantidad de datos, un tamaño de fila grande proporcionará un mejor rendimiento que un tamaño de fila pequeño porque la base de datos puede confirmar de forma más eficiente menos lotes de datos.
	- La actividad de copia inserta datos en una serie de lotes, en la que el número de filas incluidas en un lote se pueden establecer usando la propiedad "writeBatchSize". Si los datos tienen filas de un tamaño reducido, puede establecer la propiedad writeBatchSize en un valor superior para beneficiarse de una sobrecarga menor de los lotes y aumentar el rendimiento. Si el tamaño de fila de los datos es grande, tenga cuidado al aumentar el valor de writeBatchSize: un valor grande puede producir un error de copia debido a la sobrecarga de la base de datos.
- Además, para el escenario de **bases de datos relacionales locales**, como SQL Server y Oracle, en el que el uso de **Data Management Gateway** es necesario, consulte la sección [Consideraciones sobre la puerta de enlace](#considerations-on-data-management-gateway).


### Almacenes NoSQL
*(Incluyendo Tabla de Azure, Azure DocumentDB)*

- Para **Tabla de Azure**:
	- **Partición**: escribir datos en particiones intercaladas reducirá drásticamente el rendimiento. Puede ordenar los datos de origen por la clave de partición para que los datos se inserten en una partición después de otra de forma eficiente, o bien, puede ajustar la lógica para escribir los datos en una sola partición.
- Para **Azure DocumentDB**:
	- **Tamaño de lote**: la propiedad "writeBatchSize" indica el número de solicitudes paralelas al servicio de DocumentDB para crear documentos. Puede esperar un rendimiento mejor si aumenta el valor de “writeBatchSize” porque se envían más solicitudes en paralelo a DocumentDB. Sin embargo, tenga cuidado con la limitación al escribir en DocumentDB (mensaje de error "La tasa de solicitudes es grande"). La limitación puede ocurrir debido a diversos factores, incluido el tamaño de los documentos, el número de términos en los documentos y la directiva de indexación de la colección de destino. Para lograr una mayor capacidad de proceso de la copia, considere la posibilidad de usar una colección mejor (por ejemplo, S3).

## Consideraciones sobre la serialización y deserialización
La serialización y deserialización pueden ocurrir cuando el conjunto de datos de entrada o el conjunto de datos de salida es un archivo. Actualmente, la actividad de copia admite los formatos de datos Avro y texto (por ejemplo, CSV y TSV).

**Comportamientos de copia:**

- Al copiar archivos entre almacenes de datos basados en archivos:
	- Cuando los conjuntos de datos tanto de entrada como de salida tienen la misma configuración de formato de archivo, o ninguna, el servicio de movimiento de datos ejecutará una copia binaria sin realizar ninguna serialización ni deserialización. Por lo tanto, observará una capacidad de proceso mayor en comparación con el escenario en el que la configuración del formato de archivo del origen y el receptor es diferente.
	- Cuando los conjuntos de datos tanto de entrada como de salida están en formato de texto y solo el tipo de codificación es diferente, el servicio de movimiento de datos solo realizará la conversión de codificación sin realizar ninguna serialización o deserialización, lo que produce una sobrecarga en el rendimiento en comparación con la copia binaria.
	- Cuando los conjuntos de datos de entrada y salida tienen formatos de archivo o configuraciones diferentes (por ejemplo, delimitadores), el servicio de movimiento de datos deserializará los datos de origen en un flujo, los transformará y, después, los serializará en el formato de salida deseable. Esto provocará una sobrecarga mucho mayor en el rendimiento en comparación con los escenarios anteriores.
- Al copiar archivos hacia o desde un almacén de datos no basado en archivos (por ejemplo, un almacén basado en archivos en un almacén relacional), se requerirá el paso de serialización o deserialización y el resultado será una sobrecarga considerable en el rendimiento.

**Formato de archivo:** la elección del formato de archivo puede afectar al rendimiento de la copia. Por ejemplo, Avro es un formato binario compacto que almacena metadatos con datos y es ampliamente admitido en el ecosistema de Hadoop para procesamiento y consulta. Sin embargo, Avro es más costoso para serialización o deserialización, lo que resulta en una menor capacidad de proceso de copia en comparación con el formato de texto. La elección del formato de archivo que se va a usar en el flujo de procesamiento debe realizarse de manera holística, empezando por la forma en que los datos se almacenan en los almacenes de datos de origen o se extraen de los sistemas externos, el mejor formato de almacenamiento, procesamiento analítico y consultas, y en qué formato se deben exportar los datos a los repositorios de datos para las herramientas de informes y visualización. A veces, un formato de archivo que no es óptimo para el rendimiento de lectura y escritura puede terminar siendo adecuado si se tiene en cuenta el proceso analítico general.

## Consideraciones sobre la compresión
Cuando el conjunto de datos de entrada o de salida es un archivo, puede configurar la actividad de copia para realizar la compresión y descompresión a medida que se escriben los datos en el destino. Al habilitar la compresión, se establece un equilibrio entre E/S y CPU: la compresión de los datos costará recursos de proceso adicionales, pero, a cambio, reducirá la E/S de red y el almacenamiento lo que, en función de los datos, podría suponer una mejora en el rendimiento general de la copia.

**Códec:** se admiten los tipos de compresión GZIP, BZIP2 y Deflate. HDInsight de Azure pueden consumir los tres tipos para procesamiento. Cada códec de compresión tiene su característica particular. Por ejemplo, BZIP2 tiene la menor capacidad de proceso de la copia pero obtendrá el mejor rendimiento para las consultas Hive porque se puede dividir para procesamiento; GZIP proporciona la opción más equilibrada y el que se usa con mayor frecuencia. Debe elegir el códec más adecuado para su escenario de principio a fin.

**Nivel:** para cada códec de compresión, puede elegir entre dos opciones: compresión más rápida y compresión más óptima. La operación de compresión más rápida comprime los datos tan pronto como sea posible, incluso si el archivo resultante no se comprime de forma óptima. La opción de compresión óptima dedicará más tiempo a la compresión para obtener una cantidad de datos mínima. Puede probar ambas opciones para ver cuál proporciona un mejor rendimiento general en su caso.

**Una consideración:** para copiar un tamaño grande de datos entre un almacén local y la nube en casos donde el ancho de banda de la red corporativa y Azure son a menudo el factor limitante, y desea que los conjuntos de datos de entrada y de salida estén en un formato sin comprimir, puede usar un **Blob de Azure provisional** con la compresión. Concretamente, puede dividir una actividad de copia en dos actividades de copia: una primera actividad de copia que copia desde el origen al blob provisional o intermedio en formato comprimido, y la segunda actividad de copia que copia los datos comprimidos desde el blob provisional y los descomprime al escribir en el receptor.

## Consideraciones sobre la asignación de columnas
La propiedad "columnMappings" en la actividad de copia puede usarse para asignar todas las columnas de entrada, o un subconjunto de ellas, a las columnas de salida. Después de leer los datos del origen, el servicio de movimiento de datos debe realizar la asignación de columnas en los datos antes de escribirlos en el receptor. Este procesamiento adicional reduce la capacidad de proceso de la copia.

Si el almacén de datos de origen tiene capacidad para realizar consultas, por ejemplo, es un almacén relacional como Azure SQL o SQL Server o un almacén NoSQL como Tabla de Azure o Azure DocumentDB, puede considerar insertar la lógica de filtrado o reordenación de columnas en la propiedad de consulta en lugar de usar la asignación de columnas. Como resultado se realiza la proyección durante la lectura de los datos del almacén de datos de origen y es mucho más eficiente.

## Consideraciones sobre Data Management Gateway
Para ver las recomendaciones de configuración de la puerta de enlace, consulte [Consideraciones para usar Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md#Considerations-for-using-Data-Management-Gateway).

**Entorno de la máquina de puerta de enlace:** recomendamos usar un equipo dedicado para hospedar Data Management Gateway. Use herramientas como Monitor de rendimiento para examinar el uso de CPU, memoria y ancho de banda durante una operación de copia en la máquina de puerta de enlace. Cambie a un equipo con más capacidad si la CPU, la memoria o el ancho de banda de red se convierten en un cuello de botella.

**Ejecuciones de actividades de copia simultáneas:** una única instancia de Data Management Gateway puede atender varias ejecuciones de actividades de copia al mismo tiempo; es decir, una puerta de enlace puede ejecutar varios trabajos de copia al mismo tiempo (el número de trabajos simultáneos se calcula en función de la configuración de hardware de la máquina de puerta de enlace). Los trabajos de copia adicional se ponen en la cola hasta que la puerta de enlace los selecciona o hasta que se agota el tiempo del trabajo, lo que ocurra primero. Para evitar la contención de recursos en la puerta de enlace, puede organizar por fases la programación de sus actividades para reducir la cantidad de trabajos de copia que hay en cola a la vez, o bien considere la posibilidad de dividir la carga entre varias puertas de enlace.


## Otras consideraciones
Si el tamaño de los datos que se van a copiar es bastante grande, puede ajustar la lógica empresarial para particionar los datos aún más con el mecanismo de segmentación de Factoría de datos de Azure y programar la actividad de copia con más frecuencia para reducir el tamaño de los datos que cada actividad de copia ejecuta.

Tenga cuidado con el número de conjuntos de datos y actividades de copia que llegan al mismo almacén de datos en un momento dado. Un gran número de trabajos de copia simultáneos puede limitar un almacén de datos y provocar una disminución del rendimiento, reintentos internos del trabajo de copia y, en algunos casos, errores de ejecución.

## Caso práctico: copia de una instancia de SQL Server local a Blob de Azure
**Escenario**: se crea una canalización para copiar los datos de una instancia de SQL Server local a Blob de Azure en formato CSV. Para acelerar la copia, se especifica que los archivos CSV se deben comprimir en formato BZIP2.

**Análisis y pruebas:** se observa que la capacidad de proceso de la actividad de copia es inferior a 2 MB/s, mucho menor que los valores de referencia de rendimiento.

**Análisis y optimización del rendimiento:** para solucionar el problema de rendimiento, primero veremos cómo se procesan y se mueven los datos:

1.	**Lectura de datos:** la puerta de enlace abre la conexión a SQL Server y envía la consulta. SQL Server responde enviando el flujo de datos a la puerta de enlace a través de la intranet.
2.	La puerta de enlace **serializa** el flujo de datos en un formato CSV y los **comprime** en un flujo BZIP2.
3.	**Escritura de datos:** la puerta de enlace carga el flujo BZIP2 a Blob de Azure a través de Internet.

Como puede ver, los datos se procesan y se mueven en una transmisión de manera secuencial: SQL Server -> LAN -> Puerta de enlace -> WAN -> Blob de Azure; **el rendimiento general está determinado por la capacidad de proceso mínima en la canalización**.

![flujo de datos](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Uno o varios de los siguientes factores podrían ser el cuello de botella del rendimiento:

1.	**Origen:** el propio SQL Server tiene un rendimiento bajo debido a cargas intensas.
2.	**Data Management Gateway:**
	1.	**LAN**: la puerta de enlace está lejos de SQL Server con una conexión de ancho de banda bajo.
	2.	La **carga en la máquina de puerta de enlace** ha alcanzado su límite para llevar a cabo lo siguiente:
		1.	**Serialización:** la serialización del flujo de datos a CSV tiene una capacidad de proceso baja
		2.	**Compresión:** se eligió el códec de compresión lenta (por ejemplo, BZIP2 que es de 2,8 MB/s con Core i7)
	3.	**WAN**: bajo ancho de banda entre la red corporativa y Azure (por ejemplo, T1 = 1544 kbps, T2 = 6312 kbps).
4.	**Receptor:** Blob de Azure tiene una capacidad de proceso baja (aunque es bastante improbable porque su SLA garantiza un mínimo de 60 MB/s).

En este caso, la compresión de datos BZIP2 podría estar ralentizando toda la canalización. Cambiar a un códec de compresión GZIP podría aliviar este cuello de botella.


## Caso práctico: copia en paralelo  

**Escenario I**: copiar 1000 archivos de 1 MB del sistema de archivos local al almacenamiento de blobs de Azure.

**Análisis y optimización de rendimiento**: suponga que tiene instalado Data Management Gateway en una máquina de 4 núcleos, Data Factory utilizará de forma predeterminada 16 copias en paralelo para mover los archivos del sistema de archivos al blob de Azure simultáneamente. Esto debería dar como resultado un buen rendimiento. También puede especificar el número de copias en paralelo explícitamente si lo desea. Al copiar un gran número de archivos pequeños, las copias en paralelo ayudarán espectacularmente al rendimiento gracias a la utilización de los recursos implicados de forma más eficaz.

![Escenario 1.](./media/data-factory-copy-activity-performance/scenario-1.png)

**Escenario II**: copiar 20 blobs de 500 MB cada uno de Almacenamiento de blobs de Azure a Almacén de Azure Data Lake para analizar y optimizar el rendimiento.

**Análisis y optimización de rendimiento**: en este escenario, de forma predeterminada, Data Factory copiará los datos del blob de Azure a Azure Data Lake mediante una copia única (el valor de la propiedad parallelCopies establecido en 1) y el uso de una unidad de movimiento de datos en la nube. Observará que el rendimiento es más próximo al indicado en la sección [Referencia de rendimiento](#performance-reference) de más arriba.

![Escenario 2.](./media/data-factory-copy-activity-performance/scenario-2.png)

Cuando el tamaño de cada archivo es de decenas de MB y el volumen total es grande, aumentar la propiedad parallelCopies no resultará en un mejor rendimiento de copia, dadas las restricciones de recursos de una unidad de movimiento de datos de nube. En su lugar, debe especificar más unidades de movimiento de datos de nube para obtener más recursos para llevar a cabo el movimiento de datos. No especifique un valor para la propiedad parallelCopies, ya que Data Factory se encarga de controlar el paralelismo. En este caso, si se especifica un valor de 4 en la propiedad cloudDataMovementUnits, dará como resultado un rendimiento de aproximadamente 4 veces.

![Escenario 3.](./media/data-factory-copy-activity-performance/scenario-3.png)

## Referencia para la optimización del rendimiento del almacén de datos
Estas son algunas referencias para la supervisión y la optimización del rendimiento para algunos de los almacenes de datos compatibles:

- Almacenamiento de Azure (incluidos Blob de Azure y Tabla de Azure): [Objetivos de escalabilidad de Almacenamiento de Azure](../storage/storage-scalability-targets.md) y [Lista de comprobación de rendimiento y escalabilidad de Almacenamiento de Microsoft Azure](../storage//storage-performance-checklist.md)
- Base de datos SQL de Azure: puede [supervisar el rendimiento](../sql-database/sql-database-service-tiers.md#monitoring-performance) y comprobar el porcentaje de unidades de transacción de base de datos (DTU).
- Almacenamiento de datos SQL de Azure: su capacidad se mide por unidades de almacenamiento de datos (DWU). Consulte [Rendimiento y escala flexibles con Almacenamiento de datos SQL](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
- Azure DocumentDB: [Performance level in DocumentDB](../documentdb/documentdb-performance-levels.md).
- Instancia de SQL Server local: [Supervisión y optimización del rendimiento](https://msdn.microsoft.com/library/ms189081.aspx).
- Servidor de archivos local: [Performance Tuning for File Servers](https://msdn.microsoft.com/library/dn567661.aspx)

<!---HONumber=AcomDC_0810_2016-->