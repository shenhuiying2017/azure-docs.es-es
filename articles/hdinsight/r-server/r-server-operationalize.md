---
title: 'Uso de R Server en HDInsight: Azure | Microsoft Docs'
description: Obtenga información sobre cómo usar R Server en Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 6de6e78d9b4ad68d268b59cff18c75fbdd7be757
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412848"
---
# <a name="operationalize-r-server-cluster-on-azure-hdinsight"></a>Uso de R Server en Azure HDInsight

Después de haber usado el clúster de R Server en HDInsight para completar el modelado de datos, puede usar el modelo para realizar predicciones. En este artículo se ofrecen instrucciones sobre realizar esta tarea.

## <a name="prerequisites"></a>requisitos previos

* **Un clúster de R Server en HDInsight**: para obtener instrucciones, consulte [Introducción al uso de R Server en HDInsight](r-server-get-started.md).

* **Un cliente de Secure Shell (SSH)**: el cliente de SSH se usa para conectarse al clúster de HDInsight de forma remota y ejecutar comandos directamente desde el clúster. Para más información, consulte [Uso SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-r-server-cluster-with-one-box-configuration"></a>Uso del clúster de R Server con la configuración One-box

1. Uso de SSH en el nodo perimetral.  

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Para obtener instrucciones sobre cómo utilizar SSH con Azure HDInsight, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Cambie el directorio para la versión pertinente y use sudo en el archivo DLL de dotnet: 

    - Para Microsoft R Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - Para Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Se muestran las diferentes opciones que tiene. Elija la primera, tal como se muestra en la siguiente captura de pantalla, con el fin de **configurar R Server para la puesta en marcha**.

    ![operacionalización one box](./media/r-server-operationalize/admin-util-one-box-1.png)

4. Ahora verá la opción para elegir cómo desea usar R Server. Entre las opciones, elija la primera de ellas escribiendo **A**.

    ![operacionalización one box](./media/r-server-operationalize/admin-util-one-box-2.png)

5. Cuando se le solicite, escriba dos veces la contraseña de un usuario administrador local.

6. Debería ver resultados que indican que la operación se realizó correctamente. También se le pedirá que seleccione otra opción del menú. Seleccione E para volver al menú principal.

    ![operacionalización one box](./media/r-server-operationalize/admin-util-one-box-3.png)

7. Como paso opcional, puede realizar comprobaciones de diagnóstico mediante la ejecución de una prueba de diagnóstico tal y como se muestra a continuación:

    a. En el menú principal, seleccione **6** para ejecutar pruebas de diagnóstico.

    ![operacionalización one box](./media/r-server-operationalize/diagnostic-1.png)

    b. En el menú de pruebas de diagnóstico, seleccione **A**. Cuando se le solicite, escriba la contraseña que ha proporcionado para el usuario administrador local.

    ![operacionalización one box](./media/r-server-operationalize/diagnostic-2.png)

    c. Compruebe que el resultado muestra que el estado general es Pass (Aprobado).

    ![operacionalización one box](./media/r-server-operationalize/diagnostic-3.png)

    d. En las opciones de menú que se muestran, escriba **E** para volver al menú principal y, luego, escriba **8** para salir de la utilidad de administración.

### <a name="long-delays-when-consuming-web-service-on-spark"></a>Retrasos prolongados al consumir el servicio web en Spark

Si se producen retrasos prolongados al intentar consumir un servicio web creado con funciones de mrsdeploy en un contexto de proceso de Spark, puede que necesite agregar carpetas que falten. La aplicación de Spark pertenece a un usuario llamado "*rserve2*" cada vez que se invoque desde un servicio web mediante las funciones de mrsdeploy. Para evitar este problema:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


En esta fase, la configuración de la puesta en operación está completa. Ahora puede usar el paquete `mrsdeploy` en RClient para conectarse a la operacionalización en el nodo perimetral y empezar a usar sus características, como la [ejecución remota](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) y los [servicios web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). Dependiendo de si el clúster se configura en una red virtual o no, puede que necesite configurar la tunelización de reenvío del puerto mediante el inicio de sesión SSH. En las secciones siguientes se explica cómo configurar este túnel.

### <a name="r-server-cluster-on-virtual-network"></a>Clúster de R Server en una red virtual

Asegúrese de que permite que el tráfico pase a través del puerto 12800 al nodo perimetral. De esa forma, puede usar el nodo perimetral para conectarse a la característica de operacionalización.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Si `remoteLogin()` no se puede conectarse al nodo perimetral, pero se posible usar SSH en dicho nodo, tendrá que comprobar si se ha configurado correctamente la regla para permitir el tráfico en el puerto 12800. Si el problema no desaparece, puede solucionarlo mediante la configuración de la tunelización de reenvío del puerto a través de SSH. Para obtener instrucciones, consulte la siguiente sección:

### <a name="r-server-cluster-not-set-up-on-virtual-network"></a>Clúster de R Server no configurado en una red virtual

Si el clúster no está configurado en la red virtual o si tiene problemas con la conectividad a través de la red virtual, puede utilizar la tunelización de reenvío del puerto de SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Una vez que la sesión SSH esté activa, el tráfico del puerto 12800 del equipo se reenviará al puerto 12800 del nodo perimetral mediante la sesión de SSH. Asegúrese de utilizar `127.0.0.1:12800` en el método `remoteLogin()`. Con esta acción, se iniciará sesión en la operacionalización del nodo perimetral mediante el enrutamiento de puerto.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Escala de nodos de proceso operacionalizados en nodos de trabajo de HDInsight

Para escalar los nodos de proceso, retire primero los nodos de trabajo y, luego, configure los nodos de proceso en los nodos de trabajo retirados.

### <a name="step-1-decommission-the-worker-nodes"></a>Paso 1: Retirada de los nodos de trabajo

El clúster de R Server no se administra a través de YARN. Si los nodos de trabajo no se retiran, el Administrador de recursos de YARN no funcionará según lo previsto, ya que no reconocerá los recursos que utiliza el servidor. Para evitar esto, se recomienda la retirada de los nodos de trabajo antes de escalar horizontalmente los nodos de proceso.

Siga estos pasos para retirar nodos de trabajo:

1. Inicie sesión en la consola de Ambari del clúster y haga clic en la pestaña **Hosts**.

2. Seleccione los nodos de trabajo (para retirarlos).

3. Haga clic en **Acciones** > **Hosts seleccionados** > **Hosts** > **Activar modo de mantenimiento**. Por ejemplo, en la siguiente imagen se han seleccionado wn3 y wn4 para su retirada.  

   ![retirar nodos de trabajo](./media/r-server-operationalize/get-started-operationalization.png)  

* Seleccione **Acciones** > **Hosts seleccionados** > **DataNodes** > haga clic en **Retirar**.
* Seleccione **Acciones** > **Hosts seleccionados** > **NodeManagers** > haga clic en **Retirar**.
* Seleccione **Acciones** > **Hosts seleccionados** > **DataNodes** > haga clic en **Detener**.
* Seleccione **Acciones** > **Hosts seleccionados** > **NodeManagers** > haga clic en **Detener**.
* Seleccione **Acciones** > **Hosts seleccionados** > **Hosts** > haga clic en **Detener todos los componentes**.
* Anule la selección de los nodos de trabajo y selecciones los nodos principales.
* Seleccione **Acciones** > **Hosts seleccionados** > "**Hosts** > **Reiniciar todos los componentes**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Paso 2: Configuración de los nodos de proceso en cada nodo de trabajo retirado

1. SSH en cada nodo de trabajo retirado.

2. Ejecute la utilidad de administración con el archivo DLL correspondiente para el clúster de R Server que tenga. Para R Server 9.1, ejecute lo siguiente:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Escriba **1** para seleccionar la opción **Configure R Server for Operationalization** (Configurar R Server para operacionalización).

4. Escriba **c** para seleccionar la opción `C. Compute node`. Esto configurará el nodo de proceso en el nodo de trabajo.

5. Salga de la utilidad de administración.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Paso 3: Incorporación de detalles de nodos de proceso en el nodo web

Una vez que todos los nodos de trabajo retirados se han configurado para ejecutar nodos de proceso, vuelva al nodo perimetral y agregue las direcciones IP de los nodos de trabajo retirados a la configuración del nodo web de R Server:

1. Uso de SSH en el nodo perimetral.

2. Ejecute `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

3. Busque la sección de identificadores URI y agregue la dirección IP del nodo de trabajo y los detalles del puerto.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Pasos siguientes

* [Manage R Server cluster on HDInsight](r-server-hdinsight-manage.md) (Administración de un clúster de R Server en HDInsight)
* [Opciones de contexto de proceso para un clúster de R Server en HDInsight](r-server-compute-contexts.md)
* [Opciones de Azure Storage para un clúster de R Server en HDInsight](r-server-storage.md)
