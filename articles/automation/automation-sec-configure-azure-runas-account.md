---
title: "Configuración de una cuenta de ejecución de Azure | Microsoft Docs"
description: "Este tutorial le guía en la creación, la prueba y el ejemplo de uso de la autenticación de la entidad de seguridad en Azure Automation."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: "nombre de la entidad de servicio, setspn, autenticación de azure"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/06/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: fbca3d195290551d37606e231b997a40a602351f
ms.lasthandoff: 03/29/2017


---

# <a name="authenticate-runbooks-with-an-azure-run-as-account"></a>Autenticación de Runbooks con una cuenta de ejecución de Azure
En este artículo se muestra cómo configurar una cuenta de Azure Automation en el portal de Azure. Para ello, usará la característica de cuentas de ejecución para autenticar runbooks que administran recursos de Azure Resource Manager o Azure Service Management.

Al crear una cuenta de Automation en el portal de Azure, crea automáticamente dos cuentas:

* Una cuenta de ejecución. Esta cuenta crea una entidad de servicio en Azure Active Directory (Azure AD) y un certificado. También asigna el control de acceso basado en rol (RBAC) del colaborador, que administra recursos de Resource Manager mediante runbooks.
* Una cuenta de ejecución clásica. Esta cuenta carga un certificado de administración, que se usa para administrar recursos clásicos o de Service Management mediante runbooks.

La creación de una cuenta de Automation simplifica el proceso y ayuda a empezar rápidamente a generar e implementar runbooks que respalden sus necesidades de automatización.

Con una cuenta de ejecución y una cuenta de ejecución clásica puede:

* Proporcionar una forma normalizada de autenticarse en Azure cuando administra recursos de Azure Resource Manager o de Azure Service Management desde runbooks en el portal de Azure.
* Automatizar el uso de runbooks globales, que puede configurar en Azure Alerts.

> [!NOTE]
> La [característica de integración de Azure Alerts](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) con Automation Global Runbooks necesita una cuenta de Automation que esté configurada como una cuenta de ejecución y una cuenta de ejecución clásica. Puede seleccionar una cuenta de Automation que tenga una cuenta de ejecución o una cuenta de ejecución clásica ya definida, o bien crear una nueva.
>  

En este artículo se muestra cómo crear una cuenta de Automation desde el portal de Azure, actualizarla mediante Azure PowerShell, administrar la configuración de la cuenta y autenticarla en sus runbooks.

Antes de empezar a crear una cuenta de Automation, es una buena idea entender y tener en cuenta lo siguiente:

* Crear una cuenta de Automation no afecta a las cuentas de Automation que se podrían haber ya creado en el modelo de implementación clásica o de Resource Manager.
* El proceso funciona únicamente para las cuentas de Automation que se crean en el portal de Azure. El intento de crear una cuenta desde el Portal de Azure clásico no replicará la configuración de la cuenta de ejecución.
* Si ya tiene runbooks y recursos (por ejemplo, variables o programaciones) para administrar recursos clásicos y quiere que los runbooks se autentiquen con la nueva cuenta de ejecución clásica, realice una de las siguientes acciones:

  * Para crear una cuenta de ejecución clásica, siga las instrucciones que aparecen en la sección "Administración de la cuenta de ejecución". 
  * Para actualizar la cuenta existente, use el script de PowerShell de la sección "Actualización de la cuenta de Automation mediante PowerShell".
* Para realizar la autenticación con la nueva cuenta de ejecución y la cuenta de Automation de ejecución clásica, debe modificar los runbooks existentes con código de ejemplo proporcionado en la sección [Código de ejemplo de autenticación](#authentication-code-examples).

    >[!NOTE]
    >La cuenta de ejecución se usa para la autenticación con los recursos de Resource Manager mediante la entidad de servicio basada en certificados. La cuenta de ejecución clásica se usa para la autenticación con los recursos de Service Management mediante el certificado de administración.

## <a name="create-an-automation-account-from-the-azure-portal"></a>Creación de una cuenta de Automation desde el portal de Azure
En esta sección, creará una cuenta de Azure Automation desde el portal de Azure, que a su vez crea una cuenta de ejecución y una cuenta de ejecución clásica.

>[!NOTE]
>Para crear una cuenta de Automation, debe ser miembro del rol Administradores de servicios o coadministrador de la suscripción que concede el acceso a la suscripción. También se le debe agregar como usuario a la instancia predeterminada de Active Directory de esa suscripción. No es necesario asignar a la cuenta un rol con privilegios.
>
>Si no es miembro de la instancia de Active Directory de la suscripción antes de que le agreguen al rol de coadministrador de la suscripción, se le agregará a Active Directory como invitado. En este caso, recibirá una advertencia "No tiene permisos para crear..." en la hoja **Agregar cuenta de Automation**.
>
>Los usuarios que primero se agregaron al rol de coadministrador se pueden quitar de la instancia de Active Directory de la suscripción y volverse a agregar para convertirlos en usuarios completos en Active Directory. Para comprobar esta situación, en el panel de **Azure Active Directory** del portal de Azure, seleccione **Usuarios y grupos**, **Todos los usuarios** y, después de seleccionar el usuario específico, seleccione **Perfil**. El valor del atributo **Tipo de usuario** del perfil de los usuarios no debería ser **Invitado**.
>

1. Inicie sesión en el portal de Azure con una cuenta que sea miembro del rol Administradores de la suscripción y coadministrador de la suscripción.

2. Seleccione **Cuentas de Automatización**.

3. En la hoja **Cuentas de Automation**, haga clic en **Agregar**.
Se abre la hoja **Agregar cuenta de Automation**.

 ![La hoja "Agregar cuenta de Automation"](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

   > [!NOTE]
   > Si la cuenta no es miembro del rol Administradores de la suscripción ni del rol de coadministrador de la suscripción, aparece la siguiente advertencia en la hoja **Agregar cuenta de Automation**:
   >
   >![Advertencia para agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   >
   >

4. En la hoja **Agregar cuenta de Automation**, en el cuadro **Nombre**, escriba el nombre de la nueva cuenta de Automation.

5. Si tiene más de una suscripción, haga lo siguiente:

    a. En **Suscripción**, especifique una para la nueva cuenta.

    b. En **Grupo de recursos**, haga clic en **Create new** (Crear nuevo) o **Use existing** (Usar existente).

    c. En **Ubicación**, especifique un centro de datos de Azure.

6. En **Crear cuenta de ejecución de Azure**, seleccione **Sí** y luego haga clic en **Crear**.

   > [!NOTE]
   > Si decide no crear la cuenta de ejecución, al seleccionar **No** aparecerá un mensaje de advertencia en la hoja **Agregar cuenta de Automation**. Aunque la cuenta se crea en el portal de Azure, no tiene una identidad de autenticación correspondiente en el servicio de directorio de suscripción clásico o de Resource Manager. Por lo tanto, la cuenta no tiene acceso a los recursos de su suscripción. Este escenario evita que los runbooks que hacen referencia a esta cuenta puedan autenticarse y realizar tareas con los recursos en dichos modelos de implementación.
   >
   > ![Mensaje de advertencia de la hoja "Agregar cuenta de Automation"](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)
   >
   > Además, dado que no se crea la entidad de servicio, no se asigna el rol de colaborador.
   >

7. Mientras Azure crea la cuenta de Automatización, se puede seguir el progreso de **Notificaciones** desde el menú.

### <a name="resources"></a>Recursos
Una vez que se crea la cuenta de Automatización, se también varios recursos automáticamente. Los recursos se resumen en las dos tablas siguientes:

#### <a name="run-as-account-resources"></a>Recursos de la cuenta de ejecución

| Recurso | Descripción |
| --- | --- |
| Runbook AzureAutomationTutorial | Un runbook gráfico de ejemplo que muestra cómo realizar la autenticación mediante la cuenta de ejecución y que obtiene todos los recursos de Resource Manager. |
| Runbook AzureAutomationTutorialScript | Un runbook de PowerShell de ejemplo que muestra cómo realizar la autenticación mediante la cuenta de ejecución y que obtiene todos los recursos de Resource Manager. |
| AzureRunAsCertificate | El recurso de certificado que se crea automáticamente al crear una cuenta de Automation. También puede usar el siguiente script de PowerShell para una cuenta existente. El certificado le permite realizar la autenticación en Azure, de modo que puede administrar los recursos de Azure Resource Manager desde los runbooks. Este certificado tiene una duración de un año. |
| AzureRunAsConnection | El recurso de conexión que se crea automáticamente al crear una cuenta de Automation; también puede usar el script de PowerShell para una cuenta existente. |

#### <a name="classic-run-as-account-resources"></a>Recursos de la cuenta de ejecución clásica

| Recurso | Descripción |
| --- | --- |
| Runbook AzureClassicAutomationTutorial | Un runbook gráfico de ejemplo que obtiene todas las máquinas virtuales que se crean con el modelo de implementación clásica en una suscripción mediante la cuenta de ejecución clásica (certificado) y luego escribe el nombre y el estado de la máquina virtual. |
| Runbook AzureClassicAutomationTutorial Script | Un runbook de PowerShell de ejemplo que obtiene todas las máquinas virtuales clásicas de una suscripción mediante la cuenta de ejecución clásica (certificado) y luego escribe el nombre y el estado de la máquina virtual. |
| AzureClassicRunAsCertificate | Un recurso de certificado creado automáticamente que se usa para realizar la autenticación en Azure, de modo que pueda administrar los recursos del modelo clásico de Azure mediante runbooks. Este certificado tiene una duración de un año. |
| AzureClassicRunAsConnection | El recurso de conexión creado automáticamente que se usa para realizar la autenticación en Azure, de modo que pueda administrar los recursos del modelo clásico de Azure mediante runbooks. |

## <a name="verify-run-as-authentication"></a>Comprobación de la autenticación de ejecución
Realice una pequeña prueba para confirmar que puede autenticarse correctamente mediante la nueva cuenta de ejecución.

1. En el Portal de Azure, abra la cuenta de Automatización que creó anteriormente.

2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.

3. Seleccione el runbook **AzureAutomationTutorialScript** y haga clic en **Iniciar** para abrirlo. Se producen los siguientes eventos:
 * Se crea un [trabajo de runbook](automation-runbook-execution.md), se muestra la hoja **Trabajo** y el estado del trabajo aparece en el icono **Resumen del trabajo**.
 * El estado del trabajo comienza como **En cola**, lo que indica que está esperando a que haya algún trabajo de runbook disponible en la nube.
 * El estado pasa a ser **Iniciando** cuando un trabajo de runbook solicita el trabajo.
 * El estado pasa a ser **En ejecución** cuando el runbook comienza a ejecutarse.
 * Cuando el trabajo del runbook ha terminado de ejecutarse, debería ver un estado de **Completado**.

       ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)
4. Para ver los resultados detallados del runbook, haga clic en el icono **Salida**.  
Aparece la hoja **Salida**, que muestra que ese runbook se ha autenticado correctamente y ha devuelto una lista de todos los recursos disponibles en el grupo de recursos.

5. Cierre la hoja **Salida** para volver a la hoja **Resumen del trabajo**.

6. Cierre la hoja **Resumen del trabajo** y la hoja del runbook **AzureAutomationTutorialScript** correspondiente.

## <a name="verify-classic-run-as-authentication"></a>Comprobación de la autenticación de ejecución de Azure clásico
Realice una pequeña prueba para confirmar que puede autenticarse correctamente mediante la nueva cuenta de ejecución clásica.

1. En el Portal de Azure, abra la cuenta de Automatización que creó anteriormente.

2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.

3. Seleccione el runbook **AzureClassicAutomationTutorialScript** y haga clic en **Iniciar** para abrirlo. Se producen los siguientes eventos:

 * Se crea un [trabajo de runbook](automation-runbook-execution.md), se muestra la hoja **Trabajo** y el estado del trabajo aparece en el icono **Resumen del trabajo**.
 * El estado del trabajo comienza como **En cola**, lo que indica que está esperando a que haya algún trabajo de runbook disponible en la nube.
 * El estado pasa a ser **Iniciando** cuando un trabajo de runbook solicita el trabajo.
 * El estado pasa a ser **En ejecución** cuando el runbook comienza a ejecutarse.
 * Cuando el trabajo del runbook ha terminado de ejecutarse, debería ver un estado de **Completado**.

    ![Prueba de Runbook de entidad de seguridad](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
4. Para ver los resultados detallados del runbook, haga clic en el icono **Salida**.  
Aparece la hoja **Salida**, que muestra que el runbook se ha autenticado correctamente y ha devuelto una lista de todas las máquinas virtuales clásicas de la suscripción.

5. Cierre la hoja **Salida** para volver a la hoja **Resumen del trabajo**.

6. Cierre la hoja **Resumen del trabajo** y la hoja del runbook **AzureAutomationTutorialScript** correspondiente.

## <a name="managing-your-run-as-account"></a>Administración de la cuenta de ejecución
En algún momento antes de que expire su cuenta de Automation, deberá renovar el certificado. Si cree que se ha puesto en peligro la cuenta de ejecución, puede eliminarla y volver a crearla. En esta sección se describe cómo realizar estas operaciones.

### <a name="self-signed-certificate-renewal"></a>Renovación de certificado autofirmado
El certificado autofirmado que creó para la cuenta de ejecución expira un año a partir de la fecha de creación. Se puede renovar en cualquier momento antes de que expire. Cuando se renueva, el certificado válido actual se conserva para tener la seguridad de que los runbooks que están en cola o que se están ejecutando activamente y que se autentican con la cuenta de ejecución, no resultan afectados negativamente. El certificado es válido hasta la fecha de expiración.

> [!NOTE]
> Si ha configurado la cuenta de ejecución de Automation para que use un certificado emitido por una entidad de certificación de empresa y usa esta opción, ese certificado se reemplazará por un certificado autofirmado.

Para renovar el certificado, realice estos pasos:

1. Abra la cuenta de Automation en Azure Portal.

2. En la hoja **Cuenta de Automation**, en el panel **Account properties** (Propiedades de la cuenta), en **Account Settings** (Configuración de la cuenta), seleccione **Run As Accounts** (Cuentas de ejecución).

    ![Panel de propiedades de la cuenta de Automation](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)
3. En la hoja de propiedades **Run As Accounts** (Cuentas de ejecución), seleccione la cuenta de ejecución o la cuenta de ejecución clásica para la que quiere renovar el certificado.

4. En la hoja **Propiedades** de la cuenta seleccionada, haga clic en **Renovar certificado**.

    ![Renovación del certificado para una cuenta de ejecución](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)

5. Mientras se está renovando el certificado, puede seguir el progreso desde el menú, en **Notificaciones**.

### <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminación de una cuenta de ejecución o de ejecución clásica
En esta sección se describe cómo eliminar y volver a crear una cuenta de ejecución o de ejecución clásica. Al realizar esta acción, la cuenta de Automation se conserva. Después de eliminar una cuenta de ejecución o de ejecución clásica, puede volver a crearla en el portal de Azure.

1. Abra la cuenta de Automation en Azure Portal.

2. En la hoja **Cuenta de Automation**, en el panel de propiedades de la cuenta, seleccione **Run As Accounts** (Cuentas de ejecución).

3. En la hoja de propiedades **Run As Accounts** (Cuentas de ejecución), seleccione la cuenta de ejecución o la cuenta de ejecución clásica que quiere eliminar. A continuación, en la hoja **Propiedades** de la cuenta seleccionada, haga clic en **Eliminar**.

 ![Eliminación de una cuenta de ejecución](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)

4. Mientras se está eliminando la cuenta, puede seguir el progreso desde el menú, en **Notificaciones**.

5. Después de eliminar la cuenta, puede volver a crearla en la hoja de propiedades **Run As Accounts** (Cuentas de ejecución) mediante la selección de la opción de creación **Cuenta de ejecución de Azure**.

 ![Nueva creación de la cuenta de ejecución de Automation](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)

### <a name="misconfiguration"></a>Error de configuración
Puede que alguno de los elementos de configuración necesarios para que la cuenta de ejecución o la cuenta de ejecución clásica funcionen correctamente se haya eliminado o no se haya creado correctamente durante la configuración inicial. Estos elementos son:

* Recurso de certificado
* Recurso de conexión
* La cuenta de ejecución se ha quitado del rol de colaborador
* Entidad de servicio o aplicación en Azure AD

En los casos anteriores y otros casos de errores de configuración, la cuenta de Automation detecta los cambios y muestra el estado *Incomplete* (Incompleto) en la hoja de propiedades **Run As Accounts** (Cuentas de ejecución) de la cuenta.

![Estado de configuración incompleta de la cuenta de ejecución](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)

Al seleccionar la cuenta de ejecución, el panel **Propiedades** muestra el mensaje de error siguiente:

![Mensaje de advertencia de configuración incompleta de la cuenta de ejecución](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png).

Rápidamente puede resolver estos problemas de la cuenta de ejecución con solo eliminarla cuenta y volver a crearla.

## <a name="update-your-automation-account-by-using-powershell"></a>Actualización de la cuenta de Automation mediante PowerShell
Puede usar PowerShell para actualizar su cuenta de Automation existente si:

* Crea una cuenta de Automation, pero se rechaza la creación de la cuenta de ejecución.
* Ya usa una cuenta de Automation para administrar recursos de Resource Manager y quiere actualizarla para incluir la cuenta de ejecución para la autenticación de runbooks.
* Ya usa una cuenta de Automation para administrar recursos del modelo clásico y quiere actualizarla para usar la cuenta de ejecución en lugar de crear una nueva cuenta y migrar los runbooks y recursos a ella.   
* Quiere crear una cuenta de ejecución y una cuenta de ejecución clásica mediante un certificado emitido por una entidad de certificación (CA) de empresa.

Este script tiene los siguientes requisitos previos:

* El script solo se puede ejecutar en Windows 10 y Windows Server 2016 con módulos de Azure Resource Manager 2.01 y versiones posteriores. No se admite en versiones anteriores de Windows.
* Azure PowerShell 1.0 y versiones superiores. Para más información sobre la versión 1.0 de PowerShell, consulte [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Una cuenta de Automation, a la que se hace referencia como el valor de los parámetros *: -AutomationAccountName* y *- ApplicationDisplayName* en el siguiente script de PowerShell.

Para obtener los valores de *SubscriptionID*, *ResourceGroup* y *AutomationAccountName*, que son parámetros necesarios para los scripts, haga lo siguiente:
1. En el portal de Azure, seleccione su cuenta de Automation en la hoja **Cuenta de Automation** y luego seleccione **All settings** (Toda la configuración). 
2. En la hoja **All settings** (Toda la configuración), en **Account Settings** (Configuración de la cuenta), seleccione **Propiedades**. 
3. Tenga en cuenta los valores de la hoja **Propiedades**.

 ![Hoja "Propiedades" de la cuenta de Automation](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-a-run-as-account-powershell-script"></a>Creación de un script de PowerShell de una cuenta de ejecución
Este script de PowerShell incluye compatibilidad con las siguientes configuraciones:

* Creación de una cuenta de ejecución mediante un certificado autofirmado.
* Creación de una cuenta de ejecución y una cuenta de ejecución clásica mediante un certificado autofirmado.
* Creación de una cuenta de ejecución y una cuenta de ejecución clásica mediante un certificado de empresa.
* Creación de una cuenta de ejecución y una cuenta de ejecución clásica mediante un certificado autofirmado en la nube de Azure Government.

Dependiendo de la opción de configuración seleccionada, el script crea los siguientes elementos.

**Para cuentas de ejecución:**

* Crea una aplicación de Azure AD que se exporta con la clave pública del certificado autofirmado o de empresa, crea una cuenta de entidad de servicio para esta aplicación en Azure AD y asigna el rol Colaborador para esta cuenta en su suscripción actual. Puede cambiar esta configuración a Propietario o cualquier otro rol. Para más información, consulte [Control de acceso basado en rol en Azure Automation](automation-role-based-access-control.md).
* Crea un recurso de certificado de Automation llamado *AzureRunAsCertificate* en la cuenta de Automation especificada. El recurso de certificado contiene la clave privada del certificado que usa la aplicación de Azure AD.
* Crea un recurso de conexión de Automation llamado *AzureRunAsConnection* en la cuenta de Automation especificada. El recurso de conexión contiene el id. de aplicación, el id. de inquilino, el id. de suscripción y la huella digital de certificado.

**Para cuentas de ejecución clásicas:**

* Crea un recurso de certificado de Automation llamado *AzureClassicRunAsCertificate* en la cuenta de Automation especificada. El recurso de certificado contiene la clave privada del certificado que usa el certificado de administración.
* Crea un recurso de conexión de Automation llamado *AzureClassicRunAsConnection* en la cuenta de Automation especificada. El recurso de conexión contiene el nombre de la suscripción, el id. de suscripción y el nombre del recurso de certificado.

>[!NOTE]
> Si selecciona cualquiera de las opciones para crear una cuenta de ejecución clásica, cargue el certificado público (extensión de nombre del archivo .cer) en el almacén de administración para la suscripción en la que se creó la cuenta de Automation.
> 

Para ejecutar el script y cargar el certificado, realice lo siguiente:

1. Guarde el script siguiente en el equipo. En este ejemplo, guárdelo con el nombre *New-RunAsAccount.ps1*.

        #Requires -RunAsAdministrator
         Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.EndDate = $KeyCredential.EndDate.AddDays(-1)
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use key credentials and create an Azure AD application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }

        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create a Run As account by using a service principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"

        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create a service principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}


        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create a Run As account by using a service principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#"

             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. En el equipo, haga clic en **Inicio** y luego inicie **Windows PowerShell** con derechos de usuario elevados.

3. Desde el shell de línea de comandos de PowerShell con privilegios elevados, vaya a la carpeta que contiene el script que creó en el paso 1.

4. Ejecute el script mediante los valores de parámetro de la configuración que necesita.

    **Creación de una cuenta de ejecución mediante un certificado autofirmado**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Creación de una cuenta de ejecución y una cuenta de ejecución clásica mediante un certificado autofirmado**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Creación de una cuenta de ejecución y una cuenta de ejecución clásica mediante un certificado de empresa**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Creación de una cuenta de ejecución y una cuenta de ejecución clásica mediante un certificado autofirmado en la nube de Azure Government**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > Después de ejecutar el script, se le pedirá que se autentique en Azure. Inicie sesión con una cuenta que sea miembro del rol Administradores de suscripciones y coadministrador de la suscripción.
    >
    >

Una vez que el script se ejecuta correctamente, observe lo siguiente:
* Si creó una cuenta de ejecución clásica con un certificado público autofirmado (formato .cer), el script lo crea y lo guarda en la carpeta de archivos temporales del equipo con el perfil de usuario *%USERPROFILE%\AppData\Local\Temp*, que se usa para ejecutar la sesión de PowerShell.
* Si creó una cuenta de identificación clásica con un certificado público de empresa (archivo .cer) , use este certificado. Siga las instrucciones para [cargar un certificado de API de administración en el Portal de Azure clásico](../azure-api-management-certs.md) y luego valide la configuración de credenciales con recursos de Service Management mediante el [código de ejemplo para realizar la autenticación con recursos de Service Management](#sample-code-to-authenticate-with-service-management-resources). 
* Si *no* creó una cuenta de ejecución clásica, realice la autenticación con recursos de Resource Manager y valide la configuración de credenciales mediante el [código de ejemplo para la autenticación con recursos de Service Management](#sample-code-to-authenticate-with-resource-manager-resources).

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Código de ejemplo para autenticarse con recursos de Resource Manager
Puede usar el código de ejemplo actualizado siguiente, procedente del runbook de ejemplo *AzureAutomationTutorialScript*, para realizar la autenticación con la cuenta de ejecución y administrar los recursos de Resource Manager con sus runbooks.

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
       "Setting context to a specific subscription"     
       Set-AzureRmContext -SubscriptionId $SubId              
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    }

Para ayudar a trabajar fácilmente entre varias suscripciones, el script incluye dos líneas adicionales de código que admiten la referencia a un contexto de suscripción. Un recurso de variable llamado *SubscriptionId* contiene el id. de la suscripción. Después de la instrucción del cmdlet `Add-AzureRmAccount`, el cmdlet [`Set-AzureRmContext`](https://msdn.microsoft.com/library/mt619263.aspx) se declara con el conjunto de parámetros *-SubscriptionId*. Si el nombre de variable es demasiado genérico, puede revisarlo para incluir un prefijo o usar otra convención de nomenclatura para que resulte más fácil de identificar. Como alternativa, puede usar el conjunto de parámetros *-SubscriptionName* en lugar de *-SubscriptionId* con un recurso de variable correspondiente.

El cmdlet que usa para la autenticación en el runbook, `Add-AzureRmAccount`, emplea el conjunto de parámetros *ServicePrincipalCertificate*. La autenticación se realiza mediante el certificado de la entidad de servicio, y no con las credenciales de usuario.

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Código de ejemplo para autenticarse con los recursos de Service Management
Puede usar el código de ejemplo actualizado siguiente, procedente del runbook de ejemplo *AzureClassicAutomationTutorialScript*, para realizar la autenticación mediante la cuenta de ejecución clásica y administrar los recursos del modelo clásico con sus runbooks.

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID

## <a name="next-steps"></a>Pasos siguientes
* [Objetos de aplicación y de entidad de servicio en Azure AD](../active-directory/active-directory-application-objects.md)
* [Control de acceso basado en rol en Azure Automation](automation-role-based-access-control.md)
* [Introducción a los certificados para Azure Cloud Services](../cloud-services/cloud-services-certs-create.md)

