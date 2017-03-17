---
title: "Descripción de los conectores del Proxy de aplicación de Azure AD | Microsoft Docs"
description: "Se explican los conceptos básicos acerca de los conectores del Proxy de aplicación de Azure AD."
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
ms.date: 02/22/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 41d6f678dba769cf7f949751da8cacf3df7f88c1
ms.lasthandoff: 03/03/2017


---

# <a name="understand-azure-ad-application-proxy-connectors"></a>Descripción de los conectores del Proxy de aplicación de Azure AD

En este artículo se describen los conectores, que son la salsa secreta del Proxy de aplicación de Azure AD. Son simples, fáciles de implementar y mantener y muy eficaces.

> [!NOTE]
> Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

## <a name="what-are-azure-ad-application-proxy-connectors"></a>¿Qué son los Conectores del Proxy de aplicación de Azure AD?
El Proxy de aplicación funciona después de instalar el servicio de Windows Server, denominado conector, en la red. Puede instalar conectores en función de las necesidades de escalabilidad y alta disponibilidad. Empiece con uno y agregue más según sea necesario. Cada vez que se instala un conector, este se agrega al grupo de conectores que sirve al inquilino.

Se recomienda que no instale los conectores en los propios servidores de la aplicación; aunque es posible (especialmente para implementaciones pequeñas).

No es necesario eliminar los conectores que ya no esté utilizando. Cuando hay un conector en ejecución, este permanece activo tal y como se conecta al servicio. Los conectores que no se están usando se etiquetan como _inactivos_ y se quitarán tras 10 días de inactividad. 

Para obtener información acerca de cómo solucionar problemas de conectividad de Azure AD, consulte [How to troubleshoot Azure AD Application Proxy connectivity problems](https://blogs.technet.microsoft.com/applicationproxyblog/2015/03/24/how-to-troubleshoot-azure-ad-application-proxy-connectivity-problems) (Solución de problemas de conectividad del Proxy de aplicación de Azure AD). 

## <a name="what-are-the-cloud-rules-for-connectors"></a>¿Cuáles son las reglas de la nube para los conectores?
Los conectores y el servicio se encargan de todas las tareas de alta disponibilidad. Se pueden agregar o quitar de forma dinámica. Cada vez que llega una solicitud nueva, esta se enrutará a uno de los conectores que estén disponibles en ese momento. Si un conector está no disponible temporalmente, obviamente no responderá a este tráfico.

Los conectores no tienen estados ni datos de configuración en el equipo, aparte de la conectividad con la configuración del servicio y el certificado que autentican este conector. Cuando se conectan al servicio, extraen todos los datos de configuración requeridos y los actualizan cada dos minutos.
Además, sondean al servidor para buscar si hay una versión más reciente del conector. Si se encuentra alguna, los conectores se actualizan.

Puede supervisar sus conectores desde la máquina en que se ejecutan, usando el registro de eventos y los contadores de rendimiento o desde la nube por medio de la página Estado del conector, tal y como se muestra a continuación.

 ![Conectores del Proxy de aplicación de Azure AD](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

## <a name="all-networking-is-outbound"></a>Todas las redes se consideran de salida.
Los conectores solo envían solicitudes salientes, por lo que la conexión se inicia siempre por parte de los conectores. No hay necesidad de abrir los puertos de entrada, porque una vez que se ha establecido una sesión, el tráfico fluirá en ambos sentidos.

El tráfico saliente se envía al servicio de Proxy de aplicación y a las aplicaciones publicadas. El tráfico hacia el servicio se envía a los centros de datos de Azure en varios números de puertos diferentes. Para obtener más información, consulte [Habilitación del Proxy de aplicación en Azure Portal](active-directory-application-proxy-enable.md).

Como solo se tiene tráfico saliente, no es necesario configurar el equilibrio de carga entre los conectores ni configurar el acceso entrante a través de los firewalls.

Para obtener información acerca de cómo configurar las reglas de firewall de salida, consulte [Work with existing on-premise Proxy servers](application-proxy-working-with-proxy-servers.md) (Trabajo con servidores de proxy locales existentes).

Use la [herramienta de prueba Connector Ports del proxy de aplicación de Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) para comprobar que el conector puede comunicarse con el servicio Proxy de aplicación. Como mínimo, asegúrese de que la región de centro de EE. UU. y la región más cercana tienen todas las marcas de verificación en verde. Además, cuantas más marcas de verificación verde haya, mayor resistencia habrá. 

## <a name="network-security"></a>Seguridad de las redes

Los conectores pueden instalarse en cualquier ubicación de la red que les permita enviar solicitudes al servicio y a las aplicaciones de back-end. Funcionarán correctamente si se instalan dentro de la red corporativa, dentro de una red perimetral o incluso en la máquina virtual que se está ejecutando en la nube que tiene acceso a las aplicaciones.

Las implementaciones de la red perimetral son normalmente más complicadas. Sin embargo, una de las razones para implementar los conectores en la red perimetral es el uso de otra infraestructura que esté disponible para componentes que se ejecutan ahí; por ejemplo, equilibradores de carga de aplicaciones de back-end o controles de seguridad, como los sistemas de detección de intrusiones.

## <a name="domain-joining"></a>Unión a dominio

Los conectores se pueden ejecutar en un equipo que no esté unido a dominio. Sin embargo, se necesita un equipo unido a dominio si decide usar el inicio de sesión único (SSO) para aplicaciones que usan la autenticación integrada de Windows (IWA). 

En este caso, las máquinas de conector deben estar unidas a un dominio que pueda llevar a cabo la delegación restringida de [Kerberos](https://web.mit.edu/kerberos) en nombre de los usuarios correspondientes para las aplicaciones publicadas.

Los conectores también pueden estar unidos a dominios o bosques que tienen una relación de confianza parcial o en controladores de dominio de solo lectura (RODC).

## <a name="connectors-on-hardened-environments"></a>Conectores en entornos protegidos

En la mayoría de los casos, la implementación del conector es muy sencilla y no requiere ninguna configuración especial. Sin embargo, existen varias condiciones que deben tenerse en cuenta:

* Las organizaciones que limitan el tráfico saliente deben seguir las instrucciones que se detallan a continuación para abrir los puertos necesarios.
* Podría ser necesario que las máquinas compatibles con FIPS cambien su configuración para permitir que el servicio de conector, el servicio de actualización de conectores y su programa de instalación generen y almacenen un certificado en ese equipo.
* Las organizaciones que bloquean su entorno en función de los procesos que emiten las solicitudes de red tienen que asegurarse de que los servicios del conector estén habilitados para tener acceso a todas las direcciones IP y puertos necesarios.
* En algunos casos, los proxy de reenvío de salida pueden interrumpir la autenticación de certificado de dos direcciones y provocar un error en la comunicación.

## <a name="all-connectors-are-created-almost-equal"></a>Todos los conectores se crean prácticamente igual.

Se supone que todos los conectores son idénticos entre sí y todas las solicitudes entrantes pueden llegar a cada uno de los conectores. Esto significa que todas ellas deben tener la misma conectividad de red y configuración de [Kerberos](https://web.mit.edu/kerberos).

Todas las comunicaciones de conector a servicio están protegidas por un certificado de cliente que se emite y, a continuación, se instala en el equipo del conector. Para obtener información acerca de cómo renovar certificados de conector, vea [Habilitación del Proxy de aplicación en Azure Portal](active-directory-application-proxy-enable.md).

## <a name="connector-authentication"></a>Autenticación de conector

Para proporcionar un servicio seguro, los conectores tienen que autenticarse hacia el servicio y el servicio tiene que autenticarse hacia el conector. Esto se lleva a cabo mediante de certificados de cliente y servidor cuando los conectores inician la conexión. De este modo, el nombre de usuario y la contraseña del administrador no se almacenan en el equipo del conector.

Los certificados utilizados son específicos del servicio de Proxy de aplicación. Se crean durante el registro inicial y se renuevan automáticamente por los conectores cada dos meses. 

Si un conector no se conecta al servicio durante un período de varios meses, puede que tenga los certificados caducados. En este caso, es necesario el registro, por lo que debe desinstalar y volver a instalar el conector para provocarlo. Puede ejecutar los siguientes comandos de PowerShell:

```
* Import-module AppProxyPSModule
* Register-AppProxyConnector
```

## <a name="performance-and-scalability"></a>Rendimiento y escalabilidad

Aunque la escala para el servicio en línea es transparente, la escala es un factor importante en lo que respecta a los conectores. Debe tener suficientes conectores para administrar el tráfico en sus momentos de pico. Puesto que los conectores no tienen estado, no dependen del número de usuarios o sesiones. Más bien, dependen del número de solicitudes y su tamaño de carga. En el caso del tráfico web estándar, hemos visto que una máquina controla en promedio unas dos mil solicitudes por segundo. Esta cifra depende de las características exactas de la máquina.

El rendimiento del conector viene determinado por las redes y la CPU. El rendimiento de la CPU se necesita para el cifrado y el descifrado de SSL, mientras que las redes son importantes para obtener conectividad rápida para las aplicaciones y el servicio en línea de Azure. En cambio, la memoria no supone un problema para los conectores.

Para el servicio de conector, nos esforzamos por descargar los conectores en la medida de lo posible. El servicio en línea se encarga de gran parte del procesamiento y de todo el tráfico no autenticado. Todo lo que puede realizarse en la nube se realiza en la nube.

Otro factor que incide el rendimiento es la calidad de las conexiones entre los conectores; en particular:

* _El servicio en línea:_ si la conexión es lenta o tiene una latencia elevada, influirá en el nivel de servicio. Es mejor si su organización está conectada a Azure a través de ExpressRoute. En caso contrario, asegúrese de que el equipo de redes garantice que las conexiones a Azure se tratan de forma eficaz.

* _Las aplicaciones de back-end:_ en algunos casos, hay servidores proxy adicionales entre el conector y las aplicaciones de back-end. Es fácil solucionar este problema: abra un explorador desde la máquina de conector y obtenga acceso a estas aplicaciones. Si ejecuta los conectores en Azure y las aplicaciones están almacenadas de forma local, la experiencia podría no corresponderse con lo que esperan los usuarios.
* _Los controladores de dominio:_ si los conectores están realizando SSO mediante la delegación limitada de Kerberos (KCD), se ponen en contacto con los controladores de dominio antes de enviar la solicitud al servidor. Los conectores tienen una memoria caché de vales de Kerberos, pero en entornos ocupados la capacidad de respuesta de los controladores de dominio puede ralentizar la experiencia. Esto es más común para los conectores que se ejecutan en Azure mientras los controladores de dominio están almacenados localmente.

## <a name="automatic-updates-to-the-connector"></a>Actualizaciones automáticas para el conector

Con el servicio de actualización de los conectores, ofrecemos una forma automatizada de mantenerlos actualizados. De este modo, disfruta de la ventaja constante de contar con todas las nuevas características y las mejoras de seguridad y rendimiento.

Azure AD admite las actualizaciones automáticas para todos los conectores que implemente. Mientras se esté ejecutando el servicio de actualización de conectores del Proxy de aplicación, los conectores se actualizarán automáticamente sin tiempo de inactividad ni intervención manual. Si no ve el servicio de actualización de conectores en el servidor, debe volver a instalar el conector con el fin de obtener las actualizaciones. Si desea obtener más información acerca de cómo instalar conectores, consulte la [documentación de instalación](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-enable.md).

### <a name="updater-impact"></a>Incidencia de las actualizaciones

_Inquilinos con un conector:_ si tiene solo un conector, dicho conector se actualizará como parte del grupo más reciente. Dado que no hay ningún otro conector a través del cual volver a enrutar el tráfico, el servicio dejará de estar disponible durante la actualización. Para evitar este tiempo de inactividad y garantizar una alta disponibilidad con mayor facilidad, se recomienda instalar un segundo conector y crear un grupo de conectores. Para obtener más información acerca de cómo hacerlo, consulte la [documentación sobre los grupos de conectores](https://azure.microsoft.com/en-us/documentation/articlesactive-directory-application-proxy-connectors.md).

_Otros inquilinos:_ durante la actualización del conector, el tráfico se vuelve a enrutar a los demás conectores para minimizar la interrupción. Sin embargo, pueden eliminarse las transacciones en curso cuando se inicie la actualización. El explorador debería reintentar automáticamente la operación, permitiéndole ser consciente así de esta potencial eliminación. De no ser así, tendría que actualizar la página para solucionar este problema.

Sabemos que puede ser complicado sufrir incluso un minuto de inactividad, pero estas actualizaciones permiten proporcionarle un conector aún mejor con numerosas mejoras, por lo que creemos que vale la pena.

Si desea obtener más información acerca de los cambios en nuestra reciente actualización del conector, consulte la [actualización más reciente](https://azure.microsoft.com/en-us/updates/app-proxy-connector-12sept2016). Revisamos esa página junto con cada actualización.

## <a name="under-the-hood"></a>En segundo plano

Ponemos a su disposición una gran cantidad de herramientas útiles en Azure. Los conectores, en particular, tienen una gran cantidad de funcionalidades útiles. Dado que los conectores se basan en el Proxy de aplicación web de Windows Server, coinciden en la mayoría de las herramientas de administración; incluido el amplio conjunto de contadores de rendimiento de Windows y registros de eventos de Windows, tal y como se muestra a continuación en el Visor de eventos:

 ![Visor de eventos de Azure AD](./media/application-proxy-understand-connectors/event-view-window.png)

Y el Monitor de rendimiento:

 ![Monitor de rendimiento de Azure AD](./media/application-proxy-understand-connectors/performance-monitor.png)

Los conectores tienen registros de administración y sesión. Los registros de administración incluyen eventos importantes y sus errores. Los registros de sesión incluyen todas las transacciones y sus detalles de procesamiento. 

Para poder verlos, tendrá que habilitar "Mostrar registros analíticos y de depuración" en el menú "Ver" del Visor de eventos. A continuación, tendrá que habilitarlos para que puedan comenzar a recopilar eventos. Estos registros no aparecen en el Proxy de aplicación web en Windows Server 2012 R2, ya que los conectores se basan en una versión más reciente.

 ![Sesión de Visor de eventos de Azure AD](./media/application-proxy-understand-connectors/event-view-window-session.png)

Puede examinar el estado del servicio en la ventana Servicios. El conector está formado por dos servicios de Windows; uno es el conector real y otro es el que se ocupa de la actualización. Es necesario que ambos se ejecuten todo el tiempo.

 ![Local de servicios de Azure AD](./media/application-proxy-understand-connectors/aad-connector-services.png)

Para obtener información sobre cómo resolver errores del conector del Proxy de aplicación, consulte [Solucionar problemas de Proxy de aplicación](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-troubleshoot).

## <a name="next-steps"></a>Pasos siguientes
[Trabajo con servidores proxy locales existentes](application-proxy-working-with-proxy-servers.md)

[Procedimiento para instalar de forma silenciosa el conector de Proxy de aplicación de Azure AD](active-directory-application-proxy-silent-installation.md)


