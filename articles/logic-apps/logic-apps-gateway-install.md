---
title: "Instalación de una puerta de enlace de datos local: Azure Logic Apps | Microsoft Docs"
description: "Antes de obtener acceso a orígenes de datos locales, instale la puerta de enlace de datos local para obtener cifrado y transferencia de datos más rápidos entre orígenes de datos locales y aplicaciones lógicas."
keywords: "obtener acceso a datos, local, transferencia de datos, cifrado, orígenes de datos"
services: logic-apps
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: b3c1e2afadea91f010c3e4b43206b6d30a75ec38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>Instalación de la puerta de enlace de datos local para Azure Logic Apps

Para que las aplicaciones lógicas puedan obtener acceso a orígenes de datos locales, debe instalar y configurar la puerta de enlace de datos local. La puerta de enlace actúa como un puente que permite la transferencia de datos y el cifrado entre sistemas locales y las aplicaciones lógicas. La puerta de enlace retransmite datos desde orígenes locales en canales cifrados hasta Azure Service Bus. Todo el tráfico se origina como tráfico de salida seguro desde el agente de puerta de enlace. Más información sobre [cómo funciona la puerta de enlace de datos](#gateway-cloud-service).

La puerta de enlace admite conexiones a estos orígenes de datos locales:

*   BizTalk Server 2016
*   DB2  
*   Sistema de archivos
*   Informix
*   MQ
*   MySQL
*   Base de datos de Oracle
*   PostgreSQL
*   Servidor de aplicaciones de SAP 
*   Servidor de mensajes de SAP
*   SharePoint
*   SQL Server
*   Teradata

En estos pasos se muestra cómo instalar en primer lugar la puerta de enlace de datos local antes de [configurar una conexión entre la puerta de enlace y las aplicaciones lógicas](./logic-apps-gateway-connection.md). Para obtener más información sobre las conexiones admitidas, consulte [Conectores para Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list). 

Para información sobre cómo usar la puerta de enlace con otros servicios, consulte estos artículos:

*   [Puerta de enlace de datos local de Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Puerta de enlace de datos local de Azure Analysis Services](../analysis-services/analysis-services-gateway.md)
*   [Puerta de enlace de datos local de Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Administración de una puerta de enlace de datos local en Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)

<a name="requirements"></a>

## <a name="requirements"></a>Requisitos

**Mínimos**:

* .NET Framework 4.5
* versión de 64 bits de Windows 7 o Windows Server 2008 R2 (o posterior)

**Recomendaciones**:

* CPU de 8 núcleos
* 8 GB de memoria
* versión de 64 bits de Windows 2012 R2 (o posterior)

**Consideraciones importantes**:

* Instale la puerta de enlace de datos local únicamente en un equipo local.
No puede instalarla en un controlador de dominio.

   > [!TIP]
   > No tiene que instalar la puerta de enlace en el mismo equipo que el origen de datos. Para minimizar la latencia, puede instalar la puerta de enlace lo más cerca posible del origen de datos o en el mismo equipo, suponiendo que tiene los permisos necesarios.

* No instale la puerta de enlace en un equipo que se apague, que entre en suspensión o que no se conecte a Internet, ya que no se puede ejecutar la puerta de enlace en esas circunstancias. Además, el rendimiento de la puerta de enlace podría verse afectado en una red inalámbrica.

* Durante la instalación, debe iniciar sesión con una [cuenta profesional o educativa](https://docs.microsoft.com/azure/active-directory/sign-up-organization) que está administrada por Azure Active Directory (Azure AD), no una cuenta de Microsoft.

  > [!TIP]
  > Si desea usar una cuenta Microsoft que tenga una suscripción de Visual Studio con MSDN, primero [cree un directorio (inquilino) en Azure Active Directory](../active-directory/develop/active-directory-howto-tenant.md) con su cuenta Microsoft, o utilice el directorio predeterminado. Agregue un usuario con una contraseña al directorio y, a continuación, conceda acceso a la suscripción a ese usuario. A continuación, puede iniciar sesión durante la instalación de la puerta de enlace con este nombre de usuario y contraseña.

  Debe usar la misma cuenta profesional o educativa más adelante en Azure Portal al crear un recurso de puerta de enlace y asociarlo a la instalación de la puerta de enlace. Este recurso de puerta de enlace se selecciona después al crear la conexión entre la aplicación lógica y el origen de datos local. [¿Por qué debo usar una cuenta profesional o educativa de Azure AD?](#why-azure-work-school-account)

  > [!TIP]
  > Si se suscribió a una oferta de Office 365 y no proporcionó su correo electrónico profesional real, la dirección de inicio de sesión podría tener un aspecto similar al siguiente: jeff@contoso.onmicrosoft.com. 

* Si tiene una puerta de enlace existente que configuró con un instalador que es anterior a la versión 14.16.6317.4, no puede cambiar la ubicación de la puerta de enlace ejecutando el programa de instalación más reciente. Sin embargo, puede usar el programa de instalación más reciente para instalar una puerta de enlace nueva con la ubicación que quiere en su lugar.
  
  Si tiene un instalador de puerta de enlace que es anterior a la versión 14.16.6317.4, pero no ha instalado la puerta de enlace todavía, puede descargar y usar el programa de instalación más reciente.

<a name="install-gateway"></a>

## <a name="install-the-data-gateway"></a>Instalación de la puerta de enlace de datos

1.  [Descargue y ejecute el programa de instalación de la puerta de enlace en un equipo local](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2. Revise y acepte las condiciones de uso y la declaración de privacidad.

3. Especifique la ruta de acceso en el equipo local donde quiere instalar la puerta de enlace.

4. Cuando se le solicite, inicie sesión con su cuenta profesional o educativa de Azure, no con una cuenta de Microsoft.

   ![Inicio de sesión con una cuenta profesional o educativa de Azure](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. Registre ahora la puerta de enlace instalada con el [servicio en la nube de la puerta de enlace](#gateway-cloud-service). Elija **Registrar una nueva puerta de enlace en este equipo**.

   El servicio en la nube de la puerta de enlace cifra y almacena los detalles de la puerta de enlace y las credenciales del origen de datos. 
   El servicio también enruta las consultas y sus resultados entre la aplicación lógica, la puerta de enlace de datos local y el origen de datos local.

6. Especifique un nombre para la instalación de la puerta de enlace. Cree una clave de recuperación y luego confírmela. 

   > [!IMPORTANT] 
   > La clave de recuperación debe contener al menos ocho caracteres. Asegúrese de guardar y conservar la clave en un lugar seguro. También necesita esta clave cuando quiera migrar, restaurar o controlar una puerta de enlace existente.

   1. Para cambiar la región predeterminada del servicio en la nube de la puerta de enlace y de Azure Service Bus que usa la instalación de la puerta de enlace, elija **Cambiar región**.

      ![Cambio de región](./media/logic-apps-gateway-install/change-region-gateway-install.png)

      La región predeterminada es la asociada al inquilino de Azure AD.

   2. En el panel siguiente, abra **Seleccionar región** para elegir otra región.

      ![Seleccione otra región.](./media/logic-apps-gateway-install/select-region-gateway-install.png)

      Por ejemplo, puede seleccionar la misma región que la aplicación lógica o la región más cercana al origen de datos local para reducir la latencia. El recurso de puerta de enlace y la aplicación lógica pueden tener ubicaciones distintas.

      > [!IMPORTANT]
      > No puede cambiar esta región después de la instalación. Esta región también determina y restringe la ubicación en la que puede crear el recurso de Azure para la puerta de enlace. De modo que, al crear el recurso de puerta de enlace en Azure, asegúrese de que la ubicación del recurso coincide con la región que seleccionó durante la instalación de la puerta de enlace.
      > 
      > Si quiere usar una región distinta para la puerta de enlace más adelante, debe configurar una puerta de enlace nueva.

   3. Cuando esté listo, elija **Hecho**.

7. Ahora, siga estos pasos en Azure Portal para [crear un recurso de Azure para la puerta de enlace](../logic-apps/logic-apps-gateway-connection.md). 

Más información sobre [cómo funciona la puerta de enlace de datos](#gateway-cloud-service).

## <a name="migrate-restore-or-take-over-an-existing-gateway"></a>Migrar, restaurar o controlar una puerta de enlace existente

Para llevar a cabo estas tareas, debe tener la clave de recuperación que se especificó cuando se instaló la puerta de enlace.

1. En el menú Inicio del equipo, elija **Puerta de enlace de datos locales**.

2. Cuando se abra el instalador, inicie sesión con la misma cuenta profesional o educativa de Azure que se usó anteriormente para instalar la puerta de enlace.

3. Elija **Migrar, restaurar o controlar una puerta de enlace existente**.

4. Especifique la clave de recuperación para la puerta de enlace que quiere migrar, restaurar o controlar.

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Cuenta de servicio de Windows

La puerta de enlace de datos local se ejecuta como un servicio de Windows y está configurada para usar `NT SERVICE\PBIEgwService` para las credenciales de inicio de sesión del servicio de Windows. De forma predeterminada, la puerta de enlace tiene el derecho "Iniciar sesión como servicio" en la máquina donde se instala la puerta de enlace. Para crear y mantener la puerta de enlace en Azure Portal, la cuenta de servicio de Windows debe tener al menos permisos de **Colaborador**. 

> [!NOTE]
> Esta cuenta de servicio de Windows no es la misma que se usa para conectarse a orígenes de datos locales, ni la cuenta profesional o educativa de Azure que se usa para iniciar sesión en servicios en la nube.

<a name="restart-gateway"></a>

## <a name="restart-the-gateway"></a>Reinicio de la puerta de enlace

Al igual que con cualquier otro servicio de Windows, puede iniciar y detener el servicio de varias maneras. Por ejemplo, puede abrir un símbolo del sistema con permisos elevados en el equipo en el que se ejecuta la puerta de enlace y ejecutar cualquiera de estos comandos:

* Para detener el servicio, ejecute este comando:
  
    `net stop PBIEgwService`

* Para iniciar el servicio, ejecute este comando:
  
    `net start PBIEgwService`

## <a name="configure-a-firewall-or-proxy"></a>Configuración de un firewall o proxy

La puerta de enlace crea una conexión de salida a [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Para proporcionar información del proxy para la puerta de enlace, consulte [Configuración de proxy](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/).

Para comprobar si el firewall o el proxy puede bloquear conexiones, confirme si el equipo puede conectarse realmente a Internet y a [Azure Service Bus](https://azure.microsoft.com/services/service-bus/). Desde un símbolo del sistema de PowerShell, ejecute este comando:

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> Este comando solo comprueba la conectividad de red y la conectividad a Azure Service Bus. Por lo tanto, el comando no tiene nada que ver con la puerta de enlace ni con el servicio en la nube de la puerta de enlace que cifra y almacena las credenciales y los detalles de la puerta de enlace. 
>
> Además, este comando solo está disponible en Windows Server 2012 R2 o posterior y Windows 8.1 o posterior. En versiones anteriores del sistema operativo, puede usar Telnet para probar la conectividad. Obtenga más información sobre [Azure Service Bus y soluciones híbridas](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

Los resultados deben ser parecidos a los del ejemplo siguiente:

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Si el valor de **TcpTestSucceeded** no es **True**, podría estar bloqueado por un firewall. Si quiere ser detallado, sustituya los valores de **ComputerName** y **Port** por los que aparecen en [Configuración de los puertos](#configure-ports) en este tema.

El firewall también podría estar bloqueando las conexiones de Azure Service Bus con los centros de datos de Azure. Si se produce este escenario, apruebe (desbloquee) todas las direcciones IP de esos centros de datos en su región. Para esas direcciones IP, puede [obtener la lista de direcciones IP de Azure aquí](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="configure-ports"></a>Configuración de los puertos

La puerta de enlace crea una conexión de salida a [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) y se comunica en los puertos de salida: TCP 443 (predeterminado), 5671, 5672 y del 9350 al 9354. La puerta de enlace no requiere puertos de entrada. Obtenga más información sobre [Azure Service Bus y soluciones híbridas](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMBRES DE DOMINIO | PUERTOS DE SALIDA | DESCRIPCIÓN |
| --- | --- | --- |
| *. analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) | 
| *.servicebus.windows.net | 443, 9350-9354 | Agentes de escucha en Service Bus Relay sobre TCP (requiere 443 para la adquisición del token de Access Control) | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | Se utiliza para probar la conectividad a Internet cuando el servicio Power BI no puede acceder a la puerta de enlace. | 

Si tiene que aprobar direcciones IP en lugar de los dominios, puede descargar y usar la [lista de intervalos IP de Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). En algunos casos, las conexiones a Azure Service Bus se realizarán con la dirección IP en lugar de con nombres de dominio completos.

<a name="gateway-cloud-service"></a>
## <a name="how-does-the-data-gateway-work"></a>¿Cómo funciona la puerta de enlace de datos?

La puerta de enlace de datos facilita la comunicación rápida y segura entre la aplicación lógica, el servicio en la nube de la puerta de enlace y el origen de datos local. 

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

Por lo tanto, cuando el usuario en la nube interactúa con un elemento conectado a un origen de datos local, sucede lo siguiente:

1. El servicio en la nube de la puerta de enlace crea una consulta, junto con las credenciales cifradas del origen de datos y la envía a la cola de la puerta de enlace por procesar.

2. El servicio en la nube de la puerta de enlace analiza la consulta e inserta la solicitud en la instancia de Azure Service Bus.

3. La puerta de enlace de datos local sondea el bus de servicio de Azure en busca de solicitudes pendientes.

4. La puerta de enlace obtiene la consulta, descifra las credenciales y se conecta al origen de datos con ellas.

5. La puerta de enlace envía la consulta al origen de datos para su ejecución.

6. Los resultados se envían desde el origen de datos, de vuelta a la puerta de enlace y, después, al servicio en la nube de la puerta de enlace. Seguidamente, el servicio en la nube de la puerta de enlace usa los resultados.

<a name="faq"></a>
## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="general"></a>General

**P**: ¿Necesito una puerta de enlace para los orígenes de datos en la nube como, por ejemplo, SQL Azure? <br/>
**R:** No. Las puertas de enlace se conectan únicamente a orígenes de datos locales.

**P**: ¿La puerta de enlace debe estar instalada en la misma máquina que el origen de datos? <br/>
**R:** No. La puerta de enlace se conecta al origen de datos mediante la información de conexión que se proporcionó. Considere la puerta de enlace como una aplicación cliente en este sentido. La puerta de enlace solo necesita la funcionalidad para conectarse al nombre de servidor que se proporcionó.

<a name="why-azure-work-school-account"></a>

**P**: ¿Por qué debo usar una cuenta profesional o educativa de Azure para iniciar sesión? <br/>
**R**: Solo puede usar una cuenta profesional o educativa de Azure al instalar la puerta de enlace de datos local. Su cuenta de inicio de sesión se almacena en un inquilino administrado por Azure Active Directory (Azure AD). Por lo general, el nombre principal de usuario (UPN) de la cuenta de Azure AD coincide con la dirección de correo electrónico.

**P**: ¿Dónde se almacenan mis credenciales? <br/>
**R**: Las credenciales que especifique para un origen de datos se cifran y almacenan en el servicio en la nube de la puerta de enlace. Las credenciales se descifran en la puerta de enlace de datos local.

**P**: ¿Hay algún requisito con respecto al ancho de banda de red? <br/>
**R**: Se recomienda que la conexión de red tenga buen rendimiento. Cada entorno es diferente, y la cantidad de datos que se envíe afecta a los resultados. Si usa ExpressRoute, podrá ayudar a garantizar un nivel de rendimiento entre los centros de datos locales y los de Azure.
Puede usar la aplicación Azure Speed Test (desarrollada por un tercero) para medir su rendimiento.

**P**: ¿cuál es la latencia para la ejecución de consultas en un origen de datos desde la puerta de enlace? ¿Cuál es la mejor arquitectura? <br/>
**R**: Para reducir la latencia de red, instale la puerta de enlace lo más cerca posible del origen de datos. Si puede instalar la puerta de enlace en el propio origen de datos, esta proximidad minimizará la latencia introducida. Tenga en cuenta también los centros de datos. Por ejemplo, si el servicio usa el centro de datos del oeste de EE. UU. y tiene SQL Server hospedado en una máquina virtual de Azure, esta debería encontrarse también en el oeste de EE. UU. Esta proximidad minimiza la latencia y evita los cargos de salida en la máquina virtual de Azure.

**P**: ¿Cómo se devuelven los resultados a la nube? <br/>
**R**: Los resultados se envían a través de Azure Service Bus.

**P**: ¿Hay alguna conexión de entrada a la puerta de enlace desde la nube? <br/>
**R:** No. La puerta de enlace usa conexiones de salida al bus de servicio de Azure.

**P**: ¿Qué sucede si bloqueo las conexiones de salida? ¿Qué tengo que abrir? <br/>
**R**: Consulte los puertos y los hosts que usa la puerta de enlace.

**P**: ¿Cómo se llama el servicio real de Windows?<br/>
**R**: En los servicios, la puerta de enlace se llama Servicio Power BI Enterprise Gateway.

**P**: ¿Se puede ejecutar el servicio de Windows de puerta de enlace con una cuenta de Azure Active Directory? <br/>
**R:** No. El servicio de Windows debe tener una cuenta de Windows válida. De forma predeterminada, el servicio se ejecuta con el SID de servicio NT SERVICE\PBIEgwService.

### <a name="high-availability-and-disaster-recovery"></a>Alta disponibilidad y recuperación ante desastres

**P**: ¿Cuáles son las opciones de recuperación ante desastres disponibles? <br/>
**R**: Puede usar la clave de recuperación para restaurar o mover una puerta de enlace. Cuando instale la puerta de enlace, especifique la clave de recuperación.

**P**: ¿Qué ventaja aporta la clave de recuperación? <br/>
**R**: La clave de recuperación proporciona una forma de migrar o recuperar la configuración de la puerta de enlace después de un desastre.

**P**: ¿Hay algún plan para habilitar escenarios de alta disponibilidad con la puerta de enlace? <br/>
**R**: Estos escenarios figuran en nuestra agenda, pero aún no tenemos una escala de tiempo.

## <a name="troubleshooting"></a>Solución de problemas

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**P**: ¿Cómo se pueden ver las consultas que se envían al origen de datos local? <br/>
**R**: Puede habilitar el seguimiento de consultas, que incluye las consultas que se envían. No olvide devolver el seguimiento de consultas al valor original cuando haya terminado de solucionar problemas. Si lo deja activado, crea registros de mayor tamaño.

También puede examinar las herramientas de que dispone su origen de datos para el seguimiento de consultas. Por ejemplo, puede utilizar Eventos extendidos o SQL Profiler en SQL Server y Analysis Services.

**P**: ¿Dónde están los registros de la puerta de enlace? <br/>
**R**: Consulte la sección Herramientas de este mismo tema.

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
