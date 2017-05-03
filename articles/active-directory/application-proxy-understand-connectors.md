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
ms.date: 04/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 16a000074ae742cc6bc1b25bf359990fe73608f7
ms.lasthandoff: 04/21/2017


---

# <a name="understand-azure-ad-application-proxy-connectors"></a>Descripción de los conectores del Proxy de aplicación de Azure AD

Los conectores son los que hacen posible el Proxy de aplicación de Azure AD. Son simples, fáciles de implementar y mantener y muy eficaces. En este artículo se describe qué son los conectores y cómo funcionan, además de algunos procedimientos recomendados para sacar el máximo partido de su implementación. 

## <a name="connector-deployment"></a>Implementación del conector

El Proxy de aplicación funciona después de instalar el servicio de Windows Server, denominado conector, en la red. Puede instalar varios conectores en función de las necesidades de escalabilidad y alta disponibilidad. Empiece con uno y agregue más según sea necesario. Cada vez que se instala un conector, este se agrega al grupo de conectores que sirve al inquilino.

Se recomienda que no instale los conectores en los mismos servidores que hospedan las aplicaciones. Sin embargo, necesita poder acceder a la aplicación desde el servidor donde instale el conector.


## <a name="connector-maintenance"></a>Mantenimiento del conector
Los conectores y el servicio se encargan de todas las tareas de alta disponibilidad. Se pueden agregar o quitar de forma dinámica. Cada vez que llega una solicitud nueva, esta se enruta a uno de los conectores que estén disponibles en ese momento. Si un conector está no disponible temporalmente, no responderá a este tráfico.

Los conectores no tienen estados ni datos de configuración en el equipo, aparte de la conectividad con la configuración del servicio y el certificado que autentica este conector. Cuando se conectan al servicio, extraen todos los datos de configuración requeridos y los actualizan cada dos minutos.
Además, sondean al servidor para buscar si hay una versión más reciente del conector. Si se encuentra alguna, los conectores se actualizan.

Puede supervisar sus conectores desde la máquina en que se ejecutan, ya sea con el registro de eventos y los contadores de rendimiento o desde la página Proxy de aplicación de Azure Portal.

 ![Conectores del Proxy de aplicación de Azure AD](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

No es necesario que elimine manualmente los conectores que no se están utilizando. Cuando hay un conector en ejecución, este permanece activo tal y como se conecta al servicio. Los conectores que no se están usando se etiquetan como _inactivos_ y se quitarán tras 10 días de inactividad. 

## <a name="automatic-updates-to-the-connector"></a>Actualizaciones automáticas para el conector

Con el servicio de actualización de los conectores, ofrecemos una forma automatizada de mantenerlos actualizados. De este modo, disfruta de la ventaja constante de contar con todas las nuevas características y las mejoras de seguridad y rendimiento.

Azure AD admite las actualizaciones automáticas para todos los conectores que implemente. Mientras se esté ejecutando el servicio de actualización de conectores del Proxy de aplicación, los conectores se actualizan automáticamente. Si no ve el servicio de actualización de conectores en el servidor, debe [volver a instalar el conector](active-directory-application-proxy-enable.md) con el fin de obtener las actualizaciones.

Puede experimentar un tiempo de inactividad cuando el conector se actualiza si:

- Solo tiene un conector. Para evitar este tiempo de inactividad y garantizar una alta disponibilidad, se recomienda instalar un segundo conector y [crear un grupo de conectores](active-directory-application-proxy-connectors-azure-portal.md).

- Un conector estaba en medio de una transacción cuando comenzó la actualización. El explorador debería reintentar automáticamente la operación, o puede actualizar la página. Cuando se vuelve a enviar la solicitud, el tráfico se enruta a un conector de copia de seguridad.

## <a name="all-networking-is-outbound"></a>Todas las redes se consideran de salida.
Los conectores solo envían solicitudes salientes, por lo que la conexión se inicia siempre desde el conector. No hay necesidad de abrir los puertos de entrada, porque una vez que se ha establecido una sesión el tráfico fluye en ambos sentidos.

El tráfico saliente se envía al servicio de Proxy de aplicación y a las aplicaciones publicadas. El tráfico hacia el servicio se envía a los centros de datos de Azure en varios números de puertos diferentes. Para obtener más información acerca de los puertos que se utilizan, consulte [Habilitación del Proxy de aplicación en Azure Portal](active-directory-application-proxy-enable.md).

Como solo hay tráfico saliente, no es necesario configurar el equilibrio de carga entre los conectores ni configurar el acceso entrante a través de los firewalls.

Para obtener información acerca de cómo configurar las reglas de firewall de salida, consulte [Work with existing on-premise Proxy servers](application-proxy-working-with-proxy-servers.md) (Trabajo con servidores de proxy locales existentes).

Use la [herramienta de prueba Connector Ports del proxy de aplicación de Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) para comprobar que el conector puede comunicarse con el servicio Proxy de aplicación. Como mínimo, asegúrese de que la región de centro de EE. UU. y la región más cercana tienen todas las marcas de verificación en verde. Además, cuantas más marcas de verificación verde haya, mayor resistencia habrá. 

## <a name="network-security"></a>Seguridad de las redes

Los conectores pueden instalarse en cualquier ubicación de la red que les permita enviar solicitudes al servicio Proxy de la aplicación y a las aplicaciones back-end. Funcionan bien si los instala dentro de la red corporativa, dentro de una red perimetral o incluso en una máquina virtual que se ejecute en la nube. Lo importante es que el equipo que ejecuta el conector también tenga acceso a las aplicaciones.

Las implementaciones de la red perimetral son más complicadas. Sin embargo, un motivo por el cual podría interesarle implementar conectores en una red perimetral es el uso de otra infraestructura, como los equilibradores de carga de la aplicación de back-end o los sistemas de detección de intrusiones.

## <a name="domain-joining"></a>Unión a dominio

Los conectores se pueden ejecutar en un equipo que no esté unido a dominio. Sin embargo, si desea usar el inicio de sesión único (SSO) para aplicaciones que usan la autenticación integrada de Windows (IWA), necesita una máquina unida a dominio. En este caso, las máquinas de conector deben estar unidas a un dominio que pueda llevar a cabo la delegación restringida de [Kerberos](https://web.mit.edu/kerberos) en nombre de los usuarios correspondientes para las aplicaciones publicadas.

Los conectores también pueden estar unidos a dominios o bosques que tienen una relación de confianza parcial o a controladores de dominio de solo lectura.

## <a name="connectors-on-hardened-environments"></a>Conectores en entornos protegidos

Por lo general, la implementación del conector es sencilla y no requiere ninguna configuración especial. Sin embargo, hay algunas condiciones únicas que deben tenerse en cuenta:

* Las organizaciones que limitan el tráfico saliente deben [abrir los puertos necesarios](active-directory-application-proxy-enable.md#open-your-ports).
* Podría ser necesario que las máquinas compatibles con FIPS cambien su configuración para permitir que el servicio de conector, el servicio de actualización de conectores y su programa de instalación generen y almacenen un certificado.
* Las organizaciones que bloquean su entorno en función de los procesos que emiten las solicitudes de red tienen que asegurarse de que los servicios del conector estén habilitados para tener acceso a todas las direcciones IP y puertos necesarios.
* En algunos casos, los proxy de reenvío de salida pueden interrumpir la autenticación de certificado de dos direcciones y provocar un error en la comunicación.

## <a name="connector-authentication"></a>Autenticación de conector

Para proporcionar un servicio seguro, los conectores tienen que autenticarse hacia el servicio y el servicio tiene que autenticarse hacia el conector. Esto se lleva a cabo mediante de certificados de cliente y servidor cuando los conectores inician la conexión. De este modo, el nombre de usuario y la contraseña del administrador no se almacenan en el equipo del conector.

Los certificados utilizados son específicos del servicio de Proxy de aplicación. Se crean durante el registro inicial y se renuevan automáticamente por los conectores cada dos meses. 

Si un conector no se conecta al servicio durante varios meses, puede que tenga los certificados caducados. En este caso, desinstale y vuelva a instalar el conector para provocar el registro. Puede ejecutar los siguientes comandos de PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="performance-and-scalability"></a>Rendimiento y escalabilidad

Aunque la escala para el servicio en línea es transparente, la escala es un factor importante en lo que respecta a los conectores. Debe tener suficientes conectores para administrar el tráfico en sus momentos de pico. Puesto que los conectores no tienen estado, no dependen del número de usuarios o sesiones. Más bien, dependen del número de solicitudes y su tamaño de carga. En el caso del tráfico web estándar, una máquina puede controlar en promedio unas dos mil solicitudes por segundo. La capacidad específica depende de las características exactas de la máquina.

El rendimiento del conector viene determinado por las redes y la CPU. El rendimiento de la CPU se necesita para el cifrado y el descifrado de SSL, mientras que las redes son importantes para obtener conectividad rápida para las aplicaciones y el servicio en línea de Azure. 

En cambio, la memoria no supone un problema para los conectores. El servicio en línea se encarga de gran parte del procesamiento y de todo el tráfico no autenticado. Todo lo que puede realizarse en la nube se realiza en la nube.

Otro factor que incide el rendimiento es la calidad de las conexiones entre los conectores; en particular:

* **El servicio en línea:** las conexiones de latencia alta o de baja velocidad influyen en el servicio del conector. Es mejor si su organización está conectada a Azure a través de ExpressRoute. En caso contrario, asegúrese de que el equipo de redes garantice que las conexiones a Azure se tratan de forma eficaz.  
* **Las aplicaciones de back-end:** en algunos casos, hay servidores proxy adicionales entre el conector y las aplicaciones de back-end. Solucione este problema abriendo un explorador desde la máquina de conector y obteniendo acceso a estas aplicaciones. Si ejecuta los conectores en Azure y las aplicaciones están almacenadas de forma local, la experiencia podría no corresponderse con lo que esperan los usuarios.
* **Los controladores de dominio:** si los conectores están realizando SSO mediante la delegación limitada de Kerberos (KCD), se ponen en contacto con los controladores de dominio antes de enviar la solicitud al servidor. Los conectores tienen una memoria caché de vales de Kerberos, pero en entornos ocupados la capacidad de respuesta de los controladores de dominio puede repercutir en la experiencia. Este problema es más común para los conectores que se ejecutan en Azure mientras los controladores de dominio están almacenados localmente.

## <a name="under-the-hood"></a>En segundo plano

Los conectores se basan en el Proxy de aplicación web de Windows Server, por lo que coinciden mayoritariamente respecto a las herramientas de administración, incluidos los Registros de eventos de Windows.

 ![Administración de registros de eventos con el Visor de eventos](./media/application-proxy-understand-connectors/event-view-window.png)

y contadores de rendimiento de Windows. 

 ![Adición de contadores al conector con el Monitor de rendimiento](./media/application-proxy-understand-connectors/performance-monitor.png)

Los conectores tienen registros de administración y sesión. Los registros de administración incluyen eventos importantes y sus errores. Los registros de sesión incluyen todas las transacciones y sus detalles de procesamiento. 

Para poder verlos, vaya al Visor de eventos, abra el menú **Ver** y active **Mostrar registros analíticos y de depuración**. A continuación, habilítelos para que puedan comenzar a recopilar eventos. Estos registros no aparecen en el Proxy de aplicación web en Windows Server 2012 R2, ya que los conectores se basan en una versión más reciente.

Puede examinar el estado del servicio en la ventana Servicios. El conector consta de dos servicios de Windows: el conector real y el actualizador. Es necesario que ambos se ejecuten todo el tiempo.

 ![Local de servicios de Azure AD](./media/application-proxy-understand-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Pasos siguientes
[Solución de problemas y mensajes de error de Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

[Trabajo con servidores proxy locales existentes](application-proxy-working-with-proxy-servers.md)

[Cómo instalar de forma silenciosa el conector de Proxy de aplicación de Azure AD](active-directory-application-proxy-silent-installation.md)


