<properties 
	pageTitle="Personalización de los clústeres de Hadoop para el proceso de ciencia de datos en equipos | Microsoft Azure" 
	description="Están disponibles módulos de Python populares en los clústeres de Hadoop de HDInsight de Azure personalizados."
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/14/2016" 
	ms.author="hangzh;bradsev" />

# Personalización de los clústeres de Hadoop de HDInsight de Azure para el proceso de ciencia de datos en equipos | Azure 

En este artículo se describe cómo personalizar un clúster de Hadoop para HDInsight mediante la instalación de Anaconda de 64 bits (Python 2.7) en cada nodo cuando el clúster se aprovisiona como un servicio HDInsight. También muestra cómo obtener acceso al nodo principal para enviar trabajos personalizados al clúster. Esta personalización provoca que muchos módulos populares de Python incluidos en Anaconda estén disponibles convenientemente para su uso en funciones definidas por el usuario (UDF) que están diseñadas para procesar los registros de subárbol en el clúster. Para obtener instrucciones sobre los procedimientos utilizados en este escenario, consulte [Cómo enviar consultas de Hive](machine-learning-data-science-move-hive-tables.md#submit).

El menú siguiente redirige a temas en los que se describe cómo configurar los diversos entornos de ciencia de datos que usa el proceso de ciencia de datos en equipos (TDSP).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]


## <a name="customize"></a>Personalizar el clúster de Hadoop de HDInsight de Azure

Para crear un clúster de Hadoop de HDInsight personalizado, los usuarios deben iniciar sesión en [**Portal de Azure clásico**](https://manage.windowsazure.com/), hacer clic en **Nuevo** en la esquina inferior izquierda y, a continuación, seleccionar SERVICIOS DE DATOS -> HDINSIGHT -> **CREACIÓN PERSONALIZADA** para que aparezca la ventana **Detalles del clúster**.

![Creación del espacio de trabajo](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png)

Especifique el nombre del clúster a crear en la página de configuración 1 y acepte los valores predeterminados para los demás campos. Haga clic en la flecha para ir a la página de configuración siguiente.

![Creación del espacio de trabajo](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png)

En la página de configuración 2, escriba el número de **NODOS DE DATOS**, seleccione la **REGIÓN/RED VIRTUAL** y seleccione los tamaños del **NODO PRINCIPAL** y del **NODO DE DATOS**. Haga clic en la flecha para ir a la página de configuración siguiente.

>[AZURE.NOTE] La **REGIÓN/RED VIRTUAL** tiene que ser la misma que la región de la cuenta de almacenamiento que se va a usar para el clúster de Hadoop de HDInsight. De lo contrario, en la cuarta página de configuración, la cuenta de almacenamiento que los usuarios desean utilizar no aparecerá en la lista desplegable de **NOMBRE DE LA CUENTA**.

![Creación del espacio de trabajo](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png)

En la página de configuración 3, proporcione un nombre de usuario y una contraseña para el clúster de Hadoop de HDInsight. **No** seleccione _Especificar la tienda de metadatos de Hive/Oozie_. A continuación, haga clic en la flecha para ir a la página de configuración siguiente.

![Creación del espacio de trabajo](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png)

En la página de configuración 4, especifique el nombre de la cuenta de almacenamiento, el contenedor predeterminado del clúster de Hadoop de HDInsight. Si los usuarios seleccionan _Crear contenedor predeterminado_ en la lista desplegable **CONTENEDOR PREDETERMINADO**, se creará un contenedor con el mismo nombre que el del clúster. Haga clic en la flecha para ir a la última página de configuración.

![Creación del espacio de trabajo](./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png)

En la última página de configuración de **Acciones de script**, haga clic en el botón **Agregar acción de script** y rellene los campos de texto con los valores siguientes.
 
* **NOMBRE**: cualquier cadena como el nombre de esta acción de script. 
* **TIPO DE NODO**: seleccione **Todos los nodos**. 
* **URI DE SCRIPT**: **http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1*
	* *publicscripts* es un contenedor público situado en la cuenta de almacenamiento. 
	* *getgoing* se usa para compartir archivos de script de PowerShell para facilitar el trabajo de los usuarios en Azure. 
* **PARÁMETROS**: (dejar en blanco)

Por último, haga clic en la marca de verificación para iniciar la creación del clúster Hadoop de HDInsight personalizado.

![Creación del espacio de trabajo](./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png)

## <a name="headnode"></a> Acceso al nodo principal del clúster de Hadoop

Los usuarios deben habilitar el acceso remoto al clúster de Hadoop en Azure para poder acceder al nodo principal del clúster de Hadoop a través de RDP.

1. Inicie sesión en el [**Portal de Azure clásico**](https://manage.windowsazure.com/), seleccione **HDInsight** a la izquierda, seleccione el clúster de Hadoop en la lista de clústeres, haga clic en la pestaña **CONFIGURACIÓN** y a continuación, haga clic en el icono **HABILITAR REMOTO** de la parte inferior de la página.
	
	![Creación del espacio de trabajo](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png)

2. En la ventana **Configurar escritorio remoto**, escriba los campos NOMBRE DE USUARIO y CONTRASEÑA y seleccione la fecha de expiración del acceso remoto. A continuación, haga clic en la marca de verificación para habilitar el acceso remoto en el nodo principal del clúster de Hadoop.

	![Creación del espacio de trabajo](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png)
	
>[AZURE.NOTE] El nombre de usuario y la contraseña para el acceso remoto no son el nombre de usuario y la contraseña que se utilizaron al crear el clúster de Hadoop. Se trata de un conjunto de credenciales independiente. Asimismo, la fecha de expiración del acceso remoto debe estar dentro de 7 días respecto a la fecha actual.

Después de habilitar el acceso remoto, haga clic en **CONECTAR** en la parte inferior de la página para conectarse de manera remota al nodo principal. Inicie sesión en el nodo principal del clúster de Hadoop mediante la especificación de las credenciales del usuario de acceso remoto que especificó anteriormente.

![Creación del espacio de trabajo](./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png)

Los pasos siguientes del proceso de análisis avanzado se asignan en el [proceso de ciencia de datos en equipos (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) y pueden incluir otros que muevan datos a HDInsight, los procese y muestree en esa plataforma con el fin de prepararlos para el aprendizaje a partir de los datos mediante Aprendizaje automático de Azure.

Consulte [Cómo enviar consultas de Hive](machine-learning-data-science-move-hive-tables.md#submit) para obtener instrucciones sobre cómo tener acceso a los módulos de Python que se incluyen en Anaconda desde el nodo principal del clúster en las funciones definidas por el usuario (UDF) que se usan para procesar registros de Hive almacenados en el clúster.

 

<!---HONumber=AcomDC_0622_2016-->