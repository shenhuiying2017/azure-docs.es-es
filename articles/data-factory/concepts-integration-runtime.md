---
title: Integration Runtime en Azure Data Factory | Microsoft Docs
description: "Obtenga más información sobre Integration Runtime en Azure Data Factory."
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
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 5285cddcb64e44cb61c99cc18f8a66d60cd1c0fc
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="integration-runtime-in-azure-data-factory"></a>Integration Runtime en Azure Data Factory
Integration Runtime (IR) es la infraestructura de proceso que usa Azure Data Factory para proporcionar las siguientes funcionalidades de integración de datos en distintos entornos de red:

- **Movimiento de datos**: mueva los datos entre almacenes de datos de redes públicas y de redes privadas (red privada local o virtual). Proporciona compatibilidad para conectores integrados, conversión de formato, asignación de columnas, y transferencia de datos de rendimiento superior y escalable.
- **Distribución de actividades**: distribuya y supervise actividades de transformación que se ejecuten en una gran variedad de servicios de proceso, como Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server, etc.
- **Ejecución de paquetes de SSIS**: ejecute de forma nativa paquetes de SQL Server Integration Services (SSIS) en un entorno de proceso de Azure administrado.


> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea la [documentación de Data Factory versión 1](v1/data-factory-introduction.md).

En Data Factory, una actividad define la acción que se realizará. Un servicio vinculado define un almacén de datos o un servicio de proceso de destino. Una instancia de Integration Runtime proporciona el puente entre la actividad y los servicios vinculados.  El servicio vinculado hace referencia a él, y este proporciona el entorno de proceso donde se ejecuta la actividad o desde donde se distribuye.  De esta manera, la actividad puede realizarse en la región más cercana posible al almacén de datos o servicio de proceso de destino de la manera con mayor rendimiento, a la vez que se satisfacen las necesidades de seguridad y cumplimiento.

## <a name="integration-runtime-types"></a>Tipos de instancias de Integration Runtime
Data Factory ofrece tres tipos de instancias de Integration Runtime, y debe elegir el tipo que atienda mejor las funcionalidades de integración de datos y las necesidades del entorno de red que está buscando.  Estos tres tipos son:

- Azure
- Autohospedado
- SSIS de Azure

En la tabla siguiente se describen las funcionalidades y la compatibilidad de red para cada uno de los tipos de instancias de Integration Runtime:

Tipo de IR | Red pública | Red privada
------- | -------------- | ---------------
Azure | Movimiento de datos<br/>Distribución de actividades | &nbsp;
Autohospedado | Movimiento de datos<br/>Distribución de actividades | Movimiento de datos<br/>Distribución de actividades
SSIS de Azure | Ejecución de paquetes SSIS | Ejecución de paquetes SSIS

En el diagrama siguiente se muestra cómo pueden utilizarse los distintos tipos de instancias de Integration Runtime en combinación para ofrecer funcionalidades de integración de datos enriquecidas y compatibilidad con redes:

![Diferentes tipos de instancias de Integration Runtime](media\concepts-integration-runtime\different-integration-runtimes.png)


## <a name="azure-integration-runtime"></a>Tiempo de ejecución de integración de Azure
Una instancia de Integration Runtime de Azure es capaz de:

- Ejecutar la actividad de copia entre los almacenes de datos en la nube
- Distribuir las siguientes actividades de transformación en la red pública: actividad Hive de HDInsight, actividad Pig de HDInsight, actividad MapReduce de HDInsight, actividad Spark de HDInsight, actividad Streaming de HDInsight, actividad de ejecución de lotes de Machine Learning, actividad de recurso de actualización de Machine Learning, actividad de procedimiento almacenado, actividad de U-SQL de Análisis de Data Lake, actividad personalizada de .Net, actividad Web, actividad de Búsqueda y actividad de Obtener metadatos.

### <a name="network-environment"></a>Entorno de red
Integration Runtime de Azure admite la conexión a almacenes de datos y servicios de proceso en la red pública con puntos de conexión de acceso público. Use Integration Runtime autohospedado para el entorno de Azure Virtual Network.

### <a name="compute-resource-and-scaling"></a>Recursos de proceso y escalado
Integration Runtime de Azure proporciona un proceso completamente administrado y sin servidor en Azure.  No tiene que preocuparse del aprovisionamiento de la infraestructura, la instalación de software, la aplicación de revisiones ni el escalado de la capacidad.  Además, solo se paga por la duración de la utilización real.

Integration Runtime de Azure proporciona el proceso nativo para mover datos entre almacenes de datos en la nube de forma segura, confiable y con alto rendimiento.  Puede establecer cuántas unidades de movimiento de datos se usarán en la actividad de copia, y el tamaño de proceso de Integration Runtime de Azure se escala verticalmente de manera elástica en consecuencia sin tener que ajustar de manera explícita el tamaño de Integration Runtime de Azure.

La distribución de actividades es una operación ligera para enrutar la actividad al servicio de proceso de destino, por lo que no hay necesidad de escalar verticalmente el tamaño de proceso para este escenario.

Para obtener información sobre cómo crear y configurar una instancia de Integration Runtime de Azure, consulte cómo crear y configurar una instancia de Integration Runtime de Azure en las guías de procedimientos. 

## <a name="self-hosted-integration-runtime"></a>Integration Runtime autohospedado
Una instancia de Integration Runtime autohospedado es capaz de:

- Ejecutar la actividad de copia entre los almacenes de datos en la nube y un almacén de datos en la red privada.
- Distribuir las siguientes actividades de transformación frente a los recursos de proceso en la red local o Azure Virtual Network: actividad Hive de HDInsight (BYOC), actividad Pig de HDInsight (BYOC), actividad MapReduce de HDInsight (BYOC), actividad Spark de HDInsight (BYOC), actividad Streaming de HDInsight (BYOC), actividad de ejecución de lotes de Machine Learning, actividades de recurso de actualización de Machine Learning, actividad de procedimiento almacenado, actividad de U-SQL de Análisis de Data Lake, actividad personalizada de .Net, actividad de Búsqueda y actividad de Obtener metadatos.

> [!NOTE] 
> Use Integration Runtime autohospedado para admitir almacenes de datos que requieran bring-your-own driver (BYOD), como SAP Hana, MySQL, etc.  Para obtener más información, consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats).

### <a name="network-environment"></a>Entorno de red
Si desea realizar la integración de datos de manera segura en un entorno de red privada, que no tiene una línea de vista directa desde el entorno de nube pública, puede instalar Integration Runtime autohospedado en el entorno local detrás del firewall corporativo, o dentro de un red privada virtual.  Integration Runtime autohospedado establece conexiones salientes basadas en HTTP para abrir Internet.

### <a name="compute-resource-and-scaling"></a>Recursos de proceso y escalado
Integration Runtime autohospedado debe instalarse en un equipo local o en una máquina virtual dentro de una red privada. Actualmente, solo se admite ejecutar instancias de Integration Runtime autohospedados en un sistema operativo Windows.  

Para una alta disponibilidad y escalabilidad, puede escalar horizontalmente Integration Runtime autohospedado mediante la asociación de la instancia lógica con varias máquinas locales en modo activo-activo.  Para más información, consulte el artículo sobre cómo crear y configurar un IR de orquestación autohospedado en las guías de procedimientos.

## <a name="azure-ssis-integration-runtime"></a>Integration Runtime de SSIS de Azure
Para levantar y mover la carga de trabajo de SSIS existente, puede crear una instancia de Integration Runtime de SSIS de Azure para ejecutar paquetes SSIS de forma nativa.

### <a name="network-environment"></a>Entorno de red
Integration Runtime de SSIS de Azure se puede aprovisionar en la red pública o la red privada.  El acceso a datos locales se admite mediante la combinación de Integration Runtime de SSIS de Azure a una red virtual (VNet) que está conectada a la red local.  

### <a name="compute-resource-and-scaling"></a>Recursos de proceso y escalado
Integration Runtime de SSIS de Azure es un clúster totalmente administrado de máquinas virtuales de Azure dedicado a ejecutar los paquetes de SSIS. Puede hacer que su propio servidor de Azure SQL Database o Instancia administrada (versión preliminar privada) hospede el catálogo de proyectos y paquetes de SSIS (SSISDB) que se va a adjuntar. Puede escalar verticalmente la eficacia de los procesos especificando el tamaño de nodo y escalar horizontalmente especificando el número de nodos del clúster. Puede administrar el coste de funcionamiento de Integration Runtime de SSIS de Azure deteniéndolo e iniciándolo según lo considere oportuno.

Para más información, consulte el artículo sobre cómo crear y configurar un IR de SSIS de Azure en las guías de procedimientos.  Una vez creado, puede implementar y administrar los paquetes de SSIS existentes con poco o ningún cambio utilizando herramientas conocidas, como SQL Server Data Tools (SSDT) y SQL Server Management Studio (SSMS), de manera similar a usar SSIS de forma local.

Consulte los siguientes artículos para más información sobre Integration Runtime de SSIS de Azure: 

- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-deploy-ssis-packages-azure.md). En este artículo se proporcionan instrucciones paso a paso para crear una instancia de IR de SSIS de Azure y se usa una base de datos de SQL Azure para hospedar el catálogo de SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Creación de una instancia de Integration Runtime de SSIS de Azure). Este artículo amplía el tutorial y proporciona instrucciones sobre el uso de la instancia administrada de Azure SQL (versión preliminar privada) y la unión de Integration Runtime a una red virtual. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar información sobre una instancia de IR de SSIS de Azure, junto con descripciones de los estados en la información devuelta. 
- [Administración de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md). En este artículo se muestra cómo detener, iniciar o quitar una instancia de IR de SSIS de Azure. También muestra cómo escalar horizontalmente la instancia de Integration Runtime de SSIS de Azure al agregar más nodos a Integration Runtime. 
- [Unión de una instancia de Integration Runtime de SSIS de Azure a una red virtual](join-azure-ssis-integration-runtime-virtual-network.md). En este artículo se proporciona información conceptual sobre cómo unir una instancia de Integration Runtime de SSIS de Azure a una red virtual de azure (VNet). También se proporcionan los pasos para configurar la red virtual mediante Azure Portal para que se una la instancia de Integration Runtime de SSIS de Azure. 

## <a name="determining-which-ir-to-use"></a>Determinar qué instancias de Integration Runtime usar
Cada actividad de transformación tiene un destino de servicio vinculado de proceso de destino, que apunta a una instancia de Integration Runtime. Esta instancia de Integration Runtime es desde donde se distribuye la actividad de transformación.

Para la actividad de copia, necesita servicios vinculados de origen y receptor para definir la dirección del flujo de datos. Se utiliza la lógica siguiente para determinar qué instancia de Integration Runtime se utiliza para realizar la copia: 

- **Copia entre dos orígenes de datos en la nube**: cuando los servicios vinculados tanto de origen como de receptor usan Integration Runtime de Azure, la instancia de Integration Runtime que usa el servicio vinculado de receptor se usa para ejecutar la actividad de copia.
- **Copia de datos entre un origen de datos en la nube y un origen de datos en una red privada**: si el servicio vinculado de origen o de receptor apunta a una instancia de Integration Runtime autohospedado, la actividad de copia se ejecuta en esa instancia de Integration Runtime autohospedado.
- **Copia entre dos orígenes de datos en una red privada**: el servicio vinculado de origen y de receptor deben apuntar a la misma instancia de Integration Runtime, y esa instancia de Integration Runtime se usa para ejecutar la actividad de copia.

En el siguiente diagrama se muestran dos ejemplos de actividades de copia:

- Para la actividad de copia 1, su origen es un servicio vinculado de SQL Server que hacen referencia a una instancia de Integration Runtime autohospedado A, y el receptor es un servicio vinculado de Azure Storage que hace referencia a una instancia de Integration Runtime de Azure B. Cuando se ejecuta la actividad de copia, se ejecuta en la instancia de Integration Runtime autohospedado A.
- Para la actividad de copia 2, su origen es un servicio vinculado de Azure SQL Database que hace referencia a una instancia de Integration Runtime de Azure C, y su receptor es un servicio vinculado de Azure Storage que hace referencia a la instancia de Integration Runtime de Azure B. Cuando se ejecuta la actividad de copia, se ejecuta en la instancia de Integration Runtime de Azure B, ya que es la instancia de Integration Runtime utilizada por el servicio vinculado de receptor.

![Qué instancia de Integration Runtime debe usarse](media/concepts-integration-runtime/which-integration-runtime-to-use.png)

## <a name="integration-runtime-location"></a>Ubicación de Integration Runtime
La ubicación de Data Factory es donde se almacenan los metadatos de la factoría de datos y desde donde se inicia el desencadenamiento de la canalización. Actualmente, las ubicaciones de Data Factory admitidas son: Este de EE. UU., Este de EE. UU. 2. y Europa Occidental. Sin embargo, una factoría de datos puede acceder a almacenes de datos y a servicios de proceso en otras regiones de Azure para mover datos entre los almacenes de datos o para procesar datos mediante servicios de proceso. Este comportamiento se lleva a cabo a través de la instancia de Integration Runtime disponible globalmente en varias regiones para garantizar el cumplimiento de los datos, la eficacia y los menores costes de salida de la red.

La ubicación de Integration Runtime define la ubicación de su proceso de back-end y, en esencia, la ubicación donde se realizan el movimiento de datos, la distribución de actividades y la ejecución de paquetes de SSIS. La ubicación de Integration Runtime puede ser diferente de la ubicación de la factoría de datos a la que pertenece. En el diagrama siguiente se muestra la configuración de la ubicación de Data Factory y sus instancias de Integration Runtime:

![Ubicación de Integration Runtime](media/concepts-integration-runtime/integration-runtime-location.png)

### <a name="azure-ir"></a>Integration Runtime de Azure
Data Factory utiliza una instancia de Integration Runtime de Azure en la región más cercana al receptor en la misma geografía para mover los datos. Consulte la tabla siguiente para la asignación:

Geografía del almacén de datos de receptores | Ubicación del almacén de datos de receptores | Ubicación utilizada para Integration Runtime de Azure
-------------------------------| ----------------| ------------------
Estados Unidos | Este de EE. UU | Este de EE. UU
&nbsp; | Este de EE. UU. 2 | Este de EE. UU. 2
&nbsp; | Central EE. UU: | Central EE. UU:
&nbsp; | Centro-Norte de EE. UU | Centro-Norte de EE. UU
&nbsp; | Centro-Sur de EE. UU | Centro-Sur de EE. UU
&nbsp; | Centro occidental de EE.UU. | Centro occidental de EE.UU.
&nbsp; | Oeste de EE. UU | Oeste de EE. UU
&nbsp; | Oeste de EE. UU. 2 | Oeste de EE. UU. 2
Canadá | Este de Canadá | Centro de Canadá
&nbsp; | Centro de Canadá | Centro de Canadá
Brasil | Sur de Brasil | Sur de Brasil
Europa | Europa del Norte | Europa del Norte
&nbsp; | Europa occidental | Europa occidental
Reino Unido | Oeste de Reino Unido | Sur del Reino Unido 2
&nbsp; | Sur del Reino Unido 2 | Sur del Reino Unido 2
Asia Pacífico | Sudeste asiático | Sudeste asiático
&nbsp; | Asia oriental | Sudeste asiático
Australia | Australia Oriental | Australia Oriental
&nbsp; | Sudeste de Australia | Sudeste de Australia
Japón | Este de Japón | Este de Japón
&nbsp; | Oeste de Japón | Este de Japón
Corea | Corea Central | Corea Central
&nbsp; | Corea del Sur | Corea Central
India | India Central | India Central
&nbsp; | Oeste de la India | India Central
&nbsp; | Sur de la India | India Central

También puede establecer la ubicación de Integration Runtime de Azure para que se resuelva automáticamente, lo que significa que Data Factory hace todo lo posible por detectar automáticamente la mejor ubicación para utilizar según la definición del servicio vinculado.

> [!NOTE] 
> Si la región del almacén de datos de destino no está en la lista o no se detecta, se producirá un error en la actividad, en lugar de pasar a una región alternativa, por motivos de cumplimiento. En este caso, indique explícitamente la ubicación alternativa que se utilizará para realizar la copia.
 
En la siguiente imagen se muestra un ejemplo de la ubicación eficaz cuando la ubicación de Integration Runtime de Azure está definida para resolverse automáticamente. Cuando se ejecuta una actividad de copia, detecta la ubicación de destino de los datos, en este ejemplo es Japan West (Japón Occidental).  Según la tabla, una instancia de Integration Runtime de Azure en Japan East (Japón Oriental) se usa para realizar la copia de datos real. Cuando se utilizan la misma instancia de Integration Runtime para conectarse a HDInsight para una actividad de Spark, se produce el envío de la aplicación de Spark desde la ubicación de Data Factory, en este ejemplo es East US (Este de EE. UU.), y la ejecución real de la aplicación de Spark se realiza en la ubicación del servidor de HDInsight. 

![Ubicación efectiva](media/concepts-integration-runtime/effective-location.png)

### <a name="self-hosted-ir"></a>Integration Runtime autohospedado
Integration Runtime autohospedado se registra lógicamente en Data Factory, y el usuario proporciona el proceso que se usa para admitir sus capacidades. Por lo tanto, no hay ninguna propiedad de ubicación explícita para la instancia de Integration Runtime autohospedado. 

Cuando se utiliza para realizar el movimiento de datos, Integration Runtime autohospedado extrae datos del origen y los escribe en el destino.

### <a name="azure-ssis-ir"></a>Integration Runtime de SSIS de Azure
Al seleccionar la ubicación adecuada para Integration Runtime de SSIS de Azure, es esencial lograr un alto rendimiento en los flujos de trabajo de extracción, transformación y carga (ETL).  Dos ubicaciones están disponibles inicialmente para la versión preliminar (Este de EE. UU. y Europa del Norte).

- La ubicación de Integration Runtime de SSIS de Azure no tiene que ser la misma que la ubicación de la factoría de datos, pero debe ser la misma que la ubicación de su propio servidor de la Instancia administrada de Azure SQL Database (versión preliminar privada) donde se hospedará la SSISDB. De esta manera, Integration Runtime de SSIS de Azure puede acceder fácilmente a SSISDB sin incurrir en un tráfico excesivo entre las distintas ubicaciones.
- Si no tiene un servidor existente de Instancia administrada de Azure SQL Database (versión preliminar privada) para hospedar la SSISDB, pero tiene orígenes y destinos de datos locales, debe crear un nuevo servidor de Instancia administrada de Azure SQL Database (versión preliminar privada) en la misma ubicación de una VNet conectada a la red local.  De esta manera, puede crear la instancia de Integration Runtime de SSIS de Azure usando el nuevo servidor de Instancia administrada de Azure SQL Database (versión preliminar privada) y unirse a esa VNet, todo en la misma ubicación, lo que minimiza de manera eficaz los movimientos de datos entre distintas ubicaciones.
- Si la ubicación de su servidor existente de Instancia administrada de Azure SQL Database (versión preliminar privada) donde se hospeda la SSISDB no es la misma que la ubicación de una VNet conectada a la red local, primero cree la instancia de Integration Runtime de SSIS de Azure con un servidor existente de Instancia administrada de Azure SQL Database (versión preliminar privada) y únase a otra VNet en la misma ubicación y, a continuación, configure una conexión de red virtual a red virtual entre distintas ubicaciones.


## <a name="next-steps"></a>pasos siguientes
Consulte los artículos siguientes:

- [Crear Integration Runtime autohospedado](create-self-hosted-integration-runtime.md)
- [Creación de una instancia de Integration Runtime de SSIS de Azure](create-azure-ssis-integration-runtime.md). Este artículo amplía el tutorial y proporciona instrucciones sobre el uso de la instancia administrada de Azure SQL (versión preliminar privada) y la unión de Integration Runtime a una red virtual. 
