---
title: Procedimientos recomendados para el uso de Azure Data Lake Store | Microsoft Docs
description: "Más información sobre los procedimientos recomendados de ingesta de datos, seguridad de datos y rendimiento relacionados con el uso de Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: sachinsbigdata
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2018
ms.author: sachins
ms.openlocfilehash: d3a0dd70a03f97a9b6bfb243eda7cbd470b0c239
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="overview-of-azure-data-lake-store"></a>Información general del Almacén de Azure Data Lake
En este artículo hallará más información sobre los procedimientos recomendados y las consideraciones que debe tener en cuenta al trabajar con Azure Data Lake Store. En este artículo se proporciona información sobre seguridad, rendimiento, resistencia y supervisión de Data Lake Store. Antes de Data Lake Store, el trabajo con macrodatos en servicios como Azure HDInsight era complicado. Había que particionar los datos en varias cuentas de almacenamiento de blobs para que se pudiera lograr un almacenamiento de petabytes y un rendimiento óptimo a esa escala. Gracias a Data Lake Store, la mayoría de los restrictivos límites de tamaño y rendimiento se han eliminado. No obstante, aún quedan algunas consideraciones que debe tener en cuenta y que se describen en este artículo para que pueda obtener el mejor rendimiento de Data Lake Store. 

## <a name="security-considerations"></a>Consideraciones sobre la seguridad

Azure Data Lake Store ofrece controles de acceso de POSIX y una auditoría detallada de usuarios, grupos y entidades de servicio de Azure Active Directory (Azure AD). Estos controles de acceso se pueden establecer para archivos y carpetas ya existentes. Los controles de acceso también se pueden utilizar para crear valores predeterminados que se pueden aplicar a archivos o carpetas nuevos. Si los permisos se establecen para carpetas y objetos secundarios ya existentes, se deberán propagar de manera recursiva en cada objeto. Si existe un gran número de archivos, propagar los permisos puede llevar mucho tiempo. La velocidad puede oscilar entre 30 y 50 objetos procesados por segundo. Por tanto, planee la estructura de carpetas y los grupos de usuario adecuadamente. En caso contrario, puede provocar retrasos y problemas inesperados al trabajar con los datos. 

Suponga que tiene una carpeta con 100 000 objetos secundarios. Si tomamos el límite inferior de 30 objetos procesados por segundo, actualizar el permiso para toda la carpeta podría llevar una hora. Hay disponible más información sobre las listas de control de acceso de Data Lake Store en [Control de acceso en Azure Data Lake Store](data-lake-store-access-control.md). Para conseguir un rendimiento mejorado a la hora de asignar las listas de control de acceso de forma recursiva, puede usar la herramienta de línea de comandos de Azure Data Lake Store. Esta herramienta crea varios subprocesos y una lógica de navegación recursiva que permite aplicar rápidamente las listas de control de acceso a millones de archivos. La herramienta está disponible para Linux y Windows, y la [documentación](https://github.com/Azure/data-lake-adlstool) y las [descargas](http://aka.ms/adlstool-download) de esta herramienta se encuentran en GitHub.

### <a name="use-security-groups-versus-individual-users"></a>Uso de grupos de seguridad en comparación con el de usuarios individuales 

Cuando se trabaja con macrodatos en Data Lake Store, lo más probable es que se use una entidad de servicio para permitir que servicios como Azure HDInsight puedan trabajar con los datos. Sin embargo, puede haber casos en que los usuarios individuales también necesiten acceder a los datos. En estos casos, debe usar grupos de seguridad de Azure Active Directory en lugar de asignar usuarios individuales a carpetas y archivos. Una vez que se asignan permisos a un grupo de seguridad, agregar o quitar usuarios del grupo no requiere ninguna actualización en Data Lake Store. 

### <a name="security-for-groups"></a>Seguridad de los grupos 

Como ya se ha explicado, si los usuarios necesitan acceder a Data Lake Store, es mejor usar los grupos de seguridad de Azure Active Directory. Algunos grupos recomendados para empezar a trabajar son **ReadOnlyUsers**, **WriteAccessUsers** y **FullAccessUsers** para la raíz de la cuenta e incluso otros aparte para las subcarpetas principales. Si prevé que habrá más grupos que se tendrán que agregar posteriormente, pero aún no se han identificado, puede considerar la opción de crear grupos de seguridad ficticios que tengan acceso a determinadas carpetas. El uso de grupos de seguridad garantiza que más adelante no necesitará un tiempo de procesamiento largo para asignar nuevos permisos a miles de archivos. 

### <a name="security-for-service-principals"></a>Seguridad de las entidades de servicio 

Azure HDInsight suele usar entidades de servicio de Azure Active Directory para acceder a datos de Data Lake Store. Según los requisitos de acceso de varias cargas de trabajo, puede que haya que tener en cuenta algunas consideraciones para garantizar la seguridad dentro y fuera de la organización. Para muchos clientes, puede que sea suficiente una única entidad de servicio de Azure Active Directory, y esta puede contener permisos completos en la raíz de Data Lake Store. Puede que otros clientes requieran varios clústeres con diferentes entidades de servicio donde uno de los clústeres tenga acceso total a los datos y otro tenga solo acceso de lectura. Al igual que con los grupos de seguridad, puede considerar la opción de crear una entidad de servicio para cada escenario previsto (lectura, escritura, total) una vez que haya creado la cuenta de Data Lake Store. 

### <a name="enable-the-data-lake-store-firewall-with-azure-service-access"></a>Habilitamiento del firewall de Data Lake Store para el acceso de servicios de Azure 

Data Lake Store admite la opción de activar un firewall y limitar el acceso solo a los servicios de Azure, lo cual se recomienda para reducir los vectores de ataque procedentes de intrusiones externas. El firewall se puede habilitar en la cuenta de Data Lake Store de Azure Portal a través de las opciones **Firewall** > **Habilitar firewall (ON)** > **Allow access to Azure services** (Permitir acceso a servicios de Azure).  

![Configuración del firewall en Data Lake Store](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Configuración del firewall en Data Lake Store")

Una vez habilitado el firewall, solo los servicios de Azure como HDInsight, Data Factory, SQL Data Warehouse, etc., podrán acceder a Data Lake Store. Debido a la traducción interna de direcciones de red que usa Azure, el firewall de Data Lake Store no admite la restricción de servicios concretos por IP y solo está diseñado para restringir puntos de conexión externos de Azure como, por ejemplo, los locales. 

## <a name="performance-and-scale-considerations"></a>Consideraciones sobre rendimiento y escalado

Una de las características más eficaces de Data Lake Store es que permite eliminar los estrictos límites de rendimiento de datos. La eliminación de los límites permite a los clientes aumentar el tamaño de los datos y de los requisitos de rendimiento que estos requieren sin necesidad de particionar los datos. Una de las consideraciones más importantes a la hora de optimizar el rendimiento de Data Lake Store es que este funciona mejor si se proporciona paralelismo.

### <a name="improve-throughput-with-parallelism"></a>Mejora del rendimiento mediante el paralelismo 

Considere proporcionar 8-12 subprocesos por núcleo para conseguir el rendimiento óptimo de lectura y escritura. Esto se debe al bloqueo de lecturas o escrituras en un único subproceso. Un mayor número de subprocesos puede permitir una mayor simultaneidad en la máquina virtual. Para garantizar que los niveles sean correctos y que se pueda aumentar el paralelismo, asegúrese de supervisar el uso de la CPU por parte de la máquina virtual.   

### <a name="avoid-small-file-sizes"></a>Evitar tamaños de archivo reducidos

Los permisos POSIX y la auditoría de Data Lake Store presentan un problema de sobrecarga que se hace más visible al trabajar con numerosos archivos pequeños. Por tanto, es recomendable agrupar por lotes los datos en archivos de mayor tamaño en lugar de escribir miles o millones de pequeños archivos en Data Lake Store. Evitar los tamaños de archivo reducidos puede tener varias ventajas como, por ejemplo:

* Reducción de las comprobaciones de autenticación entre varios archivos
* Reducción del número de conexiones de archivos abiertas
* Copia y replicación más rápidas
* Menor número de archivos para procesar al actualizar los permisos POSIX de Data Lake Store 

Dependiendo de qué servicios y cargas de trabajo utilizan los datos, un buen intervalo de tamaños de archivo podría ser de 256 MB a 1 GB, nunca menos de 100 MB ni más de 2 GB. Si no se pueden procesar por lotes los tamaños de archivo al colocarlos en Data Lake Store, puede que necesite un trabajo de compactación independiente que combine estos archivos en otros mayores. Para más información y recomendaciones sobre tamaños de archivo, y sobre la organización de los datos de Data Lake Store, consulte [Estructuración del conjunto de datos](data-lake-store-performance-tuning-guidance.md#structure-your-data-set). 

### <a name="large-file-sizes-and-potential-performance-impact"></a>Tamaños de archivo grandes y posible impacto en el rendimiento 

Aunque Data Lake Store admite archivos grandes de hasta varios petabytes de tamaño, si desea obtener un rendimiento óptimo y en función del proceso de lectura de los datos, se recomienda no superar los 2 GB de promedio. Por ejemplo, al utilizar **Distcp** para copiar datos entre ubicaciones o diferentes cuentas de almacenamiento, los archivos constituyen el nivel de granularidad más adecuado que se usa para determinar las tareas de asignación. Por tanto, si va a copiar 10 archivos que tienen 1 TB cada uno, se asignarán 10 mapeadores como máximo. Igualmente, si tiene muchos archivos con mapeadores asignados, estos funcionarán inicialmente en paralelo para mover archivos grandes. Sin embargo, en la recta final de un trabajo, solo unos cuantos mapeadores permanecen asignados y puede quedar bloqueado con un único mapeador asignado a un archivo grande. Microsoft ha agregado mejoras a Distcp para solucionar este problema en versiones futuras de Hadoop.  

Otro ejemplo que debe tener en cuenta es cuando se usa Azure Data Lake Analytics con Data Lake Store. Según el tipo de procesamiento realizado por el extractor, puede que algunos archivos que no se pueden dividir (por ejemplo, archivos XML o JSON) experimenten un menor rendimiento si superan los 2 GB de tamaño. En aquellos casos en los que un extractor puede dividir los archivos (por ejemplo, archivos CSV), se prefieren los archivos grandes.

### <a name="capacity-plan-for-your-workload"></a>Plan de capacidad para la carga de trabajo 

Azure Data Lake Store permite suprimir las limitaciones estrictas de E/S que tienen las cuentas de Blob Storage. No obstante, aún hay limitaciones flexibles que debe tener en cuenta. Las limitaciones predeterminadas de entrada y salida permiten satisfacer las necesidades de la mayoría de los escenarios. Si su carga de trabajo necesita que se aumenten los límites, póngase en contacto con el soporte técnico de Microsoft. Además, observe los límites durante la fase de prueba de concepto para que no se alcancen los límites de E/S durante la producción. Si eso sucede, puede que tenga que esperar un aumento manual por parte del equipo de ingeniería de Microsoft. Si se produce una limitación de E/S, Azure Data Lake Store devolverá un código de error 429 y, lo ideal sería volver a intentarlo con una directiva de retroceso exponencial adecuada. 

### <a name="optimize-writes-with-the-data-lake-store-driver-buffer"></a>Optimización de "escrituras" con el búfer de controlador de Data Lake Store 

Para optimizar el rendimiento y reducir el número de operaciones de E/S por segundo cuando escriba en Data Lake Store desde Hadoop, ejecute operaciones de escritura en un número lo más adecuado posible al tamaño del búfer de controlador de Data Lake Store. Intente no superar el tamaño de búfer antes de vaciarlo como sucede, por ejemplo, durante el streaming mediante Apache Storm o en las cargas de trabajo de streaming de Spark. Al escribir en Data Lake Store desde HDInsight/Hadoop, es importante saber que Data Lake Store tiene un controlador con un búfer de 4 MB. Al igual que con muchos otros controladores del sistema de archivos, este búfer se puede vaciar manualmente antes de alcanzar ese tamaño. De lo contrario, si la siguiente operación de escritura supera el tamaño máximo del búfer este se vaciará inmediatamente en el almacenamiento. Siempre que sea posible, debe evitar tanto el desbordamiento como una significativa infrautilización del búfer al sincronizar o vaciar directivas por recuentos o períodos de tiempo.

## <a name="resiliency-considerations"></a>Consideraciones de resistencia 

Al diseñar un sistema con Data Lake Store o cualquier otro servicio en la nube, debe tener en cuenta sus requisitos de disponibilidad y cómo dar respuesta a posibles interrupciones del servicio. Un problema puede estar localizado en una instancia concreta o abarcar a toda una región. Por ello es importante disponer de un plan para ambos escenarios. Según lo estipulado en el Acuerdo de Nivel de Servicio en relación con el **objetivo de tiempo de recuperación** y el **objetivo de punto de recuperación** de la carga de trabajo, puede elegir una estrategia más o menos exigente para proporcionar alta disponibilidad y recuperación ante desastres.

### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidad y recuperación ante desastres 

La alta disponibilidad (HA) y la recuperación ante desastres (DR) se pueden combinar en algunas ocasiones, aunque cada una de ellas sigue una estrategia ligeramente diferente en lo que respecta a los datos. Data Lake Store ya controla la replicación 3x en segundo plano para proteger frente a los errores de hardware localizados. No obstante, como la replicación entre regiones no está aún integrada, deberá administrarla usted mismo. Cuando se crea un plan de alta disponibilidad, si se produce una interrupción del servicio, la carga de trabajo necesita acceder a los datos más recientes lo más rápidamente posible mediante el traslado a una instancia replicada de manera independiente localmente o en una nueva región.  

En una estrategia de recuperación ante desastres, para estar preparados para el improbable caso de un error grave en una región, también es importante tener los datos replicados en una región diferente. Estos datos pueden ser inicialmente los mismos que los datos replicados de alta disponibilidad. Sin embargo, también debe tener en cuenta los requisitos en casos extremos como, por ejemplo, datos dañados donde quiere crear instantáneas periódicas a las que revertir. Según la importancia y el tamaño de los datos, considere la posibilidad de disponer de instantáneas delta graduales de períodos de 1, 6 y 24 horas en el almacén local o en el secundario, según las tolerancias al riesgo. 

Para mejorar la resistencia de datos con Data Lake Store, se recomienda realizar una replicación geográfica de los datos en una región distinta con una frecuencia que satisfaga los requisitos de alta disponibilidad y recuperación ante desastres. Lo ideal sería cada hora. Esta frecuencia de replicación minimiza los movimientos de datos masivos que pueden presentar necesidades de rendimiento que entran en conflicto con el sistema principal y un mejor objetivo de punto de recuperación (RPO). Además, debe proporcionar medios para que la aplicación que usa Data Lake Store realice automáticamente la conmutación por error en la cuenta secundaria a través de la supervisión de desencadenadores o la longitud de los intentos erróneos, o que al menos envíe una notificación a los administradores para que intervengan de forma manual. Tenga en cuenta que también hay inconvenientes por realizar una conmutación por error en lugar de esperar a que un servicio vuelva a estar en línea. Si no ha terminado la replicación de los datos, una conmutación por error podría provocar posibles pérdidas, incoherencias o combinaciones complejas de datos. 

A continuación se indican las tres opciones principales recomendadas para orquestar la replicación entre cuentas de Data Lake Store y las principales diferencias entre cada una de ellas.


|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Límites de escalado**     | Limitado por los nodos de trabajo        | Limitado por el número máximo de unidades de movimiento de datos en la nube        | Limitado por las unidades de análisis        |
|**Admite la copia de valores delta**     |   Sí      | Sin          | Sin          |
|**Orquestación integrada**     |  No (use Oozie Airflow o trabajos cron)       | Sí        | No (use Azure Automation o el programador de tareas de Windows)         |
|**Sistemas de archivos admitidos**     | ADL, HDFS, WASB, S3, GS, CFS        |Varios, consulte [Conectores](../data-factory/connector-azure-blob-storage.md).         | ADL a ADL, WASB a ADL (solo en la misma región)        |
|**SO compatible**     |Cualquier sistema operativo que ejecute Hadoop         | N/D          | Windows 10         |
   

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Uso de Distcp para el traslado de datos entre dos ubicaciones 

Distcp es la forma abreviada (en inglés) de copia distribuida, una herramienta de línea de comandos de Linux que se incluye con Hadoop y que permite el traslado de datos distribuidos entre dos ubicaciones. Las dos ubicaciones pueden ser Data Lake Store, HDFS, WASB o S3. Esta herramienta usa trabajos de MapReduce en un clúster de Hadoop (por ejemplo, HDInsight) para realizar un escalado horizontal en todos los nodos. Distcp se considera la forma más rápida de trasladar macrodatos sin necesidad de dispositivos de compresión de red especiales. Distcp también proporciona una opción para actualizar solo los valores delta entre dos ubicaciones y controla los reintentos automáticos así como el escalado dinámico de los procesos. Este enfoque es increíblemente eficaz cuando se trata de replicar elementos tales como tablas de Hive o Spark, que pueden incluir muchos archivos grandes en un único directorio, y solo desea copiar los datos modificados. Por estos motivos, Distcp es la herramienta más recomendada para copiar datos entre almacenes de macrodatos. 

Los flujos de trabajo de Apache Oozie pueden desencadenar trabajos de copia mediante desencadenadores de frecuencia o de datos, así como trabajos de cron de Linux. Para trabajos de replicación intensivos, se recomienda poner en marcha un clúster de HDInsight Hadoop independiente que se pueda optimizar y escalar específicamente para los trabajos de copia. Esto garantiza que los trabajos de copia no interferirán con trabajos críticos. Si se ejecuta la replicación en una frecuencia lo bastante amplia, se puede incluso desconectar el clúster entre cada trabajo. Si se realiza una conmutación por error a una región secundaria, asegúrese de que también se pone en marcha otro clúster en esa región para replicar los nuevos datos otra vez en la cuenta principal de Data Lake Store una vez que se vuelva a conectar. Para ver ejemplos del uso de Distcp, consulte [Uso de Distcp para copiar datos entre Azure Storage Blob y Data Lake Store](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Uso de Azure Data Factory para programar trabajos de copia 

Azure Data Factory también se puede utilizar para programar trabajos de copia mediante una **actividad de copia** e incluso se puede configurar una frecuencia para esta actividad mediante el **Asistente para copia**. Tenga en cuenta que Azure Data Factory tiene un límite de unidades de movimiento de datos en la nube (DMU) que, en última instancia, limita el rendimiento y los procesos para cargas de trabajo con datos grandes. Además, Azure Data Factory no ofrece en la actualidad la actualización de valores delta entre cuentas de Data Lake Store, por lo que carpetas como las tablas de Hive necesitarán una copia completa para su replicación. Consulte la [guía de optimización de la actividad de copia](../data-factory/v1/data-factory-copy-activity-performance.md) para más información acerca de cómo copiar con Data Factory. 

### <a name="adlcopy"></a>AdlCopy

AdlCopy es una herramienta de línea de comandos de Windows que permite copiar datos entre dos cuentas de Data Lake Store solo dentro de la misma región. La herramienta AdlCopy proporciona una opción independiente o la posibilidad de usar una cuenta de Azure Data Lake Analytics para ejecutar el trabajo de copia. Aunque se diseñó originalmente para realizar copias a petición en lugar de una replicación sólida, proporciona otra opción para efectuar copiar distribuidas entre cuentas de Data Lake Store de la misma región. Para mejorar la confiabilidad, se recomienda utilizar la opción premium de Data Lake Analytics para cualquier carga de trabajo de producción. La versión independiente puede devolver respuestas de no disponible y tiene un escalado y supervisión limitados. 

Al igual que Distcp, AdlCopy se debe orquestar mediante, por ejemplo, Azure Automation o el programador de tareas de Windows. Al igual que con Data Factory, AdlCopy no admite la copia de solo los archivos actualizados, sino que vuelve a copiar y a sobrescribir los archivos existentes. Para más información y ejemplos de uso de AdlCopy, consulte [Copia de datos de Azure Storage Blob en Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Consideraciones sobre supervisión 

Data Lake Store proporciona registros de diagnóstico detallados y auditoría. Data Lake Store proporciona algunas métricas básicas en Azure Portal en la cuenta de Data Lake Store y en Azure Monitor. La disponibilidad de Data Lake Store se muestra en Azure Portal. Sin embargo, esta métrica se actualiza cada siete minutos y no se puede consultar a través de ninguna API expuesta públicamente. Para obtener la disponibilidad más actualizada de una cuenta de Data Lake Store, deberá ejecutar sus propias pruebas sintéticas para validar la disponibilidad. Otras métricas como el uso del almacenamiento total, las solicitudes de lectura/escritura y entrada/salida pueden tardar hasta 24 horas en actualizarse. Por lo tanto, para obtener métricas más actualizadas, estas se deben calcular manualmente mediante herramientas de línea de comandos de Hadoop o agregando la información del registro. La forma más rápida de obtener la información de uso del almacenamiento más reciente es ejecutar este comando HDFS desde un nodo de clúster de Hadoop (por ejemplo, el nodo principal):   

    hdfs dfs -du -s -h adl://<adls_account_name>.azuredatalakestore.net:443/

### <a name="export-data-lake-store-diagnostics"></a>Exportación de diagnósticos de Data Lake Store 

Una de las formas más rápidas de acceder a los registros que permiten búsquedas de Data Lake Store es habilitar el trasvase de registros a **Operations Management Suite (OMS)** en la hoja **Diagnóstico** de la cuenta de Data Lake Store. Esto proporciona acceso inmediato a los registros entrantes con filtros de horas y contenido, junto con opciones de alerta (correo electrónico/webhook) que se desencadenan en intervalos de 15 minutos. Para obtener instrucciones, consulte [Acceso a los registros de diagnóstico de Azure Data Lake Store](data-lake-store-diagnostic-logs.md). 

Para recibir más alertas en tiempo real y tener más control sobre dónde se guardan los registros, considere la posibilidad de exportar los registros a Azure Event Hubs, donde se puede analizar el contenido de forma individual o a lo largo de un período de tiempo para enviar notificaciones en tiempo real a una cola. Una aplicación aparte como [Logic App](../connectors/connectors-create-api-azure-event-hubs.md) puede posteriormente consumir y comunicar las alertas al canal apropiado, así como enviar las métricas a herramientas de supervisión como NewRelic, Datadog o AppDynamics. Alternativamente, si está usando una herramienta de terceros como ElasticSearch, puede exportar los registros a Blob Storage y usar el [complemento Azure Logstash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) para consumir los datos en la pila de Elasticsearch, Kibana y Logstash (ELK).

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Activación de los registros en el nivel de depuración en HDInsight 

Si el trasvase de registros de Data Lake Store no está activado, Azure HDInsight también proporciona una forma de activar [los registros en el lado del cliente para Data Lake Store](data-lake-store-performance-tuning-mapreduce.md) a través de log4j. Debe establecer la siguiente propiedad en **Ambari** > **YARN** > **Config** > **Advanced yarn-log4j configurations** (Configuraciones avanzadas de yarn-log4j): 

    log4j.logger.com.microsoft.azure.datalake.store=DEBUG 

Una vez que esta se establece y se reinician los nodos, los diagnósticos de Data Lake Store se escriben en los registros de YARN en los nodos (/tmp/<user>/yarn.log) y se pueden supervisar detalles importantes como los errores o las limitaciones (código de error HTTP 429). Esta misma información también se puede supervisar en OMS o en cualquier ubicación en la que se trasvasen los registros en la hoja [Diagnóstico](data-lake-store-diagnostic-logs.md) de la cuenta de Data Lake Store. Se recomienda tener activado al menos el registro del lado cliente o utilizar la opción de trasvase de registros con Data Lake Store para una mayor visibilidad operativa y una depuración más sencilla.

### <a name="run-synthetic-transactions"></a>Ejecutar transacciones sintéticas 

En la actualidad, la métrica de disponibilidad del servicio para Data Lake Store en Azure Portal tiene una ventana de actualización de 7 minutos. Además, no se puede consultar mediante una API expuesta públicamente. Por tanto, se recomienda compilar una aplicación básica que realice transacciones sintéticas en Data Lake Store que puedan proporcionar la disponibilidad más actualizada. Un ejemplo lo puede constituir la creación de un webjob, una instancia de Logic Apps o de Azure Function App que realice una operación de lectura, creación y actualización en Data Lake Store y envíe los resultados a la solución de supervisión. Las operaciones se pueden realizar en una carpeta temporal que se eliminará después de la prueba, y se podrían ejecutar cada 30 o 60 segundos, en función de los requisitos.

## <a name="directory-layout-considerations"></a>Consideraciones sobre el diseño del directorio

Cuando se colocan los datos en una instancia de Data Lake Store, es importante planear previamente la estructura de los datos para que se pueda emplear eficazmente la seguridad, el particionamiento y el procesamiento. Muchas de las recomendaciones siguientes se pueden usar ya sea con Azure Data Lake Store, Blob Storage o HDFS. Cada carga de trabajo tiene distintos requisitos sobre cómo los datos se consumen pero, a continuación, se muestran algunos diseños habituales a tener en cuenta al trabajar con escenarios de IoT y de lotes.

### <a name="iot-structure"></a>Estructura de IoT 

En las cargas de trabajo de IoT, puede haber una gran cantidad de datos que se colocan en el almacén de datos que abarca varios productos, dispositivos, organizaciones y clientes. Es importante planear previamente el diseño del directorio para la organización, la seguridad y un procesamiento eficaz de los datos para los consumidores de nivel inferior. Una plantilla general a tener en cuenta podría tener el siguiente diseño: 

    {Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/ 

Por ejemplo, la telemetría de aterrizaje de un motor de un avión del Reino Unido podría ser parecida a esta: 

    UK/Planes/BA1293/Engine1/2017/08/11/12/ 

Existe un motivo importante para poner la fecha al final de la estructura de carpetas. Si desea bloquear determinadas regiones o asuntos a usuarios o grupos, puede hacerlo fácilmente con los permisos POSIX. En caso contrario, si fuera necesario aplicar restricciones a un determinado grupo de seguridad para que solo vea los datos del Reino Unido o de determinados aviones con la estructura de fechas delante, se requeriría un permiso independiente para varias carpetas bajo la carpeta de cada hora. Además, el hecho de tener la estructura de fecha delante, aumentaría exponencialmente el número de carpetas a medida que transcurriera el tiempo.

### <a name="batch-jobs-structure"></a>Estructura de trabajos por lotes 

Desde una perspectiva general, un enfoque utilizado habitualmente en el procesamiento por lotes es situar los datos en una carpeta "in". Posteriormente, una vez procesados los datos, ponga los nuevos datos en una carpeta "out" para que los consuman los procesos de nivel inferior. Esto se ve a veces con trabajos que requieren el procesamiento de archivos individuales pero que no requieren un procesamiento paralelo masivo en grandes conjuntos de datos. Al igual que en la estructura de IoT recomendada anteriormente, una buena estructura de directorios dispone de carpetas de nivel primario para cosas como las regiones o los asuntos (por ejemplo, organización, producto o productor). Esto ayuda a proteger los datos en la organización y a mejorar la administración de los datos de las cargas de trabajo. Además, tenga en cuenta la posibilidad de usar la fecha y la hora en la estructura para permitir una mejor organización, búsquedas filtradas, seguridad y automatización del procesamiento. El nivel de granularidad de la estructura de fecha viene determinado por el intervalo en el que los datos se cargan o procesan como, por ejemplo, cada hora, cada día o incluso mensualmente. 

En algunas ocasiones, el procesamiento de archivos es incorrecto debido a datos dañados o a formatos imprevistos. En tales casos, podría resultar útil que la estructura de directorios tuviera una carpeta **/bad** a la que mover los archivos para una mayor inspección. El trabajo por lotes también puede controlar el informe o notificación de estos archivos *incorrectos* para una posterior intervención manual. Tenga en cuenta la siguiente estructura de plantilla: 

    {Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/ 
    {Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/ 

Por ejemplo, una empresa de marketing que recibe a diario extractos de datos de actualizaciones de los clientes de Estados Unidos podría tener este aspecto antes y después del procesamiento: 

    NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv 
    NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv 
 
En el caso habitual de datos por lotes que se procesan directamente en bases de datos como Hive o instancias tradicionales de SQL Database, no es necesaria una carpeta **/in** o **/out** puesto que la salida ya va a una carpeta independiente de la tabla de Hive o a una base de datos externa. Por ejemplo, los extractos diarios de los clientes se colocarían en sus respectivas carpetas y la orquestación de Azure Data Factory, Apache Oozie o Apache Airflow desencadenaría un trabajo diario de Hive o Spark que procesaría y escribiría los datos en una tabla de Hive.

## <a name="next-steps"></a>Pasos siguientes     

* [Información general de Azure Data Lake Store](data-lake-store-overview.md) 
* [Control de acceso en Azure Data Lake Store](data-lake-store-access-control.md) 
* [Seguridad en Azure Data Lake Store](data-lake-store-security-overview.md)
* [Optimización del rendimiento de Azure Data Lake Store](data-lake-store-performance-tuning-guidance.md)
* [Guía para la optimización del rendimiento de Spark en HDInsight y Azure Data Lake Store](data-lake-store-performance-tuning-spark.md)
* [Guía para la optimización del rendimiento de Hive en HDInsight y Azure Data Lake Store](data-lake-store-performance-tuning-hive.md)
* [Data Orchestration using Azure Data Factory for Azure Data Lake Store (Orquestación de datos mediante Azure Data Factory para el Almacén de Azure Data Lake)](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Creación de clústeres de HDInsight con Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md) 
