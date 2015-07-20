<properties
   pageTitle="Configuraciones admitidas para Visión operativa"
   description="Obtenga información acerca de las configuraciones necesarias para obtener Visión operativa"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# Configuraciones admitidas para Visión operativa

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

¿Qué necesita para usar Visión operativa? Consulte la información siguiente para prepararse para Visión operativa.


## Configuración de System Center 2012 Operations Manager

Puede usar Visión operativa como un servicio adjunto en Operations Manager en System Center 2012 R2 o System Center 2012 SP1 R2. Esto le permite usar la consola de Operations en Operations Manager para ver las alertas de Visión operativa y la información de configuración. Cuando se usa Visión operativa como un servicio adjunto en Operations Manager, el agente se comunica directamente con el servidor de administración, que a su vez se comunica con el servicio de Visión operativa.

El uso de Visión operativa como un servicio adjunto tiene los siguientes requisitos previos:


- La integración entre System Center 2012 SP1 Operations Manager y Visión operativa requiere los módulos de administración actualizados que se incluyen en el [Conector de Visión operativa para Operations Manager](https://www.microsoft.com/es-es/download/details.aspx?id=38199). Puede descargar e instalar los módulos de administración del [Conector de Visión operativa para Operations Manager](https://www.microsoft.com/es-es/download/details.aspx?id=38199).

- System Center 2012 SP1: paquete acumulativo de actualizaciones 6 de Operations Manager, aunque es preferible el paquete acumulativo de actualizaciones 7. Esta actualización debe aplicarse al servidor de administración, a los agentes y a la consola de Operations para Visión operativa como un escenario de servicio adjunto.

- System Center 2012 R2: paquete acumulativo de actualizaciones 2 de Operations Manager, aunque es preferible el paquete acumulativo de actualizaciones 3. Esta actualización debe aplicarse al servidor de administración, a los agentes y a la consola de Operations para Visión operativa como un escenario de servicio adjunto.

- Para ver los datos de administración de capacidad, debe habilitar la conectividad de Operations Manager con Virtual Machine Manager (VMM). Para obtener información adicional acerca de la conexión de los sistemas, consulte [Conexión de VMM con Operations Manager](https://technet.microsoft.com/es-es/library/hh882396.aspx).

Consulte [Visualización de alertas de Visión operativa](http://go.microsoft.com/fwlink/?LinkID=293793) para obtener instrucciones de instalación y configuración.

Si quiere ver las alertas de Visión operativa acerca de SharePoint Server 2010, Lync Server 2013, Lync Server 2010 o System Center 2012 SP1 - Virtual Machine Manager, deberá configurar una cuenta de ejecución para esas cargas de trabajo. Consulte [Consideraciones sobre Operations Manager con Visión operativa](operational-insights-operations-manager.md) para obtener más información sobre la configuración de las cuentas de ejecución.


### Sistemas operativos de Operations Manager

Los agentes de Operations Manager se admiten en diversos equipos. Consulte [Requisitos del sistema: System Center 2012 R2 Operations Manager](https://technet.microsoft.com/library/dn249696.aspx) para obtener más información acerca de la compatibilidad con el agente.

### Software necesario para Operations Manager

Para ver los datos de administración de capacidad, debe habilitar la conectividad de Operations Manager con VMM. Para obtener información adicional acerca de la conexión de los sistemas, consulte [Conexión de VMM con Operations Manager](https://technet.microsoft.com/es-es/library/hh882396.aspx).

## Agentes conectados directamente a Visión operativa

El agente usado para conectar directamente con el servicio es el agente de Microsoft Monitoring. Los requisitos del sistema se muestran en la página [Centro de descarga de Microsoft](https://www.microsoft.com/es-es/download/details.aspx?id=40316&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True).

## Exploradores

Puede usar cualquiera de los siguientes exploradores con Visión operativa:

- Windows Internet Explorer 11, Internet Explorer 10, Internet Explorer 9, Internet Explorer 8 o Internet Explorer 7

- Mozilla Firefox 3.5 o posterior

Independientemente del explorador que use, también debe instalar Microsoft Silverlight 4.

## Tecnologías que se pueden analizar

Evaluación de configuración de Visión operativa analiza las cargas de trabajo siguientes:

- Windows Server 2012 y Microsoft Hyper-V Server 2012

- Windows Server 2008 y Windows Server 2008 R2, incluido:
    - Active Directory
	- Host de Hyper-V
	- Sistema operativo general

- SQL Server 2012, SQL Server 2008 R2, SQL Server 2008
    - Motor de base de datos de SQL Server

- Microsoft SharePoint 2010

- Microsoft Exchange Server 2010 y Microsoft Exchange Server 2013

- Microsoft Lync Server 2013 y Lync Server 2010

- System Center 2012 SP1 – Virtual Machine Manager

Para SQL Server, se admiten las siguientes ediciones de 32 bits y 64 bits para el análisis:

- SQL Server 2008 y 2008 R2 Enterprise

- SQL Server 2008 y 2008 R2 Standard

- SQL Server 2008 y 2008 R2 Workgroup

- SQL Server 2008 y 2008 R2 Web

- SQL Server 2008 y 2008 R2 Express

Además, se admite la edición de 32 bits de SQL Server cuando se ejecuta en la implementación de WOW64.

<!---HONumber=July15_HO2-->