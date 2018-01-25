---
title: "Configuración de Service Map en Operations Management Suite | Microsoft Docs"
description: "Mapa de servicio es una solución de Operations Management Suite que detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios. En este artículo se proporciona información para implementar la solución Mapa de servicio en su entorno y utilizarla en distintos escenarios."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: daseidma;bwren;dairwin
ms.openlocfilehash: e23173fb6708104c39071145595e4eec3454ee76
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="configure-service-map-in-operations-management-suite"></a>Configuración de Service Map en Operations Management Suite
Mapa de servicio detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios. Puede usarlo para ver los servidores tal como los considera: como sistemas interconectados que ofrecen servicios críticos. Service Map muestra las conexiones entre servidores, procesos y puertos en cualquier arquitectura conectada TCP sin una configuración necesaria que sea distinta a la instalación de un agente.

En este artículo se describen los detalles sobre cómo configurar agentes de incorporación y de Mapa de servicio. Para información sobre cómo usar Service Map, consulte [Uso de la solución Service Map en Operations Management Suite](operations-management-suite-service-map.md).

## <a name="dependency-agent-downloads"></a>Descargas de Dependency Agent
| Archivo | SO | Versión | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.3.0 | 1F5261CAAF6C8DF4E03E4927DA918B3461B40B41C6BF5845803878D7CF975693 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.3.0 | 7BADFF2411899114F0214766160E4E871A2462DC137141CEEDEFAF528F428ADD  |


## <a name="connected-sources"></a>Orígenes conectados
Mapa de servicio obtiene sus datos de Microsoft Dependency Agent. El Agente de dependencia depende del agente de OMS para sus conexiones a Operations Management Suite. Es decir, en primer lugar, un servidor debe tener instalado y configurado el agente de OMS y, luego, se puede instalar Dependency Agent. En la tabla siguiente se describen los orígenes conectados que son compatibles con la solución Service Map.

| Origen conectado | Compatible | DESCRIPCIÓN |
|:--|:--|:--|
| Agentes de Windows | Sí | Mapa de servicio analiza y recopila datos de equipos del agente de Windows. <br><br>Además del [agente de OMS](../log-analytics/log-analytics-windows-agent.md), los agentes de Windows requieren el agente de dependencia de Microsoft. Consulte los [sistemas operativos compatibles](#supported-operating-systems) para obtener una lista completa de las versiones de sistema operativo. |
| Agentes de Linux | Sí | Mapa de servicio analiza y recopila datos de equipos de agente de Linux. <br><br>Además del [agente de OMS](../log-analytics/log-analytics-linux-agents.md), los agentes de Linux requieren Microsoft Dependency Agent. Consulte los [sistemas operativos compatibles](#supported-operating-systems) para obtener una lista completa de las versiones de sistema operativo. |
| Grupo de administración de System Center Operations Manager | Sí | Service Map analiza y recopila datos de los agentes de Windows y Linux en un [grupo de administración de System Center Operations Manager](../log-analytics/log-analytics-om-agents.md) conectado. <br><br>Se requiere una conexión directa desde el equipo agente de System Center Operations Manager a Operations Management Suite. Los datos se reenvían del grupo de administración al repositorio de Operations Management Suite.|
| Cuenta de almacenamiento de Azure | Sin  | Service Map recopila datos de equipos agentes, así que no hay ningún dato en él que recopilar de Azure Storage. |

Service Map solo es compatible con plataformas de 64 bits.

En Windows, tanto System Center Operations Manager como Operations Management Suite usan Microsoft Monitoring Agent (MMA) para recopilar y enviar datos de supervisión. (Este agente se denomina agente de System Center Operations Manager, agente OMS, agente de Log Analytics, MMA o agente directo, dependiendo del contexto). System Center Operations Manager y Operations Management Suite ofrecen distintas versiones del MMA listas para usar. Cada una de estas versiones puede informar a System Center Operations Manager, a Operations Management Suite o a ambos.  

En Linux, el agente de OMS para Linux recopila y envía datos de supervisión a Operations Management Suite. Puede usar Service Map en servidores con agentes directos de OMS o en servidores que estén conectados a Operations Management Suite a través de los grupos de administración de System Center Operations Manager.  

En este artículo se hará referencia a todos los agentes como el "agente OMS", ya sean Linux o Windows, ya sea que estén conectados a un grupo de administración de System Center Operations Manager o directamente a Operations Management Suite. Usaremos el nombre de implementación específico del agente solo si es necesario en contexto.

El agente de Mapa de servicio no transmite los datos en sí y no requiere ningún cambio en los firewalls o puertos. Los datos de Service Map siempre se transmiten mediante el agente de OMS a Operations Management Suite, ya sea directamente o a través de la puerta de enlace de OMS.

![Agentes de Service Map](media/oms-service-map/agents.png)

Si es cliente de System Center Operations Manager con un grupo de administración conectado a Operations Management Suite:

- Si los agentes de System Center Operations Manager pueden acceder a Internet para conectarse a Operations Management Suite, no se requiere ninguna configuración adicional.  
- Si los agentes de System Center Operations Manager no pueden acceder a Operations Management Suite a través de Internet, es necesario configurar la puerta de enlace de OMS para que funcione con System Center Operations Manager.
  
Si usa el agente directo de OMS, debe configurar el agente de OMS para conectarse a Operations Management Suite o a la puerta de enlace de OMS. Puede descargar la puerta de enlace de OMS en el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=52666).

### <a name="management-packs"></a>Módulos de administración
Cuando se activa Service Map en un área de trabajo de Operations Management Suite, se envía un módulo de administración de 300 KB a todos los servidores Windows en esa área de trabajo. Si usa agentes de System Center Operations Manager en un [grupo de administración conectado](../log-analytics/log-analytics-om-agents.md), el módulo de administración de Service Map se implementa desde System Center Operations Manager. Si los agentes se conectan directamente, Operations Management Suite ofrece el módulo de administración.

El módulo de administración se denomina Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Se escribe en %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\. El origen de datos que el módulo de administración usa es %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="installation"></a>Instalación
### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Instalación del agente de dependencia en Microsoft Windows
Se requieren privilegios de administrador para instalar o desinstalar al agente.

El agente de dependencia se instala en equipos Windows mediante InstallDependencyAgent-Windows.exe. Si ejecuta este archivo ejecutable sin opciones, se inicia un asistente que puede seguir para realizar la instalación de forma interactiva.  

Utilice los pasos siguientes para instalar el agente de dependencia en cada equipo de Windows:

1.  Siga las instrucciones que aparecen en [Conexión de equipos Windows al servicio Log Analytics de Azure](../log-analytics/log-analytics-windows-agent.md) para instalar el agente de OMS.
2.  Descargue el agente de Windows y ejecútelo mediante el comando siguiente: <br>`InstallDependencyAgent-Windows.exe`
3.  Siga el asistente para instalar el agente.
4.  Si el agente de dependencia no se inicia, compruebe los registros para obtener información detallada del error. En los agentes de Windows, el directorio de registro es %Programfiles%\Microsoft Dependency Agent\logs. 

#### <a name="windows-command-line"></a>Línea de comandos de Windows
Use las opciones de la tabla siguiente para realizar la instalación desde una línea de comandos. Para ver una lista de marcas de instalación, ejecute el programa de instalación con la marca /? de la manera siguiente.

    InstallDependencyAgent-Windows.exe /?

| Marca | DESCRIPCIÓN |
|:--|:--|
| /? | Obtenga una lista de las opciones de la línea de comandos. |
| /S | Realice una instalación silenciosa sin preguntas. |

Los archivos para el agente de dependencia de Windows se ubican en C:\Archivos de programa\Microsoft Dependency Agent de manera predeterminada.

### <a name="install-the-dependency-agent-on-linux"></a>Instalar Dependency Agent en Linux
Es necesario el acceso raíz para instalar o configurar el agente.

El agente de dependencia se instala en equipos Linux con InstallDependencyAgent-Linux64.bin, un script de shell con un archivo binario autoextraíble. Puede ejecutar el archivo con sh o agregar permisos de ejecución al propio archivo.
 
Utilice los pasos siguientes para instalar el agente de dependencia en cada equipo de Linux:

1.  Use las instrucciones que aparecen en [Recopilación y administración de datos de equipos Linux](https://technet.microsoft.com/library/mt622052.aspx) para instalar el agente de OMS.
2.  Instale el agente de dependencia de Linux como raíz mediante el comando siguiente:<br>`sh InstallDependencyAgent-Linux64.bin`
3.  Si el agente de dependencia no se inicia, compruebe los registros para obtener información detallada del error. En los agentes de Linux, el directorio de registro es /var/opt/microsoft/dependency-agent/log.

Para ver una lista de marcas de instalación, ejecute el programa de instalación con la marca -help de la manera siguiente.

    InstallDependencyAgent-Linux64.bin -help

| Marca | DESCRIPCIÓN |
|:--|:--|
| -help | Obtenga una lista de las opciones de la línea de comandos. |
| -s | Realice una instalación silenciosa sin preguntas. |
| --check | Compruebe los permisos y el sistema operativo, pero no instale el agente. |

Los archivos del agente de dependencia se colocan en los directorios siguientes:

| Archivos | La ubicación |
|:--|:--|
| Archivos principales | /opt/microsoft/dependency-agent |
| Archivos de registro | /var/opt/microsoft/dependency-agent/log |
| Archivos de configuración | /etc/opt/microsoft/dependency-agent/config |
| Archivos ejecutables de servicio | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Archivos binarios de almacenamiento | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Ejemplos de script de instalación
Para implementar fácilmente Dependency Agent en muchos servidores a la vez, resulta útil usar un script. Puede usar los siguientes ejemplos de script para descargar e instalar Dependency Agent en Windows o Linux.

### <a name="powershell-script-for-windows"></a>Script de PowerShell para Windows
```PowerShell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Script de shell para Linux
```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="azure-vm-extension"></a>Extensión de máquina virtual de Azure
Puede implementar fácilmente el agente de dependencia en sus máquinas virtuales de Azure mediante una [extensión de máquina virtual de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).  Con la extensión de máquina virtual de Azure, puede implementar al agente de dependencia en las máquinas virtuales mediante un script de PowerShell o directamente en la plantilla de Azure Resource Manager de la máquina virtual.  Hay una extensión disponible para Windows (DependencyAgentWindows) y Linux (DependencyAgentLinux).  Si la implementación se realiza mediante la extensión de máquina virtual de Azure, los agentes se pueden actualizar automáticamente a las versiones más recientes.

Para implementar la extensión de máquina virtual de Azure mediante PowerShell, puede usar el siguiente ejemplo:
```PowerShell
#
# Deploy the Dependency Agent to every VM in a Resource Group
#

$version = "9.1"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzureRmVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzureRmVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

Una manera incluso más fácil de garantizar que el agente de dependencia está en cada una de las máquinas virtuales consiste en incluir el agente en la plantilla de Azure Resource Manager.  Tenga en cuenta que el agente de dependencia depende todavía del agente de OMS, por lo que primero se debe implementar la [extensión de máquina virtual del agente de OMS](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-vm-extension).  El siguiente fragmento de JSON se puede agregar a la sección *Recursos* de la plantilla.
```JSON
"type": "Microsoft.Compute/virtualMachines/extensions",
"name": "[concat(parameters('vmName'), '/DependencyAgent')]",
"apiVersion": "2017-03-30",
"location": "[resourceGroup().location]",
"dependsOn": [
"[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
],
"properties": {
    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
    "type": "DependencyAgentWindows",
    "typeHandlerVersion": "9.1",
    "autoUpgradeMinorVersion": true
}

```


## <a name="desired-state-configuration"></a>Configuración de estado deseada
Para implementar el agente de dependencia a través de Desired State Configuration, puede usar el módulo xPSDesiredStateConfiguration y un poco de código similar al siguiente:
```
configuration ServiceMap {

Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

Node localhost
{ 
    # Download and install the Dependency Agent
    xRemoteFile DAPackage 
    {
        Uri = "https://aka.ms/dependencyagentwindows"
        DestinationPath = $DAPackageLocalPath
    }

    xPackage DA
    {
        Ensure="Present"
        Name = "Dependency Agent"
        Path = $DAPackageLocalPath
        Arguments = '/S'
        ProductId = ""
        InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
        InstalledCheckRegValueName = "DisplayName"
        InstalledCheckRegValueData = "Dependency Agent"
        DependsOn = "[xRemoteFile]DAPackage"
    }
  }
}
```

## <a name="uninstallation"></a>Desinstalación
### <a name="uninstall-the-dependency-agent-on-windows"></a>Desinstalación del agente de dependencia en Windows
Un administrador puede desinstalar Dependency Agent de Windows a través del Panel de control.

Un administrador también puede ejecutar %Programfiles%\Microsoft Dependency Agent\Uninstall.exe para desinstalar Dependency Agent.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Desinstalar Dependency Agent en Linux
Puede desinstalar al agente de dependencia de Linux con el siguiente comando.
<br>RHEL, CentOS y Oracle:
```
sudo rpm -e dependency-agent
```
Ubuntu:
```
sudo dpkg --purge dependency-agent
```
## <a name="troubleshooting"></a>solución de problemas
Esta sección puede ayudarle si tiene problemas para instalar o ejecutar Service Map. Si sigue sin poder resolver el problema, póngase en contacto con Soporte técnico de Microsoft.

### <a name="dependency-agent-installation-problems"></a>Problemas de instalación del agente de dependencia
#### <a name="installer-asks-for-a-reboot"></a>El instalador solicita un reinicio
Agente de dependencia, *por lo general*, no requiere un reinicio durante su instalación o desinstalación. Sin embargo, en algunos casos poco frecuentes, un servidor de Windows Server requiere un reinicio para continuar con una instalación. Esto ocurre cuando una dependencia, normalmente los redistribuibles de Visual C++ de Microsoft, requiere un reinicio debido a un archivo bloqueado.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Aparece el mensaje "Unable to install Dependency Agent: Visual Studio Runtime libraries failed to install (code = [code_number])" (No se puede instalar el agente de dependencia: error al instalar las bibliotecas en tiempo de ejecución de Visual Studio (code = [code_number]))

El agente de dependencia de Microsoft se basa en las bibliotecas en entorno de tiempo de ejecución de Microsoft Visual Studio. Recibirá un mensaje si hay algún problema durante la instalación de las bibliotecas. 

Los instaladores de la biblioteca en tiempo de ejecución crean registros en la carpeta %LOCALAPPDATA%\temp. El archivo será dd_vcredist_arch_yyyymmddhhmmss.log, donde *arch* es "x86" o "amd64" y *yyyymmddhhmmss* es la fecha y hora (con formato de 24 horas) en que se creó el registro. El registro proporciona detalles sobre el problema que bloquea la instalación.

Puede ser útil instalar uno mismo las [últimas bibliotecas en entorno de tiempo de ejecución](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) primero.

En la tabla siguiente se muestran números de código y resoluciones sugeridas.

| Código | DESCRIPCIÓN | Resolución |
|:--|:--|:--|
| 0 x 17 | El instalador de la biblioteca requiere una actualización de Windows que no se ha instalado. | Mire el registro del instalador de la biblioteca más reciente.<br><br>Si una referencia a "Windows8.1-KB2999226-x64.msu" va seguida de una línea "Error 0x80240017: No se pudo ejecutar el paquete MSU", no tiene los requisitos previos para instalar KB2999226. Siga las instrucciones que aparecen en la sección de requisitos previos en [Universal C Runtime en Windows](https://support.microsoft.com/kb/2999226). Es posible que tenga que ejecutar Windows Update y reiniciar varias veces para instalar los requisitos previos.<br><br>Ejecute de nuevo el instalador de Agente de dependencia de Microsoft. |

### <a name="post-installation-issues"></a>Problemas posteriores a la instalación
#### <a name="server-doesnt-appear-in-service-map"></a>El servidor no aparece en Service Map
Si su instalación de Agente de dependencia se ha realizado correctamente, pero no ve el servidor en la solución de Service Map (Mapa de servicios):
* ¿Se ha instalado Agente de dependencia correctamente? Para validarlo, compruebe si el servicio está instalado y se ejecuta.<br><br>
**Windows**: busque el servicio llamado "Agente de dependencia de Microsoft".<br>
**Linux**: busque el proceso en ejecución "microsoft-dependency-agent".

* ¿Se encuentra en el [plan de tarifa Gratis de Operations Management Suite/Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)? El plan gratuito permite hasta cinco servidores de Service Map (Mapa de servicios) únicos. Ningún servidor posterior aparecerá en Service Map (Mapa de servicios), incluso si los cinco anteriores ya no envían datos.

* ¿El servidor envía datos de registro y rendimiento a Operations Management Suite? Vaya a la búsqueda de registros y ejecute la siguiente consulta para el equipo: 

        * Computer="<your computer name here>" | measure count() by Type
        
  ¿obtuvo una amplia variedad de eventos en los resultados? ¿Son los datos recientes? Si es así, el agente de OMS funciona correctamente y se comunica con el servicio de Operations Management Suite. Si no es así, compruebe el agente de OMS en el servidor: [Solución de problemas del agente OMS para Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) o [Solución de problemas del agente OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>El servidor aparece en Service Map, pero no tiene procesos
Si ve el servidor en Service Map (Mapa de servicios), pero no tiene datos de proceso ni de conexión, quiere decir que Agente de dependencia está instalado y se ejecuta. Sin embargo, el controlador kernel no se ha cargado. 

Revise el archivo C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) o el archivo /var/opt/microsoft/dependency-agent/log/service.log (Linux). Las últimas líneas del archivo deben indicar por qué no se cargó el kernel. Por ejemplo, es posible que el kernel no sea compatible con Linux si actualizó el kernel.

## <a name="data-collection"></a>Colección de datos
Puede esperar que cada agente transmita aproximadamente 25 MB por día, dependiendo de la complejidad de las dependencias del sistema. Cada agente envía datos de dependencia de Service Map cada 15 segundos.  

Habitualmente, el agente de dependencia consume un 0,1 por ciento de memoria del sistema y un 0,1 por ciento de CPU del sistema.

## <a name="supported-azure-regions"></a>Regiones de Azure compatibles
Service Map actualmente está disponible en las siguientes regiones de Azure:
- Este de EE. UU.
- Europa occidental
- Centro occidental de EE.UU.
- Sudeste asiático


## <a name="supported-operating-systems"></a>Sistemas operativos compatibles
Las secciones siguientes enumeran los sistemas operativos compatibles para el agente de dependencia. Service Map no admite arquitecturas de 32 bits para ningún sistema operativo.

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Escritorio de Windows
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux y Oracle Linux (con kernel RHEL)
- Se admiten solo versiones de kernel SMP Linux y predeterminados.
- Las versiones de kernel no estándar, como PAE y Xen, no son compatibles con ninguna distribución de Linux. Por ejemplo, un sistema con la cadena de versión de "2.6.16.21-0.8-xen" no es compatible.
- No se admiten los kernel personalizados, incluidas las recompilaciones de kernels estándar.
- No se admite el kernel de CentOSPlus.
- Unbreakable Enterprise Kernel (UEK) de Oracle se aborda en una sección posterior de este artículo.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Versión del SO | Versión del kernel |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| Versión del SO | Versión del kernel |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4. | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| Versión del SO | Versión del kernel |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417<br>2.6.18-419<br>2.6.18-420 |

### <a name="ubuntu-server"></a>Ubuntu Server
- No se admiten los kernel personalizados, incluidas las recompilaciones de kernels estándar.

| Versión del SO | Versión del kernel |
|:--|:--|
| 16.04 | 4.4.0-103<br>4.11.0-1016 |
| 14.04 | 3.13.0-137<br>4.4.0-103 |

### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux con Unbreakable Enterprise Kernel
#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Versión del SO | Versión del kernel
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4. | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Versión del SO | Versión del kernel
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Versión del SO | Versión del kernel
|:--|:--|
| 11 SP2 | 3.0.101-0.7 |
| 11 SP3 | 3.0.101-0.47 |
| 11 SP4 | 3.0.101-65 |


## <a name="diagnostic-and-usage-data"></a>Datos de diagnóstico y uso
Microsoft recopila automáticamente datos de uso y rendimiento a través del servicio Mapa de servicio. Microsoft usa estos datos para proporcionar calidad, seguridad e integridad en el servicio Service Map y para mejorarlas. Los datos incluyen información sobre la configuración del software, como el sistema operativo y la versión. También incluye dirección IP, nombre DNS y nombre de estación de trabajo para proporcionar funcionalidades precisas y eficaces de solución de problemas. No recopilamos los nombres, las direcciones ni otra información de contacto.

Para más información sobre la recopilación de datos y su utilización, consulte [Declaración de privacidad de Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>pasos siguientes
- Obtenga información sobre cómo [usar Service Map](operations-management-suite-service-map.md) una vez implementado y configurado.
