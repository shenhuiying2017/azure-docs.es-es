---
title: "Solución Datos de conexión en Log Analytics | Microsoft Docs"
description: "Datos de conexión son datos consolidados de red y rendimiento de los equipos con agentes de OMS, como agentes conectados con Windows y Operations Manager. Los datos de red se combinan con los datos de registro para ayudar a correlacionar datos."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: banders
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: a552bbce2c6a1b60ab73e3e945985ff3dccbc93a
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="wire-data-20-preview-solution-in-log-analytics"></a>Solución Wire Data 2.0 (versión preliminar) en Log Analytics

![Símbolo de Wire Data](./media/log-analytics-wire-data/wire-data2-symbol.png)

Los datos de conexión son datos consolidados de red y rendimiento de los equipos con agentes de OMS, incluidos los agentes conectados con Operations Manager, Windows y Linux. Los datos de red se combinan con otros datos de registro para ayudar a correlacionar datos.

Además de los agentes de OMS, la solución Wire Data usa los agentes de dependencia de Microsoft que instale en los equipos de su infraestructura de TI. Los agentes de dependencia supervisan los datos de red enviados a los equipos y desde estos para los niveles 2 y 3 de la red en el [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), incluidos los distintos protocolos y puertos que se usan. Después, los datos se envían a Log Analytics mediante agentes.

> [!NOTE]
> No se puede agregar la versión anterior de la solución Wire Data a nuevas áreas de trabajo. Si tiene la solución Wire Data original habilitada pueden continuar usándola. Pero para usar Wire Data 2.0, primero debe quitar la versión original.

De forma predeterminada, Log Analytics recopila datos registrados de CPU, memoria, disco y los datos de rendimiento de red de contadores integrados en Windows. La recopilación de datos de red y de otros datos se realiza en tiempo real para cada agente, incluidas las subredes y los protocolos de nivel de aplicación utilizados por el equipo. Puede agregar otros contadores de rendimiento en la página Configuración de la pestaña Registros.

Si ha usado [sFlow](http://www.sflow.org/) u otro software con el [protocolo NetFlow de Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), las estadísticas y los datos que ve de los datos de conexión le resultarán familiares.

Algunos de los tipos de consultas de búsqueda de registros integradas son:

- Agentes que proporcionan datos de conexión
- Dirección IP de los agentes que proporcionan datos de conexión
- Comunicaciones salientes por direcciones IP
- Número de bytes enviados por protocolos de aplicación
- Número de bytes enviados por un servicio de aplicación
- Bytes recibidos por distintos protocolos
- Número total de bytes enviados y recibidos por versión IP
- Promedio de latencia de las conexiones que se midieron de forma fiable
- Procesos de equipo que iniciaron o recibieron tráfico de red
- Cantidad de tráfico de red de un proceso

Cuando realice una búsqueda con datos de conexión, puede filtrar y agrupar datos para ver información sobre los principales agentes y protocolos. O puede ver cuándo determinados equipos (direcciones IP y MAC) se comunicaron entre sí, durante cuánto tiempo y cuántos datos se enviaron (básicamente, verá metadatos sobre el tráfico de red, que se basan en la búsqueda).

Pero como está viendo los metadatos, no son necesariamente útiles para la solución de problemas detallada. Los datos de conexión de Log Analytics no son una captura completa de los datos de red. Por tanto, no están diseñados para la solución de problemas profunda en el nivel de paquete. La ventaja de usar el agente, en comparación con otros métodos de recopilación, es que no tiene que instalar dispositivos, volver a configurar los conmutadores de red o realizar configuraciones complicadas. Los datos de conexión simplemente se basan en agente: instala el agente en un equipo y supervisará su propio tráfico de red. Otra ventaja es cuando quiere supervisar cargas de trabajo que se ejecutan en proveedores de nube o que hospedan el proveedor de servicio o Microsoft Azure, donde el usuario no tiene la capa de tejido.

## <a name="connected-sources"></a>Orígenes conectados

Wire Data obtiene sus datos de Microsoft Dependency Agent. Dependency Agent depende del agente de OMS en lo que respecta a las conexiones a Log Analytics. Es decir, en primer lugar, un servidor debe tener instalado y configurado el agente de OMS y, luego, se puede instalar Dependency Agent. En la tabla siguiente se describen los orígenes conectados que son compatibles con la solución Wire Data.

| **Origen conectado** | **Compatible** | **Descripción** |
| --- | --- | --- |
| Agentes de Windows | Sí | Wire Data analiza y recopila datos de equipos del agente de Windows. <br><br> Además del [agente de OMS](log-analytics-windows-agents.md), los agentes de Windows requieren el agente de dependencia de Microsoft. Consulte los [sistemas operativos compatibles](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) para obtener una lista completa de las versiones de sistema operativo. |
| Agentes de Linux | Sí | Wire Data analiza y recopila datos de equipos del agente de Linux.<br><br> Además del [agente de OMS](log-analytics-linux-agents.md), los agentes de Linux requieren Microsoft Dependency Agent. Consulte los [sistemas operativos compatibles](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems) para obtener una lista completa de las versiones de sistema operativo. |
| Grupo de administración de System Center Operations Manager | Sí | Wire Data analiza y recopila datos de los agentes de Windows y Linux en un [grupo de administración de System Center Operations Manager](log-analytics-om-agents.md) conectado. <br><br> Se requiere una conexión directa desde el equipo agente de System Center Operations Manager a Log Analytics. Los datos se reenvían desde el grupo de administración a Log Analytics. |
| Cuenta de almacenamiento de Azure | No | Wire Data recopila datos de equipos de agente, por lo que no hay ningún dato en él que recopilar desde Azure Storage. |

En Windows, tanto System Center Operations Manager como Log Analytics usan Microsoft Monitoring Agent (MMA) para recopilar y enviar los datos. Dependiendo del contexto, el agente se denomina agente de System Center Operations Manager, agente de OMS, agente de Log Analytics, MMA o agente directo. System Center Operations Manager y Log Analytics proporcionan versiones ligeramente diferentes de MMA. Cada una de estas versiones puede informar a System Center Operations Manager, a Log Analytics o a ambos.

En Linux, el agente OMS para Linux recopila y envía datos de supervisión a Log Analytics. Puede usar Wire Data en servidores con agentes directos de OMS o en servidores que estén conectados a Log Analytics a través de los grupos de administración de System Center Operations Manager.

En este artículo se hace referencia a todos los agentes como el _agente de OMS_, ya sean de Linux o Windows, y estén conectados a un grupo de administración de System Center Operations Manager o directamente a Log Analytics. Usaremos el nombre de implementación específico del agente solo si es necesario en contexto.

Dependency Agent no transmite los datos por sí mismo y no requiere ningún cambio en los firewalls o puertos. Los datos de Wire Data siempre se transmiten mediante el agente de OMS a Log Analytics, directamente o a través de la puerta de enlace de OMS.

![diagrama de agente](./media/log-analytics-wire-data/agents.png)

Si es un usuario de System Center Operations Manager con un grupo de administración conectado a Log Analytics:

- No se requiere ninguna configuración adicional cuando los agentes de System Center Operations Manager pueden obtener acceso a Internet para conectarse a Log Analytics.
- Si los agentes de System Center Operations Manager no pueden obtener acceso a Log Analytics a través de Internet, es necesario configurar la puerta de enlace de OMS para que funcione con System Center Operations Manager.

Si usa el agente directo, debe configurar el agente de OMS para conectarse a Log Analytics o a la puerta de enlace de OMS. Puede descargar la nueva versión de la puerta de enlace de OMS en el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Requisitos previos

- Se requiere la oferta de la solución [Insight and Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing).
- Si usa la versión anterior de la solución Wire Data, primero debe quitarla. Pero todos los datos capturados a través de la solución Wire Data original siguen estando disponibles en Wire Data 2.0 y la búsqueda de registros.
- Se requieren privilegios de administrador para instalar o desinstalar Dependency Agent.
- Se debe instalar Dependency Agent en un equipo con un sistema operativo de 64 bits.

### <a name="operating-systems"></a>Sistemas operativos

Las secciones siguientes enumeran los sistemas operativos compatibles para el agente de dependencia. Wire Data no admite arquitecturas de 32 bits para ningún sistema operativo.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Escritorio de Windows

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux y Oracle Linux (con kernel RHEL)

- Se admiten solo versiones de kernel SMP Linux y predeterminados.
- Las versiones de kernel no estándar, como PAE y Xen, no son compatibles con ninguna distribución de Linux. Por ejemplo, un sistema con la cadena de versión de _2.6.16.21-0.8-xen_ no es compatible.
- No se admiten los kernel personalizados, incluidas las recompilaciones de kernels estándar.
- No se admite el kernel de CentOSPlus.
- Unbreakable Enterprise Kernel (UEK) de Oracle se aborda en una sección posterior de este artículo.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| **Versión del sistema operativo** | **Versión de kernel** |
| --- | --- |
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| **Versión del sistema operativo** | **Versión de kernel** |
| --- | --- |
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4. | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5

| **Versión del sistema operativo** | **Versión de kernel** |
| --- | --- |
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398 <br> 2.6.18-400 <br>2.6.18-402 <br>2.6.18-404 <br>2.6.18-406 <br> 2.6.18-407 <br> 2.6.18-408 <br> 2.6.18-409 <br> 2.6.18-410 <br> 2.6.18-411 <br> 2.6.18-412 <br> 2.6.18-416 <br> 2.6.18-417 <br> 2.6.18-419 |

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux con Unbreakable Enterprise Kernel

#### <a name="oracle-linux-6"></a>Oracle Linux 6

| **Versión del sistema operativo** | **Versión de kernel** |
| --- | --- |
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4. | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| **Versión del sistema operativo** | **Versión de kernel** |
| --- | --- |
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11

| **Versión del sistema operativo** | **Versión de kernel** |
| --- | --- |
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10

| **Versión del sistema operativo** | **Versión de kernel** |
| --- | --- |
| 10 SP4 | 2.6.16.60 |

#### <a name="dependency-agent-downloads"></a>Descargas de Dependency Agent

| **Archivo** | **SISTEMA OPERATIVO** | **Versión** | **SHA-256** |
| --- | --- | --- | --- |
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |



## <a name="configuration"></a>Configuración

Siga los pasos siguientes para configurar la solución Wire Data para las áreas de trabajo.

1. Habilite la solución Activity Log Analytics desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) o mediante el proceso descrito en [Add Log Analytics solutions from the Solutions Gallery (Agregar soluciones Log Analytics desde la Galería de soluciones)](log-analytics-add-solutions.md).
2. Instale Dependency Agent en cada equipo donde quiere obtener datos. Dependency Agent puede supervisar las conexiones a los vecinos inmediatos, por lo que es posible que no necesite un agente en cada equipo.

### <a name="install-the-dependency-agent-on-windows"></a>Instalar Dependency Agent en Windows

Se requieren privilegios de administrador para instalar o desinstalar al agente.

Dependency Agent se instala en equipos que ejecutan Windows mediante InstallDependencyAgent-Windows.exe. Si ejecuta este archivo ejecutable sin opciones, se inicia un asistente que puede seguir para realizar la instalación de forma interactiva.

Use los pasos siguientes para instalar Dependency Agent en cada equipo que ejecute Windows:

1. Siga las instrucciones que aparecen en [Conexión de equipos Windows al servicio Log Analytics de Azure](log-analytics-windows-agents.md) para instalar el agente de OMS.
2. Descargue el agente de Windows mediante el vínculo de la sección anterior y, después, ejecútelo mediante el comando siguiente: InstallDependencyAgent-Windows.exe
3. Siga el asistente para instalar el agente.
4. Si el agente de dependencia no se inicia, compruebe los registros para obtener información detallada del error. En los agentes de Windows, el directorio de registro es %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Línea de comandos de Windows

Use las opciones de la tabla siguiente para realizar la instalación desde una línea de comandos. Para ver una lista de marcas de instalación, ejecute el programa de instalación con la marca /? de la manera siguiente.

InstallDependencyAgent-Windows.exe /?

| **Marca** | **Descripción** |
| --- | --- |
| <code>/?</code> | Obtenga una lista de las opciones de la línea de comandos. |
| <code>/S</code> | Realice una instalación silenciosa sin preguntas. |

Los archivos para el agente de dependencia de Windows se ubican en C:\Archivos de programa\Microsoft Dependency Agent de manera predeterminada.

### <a name="install-the-dependency-agent-on-linux"></a>Instalar Dependency Agent en Linux

Es necesario el acceso raíz para instalar o configurar el agente.

Dependency Agent se instala en equipos Linux con InstallDependencyAgent-Linux64.bin, un script de shell con un archivo binario autoextraíble. Puede ejecutar el archivo con _sh_ o agregar permisos de ejecución al propio archivo.

Utilice los pasos siguientes para instalar el agente de dependencia en cada equipo de Linux:

1. Use las instrucciones que aparecen en [Recopilación y administración de datos de equipos Linux](log-analytics-agent-linux.md) para instalar el agente de OMS.
2. Descargue Dependency Agent de Linux mediante el vínculo de la sección anterior y, después, instálelo como raíz mediante el comando siguiente: sh InstallDependencyAgent-Linux64.bin
3. Si el agente de dependencia no se inicia, compruebe los registros para obtener información detallada del error. En los agentes de Linux, el directorio de registro es: /var/opt/microsoft/dependency-agent/log.

Para ver una lista de marcas de instalación, ejecute el programa de instalación con la marca `-help` de la siguiente forma.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Marca** | **Descripción** |
| --- | --- |
| <code>-help</code> | Obtenga una lista de las opciones de la línea de comandos. |
| <code>-s</code> | Realice una instalación silenciosa sin preguntas. |
| <code>--check</code> | Compruebe los permisos y el sistema operativo, pero no instale el agente. |

Los archivos del agente de dependencia se colocan en los directorios siguientes:

| **Archivos** | **Ubicación** |
| --- | --- |
| Archivos principales | /opt/microsoft/dependency-agent |
| Archivos de registro | /var/opt/microsoft/dependency-agent/log |
| Archivos de configuración | /etc/opt/microsoft/dependency-agent/config |
| Archivos ejecutables de servicio | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Archivos binarios de almacenamiento | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Ejemplos de script de instalación

Para implementar fácilmente Dependency Agent en muchos servidores a la vez, resulta útil usar un script. Puede usar los siguientes ejemplos de script para descargar e instalar Dependency Agent en Windows o Linux.

#### <a name="powershell-script-for-windows"></a>Script de PowerShell para Windows

```PowerShell

Invoke-WebRequest &quot;https://aka.ms/dependencyagentwindows&quot; -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Script de shell para Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Configuración de estado deseada

Para implementar Dependency Agent a través de Desired State Configuration, puede usar el módulo xPSDesiredStateConfiguration y un fragmento de código similar al siguiente:

```
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = &quot;C:\InstallDependencyAgent-Windows.exe&quot;



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = &quot;https://aka.ms/dependencyagentwindows&quot;

        DestinationPath = $DAPackageLocalPath

        DependsOn = &quot;[Package]OI&quot;

    }

    xPackage DA

    {

        Ensure=&quot;Present&quot;

        Name = &quot;Dependency Agent&quot;

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = &quot;&quot;

        InstalledCheckRegKey = &quot;HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent&quot;

        InstalledCheckRegValueName = &quot;DisplayName&quot;

        InstalledCheckRegValueData = &quot;Dependency Agent&quot;

    }

}

```
### <a name="uninstall-the-dependency-agent"></a>Desinstalar Dependency Agent

Use las secciones siguientes para quitar Dependency Agent.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Desinstalar Dependency Agent en Windows

Un administrador puede desinstalar Dependency Agent de Windows a través del Panel de control.

Un administrador también puede ejecutar %Programfiles%\Microsoft Dependency Agent\Uninstall.exe para desinstalar Dependency Agent.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Desinstalar Dependency Agent en Linux

Para desinstalar completamente Dependency Agent de Linux, debe quitar el agente y el conector que se instala automáticamente con el agente. Puede desinstalar ambos mediante el comando siguiente:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Módulos de administración

Cuando se activa Wire Data en un área de trabajo de Log Analytics, se envía un módulo de administración de 300 KB a todos los servidores Windows en esa área de trabajo. Si usa agentes de System Center Operations Manager en un [grupo de administración conectado](log-analytics-om-agents.md), el módulo de administración de Dependency Monitor se implementa desde System Center Operations Manager. Si los agentes se conectan directamente, Log Analytics ofrece el módulo de administración.

El módulo de administración se denomina Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Se escribe en: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. El origen de datos que el módulo de administración usa es: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Uso de la solución

**Instalación y configuración de la solución**

Utilice la siguiente información para instalar y configurar la solución.

- La solución Datos de conexión adquiere datos desde equipos que ejecutan Windows Server 2012 R2, Windows 8.1 y sistemas operativos posteriores.
- Se requiere Microsoft .NET Framework 4.0 o posterior en equipos de los que desea adquirir datos de conexión.
- Agregue la solución Wire Data al área de trabajo de Log Analytics mediante el proceso descrito en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md). No es necesario realizar ninguna configuración más.
- Si quiere ver los datos de conexión de una solución específica, debe tener la solución ya agregada a su área de trabajo.

Después de instalar los agentes y la solución, el icono de Wire Data 2.0 aparece en el área de trabajo.

> [!NOTE]
> Actualmente, debe usar el portal de OMS para ver los datos de conexión. No se puede usar Azure Portal para ver los datos de conexión.

![Icono de Wire Data](./media/log-analytics-wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Uso de la solución Wire Data 2.0

En el portal de OMS, haga clic en el icono **Wire Data 2.0** para abrir el panel de Wire Data. El panel incluye las hojas de la tabla siguiente. Cada hoja muestra hasta diez elementos que coinciden con los criterios de esa hoja para el ámbito e intervalo de tiempo especificados. Puede ejecutar una búsqueda de registros que devuelva todos los registros si hace clic en **Ver todo** en la parte inferior de la hoja o si hace clic en el encabezado de esta.

| **Hoja** | **Descripción** |
| --- | --- |
| Agentes que capturan el tráfico de red | Muestra el número de agentes que capturan el tráfico de red y enumera los 10 equipos principales que capturan el tráfico. Haga clic en el número para ejecutar una búsqueda de registros de <code>Type:WireData &#124; measure Sum(TotalBytes) by Computer &#124; top 500000</code>. Haga clic en un equipo de la lista para ejecutar una búsqueda de registros que devuelve el número total de bytes capturados. |
| Subredes locales | Muestra el número de subredes locales que los agentes han detectado.  Haga clic en el número para ejecutar una búsqueda de registros para <code>Type:WireData &#124; Measure Sum(TotalBytes) by LocalSubnet</code> que enumera todas las subredes con el número de bytes enviados a través de cada una de ellas. Haga clic en una subred de la lista para ejecutar una búsqueda de registros que devuelve el número total de bytes enviados sobre la subred. |
| Protocolos de nivel de aplicación | Muestra el número de protocolos de nivel de aplicación en uso detectados por los agentes. Haga clic en el número para ejecutar una búsqueda de registros de <code>Type:WireData &#124; Measure Sum(TotalBytes) by ApplicationProtocol</code>. Haga clic en un protocolo para ejecutar una búsqueda de registros que devuelve el número total de bytes enviados mediante el protocolo. |

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Panel de Wire Data](./media/log-analytics-wire-data/wire-data-dash.png)

Puede usar la hoja **Agentes que capturan el tráfico de red** para determinar la cantidad de ancho de banda de red que usan los equipos. Esta hoja puede ayudar a encontrar fácilmente el equipo _que emite más información_ en su entorno. Estos equipos se pueden sobrecargar, actuar de forma anómala o usar más recursos de red de lo normal.

![ejemplo de búsqueda de registros](./media/log-analytics-wire-data/log-search-example01.png)

De forma similar, puede usar la hoja **Subredes locales** para determinar cuánto tráfico de red se está moviendo a través de las subredes. A menudo, los usuarios definen subredes en torno a áreas cruciales de sus aplicaciones. En esta hoja se ofrece una vista de esas áreas.

![ejemplo de búsqueda de registros](./media/log-analytics-wire-data/log-search-example02.png)

La hoja **Protocolos en el nivel de la aplicación** es útil porque ayuda a saber qué protocolos están en uso. Por ejemplo, es posible que espere que SSH no esté en uso en su entorno de red. Ver la información disponible en la hoja permite confirmar o desmentir rápidamente las expectativas.

![ejemplo de búsqueda de registros](./media/log-analytics-wire-data/log-search-example03.png)

En este ejemplo, se puede profundizar en los detalles de SSH para ver qué equipos usan SSH y muchos otros detalles de comunicación.

![resultados de la búsqueda de sh](./media/log-analytics-wire-data/ssh-details.png)

También es útil saber si el tráfico de protocolo aumenta o disminuye con el tiempo. Por ejemplo, si está aumentando la cantidad de datos que transmite una aplicación, es posible que se trate de algo que deba tener en cuenta o que le resulte de interés.

## <a name="input-data"></a>Datos de entrada

La solución Datos de conexión recopila metadatos sobre el tráfico de red con los agentes habilitados. Cada agente envía datos cada 15 segundos aproximadamente.

## <a name="output-data"></a>Datos de salida

Se crea un registro con un tipo de _WireData_ para cada tipo de datos de entrada. Los registros de WireData tienen las propiedades mostradas en la tabla siguiente:

| Propiedad | Descripción |
|---|---|
| Equipo | Nombre de equipo del que se recopilan los datos |
| TimeGenerated | Hora del registro |
| LocalIP | Dirección IP del equipo local |
| SessionState | Conectado o desconectado |
| ReceivedBytes | Cantidad de bytes recibidos |
| ProtocolName | Nombre del protocolo de red que se usa |
| IPVersion | Versión de la dirección IP |
| Dirección | De entrada o de salida |
| MaliciousIP | Dirección IP de un origen malintencionado conocido |
| Gravedad | Gravedad del supuesto malware |
| RemoteIPCountry | País de la dirección IP remota |
| ManagementGroupName | Nombre del grupo de administración de Operations Manager |
| SourceSystem | Origen del que se recopilan los datos |
| SessionStartTime | Hora de inicio de la sesión |
| SessionEndTime | Hora de finalización de la sesión |
| LocalSubnet | Subred de la que se recopilan los datos |
| LocalPortNumber | Número de puerto local |
| RemoteIP | Dirección IP remota que usa el equipo remoto |
| RemotePortNumber | Número de puerto usado por la dirección IP remota |
| SessionID | Un valor único que identifica la sesión de comunicaciones entre dos direcciones IP |
| SentBytes | Número de bytes enviados |
| TotalBytes | Número total de bytes enviados durante la sesión |
| ApplicationProtocol | Tipo del protocolo de red que se usa   |
| ProcessID | Id. de proceso de Windows |
| ProcessName | Nombre de archivo y ruta de acceso del proceso |
| RemoteIPLongitude | Valor de longitud IP |
| RemoteIPLatitude | Valor de latitud IP |


## <a name="next-steps"></a>Pasos siguientes

- [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para más información sobre cómo ver registros de búsqueda de datos de conexión detallados.

