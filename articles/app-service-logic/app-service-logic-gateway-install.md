<properties
   pageTitle="Instalación de la puerta de enlace de datos local para las Aplicaciones lógicas | Microsoft Azure"
   description="Información sobre cómo instalar la puerta de enlace de datos local para su uso en una aplicación lógica."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# Instalación de la puerta de enlace de datos local para las Aplicaciones lógicas

## Instalación y configuración

### Requisitos previos

Mínimos:

* .NET Framework 4.5
* versión de 64 bits de Windows 7 o Windows Server 2008 R2 (o posterior)

Se recomienda:

* CPU de 8 núcleos
* 8 GB de memoria
* versión de 64 bits de Windows 2012 R2 (o posterior)

Consideraciones relacionadas:

* No puede instalar una puerta de enlace en un controlador de dominio.
* No debe instalar una puerta de enlace en un equipo (como, por ejemplo, un portátil) que pueda apagarse, suspenderse o no estar conectado a Internet, ya que la puerta de enlace no se podrá ejecutar en ninguna de tales circunstancias. Además, el rendimiento de la puerta de enlace podría verse afectado en una red inalámbrica.

### Instalación de una puerta de enlace

Puede obtener el [instalador de la puerta de enlace de datos local aquí](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

Especifique **On-premises data gateway** (Puerta de enlace de datos local) como el modo, inicie sesión con su cuenta profesional o educativa y luego configure una puerta de enlace nueva o bien migre, restaure o hágase con el control de una puerta de enlace existente.

* Para configurar una puerta de enlace, escriba un **nombre** y una **clave de recuperación**, y luego haga clic o pulse en **Configurar**.

    Especifique una clave de recuperación que contenga al menos ocho caracteres y guárdela en un lugar seguro. Necesitará esta clave si desea migrar la puerta de enlace, restaurarla o hacerse con el control de ella.

* Para migrar o restaurar una puerta de enlace existente o hacerse con el control de ella, indique la clave de recuperación especificada cuando se creó la puerta de enlace.

### Reinicio de la puerta de enlace

La puerta de enlace se ejecuta como un servicio de Windows y, al igual que con cualquier otro servicio de Windows, puede iniciarla y detenerla de varias maneras. Por ejemplo, puede abrir un símbolo del sistema con permisos elevados en la máquina en la que se ejecuta la puerta de enlace y ejecutar cualquiera de estos comandos:

* Para detener el servicio, ejecute este comando:

    `net stop PBIEgwService`

* Para iniciar el servicio, ejecute este comando:

    `net start PBIEgwService`

### Configuración de un firewall o proxy

Para obtener información sobre cómo dar información de proxy para la puerta de enlace, consulte [Configuración de proxy](https://powerbi.microsoft.com/es-ES/documentation/powerbi-gateway-proxy/).

Puede comprobar si el firewall (o el proxy) podría estar bloqueando las conexiones ejecutando el comando siguiente en un símbolo del sistema de PowerShell. De este modo, probará la conectividad del bus de servicio de Azure. Esto solo probará la conectividad de la red y no tiene nada que ver con el servicio de servidor en la nube o la puerta de enlace. Ayuda a determinar si el equipo puede conectarse a Internet de forma efectiva.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

Los resultados deben ser parecidos al del ejemplo siguiente. Si **TcpTestSucceeded** no es True, podría estar bloqueado por un firewall.

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

Si desea exhaustividad, sustituya los valores de **ComputerName** y **Port** por los que aparecen más adelante en la sección [Configuración de los puertos](#configure-ports) del presente tema.

El firewall también podría estar bloqueando las conexiones del bus de servicio de Azure con los centros de datos de Azure. En tal caso, necesitará una lista blanca (de desbloqueo) de todas las direcciones IP de su región para esos centros de datos. Puede obtener una lista de [direcciones IP de Azure aquí](https://www.microsoft.com/download/details.aspx?id=41653).

### Configuración de los puertos

La puerta de enlace crea una conexión de salida con el bus de servicio de Azure. Se comunica en los puertos de salida siguientes: TCP 443 (valor predeterminado), 5671, 5672 y del 9350 al 9354. La puerta de enlace no requiere puertos de entrada.

Obtenga más información sobre las [soluciones híbridas](../service-bus/service-bus-fundamentals-hybrid-solutions.md).

| NOMBRES DE DOMINIO | PUERTOS DE SALIDA | DESCRIPCIÓN |
| ----- | ------ | ------ |
| *. analysis.windows.net | 443 | HTTPS |
| *.login.windows.net | 443 | HTTPS |
| *.servicebus.windows.net |5671-5672 | Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net | 443, 9350-9354 | Agentes de escucha de retransmisión de Bus de servicio sobre TCP (requiere 443 para la adquisición del token de Control de acceso) |
| *.frontend.clouddatahub.net | 443 | HTTPS |
| *.core.windows.net | 443 | HTTPS |
| login.microsoftonline.com | 443 | HTTPS |
| *.msftncsi.com | 443 | Se utiliza para probar la conectividad a Internet si el servicio Power BI no puede acceder a la puerta de enlace. |

Si tiene que crear una lista blanca de direcciones IP en lugar de los dominios, puede descargar y usar la [lista de intervalos IP de centros de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). En algunos casos, las conexiones al bus de servicio de Azure se realizarán con la dirección IP en lugar de con los nombres de dominio completos.

### Cuenta de inicio de sesión

Los usuarios deberán iniciar sesión con una cuenta profesional o educativa. Se trata de la cuenta de la organización. Si se suscribió a una oferta de Office 365 y no proporcionó el correo electrónico profesional real, este podría aparecer de una forma parecida a jeff@contoso.onmicrosoft.com. La cuenta, dentro de un servicio en la nube, se almacena en un inquilino de Azure Active Directory (AAD). En la mayoría de los casos, el UPN de la cuenta de AAD coincidirá con la dirección de correo electrónico.

### Cuenta de servicio de Windows

La puerta de enlace de datos local está configurada para usar NT SERVICE\\PBIEgwService para las credenciales de inicio de sesión del servicio de Windows. De forma predeterminada, tiene el derecho Iniciar sesión como servicio. Esto se da en el contexto de la máquina en la que va a instalar la puerta de enlace.

Esta no es la cuenta usada para conectarse a los orígenes de datos locales ni la cuenta profesional o educativa con la que inicia sesión en los servicios en la nube.

##Preguntas más frecuentes

### General

**Pregunta**: ¿Qué orígenes de datos admite la puerta de enlace?<br/> **Respuesta**: En la fecha en la que se redactó este artículo, SQL Server.

**Pregunta**: ¿Necesito una puerta de enlace para los orígenes de datos en la nube como, por ejemplo, SQL Azure? <br/> **Respuesta**: No. Las puertas de enlace se conectan únicamente a orígenes de datos locales.

**Pregunta**: ¿Cómo se llama realmente el servicio de Windows?<br/> **Respuesta**: En los servicios, la puerta de enlace se denomina Power BI Enterprise Gateway.

**Pregunta**: ¿Hay alguna conexión de entrada a la puerta de enlace desde la nube? <br/> **Respuesta**: No. La puerta de enlace usa conexiones de salida al bus de servicio de Azure.

**Pregunta**: ¿Qué sucede si bloqueo las conexiones de salida? ¿Qué tengo que abrir? <br/> **Respuesta**: Consulte los puertos y los hosts que utiliza la puerta de enlace.


**Pregunta**: ¿La puerta de enlace debe estar instalada en la misma máquina que el origen de datos? <br/> **Respuesta**: No. La puerta de enlace se conectará al origen de datos usando la información de conexión que se proporcionó. En este sentido, considere la puerta de enlace como una aplicación cliente. Solo tendrá que poder conectarse al nombre del servidor proporcionado.


**Pregunta**: ¿Cuál es la latencia para la ejecución de consultas en un origen de datos de la puerta de enlace? ¿Cuál es la mejor arquitectura? <br/> **Respuesta**: Para reducir la latencia de red, instale la puerta de enlace lo más cerca posible del origen de datos. Si puede instalar la puerta de enlace en el propio origen de datos, la latencia introducida se minimizará. Tenga en cuenta también los centros de datos. Por ejemplo, si el servicio utiliza el centro de datos del oeste de EE. UU. y tiene SQL Server hospedado en una máquina virtual de Azure, también deberá tener la máquina virtual de Azure en el oeste de EE. UU. De este modo, minimizará la latencia y evitará cargas de salida en la máquina virtual de Azure.


**Pregunta**: ¿Hay algún requisito con respecto al ancho de banda de red? <br/> **Respuesta**: Se recomienda tener una capacidad de proceso adecuada para la conexión de red. Cada entorno es diferente, y la cantidad de datos que se envíen afectará a los resultados. Si utiliza ExpressRoute, podrá ayudar a garantizar un nivel de rendimiento entre los centros de datos locales y los de Azure.

Puede usar la aplicación Azure Speed Test (desarrollada por un tercero) para medir su capacidad de proceso.


**Pregunta**: ¿Se puede ejecutar el servicio de Windows de puerta de enlace con una cuenta de Azure Active Directory? <br/> **Respuesta**: No. El servicio de Windows debe tener una cuenta de Windows válida. De forma predeterminada, se ejecutará con el SID de servicio NT SERVICE\\PBIEgwService.


**Pregunta**: ¿Cómo se envían los resultados de vuelta a la nube? <br/> **Respuesta**: Esto se consigue mediante el bus de servicio de Azure. Para obtener más información, consulte cómo funciona.


**Pregunta**: ¿Dónde se almacenan las credenciales? <br/> **Respuesta**: Las credenciales que especifique para un origen de datos se almacenan cifradas en el servicio en la nube de puerta de enlace. Las credenciales se descifran en la puerta de enlace local.

### Alta disponibilidad / recuperación ante desastres

**Pregunta**: ¿Hay algún plan para habilitar escenarios de alta disponibilidad con la puerta de enlace? <br/> **Respuesta**: Es un punto que figura en nuestra agenda, pero aún no tenemos un cronograma para ello.


**Pregunta**: ¿Cuáles son las opciones de recuperación ante desastres disponibles? <br/> **Respuesta**: Puede usar la clave de recuperación para restaurar o mover una puerta de enlace. Cuando instale la puerta de enlace, especifique la clave de recuperación.


**Pregunta**: ¿Qué ventaja aporta la clave de recuperación? <br/> **Respuesta**: Brinda una forma de migrar o recuperar la configuración de la puerta de enlace después de un desastre.

### Solución de problemas

**Pregunta**: ¿Dónde están los registros de la puerta de enlace? <br/> **Respuesta**: Consulte la sección Herramientas más adelante en este tema.


**Pregunta**: ¿Cómo se pueden ver las consultas que se envían al origen de datos local? <br/> **Respuesta**: Puede habilitar el seguimiento de consultas, que incluirá las consultas que se envíen. No olvide devolver esta característica al valor original cuando haya terminado de solucionar problemas. Si deja habilitado el seguimiento de consultas, el tamaño de los registros aumentará.

También puede examinar las herramientas de que dispone su origen de datos para el seguimiento de consultas. Por ejemplo, puede utilizar Eventos extendidos o SQL Profiler en SQL Server y Analysis Services.

## Funcionamiento de la puerta de enlace

Cuando un usuario interactúa con un elemento conectado a un origen de datos local, sucede lo siguiente:

1. El servicio en la nube crea una consulta, junto con las credenciales cifradas del origen de datos y la envía a la cola de la puerta de enlace por procesar.
1. El servicio analiza la consulta e inserta la solicitud en el bus de servicio de Azure.
1. La puerta de enlace de datos local sondea el bus de servicio de Azure en busca de solicitudes pendientes.
1. La puerta de enlace obtiene la consulta, descifra las credenciales y se conecta a los orígenes de datos con ellas.
1. La puerta de enlace envía la consulta al origen de datos para su ejecución.
1. Los resultados se devuelven desde el origen de datos a la puerta de enlace y luego se envían al servicio en la nube. Seguidamente, dicho servicio usa los resultados.

## Solución de problemas

### Actualización a la versión más reciente

Pueden surgir muchos problemas cuando la versión de la puerta de enlace no está actualizada. En general, es conveniente asegurarse de utilizar la versión más reciente. Si no ha actualizado la puerta de enlace durante un mes o más, debe plantearse instalar la versión más reciente de la puerta de enlace y comprobar si puede reproducir el problema.

### Error: Error al agregar el usuario al grupo. (Usuarios del registro de rendimiento de-2147463168 PBIEgwService)

Es posible que reciba este error si intenta instalar la puerta de enlace en un controlador de dominio, lo cual no se admite. Debe implementar la puerta de enlace en una máquina que no sea un controlador de dominio.

## Herramientas

### Recopilación de registros desde el configurador de la puerta de enlace

Puede recopilar varios registros de la puerta de enlace. ¡Empiece siempre por los registros!

#### Registros del instalador

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### Registros de configuración

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### Registros del servicio de puerta de enlace empresarial

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### Registros de eventos

Los registros de Data Management Gateway y PowerBIGateway se encuentran en **Registros de aplicaciones y servicios**.

### Seguimiento de Fiddler

[Fiddler](http://www.telerik.com/fiddler) es una herramienta gratuita de Telerik que supervisa el tráfico HTTP. Puede ver todo el tráfico con el servicio Power BI desde el equipo cliente. Esto puede mostrar los errores y otra información relacionada.

## Pasos siguientes
- [Creación de una conexión local a las Aplicaciones lógicas](app-service-logic-gateway-connection.md)
- [Características de Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md)
- [Conectores de Aplicaciones lógicas](../connectors/apis-list.md)

<!---HONumber=AcomDC_0713_2016-->