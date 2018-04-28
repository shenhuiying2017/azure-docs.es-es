---
title: Restauración y copia de seguridad periódica de Azure Service Fabric (versión preliminar) | Microsoft Docs
description: Use la característica de restauración y copia de seguridad periódica de Service Fabric para proteger las aplicaciones frente a pérdidas de datos.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAA58600-897E-4CEE-9D1C-93FACF98AD1C
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: f2ab583ecc77122f2c76f905a1cd7b936278bd41
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric-preview"></a>Restauración y copia de seguridad periódica de Azure Service Fabric (versión preliminar)
> [!div class="op_single_selector"]
> * [Clústeres en Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Clústeres independientes](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric es una plataforma de sistemas distribuidos que facilita el desarrollo y la administración de aplicaciones en la nube basadas en microservicios, confiables y distribuidas. Permite la ejecución de microservicios sin estado y con estado. Los servicios con estado pueden mantener el estado mutable y autoritativo tras la solicitud y la respuesta o una transacción completa. Si un servicio con estado deja de funcionar durante mucho tiempo o pierde información debido a un desastre, es posible que tenga que restaurarse a alguna copia de seguridad reciente de su estado con el fin de seguir proporcionando servicios una vez que vuelva a activarse.

Service Fabric replica el estado en varios nodos para asegurarse de que el servicio ofrece alta disponibilidad. Incluso si se produce un error en un nodo del clúster, el servicio sigue estando disponible. Sin embargo, en algunos casos, aún se desea que los datos del servicio sean de confianza frente a los errores mayores.
 
Por ejemplo, es posible que un servicio quiera realizar copias de seguridad de los datos como medida de protección en los escenarios siguientes:
- En caso de que se produzca una pérdida permanente de todo un clúster de Service Fabric.
- En caso de que se produzca una pérdida permanente de la mayoría de las réplicas de una partición del servicio.
- Errores administrativos por los cuales el estado se elimina o daña accidentalmente. Por ejemplo, un administrador con suficientes privilegios elimina el servicio por error.
- Errores en el servicio que causan daños en los datos. Por ejemplo, esto puede suceder cuando una actualización de código de servicio empieza a escribir datos defectuosos en una colección confiable. En tal caso, es posible que el código y los datos se tengan que revertir a un estado anterior.
- Procesamiento de datos sin conexión. Podría ser conveniente procesar los datos para inteligencia empresarial sin conexión de forma independiente del servicio que los genera.

Service Fabric proporciona una API integrada para realizar [copias de seguridad y restauraciones](service-fabric-reliable-services-backup-restore.md) a un momento dado. Los desarrolladores de aplicaciones pueden usar estas API para hacer una copia de seguridad del estado del servicio periódicamente. Además, si los administradores de servicios quieren desencadenar una copia de seguridad desde el exterior del servicio en un momento específico, como antes de la actualización de la aplicación, los desarrolladores deben exponer la copia de seguridad (y la restauración) como una API del servicio. El mantenimiento de las copias de seguridad supone un costo adicional. Por ejemplo, es posible que quiera hacer cinco copias de seguridad incrementales cada media hora, seguidas de una copia de seguridad completa. Tras la copia de seguridad completa, puede eliminar las copias de seguridad incrementales anteriores. Este enfoque requiere un código adicional que conlleva costos adicionales durante el desarrollo de aplicaciones.

La realización de copias de seguridad de los datos de la aplicación de forma periódica es una necesidad básica para la administración de una aplicación distribuida y para la protección frente a la pérdida de datos o la pérdida prolongada de la disponibilidad del servicio. Service Fabric proporciona un servicio de restauración y copia de seguridad opcional, que le permite configurar la copia de seguridad periódica de Reliable Services con estado (incluidos los servicios de actor) sin tener que escribir ningún código adicional. También facilita la restauración de las copias de seguridad realizadas previamente. 

> [!NOTE]
> La característica de restauración y copia de seguridad periódica está actualmente en **versión preliminar** y no es compatible con las cargas de trabajo de producción. 
>

Service Fabric proporciona un conjunto de API para lograr la siguiente funcionalidad relacionada con la característica de restauración y copia de seguridad periódica:

- Programación de copias de seguridad periódicas de los servicios de confianza con estado y Reliable Actors con compatibilidad con la carga de la copia de seguridad en ubicaciones de almacenamiento (externas). Ubicaciones de almacenamiento admitidas
    - Azure Storage
    - Recurso compartido de archivos (local)
- Enumeración de las copias de seguridad
- Desencadenamiento de una copia de seguridad ad hoc de una partición
- Restauración de una partición mediante la copia de seguridad anterior
- Suspensión temporal de las copias de seguridad
- Administración de la retención de copias de seguridad (próximamente)

## <a name="prerequisites"></a>requisitos previos
* Clúster de Service Fabric con la versión 6.2 de Fabric y versiones posteriores. El clúster debe configurarse en Windows Server. Consulte este [artículo](service-fabric-cluster-creation-via-arm.md) para ver los pasos necesarios para crear el clúster de Service Fabric mediante la plantilla de recursos de Azure.
* Se requiere el certificado X.509 para el cifrado de secretos a fin de conectarse al almacenamiento y almacenar las copias de seguridad. Consulte este [artículo](service-fabric-cluster-creation-via-arm.md) para saber cómo obtener o crear un certificado X.509.
* Aplicación con estado de confianza de Service Fabric compilada con la versión 3.0 del SDK de Service Fabric o una versión posterior. En el caso de las aplicaciones destinadas a .Net Core 2.0, la aplicación debe compilarse con la versión 3.1 del SDK de Service Fabric o una versión posterior.
* Cree una cuenta de Azure Storage para almacenar las copias de seguridad de la aplicación.

## <a name="enabling-backup-and-restore-service"></a>Habilitación del servicio de copia de seguridad y restauración
Primero debe habilitar el _servicio de copia de seguridad y restauración_ en el clúster. Obtenga la plantilla del clúster que desea implementar. Puede usar las [plantillas de ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) o crear una plantilla de Resource Manager. Habilite el _servicio de copia de seguridad y restauración_ realizando los pasos siguientes:

1. Compruebe que `apiversion` está establecido en **`2018-02-01`** para el recurso `Microsoft.ServiceFabric/clusters` y, si no es así, actualícelo como se muestra en el siguiente fragmento de código:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Ahora, habilite el _servicio de copia de seguridad y restauración_ mediante la adición de la siguiente sección `addonFeatures` en la sección `properties`, como se muestra en el siguiente fragmento de código: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Configure el certificado X.509 para el cifrado de credenciales. Esto es importante para asegurarse de que las credenciales proporcionadas para conectarse al almacenamiento se cifran antes de continuar. Configure el certificado de cifrado mediante la adición de la siguiente sección `BackupRestoreService` en la sección `fabricSettings`, como se muestra en el siguiente fragmento de código: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Una vez actualizada la plantilla del clúster con los cambios anteriores, aplíquelos y deje que se complete la actualización o la implementación. Cuando haya terminado, el _servicio de copia de seguridad y restauración_ empezará a ejecutarse en el clúster. El URI de este servicio es `fabric:/System/BackupRestoreService` y el servicio puede estar ubicado en la sección de servicio del sistema de Service Fabric Explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Habilitación de la copia de seguridad periódica del servicio de confianza con estado y Reliable Actors
Vamos a examinar los pasos para habilitar la copia de seguridad periódica del servicio de confianza con estado y Reliable Actors. Con estos pasos se asume que:
- El clúster está configurado mediante la seguridad X.509 con el _servicio de copia de seguridad y restauración_.
- Se implementa un servicio de confianza con estado en el clúster. Para las finalidades de esta guía de inicio rápido, el URI de la aplicación es `fabric:/SampleApp` y el URI del servicio de confianza con estado que pertenece a esta aplicación es `fabric:/SampleApp/MyStatefulService`. Este servicio se implementa con una única partición y el id. de partición es `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- El certificado de cliente con el rol de administrador se instala en el nombre de almacén _My_ (_personal_) de la ubicación del almacén de certificados _CurrentUser_ en la máquina desde donde se invocarán los siguientes scripts. Este ejemplo utiliza `1b7ebe2174649c45474a4819dafae956712c31d3` como huella digital de este certificado. Para obtener más información sobre los certificados de cliente, consulte [Control de acceso basado en rol para clientes de Service Fabric](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Creación de una directiva de copia de seguridad

El primer paso consiste en crear una directiva de copia de seguridad que describa la programación de las copias de seguridad, el almacenamiento de destino para los datos de la copia de seguridad, el nombre de la directiva y el número máximo de copias de seguridad incrementales permitidas antes de desencadenar la copia de seguridad completa. 

Para el almacenamiento de copia de seguridad, use la cuenta de Azure Storage creada anteriormente. En este ejemplo se asume que la cuenta de Azure Storage se denomina `sfbackupstore`. El contenedor `backup-container` está configurado para almacenar las copias de seguridad; si todavía no existe, el contenedor con este nombre se crea durante la carga de la copia de seguridad. Rellene `ConnectionString` con una cadena de conexión válida para la cuenta de Azure Storage.

Ejecute el siguiente script de PowerShell para invocar la API REST necesaria para crear la nueva directiva.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=sfbackupstore;AccountKey=64S+3ykBgOuKhd2DK1qHJJtDml3NtRzgaZUa+8iwwBAH4EzuGt95JmOm7mp/HOe8V3l645iv5l8oBfnhhc7dJA==;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

### <a name="enable-periodic-backup"></a>Habilitación de la copia de seguridad periódica
Después de definir la directiva de copia de seguridad para satisfacer los requisitos de protección de datos de la aplicación, dicha directiva debe estar asociada a la aplicación. En función de los requisitos, la directiva de copia de seguridad puede asociarse a una aplicación, un servicio o una partición.

Ejecute el siguiente script de PowerShell para invocar la API REST necesaria para asociar la directiva de copia de seguridad con el nombre `BackupPolicy1` que se ha creado en el paso anterior con la aplicación `SampleApp`.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Comprobación del funcionamiento de las copias de seguridad periódicas

Después de habilitar la copia de seguridad en el nivel de aplicación, empezará a realizarse la copia de seguridad periódicamente de todas las particiones que pertenecen a los servicios de confianza con estado y Reliable Actors en la aplicación según la directiva de copia de seguridad asociada. 

![Evento de estado de copia de seguridad de la partición][0]

### <a name="list-backups"></a>Enumeración de las copias de seguridad

Las copias de seguridad asociadas a todas las particiones que pertenecen a los servicios de confianza con estado y Reliable Actors de la aplicación pueden enumerarse con _GetBackups_ API. Las copias de seguridad pueden enumerarse para una aplicación, un servicio o una partición.

Ejecute el siguiente script de PowerShell para invocar la API de HTTP a fin de enumerar las copias de seguridad creadas para todas las particiones dentro de la aplicación `SampleApp`.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
Salida de ejemplo de la ejecución anterior:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 

BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 

BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            : 
```

## <a name="preview-limitation-caveats"></a>Vista previa de las limitaciones o advertencias
- No hay ninguna instancia de Service Fabric compilada en los cmdlets de PowerShell.
- Sin compatibilidad con la CLI de Service Fabric.
- Sin compatibilidad con la purga de copia de seguridad automatizada. Requiere limpiezas manuales de las copias de seguridad.
- Sin compatibilidad con los clústeres de Service Fabric en Linux.

## <a name="next-steps"></a>Pasos siguientes
- [Referencia de la API REST de restauración de copias de seguridad](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent_Azure.png

