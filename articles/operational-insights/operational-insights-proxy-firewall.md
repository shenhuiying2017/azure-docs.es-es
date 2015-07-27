<properties
   pageTitle="Configuración de proxy y firewall para Visión operativa"
   description="Obtenga información acerca de la configuración de proxy y firewall que debe realizar para el tipo de agente que usa con Visión operativa."
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

# Configuración de proxy y firewall para Visión operativa

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Las acciones necesarias para configurar el proxy y el firewall de Visión operativa son diferentes cuando usa Operations Manager y sus agentes y cuando usa los agentes de Microsoft Monitoring Agent que se conectan directamente a los servidores. Revise las secciones siguientes que correspondan al tipo de agente que usa.

## Configuración de los valores de proxy y firewall con Microsoft Monitoring Agent

Para que Microsoft Monitoring Agent se conecte y se registre con el servicio de Visión operativa, debe tener acceso al número de puerto de los dominios y a las direcciones URL. Si se usa un servidor proxy para realizar la comunicación entre el agente y el servicio de Visión operativa, deberá asegurarse de que es posible tener acceso a los recursos adecuados. Si usa un firewall para restringir el acceso a Internet, deberá configurarlo para que permita el acceso a Visión operativa. Las tablas siguientes muestran los puertos que necesita Visión operativa.

|**Recurso del agente**|**Puertos**|
|--------------|-----|
|*.ods.opinsights.azure.com|Port 443| |*.oms.opinsights.azure.com|Puerto 443|
|ods.systemcenteradvisor.com|Puerto 443|
|*.blob.core.windows.net/*|Puerto 443|

Puede utilizar el procedimiento siguiente para configurar el proxy para Microsoft Monitoring Agent mediante el Panel de Control. Deberá utilizar el procedimiento para cada servidor. Si tiene muchos servidores que necesite configurar, le resultará más fácil usar un script para automatizar este proceso. Puede utilizar el procedimiento siguiente para *configurar el proxy para Microsoft Monitoring Agent mediante el Panel de Control*.

### Para configurar el proxy para Microsoft Monitoring Agent mediante el Panel de control

1. Abra el **Panel de control**.

2. Abra **Microsoft Monitoring Agent**.

3. Haga clic en la pestaña **Configuración de proxy**.![Pestaña Configuración de proxy](./media/operational-insights-proxy-firewall/direct-agent-proxy.png)

4. Seleccione **Usar un servidor proxy** y escriba la dirección URL y el número de puerto, si uno es necesario, de forma similar al ejemplo mostrado. Si el servidor proxy requiere autenticación, escriba el nombre de usuario y la contraseña para acceder al servidor proxy.

Use el procedimiento siguiente para crear un script de PowerShell que pueda ejecutar para establecer la configuración de proxy de cada agente que se conecta directamente a los servidores.

### Para configurar el proxy para Microsoft Monitoring Agent mediante un script


- Copie el ejemplo siguiente, actualícelo con la información específica para su entorno, guárdelo con una extensión de nombre de archivo PS1 y, a continuación, ejecute el script en cada equipo que se conecta directamente al servicio Visión operativa.


```
param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

# First we get the health service configuration object.  We need to determine if we
# have the right update rollup with the API we need.  If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}


Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username;


Write-Output "Setting Proxy to ${ProxyDomainName} with proxy username of (${ProxyUserName})."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

## Configuración de proxy y firewall con Operations Manager

Para que el grupo de administración de Operations Manager se conecte y se registre con el servicio de Visión operativa, debe tener acceso al número de puerto de los dominios y las direcciones URL. Si se usa un servidor proxy para realizar la comunicación entre el servidor de administración de Operations Manager y el servicio de Visión operativa, deberá asegurarse de que es posible tener acceso a los recursos adecuados. Si usa un firewall para restringir el acceso a Internet, deberá configurarlo para que permita el acceso a Visión operativa. Las tablas siguientes muestran los puertos relacionados con estas tareas.

>[AZURE.NOTE]Algunos de los recursos siguientes mencionan a Advisor. Sin embargo, los recursos enumerados cambiarán en el futuro.

|**Recurso del servidor de administración**|**Puertos**|
|--------------|-----|
|*.ods.opinsights.azure.com|Port 443| |service.systemcenteradvisor.com|Port 443| |scadvisor.accesscontrol.windows.net|Port 443| |scadvisorservice.accesscontrol.windows.net|Port 443| |*.blob.core.windows.net/*|Puerto 443|
|data.systemcenteradvisor.com|Puerto 443|
|ods.systemcenteradvisor.com|Puerto 443|
|*.systemcenteradvisor.com|Port 443|


|**Recurso de la consola de Operations Manager y Visión operativa**|**Puertos**|
|---|---|
|*.systemcenteradvisor.com|Port 80 and 443| |*.live.com|Puerto 80 y 443|
|*.microsoftonline.com|Port 80 and 443| |login.windows.net|Port 80 and 443|


Utilice los procedimientos siguientes para registrar el grupo de administración de Operations Manager con el servicio de Visión operativa. Si tiene problemas de comunicación entre el grupo de administración y el servicio de Visión operativa, use los procedimientos de validación para solucionar los problemas de transmisión de datos al servicio de Visión operativa.

### Para solicitar excepciones para los extremos de servicio de Visión operativa

1. Utilice la información de la primera tabla anteriormente presentada para asegurarse de que es posible tener acceso a los recursos necesarios para el servidor de administración de Operations Manager a través de cualquier firewall que pudiera tener.

2. Use la información de la segunda tabla anteriormente presentada para asegurarse de que es posible tener acceso a los recursos necesarios para la consola de operaciones de Operations Manager y Visión operativa a través de cualquier firewall que pudiera tener.

3. Si usa un servidor proxy con Internet Explorer, asegúrese de que esté configurado y que funcione correctamente. Para comprobarlo, puede abrir una conexión web segura (https), por ejemplo [https://bing.com](https://bing.com). Si la conexión web segura no funciona en un explorador, probablemente no funcionará en la consola de administración de Operations Manager con los servicios web en la nube.

### Para configurar el servidor proxy en la consola de Operations Manager

1. Abra la consola de Operations Manager y seleccione el área de trabajo **Administración**.

2. Expanda **Visión operativa** y, a continuación, seleccione **Conexión a Vista operativa**.![Conexión a Visión operativa de Operations Manager](./media/operational-insights-proxy-firewall/proxy-om01.png)

3. En la vista Conexión a Vista operativa, haga clic en **Configurar servidor proxy**.![Configurar servidor proxy de Conexión a Visión operativa de Operations Manager](./media/operational-insights-proxy-firewall/proxy-om02.png)

4. En el asistente de configuración de Visión operativa: servidor proxy, seleccione **Uso de un servidor proxy para tener acceso al servicio web de Visión operativa** y, a continuación, escriba la dirección URL con el número de puerto, por ejemplo, **http://myproxy:80**.![Dirección proxy de Visión operativa de Operations Manager](./media/operational-insights-proxy-firewall/proxy-om03.png)


### Para especificar las credenciales si el servidor proxy requiere autenticación

1. Abra la consola de Operations Manager y seleccione el área de trabajo **Administración**.

2. En **RunAs Configuration**, seleccione **Perfiles**.

3. Abra el perfil de **proxy del perfil Ejecutar como de System Center Advisor**. ![imagen del perfil de ejecutar como proxy de System Center Advisor](./media/operational-insights-proxy-firewall/proxyacct1.png)
4. En el asistente de perfil de identificación, haga clic en **Agregar** para usar una cuenta de ejecución. Puede crear una nueva cuenta de ejecución o usar una existente. Esta cuenta debe contar con los permisos suficientes para pasar a través del servidor proxy.![imagen del asistente para el perfil de ejecutar como](./media/operational-insights-proxy-firewall/proxyacct2.png)

5. Para establecer la cuenta que se va a administrar, elija **una clase seleccionada, un grupo o un objeto** para abrir el cuadro de búsqueda de objetos. ![imagen del asistente para el perfil de ejecutar como](./media/operational-insights-proxy-firewall/proxyacct2-1.png)
6. Busque y seleccione **Servidores de administración de Operations Manager**. ![imagen del cuadro de búsqueda de objetos](./media/operational-insights-proxy-firewall/proxyacct3.png)
7. Haga clic en **Aceptar** para cerrar el cuadro para agregar una ejecución como cuenta ![imagen del asistente para el perfil de ejecutar como](./media/operational-insights-proxy-firewall/proxyacct4.png)
8. Complete el asistente y guarde los cambios.![imagen del asistente para el perfil de ejecutar como](./media/operational-insights-proxy-firewall/proxyacct5.png)


### Para configurar el servidor proxy en cada servidor de administración para WinHTTP

1. Si Operations Manager no se ha actualizado con el paquete acumulativo 3 de Operations Manager 2012 R2 o con el paquete acumulativo 7 de Operations Manager 2012 SP1 o posterior, abra una ventana de símbolo del sistema como administrador en el servidor de administración de Operations Manager. De lo contrario, no necesita usar este procedimiento.

2. Escriba **netsh winhttp set proxy myproxy:80**.

3. Cierre la ventana del símbolo del sistema y reinicie el servicio de administración de System Center (HealthService).

4. Realice el paso 2 en cada servidor de administración del grupo de administración.

### Para configurar el servidor proxy en cada servidor de administración

1. Abra la consola de Operations Manager y seleccione el área de trabajo **Administración**.

2. Seleccione **Administración de dispositivos** y, a continuación, haga clic en **Servidores de administración**.

3. Haga clic con el botón derecho en el nombre de cada servidor de administración, haga clic en **Propiedades** y, a continuación, establezca la información en la pestaña **Configuración de proxy**.![Pestaña Configuración de proxy](./media/operational-insights-proxy-firewall/proxyms.png)

### Para validar la descarga de los módulos de administración de Visión operativa

- Si agregó soluciones mediante el uso de Visión operativa, puede verlas en la consola de Operations Manager como módulos de administración en **Administración**. Busque *System Center Advisor* para encontrarlos rápidamente. ![módulos de administración descargados](./media/operational-insights-proxy-firewall/mpdownloaded.png)
- También puede comprobar los módulos de administración de Visión operativa si usa los siguientes comandos de Windows PowerShell en el servidor de administración de Operations Manager:

        get-scommanagementpack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken

        get-scommanagementpack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version | ft

### Para validar que Operations Manager envía datos al servicio de Visión operativa

1. En el servidor de administración de Operations Manager, abra Monitor de rendimiento (perfmon.exe) y seleccione **Monitor de rendimiento**.

2. Haga clic en **Agregar** y, a continuación, seleccione **Grupos de administración de Servicio de mantenimiento**.

3. Agregue todos los contadores que comienzan con **HTTP**.![agregar contadores](./media/operational-insights-proxy-firewall/sendingdata1.png)
4. Si la configuración de Operations Manager es correcta, verá actividad para los contadores de Administración de Servicio de mantenimiento para eventos y otros elementos de datos, según los módulos de administración que agregó en Visión operativa y la directiva de recopilación de registros configurada.![Monitor rendimiento mostrando actividad](./media/operational-insights-proxy-firewall/sendingdata2.png)

<!---HONumber=July15_HO3-->