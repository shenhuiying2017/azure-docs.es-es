<properties 
	pageTitle="Personalizar los clústeres de Hadoop para HDInsight Azure de ciencia de datos | Azure" 
	description="Personalizar los clústeres de Hadoop para HDInsight Azure de ciencia de datos" metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="hangzh;bradsev" />

# Personalizar los clústeres de Hadoop para HDInsight Azure de ciencia de datos

En este artículo se describe cómo personalizar un clúster de Hadoop para HDInsight mediante la instalación de Anaconda de 64 bits (Python 2.7) en cada nodo cuando el clúster se está aprovisionando en el servicio HDInsight. También muestra cómo obtener acceso al nodo principal para enviar trabajos personalizados al clúster.

Esta personalización provoca que muchos módulos populares de Python incluidos en Anaconda estén disponibles convenientemente para su uso en funciones definidas por el usuario (UDF) que están diseñadas para procesar los registros de subárbol en el clúster. Para obtener instrucciones sobre los procedimientos usados en este escenario, consulte [Enviar consultas de subárbol a clústeres de Hadoop de HDInsight en el proceso de ciencia de datos en la nube](machine-learning-data-science-hive-queries.md).

## <a name="customize"></a>Personalizar el clúster de Hadoop de HDInsight de Azure

Para crear un clúster de Hadoop de HDInsight personalizado, los usuarios deben iniciar sesión en [**Portal de administración de Azure**](https://manage.windowsazure.com/), hacer clic en **Nuevo** en la esquina inferior izquierda y, a continuación, seleccionar SERVICIOS DE DATOS -> HDINSIGHT -> **CREACIÓN PERSONALIZADA** para que aparezca la ventana **Detalles del clúster**. 

![Create workspace][1]

Especifique el nombre del clúster a crear en la página de configuración 1 y acepte los valores predeterminados para los demás campos. Haga clic en la flecha para ir a la página de configuración siguiente. 

![Create workspace][2]

En la página de configuración 2, escriba el número de **NODOS DE DATOS**, seleccione la **REGIÓN/RED VIRTUAL** y seleccione los tamaños del **NODO PRINCIPAL** y del **NODO DE DATOS**. Haga clic en la flecha para ir a la página de configuración siguiente.

>[AZURE.NOTE] La **REGIÓN/RED VIRTUAL** tiene que ser la misma que la región de la cuenta de almacenamiento que se va a usar para el clúster de Hadoop de HDInsight. De lo contrario, en la cuarta página de configuración, la cuenta de almacenamiento que los usuarios desean utilizar no aparecerá en la lista desplegable de **NOMBRE DE LA CUENTA**.

![Create workspace][3]

En la página de configuración 3, proporcione un nombre de usuario y una contraseña para el clúster de Hadoop de HDInsight. **No** seleccione _Enter the Hive/Oozie Metastore_. A continuación, haga clic en la flecha para ir a la página de configuración siguiente. 

![Create workspace][4]

En la página de configuración 4, especifique el nombre de la cuenta de almacenamiento, el contenedor predeterminado del clúster de Hadoop de HDInsight. Si los usuarios seleccionan _Create default container_ en la lista desplegable **CONTENEDOR PREDETERMINADO**, se creará un contenedor con el mismo nombre que el del clúster. Haga clic en la flecha para ir a la última página de configuración.

![Create workspace][5]

En la última página de configuración de **Acciones de script**, haga clic en el botón **Agregar acción de script** y rellene los campos de texto con los valores siguientes.
 
* **NOMBRE**: cualquier cadena como el nombre de esta acción de script. 
* **TIPO DE NODO**: seleccione **Todos los nodos**. 
* **URI DE SCRIPT**:  *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1* 
	* *publicscripts* es un contenedor público situado en la cuenta de almacenamiento 
	* *getgoing* se utiliza para compartir archivos de script de PowerShell para facilitar el trabajo de los usuarios en Azure. 
* **PARÁMETROS**: (dejar en blanco)

Por último, haga clic en la marca de verificación para iniciar la creación del clúster Hadoop de HDInsight personalizado. 

![Create workspace][6]

## <a name="headnode"></a> Acceso al nodo principal del clúster de Hadoop

Los usuarios deben habilitar el acceso remoto al clúster de Hadoop en Azure para poder acceder al nodo principal del clúster de Hadoop a través de RDP. 

1. Inicie sesión en el [**Portal de administración de Azure**](https://manage.windowsazure.com/), seleccione **HDInsight** a la izquierda, seleccione el clúster de Hadoop en la lista de clústeres, haga clic en la ficha **CONFIGURACIÓN** y a continuación, haga clic en el icono **HABILITAR REMOTO** de la parte inferior de la página.
	
	![Create workspace][7]

2. En la ventana **Configurar escritorio remoto**, escriba los campos NOMBRE DE USUARIO y CONTRASEÑA y seleccione la fecha de expiración del acceso remoto. A continuación, haga clic en la marca de verificación para habilitar el acceso remoto en el nodo principal del clúster de Hadoop.
	
	>[AZURE.NOTE] 
	>
	>1. El nombre de usuario y la contraseña para el acceso remoto no son el nombre de usuario y la contraseña que se utilizaron al crear el clúster de Hadoop. Se trata de un conjunto de credenciales diferente
	>
	>2. La fecha de expiración del acceso remoto debe estar dentro de 7 días respecto a la fecha actual.

	![Create workspace][8]

3. Después de habilitar el acceso remoto, haga clic en **CONECTAR** en la parte inferior de la página para conectarse de manera remota al nodo principal. Inicie sesión en el nodo principal del clúster de Hadoop mediante la especificación de las credenciales del usuario de acceso remoto que especificó anteriormente.

	 ![Create workspace][9]

A continuación, consulte [Enviar consultas de subárbol a clústeres de Hadoop de HDInsight en el proceso de ciencia de datos en la nube](machine-learning-data-science-hive-queries.md) para obtener instrucciones sobre cómo tener acceso a los módulos de Python que se incluyen en Anaconda desde el nodo principal del clúster en las funciones definidas por el usuario (UDF) que se utilizan para procesar registros de subárbol almacenados en el clúster.

[1]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png
[2]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img2.png
[3]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png
[4]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png
[5]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png
[6]: ./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png
[7]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png
[8]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png
[9]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png


<!--HONumber=49-->