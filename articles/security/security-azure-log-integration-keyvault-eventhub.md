---
title: Integrar registros de Azure Key Vault mediante Event Hubs | Microsoft Docs
description: "Tutorial en el que se proporcionan los pasos necesarios para que los registros de Key Vault estén disponibles para un SIEM mediante la integración de registros de Azure"
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
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: a648852fadfeb5c9a4ff61c85bbe0af856e445d4
ms.contentlocale: es-es
ms.lasthandoff: 07/08/2017



---

# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Tutorial de integración de registros de Azure: Procesamiento de eventos de Azure Key Vault mediante Event Hubs

Puede usar la integración de registros de Azure (AzLog) para recuperar los eventos registrados y hacer que estén disponibles en su sistema de información de seguridad y administración de eventos (SIEM). Este tutorial le guiará por el proceso de registrar la actividad de Azure Key Vault en un centro de eventos y hacer que esté disponible como archivos JSON en el sistema SIEM. Después, puede configurar el sistema SIEM para que procese los archivos JSON.

>[!NOTE]
>La mayoría de los pasos de este tutorial conllevan la configuración de almacenes de claves, cuentas de almacenamiento y centros de eventos. Los pasos específicos de la integración de registros de Azure están al final de este tutorial.

La información proporcionada en el proceso le ayudará a comprender los motivos detrás de cada paso. Los vínculos a otros artículos proporcionan más detalles sobre determinados temas.

Para más información sobre los servicios que se mencionan en este tutorial, vea: 

- [Almacén de claves de Azure](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Integración de registros de Azure](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Configuración inicial

Necesita lo siguiente para completar los pasos de este artículo:

1. Una suscripción de Azure y una cuenta en esa suscripción con derechos de administrador. Si no tiene una suscripción, puede crear [una cuenta gratuita](https://azure.microsoft.com/free/).
 
2. Un sistema con acceso a Internet que cumpla los requisitos para la instalación de la integración de registros de Azure. El sistema puede estar en un servicio en la nube u hospedado en una ubicación local.

3. La [integración de registros de Azure](https://www.microsoft.com/download/details.aspx?id=53324) instalada. Para instalarla:
   1. Use Escritorio remoto para conectarse al sistema mencionado en el paso 2.
   2. Copie el instalador de la integración de registros de Azure en el sistema. Puede [descargar los archivos de instalación](https://www.microsoft.com/download/details.aspx?id=53324).
   3. Inicie el instalador y acepte los Términos de licencia del software de Microsoft.
   4. Si va a proporcionar información de telemetría, deje activada la casilla. Si prefiere no enviar información de uso a Microsoft, desactive la casilla.
   
   Para más información sobre la integración de registros de Azure y cómo instalarla, vea [Integración de registros de Azure en registros de Azure Diagnostics y reenvío de eventos de Windows](security-azure-log-integration-get-started.md).

4. La versión más reciente de PowerShell.
 
   Si tiene instalado Windows Server 2016, entonces tiene al menos PowerShell 5.0. Si usa otra versión de Windows Server, es posible que tenga instalada una versión anterior de PowerShell. Puede comprobar la versión escribiendo ```get-host``` en una ventana de PowerShell. Si no tiene PowerShell 5.0 instalado, puede [descargarlo](https://www.microsoft.com/download/details.aspx?id=50395).

   Después de que disponga al menos de PowerShell 5.0, puede continuar con la instalación de la versión más reciente:
   1. En una ventana de PowerShell, escriba ```Install-Module Azure``` y presione Entrar. Complete los pasos de instalación. 
   2. Escriba ```Install-Module AzureRM``` y presione Entrar. Complete los pasos de instalación.

   Para más información, vea [Instalar Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).


## <a name="create-supporting-infrastructure-elements"></a>Crear los elementos de la infraestructura de soporte

1. Abra una ventana de PowerShell con privilegios elevados y vaya a **C:\Archivos de programa\Microsoft Azure Log Integration**.
2. Importe los cmdlets de AzLog mediante la ejecución del script LoadAzLogModule.ps1. (Observe ".\" en el siguiente comando). Escriba `.\LoadAzLogModule.ps1` y presione Entrar.
Puede ver algo así:</br>

   ![Lista de módulos cargados](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. Escriba `Login-AzureRmAccount` y presione Entrar. En la ventana de inicio de sesión, escriba la información de credenciales de la suscripción que va a usar para este tutorial.

   >[!NOTE]
   >Si se trata de la primera vez que inicia sesión en Azure desde este equipo, verá un mensaje que le pregunta si quiere permitir a Microsoft recopilar datos de uso de PowerShell. Es recomendable que habilite esta recopilación de datos ya que se usará para mejorar Azure PowerShell.

4. Tras la autenticación correcta, iniciará sesión y verá la información de la captura de pantalla siguiente. Tome nota del identificador y el nombre de la suscripción, ya que los necesitará para completar los pasos posteriores.

   ![Ventana de PowerShell](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. Cree variables para almacenar valores que se usarán más adelante. Escriba todas las líneas de PowerShell siguientes y presione Entrar después de cada una. Es posible que necesite ajustar los valores para que se adapten a su entorno.
    - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’``` (El nombre de la suscripción puede ser diferente. Puede verlo como parte de la salida del comando anterior).
    - ```$location = 'West US'``` (Se utilizará esta variable para pasar la ubicación donde se deben crear los recursos. Puede cambiar esta variable para que sea cualquier otra ubicación de su elección).
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random``` (El nombre puede ser de cualquier tipo, pero solo debe incluir números y letras en minúsculas).
    - ``` $storageName = $name``` (Esta variable se usará para el nombre de la cuenta de almacenamiento).
    - ```$rgname = $name ``` (Esta variable se usará para el nombre del grupo de recursos).
    - ``` $eventHubNameSpaceName = $name``` (El nombre del espacio de nombres del centro de eventos).
6. Especifique la suscripción con la que va a trabajar:
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
7. Cree un grupo de recursos:
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   Si escribe `$rg` y presiona Entrar en este momento, deberá ver un resultado similar al de esta captura de pantalla:

   ![Salida después de la creación de un grupo de recursos](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. Cree una cuenta de almacenamiento que se usará para realizar el seguimiento de la información de estado:
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. Cree el espacio de nombres del centro de eventos. Esto es necesario para crear un centro de eventos.
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. Obtenga el identificador de regla que se usará con el proveedor de Insights:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. Obtenga todas las ubicaciones posibles de Azure y agregue los nombres a una variable que se puede usar en un paso posterior:
    
    1. ```$locationObjects = Get-AzureRMLocation```    
    2. ```$locations = @('global') + $locationobjects.location```
    
    Si escribe `$locations` y presiona Entrar en este momento, verá los nombres de las ubicaciones sin la información adicional que devuelve Get-AzureRmLocation.
12. Cree un perfil de registro de Azure Resource Manager: 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Para más información sobre el perfil de registro de Azure, vea [Información general sobre el registro de actividad de Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

>[!NOTE]
>Es posible que reciba un mensaje de error al intentar crear un perfil de registro. Revise la documentación de Get-AzureRmLogProfile y Remove-AzureRmLogProfile. Si ejecuta Get-AzureRmLogProfile, verá información sobre el perfil de registro. Puede eliminar el perfil de registro existente si escribe ```Remove-AzureRmLogProfile -name 'Log Profile Name' ``` y presiona Entrar.
>
>![Error de perfil de Resource Manager](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Creación de un Almacén de claves

1. Cree el almacén de claves:

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

2. Configure el registro del almacén de claves:

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>Generar actividad de registro

Las solicitudes se deben enviar a Key Vault para generar actividades de registro. Acciones como la generación de claves, la lectura o el almacenamiento de secretos de Key Vault, crearán entradas del registro.

1. Muestre las claves de almacenamiento actuales:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
2. Genere una nueva **key2**:
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
3. Vuelva a mostrar las claves y verá que **key2** contiene un valor diferente:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
4. Establezca y lea un secreto para generar entradas de registro adicionales:
    
   1. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)```    
   2. ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Secreto devuelto](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Configurar la integración de registros de Azure

Ahora que ya ha configurado todos los elementos necesarios para guardar el registro de Key Vault en un centro de eventos, debe configurar la integración de registros de Azure:

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

Ejecute el comando AzLog para cada centro de eventos:

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Aproximadamente un minuto después de la ejecución de los dos últimos comandos, deberían empezar a generarse los archivos JSON. Puede confirmarlo supervisando el directorio **C:\usuarios\AzLog\EventHubJson**.

## <a name="next-steps"></a>Pasos siguientes

- [Preguntas más frecuentes sobre la integración de registro de Azure](security-azure-log-integration-faq.md)
- [Introducción a la integración de registros de Azure](security-azure-log-integration-get-started.md)
- [Integrar registros de recursos de Azure en sistemas SIEM](security-azure-log-integration-overview.md)

