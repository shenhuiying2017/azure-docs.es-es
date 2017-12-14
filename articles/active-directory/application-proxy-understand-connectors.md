---
title: "Descripción de los conectores del Proxy de aplicación de Azure AD | Microsoft Docs"
description: "Se explican los conceptos básicos acerca de los conectores del Proxy de aplicación de Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: fe8d5c40249431be60dc8844adf7efa1b8e87c5f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Descripción de los conectores del Proxy de aplicación de Azure AD

Los conectores son los que hacen posible el Proxy de aplicación de Azure AD. Son simples, fáciles de implementar y mantener y muy eficaces. En este artículo se habla de qué son los conectores, cómo funcionan, y se ofrecen algunas sugerencias para optimizar la implementación. 

## <a name="what-is-an-application-proxy-connector"></a>¿Qué es un conector de proxy de aplicación?

Los conectores son agentes ligeros que se colocan en local y facilitan la conexión saliente del servicio Proxy de aplicación. Los conectores deben instalarse en un servidor de Windows Server con acceso a la aplicación de back-end. Puede organizar los conectores en grupos; cada grupo controla el tráfico a aplicaciones concretas. Los conectores equilibran la cargan automáticamente y pueden ayudar a optimizar la estructura de red. 

## <a name="requirements-and-deployment"></a>Requisitos e implementación

Para implementar correctamente Proxy de aplicación necesita al menos un conector, pero se recomiendan dos o más para conseguir una mayor resistencia. Instale el conector en un equipo Windows Server 2012 R2 o 2016. El conector debe ser capaz de comunicarse con el servicio Proxy de aplicación, así como con las aplicaciones locales que se publican. 

Para más información sobre los requisitos de red del servidor del conector, vea [Get started with Application Proxy and install a connector (Información general sobre Proxy de aplicación e instalación de un conector)](active-directory-application-proxy-enable.md).

## <a name="maintenance"></a>Mantenimiento
Los conectores y el servicio se encargan de todas las tareas de alta disponibilidad. Se pueden agregar o quitar de forma dinámica. Cada vez que llega una solicitud nueva, esta se enruta a uno de los conectores que estén disponibles en ese momento. Si un conector no está disponible temporalmente, no responde a este tráfico.

Los conectores no tienen estado ni datos de configuración en el equipo. Los únicos datos que almacenan son los valores de configuración para conectar el servicio y su certificado de autenticación. Cuando se conectan al servicio, extraen todos los datos de configuración requeridos y los actualizan cada dos minutos.

Los conectores también sondean al servidor para averiguar si hay una versión más reciente del conector. Si se encuentra alguna, los conectores se actualizan.

Puede supervisar los conectores desde el equipo en que se ejecutan, con el registro de eventos y los contadores de rendimiento. O puede ver su estado desde la página Application Proxy de Azure Portal:

 ![Conectores del Proxy de aplicación de Azure AD](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

No es necesario que elimine manualmente los conectores que no se están utilizando. Cuando hay un conector en ejecución, este permanece activo tal y como se conecta al servicio. Los conectores que no se están usando se etiquetan como _inactivos_ y se quitan tras 10 días de inactividad. No obstante, si quiere desinstalar un conector, desinstale el servicio de conector y el servicio de actualizador del servidor. Reinicie el equipo para quitar completamente el servicio.

## <a name="automatic-updates"></a>Actualizaciones automáticas

Azure AD proporciona actualizaciones automáticas para todos los conectores que se implementen. Mientras se esté ejecutando el servicio de actualización de conectores del Proxy de aplicación, los conectores se actualizan automáticamente. Si no ve el servicio de actualización de conectores en el servidor, debe [volver a instalar el conector](active-directory-application-proxy-enable.md) con el fin de obtener las actualizaciones. 

Si no quiere esperar a que una actualización automática llegue al conector, puede realizar una actualización manual. Vaya a la [página de descarga del conector](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) en el servidor en el que se encuentra el conector y seleccione **Descargar**. Este proceso inicia una actualización del conector local. 

Para los inquilinos con varios conectores, las actualizaciones automáticas se dirigen a un conector cada vez en cada grupo para evitar tiempos de inactividad en su entorno. 

Puede experimentar un tiempo de inactividad cuando el conector se actualiza si:  
- Solo tiene un conector. Para evitar este tiempo de inactividad y garantizar una alta disponibilidad, se recomienda instalar un segundo conector y [crear un grupo de conectores](active-directory-application-proxy-connectors-azure-portal.md).  
- Un conector estaba en medio de una transacción cuando comenzó la actualización. Aunque se pierde la transacción inicial, el explorador debería reintentar automáticamente la operación o el usuario podría actualizar la página. Cuando se vuelve a enviar la solicitud, el tráfico se enruta a un conector de copia de seguridad.

## <a name="creating-connector-groups"></a>Creación de grupos de conectores

Los grupos de conectores permiten asignar conectores específicos para atender a aplicaciones específicas. Puede agrupar varios conectores y, a continuación, asignar cada aplicación a un grupo. 

Los grupos de conectores facilitan la administración de implementaciones a gran escala. También mejoran la latencia para los inquilinos que tienen aplicaciones hospedadas en distintas regiones, porque se pueden crear grupos de conectores basados en la ubicación para atender solo a las aplicaciones locales. 

Para más información acerca de los grupos de conectores, consulte [Publicación de aplicaciones en redes independientes y ubicaciones mediante grupos de conectores](active-directory-application-proxy-connectors-azure-portal.md).

## <a name="capacity-planning"></a>Planificación de capacidad 

Aunque los conectores equilibrarán la carga automáticamente dentro de un grupo de conectores, también es importante asegurarse de que ha planeado suficiente capacidad entre conectores para controlar el volumen de tráfico esperado. En general, cuantos más usuarios tenga, necesitará una máquina mayor. A continuación se muestra una tabla que proporciona un esquema del volumen que pueden controlar distintas máquinas. Tenga en cuenta que todo se basa en transacciones por segundo (TPS) esperadas en lugar de por usuario, ya que los patrones de uso varían y no se pueden usar para predecir la carga.  Tenga en cuenta también que habrá algunas diferencias en función del tamaño de las respuestas y el tiempo de respuesta de la aplicación de back-end: tamaños más grandes de respuesta y tiempos de respuesta más lentos darán como resultado un menor TPS Máximo.

|Núcleos|RAM|Latencia esperada (MS)-P99|TPS Máximo|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|
\* Esta máquina tiene un límite de 800 conexiones. En todas las demás máquinas, hemos utilizado el límite de 200 conexiones de forma predeterminada.
 
>[!NOTE]
>No hay mucha diferencia en el TPS máximo entre máquinas de 4, 8 y 16 núcleos. La diferencia principal entre ellas está en la latencia esperada.  

## <a name="security-and-networking"></a>Seguridad y redes

Los conectores pueden instalarse en cualquier ubicación de la red que les permita enviar solicitudes al servicio Proxy de aplicación. Lo importante es que el equipo que ejecuta el conector también tenga acceso a las aplicaciones. Puede instalar conectores dentro de la red corporativa o en una máquina virtual que se ejecute en la nube. Los conectores se pueden ejecutar en una red perimetral (DMZ), pero no es necesario, ya que todo el tráfico es saliente, así que la red se mantiene protegida.

Los conectores solo envían solicitudes salientes. El tráfico saliente se envía al servicio de Proxy de aplicación y a las aplicaciones publicadas. No hay que abrir los puertos de entrada, porque una vez que se ha establecido una sesión, el tráfico fluye en ambos sentidos. No es necesario configurar el equilibrio de carga entre los conectores ni configurar el acceso de entrada a través de los firewalls. 

Para más información sobre cómo configurar reglas de firewall de salida, vea [Trabajo con servidores proxy locales existentes](application-proxy-working-with-proxy-servers.md).

Use la [herramienta de prueba Connector Ports del proxy de aplicación de Azure AD](https://aadap-portcheck.connectorporttest.msappproxy.net/) para comprobar que el conector puede comunicarse con el servicio Proxy de aplicación. Como mínimo, asegúrese de que la región de centro de EE. UU. y la región más cercana tienen todas las marcas de verificación en verde. Además, cuantas más marcas de verificación verde haya, mayor resistencia habrá. 

## <a name="performance-and-scalability"></a>Rendimiento y escalabilidad

La escala del servicio Proxy de aplicación es transparente, pero es un factor en lo que respecta a los conectores. Debe tener suficientes conectores para administrar el tráfico en sus momentos de pico. Pero no es necesario configurar el equilibrio de carga, ya que todos los conectores de un grupo de conectores equilibran la carga automáticamente.

Puesto que los conectores no tienen estado, no dependen del número de usuarios ni sesiones. Más bien dependen del número de solicitudes y del tamaño de la carga. En el caso del tráfico web estándar, una máquina puede controlar en promedio unas dos mil solicitudes por segundo. La capacidad específica depende de las características exactas de la máquina. 

El rendimiento del conector viene determinado por las redes y la CPU. El rendimiento de la CPU es necesario para el cifrado y el descifrado SSL, mientras que las redes son importantes para conectar rápidamente con las aplicaciones y el servicio en línea de Azure.

En cambio, la memoria no supone un problema para los conectores. El servicio en línea se encarga de gran parte del procesamiento y de todo el tráfico no autenticado. Todo lo que puede realizarse en la nube se realiza en la nube. 

El equilibrio de carga se produce entre los conectores de un grupo de conectores determinado. Hacemos una variación de round robin para determinar qué conector del grupo sirve una solicitud determinada. Después de elegir un conector, mantenemos una afinidad de la sesión entre ese usuario y la aplicación para toda la duración de la sesión. Si por algún motivo ese conector o máquina no están disponibles, el tráfico comenzará a dirigirse a otro conector del grupo. Esta resistencia también es el motivo por el que se recomienda tener varios conectores.

Otro factor que incide el rendimiento es la calidad de las conexiones entre los conectores; en particular: 

* **El servicio en línea**: unas conexiones de baja o alta latencia al servicio Proxy de aplicación de Azure influyen en el rendimiento del conector. Para optimizar el rendimiento, conecte la organización a Azure con Express Route. Si no, el equipo de redes debe asegurarse de que las conexiones a Azure se administren de la manera más eficaz posible. 
* **Las aplicaciones de back-end:** en algunos casos, hay servidores proxy adicionales entre el conector y las aplicaciones de back-end que pueden ralentizar o evitar las conexiones. Para solucionar esta situación, abra un explorador desde el servidor del conector e intente acceder a la aplicación. Si ejecuta los conectores en Azure pero las aplicaciones están en local, la experiencia podría no corresponderse a lo que esperan los usuarios.
* **Los controladores de dominio:** si los conectores realizan el SSO mediante delegación limitada de kerberos, se ponen en contacto con los controladores de dominio antes de enviar la solicitud al back-end. Los conectores tienen una memoria caché de vales Kerberos, pero en un entorno ocupado, la capacidad de respuesta de los controladores de dominio puede repercutir en el rendimiento. Este problema es más común en el caso de los conectores que se ejecutan en Azure pero se comunican con controladores de dominio que están en local. 

Para más información sobre la optimización de la red, vea [Consideraciones sobre la topología de red al utilizar el Proxy de aplicación de Azure Active Directory](application-proxy-network-topology-considerations.md).

## <a name="domain-joining"></a>Unión a dominio

Los conectores se pueden ejecutar en un equipo que no esté unido a dominio. Sin embargo, si desea usar el inicio de sesión único (SSO) para aplicaciones que usan la autenticación integrada de Windows (IWA), necesita una máquina unida a dominio. En este caso, los equipos del conector deben estar unidos a un dominio que pueda llevar a cabo la delegación limitada de [kerberos](https://web.mit.edu/kerberos) en nombre de los usuarios para las aplicaciones publicadas.

Los conectores también pueden estar unidos a dominios o bosques que tienen una relación de confianza parcial o a controladores de dominio de solo lectura.

## <a name="connector-deployments-on-hardened-environments"></a>Implementaciones del conector en entornos protegidos

Por lo general, la implementación del conector es sencilla y no requiere ninguna configuración especial. Sin embargo, hay algunas condiciones únicas que deben tenerse en cuenta:

* Las organizaciones que limitan el tráfico saliente deben [abrir los puertos necesarios](active-directory-application-proxy-enable.md#open-your-ports).
* Podría ser necesario que las máquinas compatibles con FIPS cambiaran su configuración para permitir que los procesos del conector generaran y almacenaran un certificado.
* Las organizaciones que bloquean su entorno en función de los procesos que emiten las solicitudes de red tienen que asegurarse de que los servicios del conector estén habilitados para tener acceso a todas las direcciones IP y puertos necesarios.
* En algunos casos, los proxy de reenvío de salida pueden interrumpir la autenticación de certificado de dos direcciones y provocar un error en la comunicación.

## <a name="connector-authentication"></a>Autenticación de conector

Para proporcionar un servicio seguro, los conectores tienen que autenticarse hacia el servicio y el servicio tiene que autenticarse hacia el conector. Esta autenticación se lleva a cabo mediante certificados de cliente y servidor cuando los conectores inician la conexión. De este modo, el nombre de usuario y la contraseña del administrador no se almacenan en el equipo del conector.

Los certificados utilizados son específicos del servicio de Proxy de aplicación. Se crean durante el registro inicial y se renuevan automáticamente por los conectores cada dos meses. 

Si un conector no se conecta al servicio durante varios meses, puede que tenga los certificados caducados. En este caso, desinstale y vuelva a instalar el conector para provocar el registro. Puede ejecutar los siguientes comandos de PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>En segundo plano

Los conectores se basan en el Proxy de aplicación web de Windows Server, por lo que coinciden mayoritariamente respecto a las herramientas de administración, incluidos los Registros de eventos de Windows.

 ![Administración de registros de eventos con el Visor de eventos](./media/application-proxy-understand-connectors/event-view-window.png)

y contadores de rendimiento de Windows. 

 ![Adición de contadores al conector con el Monitor de rendimiento](./media/application-proxy-understand-connectors/performance-monitor.png)

Los conectores tienen registros de administración y sesión. Los registros de administración incluyen eventos importantes y sus errores. Los registros de sesión incluyen todas las transacciones y sus detalles de procesamiento. 

Para poder verlos, vaya al Visor de eventos, abra el menú **Ver** y active **Mostrar registros analíticos y de depuración**. A continuación, habilítelos para que puedan comenzar a recopilar eventos. Estos registros no aparecen en el Proxy de aplicación web en Windows Server 2012 R2, ya que los conectores se basan en una versión más reciente.

Puede examinar el estado del servicio en la ventana Servicios. El conector consta de dos servicios de Windows: el conector real y el actualizador. Ambos deben ejecutarse todo el tiempo.

 ![Local de servicios de Azure AD](./media/application-proxy-understand-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Pasos siguientes


* [Publicación de aplicaciones en redes independientes y ubicaciones mediante grupos de conectores](active-directory-application-proxy-connectors-azure-portal.md)
* [Trabajo con servidores proxy locales existentes](application-proxy-working-with-proxy-servers.md)
* [Solución de problemas y mensajes de error de Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)
* [Cómo instalar de forma silenciosa el conector de Proxy de aplicación de Azure AD](active-directory-application-proxy-silent-installation.md)

