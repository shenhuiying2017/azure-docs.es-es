---
title: "Monitorización de la infraestructura de identidad local en la nube"
description: "En esta página se describe qué es Azure AD Connect Health y por qué debería usarlo."
services: active-directory
documentationcenter: 
author: karavar
manager: mtillman
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 9919e326613ce81db32f1d72a8cb5e3668992885
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>Supervisión de la infraestructura de identidad local y los servicios de sincronización en la nube
Azure Active Directory (Azure AD) Connect Health le ayuda a supervisar y a conocer mejor su infraestructura de identidad local y los servicios de sincronización. Permite mantener una conexión confiable con Office 365 y Microsoft Online Services, ya que proporciona funcionalidades de supervisión para los principales componentes de identidad, como los servidores de Active Directory Federation Services (AD FS), los servidores de Azure AD Connect (también conocidos como motor de sincronización), los controladores de dominio de Active Directory, etc. También permite acceder con facilidad a los principales puntos de datos de estos componentes, con el fin de que pueda obtener datos de uso y otra información importante para tomar decisiones bien fundamentadas.

Esta información se encuentra en el [portal de Azure AD Connect Health](https://aka.ms/aadconnecthealth). En el portal de Azure AD Connect Health se pueden ver alertas, supervisión del rendimiento y análisis de uso, entre otros datos. Azure AD Connect Health habilita la lente de mantenimiento única para los componentes de identidad clave en un lugar único.

![Qué es Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


A medida que aumentan las características de Azure AD Connect Health, el portal proporciona un único panel a través de la lente de identidad. Se obtiene un entorno aún más sólido, correcto e integrado para que los usuarios aumenten su capacidad de hacer las cosas.
## <a name="why-use-azure-ad-connect-health"></a>Motivos para usar Azure AD Connect Health
Cuando se integran los directorios locales con Azure AD, aumenta la productividad de los usuarios, ya que hay una identidad común para acceder tanto a los recursos en la nube como a los locales. Sin embargo, esta integración crea el desafío de garantizar que este entorno es correcto, con el fin de que los usuarios puedan acceder de manera confiable a los recursos tanto a nivel local como en la nube desde cualquier dispositivo. Azure AD Connect Health le ayuda a supervisar y obtener información sobre la infraestructura de identidades local, que se utiliza para acceder a Office 365 o a otras aplicaciones de Azure AD. Es tan sencillo como instalar un agente en cada uno de los servidores de identidad locales.

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[Azure AD Connect Health para AD FS](active-directory-aadconnect-health-adfs.md)
Azure AD Connect Health para AD FS es compatible con AD FS 2.0 en Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2. También permite supervisar el proxy de AD FS o los servidores proxy de la aplicación web que proporcionan compatibilidad con la autenticación para el acceso a la extranet. Con una instalación sencilla y económica del agente de mantenimiento, Azure AD Connect Health para AD FS proporciona el siguiente conjunto de funcionalidades clave:

* Supervisión con alertas para saber si AD FS y los servidores proxy de AD FS funcionan correctamente
* Notificaciones de correo electrónico para alertas críticas
* Tendencias de los datos de rendimiento, que son útiles para planear la capacidad de AD FS
* Análisis de uso de los inicios de sesión de AD FS con elementos dinámicos (aplicaciones, usuarios, ubicación de red, etc.), que son útiles para saber cómo se utiliza AD FS
* Informes para AD FS, como los primeros 50 usuarios con nombre de usuario o contraseña de inicio de sesión erróneos y su última dirección IP
  
Aquí puede obtener información acerca del [Uso de Azure AD Connect Health con AD DS](active-directory-aadconnect-health-adfs.md)

El vídeo siguiente proporciona información general acerca de Azure AD Connect Health para AD FS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>

>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md)
Azure AD Connect Health para sincronización supervisa y proporciona información acerca de las sincronizaciones que tienen lugar entre Active Directory local y Azure AD. Azure AD Connect Health para sincronización ofrece el siguiente conjunto de funcionalidades clave:

* Supervisión con alertas para saber en qué momento un servidor de Azure AD Connect, conocido también como motor de sincronización, no funciona correctamente
* Notificaciones de correo electrónico para alertas críticas
* Sincronización de la visión operativa, que incluye gráficos de latencia de las operaciones de sincronización y tendencias de distintas operaciones, como incorporaciones, actualizaciones y eliminaciones
* Información a primea vista de las propiedades de sincronización y de la última exportación correcta a Azure AD
* Los informes acerca de los errores de sincronización de nivel de objeto \(no requieren Azure AD Premium\)

Aquí puede obtener información acerca del [Supervisión de la sincronización de Azure AD Connect con Azure AD Connect Health](active-directory-aadconnect-health-sync.md)

El vídeo siguiente proporciona información general acerca de Azure AD Connect Health para sincronización.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-dsactive-directory-aadconnect-health-addsmd"></a>[Azure AD Connect Health para AD DS](active-directory-aadconnect-health-adds.md)
Azure AD Connect Health para Active Directory Domain Services (AD DS) proporciona supervisión para los controladores de dominio instalados en Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 y Windows Server 2016. La instalación de un agente de mantenimiento le permite supervisar el entorno de AD DS local desde la nube. Azure AD Connect Health para AD DS proporciona el siguiente conjunto de funcionalidades clave:

* Alertas de supervisión que detectan si los controladores de dominio no son correctos y las notificaciones de correo electrónico para alertas críticas
* El panel de controladores de dominio, que proporciona una vista rápida del mantenimiento y del estado operativo de los controladores de dominio
* El panel de estado de replicación que tiene la información de replicación más reciente y vínculos a guías de solución de problemas cuando se detectan errores
* Acceso rápido en cualquier lugar a los gráficos de datos de los contadores de rendimiento habituales, que son necesarios para la solución de problemas y la supervisión

Aquí puede obtener información acerca del [Uso de Azure AD Connect Health con AD DS](active-directory-aadconnect-health-adds.md)

El vídeo siguiente proporciona información general de Azure AD Connect Health para AD DS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Introducción a Azure AD Connect Health
Para empezar a usar Azure AD Connect Health, siga estos pasos:

1. [Obtenga Azure AD Premium](../active-directory-get-started-premium.md) o [inicie una prueba](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Descargue e instale los agentes de Azure AD Connect Health](#download-and-install-azure-ad-connect-health-agent) en los servidores de identidad.
3. Vea el panel de Azure AD Connect Health en [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth).

> [!NOTE]
> Recuerde que para poder ver los datos de panel de Azure AD Connect Health, es preciso instalar los agentes de Azure AD Connect Health en los servidores de destino.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Descarga e instalación de un agente de Azure AD Connect Health
* Asegúrese de que [cumple los requisitos](active-directory-aadconnect-health-agent-install.md#requirements) de Azure AD Connect Health.
* Introducción a Azure AD Connect Health para AD FS
    * [Descargue el agente de Azure AD Connect Health para AD FS.](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [Consulte las instrucciones de instalación](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Introducción al uso de Azure AD Connect Health para la sincronización
    * [Descargue e instale la versión más reciente de Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771). El agente de Health para la sincronización se instalará como parte de la instalación de Azure AD Connect (versión 1.0.9125.0 o superior).
* Introducción a Azure AD Connect Health para AD DS
    * [Descargue el agente de Azure AD Connect Health para AD DS](http://go.microsoft.com/fwlink/?LinkID=820540).
    * [Consulte las instrucciones de instalación](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="azure-ad-connect-health-portal"></a>Portal de Azure AD Connect Health
El portal de Azure AD Connect Health muestra las vistas de alertas, la supervisión del rendimiento y el análisis de uso. La dirección URL https://AKA.ms/aadconnecthealth le lleva a la hoja principal de Azure AD Connect Health. Puede considerar una hoja como una ventana. En la hoja principal, verá **Inicio rápido**, los servicios de Azure AD Connect Health y otras opciones de configuración. Consulte la siguiente captura de pantalla y las explicaciones que se proporciona después de la captura de pantalla. Después de implementar los agentes, el servicio de mantenimiento identifica automáticamente los servicios que Azure AD Connect Health supervisa.

> [!NOTE]
> Para obtener información sobre licencias, consulte [Preguntas más frecuentes sobre Azure AD Connect Health](active-directory-aadconnect-health-faq.md) o la [página de precios de Azure AD](https://aka.ms/aadpricing).
    
![portal de Azure AD Connect Health](./media/active-directory-aadconnect-health/portal4.png)

* **Inicio rápido**: si se selecciona esta opción, se abre la hoja **Inicio rápido**. Para descargar el agente de Azure AD Connect Health, seleccione **Obtener herramientas**. También puede acceder a la documentación y aportar comentarios.
* **Servicios de federación de Active Directory**: esta opción muestra todos los servicios de AD FS que Azure AD Connect Health supervisa actualmente. Cuando se selecciona una instancia, la hoja que se abre muestra información acerca de ella. Esta información incluye una descripción general, propiedades, alertas, supervisión y análisis de uso. Para más información sobre las funcionalidades, consulte [Uso de Azure AD Connect Health para sincronización](active-directory-aadconnect-health-adfs.md).
* **Azure Active Directory Connect (sync)**: esta opción muestra los servidores de Azure AD Connect que Azure AD Connect Health supervisa actualmente. Al seleccionar la entrada, la hoja que se abre muestra información acerca de los servidores de Azure AD Connect. Para más información sobre las funcionalidades, consulte [Uso de Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md).
* **Active Directory Domain Services**: esta opción muestra todos los bosques de AD DS que Azure AD Connect Health supervisa actualmente. Cuando se selecciona un bosque, la hoja que se abre muestra información acerca de él. Esta información incluye una introducción a la información esencial, el panel de controladores de dominio, el panel de estado de replicación, alertas y supervisión. Para más información sobre las funcionalidades, consulte [Uso de Azure AD Connect Health con AD DS](active-directory-aadconnect-health-adds.md).
* **Configurar**: esta sección incluye opciones para activar o desactivar lo siguiente:

  - Actualización automática para actualizar automáticamente el agente de Azure AD Connect Health a la versión más reciente: se realizará automáticamente la actualización a las versiones más reciente del agente de Azure AD Connect Health cuando estén disponibles. Esta opción está habilitada de manera predeterminada.
  - Permitir el acceso de Microsoft a los datos de mantenimiento del directorio de Azure AD solo con para solucionar problemas: si esta opción está habilitada, Microsoft podrá ver los mismos datos que usted. Esta información puede ayudar a solucionar problemas. Esta opción está deshabilitada de manera predeterminada.

## <a name="related-links"></a>Vínculos relacionados
* [Instalación del Agente de Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operaciones de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso de Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso de Azure AD Connect Health para sincronización](active-directory-aadconnect-health-sync.md)
* [Uso de Azure AD Connect Health con AD DS](active-directory-aadconnect-health-adds.md)
* [Preguntas más frecuentes de Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Historial de versiones de Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
