---
title: "Creación de una cuenta de Batch en Azure Portal | Microsoft Docs"
description: Aprenda a crear una cuenta de Lote de Azure en el Portal de Azure para ejecutar cargas de trabajo paralelas a gran escala en la nube
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6b6c548ca1001587e2b40bbe9ee2fcb298f40d72
ms.openlocfilehash: bdd7f1410906a46c66c1fd33949b30cc669e5a6b
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Creación de una cuenta de Batch con Azure Portal

> [!div class="op_single_selector"]
> * [Portal de Azure](batch-account-create-portal.md)
> * [NET de Administración de Lote](batch-management-dotnet.md)
> 
> 

Aprenda a crear una cuenta de Azure Batch en [Azure Portal][azure_portal] y dónde encontrar propiedades de cuenta importantes, como claves de acceso y direcciones URL de la cuenta. También analizamos los precios de Batch y la vinculación de una cuenta de Azure Storage con la cuenta de Batch, para que pueda usar [paquetes de aplicación](batch-application-packages.md) y [almacenar la salida de trabajos y tareas](batch-task-output.md).

## <a name="create-a-batch-account"></a>Crear una cuenta de lote
1. Inicie sesión en [Azure Portal][azure_portal].
2. Haga clic en **Nuevo** > **Proceso** > **Servicio de Batch**.
   
    ![Lote en Marketplace][marketplace_portal]
3. Se muestra la hoja **Nueva cuenta de Lote** . Para obtener descripciones de cada elemento de la hoja, consulte los elementos de *a* a *e*.
   
    ![Crear una cuenta de lote][account_portal]
   
    a. **Nombre de cuenta**: el nombre de la cuenta de Batch. El nombre que elija debe ser único en la región de Azure en la que se creará la nueva cuenta (consulte **Ubicación** a continuación). El nombre de la cuenta solo puede contener caracteres en minúsculas o números, y su longitud debe oscilar entre 3 y 24 caracteres.
   
    b. **Suscripción**: la suscripción en la que se crea la cuenta de Batch. Si tiene una sola suscripción, se selecciona de forma predeterminada.
   
    c. **Grupo de recursos**: seleccione un grupo de recursos existente para la nueva cuenta de Batch, o bien cree uno nuevo.
   
    d. **Ubicación**: la región de Azure en la que se va a crear la cuenta de Batch. Solo se muestran como opciones las regiones admitidas por su suscripción y grupo de recursos.
   
    e. **Cuenta de almacenamiento** (opcional): cuenta de Azure Storage de uso general que se asocia a la nueva cuenta de Batch. Consulte [Cuenta de Almacenamiento de Azure vinculada](#linked-azure-storage-account) a continuación para más información.

4. Haga clic en **Crear** para crear la cuenta.
   
   El portal indica que la cuenta se está **implementando** y, tras la finalización, se muestra una notificación de **Implementaciones correctas** en *Notificaciones*.

## <a name="view-batch-account-properties"></a>Visualización de propiedades de la cuenta de Lote
Una vez creada la cuenta, puede abrir la **hoja de la cuenta de Lote** para acceder a sus propiedades y configuración. Puede tener acceso a todas las propiedades y configuración de la cuenta en el menú izquierdo de la hoja Cuenta de Batch.

![Hoja Cuenta de Batch en el Portal de Azure][account_blade]

* **URL cuenta de Batch**: al desarrollar una aplicación con las [API de Batch](batch-technical-overview.md#batch-development-apis), necesitará una dirección URL de la cuenta para acceder a los recursos de Batch. Una dirección URL de la cuenta de Batch tiene el formato siguiente:
  
    `https://<account_name>.<region>.batch.azure.com`

![Dirección URL de la cuenta de Batch en el portal][account_url]

* **Claves de acceso**: para autenticar el acceso a su cuenta de Batch desde su aplicación, necesitará una clave de acceso a la cuenta. Para ver o volver a generar las claves de acceso de la cuenta de Batch, escriba `keys` en el cuadro de **búsqueda** del menú de la izquierda, en la hoja de cuenta de Batch, y seleccione **Claves**.
  
    ![Claves de la cuenta de Lote en el Portal de Azure][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Cuenta de Almacenamiento de Azure vinculada

Como se ha mencionado antes, existe la posibilidad de vincular una cuenta de Azure Storage de uso general a una cuenta de Batch. La característica de [paquetes de aplicación](batch-application-packages.md) de Batch usa Azure Blob Storage, tal como hace la biblioteca [Batch File Conventions .NET](batch-task-output.md). Estas características opcionales le ayudan a implementar las aplicaciones que ejecutan las tareas de Batch y a conservar los datos que generan.

Se recomienda crear una cuenta de Storage nueva para que la use exclusivamente su cuenta de Batch.

![Creación de una cuenta de almacenamiento "de uso general"][storage_account]

> [!NOTE] 
> En la actualidad, Azure Batch solo admite el tipo de cuenta de Storage de uso general. Este tipo de cuenta se describe en el paso 5, [Crear una cuenta de almacenamiento] (../storage/storage-create-storage-account.md#create-a-storage-account), de [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md).
>
>

> [!WARNING]
> Tenga cuidado al volver a generar las claves de acceso de una cuenta de Storage vinculada. Vuelva a generar solo una clave de la cuenta de almacenamiento y haga clic en **Sincronizar claves** en la hoja de la cuenta de almacenamiento vinculada. Espere cinco minutos para que las claves se propaguen a los nodos de proceso de los grupos, y después vuelva a generar y sincronizar la otra clave si es necesario. Si vuelve a generar las dos claves al mismo tiempo, los nodos de proceso no podrán sincronizar ninguna de las claves y perderán el acceso a la cuenta de Almacenamiento.
> 
> 

![Regeneración de claves de cuenta de almacenamiento][4]

## <a name="batch-service-quotas-and-limits"></a>Límites y cuotas del servicio Lote
Tenga en cuenta que como con su suscripción de Azure y otros servicios de Azure, se aplican ciertas [cuotas y límites](batch-quota-limit.md) a las cuentas de Lote. Las cuotas actuales de una cuenta de Lote aparecen en el portal en las **propiedades**de la cuenta.

![Cuotas de la cuenta de Lote en el Portal de Azure][quotas]

Tenga presente estas cuotas cuando diseñe y escale las cargas de trabajo de Lote. Por ejemplo, si su grupo no alcanza el número objetivo de nodos de proceso especificado, es posible que se haya alcanzado el límite de cuota de núcleos de la cuenta de Lote.

La cuota para las cuentas de Batch es por región y por suscripción, por lo que es posible tener más de una cuenta de Batch de forma predeterminada, siempre que se encuentren en regiones diferentes. Se pueden ejecutar varias cargas de trabajo de Batch en una sola cuenta de Batch, o bien distribuir las cargas de trabajo entre cuentas de Batch que se encuentren en la misma suscripción, pero en diferentes regiones de Azure.

Además, muchas de estas cuotas se pueden aumentar simplemente con una solicitud gratuita de soporte técnico de producto enviada en Azure Portal. Para más información sobre la solicitud de aumentos de cuotas, consulte [Cuotas y límites del servicio Lote de Azure](batch-quota-limit.md) .

## <a name="other-batch-account-management-options"></a>Otras opciones de administración de la cuenta de Lote
Además de usar el Portal de Azure, también puede crear y administrar cuentas de Lote con lo siguiente:

* [Cmdlets de PowerShell de Lote](batch-powershell-cmdlets-get-started.md)
* [CLI de Azure](../xplat-cli-install.md)
* [NET de Administración de Lote](batch-management-dotnet.md)

## <a name="next-steps"></a>Pasos siguientes
* Para más información acerca de los conceptos y las características del servicio Lote, consulte [Información general de las características de Lote de Azure](batch-api-basics.md) . El artículo describe los principales recursos de Lote como grupos, nodos de proceso, trabajos y tareas, y proporciona información general acerca de las características del servicio que permiten la ejecución de cargas de trabajo de proceso a gran escala.
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

