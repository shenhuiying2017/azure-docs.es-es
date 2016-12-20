---
title: "Creación de clústeres de Hadoop, HBase, Storm o Spark en Linux en HDInsight | Microsoft Docs"
description: "Aprenda a crear clústeres de Hadoop, HBase, Storm o Spark en Linux para HDInsight con un explorador web, la CLI de Azure, Azure PowerShell, REST o mediante un SDK."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 23a01938-3fe5-4e2e-8e8b-3368e1bbe2ca
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/18/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 8b9a3b853adcbdfd2a5d4113489e8fcad26d9f59


---
# <a name="create-linux-based-hadoop-clusters-in-hdinsight"></a>Creación de clústeres de Hadoop basados en Linux en HDInsight
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Un clúster de Hadoop se compone de varias máquinas virtuales (nodos) que se usan para el procesamiento distribuido de tareas en el clúster. Azure abstrae de los detalles de implementación de la instalación y configuración de los nodos individuales, por lo que solo tiene que proporcionar información de configuración general. En este artículo, obtendrá información sobre estas opciones de configuración.

## <a name="access-control-requirements"></a>Requisitos de control de acceso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="cluster-types"></a>Tipos de clúster
Actualmente, HDInsight de Azure proporciona cuatro tipos diferentes de clústeres, cada uno de ellos con un conjunto de componentes que ofrecen determinadas funcionalidades:

| Tipo de clúster | Funcionalidad |
| --- | --- |
| Hadoop |Consulta y análisis (trabajos por lotes) |
| HBase |Almacenamiento de datos NoSQL |
| Storm |Procesamiento de eventos en tiempo real |
| Spark |Procesamiento en memoria, consultas interactivas, procesamiento de transmisión de microlotes |
| [Interactive Hive (versión preliminar)](hdinsight-hadoop-use-interactive-hive.md) |Almacenamiento en la caché de memoria para realizar consultas de Hive interactivas y más rápidas |
| R Server en Spark (versión preliminar) |Diversas estadísticas de macrodatos, modelado de predicción y funcionalidades de aprendizaje automático. |

Cada tipo de clúster tiene dentro su propio número de nodos, la terminología de los nodos y el tamaño de máquina virtual predeterminado para cada tipo de nodo. En la siguiente tabla, el número de nodos de cada tipo de nodo se muestra entre paréntesis.

| Tipo | Nodos | Diagrama |
| --- | --- | --- |
| Hadoop |Nodo principal (2), nodo de datos (más de 1) |![Nodos de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png) |
| HBase |Servidor principal (2), servidor de región (más de 1), nodo maestro/Zookeeper (3) |![Nodos de clúster de HBase en HDInsight](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png) |
| Storm |Nodo Nimbus (2), servidor de supervisor (más de 1), nodo Zookeeper (3) |![Nodos de clúster de Storm en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png) |
| Spark |Nodo principal (2), nodo de trabajo (más de 1), nodo Zookeeper (3) (gratis para el tamaño de máquina virtual Zookeepers A1) |![Nodos de clúster de Spark en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png) |

En la tabla siguiente se indican los tamaños de máquina virtual predeterminados para HDInsight:

* Todas las regiones, excepto Sur de Brasil y Japón Occidental:
  
  | Tipo de clúster | Hadoop | HBase | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- |
  | Cabeza: tamaño de máquina virtual predeterminado |D3 v2 |D3 v2 |A3 |D12 v2 |D12 v2 |
  | Cabeza: tamaños de máquina virtual recomendados |D3 v2, D4 v2 y D12 v2 |D3 v2, D4 v2 y D12 v2 |A3, A4, A5 |D12 v2, v2 D13 y D14 v2 |D12 v2, v2 D13 y D14 v2 |
  | Trabajo: tamaño de máquina virtual predeterminado |D3 v2 |D3 v2 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | Trabajo: tamaños de máquina virtual recomendados |D3 v2, D4 v2 y D12 v2 |D3 v2, D4 v2 y D12 v2 |D3 v2, D4 v2 y D12 v2 |Windows: D12 v2, v2 D13 y D14 v2; Linux: D4 v2, D12 v2, v2 D13 y D14 v2 |Windows: D12 v2, v2 D13 y D14 v2; Linux: D4 v2, D12 v2, v2 D13 y D14 v2 |
  | Zookeeper: tamaño de máquina virtual predeterminado | |A3 |A2 | | |
  | Zookeeper: tamaños de máquina virtual recomendados | |A3, A4, A5 |A2, A3, A4 | | |
  | Perimetral: tamaño de máquina virtual predeterminado | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Perimetral: tamaño de máquina virtual recomendado | | | | |Windows: D12 v2, v2 D13 y D14 v2; Linux: D4 v2, D12 v2, v2 D13 y D14 v2 |
* Solo Sur de Brasil y Japón Occidental (ningún tamaño v2 aquí):
  
  | Tipo de clúster | Hadoop | HBase | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- |
  | Cabeza: tamaño de máquina virtual predeterminado |D3 |D3 |A3 |D12 |D12 |
  | Cabeza: tamaños de máquina virtual recomendados |D3, D4, D12 |D3, D4, D12 |A3, A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | Trabajo: tamaño de máquina virtual predeterminado |D3 |D3 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | Trabajo: tamaños de máquina virtual recomendados |D3, D4, D12 |D3, D4, D12 |D3, D4, D12 |Windows: D12, D13 y D14; Linux: D4, D12, D13 y D14 |Windows: D12, D13 y D14; Linux: D4, D12, D13 y D14 |
  | Zookeeper: tamaño de máquina virtual predeterminado | |A2 |A2 | | |
  | Zookeeper: tamaños de máquina virtual recomendados | |A2, A3, A4 |A2, A3, A4 | | |
  | Perimetral: tamaños de máquina virtual predeterminados | | | | |Windows: D12; Linux: D4 |
  | Perimetral: tamaños de máquina virtual recomendados | | | | |Windows: D12, D13 y D14; Linux: D4, D12, D13 y D14 |

Observe que Principal se conoce como *Nimbus* en el tipo de clúster de Storm. Trabajo se conoce como *Región* en el tipo de clúster de HBase y como *Supervisor* en el tipo de clúster de Storm.

> [!IMPORTANT]
> Si tiene pensado crear más de 32 nodos de trabajo, bien en el momento de creación del clúster o escalando el clúster tras la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
> 
> 

Puede agregar otros componentes, como Hue o R, a estos tipos básicos usando [acciones de script](#customize-clusters-using-script-action).

> [!IMPORTANT]
> Los clústeres de HDInsight incluyen diversos tipos, que corresponden a la carga de trabajo o la tecnología para los que el clúster está optimizado. No hay ningún método compatible para crear un clúster que combina varios tipos, como Storm y HBase en un clúster. 
> 
> 

Si la solución requiere tecnologías que están repartidas entre varios tipos de clúster de HDInsight, debe crear una red Azure Virtual Network y los tipos de clústeres necesarios en la red virtual. De este modo, los clústeres, y cualquier otro código que se implemente en ellos, pueden comunicarse directamente entre sí.

Para obtener más información sobre cómo usar Azure Virtual Network con HDInsight, consulte el artículo sobre cómo [extender las funcionalidades de HDInsight con Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

Para ver un ejemplo de cómo usar dos tipos de clúster en una red Azure Virtual Network, consulte [Análisis de datos de sensores con Apache Storm, Centro de eventos y HBase en HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md).

## <a name="cluster-tiers"></a>Niveles de clúster
HDInsight de Azure proporciona las ofertas de la nube de macrodatos en dos categorías: Estándar y [Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium). HDInsight Premium incluye R y otros componentes adicionales. HDInsight Premium solo se admite en la versión 3.4 de HDInsight.

En la tabla siguiente se recoge el tipo de clúster de HDInsight y la matriz de compatibilidad de HDInsight.

| Tipo de clúster | Estándar | Premium |
| --- | --- | --- |
| Hadoop |Sí |Sí |
| Spark |Sí |Sí |
| HBase |Sí |No |
| Storm |Sí |No |
| R Server en Spark |No |Sí |

Esta tabla se actualizará cuando se incluyan nuevos tipos de clúster en HDInsight Premium. La captura de pantalla siguiente muestra la información del Portal de Azure para elegir los tipos de clúster:

![Configuración de HDInsight Premium](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)

## <a name="basic-configuration-options"></a>Opciones de configuración básica
A continuación se presentan las opciones de configuración básicas para crear un clúster de HDInsight.

### <a name="cluster-name"></a>Nombre del clúster
El nombre del clúster se utiliza para identificar un clúster. El nombre de clúster debe ser único globalmente y debe seguir estas directrices de nomenclatura:

* El campo debe ser una cadena que contenga entre 3 y 63 caracteres.
* El campo solo puede contener letras, números y guiones.

### <a name="cluster-type"></a>Tipo de clúster
Consulte [Tipos de clúster](#cluster-types) y [Niveles de clúster](#cluster-tiers).

### <a name="operating-system"></a>Sistema operativo
Puede crear clústeres de HDInsight en uno de los dos sistemas operativos siguientes:

* HDInsight en Linux.  HDInsight proporciona la opción de configurar clústeres de Linux en Azure. Configure un clúster de Linux si conoce Linux o Unix, migrando desde una solución existente de Hadoop basado en Linux, o si desea una integración fácil con componentes del ecosistema de Hadoop creados para Linux. Para obtener más información, vea [Introducción a Hadoop en Linux en HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
* HDInsight en Windows (Windows Server 2012 R2 Datacenter).

### <a name="hdinsight-version"></a>Versión de HDInsight
Se utiliza para determinar la versión de HDInsight necesaria para este clúster. Para más información, consulte [Componentes y versiones de clústeres de Hadoop en HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409).

### <a name="subscription-name"></a>Nombre de la suscripción
Cada clúster de HDInsight está asociado a una suscripción a Azure.

### <a name="resource-group-name"></a>Definición de un nombre de grupo de recursos
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) lo ayuda a trabajar con los recursos de la aplicación como grupo, al que se conoce como "grupo de recursos de Azure". Puede implementar, actualizar, supervisar o eliminar todos los recursos de la aplicación en una operación única y coordinada.

### <a name="credentials"></a>Credenciales
Con los clústeres de HDInsight, puede configurar dos cuentas de usuario durante la creación del clúster:

* Usuario de HTTP. El nombre de usuario predeterminado es *admin* durante la configuración básica en Azure Portal. A veces, se denomina "Usuario de clúster".
* Usuario de SSH (clústeres de Linux). Se utiliza para conectarse al clúster mediante SSH. Puede crear más cuentas de usuario de SSH cuando cree el clúster siguiendo los pasos indicados en [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md) o [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-unix.md).
  
  > [!NOTE]
  > En los clústeres basados en Windows, puede crear un usuario RDP para conectarse al clúster mediante RDP.
  > 
  > 

### <a name="data-source"></a>Origen de datos
El sistema de archivos distribuido de Hadoop (HDFS) original usa muchos discos locales en el clúster. HDInsight usa el almacenamiento de blobs de Azure para el almacenamiento de datos. El almacenamiento de blobs de Azure es una solución de almacenamiento sólida y de uso general, que se integra sin problemas con HDInsight. Mediante una interfaz HDFS, el conjunto completo de componentes de HDInsight puede operar directamente en datos estructurados o no estructurados en Almacenamiento de blobs. Almacenar los datos en Almacenamiento de blobs ayuda a eliminar de forma segura los clústeres de HDInsight que se usan para el cálculo sin perder los datos del usuario.

Durante la configuración, debe especificar una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs de Azure en la cuenta de almacenamiento de Azure. Algunos de los procesos de creación requieren que la cuenta de almacenamiento de Azure y el contenedor de Almacenamiento de blobs se creen de antemano. El clúster utiliza el contenedor de almacenamiento de blobs como la ubicación de almacenamiento predeterminada. De manera opcional, puede especificar cuentas de almacenamiento de Azure adicionales (almacenamiento vinculado) al que podrá tener acceso el clúster. El clúster también puede tener acceso a cualquier contenedor de Almacenamiento de blobs que esté configurado con acceso de lectura público completo o con acceso de lectura público solo para blobs.  Para obtener más información, consulte [Administración del acceso a los recursos de Almacenamiento de Azure](../storage/storage-manage-access-to-resources.md).

![Almacenamiento de HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

> [!NOTE]
> Un contenedor de Almacenamiento de blobs proporciona una agrupación de un conjunto de blobs, tal como se muestra en la imagen:
> 
> 

![Almacenamiento de blobs de Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

No se recomienda usar el contenedor de Blob Storage predeterminado para almacenar datos empresariales. Conviene eliminar el contenedor de Almacenamiento de blobs predeterminado después de cada uso para reducir los costos de almacenamiento. Tenga en cuenta que el contenedor predeterminado contiene los registros del sistema y de la aplicación. Asegúrese de recuperar los registros antes de eliminar el contenedor.

> [!WARNING]
> No se permite compartir un contenedor de almacenamiento de blobs entre varios clústeres.
> 
> 

Para más información sobre el uso de Almacenamiento de blobs, consulte [Uso de Almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

Además del Almacenamiento de blobs de Azure, también puede usar el [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) como cuenta de almacenamiento predeterminada para el clúster de HBase en HDInsight y como almacenamiento vinculado para los cuatro tipos de clúster de HDInsight. Para más información, consulte [Creación de un clúster de HDInsight con Data Lake Store mediante el Portal de Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="location-region"></a>Ubicación (región)
El clúster de HDInsight y su cuenta de almacenamiento predeterminada deben colocarse en la misma ubicación de Azure.

![Regiones de Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

Si desea obtener una lista de las regiones admitidas, haga clic en la lista desplegable **Región** en [Precios de HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

### <a name="node-pricing-tiers"></a>Planes de tarifa de nodo
El uso de esos nodos se factura a los clientes por la duración del clúster. La facturación se inicia una vez creado el clúster y se detiene cuando se elimina el clúster. Los clústeres no se puede desasignar ni ponerse en espera.

Distintos tipos de clúster tienen distintos tipos de nodos, números de nodos y tamaños de nodo. Por ejemplo, un tipo de clúster de Hadoop tiene dos *nodos principales* y su valor predeterminado es de cuatro *nodos de datos*, mientras que un tipo de clúster Storm tiene dos *nodos nimbus*, tres *nodos zookeeper* y su valor predeterminado es de cuatro *nodos de supervisor*. El costo de los clústeres de HDInsight viene determinado por el número de nodos y por los tamaños de las máquinas virtuales de los nodos. Por ejemplo, si sabe que va a realizar las operaciones que necesitan una gran cantidad de memoria, es posible que quiera seleccionar un recurso de proceso con más memoria. Con fines de aprendizaje, se recomienda utilizar un nodo de datos. Para más información sobre los precios de HDInsight, vea [HDInsight Precios](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> El límite de tamaño del clúster varía entre las suscripciones a Azure. Póngase en contacto con el servicio de soporte relacionado con la facturación para aumentar el límite.
> 
> Los nodos usados por el clúster no se cuentan como máquinas virtuales, ya que las imágenes de máquina virtual que se usan para los nodos son un detalle de implementación del servicio HDInsight. Los núcleos de proceso utilizados por los nodos cuentan para el número total de núcleos de proceso disponibles para su suscripción. Puede ver el número de núcleos disponibles y los núcleos que se utilizarán en el clúster en la sección de resumen de la hoja Niveles de precios de nodo al crear un clúster de HDInsight.
> 
> 

Al usar el Portal de Azure para configurar el clúster, el tamaño del nodo está disponible en la hoja **Niveles de precios de nodo** . También puede ver el costo asociado con los diferentes tamaños de nodo. En la captura de pantalla siguiente se muestran las opciones para un clúster de Hadoop basado en Linux:

![Tamaños de nodo de máquina virtual de HDInsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

En las siguientes tablas se muestran los tamaños admitidos por los clústeres de HDInsight y las capacidades que ofrecen.

#### <a name="standard-tier-a-series"></a>Nivel estándar: serie A
En el modelo de implementación clásica, algunos tamaños de máquina virtual son ligeramente diferentes en Powershell y la CLI.

* Standard_A3 es Large
* Standard_A4 es ExtraLarge

| Tamaño | Núcleos de CPU | Memoria | NICs (Máx) | Tamaño máx. del disco | Máx. Discos de datos (1023 GB cada uno) | Máx. E/S (500 por disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A3\Large |4 |7 GB |2 |Temporal = 285 GB |8 |8x500 |
| Standard_A4\ExtraLarge |8 |14 GB |4 |Temporal = 605 GB |16 |16x500 |
| Standard_A6 |4 |28 GB |2 |Temporal = 285 GB |8 |8x500 |
| Standard_A7 |8 |56 GB |4 |Temporal = 605 GB |16 |16x500 |

#### <a name="standard-tier-d-series"></a>Nivel estándar: serie D
| Tamaño | Núcleos de CPU | Memoria | NICs (Máx) | Tamaño máx. del disco | Máx. Discos de datos (1023 GB cada uno) | Máx. E/S (500 por disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3 |4 |14 GB |4 |Temporal (SSD) =200 GB |8 |8x500 |
| Standard_D4 |8 |28 GB |8 |Temporal (SSD) =400 GB |16 |16x500 |
| Standard_D12 |4 |28 GB |4 |Temporal (SSD) =200 GB |8 |8x500 |
| Standard_D13 |8 |56 GB |8 |Temporal (SSD) =400 GB |16 |16x500 |
| Standard_D14 |16 |112 GB |8 |Temporal (SSD) =800 GB |32 |32x500 |

#### <a name="standard-tier-dv2-series"></a>Nivel estándar: serie Dv2
| Tamaño | Núcleos de CPU | Memoria | NICs (Máx) | Tamaño máx. del disco | Máx. Discos de datos (1023 GB cada uno) | Máx. E/S (500 por disco) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3_v2 |4 |14 GB |4 |Temporal (SSD) =200 GB |8 |8x500 |
| Standard_D4_v2 |8 |28 GB |8 |Temporal (SSD) =400 GB |16 |16x500 |
| Standard_D12_v2 |4 |28 GB |4 |Temporal (SSD) =200 GB |8 |8x500 |
| Standard_D13_v2 |8 |56 GB |8 |Temporal (SSD) =400 GB |16 |16x500 |
| Standard_D14_v2 |16 |112 GB |8 |Temporal (SSD) =800 GB |32 |32x500 |

Para conocer los aspectos que se deben tener en cuenta en la implementación al planear el uso de estos recursos, consulte [Tamaños de las máquinas virtuales](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para más información sobre los precios de los diferentes tamaños, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).   

> [!IMPORTANT]
> Si tiene pensado crear más de 32 nodos de trabajo, bien en el momento de creación del clúster o escalando el clúster tras la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
> 
> 

La facturación se inicia cuando se crea un clúster y solo se detiene cuando se elimina. Para obtener más información sobre los precios, consulte [Detalles de precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="use-additional-storage"></a>Uso de almacenamiento adicional
En algunos casos, es posible que desee agregar almacenamiento adicional al clúster. Por ejemplo, podría tener varias cuentas de almacenamiento de Azure para diferentes regiones geográficas, o para distintos servicios, pero querer analizarlas todas con HDInsight.

Puede agregar cuentas de almacenamiento al crear un clúster de HDInsight o después de haberlo creado.  Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

Para más información sobre el uso de Almacenamiento de blobs secundario, consulte [Uso de Almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obtener más información sobre el uso de almacenamiento de Data Lake secundario, consulte [Creación de clústeres de HDInsight con Data Lake Store con Azure Portal](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="use-hiveoozie-metastore"></a>Uso de la tienda de metadatos de Hive/Oozie
Se recomienda encarecidamente que utilice un Metastore personalizado si quiere conservar las tablas de Hive después de eliminar el clúster de HDInsight. Podrá adjuntar ese Metastore a otro clúster de HDInsight.

> [!IMPORTANT]
> La instancia de Metastore de HDInsight creada para una versión del clúster de HDInsight no se puede compartir entre diferentes versiones del clúster de HDInsight. Para obtener una lista de versiones de HDInsight, consulte [Versiones compatibles de HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).
> 
> 

La tienda de metadatos contiene metadatos de Hive y Oozie, como columnas, esquemas, particiones y tablas de Hive. El Metastore le ayuda a conservar sus metadatos de Hive y Oozie, por lo que no es necesario volver a crear tablas de Hive o trabajos de Oozie al crear un nuevo clúster. De forma predeterminada, Hive utiliza una base de datos SQL de Azure incrustada para almacenar esta información. La base de datos incrustada no puede conservar los metadatos cuando se elimina el clúster. Al crear tablas de Hive en un clúster de HDInsight con una instancia de Hive Metastore configurada, esas tablas se conservan al volver a crear el clúster con la misma instancia de Hive Metastore.

La configuración de la tienda de metadatos no está disponible para los tipos de clúster de HBase.

> [!IMPORTANT]
> Al crear un Metastore personalizado, no use un nombre de base de datos que contenga guiones. Podría provocar que el proceso de creación del clúster diera error.
> 
> 

## <a name="use-azure-virtual-networks"></a>Uso de redes virtuales de Azure
Una [red virtual de Azure](https://azure.microsoft.com/documentation/services/virtual-network/)permite crear una red segura y persistente que contenga los recursos que necesita para la solución. Con una red virtual, puede:

* Conectar recursos en la nube en una red privada (solo en la nube)
  
    ![Diagrama de la configuración solo en la nube](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)
* Conecte sus recursos en la nube a la red de su centro de datos local (sitio a sitio o punto a sitio) usando una red privada virtual (VPN).

| Configuración de sitio a sitio | Configuración de punto a sitio |
| --- | --- |
| La configuración sitio a sitio permite conectar varios recursos de su centro de datos a la red virtual de Azure usando una VPN de hardware o el servicio de enrutamiento y acceso remoto.<br />![Diagrama de la configuración de sitio a sitio](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) |La configuración punto a sitio permite conectar un recurso específico a la red virtual de Azure usando una VPN de software.<br />![Diagrama de la configuración de punto a sitio](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Los clústeres basados en Windows requieren una red virtual v1 (clásica), mientras que los clústeres basados en Linux requieren una red virtual v2 (Azure Resource Manager). Si no dispone del tipo correcto de red, no se podrá usar cuando cree el clúster.

Para más información sobre el uso de HDInsight con una red virtual, incluidos los requisitos de configuración específicos de la red virtual, consulte [Extensión de las funcionalidades de HDInsight con Red virtual de Azure](hdinsight-extend-hadoop-virtual-network.md).

## <a name="customize-clusters-using-hdinsight-cluster-customization-bootstrap"></a>Personalización de clústeres mediante la personalización de clústeres de HDInsight (bootstrap)
En ocasiones, querrá configurar los siguientes archivos de configuración:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Para mantener los cambios durante la vigencia de un clúster, puede usar la personalización de clústeres de HDInsight durante el proceso de creación, o también puede usar Ambari en clústeres basados en Linux. Para más información, consulte [Personalización de los clústeres de HDInsight con Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

> [!NOTE]
> Los clústeres basados en Windows no pueden conservar los cambios debido a la recreación de imágenes. Para obtener más información, consulte [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).  Para mantener los cambios durante la vida útil de los clústeres, debe usar la personalización de clústeres de HDInsight durante el proceso de creación.
> 
> 

## <a name="customize-clusters-using-script-action"></a>Personalización de clústeres mediante la acción de script
Puede instalar componentes adicionales o personalizar la configuración del clúster mediante el uso de scripts durante la creación. Tales scripts se invocan mediante la opción de **Acción de script**, una opción de configuración que se puede usar a partir de los cmdlets de Windows PowerShell de HDInsight, en el Portal de Azure o el SDK de .NET para HDInsight. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-linux.md).

Algunos de los componentes nativos de Java, como Mahout y Cascading, se pueden ejecutar en el clúster como archivos Java Archive (JAR). Estos archivos JAR se pueden distribuir al Almacenamiento de blobs de Azure y enviarse a los clústeres de HDInsight usando los mecanismos de envío de trabajo de Hadoop. Para obtener más información, consulte [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md).

> [!NOTE]
> Si tiene problemas con la implementación de los archivos JAR en los clústeres de HDInsight o con la llamada de los archivos JAR en los clústeres de HDInsight, póngase en contacto con [Soporte técnico de Microsoft](https://azure.microsoft.com/support/options/).
> 
> Cascading no se admite en HDInsight, y no puede optar a recibir soporte técnico de Microsoft. Para ver las listas de los componentes compatibles, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight](hdinsight-component-versioning.md)
> 
> 

## <a name="use-edge-node"></a>Uso de nodos perimetrales
 Un nodo perimetral vacío es una máquina virtual Linux con las mismas herramientas cliente instaladas y configuradas que en los nodos principales. Puede usar el nodo perimetral para acceder al clúster, y probar y hospedar sus aplicaciones cliente. Para obtener más información, consulte [Uso de nodos perimetrales vacíos en HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="cluster-creation-methods"></a>Métodos de creación de clústeres
En este artículo, aprendió información básica acerca de cómo crear un clúster de HDInsight basado en Linux. Use la siguiente tabla para buscar información específica sobre cómo crear un clúster mediante el método que mejor se adapte a sus necesidades.

| Clústeres creados con | Explorador web | Línea de comandos | API de REST | SDK | Linux, Mac OS X o Unix | Windows |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| [Portal de Azure](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |
| [Factoría de datos de Azure](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |✔ |✔ |
| [CLI de Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |✔ |✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |✔ |
| [Plantillas del Administrador de recursos de Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |




<!--HONumber=Nov16_HO3-->


