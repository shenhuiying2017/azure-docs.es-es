---
title: "Azure Data Factory: preguntas más frecuentes | Microsoft Docs"
description: "Obtenga respuestas a las preguntas más frecuentes acerca de Azure Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: shlo
ms.openlocfilehash: cf98bb7fab4942955287e8e211e98b9da59472f6
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="azure-data-factory-faq"></a>Preguntas más frecuentes de Azure Data Factory
Este artículo se aplica a la versión 2 del servicio de Azure Data Factory. Proporciona respuestas a las preguntas más frecuentes acerca de Data Factory.  

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte las [preguntas más frecuentes de la versión 1 de Data Factory](v1/data-factory-faq.md).

## <a name="what-is-azure-data-factory"></a>¿Qué es Azure Data Factory? 
Data Factory es un servicio de integración de datos basado en la nube que automatiza el movimiento y la transformación de datos. Al igual que las máquinas de una fábrica transforman las materias primas en productos acabados, Azure Data Factory organiza los servicios existentes que recopilan datos sin procesar y los transforman en información lista para utilizar. 

Con Azure Data Factory, puede crear flujos de trabajo controlados por datos para mover datos entre almacenes de datos locales y en la nube. Puede procesar y transformar datos mediante servicios de proceso, como Azure HDInsight, Azure Data Lake Analytics e Integration Runtime de SQL Server Integration Services (SSIS). 

Con Data Factory, puede ejecutar el procesamiento de datos en un servicio en la nube basado en Azure o en su propio entorno de proceso autohospedado, como SSIS, SQL Server u Oracle. Después de crear una canalización que realice la acción que necesita, puede programar que se ejecute periódicamente (por ejemplo, cada hora, diariamente o semanalmente) o desencadenarla a partir de la repetición de un evento. Para más información, consulte [Introducción a Azure Data Factory](introduction.md).

## <a name="whats-different-in-version-2"></a>Diferencias en la versión 2
La versión 2 de Azure Data Factory se basa en el servicio de transformación y movimiento de datos original de Azure Data Factory, que se extiende a un conjunto más amplio de escenarios de integración de datos en la nube en primer lugar. La versión 2 de Azure Data Factory ofrece las siguientes funcionalidades:

- Escala y flujos de control
- Implementación y ejecución de paquetes SSIS en Azure

Tras el lanzamiento de la versión 1, detectamos que los clientes necesitaban diseñar escenarios complejos de integración de datos híbridos que requerían tanto el movimiento como el procesamiento de datos en la nube, en el entorno local y en las máquinas virtuales en la nube (VM). Estos requisitos ponen de manifiesto la necesidad de transferir y procesar los datos dentro de entornos de red virtual protegidos y escalar horizontalmente la potencia de procesamiento a petición.

Como las canalizaciones de datos se están convirtiendo en una parte fundamental de una estrategia de análisis de negocios, hemos observado que estas actividades requieren una programación flexible para admitir cargas de datos incrementales y ejecuciones desencadenadas por eventos. Al ir aumentando la complejidad, también lo hacen los requisitos para que el servicio admita los paradigmas comunes de flujo de trabajo, como el procesamiento condicional, la creación de ramas y el bucle.

Con la versión 2, también puede migrar paquetes SSIS existentes a la nube. Esta acción migra mediante lift-and-shift SSIS como servicio de Azure que se administra dentro de Data Factory, que utiliza una nueva característica de Integration Runtime. Al poner en marcha una instancia de Integration Runtime de SSIS en la versión 2, puede ejecutar, administrar, supervisar y generar paquetes SSIS en la nube.

### <a name="control-flows-and-scale"></a>Escala y flujos de control 
Para admitir los diferentes patrones y flujos de integración en el almacenamiento de datos actual, Data Factory ha habilitado un nuevo modelo de canalización de datos flexible que ya no está asociado a los datos de serie temporal. Con esta versión, puede modelar condicionales, crear una rama en el flujo de control de una canalización de datos, así como pasar explícitamente los parámetros dentro y entre estos flujos.

Ahora tiene la libertad de modelar cualquier estilo de flujo necesario para la integración de datos y que se puede enviar a petición o varias veces según una programación. A continuación, se indican algunos flujos comunes ya habilitados que anteriormente no eran posibles:   

- Flujos de control:
    - Encadenamiento de actividades en una secuencia dentro de una canalización.
    - Creación de una rama de actividades dentro de una canalización.
    - Parámetros
        - Defina parámetros en el nivel de canalización y pase argumentos al invocar la canalización a petición o mediante un desencadenador.
        - Las actividades pueden consumir los argumentos que se pasan a la canalización.
    - Paso a estado personalizado
        - Los resultados de la actividad, incluido el estado, se pueden usar en una posterior actividad de la canalización.
    - Contenedores de bucle
        - Para cada 
- Flujos basados en el desencadenador:
    - Las canalizaciones se pueden desencadenar a petición o por tiempo de reloj.
- Flujos delta:
    - Use parámetros y defina el límite máximo de la copia delta al mover tablas de dimensiones o de referencia desde un almacén relacional de forma local o en la nube para cargar los datos en Data Lake. 

Para más información, consulte el siguiente [tutorial: flujos de control](tutorial-control-flow.md).

### <a name="deploy-ssis-packages-to-azure"></a>Implementación de paquetes SSIS en Azure 
Si desea mover las cargas de trabajo de SSIS, puede crear una versión 2 de Data Factory y aprovisionar una instancia de Integration Runtime de SSIS de Azure. Integration Runtime de SSIS de Azure es un clúster totalmente administrado de máquinas virtuales de Azure (nodos) que se dedican a ejecutar los paquetes SSIS en la nube. Para obtener instrucciones paso a paso, vea el tutorial [Implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). 
 

### <a name="sdks"></a>SDK
Si es un usuario avanzado y busca una interfaz programática, la versión 2 le proporciona un amplio conjunto de SDK que puede usar para crear, administrar o supervisar canalizaciones mediante su IDE favorito.

- **SDK de .NET**: el SDK de .NET se actualiza para la versión 2. 
- **PowerShell**: los cmdlets de PowerShell se actualizan para la versión 2. La versión 2 de los cmdlets tienen *DataFactoryV2* en el nombre. Por ejemplo, *Get-AzureRmDataFactoryV2*. 
- **SDK de Python**: este SDK es una novedad de la versión 2.
- **API de REST**: la API de REST se actualiza para la versión 2.  

Los SDK que se actualizan para la versión 2 no son compatibles con los clientes de la versión 1. 

### <a name="monitoring"></a>Supervisión
Actualmente, la versión 2 admite la supervisión de factorías de datos usando solo SDK. El portal aún no es compatible con la supervisión de las factorías de datos de la versión 2. 

## <a name="what-is-integration-runtime"></a>¿Qué es Integration Runtime?
Integration Runtime es la infraestructura de proceso que Azure Data Factory usa para proporcionar las siguientes funcionalidades de integración de datos en diversos entornos de red:

- **Movimiento de datos**: mueva los datos entre almacenes de datos de redes públicas y de redes privadas (red privada local o virtual). Proporciona compatibilidad para conectores integrados, conversión de formato, asignación de columnas y transferencia de datos escalable y de rendimiento superior.
- **Distribución de actividades**: distribuya y supervise actividades de transformación que se ejecuten en una gran variedad de servicios de proceso, como Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server, etc.
- **Ejecución de paquetes SSIS**: ejecuta de forma nativa paquetes SSIS en un entorno de proceso de Azure administrado.

Puede implementar una o varias instancias de Integration Runtime según sea necesario para mover y transformar los datos. Integration Runtime se puede ejecutar en una red de Azure pública o en una red privada (local, Azure Virtual Network o en la nube privada virtual de Amazon Web Services [VPC]). 

Para más información, consulte [Integration Runtime en Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>¿Cuál es el límite del número de Integration Runtimes?
No hay ningún límite estricto acerca del número de instancias de Integration Runtime que puede tener en una factoría de datos. Sin embargo, existe un límite acerca del número de núcleos de máquina virtual que Integration Runtime puede usar por suscripción para la ejecución de paquetes SSIS. Para más información, consulte los [Límites de Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="when-should-i-use-version-2-rather-than-version-1"></a>¿Cuándo debo utilizar la versión 2 en lugar de la versión 1? 
Si es nuevo en Azure Data Factory, inicie directamente con la versión 2. Si ya usa la versión 1, recompile las factorías de datos en la versión 2.

> [!WARNING]
> La versión 2 de Data Factory está en versión preliminar y no en disponibilidad general (GA). Por lo tanto, no entran en los mismos compromisos del contrato de nivel de servicio (SLA) de Azure que la versión 1 de Data Factory, que se encuentra en versión GA. 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>¿Cuáles son los conceptos de nivel superior de la versión 2?
Una suscripción de Azure puede tener una o varias instancias de Azure Data Factory (o factorías de datos). Azure Data Factory contiene cuatro componentes principales que funcionan juntos como plataforma en la que pueda crear flujos de trabajo orientados a datos con pasos para moverlos y transformarlos.

### <a name="pipelines"></a>Procesos
Una factoría de datos puede tener una o más canalizaciones. Una canalización es una agrupación lógica de actividades para realizar una unidad de trabajo. Juntas, las actividades de una canalización realizan una tarea. Por ejemplo, una canalización puede contener un grupo de actividades que ingiere datos de un blob de Azure y luego ejecuta una consulta de Hive en un clúster de HDInsight para particionar los datos. La ventaja es que puede usar una canalización para administrar las actividades como un conjunto en lugar de tener que administrar individualmente cada actividad. Puede encadenar juntas las actividades en una canalización para hacerlas funcionar de forma secuencial o puede hacerlas funcionar de forma independiente en paralelo.

### <a name="activity"></a>Actividad
Las actividades representan un paso del procesamiento en una canalización. Por ejemplo, puede utilizar una actividad de *Copia* para copiar datos de un almacén de datos a otro. De igual forma, puede usar una actividad de Hive, que ejecuta una consulta de Hive en un clúster de Azure HDInsight para transformar o analizar los datos. Data Factory admite tres tipos de actividades: actividades de movimiento de datos, actividades de transformación de datos y actividades de control.

### <a name="data-sets"></a>Conjuntos de datos
Los conjuntos de datos representan las estructuras de datos de los almacenes de datos que simplemente apuntan o hacen referencia a los datos que desea utilizar en sus actividades como entradas o salidas. 

### <a name="linked-services"></a>Servicios vinculados
Los servicios vinculados son muy similares a las cadenas de conexión que definen la información de conexión necesaria para que Data Factory se conecte a recursos externos. Considérelos de esta forma: un servicio vinculado define la conexión al origen de datos y un conjunto de datos representa la estructura de los datos. Por ejemplo, un servicio vinculado de Azure Storage especifica la cadena de conexión para conectarse a la cuenta de Azure Storage. Además, un conjunto de datos de Azure Blob especifica el contenedor de blobs y la carpeta que contiene los datos.

Los servicios vinculados tienen dos fines en Data Factory:

- Para representar un *almacén de datos* que incluye, entre otros, una instancia de SQL Server local, una instancia de base de datos de Oracle, un recurso compartido de archivos o una cuenta de Azure Blob Storage. Para obtener una lista de almacenes de datos compatibles, consulte [Actividad de copia en Azure Data Factory](copy-activity-overview.md).
- Para representar un *recurso de proceso* que puede hospedar la ejecución de una actividad. Por ejemplo, la actividad HDInsight Hive se ejecuta en un clúster de Hadoop para HDInsight. Para ver una lista de actividades de transformación y de entornos de proceso admitidos, consulte el artículo [Transformar datos en Azure Data Factory](transform-data.md).

### <a name="triggers"></a>Desencadenadores
Los desencadenadores representan unidades de procesamiento que determinan cuándo se pone en marcha una ejecución de canalización. Existen diferentes tipos de desencadenadores para diferentes tipos de eventos. En versión preliminar, se admite un desencadenador del programador de reloj. 


### <a name="pipeline-runs"></a>Ejecuciones de la canalización
Una ejecución de una canalización es una instancia de la ejecución de la canalización. Normalmente, crea instancias de una ejecución de canalización al pasar argumentos a los parámetros definidos en la canalización. Puede pasar los argumentos manualmente o dentro de la definición del desencadenador.

### <a name="parameters"></a>Parámetros
Los parámetros son pares clave-valor en una configuración de solo lectura. Defina parámetros en una canalización y pase los argumentos para los parámetros definidos durante la ejecución de un contexto de ejecución. El contexto de ejecución se crea mediante un desencadenador o desde una canalización que ejecuta manualmente. Las actividades dentro de la canalización consumen los valores de parámetro.

Un conjunto de datos es un parámetro fuertemente tipado y una entidad que puede reutilizar o a la que puede hacer referencia. Una actividad puede hacer referencia a conjuntos de datos y puede consumir las propiedades definidas en la definición del conjunto de datos.

Un servicio vinculado también es un parámetro fuertemente tipado que contiene información de conexión a un almacén de datos o a un entorno de proceso. También es una entidad que puede reutilizar o a la que puede hacer referencia.

### <a name="control-flows"></a>Flujos de control
Los flujos de control organizan actividades de canalización que incluyen el encadenamiento de actividades en una secuencia, la creación de ramas, los parámetros que define en el nivel de canalización y los argumentos que pasa al invocar la canalización a petición o desde un desencadenador. Los flujos de control también incluyen el paso a un estado personalizado y contenedores de bucle (es decir, los iteradores Para cada).


Para más información sobre los conceptos de Data Factory, consulte los siguientes artículos:

- [Conjuntos de datos y servicios vinculados](concepts-datasets-linked-services.md)
- [Canalizaciones y actividades](concepts-pipelines-activities.md)
- [Integration runtime](concepts-integration-runtime.md) (Tiempo de ejecución de integración)

## <a name="what-is-the-pricing-model-for-data-factory"></a>¿Qué es el modelo de precios de Data Factory?
Consulte los [detalles de precios de Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) para obtener información al respecto.

## <a name="what-regions-support-azure-data-factory-version-2"></a>¿Qué regiones admiten la versión 2 de Azure Data Factory?
Actualmente, puede crear factorías de datos de la versión 2 en las regiones Este de EE. UU., Este de EE. UU. 2 y Europa Occidental. Sin embargo, una factoría de datos puede usar Integration Runtime en otra región para mover datos entre almacenes de datos, distribuir las actividades según los servicios de proceso o enviar paquetes SSIS. Para más información, consulte las [ubicaciones de Data Factory](concepts-integration-runtime.md#integration-runtime-location).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>¿Cómo puedo mantenerme actualizado con información acerca de Data Factory?
Para ver la información más actualizada acerca de Azure Data Factory, vaya a los sitios siguientes:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Página principal de la documentación](/azure/data-factory)
- [Página principal del producto](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Inmersión técnica profunda 

### <a name="can-version-1-and-version-2-pipelines-run-side-by-side"></a>¿Pueden las canalizaciones de la versión 1 y de la versión 2 ejecutarse en paralelo?
Nº Las factorías de datos de la versión 2 y la versión 1 no pueden contener entidades (por ejemplo, servicios vinculados, conjuntos de datos o canalizaciones) de la otra versión.   

### <a name="do-i-still-need-to-define-data-sets-in-version-2"></a>¿Aún tengo que definir los conjuntos de datos en la versión 2?
Un conjunto de datos ya no es una entidad obligatoria para la mayoría de las actividades. Es necesario para la copia, el aprendizaje automático, la búsqueda, la validación y las actividades personalizadas que utilizan el esquema y otra información de metadatos del conjunto de datos para la transformación. El resto de las actividades ya no requieren conjuntos de datos.

### <a name="can-i-chain-two-activities-without-a-data-set-in-version-2"></a>¿Puedo encadenar dos actividades sin un conjunto de datos en la versión 2?
Sí. Puede encadenar las actividades en la versión 2 sin necesidad de conjuntos de datos. Puede encadenar las actividades mediante la propiedad **dependsOn** en la definición de JSON de la canalización. 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>¿Todas las actividades de la versión 1 se admiten en la versión 2? 
Sí, todas las actividades de la versión 1 se admiten en la versión 2.

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>¿Cómo puedo programar una canalización de la versión 2? 
Puede usar el desencadenador de programador para programar una canalización de la versión 2. El desencadenador utiliza una programación del calendario del reloj y podrá utilizarlo para programar canalizaciones periódicamente o mediante patrones periódicos basados en calendarios (por ejemplo, semanalmente los lunes a las 6 p.m. y los jueves a las 9 p.m.). Para obtener más información, consulte [Desencadenadores y ejecución de la canalización](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>¿Puedo pasar parámetros a una canalización que se ejecuta en la versión 2?
Sí, los parámetros son conceptos de primera clase de nivel superior en la versión 2. Puede definir parámetros en el nivel de canalización y pasar argumentos al ejecutar la canalización a petición o mediante un desencadenador.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>¿Puedo definir valores predeterminados para los parámetros de la canalización? 
Sí. Puede definir valores predeterminados para los parámetros de las canalizaciones. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>¿Una actividad de una canalización puede consumir los argumentos que se pasan a una ejecución de canalización? 
Sí. Cada actividad dentro de la canalización puede utilizar el valor del parámetro que se pasa a la canalización y que se ejecuta con la construcción de `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>¿Puede utilizarse una propiedad de salida de actividad en otra actividad? 
Sí. Un resultado de la actividad se puede utilizar en una actividad posterior con la construcción de `@activity`.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>¿Cómo puedo controlar correctamente los valores NULL en una salida de actividad? 
Puede usar la construcción de `@coalesce` en las expresiones para controlar correctamente los valores NULL. 

### <a name="can-i-use-retry-and-timeout-at-the-activity-level-in-version-2"></a>¿Puedo usar el reintento o el tiempo de expiración en el nivel de actividad en la versión 2?
Sí. Para controlar la ejecución de actividades en la versión 2, al igual que en la versión 1, puede configurar el reintento y el tiempo de expiración en el nivel de actividad. 

## <a name="next-steps"></a>pasos siguientes
Para obtener instrucciones paso a paso para crear una factoría de datos de la versión 2, consulte los siguientes tutoriales:

- [Guía de inicio rápido: Creación de una factoría de datos](quickstart-create-data-factory-dot-net.md)
- [Tutorial: Copia de datos en la nube](tutorial-copy-data-dot-net.md)

