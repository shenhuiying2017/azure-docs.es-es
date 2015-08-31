<properties
	pageTitle="Administración de clústeres de Hadoop en HDInsight usando el portal de Azure | Microsoft Azure"
	description="Vea cómo administrar el servicio HDInsight. Cree un clúster de HDInsight, abra la consola interactiva de JavaScript y la consola de comandos de Hadoop."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/06/2015"
	ms.author="larryfr"/>

# Administración de clústeres de Hadoop en HDInsight mediante el portal de vista previa de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]


Con el [portal de vista previa de Azure][preview-portal] puede aprovisionar y administrar clústeres de Hadoop basados en Linux en HDInsight de Azure.

> [AZURE.NOTE]Los pasos descritos en este documento son específicos para trabajar con clústeres de Hadoop basados en Linux. Para obtener información sobre cómo trabajar con clústeres basados en Windows, consulte [Administración de clústeres de Hadoop en HDInsight mediante el portal de vista previa de Azure](hdinsight-administer-use-management-portal.md).


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## Otras herramientas de administración de HDInsight
También hay otras herramientas disponibles para administrar HDInsight además del Portal de Azure.

- [Administrar HDInsight mediante la CLI de Azure](hdinsight-administer-use-command-line.md): la CLI de Azure es una herramienta de línea de comandos multiplataforma que le permite administrar los servicios de Azure

- [Administrar HDInsight mediante Azure PowerShell](hdinsight-administer-use-powershell.md): Azure PowerShell proporciona cmdlets de PowerShell para administrar los servicios de Azure

##Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Consultar [Obtención de una versión de evaluación gratuita](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

##Aprovisionamiento de clústeres de HDInsight

Puede aprovisionar clústeres de HDInsight desde el portal de Azure si sigue estos pasos:

1. Inicie sesión en el [Portal de vista previa de Azure][preview-portal].

2. Seleccione **NUEVO**, __Análisis de datos__ y __HDInsight__

	![Crear un nuevo clúster en el Portal de vista previa de Azure](./media/hdinsight-administer-use-portal-linux/new-cluster.png)

3. Escriba un __Nombre de clúster__ y seleccione el __Tipo de clúster__ que desea crear. Si está disponible, aparecerá una marca de verificación verde junto al __Nombre de clúster__.

	![Nombre del clúster, tipo de clúster y tipo de sistema operativo](./media/hdinsight-administer-use-portal-linux/clustername.png)
	
	> [AZURE.NOTE]En la vista previa de HDInsight basado en Linux, solo está disponible el tipo de clúster de Hadoop.

4. Si tiene más de una suscripción, seleccione la entrada __Suscripción__ entrada para elegir la suscripción de Azure que se usará para el clúster.

5. En cuanto al __Grupo de recursos__, puede seleccionar la entrada para ver una lista de grupos de recursos existentes y seleccionar en el que desea crear el clúster. También puede seleccionar __Crear nuevo__ y escribir el nombre del nuevo grupo de recursos. Aparecerá una marca de verificación verde para indicar si el nuevo nombre de grupo está disponible.

	> [AZURE.NOTE]Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.

6. Seleccione __Credenciales__, especifique una __Contraseña de inicio de sesión de clúster__ y un __Nombre de usuario de inicio de sesión de clúster__. También debe especificar un__ Nombre de usuario de SSH__ y una __CONTRASEÑA__ o una __CLAVE PÚBLICA__, que se usará para autenticar al usuario de SSH. Por último, use el botón __Seleccionar__ para establecer las credenciales. Escritorio remoto no se usará en este documento, por lo que puede dejarlo deshabilitado.

	![Hoja Credenciales de clúster](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)

	Para obtener más información sobre el uso de SSH con HDInsight, vea uno de los siguientes artículos:

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows)

6. Como __Origen de datos__, puede seleccionar la entrada para elegir un origen de datos existente o crear uno nuevo.

	![Hoja Origen de datos](./media/hdinsight-administer-use-portal-linux/datasource.png)
	
	Actualmente puede seleccionar una cuenta de almacenamiento de Azure como origen de datos para un clúster de HDInsight. Use lo siguiente para comprender las entradas de la hoja __Origen de datos__.
	
	- __Método de selección__: establézcalo como __De todas las suscripciones__ para habilitar la exploración de cuentas de almacenamiento en sus suscripciones. Establézcalo como __Tecla de acceso__ si desea especificar el __Nombre de almacenamiento__ y la __Tecla de acceso__ de una cuenta de almacenamiento existente.
	
	- __Crear nuevo__: use esta opción para crear una nueva cuenta de almacenamiento. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.
	
	- __Elegir contenedor predeterminado__: use esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.
	
	- __Ubicación__: la región geográfica en la que se encuentra o donde se creará la cuenta de almacenamiento.
	
		> [AZURE.IMPORTANT]Al seleccionar la ubicación del origen de datos predeterminado también establecerá la ubicación del clúster de HDInsight. El origen de datos predeterminado y el clúster deben encontrarse en la misma región.
		
	- __Seleccionar__: use esta opción para guardar la configuración del origen de datos.
	
7. Seleccione __Niveles de precios de nodo__ para mostrar información sobre los nodos que se crearán para este clúster. De forma predeterminada, el número de nodos de trabajo se establecerá en __4__. Establezca esta propiedad en __1__, ya que esto será suficiente para este tutorial y reducirá el costo del clúster. También puede cambiar el número de nodos en un clúster después su aprovisionamiento.

	El costo estimado del clúster se mostrará en la parte inferior de esta hoja.

	![Hoja Niveles de precios de nodo](./media/hdinsight-administer-use-portal-linux/nodepricingtiers.png)
	
	Use el botón __Seleccionar__ para guardar la información de los __Niveles de precios de nodo__.

8. Seleccione __Configuración opcional__. Esta hoja le permite configurar los siguientes elementos:

	* __Versión de HDInsight__: la versión de HDInsight que se usa para el clúster. Para obtener más información sobre las versiones de HDInsight, consulte [Versiones de los componentes de HDInsight](hdinsight-component-versioning.md)
	* __Tiendas de metadatos externas__: permite seleccionar una Base de datos SQL, que se usará para almacenar la información de configuración de Oozie y Hive. Esto permite reutilizar la configuración cuando se elimina y se vuelve a crear un clúster, en lugar de tener que volver a crear la configuración de Oozie y Hive cada vez. **__Claves de almacenamiento de Azure__: permite asociar cuentas de almacenamiento adicionales con el servidor de HDInsight.

		> [AZURE.NOTE]HDInsight solo puede tener acceso a las cuentas de almacenamiento de Azure que se usen como almacén de datos predeterminado, se hayan agregado a través de esta sección de configuración o sean accesibles públicamente.

	![Hoja Configuración opcional](./media/hdinsight-administer-use-portal-linux/optionalconfiguration.png)

9. Asegúrese de que __Anclar a Panel de inicio__ está seleccionado y, después, seleccione __Crear__. Esto creará el clúster y agregará un icono para él en el panel de inicio de su Portal de Azure. El icono indicará que el clúster está aprovisionando y cambiará para mostrar el icono de HDInsight cuando se haya completado el proceso.

	| Durante el aprovisionamiento | Aprovisionamiento completado |
	| ------------------ | --------------------- |
	| ![Indicador de aprovisionamiento en el panel de inicio](./media/hdinsight-administer-use-portal-linux/provisioning.png) | ![Icono de clúster aprovisionado](./media/hdinsight-administer-use-portal-linux/provisioned.png) |

	> [AZURE.NOTE]El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del Panel de inicio o la entrada __Notificaciones__ de la izquierda de la página para comprobar el estado del proceso de aprovisionamiento.

##Administración de un clúster

Si selecciona un clúster desde el portal de vista previa de Azure, aparecerá información esencial sobre el clúster, como el nombre, el grupo de recursos, el sistema operativo y la dirección URL del panel de clúster (que se usa para tener acceso a la web de Ambari para clústeres de Linux).

![Detalles del clúster](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

Consulte la información siguiente para comprender los iconos de la parte superior de esta hoja y la que encontrará en las secciones __Conceptos básicos__ y __Vínculos rápidos__:

* __Configuración__ y __Toda la configuración__: muestra la hoja __Configuración__ del clúster, que permite obtener acceso a información de configuración detallada del clúster.

* __Panel__, __Panel de clúster__ y __URL__: se trata de formas de tener acceso al panel del clúster, que es la web de Ambari para clústeres de Linux.

* __Shell seguro__: información necesaria para tener acceso al clúster mediante SSH.

* __Escalar clúster__: permite cambiar el número de nodos de trabajo de este clúster.

* __Eliminar__: elimina el clúster de HDInsight.

* __Inicio rápido(![icono de nube y rayo = inicio rápido](./media/hdinsight-administer-use-portal-linux/quickstart.png))__: muestra información que le ayudará a empezar a usar HDInsight.

* __Usuarios (![icono de usuarios](./media/hdinsight-administer-use-portal-linux/users.png))__: permite establecer permisos para la _administración del portal_ de este clúster para otros usuarios de su suscripción de Azure.

	> [AZURE.IMPORTANT]Esto _solo_ afecta al acceso y los permisos de este clúster en el portal de vista previa de Azure, y no tiene ningún efecto sobre quién puede conectarse o enviar trabajos al clúster de HDInsight.

* __Etiquetas (![icono de etiqueta](./media/hdinsight-administer-use-portal-linux/tags.png))__: las etiquetas permiten establecer pares clave/valor para definir una taxonomía personalizada de sus servicios en la nube. Por ejemplo, puede crear una clave denominada __proyecto__ y así usar un valor común para todos los servicios asociados a un proyecto específico.

* __Documentación__: vínculos a la documentación de HDInsight de Azure.

> [AZURE.IMPORTANT]Para administrar los servicios que proporciona el clúster de HDInsight, debe usar la web de Ambari o la API de REST de Ambari. Para obtener más información sobre el uso de Ambari, consulte [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md).

##Supervisión de un clúster

La sección __Uso__ de la hoja del clúster de HDInsight muestra información sobre el número de núcleos disponibles con su suscripción para su uso con HDInsight, así como el número de núcleos asignados a este clúster y cómo se asignan para los nodos de este clúster.

![Información de uso](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT]Para supervisar los servicios que proporciona el clúster de HDInsight, debe usar la web de Ambari o la API de REST de Ambari. Para obtener más información sobre el uso de Ambari, consulte [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md)

##Pasos siguientes
En este artículo, ha aprendido a crear un clúster de HDInsight mediante el Portal de Azure y a abrir la herramienta de línea de comandos de Hadoop. Para obtener más información, consulte los artículos siguientes:

* [Administración de HDInsight con PowerShell de Azure](hdinsight-administer-use-powershell.md)
* [Administración de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md)
* [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md)
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introducción a HDInsight de Azure](../hdinsight-get-started.md)
* [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=August15_HO8-->