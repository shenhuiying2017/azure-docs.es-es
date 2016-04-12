<properties
	pageTitle="Creación de una cuenta de Lote de Azure | Microsoft Azure"
	description="Aprenda a crear una cuenta de Lote de Azure en el Portal de Azure para ejecutar cargas de trabajo paralelas a gran escala en la nube"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/18/2016"
	ms.author="marsma"/>

# Creación y administración de una cuenta de Lote de Azure en el Portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](batch-account-create-portal.md)
- [NET de Administración de Lote](batch-management-dotnet.md)

El [Portal de Azure][azure_portal] proporciona las herramientas necesarias para crear y administrar una cuenta de Lote de Azure, que se puede usar para el procesamiento paralelo de cargas de trabajo a gran escala. En este artículo, le guiaremos por el proceso de creación de una cuenta de Lote y se analizarán varias de las opciones y propiedades más importantes de una cuenta de Lote. Por ejemplo, las aplicaciones y servicios que desarrolla con Lote necesitan la dirección URL de su cuenta y una clave de acceso para comunicarse con las API del servicio de Lote, que se encuentran en el Portal de Azure.

>[AZURE.NOTE] Actualmente, el Portal de Azure admite un subconjunto de las características disponibles en el servicio de Lote, donde se incluyen la creación de la cuenta y la administración de la configuración y las propiedades de la cuenta. El conjunto completo de características de Lote, que incluye la creación y ejecución de trabajos y tareas, está disponible para los desarrolladores a través de las API de Lote.

## Crear una cuenta de lote

1. Inicie sesión en el [Portal de Azure][azure_portal].

2. Haga clic en **Nuevo** > **Proceso** > **Servicio de Lote**.

	![Lote en Marketplace][marketplace_portal]

3. Revise la información de la hoja **Servicio de Lote** y haga clic en **Crear**. Tenga en cuenta que la selección del modelo de implementación está deshabilitada. Esto se debe a que Lote usa solo el modelo de implementación de grupos de recursos.

	![Hoja de creación del servicio Lote en el Portal de Azure][3]

4. Se muestra la hoja **Nueva cuenta de Lote**. Para obtener descripciones de cada elemento de la hoja, consulte los elementos *a* a *e*.

    ![Crear una cuenta de lote][account_portal]

	a. **Nombre de cuenta**: especifique un nombre único para la cuenta de Lote. Este nombre debe ser único en la región de Azure en que se crea la cuenta (consulte *Ubicación* a continuación). Puede contener solo caracteres en minúsculas, números y su longitud debe oscilar entre 3 y 24 caracteres.

	b. **Suscripción**: seleccione la suscripción en la que se va a crear la cuenta de Lote. Si tiene una sola suscripción, se selecciona de forma predeterminada.

	c. **Grupo de recursos**: seleccione un grupo de recursos para la nueva cuenta de Lote o cree uno nuevo, en caso de que no haya grupos de recursos en su suscripción.

	d. **Suscripción**: seleccione la región de Azure en la que se va a crear la cuenta de Lote. Solo se mostrarán como opciones las regiones admitidas por su suscripción y grupo de recursos.

    e. **Cuenta de almacenamiento** (opcional): puede asociar (vincular) una cuenta de almacenamiento a su nueva cuenta de Lote. La característica [paquetes de aplicación](batch-application-packages.md) de Lote utilizará la cuenta de almacenamiento vinculado para el almacenamiento y la recuperación de paquetes de aplicación. Para más información acerca de esta característica, consulte [Application deployment with Azure Batch application packages](batch-application-packages.md) (Implementación de aplicaciones con paquetes de aplicaciones de Lote de Azure).

     > [AZURE.TIP] La regeneración de claves en una cuenta de almacenamiento vinculada requiere consideraciones especiales. Para más información, consulte [Creación y administración de una cuenta de Lote de Azure en el Portal de Azure](#considerations-for-batch-accounts).

5. Haga clic en **Crear** para crear la cuenta.

  El portal se indicará que está **implementando** la cuenta y tras la finalización, se mostrará la hoja Cuenta de Lote.

## Visualización de propiedades de la cuenta de Lote

La hoja Cuenta de lote muestra varias propiedades de la cuenta y proporciona acceso a opciones adicionales, como las teclas de acceso, las cuotas, los usuarios y la asociación de la cuenta de almacenamiento.

* **Dirección URL de cuenta de Lote**: esta dirección URL proporciona acceso a su cuenta de Lote cuando se usan API como la API de [REST de Lote][api_rest] o la biblioteca de cliente [.NET de Lote][api_net] y se ajusta al formato siguiente:

  `https://<account_name>.<region>.batch.azure.com`

* **Claves de acceso**: para ver y administrar las claves de acceso de su cuenta de Lote, haga clic en el icono de la llave para abrir la hoja **Administrar claves** o haga clic en **Todas las opciones** > **Claves**. Se requiere una clave de acceso al comunicarse con las API del servicio Lote, como con [REST de Lote][api_rest] o la biblioteca de cliente [.NET de Lote][api_net].

 ![Claves de la cuenta de Lote][account_keys]

* **Todas las opciones**: para administrar todas las opciones de la cuenta de Lote o para ver sus propiedades, haga clic en **Todas las opciones** para abrir la hoja **Configuración**. Esta hoja proporciona acceso a todas las opciones y propiedades de la cuenta, entre las que se incluyen la visualización de las cuotas de la cuenta, la selección de una cuenta de Almacenamiento de Azure para vincularla a la cuenta de Lote y la administración de usuarios.

 ![Hojas de propiedades y configuración de la cuenta de Lote][5]

## Consideraciones de las cuentas de Lote

* Las cuentas de Lote también se pueden crear y administrar con los [cmdlets de PowerShell de Lote](batch-powershell-cmdlets-get-started.md) y la biblioteca [.NET de administración de Lote](batch-management-dotnet.md).

* La propia cuenta de Lote no tiene ningún costo. Solo se cobran los recursos de proceso de Azure que consumen sus soluciones de Lote y los recursos que consumen otros servicios cuando se ejecutan sus cargas de trabajo. Por ejemplo, se cobran los nodos de proceso de los grupos, y si se usa la característica [paquetes de aplicación](batch-application-packages.md), se cobran los recursos de Almacenamiento de Azure que se usan para almacenar las versiones del paquete de aplicación. Para más información, consulte [Precios de Lote][batch_pricing].

* Puede ejecutar varias cargas de trabajo de Lote en una sola cuenta de Lote, o bien distribuir las cargas de trabajo entre cuentas de Lote situadas en diferentes regiones de Azure.

* Si va a ejecutar varias cargas de trabajo de Lote a gran escala, tenga en cuenta que se aplican algunas [cuotas y límites de servicio de Lote](batch-quota-limit.md) a la suscripción de Azure y a cada cuenta de Lote. Las cuotas actuales de una cuenta de Lote aparecen en el portal de vista previa en las propiedades de la cuenta.

* Si asocia una cuenta de almacenamiento con su cuenta de Lote, tenga cuidado al volver a generar las claves de acceso de la cuenta de almacenamiento. Solo debe volver a generar una única clave de la cuenta de almacenamiento, hacer clic en **Sincronizar claves** en la hoja de la cuenta de almacenamiento vinculada y esperar 5 minutos para que las claves se propaguen a los nodos de proceso de los grupos y, por último, volver a generar y sincronizar la otra clave si fuera necesario. Si vuelve a generar las dos claves al mismo tiempo, los nodos de proceso no podrán sincronizar ninguna de las claves y perderán el acceso a la cuenta de almacenamiento.

  ![Regeneración de claves de cuenta de almacenamiento][4]

## Pasos siguientes

* Para más información acerca de los conceptos y las características de Lote, consulte [Información general de las características de Lote de Azure](batch-api-basics.md). El artículo describe los principales recursos de Lote como grupos, nodos de proceso, trabajos y tareas, y proporciona información general acerca de las características del servicio que permiten la ejecución de cargas de trabajo de proceso a gran escala.

* Obtenga información sobre los aspectos básicos del desarrollo de una aplicación habilitada por Lote mediante la [biblioteca de cliente .NET de Lote](batch-dotnet-get-started.md). El [artículo introductorio](batch-dotnet-get-started.md) le guía a través de una aplicación activa que utiliza el servicio Lote para ejecutar una carga de trabajo en varios nodos de proceso e incluye el uso de Almacenamiento de Azure para el almacenamiento provisional y la recuperación del archivo de la carga de trabajo.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[3]: ./media/batch-account-create-portal/batch_acct_03.png "Hoja de creación del servicio Lote en el Portal de Azure"
[4]: ./media/batch-account-create-portal/batch_acct_04.png "Regeneración de claves de cuenta de almacenamiento"
[5]: ./media/batch-account-create-portal/batch_acct_05.png "Hojas de propiedades y configuración de la cuenta de Lote"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=AcomDC_0323_2016-->