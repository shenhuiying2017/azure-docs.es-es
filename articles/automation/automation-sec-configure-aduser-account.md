<properties
   pageTitle="Configuración de una cuenta de usuario de Azure AD | Microsoft Azure"
   description="En este artículo se describe cómo configurar credenciales de cuentas de usuario de Azure AD para Runbooks en Automatización de Azure para autenticarse en ARM y ASM."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="usuario de azure active directory, administración de servicios de azure, cuenta de usuario de azure ad" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/10/2016"
   ms.author="magoedte" />

# Autenticación de Runbooks con Administración de servicios de Azure y Resource Manager

En este artículo se describen los pasos que debe realizar para configurar una cuenta de usuario de Azure AD para Runbooks de Automatización de Azure que se ejecutan en recursos de Administración de servicios de Azure (ASM) o Azure Resource Manager (ARM). Aunque esta sigue siendo una identidad de autenticación admitida para sus Runbooks basados en ARM, el método recomendado es usar la nueva cuenta de ejecución de Azure.

## Creación de un usuario de Azure Active Directory nuevo

1. Inicie sesión en el Portal de Azure clásico como administrador de servicios para la suscripción de Azure que desea administrar.
2. Haga clic en **Active Directory** y seleccione el nombre del directorio de su organización.
3. Seleccione la pestaña **Usuarios** y, en el área de comandos, seleccione **Agregar usuario**.
4. En la página **Proporcione información sobre este usuario**, en **Tipo de usuario**, seleccione **Nuevo usuario de la organización**.
5. Escriba un nombre de usuario.  
6. Seleccione el nombre del directorio asociado con su suscripción de Azure en la página Active Directory.
7. En la página **Perfil de usuario**, especifique el nombre, los apellidos y un nombre descriptivo, así como un usuario de la lista **Roles**. No seleccione **Habilitar Multi-Factor Authentication**.
8. Observe el nombre completo y la contraseña temporal del usuario.
9. Seleccione **Configuración > Administradores > Agregar**.
10. Escriba el nombre de usuario completo del usuario que creó.
11. Seleccione la suscripción que desea que administre el usuario.
12. Cierre sesión en Azure y vuelva a iniciarla con la cuenta que acaba de crear. Se le pedirá que cambie la contraseña del usuario.


## Creación de una cuenta de automatización en el Portal de Azure clásico
En esta sección, llevará a cabo los pasos siguientes en el Portal de Azure para crear una nueva cuenta de Automatización de Azure que se usará con sus recursos de administración de Runbooks en modo ASM y ARM.

>[AZURE.NOTE] Las cuentas de Automatización que se crean con el Portal de Azure clásico se pueden administrar tanto con el Portal clásico como con el Portal de Azure, y con cualquiera de los conjuntos de cmdlets. Tras crear la cuenta da igual la forma en que cree y administre los recursos en la misma. Si piensa seguir usando el Portal de Azure clásico, debe utilizarlo en lugar del Portal de Azure para crear las cuentas de Automatización.


1. Inicie sesión en el Portal de Azure clásico como administrador de servicios para la suscripción de Azure que desea administrar.
2. Seleccione **Automatización**.
3. En la página **Automatización**, seleccione **Crear una cuenta de automatización**.
4. En el cuadro **Crear una cuenta de automatización**, escriba un nombre para la nueva cuenta de automatización y seleccione una **Región** en la lista desplegable.  
5. Haga clic en **Aceptar** para aceptar la configuración y crear la cuenta.
6. Después de crearla, se mostrará en la página **Automatización**.
7. Haga clic en la cuenta y le llevará a la página Panel.  
8. En la página Automation Dashboard (Panel de automatización), seleccione **Recursos**.
9. En la página **Recursos**, seleccione **Add Settings** (Agregar la configuración) de la parte inferior de la página.
10. En la página **Add Settings** (Agregar configuración), seleccione **Agregar credenciales**.
11. En la página **Definir credenciales**, seleccione **Credenciales de Windows PowerShell** de la lista desplegable **Tipo de credenciales** y escriba un nombre para las credenciales.
12. En la página **Definir credenciales** siguiente, escriba el nombre de usuario de la cuenta de AD creada anteriormente en el campo **Nombre de usuario** y la contraseña en los campos **Contraseña** y **Confirmar contraseña**. Haga clic en **Aceptar** para guardar los cambios.

## Creación de una cuenta de Automatización en el Portal de Azure

En esta sección, llevará a cabo los pasos siguientes en el Portal de Azure para crear una nueva cuenta de Automatización de Azure que se usará con sus recursos de administración de Runbooks en modo ARM.

1. Inicie sesión en el portal de Azure como administrador de servicios para la suscripción de Azure que desea administrar.
2. Seleccione **Cuentas de Automatización**.
3. En la hoja Cuentas de Automatización, haga clic en **Agregar**.<br>![Agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
2. En la hoja **Agregar cuenta de Automatización**, en el cuadro **Nombre**, escriba el nombre de la nueva cuenta de Automatización.
5. Si tiene más de una suscripción, especifique la de la cuenta nueva, así como un **grupo de recursos** nuevo o existente, y la **ubicación** de un centro de datos de Azure.
3. Seleccione el valor **No** en la opción **Crear cuenta de ejecución de Azure** y haga clic en el botón **Crear**.  

    >[AZURE.NOTE] Si decide no crear la cuenta de ejecución seleccionando la opción **No**, aparecerá un mensaje de advertencia en la hoja **Agregar cuenta de Automatización**. Aunque la cuenta se crea y se asigna al rol de **Colaborador** en la suscripción, esta no tendrá la identidad de autenticación correspondiente dentro del servicio de directorio de suscripciones y, por lo tanto, no tendrá acceso a los recursos de su suscripción. Esto impedirá que los runbooks que hacen referencia a esta cuenta puedan autenticarse y realizar tareas en los recursos del ARM.

    ![Advertencia para agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

4. Mientras Azure crea la cuenta de Automatización, puede seguir el progreso en **Notificaciones** en el menú.

Después de completar la creación de las credenciales, necesitará crear un recurso de credenciales para asociar la cuenta de Automatización con la cuenta de usuario de AD que creó anteriormente. Recuerde que solo creamos la cuenta de Automatización y que no está asociada con una identidad de autenticación. Siga los pasos descritos en el artículo [Recursos de credenciales en Automatización de Azure](../automation/automation-credentials.md#creating-a-new-credential) y escriba el valor de **nombre de usuario** con el formato **dominio\\usuario**.

## Uso de la credencial en un runbook

Puede recuperar la credencial en un runbook mediante el uso de la actividad [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) y, a continuación, úsela con [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) para conectarse a su suscripción de Azure. Si credencial es un administrador de varias suscripciones de Azure, deberá usar también [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) para especificar la suscripción correcta. Esto se muestra en el ejemplo de Windows PowerShell que aparece a continuación, que normalmente aparecerá en la parte superior de la mayoría de los runbooks de Automatización de Azure.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
	Add-AzureAccount –Credential $cred
	Select-AzureSubscription –SubscriptionName "My Subscription"

Deberá repetir estas líneas después de cada [punto de control](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) de su runbook. Si el runbook se suspende y luego se reanuda en otro trabajo, se deberá volver a realizar la autenticación.

## Pasos siguientes
* Revise los tipos de runbook diferentes y los pasos necesarios para crear sus propios runbooks en el siguiente artículo [Tipos de runbooks de Automatización de Azure](../automation/automation-runbook-types.md).

<!---HONumber=AcomDC_0511_2016-->