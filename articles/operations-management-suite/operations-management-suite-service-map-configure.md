---
title: "Configuración de la solución Mapa de servicio de Operations Management Suite (OMS) | Microsoft Docs"
description: "Mapa de servicio es una solución de Operations Management Suite (OMS) que detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios.  En este artículo se proporciona información para implementar la solución Mapa de servicio en su entorno y utilizarla en distintos escenarios."
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
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 3297fec53e97c33461b7fa5bfd9e813c30a3da9d
ms.lasthandoff: 04/17/2017


---
# <a name="configuring-service-map-solution-in-operations-management-suite-oms"></a>Configuración de la solución Mapa de servicio de Operations Management Suite (OMS)
Mapa de servicio detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux y asigna la comunicación entre servicios. Permite ver los servidores a medida que piensa en ellos, como los sistemas interconectados que ofrecen servicios críticos.  Mapa de servicio muestra las conexiones entre servidores, procesos y puertos en cualquier arquitectura conectada TCP sin una configuración necesaria que sea distinta a la instalación de un agente.

En este artículo se describen los detalles sobre cómo configurar agentes de incorporación y de Mapa de servicio.  Para información sobre cómo utilizar Mapa de servicio, consulte [Uso de la solución Mapa de servicio de Operations Management Suite (OMS)](operations-management-suite-service-map.md)

## <a name="dependency-agent-downloads"></a>Descargas de Dependency Agent
| Archivo | SO | Versión | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.4 | EF9BF7106BD6856384CDAF704FDE5872DD0A6D0C3BE26F62EAB17BA5D50702DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.4 | D685A2BAEEE30151EAD074F2BF5774F32446EAAB89B0FC04DC36D24A8A6397AC |


## <a name="connected-sources"></a>Orígenes conectados
Mapa de servicio obtiene sus datos de Microsoft Dependency Agent.  Dependency Agent depende del agente de OMS en lo que respecta a las conexiones a OMS.  Es decir, en primer lugar, un servidor debe tener instalado y configurado el agente de OMS y, luego, se puede instalar Dependency Agent.  En la tabla siguiente se describen los orígenes conectados que son compatibles con la solución Mapa de servicio.

| Origen conectado | Compatible | Description |
|:--|:--|:--|
| Agentes de Windows | Sí | Mapa de servicio analiza y recopila datos de equipos del agente de Windows.  <br><br>Además del [agente de OMS](../log-analytics/log-analytics-windows-agents.md), los agentes de Windows requieren el agente de dependencia de Microsoft.  Consulte los [sistemas operativos compatibles](#supported-operating-systems) para obtener una lista completa de las versiones de sistema operativo. |
| Agentes de Linux | Sí | Mapa de servicio analiza y recopila datos de equipos de agente de Linux.  <br><br>Además del [agente de OMS](../log-analytics/log-analytics-linux-agents.md), los agentes de Linux requieren Microsoft Dependency Agent.  Consulte los [sistemas operativos compatibles](#supported-operating-systems) para obtener una lista completa de las versiones de sistema operativo. |
| Grupo de administración de SCOM | Sí | Mapa de servicio analiza y recopila datos de los agentes de Windows y Linux en un [grupo de administración de System Center Operations Manager (SCOM)](../log-analytics/log-analytics-om-agents.md) conectado. <br><br>No se requiere una conexión directa entre el equipo agente de SCOM y OMS. Los datos se reeenvían directamente del grupo de administración al repositorio de OMS.|
| Cuenta de almacenamiento de Azure | No | Mapa de servicio recopila datos de equipos agentes, así que no hay ningún dato en él que recopilar de Azure Storage. |

Mapa de servicio solo es compatible con plataformas de 64 bits.

En Windows, Microsoft Monitoring Agent (MMA) utiliza SCOM y OMS para recopilar y enviar datos de supervisión.  (Este agente se denomina agente SCOM, Agente OMS, MMA o agente directo, según el contexto).  SCOM y OMS proporcionan diferentes versiones fáciles de usar de MMA, pero estas versiones pueden informara SCOM, OMS o a ambos.  En Linux, el agente OMS para Linux recopila y envía datos de supervisión a OMS.  Puede usar Mapa de servicio en servidores con agentes directos de OMS o en servidores que están conectados a OMS a través de grupos de administración de SCOM.  En esta documentación, nos referiremos a todos estos agentes, ya sea en Linux o Windows, conectado a un MG de SCOM o directamente a OMS: como "agente de OMS", a menos que el nombre de la implementación específica del agente sea necesario para el contexto.

El agente de Mapa de servicio no transmite los datos en sí y no requiere ningún cambio en los firewalls o puertos.  Los datos de Mapa de servicio siempre se transmiten mediante el agente de OMS a OMS, directamente o a través de la puerta de enlace de OMS.

![Agentes de Mapa de servicio](media/oms-service-map/agents.png)

Si es cliente SCOM con un grupo de administración conectado a OMS:

- Si los agentes SCOM pueden tener acceso a Internet para conectarse a OMS, no se requiere ninguna configuración adicional.  
- Si los agentes SCOM no pueden tener acceso a OMS a través de Internet, debe configurar la puerta de enlace de OMS para trabajar con SCOM.
  
Si utiliza el agente directo de OMS, debe configurar el agente de OMS para conectarse a OMS o a la puerta de enlace de OMS.  La puerta de enlace de OMS puede descargarse desde [https://www.microsoft.com/download/details.aspx?id=52666](https://www.microsoft.com/download/details.aspx?id=52666)


### <a name="avoiding-duplicate-data"></a>Evitar datos duplicados

Si es cliente de SCOM, no debe configurar los agentes SCOM para comunicarse directamente con OMS o se notificarán los datos dos veces.  En Mapa de servicio, esto provocará que los equipos aparezcan dos veces en la lista de máquinas.

La configuración de OMS debe producirse en una sola de las siguientes ubicaciones:

- El panel de Operations Management Suite para la consola de SCOM para equipos administrados
- Configuración de Azure Operational Insights en las propiedades MMA

El uso de ambas configuraciones con la *misma* área de trabajo en cada provocará la duplicación de datos. El uso de ambas configuraciones con *distintas* áreas de trabajo puede producir conflictos en la configuración (una con la solución Mapa de servicio habilitada y la otra sin habilitar) que puede impedir que los datos fluyan a Mapa de servicio completamente.  

Incluso si la máquina propiamente dicha no se especifica en la configuración de OMS de la consola SCOM, si un grupo de instancias como "Grupo de instancias de Windows Server" está activo, puede seguir provocando que la máquina reciba la configuración OMS a través de SCOM.



## <a name="management-packs"></a>Módulos de administración
Cuando se activa Mapa de servicio en un área de trabajo OMS, se envía un módulo de administración de 300 kB a todas las instancias de Microsoft Monitoring Agent en esa área de trabajo.  Si utiliza agentes SCOM en un [grupo de administración conectado](../log-analytics/log-analytics-om-agents.md), se implementará el módulo de administración de Mapa de servicio desde SCOM.  Si los agentes están conectados directamente, OMS entregará el módulo de administración.

El módulo de administración se denomina Microsoft.IntelligencePacks.ApplicationDependencyMonitor*.  Se escribe en *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\*.  El origen de datos que utiliza el módulo de administración es *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*.



## <a name="configuration"></a>Configuración
Además de equipos de Windows y Linux, tenga un agente instalado y conectado a OMS, el instalador del agente de dependencia debe descargarse de la solución de Mapa de servicio y, a continuación, instalarse como administrador o raíz en cada servidor administrado.  Una vez instalado el agente de Mapa de servicio en un servidor que informe a OMS, los mapas de dependencia de Mapa de servicio aparecerán en 10 minutos.


### <a name="migrating-from-bluestripe-factfinder"></a>Migración de BlueStripe FactFinder
Mapa de servicio proporcionará la tecnología BlueStripe de OMS en fases. FactFinder todavía se admite para los clientes existentes, pero ya no está disponible para su compra individual.  Esta versión de vista previa del agente de dependencia solo puede comunicarse con OMS.  Si es un cliente actual de FactFinder, identifique un conjunto de servidores de prueba de Mapa de servicio que no esté administrado por FactFinder. 

### <a name="download-the-dependency-agent"></a>Descarga del agente de dependencia
Además de Microsoft Management Agent (MMA) y el agente de Linux de OMS que proporcione la conexión entre el equipo y OMS, todos los equipos analizados por Mapa de servicio deben tener instalado el agente de dependencia.  En Linux, el agente de OMS para Linux debe instalarse antes que el agente de dependencia. 

![Icono de Mapa de servicio](media/oms-service-map/additional_configuration.png)

Para descargar el agente de dependencia, haga clic en **Configurar solución** en el icono de **Mapa de servicio** para abrir la hoja del **agente de dependencia**.  La hoja del agente de dependencia tiene vínculos para Windows y los agentes de Linux. Consulte las siguientes secciones para obtener más información acerca de cómo instalar el agente en sistemas diferentes.

### <a name="install-the-dependency-agent"></a>Instalar el agente de dependencia

#### <a name="microsoft-windows"></a>Microsoft Windows
Se requieren privilegios de administrador para instalar o desinstalar al agente.

El agente de dependencia se instala en equipos de Windows con InstallDependencyAgent-Windows.exe. Si ejecuta este ejecutable sin opciones, se iniciará a un asistente que puede seguir para realizar la instalación de forma interactiva.  

Utilice los pasos siguientes para instalar el agente de dependencia en cada equipo de Windows:

1.    Asegúrese de que está instalado el agente de OMS mediante las instrucciones en la conexión de equipos directamente a OMS.
2.    Descargue al agente de Windows y ejecútelo con el siguiente comando: <br>*InstallDependencyAgent-Windows.exe*
3.    Siga el asistente para instalar el agente.
4.    Si el agente de dependencia no se inicia, compruebe los registros para obtener información detallada del error. En los agentes de Windows, el directorio de registro es *C:\Program Files\Microsoft Dependency Agent\logs*. 

Un agente de dependencia de Windows se puede desinstalar mediante un administrador a través del Panel de control.


#### <a name="linux"></a>Linux
Es necesario el acceso raíz para instalar o configurar el agente.

El agente de dependencia se instala en equipos de Linux con InstallDependencyAgent-Linux64.bin, un script de shell con un archivo binario autoextraíble. Puede ejecutar el archivo con sh o agregar permisos de ejecución al propio archivo.
 
Utilice los pasos siguientes para instalar el agente de dependencia en cada equipo de Linux:

1.    Asegúrese de que está instalado el agente de OMS mediante las instrucciones en [Recopilación y administración de datos de equipos de Linux.  El agente de OMS debe instalarse antes que el agente de dependencia de Linux](https://technet.microsoft.com/library/mt622052.aspx).
2.    Instale el agente de dependencia de Linux como raíz mediante el siguiente comando:<br>*sh InstallDependencyAgent-Linux64.bin*.
3.    Si el agente de dependencia no se inicia, compruebe los registros para obtener información detallada del error. En los agentes de Linux, el directorio de registro es */var/opt/microsoft/dependency-agent/log*.

### <a name="uninstalling-the-dependency-agent-on-linux"></a>Desinstalación del agente de dependencia en Linux
Para desinstalar completamente el agente de dependencia de Linux, debe quitar el agente y el conector que se instala automáticamente con el agente.  Puede desinstalar ambos con el siguiente comando único:

    rpm -e dependency-agent dependency-agent-connector


### <a name="installing-from-a-command-line"></a>Instalación desde una línea de comandos
La sección anterior proporciona orientación acerca de cómo instalar el agente de Dependency Monitor mediante las opciones predeterminadas.  Las secciones siguientes proporcionan instrucciones para instalar el agente desde una línea de comandos usando opciones personalizadas.

#### <a name="windows"></a>Windows
Use las opciones de la tabla siguiente para realizar la instalación desde una línea de comandos. Para ver una lista de marcas de instalación, ejecute el programa de instalación con la marca /? de la manera siguiente.

    InstallDependencyAgent-Windows.exe /?

| Marca | Descripción |
|:--|:--|
| /S | Realice una instalación silenciosa sin preguntas. |

Los archivos para el agente de dependencia de Microsoft se colocan en *C:\Program Files\Microsoft Dependency Agent* de forma predeterminada.


#### <a name="linux"></a>Linux
Use las opciones de la tabla siguiente para realizar la instalación. Para ver una lista de marcas de instalación, ejecute el programa de instalación con la marca -help de la siguiente forma.

    InstallDependencyAgent-Linux64.bin -help

| Marca | Descripción |
|:--|:--|
| -s | Realice una instalación silenciosa sin preguntas. |
| --check | Comprueba los permisos y el sistema operativo, pero no instala al agente. |

Los archivos del agente de dependencia se colocan en los directorios siguientes:

| Archivos | La ubicación |
|:--|:--|
| Archivos principales | /opt/microsoft/dependency-agent |
| Archivos de registro | /var/opt/microsoft/dependency-agent/log |
| Archivos de configuración | /etc/opt/microsoft/dependency-agent/config |
| Archivos ejecutables de servicio | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Archivos binarios de almacenamiento | /var/opt/microsoft/dependency-agent/storage |



## <a name="troubleshooting"></a>Solución de problemas
Si tiene algún problema al instalar o ejecutar Service Map (Mapa de servicios), esta sección puede ayudarle a ponerse a pleno funcionamiento.  Si el problema sigue sin poder resolverse, póngase en contacto con el soporte técnico de Microsoft.

### <a name="dependency-agent-installation-issues"></a>Problemas de instalación de Agente de dependencia
#### <a name="installer-asks-for-a-reboot"></a>El instalador solicita un reinicio
Agente de dependencia, *por lo general*, no requiere un reinicio durante su instalación o desinstalación.  Sin embargo, en algunos casos poco frecuentes, un servidor de Windows Server requerirá un reinicio para continuar con una instalación.  Esto ocurre cuando una dependencia, normalmente los redistribuibles de VC++ de Microsoft, requiere un reinicio debido a un archivo bloqueado.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber"></a>Mensaje "Unable to install Dependency Agent: Visual Studio Runtime libraries failed to install (code = [code_number])" (No se puede instalar Agente de dependencia: error al instalar las bibliotecas en tiempo de ejecución de Visual Studio (code = [code_number])".

Agente de dependencia de Microsoft se basa en las bibliotecas en tiempo de ejecución de Microsoft Visual Studio. Se encontró un problema al intentar instalar las bibliotecas. Los instaladores de la biblioteca en tiempo de ejecución crean registros en la carpeta %LOCALAPPDATA%\temp. El archivo será dd_vcredist_arch_yyyymmddhhmmss.log, donde arch será "x86" o "amd64" y yyyymmddhhmmss será la fecha y hora (reloj de 24 horas) en el momento de la creación del registro. El registro proporcionará detalles sobre el problema que bloquea la instalación.

Puede ser útil instalar uno mismo las [últimas bibliotecas en tiempo de ejecución](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) primero.

A continuación, verá algunos code_numbers y resoluciones sugeridas.

| Código | Descripción | Resolución |
|:--|:--|:--|
| 0 x 17 | El instalador de la biblioteca requiere una actualización de Windows que no se ha instalado. | Mire en el registro del instalador de la biblioteca más reciente (consulte más arriba).<br><br>Si una referencia a "Windows8.1-KB2999226-x64.msu" va seguida de una línea "Error 0x80240017: Failed to execute MSU package" (Error 0x80240017: No se pudo ejecutar el paquete MSU), no tiene instalados los requisitos previos necesarios para instalar KB2999226.  Siga las instrucciones de la sección de requisitos previos de https://support.microsoft.com/kb/2999226.  Tenga en cuenta que es posible que tenga que ejecutar Windows Update y reiniciar varias veces para instalar los requisitos previos necesarios.<br><br>Ejecute de nuevo el instalador de Agente de dependencia de Microsoft. |

### <a name="post-installation-issues"></a>Problemas posteriores a la instalación
#### <a name="server-doesnt-show-in-service-map"></a>El servidor no aparece en Service Map (Mapa de servicios)
Si su instalación de Agente de dependencia se ha realizado correctamente, pero no ve el servidor en la solución de Service Map (Mapa de servicios):
1. ¿Se ha instalado Agente de dependencia correctamente?  Para validarlo, compruebe si el servicio está instalado y se ejecuta.<br><br>
**Windows**: busque el servicio llamado "Agente de dependencia de Microsoft"<br>
**Linux**: busque el proceso llamado "microsoft-dependency-agent"

2. ¿Está en el [plan de tarifa gratuito de OMS o Log Analytics ](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)?  El plan gratuito permite hasta cinco servidores de Service Map (Mapa de servicios) únicos.  Ningún servidor posterior aparecerá en Service Map (Mapa de servicios), incluso si los cinco anteriores ya no envían datos.

3. ¿Envía su servidor datos de registro y rendimiento a OMS?  Vaya a la búsqueda de registros y ejecute la siguiente consulta para el equipo: 

        * Computer="<your computer name here>" | measure count() by Type
        
¿obtuvo una amplia variedad de eventos en los resultados?  ¿Son los datos recientes?  Si es así, el agente OMS funciona correctamente y se comunica con el servicio de OMS. Si no es así, compruebe el agente OMS del servidor: [Solución de problemas del agente OMS para Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).  [Solución de problemas del agente de OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md).

#### <a name="server-shows-in-service-map-but-has-no-processes"></a>El servidor aparece en Service Map (Mapa de servicios), pero no tiene procesos
Si ve el servidor en Service Map (Mapa de servicios), pero no tiene datos de proceso ni de conexión, quiere decir que Agente de dependencia está instalado y se ejecuta. Sin embargo, el controlador kernel no se ha cargado.  Para averiguar por qué no se ha cargado el controlador, compruebe el archivo wrapper.log (Windows) o service.log (Linux).  Las últimas líneas del archivo deben indicar por qué (p. ej., el kernel no se admite, lo que puede suceder en Linux si ha actualizado el kernel) no se ha cargado el kernel.

Windows: C:\Archivos de programa\Microsoft Dependency Agent\logs\wrapper.log

Linux: /var/opt/microsoft/dependency-agent/log/service.log

## <a name="data-collection"></a>Colección de datos
Puede esperar que cada agente transmita aproximadamente 25 MB por día, dependiendo de la complejidad de las dependencias del sistema.  Los datos de dependencia de Mapa de servicio se envían mediante cada agente cada 15 segundos.  

Normalmente, el agente de Mapa de servicio utiliza un 0,1 % de memoria del sistema y un 0,1 % de CPU del sistema.


## <a name="supported-operating-systems"></a>Sistemas operativos compatibles
Las secciones siguientes enumeran los sistemas operativos compatibles para el agente de dependencia.   No se admiten las arquitecturas de 32 bits para cualquier sistema operativo.

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
- Las versiones de kernel no estándar, como PAE y Xen, no son compatibles con cualquier distribución de Linux. Por ejemplo, un sistema con la cadena de versión de "2.6.16.21-0.8-xen" no es compatible.
- No se admiten los kernel personalizados, incluidas las recompilaciones de kernels estándar
- No se admite el kernel de CentOS Plus.
- Oracle Unbreakable Kernel (UEK) se trata de una sección diferente a continuación.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Versión del SO. | Versión de kernel |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| Versión del SO. | Versión de kernel |
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

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| Versión del SO. | Versión de kernel |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417<br>2.6.18-419 |

#### <a name="oracle-enterprise-linux-w-unbreakable-kernel-uek"></a>Oracle Enterprise Linux con Unbreakable Kernel (UEK)

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Versión del SO. | Versión de kernel
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4. | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Versión del SO. | Versión de kernel
|:--|:--|
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Versión del SO. | Versión de kernel
|:--|:--|
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| Versión del SO. | Versión de kernel
|:--|:--|
| 10 SP4 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>Datos de diagnóstico y uso
Microsoft recopila automáticamente datos de uso y rendimiento a través del servicio Mapa de servicio. Microsoft usa estos datos para proporcionar y mejorar la calidad, la seguridad y la integridad del servicio Mapa de servicio. Los datos incluyen información sobre la configuración del software como sistema operativo y la versión, y también incluyen la dirección IP, el nombre DNS y el nombre de la estación de trabajo con el fin de proporcionar funcionalidades de solución de problemas precisas y eficientes. No recopilamos los nombres, las direcciones ni otra información de contacto.

Para obtener más información sobre el uso y la recopilación de datos, consulte la [Declaración de privacidad de Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Pasos siguientes
- Obtenga información sobre cómo [usar Mapa de servicio](operations-management-suite-service-map.md) una vez que se ha implementado y configurado.

