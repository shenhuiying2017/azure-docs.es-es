---
title: "Creación de una cuenta de Batch en Azure Portal | Microsoft Docs"
description: Aprenda a crear una cuenta de Azure Batch en Azure Portal para ejecutar cargas de trabajo paralelas a gran escala en la nube
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
ms.date: 11/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ebda2f11f93b04a5592d18f8e15c8fc3b560aac3
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Creación de una cuenta de Batch con Azure Portal

> [!div class="op_single_selector"]
> * [Portal de Azure](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
>
>

Aprenda a crear una cuenta de Azure Batch en [Azure Portal][azure_portal] y seleccione las propiedades de cuenta más adecuadas para su escenario de proceso. Aprenda dónde encontrar importantes propiedades de cuenta, como teclas de acceso y direcciones URL de la cuenta.

Para más información acerca de los escenarios y las cuentas de Batch, consulte la [descripción de las características](batch-api-basics.md).



## <a name="create-a-batch-account"></a>Crear una cuenta de Batch

> [!NOTE]
> Cuando se crea una cuenta de Batch, por lo general debería elegir el modo de **servicio Batch** predeterminado, en el que los grupos se asignan en segundo plano en suscripciones administradas por Azure. En el modo de **suscripción de usuario** alternativo, que no se recomienda en la mayor parte de los escenarios, tanto las máquinas virtuales de Batch como otros recursos se crean directamente en su suscripción cuando se crea un grupo. Para crear una cuenta de Batch en modo de suscripción de usuario, también debe registrar su suscripción a Azure Batch y asociar la cuenta a una instancia de Azure Key Vault.

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



## <a name="view-batch-account-properties"></a>Visualización de propiedades de la cuenta de Batch
Una vez creada la cuenta, haga clic en ella para acceder a su configuración y propiedades. Con las opciones del menú izquierdo puede acceder a todas las propiedades y a la configuración de la cuenta.

![Página de la cuenta de Batch en Azure Portal][account_blade]

* **URL de cuenta de Batch**: al desarrollar una aplicación con las [API de Batch](batch-apis-tools.md#azure-accounts-for-batch-development), se necesita una dirección URL de la cuenta para acceder a los recursos de Batch. Una dirección URL de la cuenta de Batch tiene el formato siguiente:

    `https://<account_name>.<region>.batch.azure.com`

![Dirección URL de la cuenta de Batch en el Portal][account_url]

* **Claves de acceso**: para autenticar el acceso a una cuenta de Batch desde una aplicación, se puede usar una clave de acceso a la cuenta. (Batch también admite la autenticación de Azure Active Directory.)

    Para ver o volver a generar las claves de acceso, seleccione **Claves**.

    ![Claves de la cuenta de Batch en Azure Portal][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Cuenta de Azure Storage vinculada

Puede vincular una cuenta de Azure Storage de uso general a su cuenta de Batch, algo que resulta muy útil en muchos escenarios. La característica de [paquetes de aplicación](batch-application-packages.md) de Batch usa Azure Blob Storage, tal como hace la biblioteca [Batch File Conventions .NET](batch-task-output.md). Estas características opcionales le ayudan a implementar las aplicaciones que ejecutan las tareas de Batch y a conservar los datos que generan.

Se recomienda crear una cuenta de Storage nueva para que la use exclusivamente su cuenta de Batch. En la actualidad, Azure Batch solo admite el tipo de cuenta de Storage de uso general. Este tipo de cuenta se describe en el paso 5, [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) del artículo [Acerca de las cuentas de almacenamiento de Azure](../storage/common/storage-create-storage-account.md).

![Creación de una cuenta de almacenamiento de uso general][storage_account]

> [!NOTE]
> Tenga cuidado al volver a generar las claves de acceso de una cuenta de Storage vinculada. Vuelva a generar solo una clave de la cuenta de almacenamiento y haga clic en **Sincronizar claves** en la página de la cuenta de almacenamiento vinculada. Espere cinco minutos para que las claves se propaguen a los nodos de proceso de los grupos, y después vuelva a generar y sincronizar la otra clave si es necesario. Si vuelve a generar las dos claves al mismo tiempo, los nodos de proceso no podrán sincronizar ninguna de las claves y perderán el acceso a la cuenta de Almacenamiento.
>
>

![Regeneración de claves de cuenta de almacenamiento][4]

## <a name="additional-configuration-for-user-subscription-mode"></a>Configuración adicional para el modo de suscripción de usuario

Si decide crear una cuenta de Batch en el modo de suscripción de usuario, antes, realice los siguientes pasos adicionales.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Procedimiento para permitir que Azure Batch acceda a la suscripción (operación única)
Al crear la primera cuenta de Batch en el modo de suscripción de usuario, tiene que registrar la suscripción con Batch. (si ya lo ha hecho anteriormente, vaya a la sección siguiente).

1. Inicie sesión en [Azure Portal][azure_portal].

2. Haga clic en **Más servicios** > **Suscripciones** y, a continuación, en la suscripción que desea usar para la cuenta de Batch.

3. En la página **Suscripción**, haga clic en **Control de acceso (IAM)** > **Agregar**.

    ![Control de acceso a la suscripción][subscription_access]

4. En la página **Agregar permisos**, seleccione el rol **Colaborador** y busque Batch API. Busque cada una de estas cadenas hasta que encuentre la API:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Los inquilinos más recientes de Azure AD pueden utilizar este nombre.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** es el identificador de Batch API. 

5. Una vez que encuentre Batch API, selecciónelo y haga clic en **Guardar**.

    ![Adición de permisos de Batch][add_permission]

### <a name="create-a-key-vault"></a>Creación de un Almacén de claves
En el modo de suscripción de usuario, se requiere una instancia de Azure Key Vault que pertenezca al mismo grupo de recursos que la cuenta de Batch que se va a crear. Asegúrese de que el grupo de recursos se encuentre en una región en la que Batch esté [disponible](https://azure.microsoft.com/regions/services/) y que la suscripción admita.

1. En [Azure Portal][azure_portal], haga clic en **Nuevo** > **Seguridad e identidad** > **Key Vault**.

2. En la página **Crear Key Vault**, escriba un nombre para el almacén de claves y cree un grupo de recursos en la región que desee para su cuenta de Batch. Deje los valores predeterminados para el resto de la configuración y, a continuación, haga clic en **Crear**.




## <a name="batch-service-quotas-and-limits"></a>Límites y cuotas del servicio Batch
Igual que sucede con su suscripción a Azure y otros servicios de Azure, a las cuentas de Batch se les aplican ciertas [cuotas y límites](batch-quota-limit.md). Las cuotas actuales de una cuenta de Batch aparecen en **Cuotas**.

![Cuotas de la cuenta de Batch en Azure Portal][quotas]



Además, muchas de estas cuotas se pueden aumentar con una solicitud gratuita de soporte técnico de producto enviada en Azure Portal. Para más información sobre la solicitud de aumentos de cuotas, consulte [Cuotas y límites del servicio Azure Batch](batch-quota-limit.md) .

## <a name="other-batch-account-management-options"></a>Otras opciones de administración de la cuenta de Batch
Además de usar Azure Portal, las cuentas de Batch se pueden crear y administrar con los siguientes elementos:

* [Cmdlets de PowerShell de Batch](batch-powershell-cmdlets-get-started.md)
* [CLI de Azure](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

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

