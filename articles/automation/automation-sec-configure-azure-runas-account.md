<properties
    pageTitle="Configuración de una cuenta de ejecución de Azure | Microsoft Azure"
    description="Tutorial que le guía a través de la creación, prueba y uso de ejemplo de la autenticación de la entidad de seguridad en Automatización de Azure."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/31/2016"
    ms.author="magoedte"/>

# Autenticación de Runbooks con una cuenta de ejecución de Azure
En este tema se mostrará cómo configurar una cuenta de Automatización desde el Portal de Azure mediante la nueva característica de cuenta de ejecución (también denominada entidad de servicio) para acceder a los recursos de Azure Resource Manager (ARM) de la suscripción con Runbooks de Automatización. Cuando se crea una nueva cuenta de Automatización en el Portal de Azure, esta crea automáticamente una nueva entidad de servicio, que se asigna al rol de control de acceso basado en roles (RBAC) del colaborador de la suscripción de manera predeterminada. Esto simplifica el proceso y ayuda a que empezar rápidamente a generar e implementar Runbooks que den soporte técnico a sus necesidades de automatización.

Con una entidad de servicio se puede:

* Proporcionar una forma normalizada de autenticarse en Azure al administrar los recursos ARM de Azure
* Automatizar el uso de Runbooks globales configurados en Azure Alerts


>[AZURE.NOTE] La [característica de integración de alertas](../azure-portal/insights-receive-alert-notifications.md) de Azure con los Runbooks globales de Automatización requiere una cuenta de Automatización que esté configurada con una entidad de servicio. Puede seleccionar una cuenta de Automatización que ya tenga un usuario de entidad de servicio definido, o bien crear una nueva.



Le mostraremos cómo crear la cuenta de Automatización desde el Portal de Azure, cómo actualizar una cuenta mediante una cuenta de ejecución a través de Azure PowerShell y cómo autenticarla en la entidad de servicio de sus Runbooks.

## Creación de una nueva cuenta de Automatización desde el Portal de Azure.
En esta sección, realizará los siguientes pasos para crear una cuenta de Automatización de Azure nueva y una entidad de servicio desde el Portal de Azure.

>[AZURE.NOTE] El usuario que realiza estos pasos *debe* ser miembro del rol Administradores de suscripciones.

1. Inicie sesión en el portal de Azure como administrador de servicios para la suscripción de Azure que desea administrar.
2. Seleccione **Cuentas de Automatización**.
3. En la hoja Cuentas de Automatización, haga clic en **Agregar**.<br>![Agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. En la hoja **Agregar cuenta de Automatización**, en el cuadro **Nombre**, escriba el nombre de la nueva cuenta de Automatización.
5. Si tiene más de una suscripción, especifique la de la nueva cuenta, así como un **grupo de recursos** nuevo o existente, y la **ubicación** de un centro de datos de Azure.
6. Compruebe que el valor **Sí** está seleccionado en la opción **Crear cuenta de ejecución de Azure** y haga clic en el botón **Crear**.  

    ![Advertencia para agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/add-account-decline-create-runas-msg.png)

    >[AZURE.NOTE] Si decide no crear la cuenta de ejecución seleccionando la opción **No**, aparecerá un mensaje de advertencia en la hoja **Agregar Cuenta de Automatización**. Aunque la cuenta se crea y se asigna al rol de **Colaborador** en la suscripción, ésta no tendrá la identidad de autenticación correspondiente dentro del servicio de directorio de suscripciones y, por lo tanto, no tendrá acceso a los recursos de su suscripción. Esto impedirá que los runbooks que hacen referencia a esta cuenta puedan autenticarse y realizar tareas en los recursos del ARM.

    ![Advertencia para agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

    >[AZURE.NOTE] Si recibe un mensaje de error de denegación de permiso tras hacer clic en el botón **Crear**, se debe a que la cuenta no es miembro del rol de administradores de la suscripción.

7. Mientras Azure crea la cuenta de Automatización, puede seguir el progreso en **Notificaciones** en el menú.

Cuando haya finalizado, se crea la cuenta de Automatización con un recurso de certificado denominado **AzureRunAsCertificate** que tiene una duración de un año y un recurso de conexión denominado **AzureRunAsConnection**.

## Actualización de una cuenta de Automatización mediante PowerShell
El procedimiento siguiente actualiza una cuenta de Automatización existente y crea la entidad de servicio mediante PowerShell. Este procedimiento es necesario si se ha creado una cuenta, pero se rechazado crear la cuenta de ejecución.

Antes de continuar, compruebe lo siguiente:

1. Ha descargado e instalado el [módulo de Azure Active Directory para Windows PowerShell (versión de 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297).
2. Ha creado una cuenta de Automatización. Se hará referencia a esta cuenta como valor para los parámetros-AutomationAccountName y - ApplicationDisplayName en el siguiente script.

El script de PowerShell configurará los siguientes elementos:

* Una aplicación de Azure AD que pueda autenticarse con el certificado autofirmado, una cuenta de la entidad principal para esta aplicación en Azure AD y la asignación del rol Colaborador (se puede cambiar a propietario o a cualquier otro rol) a esta cuenta de su suscripción actual. Para más información, consulte el artículo [Control de acceso basado en rol en Automatización de Azure](../automation/automation-role-based-access-control.md).  
* Un recurso de certificado de Automatización de la cuenta de Automatización especificada denominado **AzureRunAsCertificate**, que contiene el certificado utilizado en la entidad de servicio.
* Un recurso de conexión de Automatización de la cuenta de Automatización especificada denominado **AzureRunAsConnection**, que contiene los valores applicationId, tenantId, subscriptionId y la huella digital del certificado.  


### Ejecución del script de PowerShell

1. Guarde el script siguiente en el equipo. En este ejemplo, guárdelo con el nombre de archivo **New-AzureServicePrincipal.ps1**.  

    ```
    #Requires - RunAsAdministrator
    Param (
    [Parameter(Mandatory=$true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory=$true)]
    [String] $AutomationAccountName,
   
    [Parameter(Mandatory=$true)]
    [String] $ApplicationDisplayName,
   
    [Parameter(Mandatory=$true)]
    [String] $CertPlainPassword,
   
    [Parameter(Mandatory=$false)]
    [int] $NoOfMonthsUntilExpired = 12
    )
   
    Login-AzureRmAccount
    Import-Module AzureRM.Resources
		
    $CurrentDate = Get-Date
    $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
    $KeyId = (New-Guid).Guid
    $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")

    $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

    $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose

    $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
    $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

    $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
    $KeyCredential.StartDate = $CurrentDate
    $KeyCredential.EndDate= $EndDate
    $KeyCredential.KeyId = $KeyId
    $KeyCredential.Type = "AsymmetricX509Cert"
    $KeyCredential.Usage = "Verify"
    $KeyCredential.Value = $KeyValue

    # Use Key credentials
    $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ServicePrincipalDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

    New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
    Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

    $NewRole = $null
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose
      Sleep 5
      $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
      $Retries++;
    }

    # Get the tenant id for this subscription
    $SubscriptionInfo = Get-AzureRmSubscription
    $TenantID = $SubscriptionInfo | Select TenantId -First 1

    # Create the automation resources
    New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

    # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    $ConnectionAssetName = "AzureRunAsConnection"
    $SubscriptionId = $SubscriptionInfo | Select SubscriptionId -First 1
    Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId.SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
    ```
<br>
2. En el equipo, inicie **Windows PowerShell** desde la pantalla **Inicio** con derechos de usuario elevados.
3. Desde el shell de línea de comandos de PowerShell elevado, navegue hasta la carpeta que contiene el script que se creó en el paso 1 y ejecute el script, pero cambie los valores de los parámetros *– ResourceGroup*, *- AutomationAccountName*, *- ApplicationDisplayName* y *- CertPlainPassword*.<br> 

    ```
    .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> 
     -AutomationAccountName <NameofAutomationAccount> 
     -ApplicationDisplayName <DisplayNameofAutomationAccount> 
     -CertPlainPassword "<StrongPassword>"
    ```   
<br>

    >[AZURE.NOTE] Se le solicitará que se autentique en Azure después de ejecutar el script. *Debe* iniciar sesión con una cuenta de administrador de servicios en la suscripción. <br> 
4. Cuando el script se complete correctamente, pase a la sección siguiente para probar y comprobar la nueva configuración de credenciales.

### Comprobación de la autenticación 
A continuación, realizaremos una pequeña prueba para confirmar que puede autenticarse correctamente mediante la nueva entidad de servicio. Si no puede hacerlo, vuelva al paso 1 y vuelva a confirmar tofos los pasos anteriores.

1. En el Portal de Azure, abra la cuenta de Automatización que creó anteriormente.  
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Cree un Runbook nuevo, para lo que debe hacer clic en el botón **Agregar un Runbook** y luego, en la hoja **Agregar Runbook**, seleccione **Crear un nuevo Runbook**.
4. Asigne al Runbook el nombre *Test-SecPrin-Runbook* y seleccione PowerShell en **Tipo de Runbook**. Haga clic en **Crear** para crear el Runbook.
5. En la hoja **Editar Runbook de PowerShell**, pegue el siguiente código en el lienzo:<br>
  
    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID 
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```  
<br>
6. Guarde el Runbook, para lo que debe hacer clic en **Guardar**.
7. Haga clic en **Probar panel** para abrir la hoja **Probar**.
8. Haga clic en **Iniciar** para iniciar la prueba.
9. Se crea un [trabajo de runbook](automation-runbook-execution.md) y su estado se muestra en el panel.  
10. El estado del trabajo se iniciará como *En cola*, lo que indica que espera que haya algún trabajo de Runbook en la nube disponible. A continuación, pasará a *Iniciando* cuando un trabajador de runbook solicite el trabajo y, a continuación, a *En ejecución* cuando el runbook empiece a ejecutarse realmente.  
11. Cuando se complete el trabajo del runbook, se mostrará su resultado. En nuestro caso, deberíamos ver el estado **Completado**.<br> ![Prueba de Runbook de entidad de seguridad](media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br> 
12. Cierre la hoja **Probar** para volver al lienzo.
13. Cierre la hoja **Editar Runbook de PowerShell**.
14. Cierre la hoja **Test-SecPrin-Runbook**.

El código anterior, que se ha usado para comprobar si la nueva cuenta está configurada correctamente, es lo que usará en los Runbooks de PowerShell para autenticarse en Automatización de Azure para administrar los recursos de ARM. Por supuesto, aún puede autenticarse con la cuenta de Automatización que usa actualmente.

## Pasos siguientes
- Para más información acerca de las entidades de servicio, consulte [Objetos Application y objetos ServicePrincipal](../active-directory/active-directory-application-objects.md).
- Para más información acerca del control de acceso basado en rol en Automatización de Azure, consulte [Control de acceso basado en rol en Automatización de Azure](../automation/automation-role-based-access-control.md).

<!---HONumber=AcomDC_0406_2016-->