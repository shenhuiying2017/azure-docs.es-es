---
title: "Información general sobre la seguridad operativa de Azure| Microsoft Docs"
description: "En este artículo se proporciona información general sobre la seguridad operativa de Azure."
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
ms.date: 07/27/2017
ms.author: tomsh
ms.openlocfilehash: f2153e783adb955cf9055b09ba9aa2592f51e4b4
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="azure-operational-security-overview"></a>Información general sobre la seguridad operativa de Azure
Con seguridad operativa de Azure, se hace referencia a los servicios, los controles y las características disponibles para los usuarios para proteger sus datos, aplicaciones y otros recursos en Microsoft Azure. La [seguridad operativa de Azure](https://docs.microsoft.com/azure/security/azure-operational-security) es una plataforma que incorpora el conocimiento adquirido a través de diversas funcionalidades exclusivas de Microsoft, incluido el ciclo de vida de desarrollo de seguridad (SDL) de Microsoft, el programa Microsoft Security Response Center y un conocimiento en profundidad del panorama de amenazas de ciberseguridad.

Este artículo de información general sobre la seguridad operativa de Azure se centra en las siguientes áreas:

- Azure Operations Management Suite
-   Azure Security Center
-   Azure Monitor
-   Azure Network Watcher
-   Azure Storage Analytics
-   Azure Active Directory

## <a name="azure-operations-management-suite"></a>Azure Operations Management Suite
Las operaciones de TI son responsables de administrar la infraestructura de centro de datos, las aplicaciones y los datos, incluida la estabilidad y la seguridad de estos sistemas. Sin embargo, la obtención de información de seguridad sobre el cada vez mayor número de entornos de TI complejos, a menudo requiere que las organizaciones reúnan datos a partir de distintos sistemas de administración y seguridad.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) es la solución de administración de TI basada en la nube de Microsoft que le ayuda a administrar y proteger su infraestructura local y en la nube.

OMS es una solución de administración de TI basada en la nube con muchas ofertas, como IT Automation, Security & Compliance, Log Analytics y Backup & Recovery. Por lo tanto, es una perfecta ayuda para administrar y proteger su infraestructura de TI: de forma local y en la nube.

Un conjunto de servicios que se ejecutan en Azure proporciona la funcionalidad principal de OMS. Cada servicio ofrece una función de administración específica, y se pueden combinar los servicios para lograr escenarios de administración diferentes. Incluye:

-   Log Analytics
-   Automation
-   Backup
-   Site Recovery

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) proporciona servicios de supervisión para OMS recopilando datos de los recursos administrados en un repositorio central. Estos datos podrían incluir eventos, datos de rendimiento o datos personalizados proporcionados a través de la API. Una vez recopilados, los datos están disponibles para las alertas, el análisis y la exportación. Este método permite consolidar datos de varios orígenes, por lo que puede combinar datos de los servicios de Azure con el entorno local existente. También separa claramente la recopilación de los datos de la acción realizada en los datos, para que todas las acciones estén disponibles para todos los tipos de datos.

### <a name="automation"></a>Automation
Microsoft [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) ofrece a los usuarios una manera de automatizar las tareas manuales, propensas a errores, con una ejecución prolongada y repetidas con frecuencia que se realizan normalmente en un entorno empresarial y en la nube. Ahorra tiempo y aumenta la confiabilidad de las tareas administrativas periódicas e incluso las programa para que se realicen automáticamente a intervalos regulares. Puede automatizar procesos mediante runbooks o automatizar la administración de configuración mediante la configuración de estado deseado.

### <a name="backup"></a>Copia de seguridad
[Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) es el servicio de Azure que puede usar para hacer una copia de seguridad de los datos (protegerlos) y restaurarlos en Microsoft Cloud. Reemplaza su solución de copia de seguridad local o remota existente por una solución confiable, segura y rentable basada en la nube. Azure Backup ofrece varios componentes que se descargan e implementan en el equipo o servidor adecuados, o en la nube. El componente, o agente, que se implemente depende de lo que quiera proteger. Todos los componentes de Azure Backup (sin importar si va a proteger los datos de forma local o en la nube) se pueden usar para realizar una copia de seguridad de datos en un almacén de Recovery Services de Azure. Vea la [tabla de componentes de Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) proporciona continuidad del negocio a través de la organización de la replicación de máquinas virtuales y físicas locales en Azure, o en un sitio secundario. Si su sitio primario no está disponible, se realizará la conmutación por error a la ubicación secundaria para que los usuarios pueden seguir trabajando y se realizará una conmutación por recuperación cuando los sistemas vuelvan a las condiciones de funcionamiento normales. detección de amenazas inteligente y eficaz.

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) es la solución completa de identidad como servicio (IDaaS) de Microsoft que:

-   Habilita la IAM como un servicio en la nube.
-   Proporciona administración de acceso central, inicio de sesión único (SSO) y creación de informes.
-   Admite la administración de acceso integrado para [miles de aplicaciones](https://azure.microsoft.com/marketplace/active-directory/) de la Galería de aplicaciones, incluidas Salesforce, Google Apps, Box, Concur, etc.

Azure AD también incluye una serie completa de [capacidades de administración de identidades](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), incluida [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), el [registro de dispositivos]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview), [la administración de contraseñas de autoservicio](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/), [la administración de grupos de autoservicio](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), [la administración de cuentas con privilegios](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), [el control de acceso basado en roles](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is), [la supervisión del uso de aplicaciones](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health), [la auditoría enriquecida](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) y la [supervisión y alertas de seguridad](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts).

Con todas las aplicaciones de Azure Active Directory que publica para sus asociados y clientes (empresa o consumidor) tendrá las mismas funcionalidades de administración de identidad y acceso. Esto permite reducir significativamente los costos operativos.

## <a name="azure-security-center"></a>Azure Security Center
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started) ayuda a evitar, detectar y responder a amenazas con más visibilidad y control sobre la seguridad de sus recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

[Security Center](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) le ayuda a proteger los datos de máquinas virtuales en Azure proporcionando visibilidad en la configuración de seguridad de su máquina virtual y supervisando las amenazas. Security Center puede supervisar las máquinas virtuales con los siguientes fines:

-   Configuración de seguridad del sistema operativo (SO) con las reglas de configuración recomendadas
-   Seguridad del sistema y actualizaciones críticas que faltan
-   Recomendaciones de protección de puntos de conexión
-   Validación de cifrado de disco
-   Ataques basados en la red

Azure Security Center usa el [control de acceso basado en roles (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure), que proporciona [roles integrados](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) que se pueden asignar a usuarios, grupos y servicios de Azure.

Security Center evalúa la configuración de los recursos para identificar problemas de seguridad y vulnerabilidades. En Security Center, solo se muestra información relacionada con un recurso cuando tiene asignado el rol de Propietario, Colaborador o Lector a la suscripción o grupo de recursos al que pertenece un recurso.

>[!Note]
>Consulte [Permisos en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-permissions) para más información sobre los roles y las acciones permitidas en Security Center.

Security Center usará Microsoft Monitoring Agent, que es el mismo agente que usan la solución Operations Management Suite y el servicio Log Analytics. Los datos recopilados por este agente se almacenan en [áreas de trabajo](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) existentes de Log Analytics asociadas con la suscripción de Azure o en unas nuevas áreas de trabajo, según la geolocalización de la máquina virtual.

## <a name="azure-monitor"></a>Azure Monitor
Los problemas de rendimiento de la aplicación en la nube pueden afectar a su negocio. Con varios componentes interconectados y versiones frecuentes, las degradaciones pueden ocurrir en cualquier momento. Y, si va a desarrollar una aplicación, los usuarios normalmente encuentran problemas que no se han detectado durante las pruebas. Debe tener conocimiento de estos problemas de inmediato y disponer de las herramientas de diagnóstico y solución de problemas.

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) es una herramienta básica para la supervisión de servicios que se ejecutan en Azure. Proporciona datos a nivel de infraestructura sobre el rendimiento de un servicio y el entorno circundante. Si va a administrar todas las aplicaciones en Azure, decida si desea escalar o reducir verticalmente los recursos y luego Azure Monitor proporciona lo que debe usar para empezar.

Además, puede usar datos de supervisión para obtener un conocimiento más profundo sobre su aplicación. Este conocimiento puede ayudarle a mejorar el rendimiento o mantenimiento de la aplicación, o a automatizar acciones que de lo contrario requerirían intervención manual. Incluye:

-   Azure Activity Log
-   Registros de diagnóstico de Azure
-   Métricas
-   Diagnóstico de Azure

### <a name="azure-activity-log"></a>Azure Activity Log
Es un registro que proporciona información sobre las operaciones que se realizaron en los recursos de su suscripción. El [registro de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) se conocía antes como "Registros de auditoría" o "Registros operativos", ya que notifica eventos del plano de control para las suscripciones.

### <a name="azure-diagnostic-logs"></a>Registros de diagnóstico de Azure
Un recurso emite [registros de diagnóstico de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) que proporcionan datos exhaustivos y frecuentes acerca del funcionamiento de ese recurso. El contenido de estos registros varía según el tipo de recurso.

Por ejemplo, los registros del sistema de eventos de Windows son una categoría de registro de diagnóstico para máquinas virtuales y los registros de blob, tabla y cola son categorías de los registros de diagnóstico para cuentas de almacenamiento.

Los registros de diagnóstico son distintos del [registro de actividad (anteriormente conocido como registro de auditoría o registro operativo)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). El registro de actividad proporciona información sobre las operaciones que se realizaron en los recursos de su suscripción. Los registros de diagnóstico proporcionan información detallada sobre las operaciones que el mismo recurso realiza.

### <a name="metrics"></a>Métricas
Azure Monitor permite utilizar telemetría para obtener información sobre el rendimiento y el estado de las cargas de trabajo en Azure. El tipo de telemetría de datos de Azure más importante son las métricas (también denominadas contadores de rendimiento) emitidas por la mayoría de los recursos de Azure. Azure Monitor proporciona varias maneras de configurar y consumir estas [métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) para supervisar y solucionar problemas.

### <a name="azure-diagnostics"></a>Diagnóstico de Azure
Es la funcionalidad de Azure que habilita la recopilación de datos de diagnóstico en una aplicación implementada. Puede utilizar la extensión de diagnóstico desde diversos orígenes diferentes. Actualmente se admiten [roles web y de trabajo del servicio en la nube de Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [máquinas virtuales de Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) que ejecutan Microsoft Windows, y [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).


## <a name="network-watcher"></a>Network Watcher
Los clientes pueden crear una red integral en Azure mediante la orquestación y composición de varios recursos de red individuales como, por ejemplo, redes virtuales, ExpressRoute, Application Gateway, equilibradores de carga y muchos más. Se puede supervisar cada uno de los recursos de la red.

La red integral puede tener configuraciones complejas e interacciones entre los recursos, lo cual crea escenarios complejos que necesitan una supervisión basada en escenarios mediante Network Watcher.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) simplificará la supervisión y diagnóstico de la red de Azure. Las herramientas de diagnóstico y visualización disponibles con Network Watcher le permiten realizar capturas de paquetes remotas en una VM de Azure, obtener información detallada en el tráfico de red con registros de flujo y diagnosticar VPN Gateway y conexiones.

En la actualidad, Network Watcher dispone de las siguientes funcionalidades:

- [Topología](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): proporciona una vista de nivel de red que muestra las diversas interconexiones y asociaciones entre los recursos de red de un grupo de recursos.
-   [Captura de paquetes variable](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): captura datos de paquetes dentro y fuera de una máquina virtual. Opciones de filtrado avanzadas y controles optimizados con los que se pueden establecer límites de tiempo y de tamaño, lo cual proporciona una gran versatilidad. Los datos de paquete se pueden almacenar en un almacén de blobs o en el disco local en formato .cap.
-   [Comprobación de flujos de IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): comprueba si un paquete está permitido o no en función de los parámetros de paquete de 5-tuplas de información del flujo (IP de destino, IP de origen, puerto de destino, puerto de origen y protocolo). Si un grupo de seguridad deniega un paquete, se devuelve el nombre de la regla y del grupo que lo deniega.
-   [Próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): determina el próximo salto para los paquetes que se enrutan en el tejido de red de Azure, lo que le permite diagnosticar cualquier ruta definida por el usuario que se haya configurado incorrectamente.
-   [Vista de grupos de seguridad](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): obtiene las reglas de seguridad eficaces que se aplican en una máquina virtual.
-   [Registros de flujos de NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview): los registros de flujos de los grupos de seguridad de red permiten capturar registros relacionados con el tráfico que están permitidos o no por las reglas de seguridad del grupo. El flujo se define mediante una información de 5-tuplas: IP de origen, IP de destino, puerto de origen, puerto de destino y protocolo.
-   [Solución de problemas de la puerta de enlace de Virtual Network y de las conexiones](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): ofrece la capacidad para solucionar problemas con las puertas de enlace de Virtual Network y las conexiones.
-   [Límites de suscripción de red](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): le permite ver el uso de los recursos de la red en comparación con los límites.
-   [Configuración del registro de diagnósticos](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): proporciona un único panel para habilitar o deshabilitar los registros de diagnóstico de los recursos de red de un grupo de recursos.

Para obtener más información sobre cómo configurar Network Watcher, vea [Configuración de Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="developer-operations-devops"></a>Developer Operations (DevOps)
Antes de desarrollo de aplicaciones de DevOps, los equipos eran responsables de recopilar los requisitos de negocios para un programa de software y un código de escritura. A continuación, un equipo independiente de QA realiza una prueba del programa en un entorno de desarrollo aislado, si se cumplieron los requisitos y libera el código para las operaciones que implementar. Los equipos de implementación se dividen a su vez en grupos en silos como redes y base de datos. Cada vez que un programa de software se "lanza por encima de la pared" a un equipo independiente, se agregan cuellos de botella.

[DevOps](https://www.visualstudio.com/learn/what-is-devops/) permite a los equipos ofrecer soluciones más seguras y de mayor calidad más rápidas y baratas. Los clientes esperan una experiencia dinámica y confiable al consumir servicios y software.  Los equipos deben iterar rápidamente sobre actualizaciones de software, medir el impacto de las actualizaciones y responder rápidamente con nuevas iteraciones de desarrollo para solucionar problemas o para proporcionar más valor.  Las plataformas en la nube, como Microsoft Azure, han quitado los cuellos de botella tradicionales y ayudaron a homogeneizar la infraestructura. El software impera en cada negocio como factor y diferenciador claves en los resultados empresariales. Ninguna organización, desarrollador o trabajador de TI puede o debe evitar el movimiento de DevOps.

Los profesionales de DevOps consolidados adoptarán algunos de los siguientes procedimientos. Estos procedimientos [implican que personas](https://www.visualstudio.com/learn/what-is-devops-culture/) realicen estrategias basadas en escenarios empresariales.  Las herramientas pueden ayudarle a automatizar varias prácticas:

-   Las técnicas de [administración de proyectos y planificación ágiles](https://www.visualstudio.com/learn/what-is-agile/) se usan para planear y aislar el trabajo en sprints, administrar la capacidad del equipo y ayudar a los equipos a adaptarse rápidamente ante las cambiantes necesidades empresariales.
-   [Control de versiones, normalmente con Git](https://www.visualstudio.com/learn/what-is-git/), permite que los equipos ubicados en cualquier lugar del mundo para compartir código fuente e integrarlo con herramientas de desarrollo de software para automatizar la canalización de versiones.
-   [Integración continua](https://www.visualstudio.com/learn/what-is-continuous-integration/) impulsa la fusión en curso y la prueba de código, que permite la detección temprana de defectos.  Entre otras ventajas se incluye menos tiempo perdido en la lucha de problemas de fusión y comentarios rápidos para los equipos de desarrollo.
-   La [entrega continua](https://www.visualstudio.com/learn/what-is-continuous-delivery/) de soluciones de software para los entornos de producción y prueba ayudan a las organizaciones a solucionar los problemas rápidamente y a responder a los requisitos empresariales en constante cambio.
-   La [supervisión](https://www.visualstudio.com/learn/what-is-monitoring/) de aplicaciones en ejecución, incluidos los entornos de producción para el estado de la aplicación, así como el uso del cliente, ayudan a las organizaciones a plantear una hipótesis y validar o refutar estrategias rápidamente.  Los datos enriquecidos se capturan y almacenan en distintos formatos de registro.
-   La [infraestructura como código (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) es un procedimiento que permite la automatización y validación de la creación y destrucción de redes y máquinas virtuales para ayudar a proporcionar plataformas de host de aplicaciones estables y seguras.
-   La arquitectura de [microservicios](https://www.visualstudio.com/learn/what-are-microservices/) aprovecha el aislamiento de los casos de uso empresariales en servicios reusables reducidos.  Esta arquitectura permite la escalabilidad y la eficacia.

## <a name="next-steps"></a>Pasos siguientes
Para aprender más acerca de la solución Security and Audit de OMS, vea los siguientes artículos:

- [Operations Management Suite | Security & Compliance](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Supervisión de las alertas de seguridad y su respuesta en la solución Security and Audit de Operations Management Suite](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-security-responding-alerts)
- [Supervisión de los recursos en la solución Security and Audit de Operations Management Suite](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-security-monitoring-resources)
