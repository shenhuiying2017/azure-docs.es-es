---
title: Creación de una cuenta de Batch en Azure Portal | Microsoft Docs
description: Aprenda a crear una cuenta de Azure Batch en Azure Portal para ejecutar cargas de trabajo paralelas a gran escala en la nube
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83d97d9ed9c51d59500115c4ee3896d471024999
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359764"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Creación de una cuenta de Batch con Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
>
>

Aprenda a crear una cuenta de Azure Batch en [Azure Portal][azure_portal] y seleccione las propiedades de cuenta más adecuadas para su escenario de proceso. Aprenda dónde encontrar importantes propiedades de cuenta, como teclas de acceso y direcciones URL de la cuenta.

Para más información acerca de los escenarios y las cuentas de Batch, consulte la [descripción de las características](batch-api-basics.md).

## <a name="create-a-batch-account"></a>Crear una cuenta de Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Inicie sesión en [Azure Portal][azure_portal].

2. Haga clic en **Nuevo** > **Proceso** > **Servicio de Batch**.

    ![Batch en Marketplace][marketplace_portal]

3. Especifique los detalles de la opción **Nueva cuenta de Batch**. Vea los siguientes detalles.

    ![Crear una cuenta de Batch][account_portal]

    a. **Nombre de cuenta**: el nombre que elija debe ser único en la región de Azure en la que se crea cuenta (consulte **Ubicación** a continuación). El nombre de la cuenta solo puede contener caracteres en minúsculas o números, y su longitud debe oscilar entre 3 y 24 caracteres.

    b. **Suscripción**: la suscripción en la que se crea la cuenta de Batch. Si tiene una sola suscripción, se selecciona de forma predeterminada.

    c. **Grupo de recursos**: seleccione un grupo de recursos existente para la nueva cuenta de Batch, o bien cree uno nuevo.

    d. **Ubicación**: la región de Azure en la que se va a crear la cuenta de Batch. Solo se muestran como opciones las regiones admitidas por su suscripción y grupo de recursos.

    e. **Cuenta de almacenamiento** (opcional): cuenta de Azure Storage que se asocia a la cuenta de Batch. Se recomienda su uso para la mayoría de las cuentas de Batch. Para conocer las opciones de cuenta de almacenamiento de Batch, consulte la [introducción a la característica de Batch](batch-api-basics.md#azure-storage-account). En el portal, seleccione una cuenta de almacenamiento existente o cree una.

      ![Crear una cuenta de almacenamiento][storage_account]

    f. **Modo de asignación de grupo**: para la mayoría de los escenarios, acepte el valor predeterminado de **Servicio Batch**.

4. Haga clic en **Crear** para crear la cuenta.



## <a name="view-batch-account-properties"></a>Visualización de propiedades de la cuenta de Batch
Una vez creada la cuenta, haga clic en ella para acceder a su configuración y propiedades. Con las opciones del menú izquierdo puede acceder a todas las propiedades y a la configuración de la cuenta.

![Página de la cuenta de Batch en Azure Portal][account_blade]

* **Nombre, URL y claves de cuenta de Batch**: al desarrollar una aplicación con las [API de Batch](batch-apis-tools.md#azure-accounts-for-batch-development), se necesita una dirección URL de la cuenta y una clave para acceder a los recursos de Batch. (Batch también admite la autenticación de Azure Active Directory.)

    Para ver la información de acceso de la cuenta de Batch, haga clic en **Claves**.

    ![Claves de la cuenta de Batch en Azure Portal][account_keys]

* Para ver el nombre y las claves de la cuenta de almacenamiento asociada con la cuenta de Batch, haga clic en **Cuenta de almacenamiento**.

* Para ver las cuotas de recursos que se aplican a la cuenta de Batch, haga clic en **Cuotas**. Para más información, consulte [Límites y cuotas del servicio Batch](batch-quota-limit.md).


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

1. En [Azure Portal][azure_portal], haga clic en **Nuevo** > **Seguridad** > **Key Vault**.

2. En la página **Crear Key Vault**, escriba un nombre para el almacén de claves y cree un grupo de recursos en la región que desee para su cuenta de Batch. Deje los valores predeterminados para el resto de la configuración y, a continuación, haga clic en **Crear**.

Al crear la cuenta de Batch en el modo de suscripción de usuario, use el grupo de recursos del almacén de claves, especifique **Suscripción de usuario** como modo de asignación de grupos y seleccione el almacén de claves.

## <a name="other-batch-account-management-options"></a>Otras opciones de administración de la cuenta de Batch
Además de usar Azure Portal, las cuentas de Batch se pueden crear y administrar con las siguientes herramientas:

* [Cmdlets de PowerShell de Batch](batch-powershell-cmdlets-get-started.md)
* [CLI de Azure](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Pasos siguientes
* Para más información acerca de los conceptos y las características del servicio de Batch, consulte la [descripción de las características de Batch](batch-api-basics.md). El artículo describe los principales recursos de Batch como grupos, nodos de proceso, trabajos y tareas, y proporciona información general acerca de las características del servicio específicas para las cargas de trabajo de proceso a gran escala.
* Para conocer los aspectos básicos del desarrollo de una aplicación habilitada para Batch, consulte la [biblioteca de cliente de Batch para .NET](batch-dotnet-get-started.md) o [Python](batch-python-tutorial.md). Estos artículos introductorios le guían a través de una aplicación activa que usa el servicio de Batch para ejecutar una carga de trabajo en varios nodos de proceso e incluye el uso de Azure Storage para el almacenamiento provisional y la recuperación del archivo de la carga de trabajo.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png

