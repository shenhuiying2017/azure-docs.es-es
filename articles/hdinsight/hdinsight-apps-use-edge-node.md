---
title: "Uso de nodos perimetrales en clústeres de Hadoop en HDInsight y Azure | Microsoft Docs"
description: "Procedimientos para agregar un nodo perimetral vacío a un clúster de HDInsight que se puede usar como cliente, y para probar u hospedar las aplicaciones de HDInsight."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jgao
ms.openlocfilehash: e21dabcc6999b1f1047d334e782f723d0c03c2cb
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="use-empty-edge-nodes-on-hadoop-clusters-in-hdinsight"></a>Uso de nodos perimetrales en clústeres vacíos en HDInsight

Aprenda a agregar un nodo perimetral vacío a un clúster de HDInsight. Un nodo perimetral vacío es una máquina virtual Linux con las mismas herramientas de cliente instaladas y configuradas que en los nodos principales, pero sin que se ejecuten servicios de Hadoop. Se puede usar el nodo perimetral para acceder al clúster y para probar y hospedar las aplicaciones cliente. 

Un nodo perimetral vacío se puede agregar a un clúster de HDInsight existente o a uno nuevo al crear el clúster. La adición de un nodo perimetral vacío se realiza mediante una plantilla de Azure Resource Manager.  En el ejemplo siguiente se muestra cómo se hace esto mediante una plantilla:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Como se muestra en el ejemplo, opcionalmente puede llamar a una [acción de script](hdinsight-hadoop-customize-cluster-linux.md) para realizar configuraciones adicionales, como instalar [Apache Hue](hdinsight-hadoop-hue-linux.md) en el nodo perimetral. El script de acción de script debe estar accesible públicamente en la web.  Por ejemplo, si el script se almacena en Azure Storage, use contenedores o blobs públicos.

El tamaño de la máquina virtual del nodo perimetral debe cumplir con los requisitos de tamaño para las máquinas virtuales de nodos de trabajador de clústeres de HDInsight. Para conocer los tamaños recomendados para máquinas virtuales de nodos de trabajador, consulte [Creación de clústeres de Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Después de haber creado un nodo perimetral, puede conectarse a él mediante SSH y ejecutar herramientas de cliente para acceder al clúster de Hadoop en HDInsight.

> [!WARNING] 
> El uso de un nodo perimetral vacío con HDInsight se permite actualmente en versión preliminar. Los componentes personalizados que se instalan en el nodo perimetral reciben un soporte técnico de Microsoft comercialmente razonable. Esto podría suponer que disponga de su ayuda en los problemas que pueda encontrar. O bien, puede remitirse a los recursos de la comunidad para obtener más asistencia. Los siguientes son algunos de los sitios más activos donde puede obtener ayuda de la comunidad de usuarios:
>
> * [Foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [http://stackoverflow.com](http://stackoverflow.com).
>
> Si usa una tecnología de Apache, es posible que encuentre asistencia a través de los sitios de los proyectos de Apache en [http://apache.org](http://apache.org), por ejemplo, en el sitio de [Hadoop](http://hadoop.apache.org/).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Adición de un nodo perimetral a un clúster existente
En esta sección, usará una plantilla de Resource Manager para agregar un nodo perimetral a un clúster de HDInsight existente.  La plantilla de Resource Manager se puede encontrar en [GitHub](https://azure.microsoft.com/en-us/resources/templates/101-hdinsight-linux-add-edge-node/). La plantilla de Resource Manager llama a una acción de script situada en https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. El script no realiza ninguna acción.  Sirve para demostrar la llamada a la acción de script desde una plantilla de Resource Manager.

**Para agregar un nodo perimetral vacío a un clúster existente**

1. Si todavía no tiene uno, cree un clúster de HDInsight.  Vea el [Tutorial de Hadoop: introducción al uso de Hadoop en HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Haga clic en la imagen siguiente para iniciar sesión en Azure y abrir la plantilla de Azure Resource Manager en Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure las siguientes propiedades:
   
   * **Suscripción**: seleccione la suscripción de Azure usada para crear este clúster.
   * **Grupo de recursos**: seleccione el grupo de recursos que se usa para el clúster de HDInsight existente.
   * **Ubicación**: seleccione la ubicación del clúster de HDInsight existente.
   * **Nombre del clúster**: escriba el nombre de un clúster de HDInsight existente.
   * **Tamaño de nodo perimetral**: seleccione uno de los tamaños de máquina virtual. El tamaño de la máquina virtual debe cumplir los requisitos de tamaño para máquinas virtuales de nodos de trabajador. Para conocer los tamaños recomendados para máquinas virtuales de nodos de trabajador, consulte [Creación de clústeres de Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Prefijo de nodo perimetral**: el valor predeterminado es **new**.  Usando el valor predeterminado, el nombre del nodo perimetral es **new-edgenode**.  Puede personalizar el prefijo desde el portal. También puede personalizar el nombre completo desde la plantilla.

4. Seleccione **Acepto los términos y condiciones indicadas anteriormente** y, después, haga clic en **Comprar** para crear el nodo perimetral.

>[!IMPORTANT]
> Asegúrese de seleccionar el grupo de recursos de Azure para el clúster de HDInsight existente.  De lo contrario, obtendrá el mensaje de error "No se puede llevar a cabo la operación solicitada en el recurso anidado. No se encontró el recurso primario "&lt;ClusterName>"".

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Adición de un nodo perimetral al crear un clúster
En esta sección, usará una plantilla de Resource Manager para crear un clúster de HDInsight con un nodo perimetral.  La plantilla de Resource Manager se puede encontrar en la galería [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). La plantilla de Resource Manager llama a una acción de script situada en https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. El script no realiza ninguna acción.  Sirve para demostrar la llamada a la acción de script desde una plantilla de Resource Manager.

**Para agregar un nodo perimetral vacío a un clúster existente**

1. Si todavía no tiene uno, cree un clúster de HDInsight.  Vea la [Introducción al uso de Hadoop en HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Haga clic en la imagen siguiente para iniciar sesión en Azure y abrir la plantilla de Azure Resource Manager en Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure las siguientes propiedades:
   
   * **Suscripción**: seleccione la suscripción de Azure usada para crear este clúster.
   * **Grupo de recursos**: cree un nuevo grupo de recursos para el clúster.
   * **Ubicación**: seleccione una ubicación para el grupo de recursos.
   * **Nombre del clúster**: escriba un nombre para el nuevo clúster que se va a crear.
   * **Nombre de usuario de inicio de sesión del clúster**: escriba el nombre de usuario HTTP de Hadoop.  El nombre predeterminado es **admin**.
   * **Contraseña de inicio de sesión de clúster**: escriba la contraseña de usuario HTTP de Hadoop.
   * **Nombre de usuario SSH**: escriba el nombre de usuario SSH. El nombre predeterminado es **sshuser**.
   * **Contraseña SSH**: escriba la contraseña de usuario SSH.
   * **Instalar acción de script**: mantenga el valor predeterminado para realizar este tutorial.
     
     Algunas propiedades han sido codificadas de forma rígida en la plantilla: tipo de clúster, número de nodos de trabajo del clúster, tamaño del nodo perimetral y nombre del nodo perimetral.
4. Seleccione **Acepto los términos y condiciones indicadas anteriormente** y, después, haga clic en **Comprar** para crear el clúster con el nodo perimetral.

## <a name="access-an-edge-node"></a>Acceso a un nodo perimetral
El punto de conexión ssh del nodo perimetral es &lt;NombreNodoPerimetral>.&lt;NombreClúster>-ssh.azurehdinsight.net:22.  Por ejemplo, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

El nodo perimetral aparece como una aplicación en Azure Portal.  El portal le proporciona la información para acceder al nodo perimetral mediante SSH.

**Para comprobar el punto de conexión SSH del nodo perimetral**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Abra el clúster de HDInsight con un nodo perimetral.
3. Haga clic en **Aplicaciones** en la hoja del clúster. Verá el nodo perimetral.  El nombre predeterminado es **new-edgenode**.
4. Haga clic en el nodo perimetral. Verá el punto de conexión SSH.

**Para usar Hive en el nodo perimetral**

1. Use SSH para conectarse al nodo perimetral. Para obtener más información, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Después de haberse conectado al nodo perimetral mediante SSH, use el comando siguiente para abrir la consola de Hive:
   
        hive
3. Ejecute el siguiente comando para mostrar las tablas de Hive en el clúster:
   
        show tables;

## <a name="delete-an-edge-node"></a>Eliminación de un nodo perimetral
Puede eliminar un nodo perimetral de Azure Portal.

**Para acceder a un nodo perimetral**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Abra el clúster de HDInsight con un nodo perimetral.
3. Haga clic en **Aplicaciones** en la hoja del clúster. Verá una lista de nodos perimetrales.  
4. Haga clic con el botón derecho en el nodo perimetral que quiere eliminar y luego haga clic en **Eliminar**.
5. Haga clic en **Sí** para continuar.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido a agregar un nodo perimetral y a acceder a él. Para obtener más información, consulte los artículos siguientes:

* [Instalación de aplicaciones de HDInsight](hdinsight-apps-install-applications.md): aprenda a instalar una aplicación de HDInsight en sus clústeres.
* [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a implementar en HDInsight una aplicación de HDInsight no publicada.
* [Publicación de aplicaciones de HDInsight en Azure Marketplace](hdinsight-apps-publish-applications.md): aprenda a publicar aplicaciones de HDInsight personalizadas en Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: instalación de una aplicación de HDInsight): aprenda a definir aplicaciones de HDInsight.
* [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md): aprenda a usar acciones de script para instalar otras aplicaciones.
* [Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): aprenda a llamar a plantillas de Resource Manager para crear clústeres de HDInsight.

