---
title: "Creación de una cuenta independiente de Azure Automation | Microsoft Docs"
description: "Tutorial que le guía a través de la creación, prueba y uso de ejemplo de la autenticación de la entidad de seguridad en Azure Automation."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/18/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 6eadfb0c3f91c1f2c7783d70604b45d5dc9912a3
ms.contentlocale: es-es
ms.lasthandoff: 08/19/2017

---

# <a name="create-a-standalone-azure-automation-account"></a>Creación de una cuenta independiente de Azure Automation
En este tema se muestra cómo crear una cuenta de Automation desde Azure Portal si desea evaluar y obtener información sobre Azure Automation, sin incluir las soluciones de administración adicionales ni la integración con Log Analytics de OMS para proporcionar supervisión avanzada de los trabajos de runbook.  Puede agregar estas soluciones de administración o realizar la integración con Log Analytics en cualquier momento en el futuro.  Con la cuenta de Automation podrá autenticar runbooks que administran recursos en Azure Resource Manager o en la implementación clásica de Azure.

Al crear una cuenta de Automation en el Azure Portal, se crea automáticamente:

* Una cuenta de ejecución que crea a una nueva entidad de servicio en Azure Active Directory y un certificado, y asigna el control de acceso basado en rol (RBAC) Colaborador, que se utilizará para administrar los recursos de Resource Manager mediante runbooks.   
* Una cuenta de ejecución clásica mediante la carga de un certificado de administración, que se usa para administrar recursos clásicos mediante runbooks.  

Esto simplifica el proceso y ayuda a que empezar rápidamente a generar e implementar runbooks que den soporte técnico a sus necesidades de automatización.  

## <a name="permissions-required-to-create-automation-account"></a>Permisos necesarios para crear la cuenta de Automation
Para crear o actualizar una cuenta de Automation, debe tener los siguientes privilegios y permisos, necesarios para completar este tema.   
 
* Para crear una cuenta de Automation, debe agregar la cuenta de usuario de AD a un rol con los privilegios equivalentes a los del rol Propietario para los recursos de Microsoft Automation, como se resalta en el artículo [Control de acceso basado en roles en Azure Automation](automation-role-based-access-control.md).  
* Los usuarios sin privilegios de administrador en el inquilino de Azure AD pueden **registrar aplicaciones de AD** si los registros de aplicaciones se configuran en [Sí](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions).  Si se establecen en **No**, el usuario que realice esta acción debe ser administrador global de Azure AD. 

Si no es miembro de la instancia de Active Directory de la suscripción antes de que le agreguen al rol de administrador global/coadministrador de esta última, se le agregará a Active Directory como invitado. En este caso, recibirá una advertencia tipo "No tiene permisos para crear..." en la hoja **Agregar cuenta de Automation**. Los usuarios que primero se agregaron al rol de administrador global/coadministrador se pueden quitar de la instancia de Active Directory de la suscripción y volverse a agregar para convertirlos en usuarios completos en Active Directory. Para comprobar esta situación, en el panel de **Azure Active Directory** de Azure Portal, seleccione **Usuarios y grupos**, **All Users** y, después de seleccionar el usuario específico, seleccione **Perfil**. El valor del atributo **Tipo de usuario** del perfil de los usuarios no debería ser **Invitado**.

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Creación de una nueva cuenta de Automation desde Azure Portal
En esta sección, realizará los pasos que se describen a continuación para crear una cuenta de Azure Automation en Azure Portal.    

1. Inicie sesión en Azure Portal con una cuenta que sea miembro del rol Administradores de suscripciones y coadministrador de la suscripción.
2. Haga clic en **Nuevo**.<br><br> ![Seleccione la opción Nuevo en Azure Portal](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Busque **Automation** y, a continuación, en los resultados de la búsqueda seleccione **Automation and Control***.<br><br> ![Busque y seleccione Automation en Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)<br> 
3. En la hoja Cuentas de Automation, haga clic en **Agregar**.<br><br>![Agregar cuenta de Automation](media/automation-create-standalone-account/automation-create-automationacct-properties.png)
   
   > [!NOTE]
   > Si aparece la advertencia siguiente en la hoja **Agregar cuenta de Automation**, se debe a que la cuenta no es miembro del rol Administradores de la suscripción ni coadministradora de la suscripción.<br><br>![Advertencia para agregar cuenta de Automation](media/automation-create-standalone-account/create-account-without-perms.png)
   > 
   > 
4. En la hoja **Agregar cuenta de Automation**, en el cuadro **Nombre**, especifique un nombre para la nueva cuenta de Automation.
5. Si tiene varias suscripciones, especifique una para la nueva cuenta, así como un **grupo de recursos** nuevo o existente y la **ubicación** de un centro de datos de Azure.
6. Compruebe que el valor **Sí** está seleccionado en la opción **Crear cuenta de ejecución de Azure** y haga clic en el botón **Crear**.  
   
   > [!NOTE]
   > Si decide seleccionar la opción **No** porque no quiere crear la cuenta de ejecución, aparecerá un mensaje de advertencia en la hoja **Agregar cuenta de Automation**.  Aunque la cuenta se crea en Azure Portal, no tiene la identidad de autenticación correspondiente en el servicio de directorio de suscripciones clásico o de Resource Manager y, por lo tanto, no tendrá acceso a los recursos de la suscripción.  Esto evitará que los runbooks que hacen referencia a esta cuenta puedan autenticarse y realizar tareas con los recursos en dichos modelos de suscripción.
   > 
   > ![Advertencia para agregar cuenta de Automation](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)<br>
   > Si no se crea la entidad de servicio, no se asigna el rol Colaborador.
   > 

7. Mientras Azure crea la cuenta de Automation, se puede seguir el progreso de **Notificaciones** desde el menú.

### <a name="resources-included"></a>Recursos incluidos
Una vez que se crea la cuenta de Automation, se también varios recursos automáticamente.  La siguiente tabla resume los recursos de la cuenta de ejecución.<br>

| Recurso | Descripción |
| --- | --- |
| Runbook AzureAutomationTutorial |Un runbook gráfico de ejemplo que muestra cómo realizar la autenticación mediante la cuenta de ejecución y que obtiene todos los recursos de Resource Manager. |
| Runbook AzureAutomationTutorialScript |Un Runbook de PowerShell de ejemplo que muestra cómo realizar la autenticación mediante la cuenta de ejecución y que obtiene todos los recursos de Resource Manager. |
| AzureRunAsCertificate |Recurso de certificado creado automáticamente durante la creación de la cuenta de Automation o mediante el siguiente script de PowerShell de una cuenta existente.  Permite realizar la autenticación con Azure, con el fin de poder administrar los recursos de Azure Resource Manager desde los Runbooks.  Este certificado tiene una duración de un año. |
| AzureRunAsConnection |Recurso de conexión creado automáticamente durante la creación de la cuenta de Automation o mediante el siguiente script de PowerShell de una cuenta existente. |

La siguiente tabla resume los recursos de la cuenta de ejecución de Azure clásico.<br>

| Recurso | Descripción |
| --- | --- |
| Runbook AzureClassicAutomationTutorial |Un runbook gráfico de ejemplo que obtiene todas las máquinas virtuales clásicas de una suscripción mediante la cuenta de ejecución de Azure clásico (certificado) y, luego, envía el nombre y el estado de la máquina virtual. |
| Runbook AzureClassicAutomationTutorial Script |Un runbook de PowerShell de ejemplo que obtiene todas las máquinas virtuales clásicas de una suscripción mediante la cuenta de ejecución de Azure clásico (certificado) y, luego, envía el nombre y el estado de la máquina virtual. |
| AzureClassicRunAsCertificate |Recurso de certificado creado automáticamente que se utiliza para realizar la autenticación en Azure, de modo sea posible administrar los recursos del modelo clásico de Azure desde Runbooks.  Este certificado tiene una duración de un año. |
| AzureClassicRunAsConnection |Recurso de conexión creado automáticamente que se utiliza para realizar la autenticación en Azure, de modo sea posible administrar los recursos del modelo clásico de Azure desde Runbooks. |


## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre la creación de gráficos, consulte [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md).
* Para empezar a trabajar con runbooks de PowerShell, consulte [Mi primer runbook de PowerShell](automation-first-runbook-textual-powershell.md).
* Para empezar a trabajar con Runbooks de flujo de trabajo de PowerShell, consulte [Mi primer Runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md).
