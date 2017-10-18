---
title: "Integración de asociados y soluciones en Azure Security Center | Microsoft Docs"
description: "Aprenda cómo Azure Security Center se integra con los asociados para mejorar la seguridad general de los recursos de Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: a6998c997840f1a9f349b85a4274908b611cd315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="partner-and-solutions-integration-in-azure-security-center"></a>Integración de asociados y soluciones en Azure Security Center

En este artículo se describe la forma en que Azure Security Center se integra con los asociados para ayudarle a mejorar la seguridad general. Security Center ofrece una experiencia integrada en Azure y aprovecha Azure Marketplace para la certificación de asociados y su facturación.

## <a name="deploy-partner-solutions-from-security-center"></a>Implementación de soluciones de asociados desde Security Center

Las cuatro principales razones para usar la integración de asociados en Security Center son:

- **Facilidad de implementación**. Resulta mucho más fácil implementar una solución de asociado siguiendo las recomendaciones de Security Center. El proceso de implementación se puede automatizar completamente mediante el uso de una topología de red y un programa de instalación predeterminados. Como alternativa, los clientes pueden elegir una opción semiautomatizada para lograr más flexibilidad y personalización.
- **Detecciones integradas**. Los eventos de seguridad de las soluciones de asociados se recopilan, agregan y aparecen automáticamente como parte de las alertas e incidentes de Security Center. Estos eventos también se fusionan con las detecciones procedentes de otros orígenes para proporcionar funcionalidades avanzadas de detección de amenazas.
- **Supervisión y administración unificadas del mantenimiento**. Los clientes pueden usar eventos de mantenimiento integrados para supervisar todas las soluciones de asociados de un vistazo. La administración básica está disponible con un acceso sencillo a la configuración avanzada mediante la solución de asociado.
- **Exportación a SIEM**. Los clientes pueden exportar todas las alertas de asociados y de Security Center en formato Common Event Format (CEF) a sistemas de administración de eventos e información de seguridad (SIEM) locales mediante la integración de registros de Azure (versión preliminar).


## <a name="partners-that-integrate-with-security-center"></a>Asociados que se integran con Security Center

Actualmente, las integraciones nativas de soluciones de asociados que hay disponibles en Azure Marketplace con Security Center son:

- **Endpoint Protection**. [Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec y [Microsoft Antimalware para servicios en la nube y máquinas virtuales de Azure](https://docs.microsoft.com/azure/security/azure-security-antimalware).
- **Firewall de aplicaciones web**. [Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) y [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/). 
- **Firewall de próxima generación** [Punto de comprobación](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) y [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html). 
- **Evaluación de vulnerabilidades** [Qualys](https://www.qualys.com/public-clouds/microsoft-azure/). 

Con el tiempo, Security Center aumentará el número de asociados de estas categorías y agregará otras nuevas. 

## <a name="deploy-a-partner-solution"></a>Implementación de una solución de asociado

En función de la configuración de su entorno de Azure y de la directiva de seguridad que haya definido, es posible que Security Center recomiende que implemente una solución de asociados. La recomendación de Security Center le guiará a través del proceso de selección e instalación de una solución de asociados. La experiencia de implementación global puede variar en función del tipo de solución y el asociado que se use. Para más información, consulte los siguientes artículos.

- [Instalar Endpoint Protection](security-center-install-endpoint-protection.md)
- [Agregar un firewall de aplicaciones web](security-center-add-web-application-firewall.md)
- [Agregar un firewall de última generación](security-center-add-next-generation-firewall.md)
- [Evaluación de vulnerabilidades no instalada](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>Administración de soluciones de asociados

Después de la implementación, para ver información acerca del mantenimiento de la solución y realizar tareas de administración básicas, en el panel **Security Center**, seleccione **Soluciones de asociados**.

![Integración de soluciones de asociados](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

El contenido que aparece en el momento de abrir Soluciones de seguridad puede variar según la infraestructura. Utilizando la imagen anterior como ejemplo, esta página contiene tres secciones:

- **Soluciones conectadas**. Muestra las soluciones conectadas a Security Center.
- **Soluciones detectadas**. Muestra las soluciones que no están conectadas a Security Center. Puede conectar estas soluciones y se mostrarán en **Soluciones conectadas**. Si Security Center no detecta ninguna solución que no esté conectada, esta sección se oculta.
- **Add data sources** (Agregar orígenes de datos). Muestra orígenes de datos de Azure y ajenos a Azure que puede agregar a Security Center.

### <a name="connected-solutions"></a>Soluciones conectadas

La sección **Soluciones conectadas** muestra todas las soluciones de seguridad que están conectadas actualmente con Security Center. 

![Soluciones conectadas](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

La información que vea puede variar según la solución. La información disponible en cada icono puede incluir:

- **Icono de empresa del asociado**. Si Security Center no tiene el icono de la empresa, se muestran los primeros caracteres del nombre del asociado.
- **Tipo de solución**. Se muestra el tipo de solución.
- **Nombre del equipo**. Se muestra el nombre del equipo.
- **Estado de mantenimiento**. Si no se envía un indicador de mantenimiento, Security Center muestra la fecha y hora del último evento recibido para indicar si la aplicación está informando o no. Si Security Center no recibe el indicador de mantenimiento de una solución concreta, el icono de la solución no aparece en esta sección.

> [!NOTE]
> Security Center muestra la fecha y hora del último evento recibido para indicar si la aplicación está informando o no. Las soluciones que no envían indicadores de mantenimiento se muestran como conectadas si son alertas o si los eventos se enviaron en los últimos 14 días.
>  

Algunas de estas soluciones podrían estar completamente integradas en Azure, otras podrían estar en el entorno local. Como Security Center admite [CEF](https://docs.microsoft.com/azure/operations-management-suite/oms-security-connect-products#what-is-cef), se puede conectar con soluciones que usan CEF, como un firewall que admita CEF. Después de que esta solución se agrega a Security Center, el firewall envía registros en formato CEF a Security Center, que lo redirige a [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview). El firewall es un recurso no de Azure que envía eventos, pero no indicadores de mantenimiento. La única información que Security Center tiene sobre el mantenimiento es la última vez que esta aplicación envió un evento. Para todos los recursos que no son de Azure, en el área de mantenimiento del icono, Security Center muestra la fecha y la hora en que se recibió el último evento. Esta información indica que el recurso que no es de Azure aún está informando.

### <a name="discovered-solutions"></a>Soluciones detectadas

La sección **Soluciones detectadas** muestra todas las soluciones que se agregaron mediante Azure. También muestra todas las soluciones que sugiere Security Center que se conecten a él.

![Soluciones detectadas](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center se puede integrar con soluciones integradas de Azure, como [Azure Active Directory (Azure AD) Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection). Si tiene una licencia de Azure AD Identity Protection, pero no está conectada a Security Center, Azure AD Identity Protection aparece en la lista **Soluciones detectadas**. Para integrar esta solución con Security Center, haga clic en **CONECTAR** en el icono **Azure AD Identity Protection** y aparecerá la página siguiente:

![Azure AD Identity Protection](./media/security-center-partner-integration/security-center-partner-integration-fig6.png)

Para terminar la conexión de Azure AD Identity Protection, seleccione un área de trabajo en la que guardar los datos. Todos los datos de Azure AD Identity Protection se transmiten desde la región del área de trabajo que se ha seleccionado en este paso. Use el selector de áreas de trabajo para seleccionar el área de trabajo y los datos comenzarán a transmitirse ahí.

Para conectar con Security Center, debe ser un administrador global o un administrador de seguridad. Si no tiene permisos, el botón **Conectar** está deshabilitado. Un mensaje aparece para explicar por qué el botón está deshabilitado.

Las alertas de Azure AD Identity Protection pasan por la canalización de detección de Security Center. De esta forma, recibe alertas de Security Center y Azure AD Identity Protection. Security Center combina todas las alertas que parezcan pertinentes para crear un [incidente de seguridad](https://docs.microsoft.com/azure/security-center/security-center-incident). La descripción del incidente de seguridad le proporciona más conocimientos sobre la actividad sospechosa.

### <a name="add-data-sources"></a>Agregar orígenes de datos

Puede agregar equipos de Azure y ajenos a Azure para su integración con Security Center. La agregación de equipos que no son de Azure significa que puede agregar un equipo local o una aplicación compatible con CEF. 

![Orígenes de datos](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="see-also"></a>Otras referencias

En este documento, ha aprendido a integrar soluciones de asociados en Security Center. Para más información sobre Security Center, consulte los siguientes artículos:

* [Guía de planeamiento y operaciones de Security Center](security-center-planning-and-operations-guide.md)
* [Responder a alertas de seguridad en Security Center](security-center-managing-and-responding-alerts.md)
* [Comprensión de las alertas de seguridad en Azure Security Center](security-center-alerts-type.md)
* [Supervisión del estado de seguridad en Security Center](security-center-monitoring.md). Aprenda a supervisar el estado de los recursos de Azure.
* [Supervise las soluciones de asociados con Security Center](security-center-partner-solutions.md). Aprenda cómo supervisar el estado de mantenimiento de las soluciones de sus asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md). Obtenga respuestas a preguntas frecuentes acerca del uso de Security Center.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/). Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.
