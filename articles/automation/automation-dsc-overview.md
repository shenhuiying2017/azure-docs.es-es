<properties 
   pageTitle="Información general de DSC de Automatización de Azure"
	description="Una información general de Configuración de estado deseado (DSC) de Automatización de Azure, sus condiciones y problemas conocidos"
	services="automation"
	documentationCenter="dev-center-name"
	authors="coreyp-at-msft"
	manager="stevenka"
	editor="tysonn"/>

<tags
   ms.service="automation"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="TBD"
	ms.date="08/18/2015"
	ms.author="coreyp"/>

# Información general de DSC de Automatización de Azure #

>[AZURE.IMPORTANT]**DSC de Automatización de Azure actualmente está en vista previa limitada** y no es compatible para las cargas de trabajo de producción. Por ahora se basa principalmente en cmdlets y tiene una interfaz gráfica de usuario mínima. Si se registra para obtener la vista previa de DSC de Automatización de Azure, acepta que esta característica se encuentra en vista previa y que está sujeta a condiciones de servicio reducidas o menores, tal como se estipula en el [acuerdo de servicio](https://go.microsoft.com/fwLink/p/?LinkID=389530&clcid=0x409) y que acepta las [condiciones complementarias de vista previa](https://go.microsoft.com/fwLink/p/?LinkID=247638&clcid=0x409). A pesar de que el uso del servicio actualmente es gratuito, a futuro de introducirán precios.

## ¿Qué es DSC de PowerShell? ##
Configuración de estado deseado (DSC) es una plataforma de administración nueva en Windows PowerShell que permite administrar la configuración de hosts físicos y máquinas virtuales mediante el uso de una sintaxis declarativa de PowerShell.

DSC proporciona un conjunto de extensiones de lenguaje de Windows PowerShell, nuevos cmdlets de Windows PowerShell y recursos que puede usar para especificar de manera declarativa cómo desea configurar su entorno de software. También proporciona un medio para mantener y administrar las configuraciones existentes.

### Aplicaciones prácticas ###
A continuación aparecen algunos escenarios de ejemplo en los que puede usar recursos de DSC integrados para configurar y administrar un conjunto de equipos (también conocidos como nodos de destino) de manera automatizada:

- Habilitación o deshabilitación de características y roles de servidor
- Administración de la configuración del registro
- Administración de archivos y directorios
- Inicio, detención y administración de procesos y servicios
- Administración de grupos y cuentas de usuario
- Implementación de software nuevo
- Administración de variables de entorno
- Ejecución de scripts de Windows PowerShell
- Corrección de una configuración que se ha apartado del estado deseado
- Detección del estado de configuración real en un nodo determinado

Además, puede crear recursos personalizados para configurar el estado de cualquier ajuste de aplicación o sistema.


Para obtener más detalles acerca de DSC de PowerShell: [Configuration in a DevOps world - Windows PowerShell Desired State Configuration](http://blogs.msdn.com/b/powershell/archive/2013/11/01/configuration-in-a-devops-world-windows-powershell-desired-state-configuration.aspx) (Configuración en un mundo de operaciones de desarrollo: Configuración de estado deseado de Windows PowerShell)

##¿Qué es DSC de Automatización de Azure?##
DSC de Automatización de Azure se basa en los elementos fundamentales presentados en DSC de PowerShell para proporcionar una experiencia de administración de configuración incluso más simple. DSC de Automatización de Azure trae a [Configuración de estado deseado de PowerShell](https://technet.microsoft.com/library/dn249912.aspx) <link> el mismo nivel de administración que Automatización de Azure ofrece actualmente a los scripts de PowerShell.

DSC de Automatización de Azure le permite [crear y administrar Configuraciones de estado deseado de PowerShell](https://technet.microsoft.com/library/dn249918.aspx), importar [Recursos de DSC](https://technet.microsoft.com/library/dn282125.aspx) y generar Configuraciones de nodo de DSC (documentos MOF), todo en la nube. Estos elementos de DSC se colocarán en el [servidor de extracción de DSC](https://technet.microsoft.com/library/dn249913.aspx) de Automatización de Azure, para que los nodos de destino (como las máquinas físicas y virtuales) en la nube o locales puedan tomarlos, cumplir automáticamente con el estado deseado especificado e informar según su cumplimiento con el estado deseado a Automatización de Azure.

> [AZURE.VIDEO microsoft-ignite-2015-heterogeneous-configuration-management-using-microsoft-azure-automation]

## Condiciones de DSC de Automatización de Azure ##
### Configuración ###
DSC de PowerShell introdujo un concepto nuevo llamado configuraciones. Las configuraciones le permiten definir, a través de la sintaxis de PowerShell, el estado deseado de su entorno. Para usar DSC para configurar su entorno, primero defina un bloque de scripts de Windows PowerShell usando la palabra clave Configuration, seguida de un identificador y llaves ({}) para delimitar el bloque.

![texto alternativo](./media/automation-dsc-overview/AADSC_1.png)

Dentro del bloque de configuración puede definir bloques de configuración de nodo que especifican la configuración deseada de un conjunto de nodos (equipos) en su entorno que se deben configurar exactamente de la misma manera. De esta manera, una configuración de nodo representa un "rol" que uno o más nodos deben asumir. Un bloque de configuración de nodo comienza con la palabra clave Node. Esta palabra clave va seguida del nombre del rol, que puede ser una variable o una expresión. Después del nombre del rol, use llaves {} para delimitar el bloque de configuración del nodo.

![texto alternativo](./media/automation-dsc-overview/AADSC_2.png)
 
Dentro del bloque de configuración del nodo, puede definir bloques de recursos para configurar recursos de DSC específicos. Un bloque de recursos comienza con el nombre del recurso, seguido del identificador que desea especificar para ese bloque y, a continuación, llaves {} para delimitar el bloque.

![texto alternativo](./media/automation-dsc-overview/AADSC_3.png)

Para obtener información más detallada acerca de la palabra clave Configuration, consulte: [Understanding Configuration Keyword in Desired State Configuration](http://blogs.msdn.com/b/powershell/archive/2013/11/05/understanding-configuration-keyword-in-desired-state-configuration.aspx "Descripción de la palabra clave Configuration en Configuración de estado deseado") (Descripción de la palabra clave Configuration en Configuración de estado deseado)

Ejecutar (compilar) una configuración de DSC generará una o más configuraciones de nodo de DSC (documentos MOF), que son aplicados por los nodos de DSC para cumplir con el estado deseado.

DSC de Automatización de Azure le permite importar, crear y compilar configuraciones de DSC en Automatización de Azure, de manera similar a cómo se pueden importar, crear e iniciar runbooks en Automatización de Azure.

Actualmente, DSC de Automatización de Azure proporciona los siguientes cmdlets en el [módulo PowerShell de Administrador de recursos de Azure](https://msdn.microsoft.com/library/mt244122.aspx) para la administración de configuraciones de DSC:

- `Get-AzureAutomationDscConfiguration`
- `Import-AzureAutomationDscConfiguration`

>[AZURE.IMPORTANT]Una configuración solo debe contener un bloque de configuración, con el mismo nombre de la configuración, en DSC de Automatización de Azure.

###Configuración de nodo###

Cuando se compila una configuración de DSC, se generan una o más configuraciones de nodo, dependiendo de los bloques de nodo en la configuración. Una configuración de nodo es lo mismo que un "MOF" o un "documento de configuración" (si está familiarizado con esos términos de DSC de PS) y representa un "rol", como servidor web o de trabajo, cuyo estado deseado deben asumir uno o más nodos.

Los nodos de DSC de PS descubren las configuraciones de nodo que deben aplicar a través de métodos de inserción o extracción de DSC. DSC de Automatización de Azure se basa en el método de extracción de DSC, donde los nodos solicitan configuraciones de nodo que deben aplicar desde el servidor de extracción de DSC de Automatización de Azure. Debido a que los nodos realizan la solicitud a DSC de Automatización de Azure, pueden encontrarse detrás de firewalls, tener cerrados todos los puertos de entrada, etc. Lo único que necesitan es acceso de salida a Internet.

DSC de Automatización de Azure proporciona actualmente los siguientes cmdlets en el [módulo PowerShell de Administrador de recursos de Azure](https://msdn.microsoft.com/library/mt244122.aspx) para la administración de configuraciones de nodos de DSC: `Get-AzureAutomationDscNodeConfiguration`


###Nodo###

Un nodo de DSC es cualquier equipo que tenga la configuración administrada por DSC. Puede tratarse de una máquina virtual de Azure o de un host físico/máquina virtual local. Los nodos aplican las configuraciones de nodo para cumplir con el estado deseado que definen y también pueden informar a un servidor de informes sobre el estado de su configuración y su cumplimiento.

DSC de Automatización de Azure facilita la incorporación de nodos para su administración por parte de DSC de Automatización de Azure y permite cambiar la configuración de nodos asignada al lado servidor de cada nodo, para que la próxima vez que un nodo revise un servidor para obtener instrucciones, asumirá un rol distinto y cambiará cómo se configura para coincidir. Los nodos también informan el cumplimiento de estado y configuración a DSC de Automatización de Azure.

DSC de Automatización de Azure actualmente proporciona los siguientes cmdlets en el [módulo PowerShell de Administrador de recursos de Azure](https://msdn.microsoft.com/library/mt244122.aspx) para la administración de nodos de DSC:

-	`Get-AzureAutomationDscNode`  
-	`Register-AzureAutomationDscNode` (se usa para incorporar máquinas virtuales de Azure v2 como nodos)
-	`Get-AzureAutomationDscOnboardingMetaconfig` (se usa para incorporar nodos)
-	`Set-AzureAutomationDscNode` (se usa para establecer/actualizar asignaciones de configuración nodo a nodo)
-	`Unregister-AzureAutomationDscNode`
-	`Get-AzureAutomationDscNodeReport`
-	`Export-AzureAutomationDscNodeReportContent`

El cmdlet `Get-AzureAutomationRegistrationInfo` se puede usar para obtener la dirección URL de registro y la clave que se necesitan para incorporar máquinas virtuales clásicas de Azure a una cuenta de Automatización de Azure, ya sea a través de la extensión de máquina virtual de DSC de Automatización de Azure en el Portal de Azure o a través de PowerShell.


Extensión de máquina virtual de DSC de Automatización de Azure:

![texto alternativo](./media/automation-dsc-overview/AADSC_4.png)


PowerShell:

    # fill in correct values for your VM / Automation Account here
    $VMName = ""
    $ServiceName = ""
    $AutomationAccountName = ""
    $AutomationAccountResourceGroup = ""


    # get Azure Automation DSC registration info
    Switch-AzureMode AzureResourceManager

    $Account = Get-AzureAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName

    $RegistrationInfo = $Account | Get-AzureAutomationRegistrationInfo


    # use the DSC extension to onboard the VM for management with Azure Automation DSC
    Switch-AzureMode AzureServiceManagement

    $VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

    $PublicConfiguration = ConvertTo-Json -Depth 8 @{
        SasToken = ""
        ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfig.zip"
        ConfigurationFunction = "RegistrationMetaConfig.ps1\RegistrationMetaConfig"

        # update these DSC agent configurations if these defaults are not what you want. 
        # See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
        Properties = @{
            RegistrationKey = $RegistrationInfo.PrimaryKey
            RegistrationUrl = $RegistrationInfo.Endpoint
            NodeConfigurationName = "configname.webserver"
            ConfigurationMode = "ApplyAndMonitor"
            ConfigurationModeFrequencyMins = 15
            RefreshFrequencyMins = 30
           RebootNodeIfNeeded = $False
           ActionAfterReboot = "ContinueConfiguration"
           AllowModuleOverwrite = $False
        }
    } 

    $VM = Set-AzureVMExtension `
        -VM $vm `
        -Publisher Microsoft.Powershell `
        -ExtensionName DSC `
        -Version 1.9 `
        -PublicConfiguration $PublicConfiguration

    $VM | Update-AzureVM
 

###Recurso###
Los recursos de DSC son bloques de creación que puede usar para definir una configuración de Configuración de estado deseado (DSC) de Windows PowerShell. DSC incluye un conjunto de funcionalidades integradas para configurar recursos como archivos y carpetas, características y roles de servidor, configuración del registro, variables de entorno y servicios y procesos. Para obtener información acerca de la lista completa de recursos integrados de DSC y cómo usarlos, consulte [Built-In Windows PowerShell Desired State Configuration Resources](https://technet.microsoft.com/library/dn249921.aspx) (Recursos integrados de Configuración de estado deseado de Windows PowerShell).

Los recursos de DSC también se pueden importar como parte de los módulos PowerShell para extender el conjunto de recursos integrados de DSC. Los nodos de DSC extraerán recursos no predeterminados desde el servidor de extracción de DSC, si una configuración de nodo que el nodo debe aplicar contiene referencias a esos recursos. Para obtener información sobre cómo crear recursos personalizados, consulte [Build Custom Windows PowerShell Desired State Configuration Resources](https://technet.microsoft.com/library/dn249927.aspx) (Creación de recursos personalizados de Configuración de estado deseado de Windows PowerShell).

DSC de Automatización de Azure incluye los mismos recursos integrados de DSC que DSC de PS. Es posible agregar recursos adicionales a DSC de Automatización de Azure mediante la importación a Automatización de Azure de los módulos PowerShell que contienen los recursos.

DSC de Automatización de Azure actualmente proporciona los siguientes cmdlets en el [módulo PowerShell de Administrador de recursos de Azure](https://msdn.microsoft.com/library/mt244122.aspx) para la administración de módulos PowerShell:

- `New-AzureAutomationModule`
- `Remove-AzureAutomationModule`
- `Set-AzureAutomationModule`
- `Get-AzureAutomationModule`

###Trabajo de compilación###
Un trabajo de compilación en DSC de Automatización de Azure es una instancia de compilación de una configuración, para crear una o más configuraciones de nodo. Son similares a los trabajos de runbook de Automatización de Azure, excepto en que no realizan realmente ninguna tarea, salvo la creación de configuraciones de nodo. Toda configuración creada por un trabajo de compilación se coloca automáticamente en el servidor de extracción de DSC de Automatización de Azure y sobrescriben las versiones anteriores de configuraciones de nodo, si existían para esta configuración. El nombre de una configuración de nodo generada por un trabajo de compilación toma el formato de "<Configuration-name>.<Node configuration-block-name>". Por ejemplo, al compilar la siguiente configuración se generará una configuración de nodo único llamada "MyConfiguration.webserver".


![texto alternativo](./media/automation-dsc-overview/AADSC_5.png)


>[AZURE.NOTE]Las configuraciones se pueden publicar, al igual que los runbooks. Esto no está relacionado con la colocación de elementos de DSC en el servidor de extracción de DSC de Automatización de Azure. Los trabajos de compilación hacen que se coloquen los elementos de DSC en el servidor de extracción de DSC de Automatización de Azure. Para obtener más información acerca de cómo "publicar" en Automatización de Azure, consulte [Publicar un runbook](https://msdn.microsoft.com/library/dn903765.aspx).


DSC de Automatización de Azure actualmente proporciona los siguientes cmdlets en el [módulo PowerShell de Administrador de recursos de Azure](https://msdn.microsoft.com/library/mt244122.aspx) para la administración de trabajos de compilación:

-	`Get-AzureAutomationDscCompilationJob`
-	`Get-AzureAutomationDscCompilationJobOutput`
-	`Start-AzureAutomationDscCompilationJob`

##Problemas conocidos:##

- Debido a que DSC de Automatización de Azure se encuentra en vista previa, la primera vez que use esta característica, deberá registrarse para obtenerla mediante cmdlets de Azure PowerShell o el Portal de vista previa de Azure. Para registrarse, puede llamar a los dos siguientes cmdlets:

 - `Register-AzureProvider –ProviderNamespace Microsoft.Automation`
 - `Register-AzureProviderFeature -FeatureName dsc -ProviderNamespace Microsoft.Automation` 
 
>[AZURE.NOTE]El registro de DSC de Automatización de Azure puede demorar hasta una hora antes de que la característica esté disponible para su uso.

- En este momento, DSC de Automatización de Azure no es compatible con configuraciones parciales o compuestas de DSC.

- La versión más reciente de WMF 5 debe estar instalada para el agente DSC de PowerShell para que Windows pueda comunicarse con Automatización de Azure. En este momento, el agente DSC de PowerShell para Linux no admite comunicación con Automatización de Azure. Esto se actualizará pronto.

- Automatización de Azure no admite el uso en paralelo de módulos PowerShell. Esto significa que todas las configuraciones dentro de una cuenta de Automatización deben funcionar con la versión más reciente de un módulo PowerShell importado a una cuenta de Automatización y con cualquier recurso de DSC de PowerShell que un módulo contiene y que la configuración usa.

- El servidor de extracción tradicional de DSC de PowerShell espera que los archivos ZIP del módulo se coloquen en el servidor de extracción con el formato **NombreMódulo\_versión.zip**. Automatización de Azure espera que los módulos PowerShell se importen con nombres con formato **NombreMódulo.zip**. Consulte [esta entrada de blog](http://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/) para obtener más información sobre el formato de módulo de integración que se necesita para importar el módulo a Automatización de Azure.

- Los módulos PowerShell que especifican los recursos de DSC en paralelo dentro del módulo, mediante un formato de "versión por carpeta", no funcionarán en Automatización de Azure por el momento.

- Cuando se importan módulos PowerShell a Automatización de Azure con el cmdlet `New-AzureAutomationModule` o el cmdlet `Set-AzureAutomationModule`, la importación de módulos es asincrónica. Aunque el cmdlet se devuelva correctamente, esto no significa que la importación de módulos se haya realizado de manera correcta. Para comprobar si la importación se realizó correctamente, use `Get-AzureAutomationModule –Name <ModuleName>` (asegúrese de obtener solo este módulo, no todos) y compruebe que el campo **ProvisioningState** del módulo indica que se realizó con éxito.

- Los módulos PowerShell importados a Automatización de Azure no pueden contener archivos .doc o .docx. Algunos módulos PowerShell que contienen recursos de DSC contienen estos archivos, para propósitos de ayuda. Estos archivos se deben quitar de los módulos antes de la importación a Automatización de Azure.

- Las configuraciones de Automatización de Azure solo admiten actualmente cadenas para valores de parámetro. Si desea pasar un elemento distinto de una cadena para el valor de un parámetro, pase el valor de parámetro como una cadena JSON y dentro de la configuración use ConvertFrom-Json para convertir la cadena JSON nuevamente en un valor que no sea de cadena.

- Cuando un nodo se registra por primera vez con una cuenta de Automatización de Azure o cuando el nodo se cambia para asignarlo a una configuración de nodo distinta de lado servidor, su estado estará en cumplimiento, incluso si el estado del nodo no está realmente en cumplimiento con la configuración de nodo a la que ahora está asignado. Una vez que el nodo envía su primer informe después del registro o un cambio de asignación de la configuración de nodo, el estado del nodo puede ser de confianza.

- Cuando se incorpora una máquina virtual de Azure para la administración con DSC de Automatización de Azure mediante el uso de `Register-AzureAutomationDscNode`, `Set-AzureVMExtension` o la extensión de máquina virtual de DSC de Automatización de Azure en el Portal de vista previa de Azure, si el registro muestra el error **No se especificó el nombre del equipo, y el directorio de configuración no tiene ningún archivo de configuración**, se trata de una falsa alarma y el registro de la máquina virtual se realizó correctamente. Para comprobar si el registro se realizó de manera correcta, puede usar el cmdlet `Get-AzureAutomationDscNode`.

- Al incorporar una VM de Azure para la administración con DSC de Automatización de Azure usando `Register-AzureAutomationDscNode`, `Set-AzureVMExtension` o la extensión de máquina virtual de DSC de Automatización de Azure en el portal de vista previa de Azure, la máquina virtual puede tardar hasta una hora en aparecer como nodo DSC en Automatización de Azure. Esto se debe a la instalación de Windows Management Framework 5.0 en la máquina virtual por la extensión de DSC de la máquina virtual de Azure, que es necesario para incorporar la máquina a DSC de Automatización de Azure.

- Los nodos de DSC incorporados a DSC de Automatización de Azure mostrarán inicialmente el estado ''Conforme'', incluso si no son realmente conformes con la configuración del nodo de DSC que tienen asignada. Después de que un nodo realice su primera extracción y envíe su primer informe de DSC a DSC de Automatización de Azure, el estado del nodo debe ser correcto.

<!---HONumber=August15_HO9-->