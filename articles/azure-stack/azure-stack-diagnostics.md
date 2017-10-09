---
title: "Diagnósticos en Azure Stack | Microsoft Docs"
description: "Cómo recopilar archivos de registro de diagnósticos en Azure Stack"
services: azure-stack
documentationcenter: 
author: adshar
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: adshar
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: d8f39d921222a3642e3da6e288b4ca11ae1ccaeb
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---
# <a name="azure-stack-diagnostics-tools"></a>Herramientas de diagnóstico de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Azure Stack Development Kit*
 
Azure Stack es una gran colección de componentes que funcionan juntos e interactúan entre sí. Todos estos componentes generan sus propios registros únicos, lo que significa que los problemas de diagnóstico pueden convertirse rápidamente en una tarea difícil, especialmente en el caso de los errores procedentes de varios componentes que interactúan en Azure Stack. 

Nuestras herramientas de diagnóstico ayudan a asegurar que el mecanismo de recopilación de registros es fácil y eficaz. En el siguiente diagrama se muestra cómo funcionan las herramientas de recopilación de registros en Azure Stack:

![Herramientas de recopilación de registros](media/azure-stack-diagnostics/image01.png)
 
 
## <a name="trace-collector"></a>Recopilador de seguimiento
 
El recopilador de seguimiento está habilitado de forma predeterminada. Se ejecuta continuamente en segundo plano, recopila todos los registros de Seguimiento de eventos para Windows (ETW) de los servicios de componentes en Azure Stack y los almacena en un recurso compartido local. 

A continuación, se incluyen aspectos importantes que hay que saber acerca del recopilador de seguimiento:
 
* El recopilador de seguimiento se ejecuta sin interrupción con límites de tamaño predeterminados. El tamaño máximo predeterminado permitido para cada archivo (200 MB) **no** es un tamaño límite. Se realiza una comprobación de tamaño periódicamente (actualmente cada 10 minutos) y si el archivo actual es > = 200 MB, se guarda y se genera un nuevo archivo. También hay un límite de 8 GB (configurable) en el tamaño total del archivo generado por cada sesión de eventos. Una vez que se alcanza este límite, se eliminan los archivos más antiguos cuando se crean nuevos.
* Los registros tienen un límite de antigüedad de 5 días. Este límite también es configurable. 
* Cada componente define las propiedades de configuración de seguimiento a través de un archivo JSON. Los archivos JSON se almacenan en `C:\TraceCollector\Configuration`. Si es necesario, estos archivos se pueden editar para cambiar los límites de tamaño y de antigüedad de los registros recopilados. Al realizar cambios en estos archivos, será necesario reiniciar el servicio *Recopilador de seguimiento de Microsoft Azure Stack* para que los cambios surtan efecto.
* El ejemplo siguiente incluye un archivo JSON de configuración de seguimiento para las operaciones FabricRingServices de la VM XRP: 

```
{
    "LogFile": 
    {
        "SessionName": "FabricRingServicesOperationsLogSession",
        "FileName": "\\\\SU1FileServer\\SU1_ManagementLibrary_1\\Diagnostics\\FabricRingServices\\Operations\\AzureStack.Common.Infrastructure.Operations.etl",
        "RollTimeStamp": "00:00:00",
        "MaxDaysOfFiles": "5",
        "MaxSizeInMB": "200",
        "TotalSizeInMB": "5120"
    },
    "EventSources":
    [
        {"Name": "Microsoft-AzureStack-Common-Infrastructure-ResourceManager" },
        {"Name": "Microsoft-OperationManager-EventSource" },
        {"Name": "Microsoft-Operation-EventSource" }
    ]
}
```

* **MaxDaysOfFiles**

    Este parámetro controla la antigüedad de los archivos que se van a conservar. Los archivos más antiguos se eliminan.
* **MaxSizeInMB**

    Este parámetro controla el umbral de tamaño de un único archivo. Si se alcanza el tamaño, se crea un nuevo archivo .etl.
* **TotalSizeInMB**

    Este parámetro controla el tamaño total de los archivos .etl generados a partir de una sesión de eventos. Si el tamaño total del archivo es mayor que el valor de este parámetro, se eliminan los archivos antiguos.
  
## <a name="log-collection-tool"></a>Herramienta de recopilación de registros
 
El comando de PowerShell `Get-AzureStackLog` sirve para recopilar registros de todos los componentes en un entorno de Azure Stack. Los guarda en archivos ZIP en una ubicación definida por el usuario. Si nuestro equipo de soporte técnico necesita los registros para ayudarle a solucionar un problema, puede pedirle que ejecute esta herramienta.

> [!CAUTION]
> Estos archivos de registro pueden contener información de identificación personal (PII). Tenga esto en cuenta antes de publicar los archivos de registro.
 
Actualmente, recopilamos los siguientes tipos de registro:
*   **Registros de implementación de Azure Stack**
*   **Registros de eventos de Windows**
*   **Registros de Panther**

     Para solucionar problemas de creación de máquinas virtuales.
*   **Registros de clúster**
*   **Registros de diagnóstico de almacenamiento**
*   **Registros de ETW**

    El recopilador de seguimiento los recopila y almacena en un recurso compartido del cual los recupera `Get-AzureStackLog`.
 
Para identificar todos los registros que se recopilan desde todos los componentes, consulte las etiquetas `<Logs>` en el archivo de configuración del cliente ubicado en `C:\EceStore\<Guid>\<GuidWithMaxFileSize>`.
 
**Para ejecutar Get-AzureStackLog**
1.  Inicie sesión como AzureStack\AzureStackAdmin en el host.
2.  Abra una ventana de PowerShell como administrador.
3.  Ejecute `Get-AzureStackLog`.  

    **Ejemplos**

    - Recopilar todos los registros de todos los roles:

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs`

    - Recopilar registros de los roles VirtualMachines y BareMetal:

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal`

    - Recopilar registros de los roles VirtualMachines y BareMetal, filtrando por fecha los archivos de registro de las últimas 8 horas:

        `Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date)`

Si no se especifican los parámetros `FromDate` y `ToDate`, se recopilan los registros de las últimas cuatro horas de forma predeterminada.

Actualmente, no se puede usar el parámetro `FilterByRole` para filtrar la colección de registros de los roles siguientes:

|   |   |   |
| - | - | - |
| `ACSMigrationService`     | `ACSMonitoringService`   | `ACSSettingsService` |
| `ACS`                     | `ACSFabric`              | `ACSFrontEnd`        |
| `ACSTableMaster`          | `ACSTableServer`         | `ACSWac`             |
| `ADFS`                    | `ASAppGateway`           | `BareMetal`          |
| `BRP`                     | `CA`                     | `CPI`                |
| `CRP`                     | `DeploymentMachine`      | `DHCP`               |
|`Domain`                   | `ECE`                    | `ECESeedRing`        |        
| `FabricRing`              | `FabricRingServices`     | `FRP`                |
|` Gateway`                 | `HealthMonitoring`       | `HRP`                |               
| `IBC`                     | `InfraServiceController` | `KeyVaultAdminResourceProvider`|
| `KeyVaultControlPlane`    | `KeyVaultDataPlane`      | `NC`                 |            
| `NonPrivilegedAppGateway` | `NRP`                    | `SeedRing`           |
| `SeedRingServices`        | `SLB`                    | `SQL`                |     
| `SRP`                     | `Storage`                | `StorageController`  |
| `URP`                     | `UsageBridge`            | `VirtualMachines`    |  
| `WAS`                     | `WASPUBLIC`              | `WDS`                |


Puntos a tener en cuenta:

* Este comando tardará un rato en recopilar los registros en función de los registros del rol que se recopilen. Entre los factores que contribuyen, se incluye la duración especificada para la colección de registros y el número de nodos en el entorno de Azure Stack.
* Una vez finalizada la recopilación de registros, compruebe la nueva carpeta creada en el parámetro `-OutputPath` especificado en el comando.
* Un archivo denominado `Get-AzureStackLog_Output.log` se crea en la carpeta que contiene los archivos ZIP e incluye el resultado del comando, que se puede usar para solucionar problemas en la colección de registros.
* Cada rol tiene sus registros dentro de un archivo ZIP individual. 
* Para investigar un error específico, es posible que sean necesarios registros de más de un componente.
    -   Los registros de eventos y del sistema de todas las máquinas virtuales de la infraestructura se recopilan en el rol *VirtualMachines*.
    -   Los registros de eventos y del sistema de todos los hosts se recopilan en el rol *Storage*.
    -   Los registros de eventos de Hyper-V y del clúster de conmutación por error se recopilan en el rol *Storage*.
    -   Los registros de ACS se recopilan en los roles *Storage* y *ACS*.
* Para obtener más información, consulte el archivo de configuración del cliente. Investigue los distintos roles de las etiquetas `<Logs>`.

> [!NOTE]
> Estamos aplicando límites en el tamaño y la antigüedad para los registros recopilados, ya que resulta esencial para garantizar un uso eficaz del espacio de almacenamiento y para asegurarse de que este no recibe demasiados registros. Dicho esto, cuando diagnostique un problema, a menudo necesitará registros que es posible que ya no existan debido a estos límites que se aplican. Por lo tanto, se **recomienda encarecidamente** descargar los registros en un espacio de almacenamiento externo (una cuenta de almacenamiento de Azure, un dispositivo de almacenamiento local adicional, etc.) cada 8 - 12 horas y conservarlos allí durante 1 - 3 meses, en función de los requisitos.

### <a name="multi-node-considerations"></a>Consideraciones en caso de varios nodos
Si desea recopilar registros en un entorno de varios nodos, tenga en cuenta las siguientes diferencias:
* La función `get-date` no está la lista de permitidos en los entornos de varios nodos. Por lo tanto, debe especificar explícitamente una fecha. Por ejemplo:

   `-FromDate "Friday, August 18, 2017 6:34:48 AM" -ToDate "Friday, August 18, 2017 7:35:25 AM"`
* Especifique una ruta de acceso UNC para la salida a una carpeta compartida en el host de ciclo de vida del hardware, o cualquier otra carpeta compartida a la que pueda acceder. Por ejemplo:

   `Get-AzureStackLog -OutputSharePath \\10.193.128.250\logs -OutputShareCredential $sharecred`

   El parámetro `-OutputShareCredential` le solicita credenciales para acceso a la carpeta compartida.

## <a name="next-steps"></a>Pasos siguientes
[Solución de problemas de Microsoft Azure Stack](azure-stack-troubleshooting.md)

