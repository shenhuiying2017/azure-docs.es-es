---
title: Procedimientos recomendados de seguridad operativa de Azure | Microsoft Docs
description: "En este artículo se proporciona un conjunto de procedimientos recomendados para la reforzar la seguridad operativa de Azure."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: db9840817d92b4f2f63009d30dbd554666d53dbe
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="azure-operational-security-best-practices"></a>Procedimientos recomendados de seguridad operativa de Azure
Con seguridad operativa de Azure, se hace referencia a los servicios, los controles y las características disponibles para los usuarios para proteger sus datos, aplicaciones y otros recursos en Microsoft Azure. La seguridad operativa de Azure se basa en una plataforma que incorpora el conocimiento adquirido a través de diversas funcionalidades exclusivas de Microsoft, incluido el ciclo de vida de desarrollo de seguridad (SDL) de Microsoft, el programa Microsoft Security Response Center y un conocimiento en profundidad del panorama de amenazas de ciberseguridad.

En este artículo se explica un conjunto de procedimientos recomendados de seguridad de las bases de datos de Azure. Estos procedimientos recomendados se derivan de nuestra experiencia con la seguridad de las bases de datos de Azure, y de las experiencias de clientes como usted.

Para cada procedimiento recomendado, explicaremos:
-   Qué es el procedimiento recomendado
-   Por qué le conviene habilitar este procedimiento recomendado
-   Cuál podría ser el resultado si no habilita el procedimiento recomendado
- Cómo aprender a habilitar el procedimiento recomendado

Este artículo, Procedimientos recomendados de seguridad operativa de Azure, se basa en las funcionalidades y los conjuntos de características de la plataforma Azure existentes cuando se redactó. Las opiniones y las tecnologías cambian con el tiempo, por lo que se actualizará de forma periódica para reflejar esos cambios.

Los procedimientos recomendados de seguridad operativa de Azure descritos en este artículo incluyen:

-   Supervisión, administración y protección de la infraestructura de nube
-   Administración de la identidad e implementación del inicio de sesión único (SSO)
-   Solicitudes de seguimiento, análisis de tendencias de uso y diagnóstico de problemas
-   Supervisión de los servicios con una solución de supervisión centralizada
-   Prevención de las amenazas, detección y respuesta
-   Supervisión de las redes basada en un escenario integral
-   Implementación segura mediante las herramientas de DevOps comprobadas

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>Supervisión, administración y protección de la infraestructura de nube
Las operaciones de TI son responsables de administrar la infraestructura del centro de datos, las aplicaciones y los datos, incluida la estabilidad y la seguridad de estos sistemas. Sin embargo, la obtención de información de seguridad sobre el cada vez mayor número de entornos de TI complejos, a menudo requiere que las organizaciones reúnan datos a partir de distintos sistemas de administración y seguridad.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) es la solución de administración de TI basada en la nube de Microsoft que le ayuda a administrar y proteger su infraestructura local y en la nube.

[La solución Seguridad y auditoría de OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) permite a la TI supervisar activamente todos los recursos, lo que puede ayudar a minimizar el impacto de los incidentes de seguridad. Auditoría y seguridad de OMS tiene dominios de seguridad que pueden utilizarse para la supervisión de recursos.

Para más información sobre OMS, lea el artículo [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

Para ayudar a los clientes a evitar las amenazas, detectarlas y responder a ellas, la [solución Seguridad y auditoría de Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) recopila y procesa los datos sobre los recursos, incluido lo siguiente:

-   Registro de eventos de seguridad
-   Eventos de Seguimiento de eventos para Windows (ETW)
-   Eventos de auditoría de AppLocker
-   Registro de Firewall de Windows
-   Eventos de Advanced Threat Analytics
-   Resultados de evaluación de la línea base
-   Resultados de evaluación de antimalware
-   Resultados de la evaluación de la actualización o revisión
-   Transmisiones de Syslog habilitadas explícitamente en el agente


## <a name="manage-identity-and-implement-single-sign-on"></a>Administración de la identidad e implementación del inicio de sesión único
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) es el directorio multiinquilino basado en la nube y el servicio de administración de identidades de Microsoft.

[Azure AD](https://azure.microsoft.com/services/active-directory/) también incluye una serie completa de funcionalidades de [administración de identidades](https://docs.microsoft.com/azure/security/security-identity-management-overview), incluida la [autenticación multifactor](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), el registro de dispositivos, la administración de contraseñas de autoservicio, la administración de grupos de autoservicio, la administración de cuentas con privilegios, el control de acceso basado en roles, la supervisión del uso de aplicaciones, la auditoría enriquecida y la supervisión y alertas de seguridad.

Las siguientes funcionalidades pueden ayudar a proteger aplicaciones basadas en la nube, optimizar los procesos de TI, reducir costos y asegurar que se cumplen los objetivos de cumplimiento normativo corporativos:

-   Administración de identidades y acceso para la nube
-   Simplificación del acceso de los usuarios a cualquier aplicación en la nube
-   Protección de las aplicaciones y los datos confidenciales
-   Habilitación del autoservicio para los empleados
-   Integración con Azure Active Directory

### <a name="identity-and-access-management-for-the-cloud"></a>Administración de identidades y acceso para la nube
Azure Active Directory (Azure AD) es una completa [solución en la nube de administración de identidades y acceso](https://www.microsoft.com/cloud-platform/identity-management) que le ofrece un sólido conjunto de funcionalidades para administrar usuarios y grupos. Ayuda a proteger el acceso a aplicaciones locales y en la nube, que incluyen servicios web de Microsoft como Office 365 y muchas aplicaciones de software como servicio (SaaS) que no son de Microsoft.
Para más información sobre Azure Identity Protection en Azure AD, consulte [Habilitación de Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable).

### <a name="simplify-user-access-to-any-cloud-app"></a>Simplificación del acceso de los usuarios a cualquier aplicación en la nube
[Habilite el inicio de sesión único](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) para simplificar el acceso de los usuarios a miles de aplicaciones en la nube desde dispositivos Windows, Mac, Android e iOS. Los usuarios pueden iniciar aplicaciones desde un panel de acceso web personalizado o una aplicación móvil con las credenciales de la compañía. Use el módulo Azure AD Application Proxy para ampliar las aplicaciones SaaS y publicar aplicaciones web locales con objeto de ofrecer acceso remoto e inicio de sesión único de gran seguridad.

### <a name="protect-sensitive-data-and-applications"></a>Protección de las aplicaciones y los datos confidenciales
Habilite [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) para impedir el acceso no autorizado a las aplicaciones locales y en la nube al proporcionar un nivel adicional de autenticación. Proteja su negocio y mitigue posibles amenazas mediante la supervisión de la seguridad, alertas e informes basados en Machine Learning que identifican patrones de acceso incoherentes.

### <a name="enable-self-service-for-your-employees"></a>Habilitación del autoservicio para los empleados
Delegue en sus empleados tareas importantes, como el restablecimiento de contraseñas y la creación y administración de grupos. [Habilite el cambio y el restablecimiento de contraseñas de autoservicio](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), y la administración de grupos de autoservicio con Azure AD.

### <a name="integrate-with-azure-active-directory"></a>Integración con Azure Active Directory
Extienda [Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) y los demás directorios locales a Azure AD para habilitar el inicio de sesión único para todas las aplicaciones basadas en la nube. Los atributos de usuario se pueden sincronizar automáticamente con el directorio en la nube desde cualquier clase de directorio local.

Para más información acerca de la integración de Azure Active Directory y cómo habilitarlo, lea el artículo [Integración de los directorios locales con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>Solicitudes de seguimiento, análisis de tendencias de uso y diagnóstico de problemas
[Azure Storage Analytics](https://docs.microsoft.com/azure/storage/storage-analytics) realiza el registro y proporciona datos de métricas para una cuenta de almacenamiento. Puede usar estos datos para hacer un seguimiento de solicitudes, analizar tendencias de uso y diagnosticar problemas con la cuenta de almacenamiento.

Las métricas de Storage Analytics se habilitan de forma predeterminada para las nuevas cuentas de almacenamiento. Puede habilitar el registro y la configuración tanto de las métricas como del registro en Azure Portal; para más detalles, consulte [Supervisión de una cuenta de almacenamiento en Azure Portal](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). También puede habilitar Storage Analytics mediante programación a través de la API de REST o la biblioteca de cliente. Use la operación Set Service Properties para habilitar Storage Analytics de forma individual para cada servicio.

Para obtener orientación exhaustiva sobre el uso de análisis de almacenamiento y otras herramientas para identificar, diagnosticar y solucionar problemas relacionados con Azure Storage, consulte [Supervisión, diagnóstico y solución de problemas de Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Para más información acerca de la integración de Azure Active Directory y cómo habilitarlo, lea el artículo [Habilitación y configuración de Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN).

## <a name="monitoring-services"></a>Servicios de supervisión
Las aplicaciones de nube son complejas y tienen muchas partes móviles. La supervisión proporciona datos para garantizar que la aplicación permanece en funcionamiento en un estado correcto. También ayuda a evitar posibles problemas o a solucionar los existentes. Además, puede usar datos de supervisión para obtener un conocimiento más profundo sobre su aplicación. Este conocimiento puede ayudarle a mejorar el rendimiento o mantenimiento de la aplicación, o a automatizar acciones que de lo contrario requerirían intervención manual.

### <a name="monitor-azure-resources"></a>Supervisión de los recursos de Azure
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) es el servicio de la plataforma que proporciona un único origen para la supervisión de recursos de Azure. Con Azure Monitor, puede visualizar, consultar, enrutar y archivar las métricas y los registros procedentes de los recursos de Azure, así como tomar medidas relacionadas. Puede trabajar con estos datos mediante la hoja del portal de Monitor, los [cmdlets de PowerShell de Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples), la [CLI multiplataforma](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) o las [API de REST de Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).

### <a name="enable-autoscale-with-azure-monitor"></a>Habilitación del escalado automático con Azure Monitor
La habilitación del [escalado automático de Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started) solo se aplica a conjuntos de escalado de máquinas virtuales (VMSS), Cloud Services, planes de App Service y App Service Environment.

### <a name="manage-roles-permissions-and-security"></a>Administración de seguridad y permisos de roles
Muchos equipos necesitan [regular estrictamente el acceso a los datos y la configuración de supervisión](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). Por ejemplo, si tiene miembros del equipo que trabajan exclusivamente en la supervisión (ingenieros de soporte técnico o ingenieros de operaciones de desarrollo) o si usa un proveedor de servicios administrados, puede concederles acceso solo a datos de supervisión, mientras restringe su capacidad para crear, modificar o eliminar recursos.

A continuación se explica cómo aplicar rápidamente un rol RBAC de supervisión integrado a un usuario en Azure o crear un rol personalizado propio para un usuario que necesita permisos de supervisión limitados. Después se describen las consideraciones de seguridad para los recursos relacionados con Azure Monitor y cómo puede limitar el acceso a los datos que contienen.

## <a name="prevent-detect-and-respond-to-threats"></a>Prevención de las amenazas, detección y respuesta
La detección de amenazas de Security Center recopila automáticamente información de seguridad de los recursos de Azure, de la red y de soluciones de asociados relacionadas. Después, analiza estos datos (a menudo, relacionando la información de diferentes orígenes) para identificar las amenazas. En Security Center, las alertas de seguridad están clasificadas por prioridad y se incluyen recomendaciones para solucionar la amenaza.

-   [Configure una directiva de seguridad](https://docs.microsoft.com/azure/security-center/security-center-policies) para su suscripción de Azure.
-   Use las [recomendaciones de Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations) como ayuda para proteger los recursos de Azure.
-   Revise y administre las [alertas de seguridad](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) actuales.

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) ayuda a evitar, detectar y responder a amenazas con más visibilidad y control sobre la seguridad de sus recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones de Azure, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

El Centro de seguridad ofrece las funcionalidades sencillas y eficaces de prevención, detección y respuesta ante amenazas integradas en Azure. Principales capacidades:

-   Conocimiento del estado de seguridad en la nube
-   Control de la seguridad en la nube
-   Implementación de soluciones de seguridad en la nube integradas de forma sencilla
-   Detección de amenazas y respuesta rápida

### <a name="understand-cloud-security-state"></a>Conocimiento del estado de seguridad en la nube
Utilice Azure Security Center para obtener una visión centralizada del estado de la seguridad de todos sus recursos en Azure. De un vistazo, puede comprobar que los controles de seguridad adecuados se hayan implementado y configurado correctamente, ademas de identificar con rapidez cualquier recurso que requiera atención.

### <a name="take-control-of-cloud-security"></a>Control de la seguridad en la nube
Defina [directivas de seguridad](https://docs.microsoft.com/azure/security-center/security-center-policies) para sus suscripciones de Azure de acuerdo con las necesidades de seguridad en la nube de su compañía, adaptadas al tipo de aplicaciones o a la confidencialidad de los datos de cada suscripción. Utilice recomendaciones guiadas por directivas para orientar a los propietarios de recursos en el proceso de implementación de los controles necesarios. De este modo, eliminará las conjeturas en lo que respecta a la seguridad en la nube.

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>Implementación de soluciones de seguridad en la nube integradas de forma sencilla
[Habilite soluciones de seguridad](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) de Microsoft y de sus asociados, incluidos los principales firewalls del sector y antimalware. Utilice aprovisionamiento optimizado para implementar soluciones de seguridad. Incluso los cambios de red se configuran automáticamente. Los eventos de seguridad de soluciones de asociados se recopilan también de forma automática con fines de análisis y alerta.

### <a name="detect-threats-and-respond-fast"></a>Detección de amenazas y respuesta rápida
Adelántese a las amenazas en la nube, tanto a las actuales como a las que puedan surgir, con una estrategia integrada basada en el análisis. Security Center combina la experiencia y los recursos de [inteligencia globales de Microsoft en materia de amenazas](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) con el conocimiento de los eventos relacionados con la seguridad en la nube de sus implementaciones en Azure. De este modo, le ayuda a detectar a tiempo amenazas reales y reduce los falsos positivos. Las alertas de seguridad en la nube ofrecen conocimiento sobre la campaña del ataque, incluidos los eventos relacionados y los recursos afectados, y sugieren formas para remediar los problemas y recuperarse con rapidez.

## <a name="end-to-end-scenario-based-network-monitoring"></a>Supervisión de las redes basada en un escenario integral
Los clientes pueden crear una red integral en Azure mediante la orquestación y composición de varios recursos de red individuales como, por ejemplo, redes virtuales, ExpressRoute, Application Gateway, equilibradores de carga y muchos más. Se puede supervisar cada uno de los recursos de la red.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) es un servicio regional que permite supervisar y diagnosticar problemas en un nivel de escenario de red mediante Azure. Las herramientas de visualización y diagnóstico de red que incluye Network Watcher le ayudan a conocer, diagnosticar y obtener información acerca de cualquier red de Azure.

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>Automatización de la supervisión de la red remota con captura de paquetes
Supervise y diagnostique problemas de red sin iniciar sesión en las máquinas virtuales (VM) mediante Network Watcher. Desencadene la [captura de paquetes](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture) estableciendo alertas y obtenga acceso a información de rendimiento en tiempo real en el ámbito de paquete. Cuando vea un problema, podrá investigar en detalle para mejorar los diagnósticos.

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>Obtención de información detallada sobre el tráfico de la red mediante registros de flujo
Conozca al detalle el patrón de tráfico de red mediante los [registros de flujo del grupo de seguridad de red](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). La información proporcionada por los registros de flujo le ayuda a recopilar datos para el cumplimiento, la auditoría y la supervisión de su perfil de seguridad de red.

### <a name="diagnose-vpn-connectivity-issues"></a>Diagnóstico de problemas de conectividad VPN
Network Watcher le ofrece la posibilidad de [diagnosticar los problemas más comunes de las conexiones y VPN Gateway](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity). Esto no solo le permite identificar el problema sino también usar los registros detallados creados para ayudar a investigar más.

Para más información sobre cómo configurar Network Watcher y cómo habilitarlo, lea el artículo [Creación de una instancia de Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="secure-deployment-using-proven-devops-tools"></a>Implementación segura mediante las herramientas de DevOps comprobadas
Estas son algunas de las prácticas incluidas en la lista de Azure DevOps en este espacio de Microsoft Cloud, que repercuten en la mejora de la productividad y la eficiencia de las empresas y los equipos humanos.

-   **Infraestructura como código (IaC):** se trata de un conjunto de técnicas y procedimientos recomendados que ayudan a los profesionales de TI a aminorar la carga de trabajo que supone la creación y administración cotidianas de una infraestructura modular. Permite a los profesionales de TI crear y mantener su entorno de servidor moderno de forma similar a como los programadores de software crean y mantienen el código de las aplicaciones. En Azure, [Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) le permite aprovisionar las aplicaciones mediante una plantilla declarativa. En una plantilla, puede implementar varios servicios junto con sus dependencias. Use la misma plantilla para implementar su aplicación de forma repetida durante cada fase de su ciclo de vida.
-   **Implementación e integración continuas**: puede configurar los proyectos de equipo de Visual Studio Online para que se [compilen y se implementen automáticamente](https://www.visualstudio.com/docs/build/overview) en Azure Web Apps o Cloud Services. VSO implementa automáticamente los archivos binarios después de realizar una compilación en Azure tras cada comprobación de código. El proceso de compilación del paquete que se describe en este artículo es equivalente al comando Package en Visual Studio y los pasos de publicación son equivalentes al comando Publish en Visual Studio.
-   **Administración de versiones:** Visual Studio [Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) es una excelente solución para automatizar la implementación en varias fases y administrar el proceso de publicación. Cree canalizaciones de implementación continua y administrada con el fin de que el lanzamiento sea rápido, sencillo y frecuente. Con Release Management, podemos automatizar en gran medida el proceso de lanzamiento así como tener predefinidos flujos de trabajo de aprobación. Realice la implementación de forma local y en la nube, amplíela y personalícela según sea necesario.
-   **Supervisión del rendimiento de las aplicaciones:** detecte y resuelva los problemas, y mejore continuamente las aplicaciones. Diagnostique rápidamente cualquier problema en su aplicación activa. Sepa lo que sus usuarios hacen con ella. La configuración es cuestión de agregar código de Javascript y una entrada webconfig, y verá los resultados en minutos en el portal, con todos los detalles. [App Insights](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) ayuda a las empresas a agilizar la detección y corrección de problemas.
-   **Prueba de carga y escalado automático:** se pueden detectar los problemas de rendimiento en la aplicación para mejorar la calidad de la implementación y asegurarse de que esté siempre activada o disponible para satisfacer las necesidades empresariales. Asegúrese de que la aplicación puede controlar el tráfico para su campaña de marketing o su siguiente lanzamiento. Empiece a ejecutar [pruebas de carga](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) basadas en la nube sin invertir apenas tiempo con Visual Studio Online.

## <a name="next-steps"></a>Pasos siguientes
- Más información acerca de la [Seguridad operativa de Azure](https://docs.microsoft.com/azure/security/azure-operational-security).
- Para más información, consulte [Detección de amenazas y prevención con seguridad avanzada en la nube](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Introducción a la solución Seguridad y auditoría de Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started).
