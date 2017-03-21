---
title: "Configuración de una cuenta de ejecución de Azure | Microsoft Docs"
description: "Tutorial que le guía a través de la creación, prueba y uso de ejemplo de la autenticación de la entidad de seguridad en Automatización de Azure."
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
ms.date: 03/10/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 15cbf897f3f67b9d1bee0845b4d287fdabe63ba8
ms.lasthandoff: 03/11/2017


---
# <a name="authenticate-runbooks-with-azure-run-as-account"></a>Autenticación de Runbooks con una cuenta de ejecución de Azure
En este tema se muestra cómo configurar una cuenta de Automation en Azure Portal mediante la característica de cuenta de ejecución para autenticar runbooks que administran recursos en Azure Resource Manager o Azure Service Management.

Al crear una cuenta de Automation en el Azure Portal, se crea automáticamente:

* Una cuenta de ejecución, que crea una entidad de servicio en Azure Active Directory, un certificado, y asigna el control de acceso basado en rol (RBAC) de Colaborador, que se usa para administrar recursos de Resource Manager mediante runbooks.   
* Una cuenta de ejecución clásica mediante la carga de un certificado de administración, que se usa para administrar Azure Service Management o recursos clásicos mediante runbooks.  

Esto simplifica el proceso y ayuda a que empezar rápidamente a generar e implementar Runbooks que den soporte técnico a sus necesidades de automatización.      

Con una cuenta de ejecución y una cuenta de ejecución de Azure clásico puede:

* Proporcionar una forma normalizada de autenticarse en Azure al administrar los recursos de Azure Resource Manager o Azure Service Management desde Runbooks en el Portal de Azure.  
* Automatizar el uso de runbooks globales configurados en Azure Alerts.

> [!NOTE]
> La [característica de integración de alertas](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) de Azure con Automation Global Runbooks necesita una cuenta de Automation que esté configurada como una cuenta de ejecución y una cuenta de ejecución clásica. Puede seleccionar una cuenta de Automatización que ya tenga una cuenta de ejecución y de ejecución de Azure clásico definida, o bien crear una nueva.
>  

Le mostramos cómo crear la cuenta de Automation desde Azure Portal, actualizarla mediante PowerShell, administrar la configuración de la cuenta y demostrar cómo autenticarla en sus runbooks.

Antes de hacerlo, hay algunas cosas que debe conocer y considerar.

1. Esto no afecta a las cuentas de Automatización existentes creadas con el modelo de implementación clásico o con Resource Manager.  
2. Esto solo funcionará para las cuentas de Automatización creadas a través del Portal de Azure.  El intento de crear una cuenta desde el portal clásico no replicará la configuración de la cuenta de ejecución.
3. Si actualmente tiene runbooks y recursos (es decir, programaciones, variables, etc.) que creó anteriormente para administrar recursos clásicos y desea que dichos runbooks se autentiquen con la nueva cuenta de ejecución clásica, debe crear dicha cuenta mediante Administración de una cuenta de ejecución o actualizar su cuenta existente mediante el siguiente script de PowerShell.  
4. Para realizar la autenticación con la nueva cuenta de ejecución y la cuenta de Automation clásica, debe modificar los runbooks existentes con el código de ejemplo proporcionado en la sección [Ejemplos de código de autenticación](#authentication-code-examples).  
   
    >[!NOTE] 
    >La cuenta de ejecución es para realizar la autenticación con los recursos de Resource Manager mediante la entidad de servicio basada en certificado, mientras que la cuenta de ejecución clásica es para realizar la autenticación con los recursos de Service Management con un certificado de administración.     

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Creación de una nueva cuenta de Automation desde Azure Portal
En esta sección, realizará los pasos que se describen a continuación para crear una nueva cuenta de Azure Automation desde Azure Portal.  Así se crean tanto la cuenta de ejecución como la cuenta de ejecución de Azure clásico.  

> [!NOTE]
> El usuario que realice estos pasos debe ser miembro del rol Administradores de suscripciones o coadministrador de la suscripción que concede el acceso a la suscripción al usuario. También se debe agregar como usuario a la instancia predeterminada de Azure Active Directory de dicha suscripción; no es preciso asignar un rol con privilegios a la cuenta. Los usuarios que no sean miembros de Active Directory de la suscripción antes de agregarse al rol de Coadministrador de la suscripción se agregarán a Active Directory como Invitado y verán la advertencia "No tiene permisos para crear..." en la hoja **Agregar cuenta de Automation**. Los usuarios que se han agregado al rol de Coadministrador se pueden quitar de Active Directory de las suscripciones y se vuelven a agregar para que sean un usuario completo en Active Directory. Esta situación se puede comprobar desde el panel **Azure Active Directory** de Azure Portal mediante la selección de **Usuarios y grupos**, después **Todos los usuarios** y, tras seleccionar el usuario específico, seleccione **Perfil**.  El valor del atributo **Tipo de usuario** del perfil de los usuarios no debería ser **Invitado**.  
> 

1. Inicie sesión en Azure Portal con una cuenta que sea miembro del rol Administradores de suscripciones y coadministrador de la suscripción.
2. Seleccione **Cuentas de Automatización**.
3. En la hoja Cuentas de Automatización, haga clic en **Agregar**.<br>![Agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)
   
   > [!NOTE]
   > Si aparece la advertencia siguiente en la hoja **Agregar cuenta de Automatización**, se debe a que la cuenta no es miembro del rol Administradores de suscripciones ni coadministradora de la suscripción.<br>![Advertencia para agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   > 
   > 
4. En la hoja **Agregar cuenta de Automatización**, en el cuadro **Nombre**, especifique un nombre para la nueva cuenta de Automation.
5. Si tiene varias suscripciones, especifique una para la nueva cuenta, así como un **grupo de recursos** nuevo y existente y la **ubicación** de un centro de datos de Azure.
6. Compruebe que el valor **Sí** está seleccionado en la opción **Crear cuenta de ejecución de Azure** y haga clic en el botón **Crear**.  
   
   > [!NOTE]
   > Si decide seleccionar la opción **No** porque no quiere crear la cuenta de ejecución, aparecerá un mensaje de advertencia en la hoja **Agregar cuenta de Automatización**.  Aunque la cuenta se crea en el Portal de Azure, no tendrá la identidad de autenticación correspondiente en el servicio de directorio de suscripciones clásico o de Resource Manager y, por lo tanto, no tendrá acceso a los recursos de su suscripción.  Esto evitará que los Runbooks que hacen referencia a esta cuenta puedan autenticarse y realizar tareas con los recursos en dichos modelos de suscripción.
   > 
   > ![Advertencia para agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br>
   > Si no se crea la entidad de servicio, no se asignará el rol Colaborador.
   > 

7. Mientras Azure crea la cuenta de Automatización, se puede seguir el progreso de **Notificaciones** desde el menú.

### <a name="resources-included"></a>Recursos incluidos
Una vez que se crea la cuenta de Automatización, se también varios recursos automáticamente.  La siguiente tabla resume los recursos de la cuenta de ejecución.<br>

| Recurso | Descripción |
| --- | --- |
| Runbook AzureAutomationTutorial |Un runbook gráfico de ejemplo que muestra cómo realizar la autenticación mediante la cuenta de ejecución y que obtiene todos los recursos de Resource Manager. |
| Runbook AzureAutomationTutorialScript |Un Runbook de PowerShell de ejemplo que muestra cómo realizar la autenticación mediante la cuenta de ejecución y que obtiene todos los recursos de Resource Manager. |
| AzureRunAsCertificate |Recurso de certificado creado automáticamente durante la creación de la cuenta de Automation o mediante el siguiente script de PowerShell de una cuenta existente.  Permite realizar la autenticación con Azure, con el fin de poder administrar los recursos de Azure Resource Manager desde los Runbooks.  Este certificado tiene una duración de un año. |
| AzureRunAsConnection |Recurso de conexión creado automáticamente durante la creación de la cuenta de Automatización o mediante el siguiente script de PowerShell de una cuenta existente. |

La siguiente tabla resume los recursos de la cuenta de ejecución de Azure clásico.<br>

| Recurso | Descripción |
| --- | --- |
| Runbook AzureClassicAutomationTutorial |Un runbook gráfico de ejemplo que obtiene todas las máquinas virtuales clásicas de una suscripción mediante la cuenta de ejecución de Azure clásico (certificado) y, luego, envía el nombre y el estado de la máquina virtual. |
| Runbook AzureClassicAutomationTutorial Script |Un runbook de PowerShell de ejemplo que obtiene todas las máquinas virtuales clásicas de una suscripción mediante la cuenta de ejecución de Azure clásico (certificado) y, luego, envía el nombre y el estado de la máquina virtual. |
| AzureClassicRunAsCertificate |Recurso de certificado creado automáticamente que se utiliza para realizar la autenticación en Azure, de modo sea posible administrar los recursos del modelo clásico de Azure desde Runbooks.  Este certificado tiene una duración de un año. |
| AzureClassicRunAsConnection |Recurso de conexión creado automáticamente que se utiliza para realizar la autenticación en Azure, de modo sea posible administrar los recursos del modelo clásico de Azure desde Runbooks. |

## <a name="verify-run-as-authentication"></a>Comprobación de la autenticación de ejecución
A continuación, realizaremos una pequeña prueba para confirmar que puede autenticarse correctamente mediante la nueva cuenta de ejecución.     

1. En Azure Portal, abra la cuenta de Automation que creó anteriormente.  
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Seleccione el runbook **AzureAutomationTutorialScript** y haga clic en **Iniciar** para abrirlo.  Recibirá un aviso para comprobar que desea que se inicie el Runbook.
4. Se crea un [trabajo de Runbook](automation-runbook-execution.md) , se muestra la hoja Trabajo y el estado del trabajo aparece en el icono **Resumen del trabajo** .  
5. El estado del trabajo se iniciará como *En cola* , lo que indica que está esperando a que haya algún trabajo de Runbook disponible en la nube. Su estado cambiará a *Iniciando* cuando un trabajo de runbook lo solicite. Cuando el runbook comience a ejecutarse realmente, el estado será *En ejecución*.  
6. Cuando el trabajo de runbook se complete, debería aparecer el estado **Completado**.<br> ![Prueba de Runbook de entidad de seguridad](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. Para ver los resultados detallados del Runbook, haga clic en el icono **Salida** .
8. En la hoja **Salida** , debería ver que se ha autenticado correctamente y ha devuelto una lista de todos los recursos disponibles en el grupo de recursos.
9. Cierre la hoja **Salida** para volver a la hoja **Resumen del trabajo**.
10. Cierre la hoja **Resumen del trabajo** y la hoja del runbook **AzureAutomationTutorialScript**.

## <a name="verify-classic-run-as-authentication"></a>Comprobación de la autenticación de ejecución de Azure clásico
A continuación, realizaremos una pequeña prueba para confirmar que puede autenticarse correctamente mediante la nueva cuenta de ejecución de Azure clásico.     

1. En Azure Portal, abra la cuenta de Automation que creó anteriormente.  
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Seleccione el runbook **AzureClassicAutomationTutorialScript** y haga clic en **Iniciar** para abrirlo.  Recibirá un aviso para comprobar que desea que se inicie el Runbook.
4. Se crea un [trabajo de Runbook](automation-runbook-execution.md) , se muestra la hoja Trabajo y el estado del trabajo aparece en el icono **Resumen del trabajo** .  
5. El estado del trabajo se iniciará como *En cola* , lo que indica que está esperando a que haya algún trabajo de Runbook disponible en la nube. Su estado cambiará a *Iniciando* cuando un trabajo de runbook lo solicite. Cuando el runbook comience a ejecutarse realmente, el estado será *En ejecución*.  
6. Cuando el trabajo de runbook se complete, debería aparecer el estado **Completado**.<br> ![Prueba de Runbook de entidad de seguridad](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. Para ver los resultados detallados del Runbook, haga clic en el icono **Salida** .
8. En la hoja **Salida** , debería ver que se ha autenticado correctamente y ha devuelto una lista de todas las máquinas virtuales con el modelo clásico que hay en la suscripción.
9. Cierre la hoja **Salida** para volver a la hoja **Resumen del trabajo**.
10. Cierre la hoja **Resumen del trabajo** y la hoja del runbook **AzureClassicAutomationTutorialScript**.

## <a name="managing-azure-run-as-account"></a>Administración de una cuenta de ejecución de Azure
Durante la vida útil de la cuenta de Automation, deberá renovar el certificado antes de que expire o, si cree que la cuenta está en peligro, puede eliminar la cuenta de ejecución y crearla de nuevo.  En esta sección se proporcionan los pasos para realizar estas operaciones.  

### <a name="self-signed-certificate-renewal"></a>Renovación de certificado autofirmado
El certificado autofirmado creado para la cuenta de ejecución de Azure se puede renovar en cualquier momento, hasta que expire, que es un año a partir de la fecha de creación.  Cuando se renueva, el certificado válido antiguo se conserva para garantizar que los runbooks puestos en la cola activos o que se están ejecutando en ese momento, que se autentican con la cuenta de ejecución, no se verán afectados.  El certificado seguirá existiendo hasta que expire.    

> [!NOTE]
> Si ha configurado la cuenta de ejecución de Automation para que use un certificado emitido por una entidad de certificación de la empresa y usa esta opción, ese certificado se reemplazará por un certificado autofirmado.  

1. Abra la cuenta de Automation en Azure Portal.  
2. En la hoja de la cuenta de Automation, en el panel de propiedades de la cuenta, seleccione **Run As Accounts** (Cuentas de ejecución) en la sección **Account Settings** (Configuración de la cuenta).<br><br> ![Panel de propiedades de la cuenta de Automation](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)<br><br>
3. En la hoja de propiedades **Run As Accounts** (Cuentas de ejecución), seleccione la cuenta de ejecución o la cuenta de ejecución clásica para la que quiere renovar el certificado y, en la hoja de propiedades de la cuenta seleccionada, haga clic en **Renovar certificado**.<br><br> ![Renovación de un certificado para una cuenta de ejecución](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)<br><br> Recibirá un aviso para comprobar que desea continuar.  
4. Mientras se está renovando el certificado, puede seguir el progreso desde el menú, en **Notificaciones**.

### <a name="delete-run-as-account"></a>Eliminación de una cuenta de ejecución
Los pasos siguientes describen cómo eliminar y volver a crear la cuenta de ejecución de Azure o la cuenta de ejecución de Azure clásico.  Al realizar esta acción, la cuenta de Automation se conserva.  Después de eliminar la cuenta de ejecución o la cuenta de ejecución clásica, puede volver a crearla en el portal.  

1. Abra la cuenta de Automation en Azure Portal.  
2. En la hoja de la cuenta de Automation, en el panel de propiedades de la cuenta, seleccione **Run As Accounts** (Cuentas de ejecución) en la sección **Account Settings** (Configuración de la cuenta).
3. En la hoja de propiedades **Run As Accounts** (Cuentas de ejecución), seleccione la cuenta de ejecución o la cuenta de ejecución clásica que quiere eliminar y, en la hoja de propiedades de la cuenta seleccionada, haga clic en **Eliminar**.<br><br> ![Eliminación de una cuenta de ejecución](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)<br><br>  Recibirá un aviso para comprobar que desea continuar.
4. Mientras se está eliminando la cuenta, puede seguir el progreso desde el menú, en **Notificaciones**.  Cuando haya finalizado la eliminación, puede volver a crearla en la hoja de propiedades **Cuentas de ejecución** y seleccionar la opción de creación de la **cuenta de ejecución de Azure**.<br><br> ![Nueva creación de la cuenta de ejecución de Automation](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)<br> 

### <a name="misconfiguration"></a>Error de configuración
Si alguno de los elementos de configuración necesarios para que la cuenta de ejecución o la cuenta de ejecución clásica funcionen correctamente se elimina o no se ha creado correctamente durante la configuración inicial, como:

* Recurso de certificado 
* Recurso de conexión 
* La cuenta de ejecución se ha quitado del rol de colaborador
* Entidad de servicio o aplicación en Azure AD

Automation detectará estos cambios y se los notificará con un estado de **Incompleto** en la hoja de propiedades **Cuentas de ejecución** de la cuenta.<br><br> ![Mensaje de estado de configuración de ejecución incompleta](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)<br><br>Cuando se selecciona la cuenta de ejecución, aparece el siguiente error en el panel de propiedades de la cuenta:<br><br> ![Mensaje de advertencia de configuración de ejecución incompleta](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png).<br>  
Si su cuenta de ejecución está mal configurada, puede eliminarla y crearla de nuevo.   

## <a name="update-an-automation-account-using-powershell"></a>Actualización de una cuenta de Automatización mediante PowerShell
Aquí se brinda la opción de usar PowerShell para actualizar una cuenta de Automatización existente si:

1. Se creó una cuenta de Automatización, pero se rechazó crear la cuenta de ejecución 
2. Ya se dispone de una cuenta de Automatización que administre los recursos de Resource Manager y se desea actualizar para incluir la cuenta de ejecución para la autenticación de Runbooks
4. Ya se dispone de una cuenta de Automatización que administre los recursos del modelo clásico y se desea actualizar para que use la ejecución de Azure clásico, en lugar de crear una nueva cuenta y migrar los Runbooks y recursos a ella   
5. Desea crear una cuenta de ejecución y una cuenta de ejecución clásica de Azure mediante un certificado emitido por la entidad de certificación de su empresa.

Este script tiene los siguientes requisitos previos:

1. Este script solo se puede ejecutar en Windows 10 y Windows Server 2016 con los módulos de Azure Resource Manager 2.01 y versiones superiores instalados.  No se admite en versiones anteriores de Windows.  
2. Azure PowerShell 1.0 y versiones superiores. Para obtener información acerca de esta versión y cómo instalarla, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs).
3. Ha creado una cuenta de Automatización.  Se hará referencia a esta cuenta como valor para los parámetros-AutomationAccountName y - ApplicationDisplayName en el siguiente script.

Para obtener los valores de *SubscriptionID*, *ResourceGroup* y *AutomationAccountName*, que son los parámetros necesarios para los scripts, en Azure Portal, seleccione la cuenta de Automation en la hoja **Cuenta de Automatización** y seleccione **Todas las opciones**.  En la hoja **Toda la configuración**, en **Configuración de la cuenta**, seleccione **Propiedades**.  En la hoja **Propiedades**, puede anotar estos valores.<br><br> ![Propiedades de una cuenta de Automatización](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-run-as-account-powershell-script"></a>Creación de un script de PowerShell de una cuenta de ejecución
Este script de PowerShell incluye compatibilidad con las siguientes configuraciones: 

* Creación de cuenta de ejecución de Azure mediante un certificado autofirmado
* Creación de cuenta de ejecución y de cuenta de ejecución clásica de Azure mediante un certificado autofirmado
* Creación de cuenta de ejecución y de cuenta de ejecución clásica de Azure mediante un certificado de la empresa
* Creación de cuenta de ejecución y de cuenta de ejecución clásica de Azure mediante un certificado autofirmado en la nube de Azure Government

Se creará lo siguiente según la opción de configuración que seleccione:

* Una aplicación de Azure AD que se exportará con la clave pública del certificado autofirmado o de empresa, se creará una cuenta de la entidad de servicio para esta aplicación en Azure AD y se le asignará el rol Colaborador (se puede cambiar a Propietario o a cualquier otro rol) para esta cuenta en su suscripción actual.  Para más información, consulte el artículo [Control de acceso basado en rol en Automatización de Azure](automation-role-based-access-control.md) .
* Un recurso de certificado de Automation en la cuenta de automatización especificada llamada **AzureRunAsCertificate**, que contiene la clave privada del certificado usada por la aplicación de Azure AD.
* Un recurso de conexión de Automatización en la cuenta de Automatización especificada denominado **AzureRunAsConnection**, que contiene los valores applicationId, tenantId, subscriptionId y la huella digital del certificado.    

Para cuentas de ejecución clásicas:

* Un recurso de certificado de Automation en la cuenta de automatización especificada denominada **AzureClassicRunAsCertificate**, que contiene la clave privada del certificado que se usa con el certificado de administración.  
* Un recurso de conexión de Automatización en la cuenta de Automatización especificada denominado **AzureClassicRunAsConnection**, que contiene el nombre de la suscripción, el valor subscriptionId y el nombre del recurso de certificado.

Si selecciona la opción para crear una cuenta de ejecución clásica después de la ejecución del script, deberá cargar el certificado público (formato .cer) en el almacén de administración para la suscripción en la que se creó la cuenta de Automation. Los siguientes pasos le guiarán a través del proceso de ejecución del script y de carga del certificado.    

1. Guarde el script siguiente en el equipo.  En este ejemplo, guárdelo con el nombre **New-RunAsAccount.ps1**.  
   
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
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use Key credentials and create AAD Application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id
   
        # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
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

        # Create Run As Account using Service Principal
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

        # Create Service Principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId} 

        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create Run As Account using Service Principal
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

        # Create the automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. En el equipo, inicie **Windows PowerShell** desde la pantalla **Inicio** con permisos de usuario elevados.
3. Desde el shell de línea de comandos de PowerShell con privilegios elevados, navegue hasta la carpeta que contiene el script que creó en el paso 1 y ejecute el script que establece los valores de parámetro necesarios según la configuración que necesite.  

    **Creación de cuenta de ejecución de Azure mediante un certificado autofirmado**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false` 

    **Creación de cuenta de ejecución y de cuenta de ejecución clásica de Azure mediante un certificado autofirmado**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Creación de cuenta de ejecución y de cuenta de ejecución clásica de Azure mediante un certificado de la empresa**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Creación de cuenta de ejecución y de cuenta de ejecución clásica de Azure mediante un certificado autofirmado en la nube de Azure Government**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`
 
    > [!NOTE]
    > Se le solicitará que se autentique en Azure después de ejecutar el script. Debe iniciar sesión con una cuenta que sea miembro del rol Administradores de suscripciones y coadministrador de la suscripción.
    > 
    > 

Después de que el script finaliza correctamente, si creó una cuenta de ejecución clásica, siga los pasos para [cargar el certificado de API de administración](../azure-api-management-certs.md) en el Portal de Azure clásico.  Si creó una cuenta de ejecución clásica con un certificado público autofirmado (formato .cer), puede encontrar una copia del certificado creado en la carpeta de archivos temporales del equipo bajo el perfil de usuario usado para ejecutar la sesión de PowerShell: *%USERPROFILE%\AppData\Local\Temp*.  Si, por el contrario, configuró la cuenta de ejecución clásica para usar un certificado generado por la entidad de certificación empresarial (formato .cer), deberá usar este certificado.  Una vez cargado el certificado, consulte el [código de ejemplo](#sample-code-to-authenticate-with-service-management-resources) para validar la configuración de credenciales con recursos de administración de Service Management.  

Si no creó una cuenta de ejecución clásica, consulte el [código de ejemplo](#sample-code-to-authenticate-with-resource-manager-resources) que se muestra a continuación para realizar la autenticación con recursos de Resource Manager y validar la configuración de credenciales.   

##  <a name="authentication-code-examples"></a>Ejemplos de código de autenticación

En los ejemplos siguientes se muestra cómo autenticar los runbooks contra los recursos de Resource Manager o del modelo clásico mediante una cuenta de ejecución.

### <a name="authenticate-with-resource-manager-resources"></a>Autenticación con recursos de Resource Manager
Puede usar el código de ejemplo actualizado siguiente, procedente del Runbook de ejemplo **AzureAutomationTutorialScript** , para autenticarse con la cuenta de ejecución y administrar los recursos de Resource Manager con sus Runbooks.   

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


El script incluye dos líneas adicionales de código para admitir la referencia a un contexto de la suscripción, con el fin de que pueda trabajar fácilmente entre varias suscripciones. Un recurso de variable denominado SubscriptionId contiene el identificador de la suscripción y, después de la instrucción del cmdlet Add-AzureRmAccount, el [cmdlet Set-AzureRmContext](https://msdn.microsoft.com/library/mt619263.aspx) se declara con el conjunto de parámetros *-SubscriptionId*. Si el nombre de variable es demasiado genérico, puede modificarlo para que incluya un prefijo u otra convención de nomenclatura que facilite la identificación. Como alternativa, puede usar el conjunto de parámetros -SubscriptionName en lugar de -SubscriptionId con un recurso de variable correspondiente.    

Observe que el cmdlet usado para autenticarse en el Runbook - **Add-AzureRmAccount**usa el conjunto de parámetros *ServicePrincipalCertificate* .  Se autentica mediante el certificado de la entidad de servicio, no las credenciales.  

### <a name="authenticate-with-service-management-resources"></a>Autenticación con recursos de Service Management
Puede usar el código de ejemplo actualizado siguiente, procedente del Runbook de ejemplo **AzureClassicAutomationTutorialScript** , para autenticarse con la cuenta de ejecución y administrar los recursos del modelo clásico con sus Runbooks.

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
* Para más información acerca de las entidades de servicio, consulte [Objetos Application y objetos ServicePrincipal](../active-directory/active-directory-application-objects.md).
* Para más información acerca del control de acceso basado en rol de Automatización de Azure, consulte [Control de acceso basado en rol en Automatización de Azure](automation-role-based-access-control.md).
* Para más información acerca de los certificados y de los servicios de Azure, consulte [Introducción a los certificados para los servicios en la nube de Azure](../cloud-services/cloud-services-certs-create.md)


