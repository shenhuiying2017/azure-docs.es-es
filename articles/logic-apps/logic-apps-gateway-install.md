---
title: "Instalación de una puerta de enlace de datos local: Azure Logic Apps | Microsoft Docs"
description: "Acceda a datos locales desde aplicaciones lógicas mediante la instalación de una puerta de enlace de datos local"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 23b806556667539156ee399e90c7bd4af808804f
ms.lasthandoff: 03/10/2017


---
# <a name="install-an-on-premises-data-gateway-for-azure-logic-apps"></a>Instalación de una puerta de enlace de datos local para Azure Logic Apps

La puerta de enlace de datos local admite estas conexiones de origen de datos:

*   BizTalk Server
*    DB2  
*   Sistema de archivos
*   Informix
*   MQ
*    Base de datos de Oracle 
*   Servidor de aplicaciones de SAP 
*   Servidor de mensajes de SAP
*    SQL Server

Para más información acerca de estas conexiones, consulte [Conectores para Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="installation-and-configuration"></a>Instalación y configuración

### <a name="requirements"></a>Requisitos

Mínimos:

* .NET Framework 4.5
* versión de 64 bits de Windows 7 o Windows Server 2008 R2 (o posterior)

Se recomienda:

* CPU de&8; núcleos
* 8 GB de memoria
* versión de 64 bits de Windows 2012 R2 (o posterior)

Consideraciones relacionadas:

* Instale la puerta de enlace de datos local únicamente en una máquina local.
No puede instalarla en un controlador de dominio.

* No instale la puerta de enlace en un equipo que se pueda apagar, que pueda entrar en suspensión o que no se conecte a Internet porque no se puede ejecutar la puerta de enlace en esas circunstancias. Además, el rendimiento de la puerta de enlace podría verse afectado en una red inalámbrica.

* Solo puede usar una dirección de correo electrónico profesional o educativa en Azure para poder asociar la puerta de enlace de datos local a su cuenta basada en Azure Active Directory.

    Si usa una cuenta de Microsoft (por ejemplo, @outlook.com), puede usar su cuenta de Azure para   [crear una dirección de correo electrónico profesional o educativa](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal).

### <a name="install-the-gateway"></a>Instalación de la puerta de enlace

1.    [Descargue el instalador de la puerta de enlace de datos local aquí](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2.    Especifique **Puerta de enlace de datos local** como modo.

3. Inicie sesión con una cuenta profesional o educativa de Azure. 

4. Configure una nueva puerta de enlace, o bien puede migrar una existente, restaurarla o tomar control de ella.

    Para configurar una puerta de enlace, proporcione un nombre para ella y una clave de recuperación, y elija **Configurar**.
  
    Especifique una clave de recuperación que contenga al menos ocho caracteres y guárdela en un lugar seguro. Necesita esta clave si desea migrar la puerta de enlace, restaurarla o tomar control de ella.

    Para migrar una puerta de enlace existente, restaurarla o tomar control de ella, indique la clave de recuperación especificada cuando se creó la puerta de enlace.

### <a name="restart-the-gateway"></a>Reinicio de la puerta de enlace

La puerta de enlace se ejecuta como servicio de Windows, así que, al igual que con cualquier otro servicio de Windows, puede iniciar y detener el servicio de varias maneras. Por ejemplo, puede abrir un símbolo del sistema con permisos elevados en la máquina en la que se ejecuta la puerta de enlace y ejecutar cualquiera de estos comandos:

* Para detener el servicio, ejecute este comando:
  
    `net stop PBIEgwService`

* Para iniciar el servicio, ejecute este comando:
  
    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>Configuración de un firewall o proxy

Para proporcionar información del proxy para la puerta de enlace, consulte [Configuración de proxy](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/).

Para comprobar si el firewall (o el proxy) podría estar bloqueando conexiones, ejecute el comando siguiente en un símbolo del sistema de PowerShell. Este comando prueba la conectividad con Azure Service Bus y solo la conectividad de red, por lo que el comando no tiene nada que ver con el servicio de servidor en la nube ni la puerta de enlace. Esta prueba ayuda a determinar si la máquina puede conectarse a Internet.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

Los resultados deben ser parecidos al del ejemplo siguiente. Si el valor de **TcpTestSucceeded** no es True, podría estar bloqueado por un firewall.

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Si desea ser exhaustivo, sustituya los valores de **ComputerName** y **Port** por los que aparecen en [Configuración de los puertos](#configure-ports) en este tema.

El firewall también podría estar bloqueando las conexiones de Azure Service Bus con los centros de datos de Azure. Si es así, apruebe (desbloquee) todas las direcciones IP de esos centros de datos en su región.
Puede obtener una lista de [direcciones IP de Azure aquí](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="configure-ports"></a>Configuración de los puertos
La puerta de enlace crea una conexión de salida a Azure Service Bus y se comunica en los puertos de salida: TCP 443 (predeterminado), 5671, 5672 y del 9350 al 9354. La puerta de enlace no requiere puertos de entrada.

Más información sobre las [soluciones híbridas](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMBRES DE DOMINIO | PUERTOS DE SALIDA | DESCRIPCIÓN |
| --- | --- | --- |
| *. analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) | 
| *.servicebus.windows.net | 443, 9350-9354 | Agentes de escucha de retransmisión de Bus de servicio sobre TCP (requiere 443 para la adquisición del token de Control de acceso) | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | Se utiliza para probar la conectividad a Internet cuando el servicio Power BI no puede acceder a la puerta de enlace. | 

Si tiene que aprobar direcciones IP en lugar de los dominios, puede descargar y usar la [lista de intervalos IP de Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). En algunos casos, las conexiones a Azure Service Bus se realizarán con la dirección IP en lugar de con nombres de dominio completos.

### <a name="sign-in-accounts"></a>Cuentas de inicio de sesión

Puede iniciar sesión con una cuenta profesional o educativa, la cual es la cuenta de su organización. Si se suscribió a una oferta de Office 365 y no proporcionó su correo electrónico profesional real, la dirección de inicio de sesión podría tener un aspecto similar al siguiente: jeff@contoso.onmicrosoft.com. La cuenta, dentro de un servicio en la nube, se almacena en un inquilino de Azure Active Directory (Azure AD). Por lo general, el UPN de la cuenta de Azure AD coincide con la dirección de correo electrónico.

### <a name="windows-service-account"></a>Cuenta de servicio de Windows

Para las credenciales de inicio de sesión del servicio de Windows, la puerta de enlace de datos local está configurada para usar NT SERVICE\PBIEgwService. De forma predeterminada, la puerta de enlace tiene derecho a "Iniciar sesión como servicio", en el contexto de la máquina donde se instale la puerta de enlace.

Esta cuenta de servicio no es la misma que se usa para conectarse a orígenes de datos locales, ni la cuenta profesional o educativa que se usa para iniciar sesión en servicios en la nube.

## <a name="how-the-gateway-works"></a>Funcionamiento de la puerta de enlace
Cuando otros interactúan con un elemento conectado a un origen de datos local, sucede lo siguiente:

1. El servicio en la nube crea una consulta, junto con las credenciales cifradas del origen de datos y la envía a la cola de la puerta de enlace por procesar.
2. El servicio analiza la consulta e inserta la solicitud en el bus de servicio de Azure.
3. La puerta de enlace de datos local sondea el bus de servicio de Azure en busca de solicitudes pendientes.
4. La puerta de enlace obtiene la consulta, descifra las credenciales y se conecta al origen de datos con ellas.
5. La puerta de enlace envía la consulta al origen de datos para su ejecución.
6. Los resultados se envían desde el origen de datos, de vuelta a la puerta de enlace y después al servicio en la nube. Seguidamente, dicho servicio usa los resultados.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="general"></a>General

**Pregunta**: ¿necesito una puerta de enlace para los orígenes de datos en la nube como, por ejemplo, SQL Azure? <br/>
**Respuesta**: no. Las puertas de enlace se conectan únicamente a orígenes de datos locales.

**Pregunta**: ¿cómo se llama el servicio de Windows real?<br/>
**Respuesta**: en los servicios, la puerta de enlace se denomina Servicio Power BI Enterprise Gateway.

**Pregunta**: ¿hay alguna conexión de entrada a la puerta de enlace desde la nube? <br/>
**Respuesta**: no. La puerta de enlace usa conexiones de salida al bus de servicio de Azure.

**Pregunta**: ¿qué sucede si bloqueo las conexiones de salida? ¿Qué tengo que abrir? <br/>
**Respuesta**: consulte los puertos y los hosts que utiliza la puerta de enlace.

**Pregunta**: ¿la puerta de enlace debe estar instalada en la misma máquina que el origen de datos? <br/>
**Respuesta**: no. La puerta de enlace se conecta al origen de datos mediante la información de conexión que se proporcionó. Considere la puerta de enlace como una aplicación cliente en este sentido. La puerta de enlace solo necesita la funcionalidad para conectarse al nombre de servidor que se proporcionó.

**Pregunta**: ¿cuál es la latencia para la ejecución de consultas en un origen de datos desde la puerta de enlace? ¿Cuál es la mejor arquitectura? <br/>
**Respuesta**: para reducir la latencia de red, instale la puerta de enlace lo más cerca posible del origen de datos. Si puede instalar la puerta de enlace en el propio origen de datos, esta proximidad minimizará la latencia introducida. Tenga en cuenta también los centros de datos. Por ejemplo, si el servicio usa el centro de datos del oeste de EE. UU. y tiene SQL Server hospedado en una máquina virtual de Azure, esta debería encontrarse también en el oeste de EE. UU. Esta proximidad minimiza la latencia y evita los cargos de salida en la máquina virtual de Azure.

**Pregunta**: ¿hay algún requisito con respecto al ancho de banda de red? <br/>
**Respuesta**: se recomienda que la conexión de red tenga buen rendimiento. Cada entorno es diferente, y la cantidad de datos que se envíe afecta a los resultados. Si utiliza ExpressRoute, podrá ayudar a garantizar un nivel de rendimiento entre los centros de datos locales y los de Azure.

Puede usar la aplicación Azure Speed Test (desarrollada por un tercero) para medir su rendimiento.

**Pregunta**: ¿se puede ejecutar el servicio de Windows de puerta de enlace con una cuenta de Azure Active Directory? <br/>
**Respuesta**: no. El servicio de Windows debe tener una cuenta de Windows válida. De forma predeterminada, el servicio se ejecuta con el SID de servicio NT SERVICE\PBIEgwService.

**Pregunta**: ¿cómo se envían los resultados de vuelta a la nube? <br/>
**Respuesta**: los resultados se envían a través de Azure Service Bus.

**Pregunta**: ¿dónde se almacenan las credenciales? <br/>
**Respuesta**: las credenciales que especifique para un origen de datos se cifran y almacenan en el servicio en la nube de la puerta de enlace. Las credenciales se descifran en la puerta de enlace local.

### <a name="high-availabilitydisaster-recovery"></a>Alta disponibilidad / recuperación ante desastres
**Pregunta**: ¿hay algún plan para habilitar escenarios de alta disponibilidad con la puerta de enlace? <br/>
**Respuesta**: estos escenarios figuran en nuestra agenda, pero aún no tenemos una escala de tiempo.

**Pregunta**: ¿cuáles son las opciones de recuperación ante desastres disponibles? <br/>
**Respuesta**: puede usar la clave de recuperación para restaurar o mover una puerta de enlace. Cuando instale la puerta de enlace, especifique la clave de recuperación.

**Pregunta**: ¿qué ventaja aporta la clave de recuperación? <br/>
**Respuesta**: la clave de recuperación proporciona una forma de migrar o recuperar la configuración de la puerta de enlace después de un desastre.

## <a name="troubleshooting"></a>Solución de problemas

**Pregunta**: ¿dónde están los registros de la puerta de enlace? <br/>
**Respuesta**: consulte la sección Herramientas de este mismo tema.

**Pregunta**: ¿cómo se pueden ver las consultas que se envían al origen de datos local? <br/>
**Respuesta**: puede habilitar el seguimiento de consultas, lo que incluye las consultas que se envían. No olvide devolver el seguimiento de consultas al valor original cuando haya terminado de solucionar problemas. Si lo deja activado, crea registros de mayor tamaño.

También puede examinar las herramientas de que dispone su origen de datos para el seguimiento de consultas. Por ejemplo, puede utilizar Eventos extendidos o SQL Profiler en SQL Server y Analysis Services.

### <a name="update-to-the-latest-version"></a>Actualización a la versión más reciente

Pueden surgir muchos problemas cuando la versión de la puerta de enlace deja de estar actualizada. Como buena práctica general, asegúrese de que está usando la versión más reciente. Si no ha actualizado la puerta de enlace durante un mes o más, debería plantearse instalar su versión más reciente y comprobar si puede reproducir el problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Error: Error al agregar el usuario al grupo. (Usuarios del registro de rendimiento de-2147463168 PBIEgwService)

Es posible que reciba este error si intenta instalar la puerta de enlace en un controlador de dominio, lo cual no se admite. Asegúrese de implementar la puerta de enlace en una máquina que no sea un controlador de dominio.

## <a name="tools"></a>Herramientas
### <a name="collect-logs-from-the-gateway-configurer"></a>Recopilación de registros desde el configurador de la puerta de enlace

Puede recopilar varios registros de la puerta de enlace. ¡Empiece siempre por los registros!

#### <a name="installer-logs"></a>Registros del instalador

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Registros de configuración

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Registros del servicio de puerta de enlace empresarial

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Registros de eventos

Puede encontrar los registros de Data Management Gateway y PowerBIGateway en **Registros de aplicaciones y servicios**.

### <a name="fiddler-trace"></a>Seguimiento de Fiddler

[Fiddler](http://www.telerik.com/fiddler) es una herramienta gratuita de Telerik que supervisa el tráfico HTTP. Puede ver este tráfico con el servicio Power BI desde la máquina cliente. Este servicio puede mostrar errores y otra información relacionada.

## <a name="next-steps"></a>Pasos siguientes
    
* [Conexión a datos locales desde aplicaciones lógicas](../logic-apps/logic-apps-gateway-connection.md)
* [Características de Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Conectores para Azure Logic Apps](../connectors/apis-list.md)

