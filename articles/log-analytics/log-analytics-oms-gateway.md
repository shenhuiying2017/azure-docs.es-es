---
title: "Conexión de equipos sin conexión a Operations Management Suite con OMS Gateway | Microsoft Docs"
description: "Conecte equipos sin conexión administrados por Operations Management Suite y supervisados por System Center Operations Manager mediante el uso de OMS Gateway para enviar datos al servicio de Operations Management Suite."
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
ms.date: 03/27/2017
ms.author: magoedte; banders
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19bf9b3d65210458e4f018dc591e35e59287cd8e
ms.lasthandoff: 04/03/2017

---

# <a name="connect-offline-computers-to-operations-management-suite-by-using-oms-gateway"></a>Conexión de equipos sin conexión a Operations Management Suite con OMS Gateway

Los equipos administrados por Operations Management Suite (OMS) y supervisados por System Center Operations Manager (Operations Manager) pueden enviar datos al servicio de Operations Management Suite cuando no tienen acceso a Internet. OMS Gateway es un proxy de reenvío de HTTP que admite la tunelización HTTP mediante el comando HTTP CONNECT. OMS Gateway puede recopilar datos y enviarlos al servicio de Operations Management Suite en nombre de los equipos sin conexión.  

Puede usar OMS Gateway con:

* Hybrid Runbook Worker de Azure Automation.  
* Los equipos con Windows que cuentan con Microsoft Monitoring Agent y que están conectados directamente a un área de trabajo de Operations Management Suite.
* System Center Operations Manager 2012 SP1 con el paquete acumulativo de actualizaciones (UR) 7, System Center Operations Manager 2012 R2 con UR3 o un grupo de administración de System Center Operations Manager 2016 que se integra con Operations Management Suite.  

Algunas directivas de seguridad de TI no permiten que los equipos en red se conecten a Internet. Esto puede suceder en dispositivos de punto de venta (POS) o en servidores que admiten servicios de TI. Sin embargo, debe conectar los equipos a Operations Management Suite para administrarlos y supervisarlos. Puede configurar equipos sin conexión para que se comuniquen directamente con OMS Gateway. La puerta de enlace recibe la configuración y reenvía datos en nombre de los equipos sin conexión.  

Si configura los equipos que tienen Operations Management Suite Agent para que se conecten directamente a un área de trabajo de Operations Management Suite, en su lugar, todos los equipos se comunican mediante el uso de OMS Gateway. La puerta de enlace transfiere datos desde los agentes directamente a Operations Management Suite. La puerta de enlace no analiza los datos en tránsito.

Si integra un grupo de administración de Operations Manager con Operations Management Suite, puede configurar servidores de administración para que se conecten con OMS Gateway. Los servidores reciben la información de configuración y después envían los datos recopilados, en función de la solución que ha configurado. Los agentes de Operations Manager envían algunos datos, como las alertas, la evaluación de la configuración, el espacio de las instancias y los datos de capacidad de Operations Manager, al servidor de administración. Otros datos de gran volumen, como registros, rendimiento y eventos de seguridad de Internet Information Services (IIS), se envían directamente a OMS Gateway. 

Un servidor de puerta de enlace de Operations Manager que se implementa en una red perimetral (también conocida como *DMZ*, *zona desmilitarizada* y *subred filtrada*) o en otra red aislada para supervisar sistemas no de confianza puede comunicarse con OMS Gateway. Los servidores de puerta de enlace de Operations Manager solo pueden informar a un servidor de administración. Cuando se configura un grupo de administración de Operations Manager para comunicarse con OMS Gateway, la información de configuración de proxy se distribuye automáticamente a todos los equipos administrados por agente que están configurados para recopilar datos para Log Analytics. La información de configuración de proxy se distribuye automáticamente incluso si la configuración está vacía.    

Para proporcionar alta disponibilidad a agentes directamente conectados o grupos de Operations Management que se comunican con Operations Management Suite a través de la puerta de enlace, puede utilizar el equilibrio de carga de red (NLB). NLB redirige y distribuye el tráfico entre varios servidores de puerta de enlace. Si un servidor de puerta de enlace deja de funcionar, el tráfico se redirige a otro nodo disponible.  

Se recomienda que instale al agente Operations Management Suite Agent en el equipo que ejecuta el software de OMS Gateway. De esta manera, puede supervisar OMS Gateway y analizar datos de eventos o de rendimiento. El agente ayuda a OMS Gateway a identificar los puntos de conexión de servicio con los que necesita comunicarse.

Cada agente debe tener conectividad de red a su puerta de enlace para que los agentes puedan transferir automáticamente datos a la puerta de enlace y recibirlos de esta. Se recomienda no instalar la puerta de enlace en un controlador de dominio.

El siguiente diagrama muestra el flujo de datos desde los agentes directos a Operations Management Suite mediante el servidor de puerta de enlace. La configuración de proxy de los agentes debe coincidir con el puerto en que se configura OMS Gateway para comunicarse con Operations Management Suite.  

![diagrama de comunicación directa del agente con Operations Management Suite](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

El siguiente diagrama muestra el flujo de datos de un grupo de administración de Operations Manager a Operations Management Suite.   

![diagrama de comunicación de Operations Manager con Operations Management Suite](./media/log-analytics-oms-gateway/oms-omsgateway-opsmgrconnect.png)

## <a name="prerequisites"></a>Requisitos previos

Un equipo que se designa para ejecutar lOMS Gateway debe cumplir estos requisitos:

* Sistema operativo Windows 10, Windows 8.1, Windows 7, Windows Server 2012, Windows Server 2012 R2, Windows Server 2008 o Windows Server 2008 R2
* Microsoft .NET Framework 4.5
* Procesador de 4 núcleos como mínimo
* Mínimo 8 GB de memoria 

### <a name="language-availability"></a>Disponibilidad de idiomas

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

## <a name="download-oms-gateway"></a>Descarga de OMS Gateway

Tiene tres opciones para obtener la última versión de OMS Gateway:

* Descargarla del [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

* Descargarla del portal de Operations Management Suite. Una vez que inicie sesión en el área de trabajo de Operations Management Suite:

    1. Vaya a **Configuración** > **Orígenes conectados** > **Servidores de Windows**. 
    2. Seleccione **Descargar OMS Gateway**.

* Descargarla desde [Azure Portal](https://portal.azure.com). Después de conectarse:  

   1. En la lista de servicios, seleccione **Log Analytics**.  
   2. Seleccione un área de trabajo.
   3. En su hoja de área de trabajo, en **General**, seleccione **Inicio rápido**.
   4. En **Elija un origen de datos para conectarse al área de trabajo**, seleccione **Equipos**.
   5. En la hoja **Agente directo**, seleccione **Descargar OMS Gateway**.<br><br> ![descargar OMS Gateway](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-oms-gateway"></a>Instalación de OMS Gateway

Para instalar la puerta de enlace, complete los pasos siguientes. 

> [!NOTE]
> Si tiene instalada una versión anterior de la puerta de enlace (anteriormente denominada Reenviador de Log Analytics), se actualiza la versión anterior a la versión actual de OMS Gateway.
>

1. En la carpeta de destino, haga doble clic en **OMS Gateway.msi**.
2. En la página **principal**, seleccione **Siguiente**.<br><br> ![Asistente para la instalación de la puerta de enlace](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. En la página del **contrato de licencia**, seleccione **Acepto los términos del contrato de licencia** para aceptar los Términos de licencia del software de Microsoft. 
4. En la página de **dirección del proxy y puerto**:

   1. Escriba el número de puerto TCP que se va a usar para la puerta de enlace. El programa de instalación configura una regla de entrada con este número de puerto en el Firewall de Windows. El valor predeterminado es 8080.
      El intervalo válido del número de puerto es de 1 a 65535. Si el valor especificado no se encuentra en este intervalo, aparece un mensaje de error.
   2. Opcionalmente, si el servidor en que está instalada la puerta de enlace necesita comunicarse a través de un proxy, escriba la dirección del proxy al que debe conectarse la puerta de enlace. Por ejemplo, **http://myorgname.corp.contoso.com:80**. Si el cuadro de dirección de proxy está vacío, la puerta de enlace intenta conectarse directamente a Internet. Si el servidor proxy requiere autenticación, especifique el nombre de usuario y la contraseña.<br><br> ![Configuración del proxy en el Asistente de la puerta de enlace](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
5. Si Microsoft Update no está habilitado en el equipo, aparece la página de **Microsoft Update**. Puede habilitarlo en esta página. De lo contrario, continúe con el paso siguiente.
6. En la página de la **carpeta de destino**, puede dejar la carpeta predeterminada C:\Archivos de programa\OMS Gateway o escribir la ubicación en la que desea instalar la puerta de enlace.
7. En la página **Preparado para instalar**, seleccione **Instalar**. Podría aparecer un cuadro de diálogo en que se solicita permiso para instalar. Seleccione **Sí**.
8. Seleccione **Finalizar**. Para verificar que el servicio está en ejecución, abra el complemento Services.msc y compruebe que **OMS Gateway** aparece en la lista de servicios. Su estado debe ser **En ejecución**.<br><br> ![Comprobar los servicios de OMS Gateway](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="set-up-network-load-balancing"></a>Configuración de equilibrio de carga de red 
Puede configurar OMS Gateway para lograr alta disponibilidad mediante NLB. Puede utilizar la característica de equilibrio de carga de red en Windows Server o equilibradores de carga basados en hardware. Para administrar el tráfico, el equilibrador de carga redirige las conexiones solicitadas desde los agentes de Operations Management Suite o los servidores de administración de Operations Manager a través de sus nodos. Si un servidor de puerta de enlace deja de funcionar, el tráfico se redirige a otros nodos.

Para más información sobre cómo diseñar e implementar un clúster de equilibrio de carga de red de Windows Server 2016, vea [Equilibrio de carga de red](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  

Para configurar un clúster de equilibrio de carga de red de Windows Server, realice los siguientes pasos:  

1. Con una cuenta de administrador, inicie sesión en el servidor de Windows que sea miembro del clúster de equilibrio de carga de red.  
2. En el Administrador del servidor, abra el Administrador de equilibrio de carga de red.
3. Seleccione **Herramientas** y después **Administrador de equilibrio de carga de red**.
4. Para conectar un servidor de OMS Gateway con Microsoft Monitoring Agent instalado, haga clic con el botón derecho en la dirección IP del clúster y luego seleccione **Agregar host al clúster**.<br><br> ![Administrador de equilibrio de carga de red – Agregar host al clúster](./media/log-analytics-oms-gateway/nlb02.png)<br> 
5. Escriba la dirección IP del servidor de la puerta de enlace al que desea conectarse.<br><br> ![Administrador de equilibrio de carga de red – Agregar host al clúster: Conectar](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="set-up-the-operations-management-suite-agent-and-an-operations-manager-management-group"></a>Configuración de Operations Management Suite Agent y de un grupo de administración de Operations Manager
En esta sección se describe cómo configurar agentes de Operations Management Suite directamente conectados y un grupo de administración de Operations Manager. También puede configurar Hybrid Runbook Worker de Azure Automation para usar OMS Gateway para comunicarse con Operations Management Suite.  

Para obtener información sobre los requisitos y los pasos para instalar Operations Management Suite Agent en equipos de Windows que están conectados directamente a Operations Management Suite, vea [Conexión de equipos Windows a Operations Management Suite](log-analytics-windows-agents.md). Para los equipos Linux, vea [Conexión de equipos Linux a Operations Management Suite](log-analytics-linux-agents.md). 

### <a name="set-up-the-operations-management-suite-agent-and-operations-manager-to-use-the-oms-gateway-as-a-proxy-server"></a>Configuración de Operations Management Suite Agent y Operations Manager para usar OMS Gateway como un servidor proxy

### <a name="set-up-a-standalone-operations-management-suite-agent"></a>Configuración de un Operations Management Suite Agent independiente
Para obtener información sobre la configuración de un agente para que use un servidor proxy, vea [Configuración del proxy y del firewall con Microsoft Monitoring Agent](log-analytics-proxy-firewall.md). En este caso, el servidor proxy es la puerta de enlace. Si ha implementado varios servidores de puerta de enlace detrás de un equilibrador de carga de red, la configuración de proxy de Operations Management Suite Agent es la dirección IP virtual del equilibrador de carga de red:<br><br> ![Propiedades de Microsoft Monitoring Agent – Configuración del proxy](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="set-up-operations-manager-all-agents-use-the-same-proxy-server"></a>Configuración de Operations Manager (todos los agentes usan el mismo servidor proxy)
Configure Operations Manager para agregar el servidor de puerta de enlace. La configuración de proxy de Operations Manager se aplica automáticamente a todos los agentes que informan a Operations Manager, incluso si la configuración está vacía.

Para usar OMS Gateway con Operations Manager, debe cumplir los siguientes requisitos:

* Microsoft Monitoring Agent (versión del agente **8.0.10900.0** y versiones posteriores) debe estar instalado en el servidor de puerta de enlace. Debe configurarse para las áreas de trabajo de Operations Management Suite Agent con las que desea comunicarse.
* La puerta de enlace debe tener conectividad a Internet o estar conectada a un servidor proxy que la tenga.

> [!NOTE]
> Si no especifica ningún valor para la puerta de enlace, se insertan valores en blanco para todos los agentes.

Para conectar el servidor de Operations Manager con el área de trabajo de Operations Management Suite Agent:

1. En la consola de Operations Manager, vaya a **Operations Management Suite** > **Conexión** > **Configurar servidor proxy**.<br><br> ![Operations Manager: Configurar servidor proxy](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. Seleccione **Use un servidor proxy para obtener acceso al servicio Operations Management Suite**. Escriba la dirección IP del servidor de OMS Gateway o la dirección IP virtual del equilibrador de carga de red. Asegúrese de que su prefijo es **http://**.<br><br> ![Operations Manager: dirección de servidor proxy](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. Seleccione **Finalizar**. 

### <a name="set-up-operations-manager-specific-agents-use-the-proxy-server"></a>Configuración de Operations Manager (determinados agentes usan el servidor proxy)
En entornos grandes o complejos, es posible que desee tener solo determinados servidores o grupos que utilicen el servidor de OMS Gateway. En estos servidores, Operations Manager Agent no se puede actualizar directamente. El valor lo sobrescribe el valor global del grupo de administración. En su lugar, invalide la regla utilizada para insertar estos valores.

> [!NOTE] 
> Puede usar esta misma técnica de configuración para ejecutar varios servidores de OMS Gateway en su entorno. Por ejemplo, puede requerir que se especifiquen determinados servidores de OMS Gateway en cada región.

1. En la consola de System Center Operations Manager, seleccione el área de trabajo **Creación**.  
2. Seleccione **Reglas** y, en la barra de herramientas de System Center Operations Manager, seleccione el botón **Ámbito**. Si este botón no está disponible, compruebe que tiene un objeto, y no una carpeta, seleccionado en el panel **Supervisión**. El cuadro de diálogo **Objetos de módulo de administración de ámbito** muestra una lista de clases, grupos u objetos de destino comunes. 
3. En el cuadro **Buscar**, escriba **Servicio de mantenimiento**. Selecciónelo en la lista. Seleccione **Aceptar**.  
4. En la barra de herramientas de la consola de Operations Manager, busque la regla **Regla de configuración de proxy de Advisor**. Seleccione **Invalidaciones** y luego vaya a **Invalidar la regla\Para un objeto de clase específico: Servicio de mantenimiento**. Seleccione un objeto específico en la lista. Si lo desea, puede crear un grupo personalizado que incluya el objeto de servicio de mantenimiento de los servidores a los que desea aplicar esta invalidación. Después, aplique la invalidación a ese grupo.
5. En el cuadro de diálogo **Propiedades de invalidación**, junto al parámetro **WebProxyAddress**, seleccione la columna **Invalidar**. En el cuadro **Valor de invalidación**, escriba la dirección URL del servidor de OMS Gateway. Asegúrese de que su prefijo es **http://**.

   >[!NOTE]
   > No es necesario habilitar la regla. La regla se administra automáticamente con una invalidación del módulo de administración de reemplazo de referencia segura de System Center Advisor. El módulo de administración tiene como destino el grupo de servidor de supervisión de System Center Advisor.
   > 

6. Para especificar un módulo de administración, realice una de las siguientes acciones:
    * En la lista **Seleccionar módulo de administración de destino**, seleccione un módulo de administración.
    * Para crear un nuevo módulo de administración no sellado, seleccione **Nuevo**. 
7. Seleccione **Aceptar**. 

### <a name="set-up-automation-hybrid-workers"></a>Configuración de Hybrid Worker de Automation
Si tiene Hybrid Runbook Worker de Azure Automation en el entorno, puede crear una solución alternativa temporal y manual, a fin de que la puerta de enlace los admita.

En los pasos siguientes, es preciso saber en qué región de Azure reside la cuenta de Automation. Para buscar la ubicación:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione el servicio Azure Automation.
3. Seleccione la cuenta de Azure Automation competente.
4. En **Ubicación**, vea la región.<br><br> ![Azure Portal – Ubicación de la cuenta de Automation](./media/log-analytics-oms-gateway/location.png)  

Utilice las siguientes tablas para identificar la dirección URL de cada ubicación:

**Direcciones URL del servicio de datos del tiempo de ejecución del trabajo**

| Ubicación | URL |
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

| Ubicación | URL |
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

Si el equipo se registra automáticamente como Hybrid Runbook Worker para que las revisiones se apliquen mediante la solución de administración de actualizaciones, siga estos pasos:

1. Agregue las direcciones URL del servicio de datos en tiempo de ejecución del trabajo a la lista de **hosts permitidos** de OMS Gateway. Por ejemplo:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Reinicie el servicio de OMS Gateway mediante el siguiente cmdlet de Azure PowerShell: `Restart-Service OMSGatewayService`

Si el equipo está incorporado en Azure Automation mediante el cmdlet de registro de Hybrid Runbook Worker, siga estos pasos:

1. Agregue la dirección URL del registro del servicio Agente en la **lista de hosts** permitidos de OMS Gateway. Por ejemplo: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Agregue las direcciones URL del servicio de datos en tiempo de ejecución del trabajo a la lista de **hosts permitidos** de OMS Gateway. Por ejemplo:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Reinicie el servicio de OMS Gateway:  `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets de PowerShell útiles
Los cmdlets pueden ayudarlo a actualizar la configuración de OMS Gateway. Para poder usar cmdlets de PowerShell, asegúrese de lo siguiente:

1. Instale OMS Gateway (MSI).
2. Abra una ventana de la consola de PowerShell.
3. Para importar el módulo, escriba este comando: `Import-Module OMSGateway`. Si no se produce ningún error, el módulo se importa correctamente y puede usar los cmdlets. 
4. Escriba `Get-Module OMSGateway`.
5. Después de realizar los cambios con los cmdlets, reinicie el servicio de OMS Gateway.

Un error en el paso 3 indica que el módulo no se ha importado. Este error podría producirse si PowerShell no puede encontrar el módulo. El módulo aparece en la ruta de instalación de la puerta de enlace: C:\Archivos de programa\Microsoft OMS Gateway\PowerShell.

| Cmdlet | Parámetros | Descripción | Ejemplos |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |Clave (se requiere) <br> Valor |Cambia la configuración de servicio. |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |Clave |Obtiene la configuración de servicio. |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |Dirección <br> Nombre de usuario <br> Password |Establece la dirección y las credenciales el proxy de retransmisión (ascendente). |1. Establecer un proxy de respuesta y la credencial: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2. Establecer un proxy de respuesta que no necesita autenticación: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3. Borrar el valor del proxy de respuesta, es decir, no se necesita un proxy de respuesta: `Set-OMSGatewayRelayProxy -Address ""`. |
| `Get-OMSGatewayRelayProxy` | |Obtiene la dirección del proxy de retransmisión (ascendente). |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |Host (obligatorio) |Agrega el host a la lista de permitidos |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |Host (obligatorio) |Quita el host de la lista de permitidos |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |Obtiene el host permitido actualmente (solo el host permitido configurado localmente; no se incluyen los hosts permitidos que se descargan automáticamente). |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |Asunto (obligatorio) |Agrega el asunto del certificado de cliente a la lista de permitidos |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |Asunto (obligatorio) |Quita el asunto del certificado de cliente de la lista de permitidos |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |Obtiene los asuntos de certificado de cliente permitidos actualmente (solo los asuntos permitidos configurados localmente; no se incluyen los asuntos permitidos que se descargan automáticamente). |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshooting"></a>Solución de problemas
Para recopilar eventos registrados por la puerta de enlace, debe tener instalado Operations Management Suite Agent.<br><br> ![Visor de eventos: registro de OMS Gateway](./media/log-analytics-oms-gateway/event-viewer.png)

**Identificadores de evento y descripciones de OMS Gateway**

En la tabla siguiente se muestran los identificadores de evento y las descripciones de los eventos del registro de OMS Gateway:

| ID | Descripción |
| --- | --- |
| 400 |Cualquier error de la aplicación que no tiene un identificador específico |
| 401 |Configuración errónea. Por ejemplo: listenPort = "\<text\>", en lugar de un entero |
| 402 |Excepción al analizar los mensajes de protocolo de enlace de Seguridad de la capa de transporte (TLS) |
| 403 |Error de red. Por ejemplo: no se puede conectar al servidor de destino |
| 100 |Información general |
| 101 |El servicio se ha iniciado |
| 102 |El servicio se ha detenido |
| 103 |Se ha recibido un comando HTTP CONNECT del cliente |
| 104 |No es un comando HTTP CONNECT |
| 105 |El servidor de destino no está en la lista de permitidos o el puerto de destino no es un puerto seguro (443) <br> <br> Asegúrese de que el agente Microsoft Monitoring Agent del servidor de puerta de enlace y los agentes que se comunican con la puerta de enlace están conectados a la misma área de trabajo de Log Analytics. |
| 105 |ERROR TcpConnection – Invalid Client certificate: CN=Gateway <br><br> Asegúrese de que: <br>    <br> &#149; Usa la versión de OSM Gateway 1.0.395.0 o una versión posterior. <br> &#149; El agente Microsoft Monitoring Agent del servidor de puerta de enlace y los agentes que se comunican con la puerta de enlace están conectados a la misma área de trabajo de Log Analytics. |
| 106 |Cualquier razón por la que la sesión de TLS es sospechosa y rechazada |
| 107 |Se ha comprobado la sesión de TLS |

**Contadores de rendimiento que se recopilan**

En la tabla siguiente se muestran los contadores de rendimiento disponibles para OMS Gateway. Se pueden agregar contadores con el Monitor de rendimiento de Windows.

| Nombre | Descripción |
| --- | --- |
| OMS Gateway/Conexión de cliente activa |Número de conexiones de red (TCP) de cliente activas |
| OMS Gateway/Recuento de errores |Número de errores |
| OMS Gateway/Cliente conectado |Número máximo de clientes conectados |
| OMS Gateway/Recuento de rechazos |Número de rechazos debido a un error de validación de TLS |

![Contadores de rendimiento de OMS Gateway](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Obtención de ayuda
Cuando haya iniciado sesión en Azure Portal, puede crear una solicitud de asistencia con OMS Gateway o cualquier otro servicio o característica de servicio de Azure.
Para solicitar asistencia, seleccione el signo de interrogación en la esquina superior derecha del portal y después seleccione **Nueva solicitud de soporte técnico**. Complete el formulario de solicitud de soporte técnico nuevo.

![Nueva solicitud de soporte](./media/log-analytics-oms-gateway/support.png)

También puede dejar comentarios sobre Operations Management Suite o Log Analytics en el [foro de comentarios de Microsoft Azure](https://feedback.azure.com/forums/267889).

## <a name="next-steps"></a>Pasos siguientes
Para recopilar datos de los orígenes conectados en el área de trabajo de Operations Management Suite, también puede [agregar orígenes de datos](log-analytics-data-sources.md). Puede almacenar la fecha en el repositorio de Operations Management Suite.

