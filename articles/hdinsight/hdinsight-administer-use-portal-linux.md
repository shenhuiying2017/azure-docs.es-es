---
title: "Administración de clústeres de Hadoop basados en Linux en HDInsight mediante Azure Portal | Microsoft Docs"
description: "Aprenda a crear y administrar clústeres de HDInsight basados en Linux mediante el portal de Azure."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 7bfa0d73f870202e4caeb893830952ca39a35c26
ms.openlocfilehash: 27ee26b38b9881ed7f0cd7b0253be123af27955f


---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

En [Azure Portal][azure-portal], puede administrar clústeres basados en Linux en Azure HDInsight. Use el selector de pestañas para obtener información sobre cómo administrar clústeres de Hadoop en HDInsight con otras herramientas. 

**Requisitos previos**

Antes de empezar este artículo, debe tener lo siguiente:

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="open-the-portal"></a>Abrir el portal
1. Inicie sesión en [https://portal.azure.com/](https://portal.azure.com).
2. Después de abrir el portal, puede:
   
   * Hacer clic en **Nuevo** en el menú de la izquierda para crear un nuevo clúster:
     
       ![botón nuevo clúster de HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)
   * Hacer clic en **Clústeres de HDInsight** en el menú izquierdo para mostrar los clústeres existentes
     
       ![Portal de Azure botón clúster de HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

## <a name="create-clusters"></a>Creación de clústeres
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight trabaja con una amplia gama de componentes de Hadoop. Para ver la lista de los componentes que han sido comprobados y admitidos, consulte [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md). Para obtener información general sobre la creación de clústeres, consulte [Creación de clústeres de Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="list-and-show-clusters"></a>Enumeración y visualización de clústeres
1. Inicie sesión en [https://portal.azure.com/](https://portal.azure.com).
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda para mostrar los clústeres existentes.
3. Haga clic en el nombre del clúster. Si la lista de clústeres es extensa, puede usar el filtro de la parte superior de la página.
4. Haga clic en un clúster de la lista para ver la página de información general:
   
    ![Azure Portal: aspectos básicos del clúster de HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png)
   
    **Menú de información general**:

   * **Panel**, **Panel de clúster** y **Dirección URL: todas son formas de acceder al panel del clúster, que es la web de Ambari para los clústeres basados en Linux.
   * **Secure Shell**: muestra las instrucciones para conectarse al clúster mediante la conexión de Secure Shell (SSH).
   * **Escalar clúster**: Permite cambiar el número de nodos de trabajo para este clúster.
   * **Eliminar**: elimina el clúster.

    **Menú de la izquierda**
   
   * **Registros de actividad**: consultar y mostrar registros de actividad.
   * **Access Control (IAM)**: usar las asignaciones de roles.  Vea [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](../active-directory/role-based-access-control-configure.md).
   * **Etiquetas**: las etiquetas permiten establecer pares clave-valor para definir una taxonomía personalizada de Cloud Services. Por ejemplo, puede crear una clave denominada **proyecto**y luego usar un valor común para todos los servicios asociados a un proyecto específico.
   * **Diagnosticar y solucionar problemas**: mostrar información sobre solución de problemas.
   * **Bloqueos**: agregar bloqueos para evitar la modificación o eliminación del clúster.
   * **Script de automatización**: mostrar y exportar la plantilla de Azure Resource Manager para el clúster. Actualmente, solo se puede exportar la cuenta de Azure Storage dependiente. Vea [Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
   * **Inicio rápido**: muestra información que le ayudará a empezar a usar HDInsight.
   * **Herramientas para HDInsight**: información de ayuda para herramientas relacionadas con HDInsight.
   * **Inicio de sesión de clúster**: mostrar la información de inicio de sesión de clúster.
   * **Subscription Core Usage** (Uso de núcleo de suscripción): mostrar los núcleos usados y disponibles para la suscripción. 
   * **Escalar clúster**: aumente o disminuya el número de nodos de trabajo del clúster. Vea [Escalado de clústeres](hdinsight-administer-use-management-portal.md#scale-clusters).
   * **Secure Shell**: muestra las instrucciones para conectarse al clúster mediante la conexión de Secure Shell (SSH). Vea [Uso de SSH con Hadoop en HDInsight basado en Linux desde Windows con PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md) y [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
   * **Asociado de HDInsight**: agrega o quita el asociado actual de HDInsight.
   * **Tiendas de metadatos externas**: consulte las tiendas de metadatos de Hive y Oozie. Las tiendas de metadatos solo pueden configurarse durante el proceso de creación del clúster. Vea [Uso de Hive/Oozie Metastore](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore).
   * **Acciones de script**: ejecuta scripts de Bash en el clúster. Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).
   * **Aplicaciones**: agregar o quitar aplicaciones de HDInsight.  Vea [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).
   * **Propiedades**: muestra las propiedades del clúster.
   * **Cuentas de almacenamiento**: ver las cuentas de almacenamiento y las claves. Las cuentas de almacenamiento se configuran durante el proceso de creación del clúster.
   * **Identidad AAD de clúster**: 
   * **Nueva solicitud de soporte técnico**: permite crear una incidencia de soporte técnico con el soporte técnico de Microsoft.

6. Haga clic en **Propiedades**.
   
    Las propiedades son:
   
   * **Nombre del clúster**: nombre del clúster.
   * **Dirección URL del clúster**. La dirección URL para la interfaz web de Ambari.
   * **Estado**: puede ser Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
   * **Región**: ubicación de Azure. Para una lista de ubicaciones de Azure admitidas, vea el cuadro de lista desplegable **Región** en [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Fecha de creación**.
   * **Sistema operativo**: **Windows** o **Linux**.
   * **Tipo**: Hadoop, HBase, Storm, Spark. 
   * **Versión**. Consulte [Versiones de HDInsight](hdinsight-component-versioning.md)
   * **Suscripción**: nombre de la suscripción.
   * **Origen de datos predeterminado**: el sistema de archivos predeterminado del clúster.
   * **Tamaño de los nodos de trabajo**.
   * **Tamaño del nodo principal**.

## <a name="delete-clusters"></a>Eliminación de clústeres
Al eliminar un clúster, no se elimina la cuenta de almacenamiento predeterminada ni otras cuentas de almacenamiento vinculadas. Puede volver a crear el clúster con las mismas cuentas de almacenamiento y las mismas tiendas de metadatos. Se recomienda usar un nuevo contenedor de blobs predeterminado cuando vuelva a crear el clúster.

1. Inicie sesión en el [portal][azure-portal].
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda. Si no ve **clústeres de HDInsight**, primero haga clic en **More services** (Más servicios).
3. Haga clic en el clúster que desea eliminar.
4. Haga clic en **Eliminar** en el menú superior y luego siga las instrucciones.

Vea también [Pausa o apagado de clústeres](#pauseshut-down-clusters).

## <a name="scale-clusters"></a>Escalado de clústeres
La característica de escalado de clústeres permite cambiar la cantidad de nodos de trabajo que usa un clúster que se ejecuta en HDInsight de Azure sin necesidad de volver a crear el clúster.

> [!NOTE]
> Solo son compatibles los clústeres con la versión 3.1.3 de HDInsight, o superior. Si no está seguro de la versión del clúster, puede comprobar la página de propiedades.  Vea [Enumeración y visualización de clústeres](#list-and-show-clusters).
> 
> 

A continuación se muestra el efecto que tiene cambiar la cantidad de nodos de datos de cada tipo de clúster compatible con HDInsight:

* Hadoop
  
    Puede aumentar sin ningún problema la cantidad de nodos de trabajo en un clúster de Hadoop que se encuentre en ejecución, sin que afecte a ningún trabajo pendiente o en ejecución. También se pueden enviar trabajos nuevos mientras la operación está en curso. Los errores que puedan surgir en una operación de escalado se enfrentan oportunamente, por lo que el clúster siempre queda en estado funcional.
  
    Cuando se realiza la reducción vertical de un clúster de Hadoop al disminuir la cantidad de nodos de datos, se reinician algunos de los servicios del clúster. Esto provoca que todos los trabajos pendientes y en ejecución fallen al completarse la operación de escalado. Sin embargo, puede volver a enviar los trabajos una vez finalizada la operación.
* HBase
  
    Puede agregar nodos sin problemas al clúster de HBase mientras se encuentra en ejecución, así como eliminarlos. Los servidores regionales se equilibran automáticamente en unos pocos minutos tras completar la operación de escalado. Sin embargo, puede equilibrar manualmente los servidores regionales iniciando sesión en el nodo principal del clúster y ejecutando los comandos siguientes desde una ventana del símbolo del sistema:
  
        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer
  
    Para más información sobre el uso del shell HBase, consulte []
* Storm
  
    Puede agregar o quitar sin problemas nodos de datos de su clúster de Storm mientras se encuentra en ejecución. Sin embargo, después de finalizar correctamente la operación de escalado, deberá volver a equilibrar la topología.
  
    Esto se puede realizar de dos formas:
  
  * La interfaz de usuario web de Storm
  * La herramienta de la interfaz de línea de comandos (CLI)
    
    Consulte la [documentación de Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obtener más detalles.
    
    La interfaz de usuario web de Storm se encuentra disponible en el clúster de HDInsight:
    
    ![Reequilibrio de escalado de HDInsight Storm](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.storm.rebalance.png)
    
    El siguiente es un ejemplo de cómo usar el comando CLI para volver a equilibrar la topología de Storm:
    
        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**Para escalar clústeres**

1. Inicie sesión en el [portal][azure-portal].
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda.
3. Haga clic en el clúster que desea escalar.
3. Haga clic en **Escalar clúster**.
4. Escriba el **Número de nodos de trabajo**. El límite del número de nodos del clúster varía según las suscripciones de Azure. Puede ponerse en contacto con el servicio de soporte relacionado con la facturación para aumentar el límite.  La información de costo reflejará los cambios realizados en el número de nodos.
   
    ![HDInsight hadoop hbase storm spark escalar](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>Pausa o apagado de clústeres

La mayoría de los trabajos de Hadoop son trabajos por lotes que se ejecutan solo ocasionalmente. En la mayoría de los clústeres de Hadoop, hay grandes períodos de tiempo en los que el clúster no se usa para el procesamiento. Con HDInsight, los datos se almacenan en Almacenamiento de Azure, por lo que puede eliminar un clúster de forma segura cuando no está en uso.
También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso.

Hay muchas maneras de programar el proceso:

* Usar Factoría de datos de Azure. Consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight a petición con Data Factory de Azure](hdinsight-hadoop-create-linux-clusters-adf.md) para crear servicios vinculados a HDInsight a petición.
* Usar Azure PowerShell.  Vea [Análisis de datos de retrasos de vuelos](hdinsight-analyze-flight-delay-data.md).
* Uso de CLI de Azure. Consulte [Administrar clústeres de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md).
* Usar .NET SDK de HDInsight. Vea [Envío de trabajos de Hadoop](hdinsight-submit-hadoop-jobs-programmatically.md).

Para información sobre precios, vea [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para eliminar un clúster desde el portal, vea [Eliminación de clústeres](#delete-clusters)

## <a name="change-passwords"></a>Cambio de contraseñas
Un clúster de HDInsight puede tener dos cuentas de usuario. La cuenta de usuario del clúster de HDInsight (también conocida como cuenta de usuario HTTP) y la cuenta de usuario SSH se crean durante el proceso de creación. Puede usar la interfaz de usuario web de Ambari para cambiar el nombre de usuario y la contraseña de la cuenta de usuario del clúster y las acciones de script para cambiar la cuenta de usuario de SSH

### <a name="change-the-cluster-user-password"></a>Cambio de la contraseña de usuario del clúster
Puede utilizar la interfaz de usuario web de Ambari para cambiar el nombre de usuario y la contraseña del clúster. Para iniciar sesión en Ambari, debe usar el nombre de usuario y la contraseña de clúster existente.

> [!NOTE]
> Si cambia la contraseña de usuario (admin) del clúster, puede provocar el error de las acciones de script ejecutadas en este clúster. Si tiene cualquier acciones de script persistente cuyo destino son nodos de trabajo, pueden producir un error al agregar nodos al clúster a través de operaciones de cambio de tamaño. Para más información sobre acciones de script, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).
> 
> 

1. Inicie sesión en la IU web de Ambari con las credenciales de usuario del clúster de HDInsight. El nombre de usuario predeterminado es **admin**. La dirección URL es **https://&lt;nombre del clúster de HDInsight > azurehdinsight.net**.
2. Haga clic en **Administración** en el menú superior y después en "Administrar Ambari". 
3. En el menú de la izquierda, haga clic en **Usuarios**.
4. Haga clic en **Administrador**.
5. Haga clic en **Cambiar contraseña**.

A continuación, Ambari cambia la contraseña en todos los nodos del clúster.

### <a name="change-the-ssh-user-password"></a>Cambio de la contraseña de usuario de SSH
1. Con un editor de texto, guarde el texto siguiente como un archivo llamado **changepassword.sh**.
   
   > [!IMPORTANT]
   > Debe utilizar un editor que use LF como final de líneas. Si el editor utiliza CRLF, el script no funcionará.
   > 
   > 
   
        #! /bin/bash
        USER=$1
        PASS=$2
   
        usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
2. Cargue el archivo en una ubicación de almacenamiento a la que pueda accederse desde HDInsight con una dirección HTTP o HTTPS. Por ejemplo, un almacén de archivos público como OneDrive o Almacenamiento de blobs de Azure. Guarde el identificador URI (dirección HTTP o HTTPS), en el archivo, ya que se necesitará en el paso siguiente.
3. En Azure Portal, haga clic en **clústeres de HDInsight**.
4. Haga clic en el clúster de HDInsight.
4. Haga clic en **Acciones de scripts**.
4. En la hoja **Acciones de script**, seleccione **Enviar nuevo**. Cuando aparezca la hoja **Enviar acción de script**, especifique la siguiente información.
   
   | Campo | Valor |
   | --- | --- |
   | Nombre |Cambio de contraseña de SSH |
   | URI de script de Bash |El identificador URI del archivo changepassword.sh |
   | Nodos (principal, de trabajo, nimbus, supervisor, Zookeeper, etc.) |✓ para todos los tipos de nodo enumerados |
   | Parámetros |Escriba el nombre de usuario de SSH y la contraseña nueva. Debe haber un espacio entre el nombre de usuario y la contraseña. |
   | Conservar esta acción de script... |Deje este campo en sin activar. |
5. Seleccione **Crear** para aplicar el script. Una vez que finalice el script, podrá conectarse al clúster mediante SSH con la nueva contraseña.

## <a name="grantrevoke-access"></a>Concesión o revocación del acceso
Los clústeres de HDInsight tienen los siguientes servicios web HTTP (todos estos servicios tienen extremos RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

De manera predeterminada, estos servicios se conceden para el acceso. Puede revocar o conceder el acceso mediante la [CLI de Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) y [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Búsqueda del identificador de la suscripción

**Para buscar los identificadores de la suscripción de Azure**

1. Inicie sesión en el [portal][azure-portal].
2. Haga clic en **Suscripciones**. Cada suscripción tiene un nombre y un identificador.

Cada clúster está asociado a una suscripción de Azure. El identificador de la suscripción se muestra en el icono **Esencial** del clúster. Vea [Enumeración y visualización de clústeres](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Búsqueda del grupo de recursos
En el modo de Azure Resource Manager, cada clúster de HDInsight se crea con un grupo de Azure Resource Manager. El grupo de Resource Manager al que pertenece un clúster aparece en:

* La lista de clústeres tiene una columna **Grupo de recursos** .
* Icono **Esencial** del clúster.  

Vea [Enumeración y visualización de clústeres](#list-and-show-clusters).

## <a name="find-the-default-storage-account"></a>Búsqueda de la cuenta de almacenamiento predeterminada
Cada clúster de HDInsight tiene una cuenta de almacenamiento predeterminada. La cuenta de almacenamiento predeterminada y sus claves para un clúster aparecen en **Cuentas de almacenamiento**. Consulte [Enumeración y visualización de clústeres](#list-and-show-clusters).

## <a name="run-hive-queries"></a>Ejecución de consultas de Hive
No puede ejecutar un trabajo de Hive directamente desde el Portal de Azure, pero puede usar la vista de Hive en la IU web de Ambari.

**Para ejecutar las consultas de Hive mediante la vista de Hive de Ambari**

1. Inicie sesión en la IU web de Ambari con las credenciales de usuario del clúster de HDInsight. El nombre de usuario predeterminado es **admin**. La dirección URL es **https://&lt;nombre del clúster de HDInsight > azurehdinsight.net**.
2. Abra la vista de Hive como se muestra en la siguiente captura de pantalla:  
   
    ![Vista de hive de HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)
3. Haga clic en **Query** (Consulta) en el menú superior.
4. Escriba una consulta de Hive en el **Editor de consultas** y haga clic en **Execute** (Ejecutar).

## <a name="monitor-jobs"></a>Supervisión de trabajos
Consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="browse-files"></a>Examinar archivos
Con el Portal de Azure, puede examinar el contenido del contenedor predeterminado.

1. Inicie sesión en [https://portal.azure.com/](https://portal.azure.com).
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda para mostrar los clústeres existentes.
3. Haga clic en el nombre del clúster. Si la lista de clústeres es extensa, puede usar el filtro de la parte superior de la página.
4. Haga clic en **Cuentas de almacenamiento** en el menú izquierdo del clúster.
5. Haga clic en una cuenta de almacenamiento.
7. Haga clic en el icono **Blobs** .
8. Haga clic en el nombre del contenedor predeterminado.

## <a name="monitor-cluster-usage"></a>Supervisión del uso de los clústeres
La sección **Uso** de la hoja del clúster de HDInsight muestra información sobre el número de núcleos disponibles con la suscripción para su uso con HDInsight, así como el número de núcleos asignados al clúster y cómo se asignan a los nodos de este clúster. Vea [Enumeración y visualización de clústeres](#list-and-show-clusters).

> [!IMPORTANT]
> Para supervisar los servicios que proporciona el clúster de HDInsight, debe usar la web de Ambari o la API de REST de Ambari. Para obtener más información sobre el uso de Ambari, consulte [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md)
> 
> 

## <a name="connect-to-a-cluster"></a>Conectarse a un clúster
Consulte [Uso de Hive con Hadoop en HDInsight con SSH](hdinsight-hadoop-use-hive-ssh.md#ssh).

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido a crear un clúster de HDInsight mediante el Portal y a abrir la herramienta de línea de comandos de Hadoop. Para obtener más información, consulte los artículos siguientes:

* [Administración de HDInsight con PowerShell de Azure](hdinsight-administer-use-powershell.md)
* [Administración de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md)
* [Creación de clústeres de HDInsight](hdinsight-provision-clusters.md)
* [Uso de Hive en HDInsight](hdinsight-use-hive.md)
* [Uso de Pig en HDInsight](hdinsight-use-pig.md)
* [Uso de Sqoop en HDInsight](hdinsight-use-sqoop.md)
* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md)
* [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Línea de comandos de Hadoop"



<!--HONumber=Dec16_HO3-->


