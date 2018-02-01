---
title: "Creación de una cuenta independiente de Azure Automation | Microsoft Docs"
description: "En este artículo se le guía en la creación, la prueba y el uso de la autenticación de la entidad de seguridad en Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: magoedte
ms.openlocfilehash: 4a6946f34babfd63a2b9a12818761c6d6c74bc15
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="create-a-standalone-azure-automation-account"></a>Creación de una cuenta independiente de Azure Automation
En este artículo se muestra cómo crear una cuenta de Azure Automation en el portal de Azure. Puede usar la cuenta de Automation del portal para evaluar y obtener información acerca de Automation sin usar soluciones de administración o integración adicionales con Azure Log Analytics en Operations Management Suite (OMS). Puede agregar estas soluciones de administración o realizar la integración con Log Analytics para disfrutar de una supervisión avanzada de los trabajos de runbook en cualquier momento en el futuro. 

Con una cuenta de Automation, podrá autenticar runbooks mediante la administración de recursos en Azure Resource Manager o en el modelo de implementación clásica.

Al crear una cuenta de Automation en Azure Portal, estas cuentas se crean automáticamente:

* **Cuenta de ejecución**. Esta cuenta hace las siguientes tareas:
  - Creación de una entidad de servicio en Azure Active Directory (Azure AD).
  - Crea un certificado.
  - Asigna el control de acceso basado en rol (RBAC) del colaborador, que administra recursos de Azure Resource Manager mediante runbooks.
* **Cuenta de ejecución clásica**. Esta cuenta carga un certificado de administración. El certificado administra los recursos clásicos mediante runbooks.

Con estas cuentas que cree, podrá generar e implementar Runbooks que den soporte técnico a sus necesidades de automatización.  

## <a name="permissions-required-to-create-an-automation-account"></a>Permisos necesarios para crear una cuenta de Automation
Para crear o actualizar una cuenta de Automation, y completar las tareas que se describen en este artículo, debe tener los siguientes privilegios y permisos: 

* Para crear una cuenta de Automation, la cuenta de usuario de Azure AD debe agregarse a un rol con permisos equivalentes al rol de propietario para **Microsoft. Recursos de Automation**. Para más información, consulte [Control de acceso basado en rol en Azure Automation](automation-role-based-access-control.md).  
* En Azure Portal, en **Azure Active Directory** > **ADMINISTRAR** > **Registros de aplicaciones**, si **Registros de aplicaciones** está establecido en **Sí**, los usuarios sin derechos administrativos del inquilino de Azure AD pueden [registrar aplicaciones de Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Si **Registros de aplicaciones** está establecido en **No**, el usuario que realice esta acción debe ser administrador global de Azure AD. 

Si no es miembro de la instancia de Active Directory de la suscripción antes de que le agreguen al rol de administrador global/coadministrador de esta última, se le agregará a Active Directory como invitado. En este escenario, verá este mensaje en la página **Agregar cuenta de Automation**: "No tiene permisos para crear". 

Si un usuario se agrega primero al rol de administrador global/coadministrador, puede quitarlo de la instancia de Active Directory de la suscripción y volver a agregarlo para convertirlo en un usuario completo en Active Directory.

Para comprobar los roles de usuario:
1. En Azure Portal, vaya al panel **Azure Active Directory**.
2. Seleccione **Usuarios y grupos**.
3. Seleccione **Todos los usuarios**. 
4. Después de seleccionar un usuario específico, seleccione **Perfil**. El valor del atributo **Tipo de usuario** del perfil de los usuarios no debería ser **Invitado**.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Creación de una nueva cuenta de Automation en Azure Portal
Para crear una cuenta de Azure Automation en Azure Portal, complete los pasos siguientes:    

1. Inicie sesión en Azure Portal con una cuenta que sea miembro del rol Administradores de la suscripción y coadministrador de la suscripción.
2. Seleccione **+ Crear un recurso**.
3. Busque **Automation**. En los resultados de la búsqueda, haga clic en **Automation**.<br><br> ![Busque y seleccione Automation and Control en Azure Marketplace](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png).<br> 
4. En la pantalla siguiente, seleccione **Crear**.
  ![Agregar cuenta de Automation](media/automation-create-standalone-account/automation-create-automationacct-properties.png)
  
  > [!NOTE]
  > Si ve el mensaje siguiente en el panel **Agregar cuenta de Automation**, significa que su cuenta no es miembro del rol Administradores de la suscripción ni del rol de coadministrador de la suscripción.
  >
  > ![Advertencia de Agregar cuenta de Automation](media/automation-create-standalone-account/create-account-without-perms.png)
  >
5. En el panel **Agregar cuenta de Automation**, en el cuadro **Nombre**, escriba el nombre de la nueva cuenta de Automation.
6. Si tiene más de una suscripción, en el cuadro **Suscripción**, especifique qué suscripción quiere usar para la cuenta nueva. 
7. Para **Grupo de recursos**, escriba o seleccione un grupo de recursos nuevo o existente. 
8. Para **Ubicación**, seleccione una ubicación del centro de datos de Azure.
9. Para la opción **Crear cuenta de ejecución de Azure**, asegúrese de que la opción **Sí** está seleccionada y, después, seleccione **Crear**.
    
  > [!NOTE]
  > Si decide no crear la cuenta de ejecución, al seleccionar **No** para **Crear cuenta de ejecución de Azure**, aparecerá un mensaje en el panel **Agregar cuenta de Automation**. Aunque la cuenta se crea en Azure Portal, no tiene la identidad de autenticación correspondiente en la suscripción del modelo de implementación clásico ni en el servicio de directorio de suscripciones de Resource Manager. Por lo tanto, la cuenta de Automation no tiene acceso a los recursos en su suscripción. Esto evitará que los runbooks que hacen referencia a esta cuenta puedan autenticarse y realizar tareas con los recursos en dichos modelos de suscripción.
  >
  > ![Advertencia de Agregar cuenta de Automation](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
  >
  > Si no se crea la entidad de servicio, no se asigna el rol Colaborador.
  >
10. Para realizar un seguimiento del progreso de la creación de la cuenta de Automation, en el menú, seleccione **Notificaciones**.

### <a name="resources-included"></a>Recursos incluidos
Una vez que se crea la cuenta de Automation, se también varios recursos automáticamente. La siguiente tabla resume los recursos de la cuenta de ejecución.

| Recurso | DESCRIPCIÓN |
| --- | --- |
| Runbook AzureAutomationTutorial |Runbook gráfico de ejemplo que muestra cómo realizar la autenticación mediante la cuenta de ejecución. El runbook obtiene todos los recursos de Resource Manager. |
| Runbook AzureAutomationTutorialScript |Runbook de PowerShell de ejemplo que muestra cómo realizar la autenticación mediante la cuenta de ejecución. El runbook obtiene todos los recursos de Resource Manager. |
| Runbook AzureAutomationTutorialPython2 |Runbook de Python de ejemplo que muestra cómo realizar la autenticación mediante la cuenta de ejecución. El runbook enumera todos los grupos de recursos presentes en la suscripción. |
| AzureRunAsCertificate |Recurso de certificado que se crea automáticamente al crear una cuenta de Automation, o mediante el uso del siguiente script de PowerShell para una cuenta existente. El certificado realiza la autenticación en Azure, de modo que puede administrar los recursos de Azure Resource Manager desde los runbooks. Este certificado tiene una duración de un año. |
| AzureRunAsConnection |Recurso de conexión que se crea automáticamente al crear una cuenta de Automation, o mediante el uso del siguiente script de PowerShell para una cuenta existente. |

La siguiente tabla resume los recursos de la cuenta de ejecución de Azure clásico.

| Recurso | DESCRIPCIÓN |
| --- | --- |
| Runbook AzureClassicAutomationTutorial |Ejemplo de un runbook gráfico. El runbook obtiene todas las máquinas virtuales clásicas de una suscripción mediante el uso de la Cuenta de ejecución clásica (certificado). A continuación, muestra los nombres y el estado de las máquinas virtuales. |
| Runbook AzureClassicAutomationTutorial Script |Ejemplo de un runbook de PowerShell. El runbook obtiene todas las máquinas virtuales clásicas de una suscripción mediante el uso de la Cuenta de ejecución clásica (certificado). A continuación, muestra los nombres y el estado de las máquinas virtuales. |
| AzureClassicRunAsCertificate |Se crea automáticamente un activo de certificado. El certificado realiza la autenticación en Azure, de modo que puede administrar los recursos clásicos de Azure desde los runbooks. Este certificado tiene una duración de un año. |
| AzureClassicRunAsConnection |Se crea automáticamente un activo de conexión. El activo realiza la autenticación en Azure, de modo que puede administrar los recursos clásicos de Azure desde los runbooks. |


## <a name="next-steps"></a>pasos siguientes
* Para obtener más información acerca de la creación de gráficos, consulte [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md).
* Para empezar a trabajar con runbooks de PowerShell, consulte [Mi primer runbook de PowerShell](automation-first-runbook-textual-powershell.md).
* Para empezar a trabajar con Runbooks de flujo de trabajo de PowerShell, consulte [Mi primer Runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md).
* Para empezar a trabajar con runbooks Python2, consulte [Mi primer runbook Python2](automation-first-runbook-textual-python2.md).
