---
title: "Creación de una cuenta de Batch en Azure Portal | Microsoft Docs"
description: Aprenda a crear una cuenta de Lote de Azure en el Portal de Azure para ejecutar cargas de trabajo paralelas a gran escala en la nube
services: batch
documentationcenter: 
author: v-dotren
manager: timlt
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b7629e496f2d73798b94acdc611014a8b3afead7
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Creación de una cuenta de Batch con Azure Portal

> [!div class="op_single_selector"]
> * [Portal de Azure](batch-account-create-portal.md)
> * [NET de Administración de Lote](batch-management-dotnet.md)
>
>

Aprenda a crear una cuenta de Azure Batch en [Azure Portal][azure_portal] y seleccione las propiedades de cuenta más adecuadas para su escenario de proceso. Aprenda dónde encontrar importantes propiedades de cuenta, como teclas de acceso y direcciones URL de la cuenta.

Para más información acerca de los escenarios y las cuentas de Batch, consulte la [descripción de las características](batch-api-basics.md).



## <a name="create-a-batch-account"></a>Crear una cuenta de lote



1. Inicie sesión en [Azure Portal][azure_portal].
2. Haga clic en **Nuevo** y busque **Servicio Batch** en Marketplace.

    ![Batch en Marketplace][marketplace_portal]
3. Seleccione **Servicio Batch**, haga clic en **Crear** y especifique la configuración de **Nueva cuenta de Batch**. Vea los siguientes detalles.

    ![Crear una cuenta de Batch][account_portal]

    a. **Nombre de cuenta**: el nombre que elija debe ser único en la región de Azure en la que se crea cuenta (consulte **Ubicación** a continuación). El nombre de la cuenta solo puede contener caracteres en minúsculas o números, y su longitud debe oscilar entre 3 y 24 caracteres.

    b. **Suscripción**: la suscripción en la que se crea la cuenta de Batch. Si tiene una sola suscripción, se selecciona de forma predeterminada.

    c. **Modo de asignación de grupo**: si aparece esta opción, acepte el valor predeterminado de **Servicio Batch**.

    c. **Grupo de recursos**: seleccione un grupo de recursos existente para la nueva cuenta de Batch, o bien cree uno nuevo.

    d. **Ubicación**: la región de Azure en la que se va a crear la cuenta de Batch. Solo se muestran como opciones las regiones admitidas por su suscripción y grupo de recursos.

    e. **Cuenta de almacenamiento** (opcional): cuenta de Azure Storage de uso general que se asocia a la cuenta de Batch. Se recomienda su uso para la mayoría de las cuentas de Batch. Para más información, consulte [Cuenta de Azure Storage vinculada](#linked-azure-storage-account) en este mismo artículo.

4. Haga clic en **Crear** para crear la cuenta.



## <a name="view-batch-account-properties"></a>Visualización de propiedades de la cuenta de Lote
Una vez creada la cuenta, haga clic en ella para acceder a su configuración y propiedades. Con las opciones del menú izquierdo puede acceder a todas las propiedades y a la configuración de la cuenta.

![Hoja Cuenta de Batch en Azure Portal][account_blade]

* **URL de cuenta de Batch**: al desarrollar una aplicación con las [API de Batch](batch-apis-tools.md#azure-accounts-for-batch-development), se necesita una dirección URL de la cuenta para acceder a los recursos de Batch. Una dirección URL de la cuenta de Batch tiene el formato siguiente:

    `https://<account_name>.<region>.batch.azure.com`

![Dirección URL de la cuenta de Batch en el portal][account_url]

* **Claves de acceso**: para autenticar el acceso a una cuenta de Batch desde una aplicación, se puede usar una clave de acceso a la cuenta. (Batch también admite la autenticación de Azure Active Directory.)

    Para ver o volver a generar las claves de acceso, seleccione **Claves**.

    ![Claves de la cuenta de Lote en el Portal de Azure][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Cuenta de Almacenamiento de Azure vinculada

Puede vincular una cuenta de Azure Storage de uso general a su cuenta de Batch, algo que resulta muy útil en muchos escenarios. La característica de [paquetes de aplicación](batch-application-packages.md) de Batch usa Azure Blob Storage, tal como hace la biblioteca [Batch File Conventions .NET](batch-task-output.md). Estas características opcionales le ayudan a implementar las aplicaciones que ejecutan las tareas de Batch y a conservar los datos que generan.

Se recomienda crear una cuenta de Storage nueva para que la use exclusivamente su cuenta de Batch. En la actualidad, Azure Batch solo admite el tipo de cuenta de Storage de uso general. Este tipo de cuenta se describe en el paso 5, [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) del artículo [Acerca de las cuentas de almacenamiento de Azure](../storage/common/storage-create-storage-account.md).

![Creación de una cuenta de almacenamiento de uso general][storage_account]

> [!NOTE]
> Tenga cuidado al volver a generar las claves de acceso de una cuenta de Storage vinculada. Vuelva a generar solo una clave de la cuenta de almacenamiento y haga clic en **Sincronizar claves** en la hoja de la cuenta de almacenamiento vinculada. Espere cinco minutos para que las claves se propaguen a los nodos de proceso de los grupos, y después vuelva a generar y sincronizar la otra clave si es necesario. Si vuelve a generar las dos claves al mismo tiempo, los nodos de proceso no podrán sincronizar ninguna de las claves y perderán el acceso a la cuenta de Almacenamiento.
>
>

![Regeneración de claves de cuenta de almacenamiento][4]

## <a name="batch-service-quotas-and-limits"></a>Límites y cuotas del servicio Lote
Igual que sucede con su suscripción a Azure y otros servicios de Azure, a las cuentas de Batch se les aplican ciertas [cuotas y límites](batch-quota-limit.md). Las cuotas actuales de una cuenta de Batch aparecen en **Cuotas**.

![Cuotas de la cuenta de Batch en Azure Portal][quotas]



Además, muchas de estas cuotas se pueden aumentar con una solicitud gratuita de soporte técnico de producto enviada en Azure Portal. Para más información sobre la solicitud de aumentos de cuotas, consulte [Cuotas y límites del servicio Lote de Azure](batch-quota-limit.md).

## <a name="other-batch-account-management-options"></a>Otras opciones de administración de la cuenta de Lote
Además de usar Azure Portal, las cuentas de Batch se pueden crear y administrar con los siguientes elementos:

* [Cmdlets de PowerShell de Batch](batch-powershell-cmdlets-get-started.md)
* [CLI de Azure](batch-cli-get-started.md)
* [NET de Administración de Lote](batch-management-dotnet.md)

## <a name="next-steps"></a>Pasos siguientes
* Para más información acerca de los conceptos y las características del servicio de Batch, consulte la [descripción de las características de Batch](batch-api-basics.md). El artículo describe los principales recursos de Batch como grupos, nodos de proceso, trabajos y tareas, y proporciona información general acerca de las características del servicio específicas para las cargas de trabajo de proceso a gran escala.
* Para conocer los aspectos básicos del desarrollo de una aplicación habilitada para Batch, consulte la [biblioteca de cliente de Batch para .NET](batch-dotnet-get-started.md) o [Python](batch-python-tutorial.md). Estos artículos introductorios le guían a través de una aplicación activa que usa el servicio de Batch para ejecutar una carga de trabajo en varios nodos de proceso e incluye el uso de Azure Storage para el almacenamiento provisional y la recuperación del archivo de la carga de trabajo.

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
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[account_portal_byos]: ./media/batch-account-create-portal/batch_acct_portal_byos.png
