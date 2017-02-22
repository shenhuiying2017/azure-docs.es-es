---
title: "Conexión de equipos y dispositivos a OMS mediante OMS Gateway | Microsoft Docs"
description: Conecte los dispositivos administrados por OMS y los equipos supervisados por Operations Manager con OMS Gateway para enviar datos al servicio de OMS cuando no tienen acceso a Internet.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 6f9974c109905f432705b85dcc8fc4d3549f16e9
ms.openlocfilehash: 445bc1259cb3fa6f02fa1cadec11b1ac4b186e78


---
# <a name="connect-computers-and-devices-to-oms-using-the-oms-gateway"></a>Conexión de equipos y dispositivos a OMS mediante OMS Gateway
En este documento se describe la forma en que los dispositivos administrados por OMS y los equipos supervisados por System Center Operations Manager (SCOM) pueden enviar datos al servicio de OMS cuando no tienen acceso a Internet. OMS Gateway puede recopilar los datos y enviarlos al servicio de OMS en su nombre.

La puerta de enlace es un proxy de reenvío de HTTP que admite la tunelización HTTP mediante el comando HTTP CONNECT. La puerta de enlace puede controlar hasta 2.000 dispositivos conectados simultáneamente de OMS si se ejecuta en un servidor de 16 GB con CPU de 4 núcleos en el que se ejecuta Windows.

Por ejemplo, su empresa u organización grande podría tener servidores con conectividad de red, pero no tener conexión a Internet. En otro ejemplo, podría tener muchos dispositivos de punto de venta (PDV) sin medio alguno de supervisarlos directamente. En un tercer ejemplo, Operations Manager puede utilizar OMS Gateway como servidor proxy. En todos estos ejemplos, OMS Gateway puede transferir datos de los agentes instalados en los servidores o dispositivos de PDV a OMS.

En lugar de que cada uno de los agentes envíe los datos directamente a OMS, lo que requeriría una conexión directa a Internet, los datos de todos los agentes se envían a través de un solo equipo con conexión a Internet. En ese equipo es donde se instala y se usa la puerta de enlace. En este escenario, se pueden instalar agentes en todos los equipos de donde se vayan a recopilar datos. Después, la puerta de enlace transfiere los datos de los agentes a OMS directamente (no analiza los datos que se transfieren).

Debe instalar al agente de OMS en el equipo donde también esté instalada la puerta de enlace. Si lo hace, puede supervisar OMS Gateway y analizar datos de rendimiento o de eventos del servidor donde esté instalado. Además, el agente ayuda a OMS Gateway a identificar los puntos de conexión del servicio con los que necesita comunicarse.

La puerta de enlace debe tener acceso a Internet para cargar los datos en OMS. Cada agente también debe tener conectividad de red a su puerta de enlace para que los agentes puedan transferir automáticamente datos a la puerta de enlace y recibirlos de esta. Para que el resultado sea óptimo, no instale la puerta de enlace en un equipo que también sea controlador de dominio.

En este diagrama se muestra el flujo de datos de los agentes directos a OMS.

![diagrama de agente directo](./media/log-analytics-oms-gateway/direct-agent-diagram.png)

En este diagrama se muestra el flujo de datos de Operations Manager a OMS.

![Diagrama de Operations Manager](./media/log-analytics-oms-gateway/scom-mgt-server.png)

## <a name="language-availability"></a>Disponibilidad de idiomas

OMS Gateway está disponible en los idiomas siguientes:

- Chino (simplificado)
- Chino (tradicional)
- Checo
- Neerlandés
- English
- Francés
- Alemán
- Húngaro
- Italiano
- Japonés
- Coreano
- Polaco
- Portugués (Brasil)
- Portugués (Portugal)
- Ruso
- Español (internacional)

## <a name="download-the-oms-gateway"></a>Descarga de OMS Gateway

Hay tres maneras de obtener el archivo de instalación de OMS Gateway.

### <a name="microsoft-download-center"></a>Centro de descarga de Microsoft

- La versión más reciente de OMS Gateway se puede descargar desde el [Centro de descarga de Microsoft](http://download.microsoft.com/download/2/5/C/25CF992A-0347-4765-BD7D-D45D5B27F92C/OMS%20Gateway.msi).

### <a name="oms-portal"></a>Portal de OMS

1.    Inicie sesión en su área de trabajo de OMS.
2.    Seleccione **Configuración** > **Orígenes conectados** > **Servidores Windows**.
3.    Haga clic en **Descargar OMS Gateway**.


### <a name="azure-portal"></a>Portal de Azure

1. Vaya a [Azure Portal](https://portal.azure.com) e inicie sesión, examine la lista de servicios y, luego, seleccione **Log Analytics**.
2. Seleccione un área de trabajo.
3. En su hoja de área de trabajo, en **General**, haga clic en **Inicio rápido**.
4. En **Elegir un origen de datos para conectarse al área de trabajo**, haga clic en **Equipos**.
4. En la hoja **Agente directo**, haga clic en **Descargar OMS Gateway**.  
    ![descargar OMS Gateway](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-the-oms-gateway"></a>Instalación de OMS Gateway
La instalación de esta puerta de enlace reemplaza las versiones anteriores de la puerta de enlace que se hayan instalado (reenviador de Log Analytics).

Requisitos previos: .Net Framework 4.5, Windows Server 2012 R2 SP1 y versiones posteriores


1. Para iniciar la instalación, haga doble clic en **OMS Gateway.msi**.
2. En la página principal, haga clic en **Siguiente**.  
    ![Asistente para la instalación de la puerta de enlace](./media/log-analytics-oms-gateway/gateway-wizard01.png)
3. En la página del contrato de licencia, seleccione **I accept the terms in the License Agreement** (Acepto los términos del Contrato de licencia) para aceptar el CLUF y, después, haga clic en **Siguiente**.
4. En la página de dirección del proxy y puerto:
   1. Escriba el número de puerto TCP que se va a usar para la puerta de enlace. El programa de instalación abre este número de puerto del firewall de Windows. El valor predeterminado es 8080.
      El intervalo válido del número de puerto es 1 - 65535. Si el valor especificado no se encuentra en este intervalo, aparece un mensaje de error.
   2. Opcionalmente, si el servidor en que está instalada la puerta de enlace necesita un proxy, escriba la dirección del proxy al que debe conectarse la puerta de enlace. Por ejemplo, `http://myorgname.corp.contoso.com:80`. Si está en blanco, la puerta de enlace intentará conectarse a Internet directamente. De lo contrario, la puerta de enlace se conecta al proxy. Si el servidor proxy requiere autenticación, escriba el nombre de usuario y la contraseña.  
       ![Configuración del proxy en el Asistente de la puerta de enlace](./media/log-analytics-oms-gateway/gateway-wizard02.png)  
   3. Haga clic en **Siguiente**
5. Si Microsoft Update no está habilitado, aparecerá la página de Microsoft Update, donde puede elegir habilitarlo. Elija la opción que desee y haga clic en **Siguiente**. De lo contrario, continúe con el paso siguiente.
6. En la página de la carpeta de destino, deje la carpeta predeterminada %ProgramFiles%\OMS Gateway o escriba la ubicación en la que desea instalar la puerta de enlace y haga clic en **Siguiente**.
7. En la página Ready to install OMS Gateway Preparado para instalar OMS Gateway, haga clic en **Instalar**. Puede aparecer un Control de cuentas de usuario que solicite permiso para realizar la instalación. Si aparece, haga clic en **Sí**.
8. Una vez completada la instalación, haga clic en **Finalizar**. Para comprobar que el servicio se ejecuta, abra el complemento services.msc y compruebe que **OMS Gateway** aparece en la lista de servicios.  
    ![Servicios: OMS Gateway](./media/log-analytics-oms-gateway/gateway-service.png)

## <a name="install-an-agent-on-devices"></a>Instalación de un agente en dispositivos
Si es necesario, consulte [Conexión de equipos Windows a Log Analytics](log-analytics-windows-agents.md) para obtener información acerca de cómo instalar los agentes conectados directamente. En el artículo se describe cómo se puede instalar el agente desde un Asistente para instalación o desde la línea de comandos.

## <a name="configure-oms-agents"></a>Configuración de agentes de OMS
Consulte [Configuración del proxy y del firewall (opcional) en Log Analytics](log-analytics-proxy-firewall.md) para obtener información acerca de cómo configurar un agente para que use un servidor proxy, que es lo que sucede en la puerta de enlace.

Los agentes de Operations Manager envían algunos datos, como las alertas, la evaluación de la configuración, el espacio de las instancias y los datos de capacidad de Operations Manager, a través del servidor de administración. Otros datos de gran volumen, como registros, rendimiento y seguridad de IIS se envían directamente a OMS Gateway. Consulte [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md) para obtener una lista completa de los datos que se envían a través de cada canal.

> [!NOTE]
> Si tiene intención de utilizar la puerta de enlace con equilibrio de carga de red, consulte [Configuración de equilibrio de carga de red](#optionally-configure-network-load-balancing).
>
>

## <a name="configure-a-scom-proxy-server"></a>Configuración de un servidor proxy de SCOM
Configure Operations Manager para agregar la puerta de enlace para que actúe como servidor proxy. Al actualizar la configuración del proxy, esta se aplica automáticamente a todos los agentes que informan a Operations Manager.

Para utilizar la puerta de enlace para dar soporte a Operations Manager, es preciso tener:

* Microsoft Monitoring Agent (versión del agente: **8.0.10900.0** y posterior) instalado en el servidor de la puerta de enlace y configurado para las áreas de trabajo de OMS con las que desea comunicarse.
* La puerta de enlace debe tener conectividad a Internet o estar conectada a un servidor proxy que la tenga.

### <a name="to-configure-scom-for-the-gateway"></a>Para configurar SCOM para la puerta de enlace
1. Abra la consola de Operations Manager y en **Operations Management Suite**, haga clic en **Conexión**, **Configurar servidor proxy**:  
    ![Operations Manager: Configurar servidor proxy](./media/log-analytics-oms-gateway/scom01.png)
2. Seleccione **Use un servidor proxy para obtener acceso al servicio Operations Management Suite** y, a continuación, escriba la dirección IP del servidor de OMS Gateway. Asegúrese de que su prefijo es `http://`:  
    ![Operations Manager: dirección de servidor proxy](./media/log-analytics-oms-gateway/scom02.png)
3. Haga clic en **Finalizar** El servidor de Operations Manager está conectado a su área de trabajo de OMS.

## <a name="configure-network-load-balancing"></a>Configuración de equilibrio de carga de red
La puerta de enlace se puede configurar para alta disponibilidad al usar el equilibrio de carga de red mediante la creación de un clúster. El clúster administra el tráfico de los agentes mediante el redireccionamiento de las conexiones solicitadas desde los agentes de Microsoft Monitoring Agents a través de sus nodos. Si un servidor de puerta de enlace deja de funcionar, el tráfico se redirige a otros nodos.

1. Abra el Administrador de equilibrio de carga de red y cree un clúster.
2. Haga clic con el botón derecho en el clúster antes de agregar las puertas de enlace y seleccione **Propiedades de clúster**. Configure el clúster para que tenga su propia dirección IP:  
    ![Administrador de equilibrio de carga de red – Direcciones IP del clúster](./media/log-analytics-oms-gateway/nlb01.png)
3. Para conectar un servidor de OMS Gateway con Microsoft Monitoring Agent instalado, haga clic con el botón derecho en la dirección IP del clúster y, luego, haga clic en **Agregar host al clúster**.  
    ![Administrador de equilibrio de carga de red – Agregar host al clúster](./media/log-analytics-oms-gateway/nlb02.png)
4. Escriba la dirección IP del servidor de la puerta de enlace al que desea conectarse:  
    ![Administrador de equilibrio de carga de red – Agregar host al clúster: Conectar](./media/log-analytics-oms-gateway/nlb03.png)
5. En los equipos que no tiene conectividad a Internet, asegúrese de usar la dirección IP del clúster al configurar **Propiedades de Microsoft Monitoring Agent**:  
    ![Propiedades de Microsoft Monitoring Agent – Configuración del proxy](./media/log-analytics-oms-gateway/nlb04.png)

## <a name="configure-for-automation-hybrid-workers"></a>Configuración de Hybrid Workers de Automation
Si tiene Hybrid Workers de Automation en su entorno, los pasos siguientes proporcionan soluciones temporales manuales para configurar la puerta de enlace para darles soporte técnico.

En los pasos siguientes, es preciso saber en qué región de Azure reside la cuenta de Automation. Para buscar la ubicación:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione el servicio Azure Automation.
3. Seleccione la cuenta de Azure Automation apropiada.
4. Vea su región en **Ubicación**.  
    ![Azure Portal – Ubicación de la cuenta de Automation](./media/log-analytics-oms-gateway/location.png)

Utilice las siguientes tablas para identificar la dirección URL de cada ubicación:

**Direcciones URL del servicio de datos del tiempo de ejecución del trabajo**

| **ubicación** | **URL** |
| --- | --- |
| Centro-Norte de EE. UU |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Europa occidental |we-jobruntimedata-prod-su1.azure-automation.net |
| Centro-Sur de EE. UU |scus-jobruntimedata-prod-su1.azure-automation.net |
| Este de EE. UU. 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Canadá central |cc-jobruntimedata-prod-su1.azure-automation.net |
| Europa del Norte |ne-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste de Asia |sea-jobruntimedata-prod-su1.azure-automation.net |
| India Central |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japón |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australia |ase-jobruntimedata-prod-su1.azure-automation.net |

**Direcciones URL de servicio Agente**

| **ubicación** | **URL** |
| --- | --- |
| Centro-Norte de EE. UU |ncus-agentservice-prod-1.azure-automation.net |
| Europa occidental |we-agentservice-prod-1.azure-automation.net |
| Centro-Sur de EE. UU |scus-agentservice-prod-1.azure-automation.net |
| Este de EE. UU. 2 |eus2-agentservice-prod-1.azure-automation.net |
| Canadá central |cc-agentservice-prod-1.azure-automation.net |
| Europa del Norte |ne-agentservice-prod-1.azure-automation.net |
| Sudeste de Asia |sea-agentservice-prod-1.azure-automation.net |
| India Central |cid-agentservice-prod-1.azure-automation.net |
| Japón |jpe-agentservice-prod-1.azure-automation.net |
| Australia |ase-agentservice-prod-1.azure-automation.net |

Si el equipo se registra automáticamente como Hybrid Worker para que las revisiones se apliquen mediante la solución Administración de actualizaciones, siga estos pasos:

1. Agregue las direcciones URL del servicio de datos en tiempo de ejecución del trabajo a la lista de hosts permitidos de OMS Gateway. Por ejemplo:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Reinicie el servicio de OMS Gateway mediante el siguiente cmdlet de PowerShell: `Restart-Service OMSGatewayService`

Si el equipo está incorporado en Azure Automation mediante el cmdlet de registro de Hybrid Worker, siga estos pasos:

1. Agregue la dirección URL del registro del servicio Agente en la lista de hosts permitidos de OMS Gateway. Por ejemplo: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Agregue las direcciones URL del servicio de datos en tiempo de ejecución del trabajo a la lista de hosts permitidos de OMS Gateway. Por ejemplo:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Reinicie el servicio OMS Gateway.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets de PowerShell útiles
Los cmdlets pueden ayudarle a completar las tareas necesarias para actualizar la configuración de la OMS Gateway. Antes de usarlos, asegúrese de:

1. Instalar OMS Gateway (MSI).
2. Abrir la ventana de PowerShell.
3. Para importar el módulo, escriba este comando: `Import-Module OMSGateway`
4. Si no se ha producido ningún error en el paso anterior, el módulo se ha importado correctamente y se pueden usar los cmdlets. Escriba `Get-Module OMSGateway`
5. Después de realizar los cambios mediante los cmdlets, asegúrese de reiniciar el servicio Gateway.

Un error en el paso 3 indica que el módulo no se ha importado. El error puede producirse si PowerShell no puede encontrar el módulo. Se puede encontrar en la ruta de instalación de la puerta de enlace: C:\Archivos de programa\Microsoft OMS Gateway\PowerShell.

| **Cmdlet** | **Parámetros** | **Descripción** | **Ejemplos** |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |Clave (se requiere) <br> Valor |Cambia la configuración del servicio |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |Clave |Obtiene la configuración del servicio |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |Dirección <br> Nombre de usuario <br> Password |Establece la dirección (y credencial) del proxy de retransmisión (ascendente) |1. Establecer un proxy de respuesta y la credencial: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2. Establecer un proxy de respuesta que no necesita autenticación: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3. Borrar el valor del proxy de respuesta, es decir, no se necesita un proxy de respuesta: `Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` | |Obtiene la dirección del proxy de retransmisión (ascendente) |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |Host (obligatorio) |Agrega el host a la lista de permitidos |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |Host (obligatorio) |Quita el host de la lista de permitidos |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |Obtiene el host permitido actualmente (solo el host permitido configurado localmente, no incluya los hosts permitidos que se descargan automáticamente) |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |Asunto (obligatorio) |Agrega el asunto del certificado de cliente a la lista de permitidos |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |Asunto (obligatorio) |Quita el asunto del certificado de cliente de la lista de permitidos |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |Obtiene los asuntos de certificado de cliente permitidos actualmente (solo los asuntos permitidos configurados localmente, no se incluyen los asuntos permitidos que se descargan automáticamente) |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshoot"></a>Solución de problemas
Se recomienda instalar el agente de OMS en equipos que tengan la puerta de enlace instalada. A continuación, puede utilizar al agente para recopilar los eventos que registra la puerta de enlace.

![Visor de eventos: registro de OMS Gateway](./media/log-analytics-oms-gateway/event-viewer.png)

**Identificadores de evento y descripciones de OMS Gateway**

La siguiente tabla muestra los identificadores de evento y las descripciones de los eventos del registro de la OMS Gateway.

| **Id** | **Descripción** |
| --- | --- |
| 400 |Cualquier error de la aplicación que no tiene un identificador específico |
| 401 |Configuración errónea. Por ejemplo: listenPort = "text", en lugar de un entero |
| 402 |Excepción al analizar los mensajes de protocolo de enlace TLS |
| 403 |Error de red. Por ejemplo: no se puede conectar al servidor de destino |
| 100 |Información general |
| 101 |El servicio se ha iniciado |
| 102 |El servicio se ha detenido |
| 103 |Se ha recibido un comando HTTP CONNECT del cliente |
| 104 |No es un comando HTTP CONNECT |
| 105 |El servidor de destino no está en la lista de permitidos o el puerto de destino no es un puerto seguro (443) <br> <br> Asegúrese de que el agente MMA del servidor de puerta de enlace y los agentes que se comunican con la puerta de enlace están conectados a la misma área de trabajo de Log Analytics. |
| 105 |ERROR TcpConnection – Invalid Client certificate: CN=Gateway <br><br> Asegúrese de que: <br>    <br> &#149; Utiliza una puerta de enlace con el número de versión 1.0.395.0, o uno superior. <br> &#149; Tanto el agente MMA del servidor de puerta de enlace como los agentes que se comunican con la puerta de enlace están conectados a la misma área de trabajo de Log Analytics. |
| 106 |Cualquier razón por la que la sesión de TLS es sospechosa y rechazada |
| 107 |Se ha comprobado la sesión de TLS |

**Contadores de rendimiento que se recopilan**

La siguiente tabla muestra los contadores de rendimiento disponibles para OMS Gateway. Los contadores se pueden agregar mediante el Monitor de rendimiento.

| **Name** | **Descripción** |
| --- | --- |
| OMS Gateway/Conexión de cliente activa |Número de conexiones de red (TCP) de cliente activas |
| OMS Gateway/Recuento de errores |Número de errores |
| OMS Gateway/Cliente conectado |Número máximo de clientes conectados |
| OMS Gateway/Recuento de rechazos |Número de rechazos debido a un error de validación de TLS |

![Contadores de rendimiento de OMS Gateway](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Obtención de ayuda
Cuando haya iniciado sesión en Azure Portal, puede crear una solicitud de asistencia con OMS Gateway o cualquier otro servicio o característica de un servicio de Azure.
Para solicitar asistencia, haga clic en el signo de interrogación en la esquina superior derecha del portal y, después, en **Nueva solicitud de soporte técnico**. A continuación, complete el formulario de solicitud de soporte técnico nuevo.

![Nueva solicitud de soporte](./media/log-analytics-oms-gateway/support.png)

También puede dejar sus comentarios acerca de OMS o Log Analytics en el [foro de comentarios de Microsoft Azure](https://feedback.azure.com/forums/267889).

## <a name="next-steps"></a>Pasos siguientes
* [Agregue orígenes de datos](log-analytics-data-sources.md) para recopilar datos de los orígenes conectados del área de trabajo de OMS y almacenarlos en el repositorio de OMS.



<!--HONumber=Feb17_HO2-->


