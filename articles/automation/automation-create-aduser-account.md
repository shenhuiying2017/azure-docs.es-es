---
title: Creación de una cuenta de usuario de Azure AD
description: En este artículo se describe cómo crear credenciales de cuentas de usuario de Azure AD para runbooks en Azure Automation para autenticarse en Azure.
keywords: usuario de azure active directory, administración de servicios de azure, cuenta de usuario de azure ad
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 105dcf5564ec5c1d0b3528e9b5667d89d98c6cab
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195512"
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Autenticación de runbooks con la implementación clásica de Azure y Resource Manager
En este artículo se describen los pasos que debe realizar para configurar una cuenta de usuario de Azure AD para runbooks de Azure Automation que se ejecutan en recursos del modelo de implementación clásica de Azure o en Azure Resource Manager (ARM). Aunque esta sigue siendo una identidad de autenticación admitida para sus runbooks basados en Azure Resource Manager, el método recomendado es usar una cuenta de ejecución de Azure.       

## <a name="create-a-new-azure-active-directory-user"></a>Creación de un usuario de Azure Active Directory nuevo
1. Inicie sesión en Azure Portal como administrador de servicios para la suscripción de Azure que desea administrar.
2. Seleccione **Azure Active Directory** > **Usuarios y grupos** > **Todos los usuarios** > **Nuevo usuario**.
3. Especifique los detalles del usuario, como el **nombre** y el **nombre de usuario**.  
4. Observe el nombre completo y la contraseña temporal del usuario.
5. Seleccione **Rol de directorio**.
6. Asigne el rol Administrador limitado o global.
7. Cierre la sesión en Azure y, a continuación, vuelva a iniciarla con la cuenta que acaba de crear. Se le pedirá que cambie la contraseña del usuario.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Creación de una cuenta de Automation en Azure Portal
En esta sección, realizará los pasos siguientes en Azure Portal para crear una cuenta de Azure Automation que se usará con sus recursos de administración de runbooks en el modo de Azure Resource Manager.  

1. Inicie sesión en Azure Portal como administrador de servicios para la suscripción de Azure que desea administrar.
2. Seleccione **Cuentas de Automation**.
3. Seleccione **Agregar**.<br><br>![Agregar cuenta de Automation](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. En la hoja **Agregar cuenta de Automation**, en el cuadro **Nombre**, especifique un nombre para la nueva cuenta de Automation.
5. Si tiene más de una suscripción, especifique la de la cuenta nueva, así como un **grupo de recursos** nuevo o existente, y la **ubicación** de un centro de datos de Azure.
6. Seleccione el valor **Sí** en la opción **Crear cuenta de ejecución de Azure** y haga clic en el botón **Crear**.  
   
    > [!NOTE]
    > Si decide seleccionar la opción **No** porque no quiere crear la cuenta de ejecución, aparecerá un mensaje de advertencia en la hoja **Agregar cuenta de Automation**. Aunque la cuenta se crea y se asigna al rol de **Colaborador** en la suscripción, esta no tendrá la identidad de autenticación correspondiente dentro del servicio de directorio de suscripciones y, por lo tanto, no tendrá acceso a los recursos de su suscripción. Como consecuencia, los runbooks que hacen referencia a esta cuenta no podrán autenticarse ni realizar tareas en los recursos de Azure Resource Manager.
    > 
    >

    <br>![Advertencia para agregar cuenta de Automation](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. Mientras Azure crea la cuenta de Automation, se puede seguir el progreso de **Notificaciones** desde el menú.

Después de completar la creación de las credenciales, necesitará crear un recurso de credenciales para asociar la cuenta de Automation con la cuenta de usuario de AD que creó anteriormente. Recuerde que solo se crea la cuenta de Automation y que no está asociada con una identidad de autenticación. Siga los pasos descritos en el artículo [Recursos de credenciales en Azure Automation](automation-credentials.md#creating-a-new-credential-asset) y escriba el valor de **nombre de usuario** con el formato **dominio\usuario**.

## <a name="use-the-credential-in-a-runbook"></a>Uso de la credencial en un runbook
Puede recuperar la credencial en un runbook mediante el uso de la actividad [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) y, a continuación, úsela con [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) para conectarse a su suscripción de Azure. Si credencial es un administrador de varias suscripciones de Azure, deberá usar también [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) para especificar la suscripción correcta. Esto se muestra en el siguiente ejemplo de PowerShell que normalmente aparece en la parte superior de la mayoría de los runbooks de Azure Automation.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Repita estas líneas después de cada [punto de control](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) de su runbook. Si el runbook se suspende y luego se reanuda en otro trabajo, se deberá volver a realizar la autenticación.

## <a name="next-steps"></a>Pasos siguientes
* Revise los tipos de runbook diferentes y los pasos necesarios para crear sus propios runbooks en el siguiente artículo [Tipos de runbooks de Azure Automation](automation-runbook-types.md)

