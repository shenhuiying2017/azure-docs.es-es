---
title: "Integración de registros de Key Vault mediante Event Hubs - Azure | Microsoft Docs"
description: "Tutorial que repasa los pasos necesarios para que los registros de Key Vault estén disponibles para un SIEM mediante la integración de registros de Azure"
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.topic: article
ms.date: 06/29/2017
ms.author: Barclayn
ms.custom: AzLog
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 9d1e5505ad6da7e24d51d2301f806695e0d9e2c2
ms.contentlocale: es-es
ms.lasthandoff: 06/30/2017



---

# <a name="azure-log-integration-tutorial---processing-azure-key-vault-events-using-event-hubs"></a>Tutorial de integración de registros de Azure: Procesamiento de eventos de Azure Key Vault mediante Event Hubs

La integración de registros de Azure (AzLog) le permite recuperar los eventos registrados y hacer que estén disponibles en su sistema de información de seguridad y administración de eventos (SIEM). Este tutorial le guiará por el proceso para tomar las actividades de Key Vault registradas en una instancia de Event Hubs y hacer que estén disponibles como archivos JSON en el SIEM. Después, puede configurar el SIEM para que procese archivos JSON.

>[!NOTE]
La mayoría de los pasos de este tutorial conllevan la configuración de Key Vault, de las cuentas de almacenamiento y de Event Hubs. Los pasos de integración de registros de Azure específicos están al final de este documento.

A lo largo del documento se ofrece información que le ayudará a entender las razones que hay detrás de cada paso y, cuando sea adecuado, se incluirán vínculos a otros artículos que le ofrecerán una información más detallada sobre determinados temas.

Tenga en cuenta que la mayoría de estos pasos implica la configuración de Event Hubs y Key Vault.  

- [Almacén de claves de Azure](../key-vault/key-vault-whatis.md)
- [Centros de eventos](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Integración de registros de Azure](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Configuración inicial

Necesitará lo siguiente para completar los pasos de este artículo:

1. Una suscripción de Azure y una cuenta en esa suscripción con derechos de administrador. Si no tiene una suscripción, puede obtener una [suscripción gratis](https://azure.microsoft.com/free/).
2. Un sistema con acceso a Internet que cumpla los requisitos para la instalación de la integración de registros de Azure. El sistema puede estar en un servicio en la nube u hospedado en una ubicación local.
3. La [integración de registros de Azure](https://www.microsoft.com/download/details.aspx?id=53324) instalada.
  - Use el escritorio remoto para conectarse al sistema mencionado en el paso 2.
  - Copie el instalador de la integración de registros de Azure en él. Puede [descargar los archivos de instalación](https://www.microsoft.com/download/details.aspx?id=53324)
  - Inicie el instalador y acepte los términos del Contrato de licencia.
  - Decida si va a proporcionar información de telemetría activando la casilla o desactivándola si prefiere no enviar información de uso a Microsoft.
4. Compruebe la versión de PowerShell.
   - Si tiene instalado Windows Server 2016 significa que tiene al menos PowerShell 5.0. Si usa otra versión de Windows Server es posible que tenga versiones inferiores de PowerShell instaladas. Puede comprobar la versión escribiendo ```get-host``` en una ventana de PowerShell.
   - Si no tiene PowerShell 5.0 instalado puede descargarlo de [aquí](https://www.microsoft.com/download/details.aspx?id=50395).
   - Cuando ya disponga al menos de PowerShell 5.0, puede continuar con la instalación de la versión más reciente de Azure PowerShell.
    - Abra una ventana de PowerShell y escriba:
   </br>```Install-Module Azure``` y presione la tecla **ENTRAR**. Ejecute el proceso de instalación y cuando se complete pase al paso siguiente. </br>
   Escriba ```Install-Module AzureRM``` y presione la tecla **ENTRAR**. Ejecute los pasos de instalación.

En este momento ya tiene las versiones más recientes de Azure PowerShell y de la integración de registros de Azure instaladas en un sistema y ya está listo para continuar con los siguientes pasos de este tutorial.

>[!NOTE]
- Para obtener pasos detallados e información acerca de cómo instalar Azure PowerShell, consulte el artículo titulado [Instalación de Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0)
- Para conocer una información más detallada sobre los pasos de instalación de la integración de registros de Azure, consulte el artículo [Integración de registros de Azure, registros de Azure Diagnostics y reenvío de eventos de Windows](security-azure-log-integration-get-started.md). Este artículo no solo describe la instalación de la integración de registros de Azure pero incluye información general sobre ella.

## <a name="creating-supporting-infrastructure-elements"></a>Creación de los elementos de la infraestructura de soporte

1. Abra una ventana de PowerShell con privilegios elevados y vaya a **c:\Archivos de programa\Microsoft Azure Log Integration**.
2. El primer paso que debe realizar es obtener los cmdlets de AzLog importados. Puede hacerlo mediante la ejecución del script LoadAzLogModule.ps1 (tenga en cuenta el ".\" en el siguiente comando). Escriba **.\LoadAzLogModule.ps1** y presione ENTRAR.
Debería ver algo parecido a lo que se muestra en la siguiente ilustración. </br>

    ![Lista de módulos cargados](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. Escriba **Login-AzureRmAccount** y presione ENTRAR. Aparecerá una ventana de inicio de sesión. Escriba la información de credenciales de la suscripción que va a usar para este tutorial.

    >[!NOTE]
    Si se trata de la primera vez que inicia sesión en Azure desde este equipo verá un mensaje que le pregunta si desea permitir a Microsoft recopilar datos de uso de PowerShell. Es recomendable que lo habilite ya que se utilizará para mejorar Azure PowerShell.

4. Después de decidir cómo proceder con la solicitud de recopilación de datos y de haberse autenticado correctamente, ya habrá iniciado sesión y en pantalla aparecerá algo de información tal y como se muestra a continuación. Tome nota de la información de suscripción.

    ![Lista de módulos cargados](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. Debe crear algunas variables para almacenar algunos valores que se usarán más adelante.
    - Escriba todas las líneas de PowerShell que aparecen a continuación y presione **ENTRAR** después de cada una de ellas. Preste atención a los comentarios que aparecen junto a ellas. Puede que necesite ajustar los valores para que se adapten a su entorno:
        - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’``` (El nombre de la suscripción puede ser diferente y puede verlo como parte de la salida del comando anterior)
        - ```$location = 'West US'``` (Se utilizará esta variable para pasar la ubicación donde se deben crear los recursos. Puede cambiarla para que sea cualquier otra ubicación de su elección)
        - ```$random = Get-Random```
        - ``` $name = 'azlogtest' + $random``` (El nombre puede ser de cualquier tipo, pero solo debe incluir números y letras en minúsculas)
        - ``` $storageName = $name``` (Esto se utilizará para el nombre de la cuenta de almacenamiento)
        - ```$rgname = $name ``` (Esto se utilizará para el nombre del grupo de recursos)
        - ``` $eventHubNameSpaceName = $name``` (Este es el nombre del espacio de nombres de la instancia de Event Hubs)
6. Después. debe especificar la suscripción con la que va a trabajar.
    - Escriba ```Select-AzureRmSubscription -SubscriptionName $subscriptionName``` y pulse **ENTRAR**
7. A continuación, cree un grupo de recursos.
    - Escriba ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location``` y pulse **ENTRAR**. Esto permite crear un grupo de recursos. Si en este momento escribe $rg y pulsa **ENTRAR** aparecerá una salida parecida a la información que se muestra a continuación:

        ![Lista de módulos cargados](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. A continuación, cree una cuenta de almacenamiento que se usará para realizar un seguimiento de la información de estado.
    - ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. Después, el espacio de nombres de la instancia de Event Hubs. Esto es necesario para crear una instancia de Event Hubs.
    - ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. A continuación, obtendrá el identificador de regla que se usará con el proveedor de Insights.
    - ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. Ahora obtendrá todas las ubicaciones posibles de Azure y agregará los nombres a una variable que puede que use en un paso posterior.
    - ```$locationObjects = Get-AzureRMLocation```
    - ```$locations = @('global') + $locationobjects.location```Si escribe $locations y pulsa **ENTRAR** en este momento, verá los nombres de las ubicaciones sin la información adicional que devuelve get-AzureRmLocation.
12. Ahora puede crear un perfil de registro de Azure Resource Manager. Puede obtener más información sobre el perfil de registro de Azure en el artículo [Información general sobre el registro de actividad de Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)
    - ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```

>[!NOTE]
Puede que reciba un mensaje de error al intentar crear un perfil de registro. Revise la documentación de Get-AzureRmLogProfile y Remove-AzureRmLogProfile. Si ejecuta Get-AzureRmLogProfile, verá información sobre el perfil de registro. Puede eliminar el perfil de registro existente escribiendo ```Remove-AzureRmLogProfile -name 'Log Profile Name' ```


![Error de perfil de Resource Manager](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="creating-a-key-vault"></a>Creación de una instancia de Key Vault

En primer lugar, cree la instancia de Azure Key Vault escribiendo:

```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

A continuación, configure el registro para esa instancia.

```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generating-log-activity"></a>Generación de actividades de registro

 Las solicitudes se deben enviar a Key Vault para generar actividades de registro. Acciones como la generación de claves, la lectura o el almacenamiento de secretos de Key Vault, crearán entradas del registro.

 - El siguiente paso muestra simplemente las claves de almacenamiento actuales.
    - ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
 - La siguiente línea genera una nueva **key2**
    - ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
 - Ahora puede volver a mostrar las claves y verá que **key2** contiene un valor diferente.
    - ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
- Por último, la configuración y lectura de un secreto generará entradas adicionales del registro
    - ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)```
    - ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

        ![Secreto devuelto](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configuring-azure-log-integration"></a>Configuración de la integración de registros de Azure

Ahora que ya ha configurado todos los elementos necesarios para guardar el registro de Key Vault en una instancia de Event Hubs, debe seguir los pasos necesarios para configurar la integración de registros de Azure.

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

Ejecute el comando AzLog para cada instancia de Event Hubs:

1.  ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2.  ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Aproximadamente un minuto después de la ejecución de los últimos comandos, deberían empezar a generarse los archivos json. Puede confirmarlo supervisando el directorio **c:\users\AzLog\EventHubJson**

## <a name="next-steps"></a>Pasos siguientes

- [Preguntas más frecuentes sobre la integración de registro de Azure](security-azure-log-integration-faq.md)
- [Introducción a la integración de registro de Azure](security-azure-log-integration-get-started.md)
- [Integración de registros de recursos de Azure en sistemas SIEM ](security-azure-log-integration-overview.md)

