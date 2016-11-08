---
title: Crear clústeres de Hadoop, HBase, Storm o Spark en Linux en HDInsight con el portal | Microsoft Docs
description: Aprenda a crear clústeres Hadoop, HBase, Storm o Spark en Linux para HDInsight con un explorador web y el Portal de vista previa de Azure.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/07/2016
ms.author: nitinme

---
# Crear clústeres basados en Linux en HDInsight con el Portal de Azure
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

El Portal de Azure es una herramienta de administración basada en web para servicios y recursos hospedados en la nube de Microsoft Azure. En este artículo aprenderá a crear clústeres de HDInsight basados en Linux mediante el portal.

## Requisitos previos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un explorador web moderno**. El Portal de Azure usa HTML5 y Javascript, y puede que no funcione correctamente en exploradores más antiguos.

## Creación de clústeres
El Portal de Azure expone la mayor parte de las propiedades del clúster. Mediante la plantilla de Azure Resource Manager puede ocultar una gran cantidad de detalles. Para más información, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **NUEVO**, en **Análisis de datos** y luego en **HDInsight**.
   
    ![Creación de un clúster en Azure Portal](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Creación de un clúster en Azure Portal")
3. Especifique el **nombre del clúster**: dicho nombre debe ser único globalmente.
4. Haga clic en **Seleccionar tipo de clúster** y luego seleccione:
   
   * **Tipo de clúster**: si no sabe qué opción elegir, seleccione **Hadoop**. Es el tipo de clúster más popular.
   * **Sistema operativo**: seleccione **Linux**.
   * **Versión**: use la versión predeterminada si no sabe qué opción elegir. Para obtener más información, consulte [Versiones de clústeres de HDInsight](hdinsight-component-versioning.md).
   * **Nivel de clúster**: HDInsight de Azure proporciona las ofertas de macrodatos en la nube en dos categorías: Estándar y Premium. Para más información, consulte [Niveles de clúster](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).
     
     ![Configuración de las capas de HDInsight Premium](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)
5. Haga clic en **Suscripción** para seleccionar la suscripción de Azure que se usará para el clúster.
6. Haga clic en **Grupo de recursos** para seleccionar un grupo de recursos existente, o bien haga clic en **Nuevo** para crear uno nuevo.
   
   > [!NOTE]
   > Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.
   > 
   > 
7. Haga clic en **Credenciales** y escriba una contraseña para el usuario administrador. También debe especificar un **Nombre de usuario de SSH** y una **CONTRASEÑA** o una **CLAVE PÚBLICA**, que se usarán para autenticar al usuario de SSH. Es recomendable usar una clave pública. Haga clic en **Seleccionar** en la parte inferior para guardar la configuración de las credenciales.
   
    ![Proporcione las credenciales del clúster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "Proporcione las credenciales del clúster")
   
    Para obtener más información sobre el uso de SSH con HDInsight, vea uno de los siguientes artículos:
   
   * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
8. Haga clic en **Origen de datos** para elegir un origen de datos existente para el clúster o crear uno nuevo.
   
    ![Hoja Origen de datos](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "Proporcione la configuración del origen de datos")
   
    Actualmente puede seleccionar una cuenta de almacenamiento de Azure como origen de datos para un clúster de HDInsight. Use lo siguiente para comprender las entradas de la hoja **Origen de datos**.
   
   * **Método de selección**: establezca esta opción en **De todas las suscripciones** para permitir la exploración de cuentas de almacenamiento en todas sus suscripciones. Establézcalo en **Clave de acceso** si desea especificar el **Nombre de almacenamiento** y la **Clave de acceso** de una cuenta de almacenamiento existente.
   * **Seleccionar cuenta de almacenamiento o Nueva**: haga clic en **Seleccionar cuenta de almacenamiento** para buscar y seleccionar la cuenta de almacenamiento existente que desee asociar con el clúster. O bien, haga clic en **Nueva** para crear una cuenta de almacenamiento nueva. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.
   * **Elegir contenedor predeterminado**: use esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.
   * **Ubicación**: región geográfica en la que se encuentra o donde se creará la cuenta de almacenamiento.
     
     > [!IMPORTANT]
     > Seleccionar la ubicación del origen de datos predeterminado también establecerá la ubicación del clúster de HDInsight. El origen de datos predeterminado y el clúster deben encontrarse en la misma región.
     > 
     > 
   * **Identidad de AAD del clúster**: al configurarla, los almacenes de Azure Data Lake podrán acceder al clúster en función de la configuración de AAD.
     
     Haga clic en **Seleccionar** para guardar la configuración del origen de datos.
9. Haga clic en **Planes de tarifa de nodo** para mostrar información sobre los nodos que se creen para este clúster. Establezca el número de nodos de trabajo que necesita para el clúster. El costo estimado del clúster se mostrará en la hoja.
   
    ![Hoja Niveles de precios de nodo](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "Especifique el número de nodos de clúster")
   
   > [!IMPORTANT]
   > Si planea crear más de 32 nodos de trabajo, en la creación de clústeres o al cambiar el tamaño del clúster después de la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
   > 
   > Para obtener más información acerca de los tamaños de nodo y los costos asociados, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   > 
   > 
   
    Haga clic en **Seleccionar** para guardar la configuración de precios de nodo.
10. Haga clic en **Configuración opcional** para seleccionar la versión del clúster y configurar otros valores opcionales, como unirse a una **Red virtual**, configurar una **tienda de metadatos externos** para almacenar datos de Hive y Oozie, usar acciones de script para personalizar un clúster para instalar componentes personalizados o usar cuentas de almacenamiento adicionales con el clúster.
    
    * **Red virtual**: seleccione una red virtual de Azure y la subred si quiere colocar el clúster en una red virtual.
      
        ![Hoja de red virtual](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "Especifique detalles de red virtual")
      
        Para más información sobre el uso de HDInsight con una red virtual como, por ejemplo, los requisitos de configuración específicos de la red virtual, consulte [Extensión de las funcionalidades de HDInsight con Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).
    * Haga clic en **Tiendas de metadatos externas** para especificar la base de datos SQL que quiere usar para guardar los metadatos de Hive y Oozie asociados al clúster.
      
      > [!NOTE]
      > La configuración de la tienda de metadatos no está disponible para los tipos de clúster de HBase.
      > 
      > 
      
        ![Hoja de tiendas de metadatos personalizados](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "Especificar tiendas de metadatos externas")
      
        En **Usar una Base de datos SQL existente para metadatos de Hive**, haga clic en **Sí**, seleccione una base de datos SQL y luego escriba el nombre de usuario y la contraseña para la base de datos. Repita estos pasos si desea **Usar una Base de datos SQL existente para los metadatos de Oozie**. Haga clic en **Seleccionar** hasta volver a la hoja **Configuración opcional**.
      
      > [!NOTE]
      > La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.
      > 
      > 
      
        &nbsp;
      
      > [!IMPORTANT]
      > Al crear una tienda de metadatos, no debe usar un nombre de base de datos que contenga guiones, ya que esto puede producir un error en el proceso de creación del clúster.
      > 
      > 
    * **Acciones de script** si quiere usar un script personalizado para personalizar un clúster, conforme se crea el clúster. Para obtener más información sobre acciones de script, vea [Personalización de clústeres de HDInsight con una acción de script](hdinsight-hadoop-customize-cluster-linux.md). En la hoja Acciones de script, proporcione los detalles como se muestra en la captura de pantalla.
      
        ![Hoja de acción de script](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "Especifique una acción de script")
    * Haga clic en **Cuentas de almacenamiento vinculadas** para especificar las cuentas de almacenamiento adicionales que se van a asociar con al clúster. En la hoja **Claves de almacenamiento de Azure**, haga clic en **Agregar una clave de almacenamiento** y luego seleccione una cuenta de almacenamiento existente o cree una nueva cuenta.
      
        ![Hoja de almacenamiento adicional](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "Especificar cuentas de almacenamiento adicionales")
      
        También puede agregar más cuentas de almacenamiento una vez creado un clúster. Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).
      
        Haga clic en **Seleccionar** hasta volver a la hoja **Nuevo clúster de HDInsight**.
      
        Además de la cuenta de almacenamiento de blobs, también puede vincular almacenes de Azure Data Lake. Para realizar la configuración, configure AAD en el origen de datos donde configuró la cuenta de almacenamiento predeterminada y el contenedor predeterminado.
11. En la hoja **Nuevo clúster de HDInsight**, asegúrese de que la opción **Anclar a Panel de inicio** está seleccionada y haga clic en **Crear**. Esto creará el clúster y agregará un icono para él en el panel de inicio de Azure Portal. El icono indicará que el clúster está aprovisionando y cambiará para mostrar el icono de HDInsight cuando se haya completado el proceso.
    
    | Durante el aprovisionamiento | Aprovisionamiento completado |
    | --- | --- |
    | ![Indicador de aprovisionamiento en el panel de inicio](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) |![Icono de clúster aprovisionado](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |
    
    > [!NOTE]
    > El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del Panel de inicio o la entrada **Notificaciones** en la parte izquierda de la página para comprobar el proceso de aprovisionamiento.
    > 
    > 
12. Cuando termine el proceso de creación, haga clic en el icono del clúster desde el panel de inicio para iniciar la hoja del clúster. La hoja de clúster proporciona información esencial sobre el clúster, como el nombre, el grupo de recursos al que pertenece, la ubicación, el sistema operativo, la dirección URL para el panel del clúster, etc.
    
    ![Hoja de clúster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "Propiedades de clúster")
    
    Use las siguientes opciones para comprender los iconos de la parte superior de esta hoja y, en la sección **Conceptos básicos**:
    
    * **Configuración** y **Toda la configuración**: Muestra la hoja **Configuración** del clúster, que permite obtener acceso a información de configuración detallada para el clúster.
    * **Panel**, **Panel del clúster** y **URL**: se trata de formas de tener acceso al panel del clúster, que es un portal web que ejecuta trabajos en el clúster.
    * **Shell seguro**: información necesaria para acceder al clúster mediante SSH.
    * **Eliminar**: elimina el clúster de HDInsight.
    * **Inicio rápido** (![icono de nube y rayo = inicio rápido](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)): muestra información que le ayudará a empezar a usar HDInsight.
    * **Usuarios** (![icono de usuarios](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)): permite establecer permisos de *administración del portal* de este clúster para otros usuarios de su suscripción de Azure.
      
      > [!IMPORTANT]
      > Esto *solo* afecta al acceso y a los permisos para este clúster en el Portal de Azure, y no tiene ningún efecto sobre quién puede conectarse o enviar trabajos al clúster de HDInsight.
      > 
      > 
    * **Etiquetas** (![icono de etiqueta](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)): las etiquetas permiten establecer pares clave-valor para definir una taxonomía personalizada de sus servicios en la nube. Por ejemplo, puede crear una clave denominada **proyecto** y luego usar un valor común para todos los servicios asociados a un proyecto específico.

## Personalización de los clústeres
* Consulte [Personalización de los clústeres de HDInsight con Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## Eliminación del clúster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Pasos siguientes
Ahora que ya creó un clúster de HDInsight correctamente, use lo siguiente para aprender a trabajar con el clúster:

### Clústeres Hadoop
* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

### Clústeres HBase
* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desarrollo de aplicaciones de Java para HBase en HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### Clústeres Storm
* [Desarrollo de topologías de Java para Storm en HDInsight](hdinsight-storm-develop-java-topology.md)
* [Uso de componentes de Python en Storm en HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementación y supervisión de topologías con Storm en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### Clústeres de Spark
* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_0914_2016-->