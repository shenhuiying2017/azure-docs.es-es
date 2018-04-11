---
title: Conexión de equipos mediante OMS Gateway | Microsoft Docs
description: Conecte los dispositivos y los equipos supervisados por Operations Manager con OMS Gateway para enviar datos al servicio de Azure Automation y Log Analytics cuando no tienen acceso a Internet.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: magoedte
ms.openlocfilehash: 66e5444f5346a44cfc8a43cf2b43dbaeacffedc9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="connect-computers-without-internet-access-by-using-the-oms-gateway"></a>Conexión de equipos sin acceso a Internet mediante OMS Gateway
Este documento describe cómo configurar la comunicación con Azure Automation y Log Analytics mediante OMS Gateway cuando equipos que están directamente conectados o están supervisados por Operations Manager no tienen acceso a Internet. OMS Gateway es un proxy de reenvío de HTTP que admite la tunelización HTTP mediante el comando HTTP CONNECT. Puede recopilar datos y enviarlos a Azure Automation y Log Analytics en nombre de los equipos que no tienen acceso a Internet.  

OMS Gateway admite:

* Hybrid Runbook Workers de Azure Automation  
* Equipos Windows con Microsoft Monitoring Agent directamente conectado a un área de trabajo de Log Analytics
* Equipos Linux con el Agente de OMS para Linux directamente conectado a un área de trabajo de Log Analytics  
* System Center Operations Manager 2012 SP1 con UR7, Operations Manager 2012 R2 con UR3, Operations Manager 2016 y el grupo de administración de Operations Manager versión 1801 integrado en Log Analytics  

Si las directivas de seguridad de TI no permiten a determinados equipos de la red conectarse a Internet (como dispositivos de punto de venta o servidores que admiten servicios de TI), pero necesita conectarlos a Azure Automation o Log Analytics para administrarlos y supervisarlos, se pueden configurar para que se comuniquen directamente con OMS Gateway.

 Si estos equipos están configurados con el agente de OMS para conectarse directamente a un área de trabajo de Log Analytics, todos los equipos se comunicarán en su lugar con OMS Gateway. OMS Gateway transfiere datos desde los agentes al servicio directamente. No analiza los datos mientras están en tránsito.

Cuando un grupo de administración de Operations Manager se integra en Log Analytics, se pueden configurar los servidores de administración para que se conecten a OMS Gateway a fin de recibir información de configuración y enviar los datos recopilados. Los agentes de Operations Manager envían algunos datos, como las alertas, la evaluación de la configuración, el espacio de las instancias y los datos de capacidad de Operations Manager, al servidor de administración. Otros datos de gran volumen, como registros, información sobre rendimiento y eventos de seguridad de IIS se envían directamente a OMS Gateway.  

Si dispone de uno o más servidores de puerta de enlace de Operations Manager implementados en una red perimetral u otra red aislada para supervisar sistemas no confiables, estos no se pueden comunicar con OMS Gateway. Los servidores de puerta de enlace de Operations Manager solo pueden informar a un servidor de administración. 

Cuando se configura un grupo de administración de Operations Manager para comunicarse con OMS Gateway, la información de configuración de proxy se distribuye automáticamente a todos los equipos administrados por agente que están configurados para recopilar datos para Log Analytics, incluso si la configuración está vacía.    

Para proporcionar alta disponibilidad a grupos directamente conectados o grupos de Operations Management que se comunican con Log Analytics mediante OMS Gateway, puede utilizar el equilibrio de carga de red para redirigir y distribuir el tráfico entre varios servidores de OMS Gateway. Si un servidor de puerta de enlace deja de funcionar, el tráfico se redirige a otro nodo disponible.  

Se recomienda que instale el agente de OMS en el equipo que ejecuta el software de OMS Gateway para supervisarlo y analizar el rendimiento o los datos de eventos. Además, el agente ayuda a OMS Gateway a identificar los puntos de conexión del servicio con los que necesita comunicarse.

Todos los agentes deben tener conectividad de red a su puerta de enlace para poder transferir automáticamente datos a la puerta de enlace y recibirlos de esta. No se recomienda instalar una puerta de enlace en un controlador de dominio.

El siguiente diagrama muestra el flujo de datos desde los agentes directos a Azure Automation y Log Analytics mediante el servidor de puerta de enlace. La configuración de proxy del agente debe usar el mismo puerto que usa OMS Gateway para comunicarse con el servicio.  

![Diagrama de comunicación de agente directo con los servicios](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

El siguiente diagrama muestra el flujo de datos de un grupo de administración de Operations Manager a Log Analytics.   

![Diagrama de comunicación de Operations Manager con Log Analytics](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>requisitos previos

Para designar un equipo para que ejecute OMS Gateway, asegúrese de que tiene los siguientes componentes:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .NET Framework 4.5
* Procesador de 4 núcleos y 8 GB de memoria como mínimo 

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

### <a name="supported-encryption-protocols"></a>Protocolos de cifrado admitidos
OMS Gateway solo admite Seguridad de la capa de transporte (TLS) 1.0, 1.1 y 1.2.  No se admite Capa de sockets seguros (SSL).

### <a name="supported-number-of-agent-connections"></a>Número admitido de conexiones del agente
En la siguiente tabla se resalta el número admitido de agentes que se comunican con un servidor de puerta de enlace. Esta compatibilidad se basa en los agentes que cargan aproximadamente 200 KB de datos cada 6 segundos. El volumen de datos por agente de prueba es de unos 2,7 GB por día.

|Puerta de enlace |Número aproximado de agentes que se admiten|  
|--------|----------------------------------|  
|- CPU: Intel XEON CPU E5-2660 v3 @ 2.6 GHz 2 núcleos<br> - Memoria: 4 GB<br> - Ancho de banda de red: 1 Gbps| 600|  
|- CPU: Intel XEON CPU E5-2660 v3 @ 2.6 GHz 4 núcleos<br> - Memoria: 8 GB<br> - Ancho de banda de red: 1 Gbps| 1000|  

## <a name="download-the-oms-gateway"></a>Descarga de OMS Gateway

Hay dos maneras de obtener la versión más reciente del archivo de instalación de OMS Gateway.

1. Descargarla del [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

2. Descargarla desde Azure Portal. Tras iniciar sesión en Azure Portal, siga estos pasos:  

   1. Vaya a la lista de servicios y, luego, seleccione **Log Analytics**.  
   2. Seleccione un área de trabajo.
   3. En su hoja de área de trabajo, en **General**, seleccione **Inicio rápido**.
   4. En **Elija un origen de datos para conectarse al área de trabajo**, seleccione **Equipos**.
   5. En el panel **Agente directo**, seleccione **Descargar OMS Gateway**.
   
    ![Descarga de OMS Gateway](./media/log-analytics-oms-gateway/download-gateway.png)

O 

   1. En la hoja de área de trabajo en **Configuración**, seleccione **Configuración avanzada**.
   2. Seleccione **Orígenes conectados** > **Servidores Windows**. Después, seleccione **Descargar OMS Gateway**.

## <a name="install-the-oms-gateway"></a>Instalación de OMS Gateway

Para instalar una puerta de enlace, realice los pasos siguientes. Si tiene instalada la versión anterior, anteriormente denominada *Reenviador de Log Analytics*, se actualizará a esta versión.  

1. En la carpeta de destino, seleccione **OMS Gateway.msi**.
2. En la página **principal**, seleccione **Siguiente**.

 ![Asistente para instalación de la puerta de enlace](./media/log-analytics-oms-gateway/gateway-wizard01.png)
  
3. En la página del **contrato de licencia**, seleccione **I accept the terms in the License Agreement** (Acepto los términos del Contrato de licencia). Luego, seleccione **Siguiente**.

4. En la página de **dirección del proxy y puerto**:

   a. Escriba el número de puerto TCP que se va a usar para la puerta de enlace. El programa de instalación configura una regla de entrada con este número de puerto en el firewall de Windows. El valor predeterminado es 8080. El intervalo válido del número de puerto es de 1 a 65535. Si el valor especificado no se encuentra en este intervalo, aparece un mensaje de error.

   b. Opcionalmente, si el servidor en que está instalada la puerta de enlace necesita comunicarse a través de un proxy, escriba el proxy al que debe conectarse la puerta de enlace. Un ejemplo podría ser `http://myorgname.corp.contoso.com:80`, como se indica en la siguiente captura de pantalla. Si deja este campo en blanco, la puerta de enlace intentará conectarse directamente a Internet.  Si el servidor proxy requiere autenticación, especifique el nombre de usuario y la contraseña.
   
    ![Configuración del proxy en el Asistente para puerta de enlace](./media/log-analytics-oms-gateway/gateway-wizard02.png)

4. Seleccione **Next** (Siguiente).

5. Si Microsoft Update no está habilitado, aparecerá la página de Microsoft Update, donde puede elegir habilitarlo. Elija la opción que desee y seleccione **Siguiente**. De lo contrario, continúe con el paso siguiente.

6. En la página de la **carpeta de destino**, deje la carpeta predeterminada **C:\Archivos de programa\OMS Gateway** o escriba la ubicación en la que desea instalar OMS Gateway. Luego, seleccione **Siguiente**.

7. En la página **Preparado para instalar**, seleccione **Instalar**. Puede aparecer un Control de cuentas de usuario que solicite permiso para realizar la instalación. Si recibe una solicitud de permiso, seleccione **Sí**.

8. Una vez finalizada la instalación, seleccione **Finalizar**. Para comprobar que el servicio se está ejecutando, abra el complemento services.msc y asegúrese de que **OMS Gateway** aparece en la lista de servicios y que su estado sea **En ejecución**.

    ![Servicios: OMS Gateway](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Configuración de equilibrio de carga de red 
La puerta de enlace se puede configurar para alta disponibilidad mediante el equilibrio de carga de red (NLB). Use Equilibrio de carga de red de Microsoft o equilibradores de carga basados en el hardware.  El equilibrador de carga administra el tráfico mediante el redireccionamiento de las conexiones solicitadas desde los agentes de OMS o los servidores de administración de Operations Manager a través de sus nodos. Si un servidor de puerta de enlace deja de funcionar, el tráfico se redirige a otros nodos.

Para más información sobre cómo diseñar e implementar un clúster de equilibrio de carga de red de Windows Server 2016, consulte [Equilibrio de carga de red](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Los pasos siguientes describen cómo configurar un clúster de equilibrio de carga de red de Microsoft.  

1.  Inicie sesión con una cuenta administrativa en el servidor Windows que sea miembro del clúster NLB.  

2.  En el Administrador del servidor, abra el Administrador de equilibrio de carga de red. Seleccione **Herramientas** y después **Administrador de equilibrio de carga de red**.

3. Para conectar un servidor de OMS Gateway con Microsoft Monitoring Agent instalado, haga clic con el botón derecho en la dirección IP del clúster y, luego, seleccione **Agregar host al clúster**.

 ![Administrador de equilibrio de carga de red: Agregar host al clúster](./media/log-analytics-oms-gateway/nlb02.png)

4. Escriba la dirección IP del servidor de la puerta de enlace al que desea conectarse.

    ![Administrador de equilibrio de carga de red: Agregar host al clúster: Conectar](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-the-oms-agent-and-the-operations-manager-management-group"></a>Configuración del agente de OMS y del grupo de administración de Operations Manager
Las siguientes de este artículo incluyen pasos sobre cómo configurar directamente los agentes de OMS conectados, un grupo de administración de Operations Manager o Hybrid Runbook Workers de Azure Automation con OMS Gateway para comunicarse con Azure Automation o Log Analytics.  

Para comprender los requisitos y pasos de instalación del agente de OMS en equipos con Windows que directamente se conectan a Log Analytics, consulte [Recopilar datos de equipos en su entorno con Log Analytics](log-analytics-concept-hybrid.md#prerequisites).

 Para los equipos Linux, consulte [Conexión de equipos Linux a Log Analytics](log-analytics-quick-collect-linux-computer.md). Para información relacionada con el Hybrid Runbook Worker de Automation, consulte la [Implementación de Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-the-standalone-oms-agent"></a>Configuración del agente de OMS de forma independiente
Para más información acerca de cómo configurar un agente para que use un servidor proxy (que en este caso es la puerta de enlace), consulte [Recopilar datos de equipos en su entorno con Log Analytics](log-analytics-concept-hybrid.md#prerequisites). Si ha implementado varios servidores de puerta de enlace detrás de un equilibrador de carga de red, la configuración de proxy del agente de OMS es la dirección IP virtual de NLB:

![Propiedades de Microsoft Monitoring Agent: Configuración del proxy](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager-all-agents-use-the-same-proxy-server"></a>Configuración de Operations Manager: todos los agentes usan el mismo servidor proxy
Configure Operations Manager para agregar la puerta de enlace del servidor.  La configuración de proxy de Operations Manager se aplica automáticamente a todos los agentes que informan a Operations Manager, incluso si la configuración está vacía.  

Para usar OMS Gateway para que admita Operations Manager, debe disponer de los siguientes componentes:

* Microsoft Monitoring Agent (versión del agente: **8.0.10900.0** o posterior) instalado en el servidor de la puerta de enlace y configurado para las áreas de trabajo de Log Analytics con las que desea comunicarse.

* Una puerta de enlace que tenga conectividad a Internet o una conexión a un servidor proxy que la tenga.

> [!NOTE]
> Si no especifica ningún valor para la puerta de enlace, se insertan valores en blanco para todos los agentes.
> 

Si se trata de la primera vez que se registra el grupo de administración de Operations Manager con un área de trabajo de Log Analytics, la opción para especificar la configuración de proxy para el grupo de administración no estará disponible en la consola del operador. 

El grupo de administración tiene que estar registrado correctamente con el servicio para que esta opción esté disponible. Actualice la configuración de proxy del sistema con Netsh en el sistema desde el que se esté ejecutando la consola del operador y todos los servidores de administración del grupo de administración.

1. Abra un símbolo del sistema con privilegios elevados.

    a. Vaya a **Inicio**. Escriba **cmd**.
    
    b. Haga clic con el botón derecho en el **símbolo del sistema**. A continuación, seleccione **Ejecutar como administrador**.
    
2. Escriba el comando siguiente y, a continuación, seleccione **Entrar**:

    `netsh winhttp set proxy <proxy>:<port>`

Después de completar la integración con Log Analytics, puede quitar el cambio ejecutando `netsh winhttp reset proxy`. A continuación, use la opción **Configurar el servidor proxy** en la consola de operaciones para especificar el servidor de OMS Gateway. 

1. Abra la consola de Operations Manager. En **Operations Management Suite**, seleccione **Conexión**. A continuación, seleccione **Configurar el servidor proxy**.

    ![Operations Manager: Configurar el servidor proxy](./media/log-analytics-oms-gateway/scom01.png)

2. Seleccione **Use un servidor proxy para obtener acceso al servicio Operations Management Suite**. Después, escriba la dirección IP del servidor de OMS Gateway o la dirección IP virtual del equilibrador de carga de red. Asegúrese de que su prefijo es `http://`.

    ![Operations Manager: dirección de servidor proxy](./media/log-analytics-oms-gateway/scom02.png)

3. Seleccione **Finalizar**. El grupo de administración de Operations Manager está ahora configurado para comunicarse mediante el servidor de puerta de enlace con el servicio Log Analytics.

### <a name="configure-operations-manager-specific-agents-use-proxy-server"></a>Configuración de Operations Manager: determinados agentes usan el servidor proxy
En entornos grandes o complejos, es posible que desee tener solo determinados servidores (o grupos) que utilicen el servidor de OMS Gateway. En estos servidores, no se puede actualizar el agente Operations Manager directamente, ya que el valor global para el grupo de administración sobrescribe este valor. En su lugar, invalide la regla que usó para insertar estos valores.  

> [!NOTE] 
> Esta misma técnica de configuración se puede utilizar para permitir el uso de varios servidores de OMS Gateway en su entorno. Por ejemplo, puede requerir que se especifiquen determinados servidores de OMS Gateway en cada región.
>  

1. Abra la consola de Operations Manager y seleccione el área de trabajo **Creación**.

2. En el área de trabajo Creación, seleccione **Reglas**. A continuación, seleccione el botón **Ámbito** de la barra de herramientas de Operations Manager. Si este botón no está disponible, compruebe que tiene un objeto, y no una carpeta, seleccionado en el panel **Supervisión**. El cuadro de diálogo **Objetos de módulo de administración de ámbito** muestra una lista de clases, grupos u objetos de destino comunes. 

3. En el campo **Buscar**, escriba **Servicio de mantenimiento**. Selecciónelo en la lista. Seleccione **Aceptar**.  

4. Busque la **Regla de configuración de proxy de Advisor**. En la barra de herramientas de la consola de operaciones, seleccione **Invalidaciones**. Después, vaya a **Invalidar la regla\Para un objeto de clase específico: Servicio de mantenimiento**. 

5. A continuación, seleccione un objeto específico en la lista. 

6. Si lo desea, puede crear un grupo personalizado que incluya el objeto de servicio de mantenimiento de los servidores a los que desea aplicar esta invalidación. Después, aplique la invalidación a ese grupo.

7. En el cuadro de diálogo **Propiedades de invalidación**, haga clic para poner una marca de verificación en la columna **Invalidar** junto al parámetro **WebProxyAddress**.  En el campo **Valor de invalidación**, escriba la dirección URL del servidor de OMS Gateway y asegúrese de que empieza por el prefijo `http://`.  

    >[!NOTE]
    > No es necesario habilitar la regla. Esta ya se administra automáticamente con una invalidación incluida en el módulo de administración de reemplazo de referencia segura de Microsoft System Center Advisor dirigido al grupo de servidores de supervisión de Microsoft System Center Advisor.
    >   

8. Seleccione un módulo de administración de la lista **Seleccionar módulo de administración de destino** o cree un nuevo módulo de administración no sellado seleccionando **Nuevo**. 

9. Cuando haya completado los cambios, seleccione **Aceptar**. 

### <a name="configure-the-oms-gateway-for-automation-hybrid-runbook-workers"></a>Configuración de OMS Gateway para Hybrid Runbook Workers de Automation
Si tiene Hybrid Runbook Workers de Automation en su entorno, los pasos siguientes proporcionan soluciones temporales manuales para configurar OMS Gateway para que sea compatible con ellos.

En los pasos siguientes, es preciso saber en qué región de Azure reside la cuenta de Automation. Para encontrar la ubicación, siga estos pasos:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).
2. Seleccione el servicio Azure Automation.
3. Seleccione la cuenta de Azure Automation apropiada.
4. En **Ubicación** puede ver la región de la cuenta.

    ![Azure Portal: Ubicación de la cuenta de Automation](./media/log-analytics-oms-gateway/location.png)  

Utilice las siguientes tablas para identificar la dirección URL de cada ubicación:

**Direcciones URL del servicio de datos del tiempo de ejecución del trabajo**

| **Ubicación** | **URL** |
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

| **Ubicación** | **URL** |
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

Si el equipo se registra automáticamente como Hybrid Runbook Worker para que las revisiones se apliquen mediante la solución Update Management, siga estos pasos:

1. Agregue las direcciones URL del servicio de datos en tiempo de ejecución del trabajo a la lista de hosts permitidos de OMS Gateway. Por ejemplo, escriba lo siguiente: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

2. Reinicie el servicio de OMS Gateway mediante el siguiente cmdlet de PowerShell: `Restart-Service OMSGatewayService`

Si el equipo está incorporado en Azure Automation mediante el cmdlet de registro de Hybrid Runbook Worker, siga estos pasos:

1. Agregue la dirección URL del registro del servicio Agente en la lista de hosts permitidos de OMS Gateway. Por ejemplo, escriba lo siguiente: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`

2. Agregue las direcciones URL del servicio de datos en tiempo de ejecución del trabajo a la lista de hosts permitidos de OMS Gateway. Por ejemplo, escriba lo siguiente: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

3. Reinicie el servicio de OMS Gateway: `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets de PowerShell útiles
Los cmdlets pueden ayudarle a completar las tareas necesarias para actualizar la configuración de OMS Gateway. Antes de usarlos, no olvide realizar los pasos siguientes:

1. Instalar OMS Gateway (MSI).
2. Abra una ventana de la consola de PowerShell.
3. Para importar el módulo, escriba este comando: `Import-Module OMSGateway`.
4. Si no se ha producido ningún error en el paso anterior, el módulo se ha importado correctamente y se pueden usar los cmdlets. Escriba `Get-Module OMSGateway`.
5. Después de realizar los cambios mediante los cmdlets, asegúrese de reiniciar el servicio OMS Gateway.

Un error en el paso 3 indica que el módulo no se ha importado. El error puede producirse si PowerShell no puede encontrar el módulo. Es posible encontrarlo en la ruta de instalación de OMS Gateway: *C:\Archivos de programa\Microsoft OMS Gateway\PowerShell*.

| **Cmdlet** | **Parámetros** | **Descripción** | **Ejemplo** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Clave |Obtiene la configuración del servicio |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Clave (se requiere) <br> Valor |Cambia la configuración del servicio |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Obtiene la dirección del proxy de retransmisión (ascendente). |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Dirección<br> Nombre de usuario<br> Password |Establece la dirección (y las credenciales) del proxy de retransmisión (ascendente). |1. Establezca un proxy de retransmisión y la credencial:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Establezca un proxy de retransmisión que no necesite autenticación: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Desactive la configuración del proxy de retransmisión:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Obtiene el host permitido actualmente (solo el host permitido configurado localmente; no se incluyen los hosts permitidos que se descargan automáticamente). |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (obligatorio) |Agrega el host a la lista de permitidos |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (obligatorio) |Quita el host de la lista de permitidos |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Asunto (obligatorio) |Agrega el asunto del certificado de cliente a la lista de permitidos |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Asunto (obligatorio) |Quita el asunto del certificado de cliente de la lista de permitidos |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Obtiene los asuntos de certificado de cliente permitidos actualmente (solo los asuntos permitidos configurados localmente; no se incluyen los asuntos permitidos que se descargan automáticamente). |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>solución de problemas
Para recopilar eventos registrados por la puerta de enlace, debe tener también instalado el agente de OMS.

![Visor de eventos: registro de OMS Gateway](./media/log-analytics-oms-gateway/event-viewer.png)

**Identificadores de evento y descripciones de OMS Gateway**

La siguiente tabla muestra los identificadores de evento y las descripciones de los eventos del registro de OMS Gateway:

| **Id** | **Descripción** |
| --- | --- |
| 400 |Cualquier error de la aplicación que no tiene un identificador específico. |
| 401 |Configuración errónea. Por ejemplo: listenPort = "text", en lugar de un entero. |
| 402 |Excepción al analizar los mensajes de protocolo de enlace TLS. |
| 403 |Error de red. Por ejemplo: no se puede conectar al servidor de destino. |
| 100 |Información general. |
| 101 |El servicio se ha iniciado. |
| 102 |El servicio se ha detenido. |
| 103 |Se ha recibido un comando HTTP CONNECT del cliente. |
| 104 |No es un comando HTTP CONNECT. |
| 105 |El servidor de destino no está en la lista de permitidos o el puerto de destino no es un puerto seguro (443). <br> <br> Asegúrese de que el agente MMA del servidor de puerta de enlace y los agentes que se comunican con la puerta de enlace están conectados a la misma área de trabajo de Log Analytics. |
| 105 |ERROR TcpConnection – Invalid Client certificate: CN=Gateway <br><br> Asegúrese de que: <br>    <br> - Utiliza una puerta de enlace con el número de versión 1.0.395.0, o uno superior. <br> - Tanto el agente MMA del servidor de puerta de enlace como los agentes que se comunican con la puerta de enlace están conectados a la misma área de trabajo de Log Analytics. |
| 106 |La puerta de enlace de OMS solo admite TLS 1.0, TLS 1.1 y 1.2.  No admite SSL. Para las versiones de protocolo TLS/SSL no admitidas, la puerta de enlace de OMS genera el identificador de evento 106.|
| 107 |Se ha comprobado la sesión de TLS. |

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

Para solicitar asistencia, seleccione el signo de interrogación en la esquina superior derecha del portal. Seleccione **Nueva solicitud de soporte técnico**. Complete el formulario de solicitud de soporte técnico nuevo.

![Nueva solicitud de soporte](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>Pasos siguientes
[Agregue orígenes de datos](log-analytics-data-sources.md) para recopilar datos de los orígenes conectados y almacénelos en el área de trabajo de Log Analytics.
