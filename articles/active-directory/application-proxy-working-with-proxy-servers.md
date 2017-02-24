---
title: Trabajo con servidores proxy locales existentes y Azure AD | Microsoft Docs
description: "Se explica cómo trabajar con servidores proxy locales existentes."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2fab64ef8bf07532c4fd2fff4f4af68dc0b44b45
ms.openlocfilehash: 659edc19c9f043d6a388f1bb293c72e379fa6e4a


---

# <a name="work-with-existing-on-premises-proxy-servers"></a>Trabajo con servidores proxy locales existentes

En este artículo se explica cómo configurar el conector del Proxy de aplicación para que funcione con servidores proxy de salida. Está destinado a clientes con entornos de red que tienen servidores proxy en la actualidad.

Empezaremos examinando estos escenarios de implementación principales:
* Configuración de conectores para omitir los proxy de salida locales.
* Configuración de conectores para usar a un proxy de salida para obtener acceso al Proxy de aplicación de Azure AD.

Para obtener más información acerca de cómo funcionan los conectores, consulte [Provisión de acceso remoto seguro a aplicaciones locales](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="configure-your-connectors"></a>Configuración de los conectores

El servicio de conector principal usa Service Bus de Azure para ocuparse de algunas de las comunicaciones subyacentes con el servicio de Proxy de aplicación de Azure AD. Service Bus aporta requisitos de configuración adicionales.

Para obtener información acerca de cómo solucionar problemas de conectividad de Azure AD, consulte [How to troubleshoot Azure AD Application Proxy connectivity problems](https://blogs.technet.microsoft.com/applicationproxyblog/2015/03/24/how-to-troubleshoot-azure-ad-application-proxy-connectivity-problems) (Solución de problemas de conectividad del Proxy de aplicación de Azure AD). 

## <a name="configure-the-outbound-proxy"></a>Configuración del proxy de salida

Si tiene un proxy de salida en su entorno, asegúrese de que la cuenta que realiza la instalación está correctamente configurada para usar el proxy de salida. Como el programa de instalación se ejecuta en el contexto del usuario que realiza la instalación, es posible hacerlo mediante Microsoft Edge u otro explorador de Internet. 

Para configurar las opciones de proxy con Microsoft Edge, vaya a Configuración, Ver configuración avanzada, Abrir la configuración de proxy, Configuración manual del proxy. Establezca "Utilizar un servidor proxy" en "Activado", active "No usar el servidor proxy para direcciones locales (intranet)" y, a continuación, cambie la dirección y el puerto para reflejar su servidor proxy local.

Rellene los valores de proxy necesarios tal y como se muestra en el cuadro de opciones inferior.

 ![Direcciones locales de omisión de Azure AD](./media/application-proxy-working-with-proxy-servers/proxy-bypass-local-addresses.png)
 
## <a name="bypass-outbound-proxies"></a>Omisión de servidores proxy de salida

De forma predeterminada, los componentes del sistema operativo subyacentes utilizados por el conector para realizar solicitudes salientes intentan localizar automáticamente un servidor proxy en la red mediante la detección automática de proxy web, si está habilitada en el entorno.

Normalmente, para ello se lleva a cabo una búsqueda de DNS para wpad.domainsuffix. Si se resuelve en DNS, se realizará una solicitud HTTP a la dirección IP para wpad.dat que será el script de configuración de proxy en su entorno. El conector, a continuación, lo usará para seleccionar un servidor proxy de salida. Sin embargo, es posible que el tráfico de conector no pase aún porque se requiera una configuración adicional en el servidor proxy. 

En la siguiente sección, trataremos los pasos de configuración necesarios en el proxy de salida para hacer que el flujo de tráfico pase por él. Pero primero, vamos a resolver cómo puede configurar el conector para omitir el proxy local a fin de garantizar que utiliza una conexión directa a los servicios de Azure. Esta es la manera recomendada de proceder (siempre que lo permita la directiva de red), ya que implica que tendrá una configuración menos que mantener.

Para deshabilitar el uso de proxy de salida para el conector, edite el archivo C:\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config y agregue la sección [system.net] que se muestra en el ejemplo de código siguiente:

```xml
 <?xml version="1.0" encoding="utf-8" ?>
<configuration>
<system.net>
<defaultProxy enabled="false"></defaultProxy>
</system.net>
 <runtime>
<gcServer enabled="true"/>
  </runtime>
  <appSettings>
<add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```
Para asegurarse de que el servicio de actualización del conector también omite el proxy, realice un cambio similar en el archivo ApplicationProxyConnectorUpdaterService.exe.config que se encuentra en C:\Archivos de programa\Microsoft AAD aplicación Proxy conector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

Asegúrese de realizar copias de los archivos originales por si tuviera que revertir a los archivos .config predeterminados. 

## <a name="use-the-outbound-proxy-server"></a>Uso del servidor proxy saliente

Como se mencionó anteriormente, en algunos entornos de clientes se requiere que todo el tráfico saliente vaya sin excepciones a través de un proxy de salida. Por lo tanto, no se contempla la opción de omitir el proxy.

Puede configurar el tráfico del conector de manera que vaya a través del proxy de salida tal y como se muestra a continuación.

 ![Direcciones locales de omisión de Azure AD](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

Como solo se tiene tráfico saliente, no es necesario configurar el equilibrio de carga entre los conectores ni configurar el acceso entrante a través de los firewalls.

En cualquier caso, tendrá que completar los siguientes pasos:
1. Configure el conector y el servicio de actualización para que vayan a través del proxy de salida.
2. Configure las opciones del proxy para permitir las conexiones con el servicio del Proxy de aplicación de Azure AD.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Paso 1: Configurar el conector y los servicios relacionados para que vayan a través del proxy de salida

Tal como se explicó anteriormente, si WPAD está habilitado en el entorno y se ha configurado correctamente, el conector detectará automáticamente el servidor del proxy de salida y tratará de usarlo. Sin embargo, puede configurar explícitamente el conector para que vaya a través de un proxy de salida. 

Para ello, edite el archivo C:\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config y agregue la sección [system.net] que se muestra en el ejemplo de código siguiente:

```xml
 <?xml version="1.0" encoding="utf-8" ?>
<configuration>
<system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
</system.net>
  <runtime>
     <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

>[!NOTE]
>Cambie _proxyserver:8080_ para que refleje el nombre del servidor proxy local o la dirección IP y el puerto en el que está escuchando.
>

A continuación, debe configurar el servicio de actualización de conector para usar al proxy; para ello, haga un cambio similar en el archivo que se encuentra en C:\Archivos de programa\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

###<a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Paso 2: Configurar el Proxy para permitir el tráfico desde el conector y que los servicios relacionados pasen a través de él

Hay cuatro aspectos que se deben tener en cuenta en el servidor proxy saliente:
* Las reglas de salida del proxy
* La autenticación del proxy
* Los puertos del proxy
* La inspección de SSL

#### <a name="2a-proxy-outbound-rules"></a>2.A: Reglas de salida del proxy
Permita el acceso a los siguientes puntos de conexión para servicio de conector:

* *.msappproxy.net 
* *.servicebus.microsoft.net 

Para el registro inicial, permita el acceso a los siguientes puntos de conexión:

* login.windows.net 
* login.microsoftonline.com. 

Los canales subyacentes del control de Service Bus utilizados por el servicio de conector además requieren conectividad con direcciones IP específicas. Tenga en cuenta que estamos trabajando con el equipo de Service Bus para mover a un FQDN en su lugar. Sin embargo, por ahora hay dos opciones: 
 
* Permitir el acceso de salida del conector a todos los destinos, o
* Permitir el acceso de salida del conector a los intervalos de direcciones IP del centro de datos de Azure que figuran en https://www.microsoft.com/en-gb/download/details.aspx?id=41653.

>[!NOTE]
>La complejidad que plantea el uso de la lista de intervalos de direcciones IP del centro de datos de Azure es que esta se actualiza cada semana, por lo que deberá determinar un proceso para garantizar que las reglas de acceso se actualicen a ese ritmo.
>

#### <a name="2b-proxy-authentication"></a>2.B: Autenticación del proxy

Actualmente no se admite la autenticación del proxy. Nuestra recomendación actual es permitir el acceso anónimo de conector a los destinos de Internet.

#### <a name="2c-proxy-ports"></a>2.C: Puertos del proxy

El conector realiza las conexiones salientes basadas en SSL con el método CONNECT. Básicamente, se establece un túnel a través del proxy de salida. Algunos servidores proxy de forma predeterminada solo permiten la tunelización de salida a puertos SSL estándar como 443. Si este es el caso, el servidor proxy deberá configurarse para permitir la tunelización a puertos adicionales.

Configure el servidor proxy para permitir la tunelización a los puertos no estándar 8080, 9090, 9091 y 10100-10120.

>[!NOTE]
>Service Bus, si se ejecuta a través de HTTPS, usa el puerto 443. Sin embargo, de forma predeterminada, Service Bus intentará crear conexiones TCP directas y recurrirá a HTTPS solo si se produce un error en la conectividad directa. 
> 

Para asegurarse de que el tráfico de Service Bus también se envía a través del servidor proxy de salida, debe asegurarse de que el conector no puede conectar directamente con los servicios de Azure para los puertos 9350, 9352 y 5671.

#### <a name="2d-ssl-inspection"></a>2.D: Inspección de SSL
No utilice la inspección de SSL para el tráfico de conector, ya que le causará problemas. 

Ya ha terminado. Ahora debería ver todo el tráfico pasando a través del proxy. Si surgen problemas, los siguientes pasos deberían servirle de ayuda para solucionarlos.

### <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Solución de problemas del proxy del conector y de conectividad del servicio

La mejor manera de identificar y solucionar problemas de conectividad del conector es realizar una captura de red en el servicio de conector cuando este se inicia. Esta tarea puede resultar desalentadora; así pues, veamos algunas breves sugerencias sobre cómo capturar y filtrar seguimientos de red.

Puede usar la herramienta de supervisión que prefiera. En este artículo, hemos usado 	Microsoft Network Monitor 3.4, que puede descargar en https://www.microsoft.com/en-us/download/details.aspx?id=4865.

Los ejemplos y los filtros que se utilizan a continuación son específicos de Network Monitor, pero los principios se pueden aplicar a cualquier herramienta de análisis.

### <a name="take-a-capture-using-microsoft-network-monitor"></a>Toma de una captura con Microsoft Network Monitor

Para iniciar una captura, abra Network Monitor y haga clic en New Capture (Nueva captura). A continuación, presione el botón Start (Inicio) para comenzar.

 ![AzureAD Network Monitor](./media/application-proxy-working-with-proxy-servers/network-capture.png)

Después de completar una captura, haga clic en el botón Stop (Detener) para finalizar la captura.

### <a name="take-a-capture-of-connector-traffic"></a>Toma de una captura del tráfico de conector

Para solucionar el problema inicial, realice los pasos siguientes: 

1. Desde services.msc, detenga el servicio de conector del Proxy de aplicación de Microsoft AAD (como se muestra a continuación).
2. Inicie la captura de red.
3. Inicie el servicio de conector del Proxy de aplicación de Microsoft AAD.
4. Detenga la captura de red.

 ![AzureAD Network Monitor](./media/application-proxy-working-with-proxy-servers/services-local.png)

### <a name="look-at-the-connector-to-proxy-server-requests"></a>Búsqueda de las solicitudes del servidor proxy en el conector

Ahora que tiene una captura de red, puede filtrarla. La clave para buscar el seguimiento es comprender cómo se filtra la captura.

Para ello, puede usar un filtro como el siguiente (donde 8080 es el puerto del servicio de proxy): 

(http.Request o http.Response) y tcp.port==8080

Si escribe este filtro en la ventana Mostrar filtro y selecciona Aplicar, se filtrará el tráfico capturado en función del criterio establecido.

El filtro anterior mostrará solo las solicitudes y respuestas HTTP hacia o desde el puerto de proxy. Para un inicio de conector en el que el conector está configurado para utilizar un servidor proxy, habría que utilizar algo similar a esto:

 ![AzureAD Network Monitor](./media/application-proxy-working-with-proxy-servers/http-requests.png)

Ahora está buscando específicamente las solicitudes de CONNECT que muestren que estamos hablando con el servidor proxy. Si se realiza correctamente, obtendrá una respuesta HTTP OK (200).

Si ve otros códigos de respuesta, como 407 o 502, el proxy requiere autenticación o no permite el tráfico por algún otro motivo. En este momento debería ponerse en contacto con el equipo de soporte técnico del servidor proxy.

### <a name="identify-failed-tcp-connection-attempts"></a>Identificación de los intentos de conexión de TCP con error

El otro escenario habitual que quizá le interese es cuando el conector está intentando conectar directamente pero no lo consigue. 

Otro filtro de Network Monitor que le ayuda a identificar esta situación fácilmente es:

property.TCPSynRetransmit

Un paquete SYN es el primer paquete enviado para establecer una conexión TCP. Si esto no devuelve una respuesta, se intenta de nuevo el envío de SYN. El filtro anterior le permite ver los paquetes SYN cuya transmisión se ha vuelto a intentar. A continuación, puede comprobar si estos se corresponden con cualquier otro tráfico relacionado del conector. 

En el ejemplo siguiente se muestra un intento de conexión con error al puerto de Service Bus 9352:

 ![AzureAD Network Monitor](./media/application-proxy-working-with-proxy-servers/failed-connection-attempt.png)

Si ve algo parecido a la respuesta anterior, el conector está intentando comunicarse directamente con el servicio de Service Bus de Azure. Si el conector debería estar realizando conexiones directas a los servicios de Azure, esto es un claro indicio de que tiene un problema de red o firewall.

>[!NOTE]
>Si ha establecido su configuración para usar un servidor proxy, puede ser que el Service Bus esté intentando realizar una conexión TCP directa antes de cambiar para intentar conectarse a través de HTTPS, así que téngalo en cuenta.
>

El análisis de seguimiento de red no está indicado para todos los usuarios. Sin embargo, puede ser una herramienta muy valiosa para obtener información rápida sobre lo que ocurre con la red. 

Si sigue experimentando problemas de conectividad del conector, cree una incidencia con nuestro equipo de soporte técnico, que estará encantado de ayudarle a solucionarlos.

Para obtener información sobre cómo resolver errores del conector del Proxy de aplicación, consulte [Solucionar problemas de Proxy de aplicación](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-troubleshoot).

## <a name="next-steps"></a>Pasos siguientes

[Descripción de los conectores del Proxy de aplicación de Azure AD](application-proxy-understand-connectors.md)<br>
[Cómo instalar de forma silenciosa el conector de Proxy de aplicación de Azure AD](active-directory-application-proxy-silent-installation.md)







<!--HONumber=Feb17_HO1-->


