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
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 64d06a67ee5480e6bdbac2f6745ea32faa2cf003
ms.lasthandoff: 04/17/2017


---

# <a name="work-with-existing-on-premises-proxy-servers"></a>Trabajo con servidores proxy locales existentes

En este artículo se explica cómo configurar el conector del Proxy de aplicación de Azure Active Directory para que funcione con servidores proxy de salida. Está destinado a clientes con entornos de red que tienen servidores proxy en la actualidad.

Empezaremos examinando estos escenarios de implementación principales:
* Configuración de conectores para omitir los proxy de salida locales.
* Configuración de conectores para usar un proxy de salida para obtener acceso al Proxy de aplicación de Azure AD.

Para obtener más información acerca de cómo funcionan los conectores, consulte [Provisión de acceso remoto seguro a aplicaciones locales](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="configure-your-connectors"></a>Configuración de los conectores

El servicio de conector principal usa Service Bus de Azure para ocuparse de algunas de las comunicaciones subyacentes con el servicio de Proxy de aplicación de Azure AD. Service Bus aporta requisitos de configuración adicionales.

Para obtener información acerca de cómo solucionar problemas de conectividad de Azure AD, consulte [How to troubleshoot Azure AD Application Proxy connectivity problems](https://blogs.technet.microsoft.com/applicationproxyblog/2015/03/24/how-to-troubleshoot-azure-ad-application-proxy-connectivity-problems) (Solución de problemas de conectividad del Proxy de aplicación de Azure AD).

## <a name="configure-the-outbound-proxy"></a>Configuración del proxy de salida

Si tiene un proxy de salida en su entorno, asegúrese de que la cuenta que realiza la instalación está correctamente configurada para usarlo. Puesto que el programa de instalación se ejecuta en el contexto del usuario que lleva a cabo la instalación, puede comprobar la configuración mediante Microsoft Edge u otro explorador de Internet.

Para configurar el proxy con Microsoft Edge:

1. Vaya a **Configuración** > **Ver configuración avanzada** > **Abrir la configuración de proxy** > **Configuración manual del proxy**.
2. Establezca **Utilizar un servidor proxy** en **Activado**, active la casilla **No usar el servidor proxy para direcciones locales (intranet)** y cambie la dirección y el puerto para reflejar su servidor proxy local.
3. Rellene la configuración del proxy necesaria.

   ![Cuadro de diálogo de configuración del proxy](./media/application-proxy-working-with-proxy-servers/proxy-bypass-local-addresses.png)

## <a name="bypass-outbound-proxies"></a>Omisión de servidores proxy de salida

De forma predeterminada, los componentes del sistema operativo subyacentes utilizados por el conector para realizar solicitudes salientes intentan localizar automáticamente un servidor proxy en la red. Usan la detección automática de proxy web, si está habilitada en el entorno.

Los componentes del sistema operativo intentan localizar un servidor proxy llevando a cabo una búsqueda de DNS para wpad.domainsuffix. Si esto se resuelve en DNS, se realiza una solicitud HTTP para la dirección IP para wpad.dat. Esta solicitud se convierte en el script de configuración de proxy en su entorno. El conector lo usará para seleccionar un servidor proxy de salida. Sin embargo, es posible que el tráfico del conector no pase aún porque se requiera una configuración adicional en el servidor proxy.

En la siguiente sección, trataremos los pasos de configuración necesarios en el proxy de salida para hacer que el flujo de tráfico pase por él. Pero primero, vamos a resolver cómo puede configurar el conector para omitir el proxy local a fin de garantizar que utiliza una conexión directa a los servicios de Azure. Esto es lo que se recomienda (siempre que lo permita la directiva de red), ya que significa que tendrá una configuración menos que mantener.

Para deshabilitar el uso de proxy de salida para el conector, edite el archivo C:\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config y agregue la sección [system.net] que se muestra en este ejemplo de código:

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
Para asegurarse de que el servicio de actualización de los conectores también omite el proxy, realice un cambio similar en el archivo ApplicationProxyConnectorUpdaterService.exe.config, que se encuentra en C:\Program Files\Microsoft AAD App Proxy Connector Updater.

Asegúrese de realizar copias de los archivos originales por si fuera necesario revertir a los archivos .config predeterminados.

## <a name="use-the-outbound-proxy-server"></a>Uso del servidor proxy saliente

Como se mencionó anteriormente, algunos entornos de clientes requieren que todo el tráfico saliente vaya sin excepciones a través de un proxy de salida. Por lo tanto, no se contempla la opción de omitir el proxy.

Puede configurar el tráfico del conector de manera que vaya a través del proxy de salida, tal y como se muestra a continuación.

 ![Configuración del tráfico del conector para pasar a través de un proxy de salida para acceder al Proxy de aplicación de Azure AD](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

Como solo se tiene tráfico saliente, no es necesario configurar el equilibrio de carga entre los conectores ni configurar el acceso entrante a través de los firewalls.

En cualquier caso, tiene que realizar los siguientes pasos:
1. Configurar el servicio actualización de los conectores para que pase a través del proxy de salida.
2. Configurar las opciones del proxy para permitir las conexiones con el servicio del Proxy de aplicación de Azure AD.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Paso 1: Configurar el conector y los servicios relacionados para que vayan a través del proxy de salida

Tal como se explicó anteriormente, si WPAD está habilitado en el entorno y se ha configurado correctamente, el conector detectará automáticamente el servidor del proxy de salida y tratará de usarlo. Sin embargo, puede configurar explícitamente el conector para que vaya a través de un proxy de salida.

Para ello, edite el archivo C:\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config y agregue la sección [system.net] que se muestra en este ejemplo de código:

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

A continuación, configure el servicio de actualización de los conectores para que use el proxy; para ello, haga un cambio similar en el archivo que se encuentra en C:\Archivos de programa\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Paso 2: Configurar el proxy para permitir que pase el tráfico desde el conector y los servicios relacionados

Hay cuatro aspectos que se deben tener en cuenta en el servidor proxy saliente:
* Las reglas de salida del proxy
* La autenticación del proxy
* Los puertos del proxy
* La inspección de SSL

#### <a name="proxy-outbound-rules"></a>Las reglas de salida del proxy
Permita el acceso a los siguientes puntos de conexión para servicio de conector:

* *.msappproxy.net
* *.servicebus.microsoft.net

Para el registro inicial, permita el acceso a los siguientes puntos de conexión:

* login.windows.net
* login.microsoftonline.com

Los canales subyacentes del control de Service Bus utilizados por el servicio de conector también requieren conectividad con direcciones IP específicas. Hasta que Service Bus cambie a un nombre de dominio completo, hay dos opciones:

* Permitir el acceso de salida del conector a todos los destinos.
* Permitir el acceso de salida del conector a los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/en-gb/download/details.aspx?id=41653).

>[!NOTE]
>El desafío de usar la lista de intervalos IP del centro de datos de Azure es que se actualiza semanalmente. Es necesario colocar un proceso para garantizar que las reglas de acceso se actualizan en consecuencia.
>

#### <a name="proxy-authentication"></a>La autenticación del proxy

Actualmente no se admite la autenticación del proxy. Nuestra recomendación actual es permitir el acceso anónimo de conector a los destinos de Internet.

#### <a name="proxy-ports"></a>Los puertos del proxy

El conector realiza las conexiones salientes basadas en SSL con el método CONNECT. Básicamente, este método configura un túnel a través del proxy de salida. De forma predeterminada, algunos servidores proxy solo permiten la tunelización de salida a puertos SSL estándar como 443. Si este es el caso, el servidor proxy debe configurarse para permitir la tunelización a puertos adicionales.

Configure el servidor proxy para permitir la tunelización a los puertos no estándar 8080, 9090, 9091 y 10100-10120.

>[!NOTE]
>Service Bus, si se ejecuta a través de HTTPS, usa el puerto 443. Sin embargo, de forma predeterminada, Service Bus intenta crear conexiones TCP directas y recurrir a HTTPS solo si se produce un error en la conectividad directa.
>

Para asegurarse de que el tráfico de Service Bus también se envía a través del servidor proxy de salida, debe asegurarse de que el conector no puede conectar directamente con los servicios de Azure para los puertos 9350, 9352 y 5671.

#### <a name="ssl-inspection"></a>La inspección de SSL
No utilice la inspección de SSL para el tráfico del conector, ya que le causará problemas.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Solución de problemas del proxy del conector y de conectividad del servicio
Ahora debería ver todo el tráfico pasando a través del proxy. Si tiene problemas, la siguiente información debería ayudar.

La mejor manera de identificar y solucionar problemas de conectividad del conector es realizar una captura de red en el servicio de conector cuando se inicia. Esta tarea puede resultar desalentadora; así pues, veamos algunas breves sugerencias sobre cómo capturar y filtrar seguimientos de red.

Puede usar la herramienta de supervisión que prefiera. En este artículo, hemos usado la versión 3.4 del Monitor de red de Microsoft. Puede [descargarlo de Microsoft](https://www.microsoft.com/download/details.aspx?id=4865).

Los ejemplos y los filtros que se utilizan en las siguientes secciones son específicos del Monitor de red, pero los principios se pueden aplicar a cualquier herramienta de análisis.

### <a name="take-a-capture-by-using-network-monitor"></a>Realización de una captura mediante el Monitor de red

Para iniciar una captura:

1. Abra el Monitor de red y haga clic en **New Capture** (Nueva captura).
2. Haga clic en el botón **Iniciar**.

   ![Ventana Monitor de red](./media/application-proxy-working-with-proxy-servers/network-capture.png)

Después de completar una captura, haga clic en el botón **Stop** (Detener) para finalizarla.

### <a name="take-a-capture-of-connector-traffic"></a>Toma de una captura del tráfico de conector

Para solucionar el problema inicial, realice los pasos siguientes:

1. Desde services.msc, detenga el servicio de conector del Proxy de aplicación de Azure AD.
2. Inicie la captura de red.
3. Inicie el servicio de conector del Proxy de aplicación de Azure AD.
4. Detenga la captura de red.

   ![Servicio de conector del Proxy de aplicación de Azure AD en services.msc](./media/application-proxy-working-with-proxy-servers/services-local.png)

### <a name="look-at-the-requests-from-the-connector-to-the-proxy-server"></a>Consulta las solicitudes del conector al servidor proxy

Ahora que tiene una captura de red, puede filtrarla. La clave para buscar el seguimiento es comprender cómo se filtra la captura.

Un filtro como el siguiente (donde 8080 es el puerto del servicio de proxy):

**(http.Request o http.Response) y tcp.port==8080**

Si escribe este filtro en la ventana **Mostrar filtro** y selecciona **Aplicar**, se filtrará el tráfico capturado en función del filtro.

El filtro anterior solo mostrará las solicitudes y respuestas HTTP hacia o desde el puerto proxy. Para un inicio de conector en el que el conector está configurado para utilizar un servidor proxy, habría que utilizar un filtro similar a este:

 ![Ejemplo de lista de las solicitudes y respuestas HTTP filtradas](./media/application-proxy-working-with-proxy-servers/http-requests.png)

Ahora está buscando específicamente las solicitudes de CONNECT que muestren que la comunicación con el servidor proxy. Si se realiza correctamente, obtendrá una respuesta HTTP OK (200).

Si ve otros códigos de respuesta, como 407 o 502, el proxy requiere autenticación o no permite el tráfico por algún otro motivo. En este momento, póngase en contacto con el equipo de soporte técnico del servidor proxy.

### <a name="identify-failed-tcp-connection-attempts"></a>Identificación de los intentos de conexión de TCP con error

El otro escenario habitual que quizá le interese es cuando el conector está intentando conectarse directamente, pero no lo consigue.

Otro filtro del Monitor de red que le ayuda a identificar este problema fácilmente es:

**property.TCPSynRetransmit**

Un paquete SYN es el primer paquete enviado para establecer una conexión TCP. Si este paquete no devuelve ninguna respuesta, se intenta de nuevo el envío de SYN. Puede utilizar el filtro anterior para ver las solicitudes de SYN retransmitidas. A continuación, puede comprobar si estas solicitudes SYN corresponden a cualquier tráfico relacionado con el conector.

En el ejemplo siguiente se muestra un intento de conexión con error al puerto de Service Bus 9352:

 ![Ejemplo de respuesta para un intento de conexión con errores](./media/application-proxy-working-with-proxy-servers/failed-connection-attempt.png)

Si ve algo parecido a la respuesta anterior, el conector está intentando comunicarse directamente con el servicio Azure Service Bus. Si el conector debería estar realizando conexiones directas a los servicios de Azure, esta respuesta es un claro indicio de que tiene un problema de red o firewall.

>[!NOTE]
>Si la configuración está establecida para usar un servidor proxy, esta respuesta puede significar que Service Bus está intentando realizar una conexión TCP directa antes de cambiar a intentar conectarse a través de HTTPS.
>

El análisis de seguimiento de red no está indicado para todos los usuarios. Sin embargo, puede ser una herramienta valiosa para obtener información rápida sobre lo que ocurre con la red.

Si sigue experimentando problemas de conectividad del conector, cree una incidencia con nuestro equipo de soporte técnico. El equipo puede ayudarle a solucionar otros problemas.

Para más información sobre cómo resolver errores del conector del Proxy de aplicación, consulte [Solucionar problemas de Proxy de aplicación](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-troubleshoot).

## <a name="next-steps"></a>Pasos siguientes

[Descripción de los conectores del Proxy de aplicación de Azure AD](application-proxy-understand-connectors.md)<br>
[Cómo instalar de forma silenciosa el conector de Proxy de aplicación de Azure AD](active-directory-application-proxy-silent-installation.md)

