<properties
	pageTitle="Uso de nodos perimetrales vacíos en HDInsight | Microsoft Azure"
	description="Procedimiento para agregar un nodo perimetral vacío a un clúster de HDInsight que se puede usar como cliente, y cómo probar u hospedar las aplicaciones de HDInsight."
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="jgao"/>

# Uso de nodos perimetrales vacíos en HDInsight

Aprenda a agregar un nodo perimetral vacío a un clúster de HDInsight basado en Linux. Un nodo perimetral vacío es una máquina virtual Linux con las mismas herramientas de cliente instaladas y configuradas que en los nodos principales, pero sin que se ejecuten servicios de Hadoop. Se puede usar el nodo perimetral para acceder al clúster y para probar y hospedar las aplicaciones cliente.

Un nodo perimetral vacío se puede agregar a un clúster de HDInsight existente o a uno nuevo al crear el clúster. La adición de un nodo perimetral vacío se realiza mediante una plantilla de Azure Resource Manager. En el ejemplo siguiente se muestra cómo se hace esto mediante una plantilla:

    "resources": [
		{
			"name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
			"type": "Microsoft.HDInsight/clusters/applications",
			"apiVersion": "[variables('clusterApiVersion')]",
			"properties": {
				"marketPlaceIdentifier": "EmptyNode",
				"computeProfile": {
					"roles": [{
						"name": "edgenode",
						"targetInstanceCount": 1,
						"hardwareProfile": {
							"vmSize": "[parameters('edgeNodeSize')]"
						}
					}]
				},
				"installScriptActions": [{
					"name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
					"uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
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

## Adición de un nodo perimetral a un clúster existente

En esta sección, usará una plantilla de Resource Manager para agregar un nodo perimetral a un clúster de HDInsight existente. La plantilla de Resource Manager se puede encontrar en [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode). Dicha plantilla llama a una acción de script ubicada en https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. El script no realiza ninguna acción. Sirve para demostrar la llamada a la acción de script desde una plantilla de Resource Manager.

**Para agregar un nodo perimetral vacío a un clúster existente**

1. Si todavía no tiene uno, cree un clúster de HDInsight. Consulte [Tutorial de Hadoop: Introducción al uso de Hadoop en HDInsight basado en Linux](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Haga clic en la imagen siguiente para iniciar sesión en Azure y abrir la plantilla de Azure Resource Manager en Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configure las siguientes propiedades:

	- CLUSTERNAME: escriba el nombre de un clúster de HDInsight existente.
	- EDGENODESIZE: seleccione uno de los tamaños de máquina virtual.
	- EDGENODEPREFIX: el valor predeterminado es **new**. Usando el valor predeterminado, el nombre del nodo perimetral es **new-edgenode**. Puede personalizar el prefijo desde el portal. También puede personalizar el nombre completo desde la plantilla.


4. Haga clic en **Aceptar** para guardar los cambios.
5. En **Grupo de recursos**, seleccione un grupo de recursos.
6. Haga clic en **Revisar los términos legales** y luego en **Comprar**.
7. Seleccione **Anclar al panel** y haga clic en **Crear**.

## Adición de un nodo perimetral al crear un clúster

En esta sección, usará una plantilla de Resource Manager para crear un clúster de HDInsight con un nodo perimetral. La plantilla de Resource Manager se puede encontrar en un [contenedor de Azure Blob Storage](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json) público. Dicha plantilla llama a una acción de script ubicada en https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. El script no realiza ninguna acción. Sirve para demostrar la llamada a la acción de script desde una plantilla de Resource Manager.

**Para agregar un nodo perimetral vacío a un clúster existente**

1. Si todavía no tiene uno, cree un clúster de HDInsight. Consulte [Tutorial de Hadoop: Introducción al uso de Hadoop en HDInsight basado en Linux](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Haga clic en la imagen siguiente para iniciar sesión en Azure y abrir la plantilla de Azure Resource Manager en Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Configure las siguientes propiedades:
		
	- CLUSTERNAME: escriba un nombre para el nuevo clúster que se va a crear.
	- CLUSTERLOGINUSERNAME: escriba el nombre de usuario HTTP de Hadoop. El nombre predeterminado es **admin**.
	- CLUSTERLOGINPASSWORD: escriba la contraseña de usuario HTTP de Hadoop.
	- SSHUSERNAME: escriba el nombre de usuario SSH. El nombre predeterminado es **sshuser**.
	- SSHPASSWORD: escriba la contraseña de usuario SSH.
	- LOCATION: especifique la ubicación del nombre del grupo de recursos, el clúster y la cuenta de almacenamiento predeterminada.
	- CLUSTERTYPE: el valor predeterminado es **hadoop**.
	- CLUSTERWORKERNODECOUNT: el valor predeterminado es 2.
	- EDGENODESIZE: seleccione uno de los tamaños de máquina virtual.
	- EDGENODEPREFIX: el valor predeterminado es **new**. Usando el valor predeterminado, el nombre del nodo perimetral es **new-edgenode**. Puede personalizar el prefijo desde el portal. También puede personalizar el nombre completo desde la plantilla.

4. Haga clic en **Aceptar** para guardar los cambios.
5. En **Grupo de recursos**, escriba un nuevo nombre para el grupo de recursos.
6. Haga clic en **Revisar los términos legales** y luego en **Comprar**.
7. Seleccione **Anclar al panel** y haga clic en **Crear**.


## Acceso a un nodo perimetral

El punto de conexión ssh del nodo perimetral es <NombreNodoPerimetral>.<NombreClúster>-ssh.azurehdinsight.net:22. Por ejemplo, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

El nodo perimetral aparece como una aplicación en Azure Portal. El portal le proporciona la información para acceder al nodo perimetral mediante SSH.

**Para comprobar el punto de conexión SSH del nodo perimetral**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Abra el clúster de HDInsight con un nodo perimetral.
3. Haga clic en **Aplicaciones** en la hoja del clúster. Verá el nodo perimetral. El nombre predeterminado es **new-edgenode**.
4. Haga clic en el nodo perimetral. Verá el punto de conexión SSH.

**Para usar Hive en el nodo perimetral**

5. Use SSH para conectarse al nodo perimetral. Consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md) o [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
6. Después de haberse conectado al nodo perimetral mediante SSH, use el comando siguiente para abrir la consola de Hive:

		hive
7. Ejecute el siguiente comando para mostrar las tablas de Hive en el clúster:

		show tables;

## Eliminación de un nodo perimetral

Puede eliminar un nodo perimetral de Azure Portal.

**Para acceder a un nodo perimetral**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Abra el clúster de HDInsight con un nodo perimetral.
3. Haga clic en **Aplicaciones** en la hoja del clúster. Verá una lista de nodos perimetrales.
4. Haga clic con el botón derecho en el nodo perimetral que quiere eliminar y luego haga clic en **Eliminar**.
5. Haga clic en **Sí** para continuar.

## Pasos siguientes

En este artículo, ha aprendido a agregar un nodo perimetral y a acceder a él. Para obtener más información, consulte los artículos siguientes:

- [Instalación de aplicaciones de HDInsight](hdinsight-apps-install-applications.md): aprenda a instalar una aplicación de HDInsight en sus clústeres.
- [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a implementar en HDInsight una aplicación de HDInsight no publicada.
- [Publicación de aplicaciones de HDInsight en Azure Marketplace](hdinsight-apps-publish-applications.md): aprenda a publicar aplicaciones de HDInsight personalizadas en Azure Marketplace.
- [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: instalación de una aplicación de HDInsight): aprenda a definir aplicaciones de HDInsight.
- [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md): aprenda a usar acciones de script para instalar otras aplicaciones.
- [Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): aprenda a llamar a plantillas de Resource Manager para crear clústeres de HDInsight.

<!---HONumber=AcomDC_0914_2016-->