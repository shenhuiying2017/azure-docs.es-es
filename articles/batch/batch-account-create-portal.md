<properties
	pageTitle="Creación de una cuenta de Lote de Azure | Microsoft Azure"
	description="Aprenda a crear una cuenta de Lote de Azure en el Portal de Azure para ejecutar cargas de trabajo paralelas a gran escala en la nube"
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/21/2016"
	ms.author="marsma"/>

# Creación de una cuenta de Lote de Azure con el Portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](batch-account-create-portal.md)
- [NET de Administración de Lote](batch-management-dotnet.md)

Aprenda a crear una cuenta de Lote de Azure en el [Portal de Azure][azure_portal] y dónde encontrar propiedades de cuenta importantes, como claves de acceso y direcciones URL de la cuenta. También analizamos los precios de Lote y la vinculación de una cuenta de Almacenamiento de Azure con la cuenta de Lote, para que pueda usar [paquetes de aplicación](batch-application-packages.md) y [almacenar la salida de trabajos y tareas](batch-task-output.md).

## Crear una cuenta de lote

1. Inicie sesión en el [Portal de Azure][azure_portal].

2. Haga clic en **Nuevo** > **Proceso** > **Servicio de Lote**.

	![Lote en Marketplace][marketplace_portal]

3. Se muestra la hoja **Nueva cuenta de Lote**. Para obtener descripciones de cada elemento de la hoja, consulte los elementos de *a* a *e*.

    ![Crear una cuenta de lote][account_portal]

	a. **Nombre de cuenta**: nombre único de la cuenta de Lote. Este nombre debe ser único en la región de Azure en que se crea la cuenta (consulte *Ubicación* a continuación). Puede contener solo caracteres en minúsculas, números y su longitud debe oscilar entre 3 y 24 caracteres.

	b. **Suscripción**: suscripción en la que se va a crear la cuenta de Lote. Si tiene una sola suscripción, se selecciona de forma predeterminada.

	c. **Grupo de recursos**: grupo de recursos existentes de la nueva cuenta de Lote (también se puede crear uno nuevo).

	d. **Ubicación**: región de Azure en la que se va a crear la cuenta de Lote. Solo se muestran como opciones las regiones admitidas por su suscripción y grupo de recursos.

    e. **Cuenta de almacenamiento** (opcional): cuenta de almacenamiento **de uso general** que se va a asociar (vincular) a la nueva cuenta de Lote. Consulte [Cuenta de Almacenamiento de Azure vinculada](#linked-azure-storage-account) a continuación para más información.

4. Haga clic en **Crear** para crear la cuenta.

  El portal indica que la cuenta se está **implementando** y, tras la finalización, se muestra una notificación de **Implementaciones correctas** en *Notificaciones*.

## Visualización de propiedades de la cuenta de Lote

Una vez creada la cuenta, puede abrir la **hoja de la cuenta de Lote** para acceder a sus propiedades y configuración. Puede tener acceso a todas las propiedades y configuración de la cuenta en el menú izquierdo de la hoja Cuenta de lote.

![Hoja de la cuenta de Lote en el Portal de Azure][account_blade]

* **Dirección URL de la cuenta de lote**: las aplicaciones creadas con las [API de desarrollo de Lote](batch-technical-overview.md#batch-development-apis) necesitan una dirección URL de la cuenta para administrar los recursos y ejecutar trabajos en la cuenta. Una dirección URL de la cuenta de Lote tiene el formato siguiente:

    `https://<account_name>.<region>.batch.azure.com`

![Dirección URL de la cuenta de Lote en el Portal][account_url]

* **Claves de acceso**: las aplicaciones también necesitan una clave de acceso al trabajar con los recursos en la cuenta de Lote. Para ver o volver a generar las claves de acceso de la cuenta de Lote, escriba `keys` en el cuadro de **búsqueda** del menú de la izquierda, en la hoja de cuenta de Lote, y seleccione **Claves**.

    ![Claves de la cuenta de Lote en el Portal de Azure][account_keys]

## Precios

Las cuentas de Lote solo se ofrecen en el "nivel Gratis", lo que significa que no se le va a cobrar por la propia cuenta de Lote. Solo se cobran los recursos de proceso de Azure subyacentes que consumen sus soluciones de Lote y los recursos que consumen otros servicios cuando se ejecutan sus cargas de trabajo. Por ejemplo, se le cobrará por los nodos de proceso de los grupos y por los datos que almacene en Almacenamiento de Azure como entrada o salida de sus tareas. De igual forma, si se usa la característica [paquetes de aplicación](batch-application-packages.md), se cobran los recursos de Almacenamiento de Azure que se utilizan para almacenar los paquetes de aplicación. Para más información, consulte [Precios de Lote][batch_pricing].

## Cuenta de Almacenamiento de Azure vinculada

Como se ha mencionado antes, puede asociar (opcionalmente) una cuenta de almacenamiento **de uso general** a su nueva cuenta de Lote. La característica de [paquetes de aplicación](batch-application-packages.md) de Lote utiliza el almacenamiento de blobs en un cuenta de almacenamiento de uso general, al igual que la biblioteca [.NET de convenciones de archivo de Lote](batch-task-output.md). Estas características opcionales le ayudan a implementar las aplicaciones que ejecutan las tareas de Lote y a conservar los datos que generan.

Actualmente, Lote *solo* admite el tipo de cuenta de almacenamiento **de uso general**, tal y como se describe en el paso 5 de la sección [Crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) del artículo [Acerca de las cuentas de Almacenamiento de Azure](../storage/storage-create-storage-account.md). Cuando vincula una cuenta de Almacenamiento de Azure a su cuenta de Lote, asegúrese de que vincula *solo* una cuenta de almacenamiento de **uso general**.

![Creación de una cuenta de almacenamiento "de uso general"][storage_account]

Le recomendamos que cree una cuenta de almacenamiento para uso exclusivo con su cuenta de Lote.

>[AZURE.WARNING] Tenga cuidado al volver a generar las claves de acceso de una cuenta de almacenamiento vinculada. Vuelva a generar solo una clave de la cuenta de almacenamiento y haga clic en **Sincronizar claves** en la hoja de la cuenta de almacenamiento vinculada. Espere cinco minutos para que las claves se propaguen a los nodos de proceso de los grupos, y después vuelva a generar y sincronizar la otra clave si es necesario. Si vuelve a generar las dos claves al mismo tiempo, los nodos de proceso no podrán sincronizar ninguna de las claves y perderán el acceso a la cuenta de Almacenamiento.

  ![Regeneración de claves de cuenta de almacenamiento][4]

## Límites y cuotas del servicio Lote

Tenga en cuenta que como con su suscripción de Azure y otros servicios de Azure, se aplican ciertas [cuotas y límites](batch-quota-limit.md) a las cuentas de Lote. Las cuotas actuales de una cuenta de Lote aparecen en el portal en las **propiedades** de la cuenta.

![Cuotas de la cuenta de Lote en el Portal de Azure][quotas]

Tenga presente estas cuotas cuando diseñe y escale las cargas de trabajo de Lote. Por ejemplo, si su grupo no alcanza el número objetivo de nodos de proceso especificado, es posible que se haya alcanzado el límite de cuota de núcleos de la cuenta de Lote.

Además, tenga en cuenta que no está restringido a una única cuenta de Lote para la suscripción de Azure. Puede ejecutar varias cargas de trabajo de Lote en una sola cuenta de Lote, o bien distribuir las cargas de trabajo entre cuentas de Lote en la misma suscripción pero en diferentes regiones de Azure.

Muchas de estas cuotas se pueden aumentar simplemente con una solicitud gratuita de soporte técnico de producto enviada en el Portal de Azure. Para más información sobre la solicitud de aumentos de cuotas, consulte [Cuotas y límites del servicio Lote de Azure](batch-quota-limit.md).

## Otras opciones de administración de la cuenta de Lote

Además de usar el Portal de Azure, también puede crear y administrar cuentas de Lote con lo siguiente:

* [Cmdlets de PowerShell de Lote](batch-powershell-cmdlets-get-started.md)
* [CLI de Azure](../xplat-cli-install.md)
* [NET de Administración de Lote](batch-management-dotnet.md)

## Pasos siguientes

* Para más información acerca de los conceptos y las características del servicio Lote, consulte [Información general de las características de Lote de Azure](batch-api-basics.md). El artículo describe los principales recursos de Lote como grupos, nodos de proceso, trabajos y tareas, y proporciona información general acerca de las características del servicio que permiten la ejecución de cargas de trabajo de proceso a gran escala.

* Para conocer los aspectos básicos del desarrollo de una aplicación habilitada para Lote, consulte [Introducción a la biblioteca de Lote de Azure para .NET](batch-dotnet-get-started.md). El [artículo introductorio](batch-dotnet-get-started.md) le guía a través de una aplicación activa que usa el servicio Lote para ejecutar una carga de trabajo en varios nodos de proceso e incluye el uso de Almacenamiento de Azure para el almacenamiento provisional y la recuperación del archivo de la carga de trabajo.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Regeneración de claves de cuenta de almacenamiento"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png

<!---HONumber=AcomDC_0921_2016-->