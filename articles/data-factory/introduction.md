---
title: "Introducción a Azure Data Factory | Microsoft Docs"
description: "Sepa lo que es Azure Data Factory, un servicio de integración de datos basado en la nube que organiza y automatiza el movimiento y la transformación de datos."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 09e514aee503b7cb045c81d8ddcb855ced9b072b
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---
# <a name="introduction-to-azure-data-factory"></a>Introducción a la Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: disponibilidad general](v1/data-factory-introduction.md)
> * [Versión 2: versión preliminar](introduction.md)

En el mundo de los macrodatos, los datos sin procesar y desorganizados suelen almacenarse en sistemas de almacenamiento relacionales, no relacionales y de otros tipos. Sin embargo, en sí mismos, los datos sin procesar no tienen el contexto o el significado adecuados para proporcionar información significativa a los analistas, científicos de datos y responsables de decisiones empresariales. Lo que se necesita es un servicio que pueda habilitar la organización y la puesta en marcha de procesos para convertir estos enormes almacenes de datos sin procesar en información empresarial accionable. Azure Data Factory es un servicio en la nube administrado creado para estos complejos proyectos híbridos de extracción, carga y transformación (ETL), de extracción, carga y transformación (ELT) y de integración de datos.

Por ejemplo, imagine una empresa de juegos que recopila petabytes de registros de juegos generados por juegos en la nube. Quiere analizar estos registros para obtener información sobre las preferencias de los clientes, la demografía y el comportamiento de uso a fin de identificar oportunidades de venta cruzada e incremento de ventas, desarrollar nuevas y atractivas características, impulsar el crecimiento del negocio y proporcionar una experiencia mejor a sus clientes.

Para analizar estos registros, la empresa debe usar datos de referencia, como información de clientes, información de juegos e información de campañas de marketing, que se encuentran en un almacén de datos local. La empresa desea utilizar estos datos del almacén de datos local y combinarlos con los datos de registro adicionales que tiene en un almacén de datos en la nube. Para extraer información, espera procesar los datos combinados mediante un clúster de Spark en la nube (HDInsight) y publicar luego los datos transformados en un almacenamiento de datos en la nube como Azure SQL Data Warehouse para generar fácilmente un informe sobre él. Este flujo de trabajo se automatiza, supervisa y administra en una programación diaria, y se ejecuta cuando los archivos terminan en un contenedor de almacenamiento de blobs.

Azure Data Factory es la plataforma que resuelve estos escenarios de datos. Se trata de un **servicio de integración de datos basado en la nube que le permite crear flujos de trabajo orientados a datos en la nube a fin de coordinar y automatizar el movimiento y la transformación de datos**. Mediante Azure Data Factory, puede crear y programar flujos de trabajo orientados a datos (llamados canalizaciones) que pueden ingerir datos de almacenes de datos dispares, procesar o transformar los datos mediante servicios de proceso, como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics y Azure Machine Learning, y publicar datos de salida en almacenes de datos, como Azure SQL Data Warehouse para que los consuman las aplicaciones de inteligencia empresarial (BI). En última instancia, mediante Azure Data Factory, los datos sin procesar se pueden organizar en almacenes de datos y Data Lakes significativos para tomar mejores decisiones empresariales.

![Vista de nivel superior de Data Factory](media/introduction/big-picture.png)

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Introduction to Data Factory version 1](v1/data-factory-introduction.md) (Introducción a Data Factory versión 1).

## <a name="how-does-it-work"></a>¿Cómo funciona?
Las canalizaciones (flujos de trabajo orientados a datos) en Azure Data Factory realizan normalmente los cuatro pasos siguientes:

![Cuatro pasos de un flujo de trabajo controlado por datos](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>Conectar y recopilar

Las empresas tienen datos de varios tipos que se encuentran en orígenes locales dispares, en la nube, estructurados, no estructurados y semiestructurados, que llegan todos a distintos intervalos y velocidades. El primer paso en la creación de un sistema de producción de información es conectarse a todos los orígenes necesarios de datos y procesamiento, como servicios de software como servicio (SaaS), bases de datos, recursos web compartidos de archivos, servicios web FTP y mover los datos según sea necesario a una ubicación centralizada para su posterior procesamiento.

Sin Data Factory, las empresas deben crear componentes de movimiento de datos personalizados o escribir servicios personalizados para integrar estos orígenes de datos y procesamientos. Tales sistemas, además de costosos, resultan difíciles de integrar y administrar, y a menudo carecen de las funcionalidades de supervisión y alerta de clase empresarial y de los controles que un servicio completamente administrado puede ofrecer.

Con Data Factory, puede usar la [actividad de copia](copy-activity-overview.md) en una canalización de datos para mover los datos desde almacenes de datos de origen locales y en la nube a un almacén de datos de centralización en la nube para su posterior análisis. Por ejemplo, puede recopilar datos de una instancia de Azure Data Lake Store y transformarlos posteriormente mediante un servicio de proceso de Azure Data Lake Analytics. O también puede recopilar los datos en Azure Blob Storage y transformarlos más adelante mediante el uso de un clúster de Hadoop de Azure HDInsight.

### <a name="transform-and-enrich"></a>Transformar y enriquecer
Una vez que los datos están presentes en un almacén de datos centralizado en la nube, querrá que los datos recopilados se procesen o transformen mediante servicios de proceso, como HDInsight Hadoop, Spark, Data Lake Analytics y Machine Learning. Querrá generar de manera confiable datos transformados según una programación controlada y fácil de mantener a fin de alimentar los entornos de producción con datos de confianza.

### <a name="publish"></a>Publicar
Ahora que se han perfeccionado los datos sin procesar en un formato compatible listo para los negocios, cargue los datos en Almacenamiento de datos de Azure, Azure SQL DB, Azure CosmosDB o los análisis del motor a los que los usuarios de su empresa pueden apuntar a partir de sus herramientas de inteligencia empresarial.

### <a name="monitor"></a>Supervisión
Una vez creada e implementada correctamente la canalización de integración de datos, que proporciona un valor empresarial desde datos refinados, desea supervisar las actividades programadas y las canalizaciones para ver las tasas de éxito y de error. Azure Data Factory tiene compatibilidad integrada para la supervisión de la canalización mediante Azure Monitor, API, PowerShell, OMS y paneles de mantenimiento en Azure Portal.

## <a name="whats-different-in-version-2"></a>Diferencias en la versión 2
La versión 2 de Azure Data Factory se basa en el servicio de transformación y movimiento de datos original de Azure Data Factory, que se extiende a un conjunto más amplio de escenarios de integración de datos en la nube en primer lugar. Azure Data Factory V2 ofrece las siguientes funcionalidades:

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
    - Contenedores de bucle
        - Para cada 
- Flujos basados en el desencadenador
    - Las canalizaciones se pueden activar a petición o por tiempo de reloj
- Flujos delta
    - Use parámetros y defina el nivel máximo de la copia delta al mover tablas de dimensiones o de referencia desde un almacén relacional de forma local o en la nube para cargar los datos en el lago 

Para obtener más información, consulte el siguiente [tutorial: flujo de control](tutorial-control-flow.md).

### <a name="deploy-ssis-packages-to-azure"></a>Implementación de paquetes SSIS en Azure 
Si desea mover las cargas de trabajo SSIS, puede crear una versión 2 de Data Factory y aprovisionar una instancia de SSIS Integration Runtime (IR) de Azure. SSIS IR de Azure es un clúster totalmente administrado de máquinas virtuales de Azure (nodos) que se dedica a ejecutar los paquetes SSIS en la nube. Para obtener instrucciones detalladas, vea el tutorial: [implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). 
 

## <a name="rich-cross-platform-sdks"></a>SDK de compatibilidad multiplataforma enriquecidos
Si es un usuario avanzado y busca una interfaz programática, la versión 2 le proporciona un amplio conjunto de SDK que se pueden usar para crear, administrar y supervisar canalizaciones mediante su IDE favorito.

- .NET SDK
- PowerShell
- SDK de Python

También puede utilizar las API de REST para crear factorías de datos. 

## <a name="load-the-data-into-a-lake"></a>Carga de los datos en un lago
Data Factory sigue teniendo más de 30 conectores para que pueda cargar datos desde entornos heterogéneos e híbridos a Azure.  Consulte [Guía de rendimiento y ajuste](copy-activity-performance.md) para ver los últimos resultados de rendimiento de las pruebas internas y las sugerencias de ajuste. Además, recientemente hemos habilitado la alta disponibilidad y escalabilidad para una instancia de Integration Runtime autohospedada que se instala en un entorno de red privado a fin de resolver los requisitos de cliente de grandes empresas de nivel 1 para una mejor disponibilidad y escalabilidad.

## <a name="top-level-concepts-in-version-2"></a>Conceptos de nivel superior en la versión 2
Una suscripción de Azure puede tener una o varias instancias de Azure Data Factory (o factorías de datos). Azure Data Factory está compuesto por cuatro componentes principales que trabajan juntos para proporcionar una plataforma en la que pueda crear flujos de trabajo orientados a datos con pasos para moverlos y transformarlos.

### <a name="pipeline"></a>Canalización
Una factoría de datos puede tener una o más canalizaciones. La canalización es una agrupación lógica de actividades para realizar una unidad de trabajo. Juntas, las actividades de una canalización realizan una tarea. Por ejemplo, una canalización podría contener un grupo de actividades que ingiere datos de un blob de Azure y luego ejecutar una consulta de Hive en un clúster de HDInsight para particionar los datos. La ventaja de esto es que la canalización le permite administrar las actividades como un conjunto en lugar de tener que administrar cada una de ellas individualmente. Las actividades de una canalización se pueden encadenar juntas para operar de forma secuencial o pueden funcionar de forma independiente en paralelo.

### <a name="activity"></a>Actividad
Las actividades representan un paso del procesamiento en una canalización. Por ejemplo, puede utilizar una actividad de copia para copiar datos de un almacén de datos a otro. De igual forma, puede usar una actividad de Hive, que ejecuta una consulta de Hive en un clúster de Azure HDInsight para transformar o analizar los datos. Data Factory admite tres tipos de actividades: actividades de movimiento de datos, actividades de transformación de datos y actividades de control.

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

## <a name="supported-regions"></a>Regiones admitidas:

Actualmente, puede crear factorías de datos en las regiones del este de EE. UU. y este de EE. UU. 2. Sin embargo, una factoría de datos puede acceder a almacenes de datos y a servicios de proceso en otras regiones de Azure para mover datos entre los almacenes de datos o para procesar datos mediante servicios de proceso.

Azure Data Factory no almacena ningún dato. Permite crear flujos de trabajo controlados por datos para orquestar el movimiento de los datos entre los almacenes de datos admitidos y organizar el procesamiento de los datos mediante servicios de proceso en otras regiones o en entornos locales. También permite supervisar y administrar flujos de trabajo mediante mecanismos de programación y de interfaz de usuario.

Aunque Data Factory solamente está disponible en las regiones del este de EE. UU. y este de EE. UU. 2, el servicio que atiende el movimiento de datos en Data Factory está disponible globalmente en varias regiones. Si un almacén de datos se encuentra detrás de un firewall, será una instancia de Data Management Gateway instalada en el entorno local la que mueva los datos en su lugar.

Por ejemplo, supongamos que sus entornos de proceso, tales como clúster de Azure HDInsight y Azure Machine Learning, se ejecutan fuera de la región de Europa Occidental. Puede crear y usar una instancia de Azure Data Factory en Europa del Norte y usarla para programar trabajos en los entornos de proceso en Europa Occidental. Data Factory tarda unos milisegundos en desencadenar el trabajo en su entorno de proceso, pero el tiempo para ejecutar el trabajo en el entorno de proceso no cambia.

## <a name="next-steps"></a>Pasos siguientes
Para saber cómo crear una factoría de datos, siga las instrucciones paso a paso en los inicios rápidos siguientes: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [API de REST](quickstart-create-data-factory-rest-api.md) y Azure Portal. 

