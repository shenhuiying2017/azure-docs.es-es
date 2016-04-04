<properties
   pageTitle="Creación de clústeres de Hadoop basados en Windows en HDInsight | Microsoft Azure"
   	description="Aprenda a crear clústeres para HDInsight de Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/21/2016"
   ms.author="jgao"/>

# Creación de clústeres de Hadoop basados en Windows en HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Un clúster de Hadoop se compone de varias máquinas virtuales (nodos), que se usan para el procesamiento distribuido de tareas en el clúster. Azure abstrae de los detalles de implementación de la instalación y configuración de los nodos individuales, por lo que solo tiene que proporcionar información de configuración general. En este artículo, obtendrá información sobre estas opciones de configuración.

>[AZURE.NOTE] La información contenida en este documento es específica de los clústeres de HDInsight basados en Windows. Para obtener información sobre los clústeres basados en Linux, consulte [Creación de clústeres de Hadoop basado en Windows en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

##Tipos de clúster

Actualmente, HDInsight proporciona cuatro tipos diferentes de clústeres, cada uno de ellos con un conjunto de componentes para proporcionar ciertas funcionalidades:

| Tipo de clúster | Úselo si necesita... |
| ------------ | ----------------------------- |
| Hadoop | consultas y análisis (trabajos por lotes) |
| HBase | Almacenamiento de datos NoSQL |
| Storm | Procesamiento de eventos en tiempo real |
| Spark (vista preliminar) | Procesamiento en memoria, consultas interactivas, procesamiento de transmisión de microlotes |

Cada tipo de clúster tiene su propia terminología de nodos dentro del clúster, así como el número de nodos y el tamaño de memoria virtual predeterminada para cada tipo de nodo:

| Tipo| Nodos (número de nodos)| Diagrama|
|-----|------|--------|
|Hadoop| Nodo principal (2), nodo de datos (más de 1)|![Nodos de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)|
|HBase|Servidor principal (2), servidor de región (más de 1), nodo maestro/Zookeeper (3)|![Nodos de clúster de HBase en HDInsight](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)|
|Storm|Nodo Nimbus (2), servidor de supervisor (más de 1), nodo Zookeeper (3)|![Nodos de clúster de Storm en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)|
|Spark|Nodo principal (2), nodo de trabajo (más de 1), nodo Zookeeper (3) (gratis para el tamaño de máquina virtual Zookeepers A1)|![Nodos de clúster de Spark en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)|

* Entre paréntesis se indica el número de nodos de cada tipo de nodo.

> [AZURE.IMPORTANT] Si planea crear más de 32 nodos de trabajo, en la creación de clústeres o cambiando el tamaño del clúster después de la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.

Puede agregar otros componentes, como Hue o R, a estos tipos básicos mediante el uso de [acciones de script](#customize-clusters-using-script-action).

## Opciones de configuración básica

A continuación se presentan las opciones de configuración básicas para crear un clúster de HDInsight.

- **Nombre del clúster**

	El nombre del clúster se utiliza para identificar un clúster. El nombre de clúster debe ser único globalmente y debe seguir estas directrices de nomenclatura:

	- El campo debe ser una cadena que contenga entre 3 y 63 caracteres
	- El campo solo puede contener letras, números y guiones.

- **Tipo de clúster**

    Vea [Tipos de clúster](#cluster-types).

- **Sistema operativos**

	Puede crear clústeres de HDInsight en uno de los dos sistemas operativos siguientes:
	- **HDInsight en Linux (Ubuntu 12.04 LTS para Linux)**: HDInsight proporciona la opción de configurar clústeres de Linux en Azure. Configure un clúster de Linux si conoce Linux o Unix, migrando desde una solución existente de Hadoop basado en Linux, o si desea una integración fácil con componentes del ecosistema de Hadoop creados para Linux. Para obtener más información, vea [Introducción a Hadoop en Linux en HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
	- **HDInsight en Windows (Windows Server 2012 R2 Datacenter)**:
    
- **Versión de HDInsight**

	Se utiliza para determinar la versión de HDInsight que se utilizará para este clúster. Para obtener más información, consulte [Componentes y versiones de clústeres de Hadoop en HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

- **Nombre de la suscripción**

	Cada clúster de HDInsight está asociado a una suscripción a Azure.
    
- **Definición de un nombre de grupo de recursos**

	[Azure Resource Manager (ARM)](resource-group-overview.md) permite trabajar con los recursos de la aplicación como un grupo, al que se hace referencia como Grupo de recursos de Azure. Puede implementar, actualizar, supervisar o eliminar todos los recursos de la aplicación en una operación única y coordinada.

- **Credenciales**

	Los clústeres de HDInsight le permiten configurar dos cuentas de usuario durante la creación del clúster:

	- Usuario de HTTP. El nombre de usuario predeterminado es admin durante la configuración básica en el Portal de Azure. A veces, se denomina "Usuario de clúster".
	- Usuario de RDP (clústeres de Windows): se usa para conectarse al clúster mediante RDP. Cuando crea la cuenta, debe establecer una fecha de caducidad que se encuentre dentro de 90 días a contar del día de hoy.
	- Usuario SSH (clústeres de Linux): se usa para conectarse al clúster mediante SSH. Puede crear cuentas de usuario SSH adicional una vez que se cree el clúster siguiendo los pasos en [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

- **Origen de datos**

	El HDFS original usa muchos discos locales en el clúster. En su lugar, HDInsight utiliza el almacenamiento de blobs de Azure para el almacenamiento de datos. El almacenamiento de blobs de Azure es una solución de almacenamiento sólida y de uso general, que se integra sin problemas con HDInsight. A través de una interfaz del sistema de archivos distribuidos de Hadoop (HDFS), el conjunto completo de componentes de HDInsight puede operar directamente en datos estructurados o no estructurados en el almacenamiento de blobs. Almacenar los datos en un almacenamiento de blobs hace que elimine de forma segura los clústeres de HDInsight que se usan para los cálculos sin perder los datos del usuario.

	Durante la configuración, debe especificar una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs de Azure en la cuenta de almacenamiento de Azure. Algunos de los procesos de creación requieren que la cuenta de almacenamiento de Azure y el contenedor de almacenamiento de blobs se creen con antelación. El clúster utiliza el contenedor de almacenamiento de blobs como la ubicación de almacenamiento predeterminada. De manera opcional, puede especificar cuentas de almacenamiento de Azure adicionales (almacenamiento vinculado) al que podrá tener acceso el clúster. Además, el clúster también podrá tener acceso a cualquier contenedor de blobs que esté configurado con acceso de lectura público completo o con acceso de lectura público solo para blobs. Para obtener más información sobre el acceso restringido, consulte [Administración del acceso a los recursos de almacenamiento de Azure](storage-manage-access-to-resources.md).

	![Almacenamiento de HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE] Un contenedor de almacenamiento de blobs proporciona una agrupación de un conjunto de blobs, tal como se muestra en la imagen:

	![Almacenamiento de blobs de Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

    No se recomienda usar el contenedor de blobs predeterminado para almacenar datos empresariales. Se recomienda eliminar el contenedor de blobs predeterminado después de cada uso para reducir los costos de almacenamiento. Tenga en cuenta que el contenedor predeterminado contiene los registros del sistema y de la aplicación. Asegúrese de recuperar los registros antes de eliminar el contenedor.
    
	>[AZURE.WARNING] No se permite compartir un contenedor de almacenamiento de blobs entre varios clústeres.

	Para obtener más información sobre el uso de almacenes de blobs secundarios, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

    Además del almacenamiento de blobs de Azure, también puede usar [Almacén de Azure Data Lake](data-lake-store-overview.md) como cuenta de almacenamiento predeterminada para el clúster de HBase en HDInsight y como almacenamiento vinculado para los cuatro tipos de clúster de HDInsight. Consulte las instrucciones en [Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
    
- **Ubicación (región)**

	El clúster de HDInsight y su cuenta de almacenamiento predeterminada deben colocarse en la misma ubicación de Azure.
	
	![Regiones de Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

	Si desea obtener una lista de las regiones admitidas, haga clic en la lista desplegable **Región** en [Precios de HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Planes de tarifa de nodo**

    El uso de esos nodos se factura a los clientes por la duración del clúster. La facturación comienza una vez que se crea un clúster y se detiene cuando se elimina el clúster (no es posible eliminar la asignación de los clústeres ni tampoco se pueden poner en espera)

	Los distintos tipos de clúster tienen tipos de nodos, número de nodos y tamaños de nodo diferentes. Por ejemplo, un tipo de clúster de Hadoop tiene dos _nodos principales_ y su valor predeterminado es de cuatro _nodos de datos_, mientras que un tipo de clúster Storm tiene dos _nodos nimbus_, tres _nodos zookeeper_ y su valor predeterminado es de cuatro _nodos de supervisor_. El costo de los clústeres de HDInsight viene determinado por el número de nodos y por los tamaños de las máquinas virtuales para los nodos. Por ejemplo, si sabe que va a realizar las operaciones que necesitan una gran cantidad de memoria, es posible que quiera seleccionar un recurso de proceso con más memoria. Con fines de aprendizaje, se recomienda utilizar 1 nodo de datos. Para más información sobre los precios de HDInsight, vea [HDInsight Precios](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

	>[AZURE.NOTE] El límite de tamaño del clúster varía entre las suscripciones a Azure. Póngase en contacto con el servicio de soporte relacionado con la facturación para aumentar el límite.
	
    >Los nodos de clúster utilizados por el clúster no se consideran máquinas virtuales, ya que las imágenes de máquinas virtuales que se usan para los nodos son un detalle de implementación del servicio de HDInsight. Sin embargo, los núcleos de proceso utilizados por los nodos sí cuentan para el número total de núcleos de proceso disponibles para su suscripción. Puede ver el número de núcleos que se utilizarán en el clúster, así como el número de núcleos disponibles, en la sección de resumen de la hoja Plan de tarifa de nodos al crear un clúster de HDInsight.

	Si usa el Portal de Azure para configurar el clúster, el tamaño del nodo estará disponible en la hoja __Plan de tarifa de nodo__, y aparecerá también el costo asociado con los distintos tamaños de nodo. La captura de pantalla siguiente muestra las opciones para un clúster de Hadoop basado en Linux:

	![tamaños de nodos de vm de hdinsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

    Las siguientes tablas muestran los tamaños admitidos por los clústeres de HDInsight y las capacidades que ofrecen.

    - Nivel estándar: serie A

        En el modelo de implementación clásica, algunos tamaños de máquina virtual son ligeramente diferentes en Powershell y la CLI.

        * Standard\_A3 es Large
        * Standard\_A4 es ExtraLarge

        <br>

        |Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (500 por disco)|
        |---|---|---|---|---|---|---|
        |Standard\_A3\\Large|4|7 GB|2|Temporal = 285 GB |8|8x500|
        |Standard\_A4\\ExtraLarge|8|14 GB|4|Temporal = 605 GB |16|16x500|
        |Standard\_A6|4|28 GB|2|Temporal = 285 GB |8|8x500|
        |Standard\_A7|8|56 GB|4|Temporal = 605 GB |16|16x500|


    - Nivel estándar: serie D

        |Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (500 por disco)|
        |---|---|---|---|---|---|---|
        |Standard\_D3 |4|14 GB|4|Temporal (SSD) =200 GB |8|8x500|
        |Standard\_D4 |8|28 GB|8|Temporal (SSD) =400 GB |16|16x500|
        |Standard\_D12 |4|28 GB|4|Temporal (SSD) =200 GB |8|8x500|
        |Standard\_D13 |8|56 GB|8|Temporal (SSD) =400 GB |16|16x500|
        |Standard\_D14 |16|112 GB|8|Temporal (SSD) =800 GB |32|32x500|

    - Nivel estándar: serie Dv2

        |Tamaño |Núcleos de CPU|Memoria|NICs (Máx)|Tamaño máx. del disco|Discos máximos de datos (1023 GB cada uno)|Máx. E/S (500 por disco)|
        |---|---|---|---|---|---|---|
        |Standard\_D3\_v2 |4|14 GB|4|Temporal (SSD) =200 GB |8|8x500|
        |Standard\_D4\_v2 |8|28 GB|8|Temporal (SSD) =400 GB |16|16x500|
        |Standard\_D12\_v2 |4|28 GB|4|Temporal (SSD) =200 GB |8|8x500|
        |Standard\_D13\_v2 |8|56 GB|8|Temporal (SSD) =400 GB |16|16x500|
        |Standard\_D14\_v2 |16|112 GB|8|Temporal (SSD) =800 GB |32|32x500|    
 
    Para conocer las consideraciones de implementación que hay que tener en cuenta siempre que planee usar estos recursos, vea [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-size-specs.md). Para obtener información sobre los precios de los diferentes tamaños, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).
    
	> [AZURE.IMPORTANT] Si planea crear más de 32 nodos de trabajo, en la creación de clústeres o al cambiar el tamaño del clúster después de la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM. La facturación se inicia una vez creado el clúster y solo se detiene cuando se elimina el clúster. Para obtener más información sobre los precios, consulte [Detalles de precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


## Uso de almacenamiento adicional

En algunos casos, es posible que desee agregar almacenamiento adicional al clúster. Por ejemplo, dispone de varias cuentas de almacenamiento de Azure para diferentes regiones geográficas, o para distintos servicios, pero desea analizarlas con HDInsight.

Para obtener más información sobre el uso de almacenes de blobs secundarios, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obtener más información sobre el uso de almacenes de Data Lake secundarios, consulte [Creación de clústeres de HDInsight con Almacén de Data Lake con el Portal de Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).


## Uso de la tienda de metadatos de Hive/Oozie

Se recomienda encarecidamente usar una tienda de metadatos personalizada si desea conservar las tablas de Hive después de eliminar el clúster de HDInsight, con el fin de asociar dicha tienda de metadatos a otro clúster de HDInsight en el futuro.

La tienda de metadatos contiene metadatos de Hive y Oozie, como columnas, esquemas, particiones y tablas de Hive. El uso de la tienda de metadatos le ayuda a conservar sus metadatos de Hive y Oozie, por lo que no es necesario volver a crear tablas de Hive o trabajos de Oozie al crear un nuevo clúster. De forma predeterminada, Hive utiliza una base de datos SQL de Azure incrustada para almacenar esta información. La base de datos incrustada no puede conservar los metadatos cuando se elimina el clúster. Por ejemplo, tiene un clúster creado con una tienda de metadatos de Hive. Creó algunas tablas de Hive. Después de eliminar el clúster y de volverlo a crear con la misma tienda de metadatos de Hive, podrá ver las tablas de Hive que creó en el clúster original.

> [AZURE.NOTE] La configuración de la tienda de metadatos no está disponible para los tipos de clúster de HBase.

## Uso de redes virtuales de Azure

Una [Red virtual de Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite crear una red segura y persistente que contenga los recursos que necesita para la solución. Una red virtual permite hacer lo siguiente:

* Conectar recursos en la nube en una red privada (solo en la nube)

	![Diagrama de la configuración solo en la nube](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* Conectar sus recursos en la nube a la red de su centro de datos local (sitio a sitio o punto a sitio) usando una red privada virtual (VPN).

    | Configuración de sitio a sitio | Configuración de punto a sitio |
    | -------------------------- | --------------------------- |
    | La configuración sitio a sitio permite conectar varios recursos de su centro de datos a la red virtual de Azure usando una VPN de hardware o el servicio de enrutamiento y acceso remoto.<br />![Diagrama de la configuración de sitio a sitio](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | La configuración punto a sitio permite conectar un recurso específico a la red virtual de Azure usando una VPN de software.<br />![Diagrama de la configuración de punto a sitio](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Para obtener información sobre el uso de HDInsight con una red virtual, incluidos los requisitos de configuración específicos de la red virtual, consulte [Extensión de las funcionalidades de HDInsight con Red virtual de Azure](hdinsight-extend-hadoop-virtual-network.md).

## Personalización de clústeres mediante la personalización de clústeres de HDInsight (bootstrap)

A veces quiere configurar los archivos de configuración.

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Para mantener los cambios durante la vida útil de los clústeres, puede usar la personalización de clústeres de HDInsight durante el proceso de creación, o también puede usar Ambari en clústeres basados en Linux de forma segura. Para obtener más información, consulte [Personalización de los clústeres de HDInsight con Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

>[AZURE.NOTE] Los clústeres basados en Windows no pueden conservar los cambios debido a la recreación de imágenes. Para obtener más información, consulte [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Para mantener los cambios durante la vida útil de los clústeres, debe usar la personalización de clústeres de HDInsight durante el proceso de creación.

## Personalización de clústeres mediante la acción de script

Puede instalar componentes adicionales o personalizar la configuración del clúster mediante el uso de scripts durante la creación. Tales scripts se invocan mediante la opción de **Acción de script**, una opción de configuración que se puede usar a partir de los cmdlet de Windows PowerShell de HDInsight, el Portal o el SDK de .NET de HDInsight. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md).



## Métodos de creación de clústeres

En este artículo, ha obtenido información básica acerca de cómo crear un clúster de HDInsight basado en Windows. Use la siguiente tabla para buscar información específica acerca de cómo crear un clúster mediante un método que mejor se adapte a sus necesidades:

| Use esto para crear clústeres... | Use un explorador web... | Use la línea de comandos | Use la API de REST | Use el SDK | Desde Linux, Mac OS X o Unix | Desde Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Portal de Azure](hdinsight-hadoop-create-windows-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [CLI de Azure](hdinsight-hadoop-create-windows-clusters-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-windows-clusters-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [Plantillas de ARM](hdinsight-hadoop-create-windows-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0323_2016-->