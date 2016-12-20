---
title: "Uso de nodos perimetrales vacíos en HDInsight | Microsoft Docs"
description: "Procedimiento para agregar un nodo perimetral vacío a un clúster de HDInsight que se puede usar como cliente, y cómo probar u hospedar las aplicaciones de HDInsight."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 3b7bbd63a0bd87ee45a387bc0612541f0d82208b
ms.openlocfilehash: 73f7235686435300d18fe518c08adbd6c3904f39


---
# <a name="use-empty-edge-nodes-in-hdinsight"></a>Uso de nodos perimetrales vacíos en HDInsight
Aprenda a agregar un nodo perimetral vacío a un clúster de HDInsight basado en Linux. Un nodo perimetral vacío es una máquina virtual Linux con las mismas herramientas de cliente instaladas y configuradas que en los nodos principales, pero sin que se ejecuten servicios de Hadoop. Se puede usar el nodo perimetral para acceder al clúster y para probar y hospedar las aplicaciones cliente. 

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

Como se muestra en el ejemplo, opcionalmente puede llamar a una [acción de script](hdinsight-hadoop-customize-cluster-linux.md) para realizar configuraciones adicionales, como instalar [Apache Hue](hdinsight-hadoop-hue-linux.md) en el nodo perimetral.

Después de haber creado un nodo perimetral, puede conectarse a él mediante SSH y ejecutar herramientas de cliente para acceder al clúster de Hadoop en HDInsight.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Adición de un nodo perimetral a un clúster existente
En esta sección, usará una plantilla de Resource Manager para agregar un nodo perimetral a un clúster de HDInsight existente.  La plantilla de Resource Manager se puede encontrar en [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/en-us/resources/templates/101-hdinsight-linux-with-edge-node/). La plantilla de Resource Manager llama a un script de acción de script situado en https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. El script no realiza ninguna acción.  Sirve para demostrar la llamada a la acción de script desde una plantilla de Resource Manager.

**Para agregar un nodo perimetral vacío a un clúster existente**

1. Si todavía no tiene uno, cree un clúster de HDInsight.  Consulte [Tutorial de Hadoop: Introducción al uso de Hadoop en HDInsight basado en Linux](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Haga clic en la imagen siguiente para iniciar sesión en Azure y abrir la plantilla de Azure Resource Manager en Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Configure las siguientes propiedades:
   
   * **Suscripción**: seleccione la suscripción de Azure usada para crear este clúster.
   * **Grupo de recursos**: seleccione el grupo de recursos que se usa para el clúster de HDInsight existente.
   * **Ubicación**: seleccione la ubicación del clúster de HDInsight existente.
   * **Nombre del clúster**: escriba el nombre de un clúster de HDInsight existente.
   * **Tamaño de nodo perimetral**: seleccione uno de los tamaños de máquina virtual.
   * **Prefijo de nodo perimetral**: el valor predeterminado es **new**.  Usando el valor predeterminado, el nombre del nodo perimetral es **new-edgenode**.  Puede personalizar el prefijo desde el portal. También puede personalizar el nombre completo desde la plantilla.
4. Seleccione **Acepto los términos y condiciones indicadas anteriormente** y, después, haga clic en **Comprar** para crear el nodo perimetral.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Adición de un nodo perimetral al crear un clúster
En esta sección, usará una plantilla de Resource Manager para crear un clúster de HDInsight con un nodo perimetral.  La plantilla de Resource Manager se puede encontrar en la galería [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). La plantilla de Resource Manager llama a un script de acción de script situado en https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. El script no realiza ninguna acción.  Sirve para demostrar la llamada a la acción de script desde una plantilla de Resource Manager.

**Para agregar un nodo perimetral vacío a un clúster existente**

1. Si todavía no tiene uno, cree un clúster de HDInsight.  Consulte [Tutorial de Hadoop: Introducción al uso de Hadoop en HDInsight basado en Linux](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Haga clic en la imagen siguiente para iniciar sesión en Azure y abrir la plantilla de Azure Resource Manager en Azure Portal. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
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
El punto de conexión ssh del nodo perimetral es <EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22.  Por ejemplo, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

El nodo perimetral aparece como una aplicación en Azure Portal.  El portal le proporciona la información para acceder al nodo perimetral mediante SSH.

**Para comprobar el punto de conexión SSH del nodo perimetral**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Abra el clúster de HDInsight con un nodo perimetral.
3. Haga clic en **Aplicaciones** en la hoja del clúster. Verá el nodo perimetral.  El nombre predeterminado es **new-edgenode**.
4. Haga clic en el nodo perimetral. Verá el punto de conexión SSH.

**Para usar Hive en el nodo perimetral**

1. Use SSH para conectarse al nodo perimetral.  Consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md) o [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
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




<!--HONumber=Nov16_HO3-->


