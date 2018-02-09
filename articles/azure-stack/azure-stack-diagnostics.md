---
title: "Diagnósticos de Azure Stack"
description: "Cómo recopilar archivos de registro de diagnósticos en Azure Stack"
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 12/15/2017
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: e823aeb4291b3e765b35181c24b41fa58c170cca
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="azure-stack-diagnostics-tools"></a>Herramientas de diagnóstico de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y kit de desarrollo de Azure Stack*
 
Azure Stack es una gran colección de componentes que funcionan juntos e interactúan entre sí. Todos estos componentes generan sus propios registros únicos. Esto puede dificultar el diagnóstico de problemas, especialmente en el caso de errores procedentes de varios componentes de Azure Stack que interactúan. 

Nuestras herramientas de diagnóstico ayudan a asegurar que el mecanismo de recopilación de registros es fácil y eficaz. En el siguiente diagrama se muestra cómo funcionan las herramientas de recopilación de registros en Azure Stack:

![Herramientas de diagnóstico de Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Recopilador de seguimiento
 
El recopilador de seguimiento está habilitado de forma predeterminada y se ejecuta continuamente en segundo plano para recopilar el todos los registros de Seguimiento de eventos para Windows (ETW) de los servicios de componentes de Azure Stack. Los registros de ETW se almacenan en un recurso compartido local común con un límite de antigüedad de cinco días. Una vez que se alcanza este límite, se eliminan los archivos más antiguos cuando se crean nuevos. El tamaño máximo predeterminado permitido para cada archivo es de 200 MB. Se realiza una comprobación de tamaño cada dos minutos y si el archivo actual es > = 200 MB, se guarda y se genera otro. También hay un límite de 8 GB en el tamaño total del archivo generado por cada sesión de eventos. 

## <a name="log-collection-tool"></a>Herramienta de recopilación de registros
 
El cmdlet de PowerShell **Get-AzureStackLog** se puede utilizar para recopilar registros de todos los componentes en un entorno de Azure Stack. Los guarda en archivos ZIP en una ubicación definida por el usuario. Si el equipo de soporte técnico de Azure Stack necesita los registros para ayudarle a solucionar un problema, puede que le pidan que ejecute esta herramienta.

> [!CAUTION]
> Estos archivos de registro pueden contener información de identificación personal (PII). Tenga esto en cuenta antes de publicar los archivos de registro.
 
Estos son algunos ejemplos de tipos de registro que se recopilan:
*   **Registros de implementación de Azure Stack**
*   **Registros de eventos de Windows**
*   **Registros de Panther**
*   **Registros de clúster**
*   **Registros de diagnóstico de almacenamiento**
*   **Registros de ETW**

El Recopilador de seguimiento recopila estos archivos y los guarda en un recurso compartido. El cmdlet de PowerShell **Get-AzureStackLog** se puede utilizar después para recopilarlos cuando sea necesario.
 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Para ejecutar Get-AzureStackLog en un sistema Azure Stack Development Kit (ASDK)
1. Inicie sesión como **AzureStack\CloudAdmin** en el host.
2. Abra una ventana de PowerShell como administrador.
3. Ejecute el cmdlet de PowerShell en **AzureStackLog Get**.

**Ejemplos:**

  Recopilar todos los registros de todos los roles:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs
  ```

  Recopilar registros de los roles VirtualMachines y BareMetal:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
  ```

  Recopilar registros de los roles VirtualMachines y BareMetal, filtrando por fecha los archivos de registro de las últimas 8 horas:
    
  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Recopilar registros de los roles VirtualMachines y BareMetal, filtrando por fecha los archivos de registro del período comprendido entre las últimas entre 8 y 2 horas:

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="to-run-get-azurestacklog-on-an-azure-stack-integrated-system"></a>Para ejecutar Get-AzureStackLog en un sistema Azure Stack integrado

Para ejecutar la herramienta de recopilación de registros en un sistema integrado, debe tener acceso al punto de conexión con privilegios (PEP). Este es un script de ejemplo que se puede ejecutar mediante el PEP para recopilar registros en un sistema integrado:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputPath "\\<HLH MACHINE ADDRESS>\c$\logs" -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- Al recopilar los registros desde el PEP, especifique el parámetro **OutputPath** para que sea una ubicación en la máquina Hardware Lifecycle Host (HLH). Asegúrese también de que la ubicación está cifrada.
- Los parámetros **OutputSharePath** y **OutputShareCredential** son opcionales y se utilizan al cargar registros en una carpeta compartida externa. Use estos parámetros *además* de **OutputPath**. Si **OutputPath** no se especifica, la herramienta de recopilación de registros usa la unidad del sistema de la máquina virtual PEP para el almacenamiento. Así puede provocar que el script genere un error porque el espacio en disco es limitado.
- Como se muestra en el ejemplo anterior, los parámetros **FromDate** y **ToDate** pueden utilizarse para recopilar registros durante un período de tiempo determinado. Puede resultar útil para escenarios como la recopilación de registros después de aplicar un paquete de actualización en un sistema integrado.

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Consideraciones sobre los parámetros para ASDK y sistemas integrados

- Si no se especifican los parámetros **FromDate** y **ToDate**, se recopilan los registros de las últimas cuatro horas de forma predeterminada.
- Puede usar el parámetro **TimeOutInMinutes** para establecer el tiempo de espera para la colección de registros. Se establece en 150 (2,5 horas) de forma predeterminada.

- Actualmente, no se puede usar el parámetro **FilterByRole** para filtrar la colección de registros de los roles siguientes:

   |   |   |   |
   | - | - | - |
   | ACSMigrationService     | ACSMonitoringService   | ACSSettingsService |
   | ACS                     | ACSFabric              | ACSFrontEnd        |
   | ACSTableMaster          | ACSTableServer         | ACSWac             |
   | ADFS                    | ASAppGateway           | BareMetal          |
   | BRP                     | CA                     | CPI                |
   | CRP                     | DeploymentMachine      | DHCP               |
   | Dominio                  | ECE                    | ECESeedRing        | 
   | FabricRing              | FabricRingServices     | FRP                |
   | Puerta de enlace                 | HealthMonitoring       | HRP                |   
   | IBC                     | InfraServiceController | KeyVaultAdminResourceProvider|
   | KeyVaultControlPlane    | KeyVaultDataPlane      | NC                 |   
   | NonPrivilegedAppGateway | NRP                    | SeedRing           |
   | SeedRingServices        | SLB                    | SQL                |   
   | SRP                     | Storage                | StorageController  |
   | URP                     | UsageBridge            | VirtualMachines    |  
   | WAS                     | WASPUBLIC              | WDS                |


### <a name="bkmk_gui"></a>Recopilación de registros mediante una interfaz gráfica de usuario
En lugar de proporcionar los parámetros necesarios para el cmdlet Get-AzureStackLog para recuperar registros de Azure Stack, también puede aprovechar las herramientas de Azure Stack de código abierto disponibles ubicadas en el repositorio de herramientas de GitHub de Azure Stack principal en http://aka.ms/AzureStackTools.

El script de PowerShell **ERCS_AzureStackLogs.ps1** se almacena en el repositorio de herramientas de GitHub y se actualiza con frecuencia. Para asegurarse de que tiene la versión más reciente disponible, debe descargarlo directamente de http://aka.ms/ERCS. Iniciado desde una sesión de PowerShell administrativa, el script se conecta al punto de conexión con privilegios y ejecuta Get-AzureStackLog con los parámetros proporcionados. Si no se proporcionan parámetros, la acción predeterminada del script es solicitar parámetros a través de una interfaz gráfica de usuario.

Para más información acerca del script de PowerShell ERCS_AzureStackLogs.ps1 script puede ver [un vídeo corto](https://www.youtube.com/watch?v=Utt7pLsXEBc) o consultar el [archivo léame](https://github.com/Azure/AzureStack-Tools/blob/master/Support/ERCS_Logs/ReadMe.md) del script ubicado en el repositorio de herramientas de GitHub de Azure Stack. 

### <a name="additional-considerations"></a>Consideraciones adicionales

* Este comando tardará un rato en ejecutarse, en función de los roles que recopilen los registros. Entre los factores que contribuyen, se incluye la duración especificada para la colección de registros y el número de nodos en el entorno de Azure Stack.
* Una vez finalizada la recopilación de registros, compruebe la nueva carpeta creada en el parámetro **OutputPath** especificado en el comando.
* Cada rol tiene sus registros dentro de archivos ZIP individuales. Según el tamaño de los registros recopilados, un rol puede dividir sus registros en varios archivos ZIP. Para tal rol, si desea que todos los archivos de registro se descompriman en una sola carpeta, use una herramienta que pueda descomprimir de forma masiva (por ejemplo, 7zip). Seleccione todos los archivos comprimidos para el rol y seleccione **extraer aquí**. Así se descomprimen todos los archivos de registro para ese rol en una sola carpeta combinada.
* También se crea un archivo denominado **Get-AzureStackLog_Output.log** en la carpeta que contiene los archivos de registro comprimidos. Este archivo es un registro de la salida del comando, que se puede usar para solucionar problemas durante la recopilación de registros.
* Para investigar un error específico, es posible que sean necesarios registros de más de un componente.
    -   Los registros de eventos y del sistema de todas las máquinas virtuales de la infraestructura se recopilan en el rol *VirtualMachines*.
    -   Los registros de eventos y del sistema de todos los hosts se recopilan en el rol *Storage*.
    -   Los registros de eventos de Hyper-V y del clúster de conmutación por error se recopilan en el rol *Storage*.
    -   Los registros de ACS se recopilan en los roles *Storage* y *ACS*.

> [!NOTE]
> Se aplican límites en el tamaño y la antigüedad para los registros recopilados, ya que resulta esencial para garantizar un uso eficaz del espacio de almacenamiento y para asegurarse de que este no recibe demasiados registros. Sin embargo, cuando se diagnostica un problema, a veces se necesitan registros que podrían no existir ya debido a estos límites. Por lo tanto, se **recomienda encarecidamente** descargar los registros en un espacio de almacenamiento externo (una cuenta de almacenamiento de Azure, un dispositivo de almacenamiento local adicional, etc.) cada entre 8 a 12 horas y conservarlos allí de 1 a 3 meses, en función de los requisitos. Asegúrese también de que la ubicación de almacenamiento está cifrada.

## <a name="next-steps"></a>pasos siguientes
[Solución de problemas de Microsoft Azure Stack](azure-stack-troubleshooting.md)

