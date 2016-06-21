<properties
    pageTitle="Configuración de una cuenta de ejecución de Azure | Microsoft Azure"
    description="Tutorial que le guía a través de la creación, prueba y uso de ejemplo de la autenticación de la entidad de seguridad en Automatización de Azure."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
	keywords="nombre de la entidad de servicio, setspn, autenticación de azure"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/09/2016"
    ms.author="magoedte"/>

# Autenticación de Runbooks con una cuenta de ejecución de Azure
En este tema se mostrará cómo configurar una cuenta de Automatización desde el Portal de Azure mediante la nueva característica de cuenta de ejecución (también denominada entidad de servicio) para acceder a los recursos de Azure Resource Manager de la suscripción con Runbooks de Automatización. Cuando se crea una nueva cuenta de Automatización en el Portal de Azure, esta crea automáticamente una nueva entidad de servicio, que se asigna al rol de control de acceso basado en roles (RBAC) del colaborador de la suscripción de manera predeterminada. Esto simplifica el proceso y ayuda a que empezar rápidamente a generar e implementar Runbooks que den soporte técnico a sus necesidades de automatización.

Con una entidad de servicio se puede:

* Proporcionar una forma normalizada de autenticarse en Azure al administrar los recursos de Azure Resource Manager con Runbooks
* Automatizar el uso de Runbooks globales configurados en Azure Alerts


>[AZURE.NOTE] La [característica de integración de alertas](../azure-portal/insights-receive-alert-notifications.md) de Azure con los runbooks globales de Automatización requiere una cuenta de Automatización configurada con una entidad de servicio. Puede seleccionar una cuenta de Automatización que ya tenga un usuario de entidad de servicio definido, o bien crear una nueva.



Le mostraremos cómo crear la cuenta de Automatización desde el Portal de Azure, cómo actualizar una cuenta mediante una cuenta de ejecución a través de Azure PowerShell y cómo autenticarla en la entidad de servicio de sus Runbooks.

## Creación de una nueva cuenta de Automatización desde el Portal de Azure.
En esta sección, realizará los siguientes pasos para crear una cuenta de Automatización de Azure nueva y una entidad de servicio desde el Portal de Azure.

>[AZURE.NOTE] El usuario que realiza estos pasos *debe* ser miembro del rol Administradores de suscripciones.

1. Inicie sesión en el portal de Azure como administrador de servicios para la suscripción de Azure que desea administrar.
2. Seleccione **Cuentas de Automatización**.
3. En la hoja Cuentas de Automatización, haga clic en **Agregar**.<br>![Agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. En la hoja **Agregar cuenta de Automatización**, en el cuadro **Nombre**, escriba el nombre de la nueva cuenta de Automatización.
5. Si tiene más de una suscripción, especifique la de la cuenta nueva, así como un **grupo de recursos** nuevo o existente, y la **ubicación** de un centro de datos de Azure.
6. Compruebe que el valor **Sí** está seleccionado en la opción **Crear cuenta de ejecución de Azure** y haga clic en el botón **Crear**.  

    ![Advertencia para agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/add-account-decline-create-runas-msg.png)

    >[AZURE.NOTE] Si decide no crear la cuenta de ejecución seleccionando la opción **No**, aparecerá un mensaje de advertencia en la hoja **Agregar cuenta de Automatización**. Aunque la cuenta se crea y se asigna al rol de **Colaborador** en la suscripción, esta no tendrá la identidad de autenticación correspondiente dentro del servicio de directorio de suscripciones y, por lo tanto, no tendrá acceso a los recursos de su suscripción. Esto impedirá que los Runbooks que hacen referencia a esta cuenta puedan autenticarse y realizar tareas en los recursos de Azure Resource Manager.

    ![Advertencia para agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

    >[AZURE.NOTE] Si recibe un mensaje de error de denegación de permiso tras hacer clic en el botón **Crear**, se debe a que la cuenta no es miembro del rol Administradores de suscripciones.

7. Mientras Azure crea la cuenta de Automatización, puede seguir el progreso en **Notificaciones** en el menú.

### Recursos incluidos
Una vez completada la creación de la cuenta de Automatización, se crean varios recursos automáticamente. Se resumen en la tabla siguiente.

Recurso|Descripción 
----|----
Runbook AzureAutomationTutorial|Un runbook de ejemplo que demuestra cómo autenticar mediante la cuenta de ejecución y mostrar las diez primeras máquinas virtuales de Azure de su suscripción.
AzureRunAsCertificate|Recurso de certificado creado si seleccionó que se creara la cuenta de ejecución durante la creación de la cuenta de Automatización o mediante el siguiente script de PowerShell para una cuenta existente. Este certificado tiene una duración de un año. 
AzureRunAsConnection|Recurso de conexión creado si seleccionó que se creara la cuenta de ejecución durante la creación de la cuenta de Automatización o mediante el siguiente script de PowerShell para una cuenta existente.
Módulos|15 módulos con cmdlets de Azure, PowerShell y Automatización que puede comenzar a usar inmediatamente en sus runbooks.  

## Actualización de una cuenta de Automatización mediante PowerShell
El procedimiento siguiente actualiza una cuenta de Automatización existente y crea la entidad de servicio mediante PowerShell. Este procedimiento es necesario si se ha creado una cuenta, pero se rechazado crear la cuenta de ejecución.

Antes de continuar, compruebe lo siguiente:

1. Si ejecuta Windows 7, asegúrese de que ha descargado e instalado [Windows Management Framework (WMF) 4.0](https://www.microsoft.com/download/details.aspx?id=40855). Si ejecuta Windows Server 2012 R2, Windows Server 2012, Windows 2008 R2, Windows 8.1 o Windows 7 SP1, asegúrese de que [Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395) está disponible para instalarse.
2. Azure PowerShell 1.0. Para más información acerca de esta versión y cómo instalarla, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md). 
3. Ha creado una cuenta de Automatización. Se hará referencia a esta cuenta como valor para los parámetros-AutomationAccountName y - ApplicationDisplayName en el siguiente script.


El script de PowerShell configurará los siguientes elementos:

* Una aplicación de Azure AD que podrá autenticarse con el certificado autofirmado, crear una cuenta de la entidad principal para esta aplicación en Azure AD y la asignación del rol Colaborador (se puede cambiar a propietario o a cualquier otro rol) a esta cuenta de su suscripción actual. Para más información, consulte el artículo [Control de acceso basado en rol en Automatización de Azure](../automation/automation-role-based-access-control.md).  
* Un recurso de certificado de Automatización en la cuenta de Automatización especificada denominado **AzureRunAsCertificate**, que contiene el certificado que se usa en la entidad de servicio.
* Un recurso de conexión de Automatización en la cuenta de Automatización especificada denominado **AzureRunAsConnection**, que contiene los valores applicationId, tenantId, subscriptionId y la huella digital del certificado.  


### Ejecución del script de PowerShell

1. Guarde el script siguiente en el equipo. En este ejemplo, guárdelo con el nombre de archivo **New-AzureServicePrincipal.ps1**.  

    ```
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
    [String] $CertPlainPassword,

    [Parameter(Mandatory=$false)]
    [int] $NoOfMonthsUntilExpired = 12
    )

    Login-AzureRmAccount
    Import-Module AzureRM.Resources
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId

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
    $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

    New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
    Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

    $NewRole = $null
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
      Sleep 5
      $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
      $Retries++;
    }

    # Get the tenant id for this subscription
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1

    # Create the automation resources
    New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

    # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    $ConnectionAssetName = "AzureRunAsConnection"
    Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
    ```
<br>
2. En el equipo, inicie **Windows PowerShell** desde la pantalla **Inicio** con permisos de usuario elevados.
3. En el shell de línea de comandos de PowerShell elevado, vaya a la carpeta que contiene el script que se creó en el paso 1 y ejecútelo después de cambiar los valores de los parámetros *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* y *-CertPlainPassword*.<br>

    ```
    .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> 
     -AutomationAccountName <NameofAutomationAccount> `
     -ApplicationDisplayName <DisplayNameofAutomationAccount> `
     -SubscriptionId <SubscriptionId> `
     -CertPlainPassword "<StrongPassword>"
    ```   
<br>

    >[AZURE.NOTE] Se le solicitará que se autentique en Azure después de ejecutar el script. *Debe* iniciar sesión con una cuenta de la suscripción en la que sea Administrador de servicios. <br>
4. Cuando el script se complete correctamente, pase a la sección siguiente para probar y comprobar la nueva configuración de credenciales.

### Comprobación de la autenticación
A continuación, realizaremos una pequeña prueba para confirmar que puede autenticarse correctamente mediante la nueva entidad de servicio. Si no puede hacerlo, vuelva al paso 1 y vuelva a confirmar tofos los pasos anteriores.

1. En el Portal de Azure, abra la cuenta de Automatización que creó anteriormente.  
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Cree un nuevo Runbook. Para ello, haga clic en el botón **Agregar un Runbook** y, en la hoja **Agregar Runbook**, seleccione **Crear un nuevo Runbook**.
4. Asigne al Runbook el nombre *Test-SecPrin-Runbook* y seleccione PowerShell en **Tipo de Runbook**. Haga clic en **Crear** para crear el Runbook.
5. En la hoja **Editar Runbook de PowerShell**, pegue el siguiente código en el lienzo:<br>

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    ```  
<br>
6. Haga clic en **Guardar** para guardar el Runbook.
7. Haga clic en **Panel de prueba** para abrir la hoja **Prueba**.
8. Haga clic en **Iniciar** para iniciar la prueba.
9. Se crea un [trabajo de runbook](automation-runbook-execution.md) y su estado se muestra en el panel.  
10. El estado del trabajo se iniciará como *En cola*, lo que indica que está esperando a que haya algún trabajo de Runbook disponible en la nube. A continuación, pasará a *Iniciando* cuando un trabajador de runbook solicite el trabajo y, a continuación, a *En ejecución* cuando el runbook empiece a ejecutarse realmente.  
11. Cuando se complete el trabajo del runbook, se mostrará su resultado. En nuestro caso, debería aparecer el estado **Completado**.<br> ![Prueba de Runbook de entidad de seguridad](media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br>
12. Cierre la hoja **Prueba** para volver al lienzo.
13. Cierre la hoja **Editar Runbook de PowerShell**.
14. Cierre la hoja **Test-SecPrin-Runbook**.

## Código de ejemplo para autenticarse con recursos de Resource Manager

Puede usar el código de ejemplo actualizado siguiente, procedente del Runbook de ejemplo AzureAutomationTutorial, para autenticarse con la cuenta de ejecución y administrar recursos de Resource Manager con sus Runbooks.

   ```
   $connectionName = "AzureRunAsConnection"
   $SubId = Get-AutomationVariable -Name 'SubscriptionId'
   try
   {
      # Get the connection "AzureRunAsConnection "
      $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

      "Logging in to Azure..."
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
   ```

El script incluye dos líneas adicionales de código para admitir la referencia a un contexto de la suscripción, de forma que pueda trabajar fácilmente entre varias suscripciones. Un recurso de variable denominado SubscriptionId contiene el identificador de la suscripción y, después de la instrucción del cmdlet Add-AzureRmAccount, el [cmdlet Set-AzureRmContext](https://msdn.microsoft.com/library/mt619263.aspx) se declara con el conjunto de parámetros *-SubscriptionId*. Si el nombre de variable es demasiado genérico, puede modificarlo para que incluya un prefijo u otra convención de nomenclatura que facilite la identificación. Como alternativa, puede usar el conjunto de parámetros -SubscriptionName en lugar de -SubscriptionId con un recurso de variable correspondiente.

## Pasos siguientes
- Para más información acerca de las entidades de servicio, consulte [Objetos Application y objetos ServicePrincipal](../active-directory/active-directory-application-objects.md).
- Para más información acerca del control de acceso basado en roles de Automatización de Azure, consulte [Control de acceso basado en rol en Automatización de Azure](../automation/automation-role-based-access-control.md).

<!---HONumber=AcomDC_0615_2016-->