<properties
	pageTitle="Administración de clústeres de Hadoop basados en Linux en HDInsight usando el portal de Azure | Microsoft Azure"
	description="Aprenda a crear y administrar clústeres de HDInsight basados en Linux mediante el portal de Azure."
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
	ms.date="03/30/2016"
	ms.author="larryfr"/>

#Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]


Con el [Portal de Azure][preview-portal] puede aprovisionar y administrar clústeres de Hadoop basados en Linux en HDInsight de Azure.

> [AZURE.NOTE] Los pasos descritos en este documento son específicos para trabajar con clústeres de Hadoop basados en Linux. Para obtener información sobre cómo trabajar con clústeres basados en Windows, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure](hdinsight-administer-use-management-portal.md).


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## Otras herramientas de administración de HDInsight
También hay otras herramientas disponibles para administrar HDInsight además del Portal de Azure.

- [Administrar HDInsight mediante la CLI de Azure](hdinsight-administer-use-command-line.md): la CLI de Azure es una herramienta de línea de comandos multiplataforma que le permite administrar los servicios de Azure

- [Administrar HDInsight mediante Azure PowerShell](hdinsight-administer-use-powershell.md): Azure PowerShell proporciona cmdlets de PowerShell para administrar los servicios de Azure

## Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Consultar [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

## Creación de clústeres de HDInsight

Puede crear clústeres de HDInsight desde el Portal de Azure. Esto se explica en detalle en el documento [Crear clústeres basados en Linux en HDInsight con el Portal de Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

## Administración de un clúster

Si selecciona un clúster desde el Portal de Azure, aparecerá información esencial sobre el clúster, como el nombre, el grupo de recursos, el sistema operativo y la dirección URL del clúster (que se usa para tener acceso a la web de Ambari para clústeres basados en Linux).

![Detalles del clúster](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

Use la siguiente información para comprender los iconos de la parte superior de esta hoja y de las secciones __Conceptos básicos__ y __Vínculos rápidos__:

* __Configuración__ y __Toda la configuración__: Muestra la hoja __Configuración__ del clúster, que permite obtener acceso a información de configuración detallada para el clúster.

* __Panel__, __Panel de clúster__ y __Dirección URL__: todas son formas de acceder al panel del clúster. Según el tipo de clúster, puede aparecer una lista de paneles en el clúster. Por ejemplo, el tipo de clúster de Spark mostrará una lista de paneles cuando se selecciona el icono __Panel__, mientras que un clúster de Hadoop abrirá la interfaz de usuario web de Ambari.

* __Shell seguro__: información necesaria para acceder al nodo principal del clúster mediante SSH.

* __Escalar clúster__: Permite cambiar el número de nodos de trabajo para este clúster.

* __Eliminar__: elimina el clúster de HDInsight.

* __Inicio rápido (![icono de nube y rayo = inicio rápido](./media/hdinsight-administer-use-portal-linux/quickstart.png))__: muestra información que le ayudará a empezar a usar HDInsight.

* __Usuarios (![icono de usuarios](./media/hdinsight-administer-use-portal-linux/users.png))__: permite establecer permisos para la _administración del portal_ de este clúster para otros usuarios de su suscripción de Azure.

	> [AZURE.IMPORTANT] Esto _solo_ afecta al acceso y los permisos para este clúster en el Portal de Azure, y no tiene ningún efecto sobre quién puede conectarse o enviar trabajos al clúster de HDInsight.

* __Etiquetas (![icono de etiqueta](./media/hdinsight-administer-use-portal-linux/tags.png))__: las etiquetas permiten establecer pares de clave-valor para definir una taxonomía personalizada de sus servicios en la nube. Por ejemplo, puede crear una clave denominada __proyecto__ y luego usar un valor común para todos los servicios asociados a un proyecto específico.

* __Documentación__: vínculos a la documentación de HDInsight de Azure.

> [AZURE.IMPORTANT] Para administrar los servicios que proporciona el clúster de HDInsight, debe usar la web de Ambari o la API de REST de Ambari. Para más información sobre el uso de Ambari, vea [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md).

### Settings

Si selecciona el icono __Configuración__ o el vínculo __Toda la configuración__, se mostrará la hoja Configuración. Aquí también están disponibles algunas de las funciones disponibles en el área __Conceptos básicos__ que se mencionó anteriormente, como la información de Escalado o Shell seguro. También puede acceder a lo siguiente desde Configuración:

* __Registros de auditoría__: información registrada que es útil para el diagnóstico de problemas con el mantenimiento de clústeres.

* __Inicio de sesión de clúster__: muestra el __Nombre de usuario de inicio de sesión de clúster__ y la __Dirección remota__ que se pueden usar para acceder al clúster mediante HTTPS.

* __Tiendas de metadatos externas__: muestra información sobre las tiendas de metadatos externas que usa el clúster, si existe alguna. Si no ha configurado una tienda de metadatos personalizada durante la configuración del clúster, no se mostrará ninguna información.

* __Acciones de script__: muestra información sobre las acciones de script que se han ejecutado en este clúster. También puede ejecutar acciones de script nuevas y conservar o eliminar las acciones de script que ya se han ejecutado. Para obtener más información sobre las acciones de script, vea [Personalización de clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-linux.md).

* __Aplicaciones__: muestra información sobre las aplicaciones que se han instalado en el clúster y permite agregar nuevas aplicaciones al clúster desde Azure Marketplace.

* __Claves de almacenamiento de Azure__: muestra información sobre las cuentas de almacenamiento de Azure que usa el clúster. Si selecciona una cuenta de almacenamiento, se cargará la hoja Cuenta de almacenamiento de la cuenta seleccionada.

* __Identidad AAD de clúster__: muestra información sobre la entidad de servicio para este clúster de HDInsight. La entidad de servicio se usa para acceder al Almacén de Azure Data Lake. Si no asoció el clúster con el Almacén de Azure Data Lake durante la creación del clúster, en las entradas de esta hoja se mostrará __No configurado__. Para obtener más información sobre el uso del Almacén de Azure Data Lake con HDInsight, consulte [Creación de un clúster de HDInsight con el Almacén de Data Lake](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="scaling"></a>Escalado

Para escalar un clúster mediante el portal, seleccione el clúster de HDInsight y elija __Escalar clúster__. Escriba el __número de nodos de trabajo__ que quieres establecer para el clúster y después haz clic en __Guardar__.

![imagen de la interfaz de usuario de escalado](./media/hdinsight-administer-use-portal-linux/scaling.png)

Para más información sobre cómo ampliar las operaciones, vea [Información sobre el uso de HDInsight en Linux](hdinsight-hadoop-linux-information.md#scaling).

## Supervisión de un clúster

La sección __Uso__ de la hoja del clúster de HDInsight muestra información sobre el número de núcleos disponibles con la suscripción para su uso con HDInsight, así como el número de núcleos asignados al clúster y cómo se asignan a los nodos de este clúster.

![Información de uso](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT] Para supervisar los servicios que proporciona el clúster de HDInsight, debe usar la web de Ambari o la API de REST de Ambari. Para obtener más información sobre el uso de Ambari, consulte [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md)

## Pasos siguientes
En este artículo, ha aprendido a crear un clúster de HDInsight mediante el Portal de Azure y a abrir la herramienta de línea de comandos de Hadoop. Para obtener más información, consulte los artículos siguientes:

* [Administración de HDInsight con Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administración de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md)
* [Aprovisionamiento de clústeres de HDInsight](hdinsight-provision-clusters.md)
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md)
* [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0406_2016-->