---
title: "Azure Data Factory: preguntas más frecuentes | Microsoft Docs"
description: "Preguntas más frecuentes acerca de Azure Data Factory."
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
ms.date: 09/23/2017
ms.author: shlo
ms.openlocfilehash: 95b088d2fdc331dc33e973172d32892693fcb648
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory: preguntas más frecuentes
Este artículo se aplica a la versión 2 del servicio de Azure Data Factory. Proporciona una lista de preguntas más frecuentes (P+F) y sus respuestas.  

## <a name="what-is-azure-data-factory"></a>¿Qué es Azure Data Factory? 
Data Factory es un servicio de integración de datos basado en la nube que automatiza el movimiento y la transformación de datos. Lo mismo que las máquinas de una fábrica toman las materias primas y las transforman en productos acabados, Azure Data Factory organiza los servicios existentes que recopilan datos sin procesar y los transforman en información lista para utilizar. Azure Data Factory le permite crear flujos de trabajo basados en datos para mover datos entre almacenes de datos locales y en la nube, así como para procesar o transformar datos mediante servicios de proceso como Azure HDInsight, Azure Data Lake Analytics y SQL Server Integration Services (SSIS) Integration Runtime. Data Factory le ofrece la opción de ejecutar el procesamiento de datos en un servicio basado en la nube de Azure o bien de utilizar su propio entorno de proceso autohospedado, como SSIS, SQL Server y Oracle. Después de crear una canalización que realice la acción que necesita, puede programar que se ejecute periódicamente (cada hora, diariamente, semanalmente, etc.) o desencadenarla a partir de una repetición del evento. Para más información, consulte [Introducción a Azure Data Factory](introduction.md).

## <a name="whats-different-in-version-2"></a>Diferencias en la versión 2
La versión 2 de Azure Data Factory se basa en el servicio de transformación y movimiento de datos original de Azure Data Factory, que se extiende a un conjunto más amplio de escenarios de integración de datos en la nube en primer lugar. La versión 2 de Azure Data Factory ofrece las siguientes capacidades:

- Escala y flujo de control
- Implementación y ejecución de paquetes SSIS en Azure

Tras el lanzamiento de la versión 1, detectamos que los clientes necesitaban diseñar escenarios de integración de datos híbridos complejos que requerían tanto el movimiento de datos como el procesamiento en la nube, local y en las máquinas virtuales en la nube. Estos requisitos pusieron de manifiesto la necesidad de transferir y procesar los datos dentro de entornos de VNET protegidos y escalar horizontalmente la potencia de procesamiento a petición.

Como las canalizaciones de datos se han convertido en una parte fundamental de una estrategia de análisis de negocios, hemos observado que estas actividades requieren una programación flexible para admitir cargas de datos incrementales y ejecuciones desencadenadas por eventos. Por último, como las complejidades de las orquestaciones aumentan, también lo hacen los requisitos para que el servicio admita los paradigmas comunes de flujo de trabajo, como el procesamiento condicional, la bifurcación y el bucle.

Con la versión 2, también puede migrar paquetes existentes de SQL Server Integration Services (SSIS) a la nube para elevar y desplazar SSIS como un servicio de Azure administrado dentro de ADF utilizando una nueva característica de "Integration Runtimes" (IR). Al poner en marcha un IR de SSIS en la versión 2, tiene la capacidad de ejecutar, administrar, supervisar y generar paquetes SSIS en la nube.

### <a name="control-flow-and-scale"></a>Escala y flujo de control 
Para admitir los diferentes patrones y flujos de integración en el almacenamiento de datos actual, Data Factory ha habilitado un nuevo modelo de canalización de datos flexible que ya no está asociado con los datos de serie temporal. Con esta versión, puede modelar condicionales y la bifurcación en el flujo de control de una canalización de datos, así como pasar explícitamente los parámetros dentro y entre estos flujos.

Ahora tiene la libertad de modelar cualquier estilo de flujo necesario para la integración de datos y enviarlo a petición o varias veces en una programación horaria. A continuación, se indican algunos flujos comunes ya habilitados que anteriormente no eran posibles:   

- Flujo de control:
    - Encadenamiento de actividades en una secuencia dentro de una canalización
    - Bifurcación de actividades dentro de una canalización
    - parameters
        - Los parámetros se pueden definir en la canalización y los argumentos se pasan al invocar la canalización a petición o desde un desencadenador
        - Las actividades pueden consumir los argumentos pasados a la canalización
    - Paso a estado personalizado
        - Los resultados de la actividad, incluido el estado, los puede usar una actividad siguiente de la canalización
    - Containers de bucle
        - Para cada 
- Flujos basados en el desencadenador
    - Las canalizaciones se pueden activar a petición o por tiempo de reloj
- Flujos delta
    - Use parámetros y defina el nivel máximo de la copia delta al mover tablas de dimensiones o de referencia desde un almacén relacional de forma local o en la nube para cargar los datos en el lago 

Para obtener más información, consulte el siguiente [tutorial: flujo de control](tutorial-control-flow.md).

### <a name="deploy-ssis-packages-to-azure"></a>Implementación de paquetes SSIS en Azure 
Si desea mover las cargas de trabajo SSIS, puede crear una versión 2 de Data Factory y aprovisionar una instancia de SSIS Integration Runtime (IR) de Azure. SSIS IR de Azure es un clúster totalmente administrado de máquinas virtuales de Azure (nodos) que se dedica a ejecutar los paquetes SSIS en la nube. Para obtener instrucciones detalladas, vea el tutorial: [implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). 
 

### <a name="sdks"></a>SDK
Si es un usuario avanzado y busca una interfaz programática, la versión 2 le proporciona un amplio conjunto de SDK que se pueden usar para crear, administrar y supervisar canalizaciones mediante su IDE favorito.

- SDK de .NET: el SDK de .NET se actualiza para la versión 2. 
- PowerShell: los cmdlets de PowerShell se actualizan para la versión 2. La versión 2 de los cmdlets tienen **DataFactoryV2** en el nombre. Por ejemplo: Get-AzureRmDataFactoryV2. 
- SDK de Python: este SDK es una novedad de la versión 2.
- API de REST: la API de REST se actualiza para la versión 2.  

Los SDK que se actualizan para la versión 2 no son compatibles con los clientes de la versión 1. 

### <a name="monitoring"></a>Supervisión
Actualmente, la versión 2 admite la supervisión de factorías de datos usando solo SDK. El portal aún no es compatible con la supervisión de las factorías de datos de la versión 2. 

## <a name="what-is-integration-runtime"></a>¿Qué es Integration Runtime?
Integration Runtime (IR) es la infraestructura de proceso que usa Azure Data Factory para proporcionar las siguientes capacidades de integración de datos en distintos entornos de red:

- **Movimiento de datos**: mueva los datos entre almacenes de datos de redes públicas y de redes privadas (red privada local o virtual). Proporciona compatibilidad para conectores integrados, conversión de formato, asignación de columnas y transferencia de datos escalable y de rendimiento superior.
- **Distribución de actividades**: distribuya y supervise actividades de transformación que se ejecuten en una gran variedad de servicios de proceso, como Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server, etc.
- **Ejecución de paquetes SSIS**: ejecuta de forma nativa paquetes de SQL Server Integration Services (SSIS) en un entorno de proceso de Azure administrado.

Puede implementar una o varias instancias de Integration Runtime según sea necesario para mover y transformar los datos.  Integration Runtime se puede ejecutar en una red de Azure pública o en una red privada (local, Azure Virtual Network o en la nube privada virtual de Amazon Web Services (VPC)). 

Para más información, consulte [Integration Runtime en Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>¿Cuál es el límite del número de Integration Runtimes?
No hay ningún límite estricto acerca del número de instancias de Integration Runtimes que puede tener en una factoría de datos. Sin embargo, existe un límite acerca del número de núcleos de la máquina virtual (VM) que Integration Runtime puede usar por suscripción para la ejecución de paquetes SSIS. Para más información, consulte los [Límites de Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="when-to-use-version-2-rather-than-version-1"></a>¿Cuándo se debe utilizar la versión 2 en lugar de la versión 1? 
Si es nuevo en Azure Data Factory, inicie directamente con la versión 2. Si ya usa la versión 1, recompile las factorías de datos en la versión 2.

> [!WARNING]
> La versión 2 de Data Factory está en vista previa, no disponible con carácter general (GA). Por lo tanto, no entran en los mismos compromisos del contrato de nivel de servicio (SLA) de Azure que la versión 1 de Data Factory, que se encuentra en versión GA. 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>¿Cuáles son los conceptos de nivel superior de la versión 2?
Una suscripción de Azure puede tener una o varias instancias de Azure Data Factory (o factorías de datos). Azure Data Factory está compuesto por cuatro componentes principales que trabajan juntos para proporcionar una plataforma en la que pueda crear flujos de trabajo orientados a datos con pasos para moverlos y transformarlos.

### <a name="pipeline"></a>Canalización
Una factoría de datos puede tener una o más canalizaciones. La canalización es una agrupación lógica de actividades para realizar una unidad de trabajo. Juntas, las actividades de una canalización realizan una tarea. Por ejemplo, una canalización podría contener un grupo de actividades que ingiere datos de un blob de Azure y luego ejecutar una consulta de Hive en un clúster de HDInsight para particionar los datos. La ventaja de esto es que la canalización le permite administrar las actividades como un conjunto en lugar de tener que administrar cada una de ellas individualmente. Las actividades de una canalización se pueden encadenar juntas para operar de forma secuencial o pueden funcionar de forma independiente en paralelo.

### <a name="activity"></a>Actividad
Las actividades representan un paso del procesamiento en una canalización. Por ejemplo, puede utilizar una actividad de copia para copiar datos de un almacén de datos a otro. De igual forma, puede usar una actividad de Hive, que ejecuta una consulta de Hive en un clúster de Azure HDInsight para transformar o analizar los datos. Data Factory admite tres tipos de actividades: actividades de movimiento de datos, actividades de transformación de datos y actividades de control

### <a name="datasets"></a>CONJUNTOS DE DATOS
Los conjuntos de datos representan las estructuras de datos de los almacenes de datos que simplemente apuntan o hacen referencia a los datos que desea utilizar en sus actividades como entradas o salidas. 

### <a name="linked-services"></a>Servicios vinculados
Los servicios vinculados son muy similares a las cadenas de conexión que definen la información de conexión necesaria para que Data Factory se conecte a recursos externos. Considérelos de esta forma: un servicio vinculado define la conexión al origen de datos y un conjunto de datos representa la estructura de los datos. Por ejemplo, un servicio vinculado de Azure Storage especifica la cadena de conexión para conectarse a la cuenta de Azure Storage. Además, un conjunto de datos de Azure Blob especifica el contenedor de blobs y la carpeta que contiene los datos.

Los servicios vinculados se utilizan con dos fines en Data Factory:

- Para representar un **almacén de datos** que incluye, entre otros, una instancia de SQL Server local, una base de datos de Oracle, un recurso compartido de archivos o una cuenta de Azure Blob Storage. Para obtener una lista de almacenes de datos compatibles, consulte el artículo [actividad de copia](copy-activity-overview.md).
- Para representar un **recurso de proceso** que puede hospedar la ejecución de una actividad. Por ejemplo, la actividad HDInsightHive se ejecuta en un clúster de Hadoop para HDInsight. Consulte el artículo sobre [transformación de datos](transform-data.md) para ver una lista de los entornos de proceso y las actividades de transformación admitidos.

### <a name="triggers"></a>Desencadenadores
Los desencadenadores representan una unidad de procesamiento que determina cuándo es necesario poner en marcha una ejecución de canalización. Hay diferentes tipos de desencadenadores para distintos tipos de eventos. En el caso de la versión preliminar, admitimos el desencadenador de programador de reloj. 


### <a name="pipeline-runs"></a>Ejecuciones de la canalización
Una ejecución de la canalización es una instancia de la ejecución de la canalización. Normalmente, las instancias de ejecuciones de canalización se crean al pasar argumentos a parámetros definidos en las canalizaciones. Los argumentos se pueden pasar manualmente o dentro de la definición del desencadenador.

### <a name="parameters"></a>parameters
Los parámetros son pares clave-valor de configuración de solo lectura.  Los parámetros se definen en la canalización y los argumentos para los parámetros definidos se pasan durante la ejecución desde el contexto de ejecución creado por un desencadenador o una canalización que se ejecuta manualmente. Las actividades dentro de la canalización consumen los valores de parámetro.
Un conjunto de datos es un parámetro fuertemente tipado y una entidad reutilizable o a la que se puede hacer referencia. Una actividad puede hacer referencia a conjuntos de datos y puede consumir las propiedades definidas en la definición de conjunto de datos.

Un servicio vinculado también es un parámetro fuertemente tipado que contiene la información de conexión a un almacén de datos o en un entorno de proceso. También es una entidad reutilizable o a la que se puede hacer referencia.

### <a name="control-flow"></a>Flujo de control
Orquestación de actividades de canalización que incluye el encadenamiento de actividades en una secuencia, la bifurcación, y los parámetros que se pueden definir en el nivel de canalización y argumentos que se pasan al invocar la canalización a petición o desde un desencadenador. También incluye el paso a un estado personalizado y contenedores de bucle, es decir, los iteradores Para cada.


Para más información sobre los conceptos de Data Factory, consulte los siguientes artículos:

- [Dataset and linked services](concepts-datasets-linked-services.md) (Conjuntos de datos y servicios vinculados)
- [Canalizaciones y actividades](concepts-pipelines-activities.md)
- [Integration runtime](concepts-integration-runtime.md) (Tiempo de ejecución de integración)

## <a name="what-is-the-pricing-model-for-data-factory"></a>¿Qué es el modelo de precios de Data Factory?
Vea la página de [detalles de precios de Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) para obtener información al respecto.

## <a name="what-regions-support-azure-data-factory-version-2"></a>¿Qué regiones admiten la versión 2 de Azure Data Factory?
Actualmente, puede crear factorías de datos de la versión 2 en las regiones del este de EE. UU. y este de EE. UU. 2. Sin embargo, una factoría de datos puede usar Integration Runtime en otra región para mover datos entre almacenes de datos, distribuir las actividades según los servicios de proceso o enviar paquetes SSIS.  Para más información, consulte las [ubicaciones de Data Factory](concepts-integration-runtime.md#integration-runtime-location).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>¿Cómo puedo mantenerme actualizado con información acerca de Data Factory?
Utilice los siguientes sitios para mantenerse al día con información acerca de Azure Data Factory:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Página principal de la documentación](/azure/data-factory)
- [Página principal del producto](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Inmersión técnica profunda 

### <a name="can-i-have-version-1-and-version-2-pipelines-run-side-by-side"></a>¿Puede hacer que las canalizaciones de la versión 1 y de la versión 2 se ejecuten en paralelo?
No. Las factorías de datos de la versión 2 o de la versión 1 no pueden tener entidades (servicios vinculados, conjuntos de datos, canalizaciones, etc.) de otra versión.   

### <a name="do-i-still-need-to-define-datasets-in-version-2"></a>¿Aún tengo que definir los conjuntos de datos en la versión 2?
Los conjunto de datos ya no es una entidad obligatoria para la mayoría de las actividades. Es necesario para la copia, Machine Learning, la búsqueda, la validación y las actividades personalizadas que utilizan el esquema y otra información de metadatos del conjunto de datos para la transformación. El resto de las actividades ya no requieren los conjuntos de datos.

### <a name="can-i-chain-two-activities-without-a-dataset-in-version-2"></a>¿Puedo encadenar dos actividades sin un conjunto de datos en la versión 2?
Sí. Puede encadenar las actividades en la versión 2 sin necesidad de conjuntos de datos. Puede encadenar las actividades mediante la propiedad **dependsOn** en la definición de JSON de la canalización. 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>¿Todas las actividades de la versión 1 se admiten en la versión 2? 
Sí, se admiten todas las actividades de la versión 1

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>¿Cómo puedo programar una canalización de la versión 2? 
Puede usar el desencadenador de programador para programar una canalización de la versión 2. Se utiliza una programación del calendario del reloj y permite a los usuarios programar canalizaciones periódicamente o mediante patrones periódicos basados en calendarios (por ejemplo, semanalmente los lunes a las 6 p.m. y los jueves a las 9 p.m.). Para obtener más información, consulte [Desencadenadores y ejecución de la canalización](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>¿Puedo pasar parámetros a una canalización que se ejecuta en la versión 2?
Sí, los parámetros son conceptos de primera clase de nivel superior en la versión 2. Puede definir parámetros en el nivel de canalización y pasar argumentos al ejecutar la canalización a petición o mediante el uso de un desencadenador.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>¿Puedo definir valores predeterminados para los parámetros de la canalización? 
Sí. Puede definir valores predeterminados para los parámetros de las canalizaciones. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-passed-to-a-pipeline-run"></a>¿Una actividad de una canalización puede consumir los argumentos pasados a una ejecución de canalización? 
Sí. Cada actividad dentro de la canalización puede utilizar el valor del parámetro pasado a la canalización que se ejecute con la construcción de `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>¿Puede utilizarse una propiedad de salida de actividad en otra actividad? 
Sí. Un resultado de la actividad se puede utilizar en una actividad posterior con la construcción de @activity.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>¿Cómo puedo controlar correctamente los valores NULL en una salida de actividad? 
Puede usar la construcción de `@coalesce` en las expresiones para controlar correctamente los valores NULL. 

### <a name="can-i-use-retries-timeouts-at-the-activity-level-in-version-2"></a>¿Puedo usar reintentos o tiempos de expiración en el nivel de actividad en la versión 2?
Sí. Puede configurar los reintentos y el tiempo de expiración en el nivel de actividad para controlar la ejecución de actividades en la versión 2, al igual que en la versión 1. 

## <a name="next-steps"></a>Pasos siguientes
Para obtener instrucciones paso a paso para crear una factoría de datos de la versión 2, consulte los siguientes tutoriales:

- [Guía de inicio rápido:creación de una factoría de datos](quickstart-create-data-factory-dot-net.md)
- [Tutorial: copia de datos en la nube](tutorial-copy-data-dot-net.md)

