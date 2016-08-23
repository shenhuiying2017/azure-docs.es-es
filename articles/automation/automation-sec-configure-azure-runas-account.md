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
    ms.date="08/17/2016"
    ms.author="magoedte"/>

# Autenticación de Runbooks con una cuenta de ejecución de Azure

En este tema se mostrará cómo configurar una cuenta de Automatización desde el Portal de Azure mediante la característica de cuenta de ejecución para autenticar los runbooks que administran recursos en Azure Resource Manager o Azure Service Management.

Al crear una nueva cuenta de Automatización en el portal de Azure, se crea automáticamente:

- Una cuenta de ejecución que crea a una nueva entidad de servicio en Azure Active Directory y un certificado, y asigna el control de acceso basado en rol (RBAC) Colaborador, que se utilizará para administrar los recursos de Resource Manager mediante Runbooks.
- Una cuenta de ejecución de Azure clásico mediante la carga de un certificado de administración, que se utilizará para administrar Azure Service Management o recursos clásicos mediante Runbooks.

Esto simplifica el proceso y ayuda a que empezar rápidamente a generar e implementar Runbooks que den soporte técnico a sus necesidades de automatización.

Con una cuenta de ejecución y una cuenta de ejecución de Azure clásico puede:

- Proporcionar una forma normalizada de autenticarse en Azure al administrar los recursos de Azure Resource Manager o Azure Service Management desde Runbooks en el Portal de Azure.
- Automatizar el uso de runbooks globales configurados en Azure Alerts.


>[AZURE.NOTE] La [característica de integración de alertas](../azure-portal/insights-receive-alert-notifications.md) de Azure con los Runbooks globales de Automatización requiere una cuenta de Automatización configurada con una cuenta de ejecución y de ejecución de Azure clásico. Puede seleccionar una cuenta de Automatización que ya tenga una cuenta de ejecución y de ejecución de Azure clásico definida, o bien crear una nueva.

Le mostraremos cómo crear la cuenta de Automatización desde el Portal de Azure, cómo actualizarla mediante PowerShell y cómo autenticarla en sus Runbooks.

Antes de hacerlo, hay algunas cosas que debe conocer y considerar.

1. Esto no afecta a las cuentas de Automatización existentes creadas con el modelo de implementación clásico o con Resource Manager.
2. Esto solo funcionará para las cuentas de Automatización creadas a través del Portal de Azure. El intento de crear una cuenta desde el portal clásico no replicará la configuración de la cuenta de ejecución.
3. Si actualmente tiene Runbooks y recursos (es decir, programaciones, variables, etc.) que creó anteriormente para administrar recursos clásicos y desea que dichos Runbooks se autentique con la nueva cuenta de ejecución de Azure clásico, será preciso que los migre a la nueva cuenta de Automatización o que actualice su cuenta existente mediante el siguiente script de PowerShell.
4. Para realizar la autenticación con la nueva cuenta de Automatización de ejecución y de ejecución de Azure clásico, será preciso que modifique los Runbooks existentes con el código de ejemplo siguiente. **Tenga en cuenta** que la cuenta de ejecución es para realizar la autenticación con los recursos de Resource Manager con la entidad de servicio basada en certificados, mientras que la cuenta de ejecución de Azure clásico es para realizar la autenticación con los recursos de Service Management con el certificado de administración.


## Creación de una nueva cuenta de Automatización desde el Portal de Azure.

En esta sección, realizará los siguientes pasos para crear una cuenta de Automatización de Azure nueva desde el Portal de Azure. Así se crean tanto la cuenta de ejecución como la cuenta de ejecución de Azure clásico.

>[AZURE.NOTE] El usuario que realice estos pasos *debe* ser miembro del rol Administradores de suscripciones y coadministrador de la suscripción que concede el acceso a la suscripción al usuario. También se debe agregar como usuario a la instancia predeterminada de Azure Active Directory de dicha suscripción; no es preciso asignar un rol con privilegios a la cuenta.

1. Inicie sesión en el Portal de Azure con una cuenta que sea miembro del rol Administradores de suscripciones y coadministrador de la suscripción.
2. Seleccione **Cuentas de Automatización**.
3. En la hoja Cuentas de Automatización, haga clic en **Agregar**.<br>![Agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

    >[AZURE.NOTE] Si ve la siguiente advertencia en la hoja **Agregar cuenta de Automatización**, se debe a que la cuenta no es un miembro del rol Administradores de suscripciones y coadministrador de la suscripción.<br>![Advertencia para agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)

4. En la hoja **Agregar cuenta de Automatización**, en el cuadro **Nombre**, escriba el nombre de la nueva cuenta de Automatización.
5. Si tiene más de una suscripción, especifique una para la cuenta nueva, así como un **grupo de recursos** nuevo o existente, y la **ubicación** de un centro de datos de Azure.
6. Compruebe que el valor **Sí** está seleccionado en la opción **Crear cuenta de ejecución de Azure** y haga clic en el botón **Crear**.

    >[AZURE.NOTE] Si elige no crear la cuenta de ejecución seleccionando la opción **No**, aparecerá un mensaje de advertencia en la hoja **Agregar cuenta de Automatización**. Aunque la cuenta se crea en el Portal de Azure, no tendrá la identidad de autenticación correspondiente en el servicio de directorio de suscripciones clásico o de Resource Manager y, por lo tanto, no tendrá acceso a los recursos de su suscripción. Esto evitará que los Runbooks que hacen referencia a esta cuenta puedan autenticarse y realizar tareas con los recursos en dichos modelos de suscripción.
    
    >![Advertencia para agregar cuenta de Automatización](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br> Si no se crea la entidad de servicio, no se asignará el rol Colaborador.


7. Mientras Azure crea la cuenta de Automatización, se puede seguir el progreso de **Notificaciones** desde el menú.

### Recursos incluidos

Una vez que se crea la cuenta de Automatización, se también varios recursos automáticamente. La siguiente tabla resume los recursos de la cuenta de ejecución.<br>

Recurso|Descripción 
--------|-----------
Runbook AzureAutomationTutorial|Un Runbook de PowerShell de ejemplo que muestra cómo realizar la autenticación mediante la cuenta de ejecución y que obtiene todos los recursos de Resource Manager.
Runbook AzureAutomationTutorialScript|Un Runbook de PowerShell de ejemplo que muestra cómo realizar la autenticación mediante la cuenta de ejecución y que obtiene todos los recursos de Resource Manager. 
AzureRunAsCertificate|Recurso de certificado creado automáticamente durante la creación de la cuenta de Automatización o mediante el siguiente script de PowerShell de una cuenta existente. Permite realizar la autenticación con Azure, con el fin de poder administrar los recursos de Azure Resource Manager desde los Runbooks. Este certificado tiene una duración de un año. 
AzureRunAsConnection|Recurso de conexión creado automáticamente durante la creación de la cuenta de Automatización o mediante el siguiente script de PowerShell de una cuenta existente.

La siguiente tabla resume los recursos de la cuenta de ejecución de Azure clásico.<br>

Recurso|Descripción 
--------|-----------
Runbook AzureClassicAutomationTutorial|Un runbook de ejemplo que obtiene todas las máquinas virtuales con el modelo clásico de una suscripción mediante la cuenta de ejecución de Azure clásico (certificado) y, luego, envía el nombre y estado de la máquina virtual.
Runbook AzureClassicAutomationTutorial Script|Un runbook de ejemplo que obtiene todas las máquinas virtuales con el modelo clásico de una suscripción mediante la cuenta de ejecución de Azure clásico (certificado) y, luego, envía el nombre y estado de la máquina virtual.
AzureClassicRunAsCertificate|Recurso de certificado creado automáticamente que se utiliza para realizar la autenticación en Azure, de modo sea posible administrar los recursos del modelo clásico de Azure desde Runbooks. Este certificado tiene una duración de un año. 
AzureClassicRunAsConnection|Recurso de conexión creado automáticamente que se utiliza para realizar la autenticación en Azure, de modo sea posible administrar los recursos del modelo clásico de Azure desde Runbooks.  

## Comprobación de la autenticación de ejecución

A continuación, realizaremos una pequeña prueba para confirmar que puede autenticarse correctamente mediante la nueva cuenta de ejecución.

1. En el Portal de Azure, abra la cuenta de Automatización que creó anteriormente.
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Seleccione el Runbook **AzureAutomationTutorialScript** y haga clic en **Iniciar** para iniciarlo. Recibirá un aviso para comprobar que desea que se inicie el Runbook.
4. Se crea un [trabajo de Runbook](automation-runbook-execution.md), se muestra la hoja Trabajo y el estado del trabajo aparece en el icono **Resumen del trabajo**.
5. El estado del trabajo se iniciará como *En cola*, lo que indica que está esperando a que haya algún trabajo de Runbook disponible en la nube. A continuación, pasará a *Iniciando* cuando un trabajador de runbook solicite el trabajo y, a continuación, a *En ejecución* cuando el runbook empiece a ejecutarse realmente.
6. Cuando el trabajo de Runbook se completa, deberíamos ver el estado **Completado**.<br> ![Prueba de Runbook de entidad de seguridad](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. Para ver los resultados detallados del Runbook, haga clic en el icono **Salida**.
8. En la hoja **Salida**, debería ver que se ha autenticado correctamente y ha devuelto una lista de todos los recursos disponibles en el grupo de recursos.
9. Cierre la hoja **Salida** para volver a la hoja **Resumen del trabajo**.
13. Cierre la hoja **Resumen del trabajo** y la hoja del Runbook **AzureAutomationTutorialScript**.

## Comprobación de la autenticación de ejecución de Azure clásico

A continuación, realizaremos una pequeña prueba para confirmar que puede autenticarse correctamente mediante la nueva cuenta de ejecución de Azure clásico.

1. En el Portal de Azure, abra la cuenta de Automatización que creó anteriormente.
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Seleccione el Runbook **AzureClassicAutomationTutorialScript** y haga clic en **Iniciar** para iniciarlo. Recibirá un aviso para comprobar que desea que se inicie el Runbook.
4. Se crea un [trabajo de Runbook](automation-runbook-execution.md), se muestra la hoja Trabajo y el estado del trabajo aparece en el icono **Resumen del trabajo**.
5. El estado del trabajo se iniciará como *En cola*, lo que indica que está esperando a que haya algún trabajo de Runbook disponible en la nube. A continuación, pasará a *Iniciando* cuando un trabajador de runbook solicite el trabajo y, a continuación, a *En ejecución* cuando el runbook empiece a ejecutarse realmente.
6. Cuando el trabajo de Runbook se completa, deberíamos ver el estado **Completado**.<br> ![Prueba de Runbook de entidad de seguridad](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. Para ver los resultados detallados del Runbook, haga clic en el icono **Salida**.
8. En la hoja **Salida**, debería ver que se ha autenticado correctamente y ha devuelto una lista de todas las máquinas virtuales con el modelo clásico que hay en la suscripción.
9. Cierre la hoja **Salida** para volver a la hoja **Resumen del trabajo**.
13. Cierre la hoja **Resumen del trabajo** y la hoja del Runbook **AzureClassicAutomationTutorialScript**.

## Actualización de una cuenta de Automatización mediante PowerShell

Aquí se brinda la opción de usar PowerShell para actualizar una cuenta de Automatización existente si:

1. Se creó una cuenta de Automatización, pero se rechazó crear la cuenta de ejecución
2. Ya se dispone de una cuenta de Automatización que administre los recursos de Resource Manager y se desea actualizar para incluir la cuenta de ejecución para la autenticación de Runbooks
2. Ya se dispone de una cuenta de Automatización que administre los recursos del modelo clásico y se desea actualizar para que use la ejecución de Azure clásico, en lugar de crear una nueva cuenta y migrar los Runbooks y recursos a ella

Antes de continuar, compruebe lo siguiente:

1. Si utiliza Windows 7, asegúrese de que ha descargado e instalado [Windows Management Framework (WMF) 4.0](https://www.microsoft.com/download/details.aspx?id=40855). Si utiliza Windows Server 2012 R2, Windows Server 2012, Windows 2008 R2, Windows 8.1 o Windows 7 SP1, asegúrese de que [Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395) está disponible para instalarse.
2. Azure PowerShell 1.0. Para obtener información acerca de esta versión y cómo instalarla, consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).
3. Ha creado una cuenta de Automatización. Se hará referencia a esta cuenta como valor para los parámetros-AutomationAccountName y - ApplicationDisplayName en los dos scripts siguientes.

Para obtener los valores de *SubscriptionID*, *ResourceGroup*, y *AutomationAccountName*, que son los parámetros necesarios para los scripts, en el Portal de Azure, seleccione su cuenta de Automatización en la hoja **cuenta de Automatización** y seleccione **Todas las opciones**. En la hoja **Todas las opciones**, en **Configuración de la cuenta**, seleccione **Propiedades**. En la hoja **Propiedades**, puede anotar estos valores.<br> ![Propiedades de una cuenta de Automatización](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)

### Creación de un script de PowerShell de una cuenta de ejecución

El siguiente script de PowerShell configurará los siguientes elementos:

- Una aplicación de Azure AD que se autenticará con el certificado autofirmado, creará una cuenta de la entidad de servicio para esta aplicación en Azure AD y a la que se asignará el rol Colaborador (se puede cambiar a Propietario o a cualquier otro rol) para esta cuenta en su suscripción actual. Para más información, consulte el artículo [Control de acceso basado en rol en Automatización de Azure](../automation/automation-role-based-access-control.md).
- Un recurso de certificado de Automatización en la cuenta de Automatización especificada denominado **AzureRunAsCertificate**, que contiene el certificado que usa la entidad de servicio.
- Un recurso de conexión de Automatización en la cuenta de Automatización especificada denominado **AzureRunAsConnection**, que contiene los valores applicationId, tenantId, subscriptionId y la huella digital del certificado.

Los siguientes pasos le guiarán a través del proceso de ejecución del script.

1. Guarde el script siguiente en el equipo. En este ejemplo, guárdelo con el nombre **New-AzureServicePrincipal.ps1**.

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
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
           Sleep 5
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           Sleep 10
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

2. En el equipo, inicie **Windows PowerShell** desde la pantalla **Inicio** con permisos de usuario elevados.
3. En el shell de línea de comandos de PowerShell elevado, navegue a la carpeta que contiene el script que se creó en el paso 1 y ejecútelo, después de cambiar los valores de los parámetros *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* y *-CertPlainPassword*.<br>

    >[AZURE.NOTE] Se le solicitará que se autentique en Azure después de ejecutar el script. Debe iniciar sesión con una cuenta que sea miembro del rol Administradores de suscripciones y coadministrador de la suscripción.
    
        .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> 
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"  
<br>

Una vez que el script se complete correctamente, consulte el [código de ejemplo](#sample-code-to-authenticate-with-resource-manager-resources) siguiente para autenticarse con los recursos de Resource Manager y validar la configuración de credenciales.

### Creación de un script de PowerShell de una cuenta de ejecución de Azure clásico

El siguiente script de PowerShell configurará los siguientes elementos:

- Un recurso de certificado de Automatización en la cuenta de Automatización especificada denominado **AzureClassicRunAsCertificate**, que contiene el certificado que se usa para autenticar los Runbooks.
- Un recurso de conexión de Automatización en la cuenta de Automatización especificada denominado **AzureClassicRunAsConnection**, que contiene el nombre de la suscripción, el valor subscriptionId y el nombre del recurso de certificado.

El script creará un certificado de administración autofirmado y lo guardará en la carpeta de archivos temporales del equipo, en el perfil de usuario que se usa para ejecutar la sesión de PowerShell - *%USERPROFILE%\\AppData\\Local\\Temp*. Después de la ejecución del script, será preciso que cargue el certificado de administración de Azure en el almacén de administración de la suscripción en que se creó la cuenta de Automatización. Los siguientes pasos le guiarán a través del proceso de ejecución del script y de carga del certificado.

1. Guarde el script siguiente en el equipo. En este ejemplo, guárdelo con el nombre **New-AzureServicePrincipal.ps1**.

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
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        $SubscriptionName = $subscription.Subscription.SubscriptionName

        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
        $CertPathCer = Join-Path $env:TEMP ($ApplicationDisplayName + ".cer")

        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPathCer -Type CERT | Write-Verbose

        # Create the automation resources
        $ClassicCertificateAssetName = "AzureClassicRunAsCertificate"
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name $ClassicCertificateAssetName  -Password $CertPassword -Exportable | write-verbose

        # Create a Automation connection asset named AzureClassicRunAsConnection in the Automation account. This connection uses the ClassicCertificateAssetName.
        $ConnectionAssetName = "AzureClassicRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicCertificateAssetName}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureClassicCertificate -ConnectionFieldValues $ConnectionFieldValues 

        Write-Host -ForegroundColor red "Please upload the cert $CertPathCer to the Management store by following the steps below."
        Write-Host -ForegroundColor red "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates."
        Write-Host -ForegroundColor red "Then click Upload and upload the certificate $CertPathCer"

2. En el equipo, inicie **Windows PowerShell** desde la pantalla **Inicio** con permisos de usuario elevados.
3. En el shell de línea de comandos de PowerShell elevado, navegue a la carpeta que contiene el script que se creó en el paso 1 y ejecútelo, después de cambiar los valores de los parámetros *–ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* y *-CertPlainPassword*.<br>

    >[AZURE.NOTE] Se le solicitará que se autentique en Azure después de ejecutar el script. Debe iniciar sesión con una cuenta que sea miembro del rol Administradores de suscripciones y coadministrador de la suscripción.
   
        .\New-AzureClassicRunAsAccount.ps1 -ResourceGroup <ResourceGroupName> 
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>" 

Una vez que el script se complete correctamente, será preciso copiar el certificado creado en la carpeta **Temp** del perfil de usuario. Siga los pasos para [cargar un certificado de la API de administración](../azure-api-management-certs.md) en el Portal de Azure clásico y, luego, haga referencia al [código de ejemplo](#sample-code-to-authenticate-with-service-management-resources) para validar la configuración de credenciales con recursos de Service Management.

## Código de ejemplo para autenticarse con recursos de Resource Manager

Puede usar el código de ejemplo actualizado siguiente, procedente del Runbook de ejemplo **AzureAutomationTutorialScript**, para autenticarse con la cuenta de ejecución y administrar los recursos de Resource Manager con sus Runbooks.

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
   

El script incluye dos líneas adicionales de código para admitir la referencia a un contexto de la suscripción, con el fin de que pueda trabajar fácilmente entre varias suscripciones. Un recurso de variable denominado SubscriptionId contiene el identificador de la suscripción y, después de la instrucción del cmdlet Add-AzureRmAccount, el [cmdlet Set-AzureRmContext](https://msdn.microsoft.com/library/mt619263.aspx) se declara con el conjunto de parámetros *-SubscriptionId*. Si el nombre de variable es demasiado genérico, puede modificarlo para que incluya un prefijo u otra convención de nomenclatura que facilite la identificación. Como alternativa, puede usar el conjunto de parámetros -SubscriptionName en lugar de -SubscriptionId con un recurso de variable correspondiente.

Observe que el cmdlet usado para autenticarse en el Runbook - **Add-AzureRmAccount** usa el conjunto de parámetros *ServicePrincipalCertificate*. Se autentica mediante el certificado de la entidad de servicio, no las credenciales.

## Código de ejemplo para autenticarse con los recursos de Service Management

Puede usar el código de ejemplo actualizado siguiente, procedente del Runbook de ejemplo **AzureClassicAutomationTutorialScript**, para autenticarse con la cuenta de ejecución y administrar los recursos del modelo clásico con sus Runbooks.
    
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


## Pasos siguientes

- Para más información acerca de las entidades de servicio, consulte [Objetos Application y objetos ServicePrincipal](../active-directory/active-directory-application-objects.md).
- Para más información acerca del control de acceso basado en rol de Automatización de Azure, consulte [Control de acceso basado en rol en Automatización de Azure](../automation/automation-role-based-access-control.md).
- Para más información acerca de los certificados y de los servicios de Azure, consulte [Introducción a los certificados para los servicios en la nube de Azure](../cloud-services/cloud-services-certs-create.md)

<!---HONumber=AcomDC_0817_2016-->