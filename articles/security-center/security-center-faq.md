---
title: "Preguntas más frecuentes sobre Azure Security Center | Microsoft Docs"
description: "Estas preguntas más frecuentes responden a las preguntas sobre el Centro de seguridad de Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2018
ms.author: terrylan
ms.openlocfilehash: 2bbd0a8be891bd472cdc631a1f8dc79471d66a77
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Preguntas más frecuentes sobre el Centro de seguridad de Azure
Estas preguntas más frecuentes responden a preguntas sobre Azure Security Center, un servicio que le ayuda a evitar y detectar amenazas y a responder a las mismas con mayor visibilidad y control sobre la seguridad de los recursos de Microsoft Azure.

> [!NOTE]
> Desde primeros de junio de 2017, Security Center usará Microsoft Monitoring Agent para recopilar y almacenar datos. Para obtener más información, consulte [Migración de la plataforma de Azure Security Center](security-center-platform-migration.md). La información de este artículo representa la funcionalidad de Security Center después de la transición a Microsoft Monitoring Agent.
>
>

## <a name="general-questions"></a>Preguntas generales
### <a name="what-is-azure-security-center"></a>¿Qué es el Centro de seguridad de Azure?
El Centro de seguridad de Azure ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en sus recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

### <a name="how-do-i-get-azure-security-center"></a>¿Cómo puedo obtener el Centro de seguridad de Azure?
Azure Security Center se habilita con la suscripción de Microsoft Azure y se accede a él desde el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/). ([Inicie sesión en el portal](https://portal.azure.com), seleccione **Examinar** y desplácese al **Security Center**).  

## <a name="billing"></a>Facturación
### <a name="how-does-billing-work-for-azure-security-center"></a>¿Cómo funciona la facturación para el Centro de seguridad de Azure?
Security Center se ofrece en dos niveles:

El nivel **Gratis** permite ver el estado de seguridad de los recursos de Azure, la directiva de seguridad básica, las recomendaciones de seguridad, y la integración con los productos y servicios de seguridad de los asociados.

En el **nivel Estándar** se agregan funcionalidades de detección de amenazas avanzada, como inteligencia de amenazas, análisis del comportamiento, detección de anomalías, incidentes de seguridad e informes de atribución de amenazas. El nivel Estándar es gratuito durante los primeros 60 días. Si decide seguir utilizando el servicio después de esos 60 días, automáticamente se empieza a cobrar por el servicio.  Para actualizarlo, seleccione el [plan de tarifa](https://docs.microsoft.com/azure/security-center/security-center-pricing) de la directiva de seguridad.

## <a name="permissions"></a>Permisos
Azure Security Center usa el [control de acceso basado en roles (RBAC)](../active-directory/role-based-access-control-configure.md), que proporciona [roles integrados](../active-directory/role-based-access-built-in-roles.md) que se pueden asignar a usuarios, grupos y servicios de Azure.

Security Center evalúa la configuración de los recursos para identificar problemas de seguridad y vulnerabilidades. En Security Center, solo se muestra información relacionada con un recurso cuando tiene asignado el rol de Propietario, Colaborador o Lector a la suscripción o grupo de recursos al que pertenece un recurso.

Consulte [Permisos en Azure Security Center](security-center-permissions.md) para más información sobre los roles y las acciones permitidas en Security Center.

## <a name="data-collection"></a>Colección de datos
Security Center recopila datos de las máquinas virtuales para evaluar su estado de seguridad, proporcionar recomendaciones de seguridad y avisarle de las amenazas. La primera vez que se accede a Azure Security Center la recopilación de datos se habilita en todas las máquinas virtuales de la suscripción. También se puede habilitar la recopilación de datos en la directiva de Security Center.

### <a name="how-do-i-disable-data-collection"></a>¿Cómo se puede deshabilitar la recolección de datos?
Si se utiliza el nivel Gratis de Azure Security Center, también es posible deshabilitar en cualquier momento la recopilación de datos de las máquinas virtuales. La recopilación de datos es necesaria para las suscripciones del nivel Estándar. Se puede deshabilitar la recopilación de datos de una suscripción en la directiva de seguridad. ([Inicie sesión en Azure Portal](https://portal.azure.com), seleccione **Examinar**, **Security Center** y, luego, **Directiva**).  Cuando se selecciona una suscripción, se abre una hoja nueva que le brinda la opción de deshabilitar la opción **Colección de datos**.

### <a name="how-do-i-enable-data-collection"></a>¿Cómo se puede habilitar la recolección de datos?
Puede habilitar la colección de datos de la suscripción de Azure en la directiva de seguridad. Para habilitar la recopilación de datos, [inicie sesión en Azure Portal](https://portal.azure.com), seleccione **Examinar**, **Security Center** y, luego, **Directiva**. Establezca **Recopilación de datos** en **Activar**.

### <a name="what-happens-when-data-collection-is-enabled"></a>¿Qué sucede cuando se habilita la colección de datos?
Cuando se habilita la recopilación de datos, Microsoft Monitoring Agent se aprovisiona automáticamente en todas las máquinas virtuales existentes y recién admitidas que estén implementadas en la suscripción.

El agente habilita el evento 4688 de creación de procesos y el campo *CommandLine* dentro del evento 4688. El registro de eventos registra los nuevos procesos creados en la VM y los servicios de detección de Security Center los supervisan. Para obtener información sobre los detalles que se registran para cada nuevo proceso, consulte los [campos de descripción en 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). El agente también recopila los eventos 4688 creados en la máquina virtual y los almacena en la búsqueda.

Cuando Security Center detecta actividad sospechosa en la máquina virtual, el cliente recibe una notificación por correo electrónico si se ha proporcionado [información de contacto de seguridad](security-center-provide-security-contact-details.md). También se puede ver una alerta en el panel de alertas de seguridad de Security Center.

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>¿Microsoft Monitoring Agent afecta al rendimiento de mis servidores?
El agente utiliza una cantidad simbólica de recursos del sistema y apenas tiene impacto en el rendimiento. Para obtener más información sobre el impacto en el rendimiento, y el agente y la extensión, vea la [guía de planeación y las operaciones](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>¿Dónde se almacenan los datos?
Los datos que recopila este agente se almacenan en un área de trabajo de Log Analytics asociada con la suscripción o en una nueva área de trabajo. Para obtener más información, consulte [Seguridad de datos de Azure Security Center](security-center-data-security.md).

## <a name="using-azure-security-center"></a>Uso del Centro de seguridad de Azure
### <a name="what-is-a-security-policy"></a>¿Qué es una directiva de seguridad?
Una directiva de seguridad define el conjunto de controles recomendados para los recursos de la suscripción específica. En Azure Security Center, se definen las directivas para las suscripciones de Azure de acuerdo con los requisitos de seguridad de la compañía y el tipo de aplicaciones o la confidencialidad de los datos de cada suscripción.

Las directivas de seguridad habilitadas en Azure Security Center generan la supervisión y recomendaciones de seguridad. Para obtener más información sobre las directivas de seguridad, consulte [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>¿Quién puede modificar una directiva de seguridad?
Para modificar una directiva de seguridad, debe ser administrador de seguridad o propietario/colaborador de esa suscripción.

Para obtener información sobre cómo configurar una directiva de seguridad, consulte [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>¿Qué es una recomendación de seguridad?
El Centro de seguridad de Azure analiza el estado de seguridad de los recursos de Azure. Las recomendaciones se crean una vez que se identifican las posibles vulnerabilidades de seguridad. Las recomendaciones le guían en el proceso de configurar el control necesario. Algunos ejemplos son:

* Aprovisionamiento de antimalware para ayudar a identificar y eliminar el software malintencionado.
* Configuración de reglas y [grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md) para controlar el tráfico a las máquinas virtuales.
* Aprovisionamiento de un firewall de aplicaciones web para ayudar a defenderse contra ataques dirigidos a las aplicaciones web.
* Implementación de actualizaciones del sistema que faltan.
* Resolución de las configuraciones de sistema operativo que no coinciden con las líneas base recomendadas.

Aquí solo se muestran las recomendaciones habilitadas en las directivas de seguridad.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>¿Cómo puedo ver el estado de seguridad actual de los recursos de Azure?
La hoja **Security Center Overview** (Introducción a Security Center) muestra la posición de seguridad general del entorno con la información desglosada por Compute, Network, Storage y datos, y aplicaciones. Cada tipo de recurso tiene un indicador que muestra si se ha identificado alguna posible vulnerabilidad de seguridad. Al hacer clic en cada icono, se muestra una lista de problemas de seguridad que ha identificado Security Center, junto con un inventario de los recursos de la suscripción.

### <a name="what-triggers-a-security-alert"></a>¿Qué desencadena una alerta de seguridad?
Azure Security Center recopila, analiza y combina automáticamente los datos de registro de los recursos de Azure, la red y soluciones de asociados como firewalls y antimalware. Cuando se detecten amenazas, se creará una alerta de seguridad. Como ejemplos se incluye la detección de:

* Máquinas virtuales en peligro que se comunican con direcciones IP malintencionadas conocidas.
* Malware avanzado detectado mediante la generación de informes de errores de Windows.
* Ataques por fuerza bruta contra máquinas virtuales.
* Alertas de seguridad de soluciones de seguridad integradas de socio, como antimalware o Firewall de aplicaciones web.

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>¿Qué diferencia hay entre las amenazas detectadas y advertidas por el Centro de respuestas de seguridad de Microsoft y las comunicadas por Azure Security Center?
El Centro de respuestas de seguridad de Microsoft (MSRC) lleva a cabo una selecta supervisión de seguridad de la red e infraestructura de Azure y recibe información sobre amenazas y quejas sobre abusos de terceros. Cuando MSRC se da cuenta de que un usuario ilegítimo o no autorizado ha tenido acceso a los datos del cliente o de que el uso de Azure por parte del cliente no cumple las condiciones de uso aceptable, un administrador de incidentes de seguridad notifica al cliente. La notificación suele consistir en el envío de un correo electrónico a los contactos de seguridad especificados en Azure Security Center o al propietario de la suscripción de Azure en caso de no especificarse un contacto de seguridad.

Azure Security Center es un servicio de Azure que no deja de supervisar el entorno de Azure del cliente y aplica análisis para detectar de forma automática una amplia variedad de actividades potencialmente malintencionadas. Estas detecciones aparecen como alertas de seguridad en el panel de Azure Security Center.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>¿Qué recursos de Azure supervisa Azure Security Center?
Azure Security Center supervisa los siguientes recursos de Azure:

* Máquinas virtuales (se incluyen [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Instancias de Azure Virtual Network
* Servicio de SQL Azure
* Cuenta de Azure Storage
* Azure Web Apps ([en App Service Environment](../app-service/environment/intro.md))
* Soluciones de asociados integradas en su suscripción de Azure, como un firewall de aplicaciones web en las máquinas virtuales y en App Service Environment

## <a name="virtual-machines"></a>Virtual Machines
### <a name="what-types-of-virtual-machines-are-supported"></a>¿Qué tipos de máquinas virtuales se admiten?
La funcionalidad de supervisión y las recomendaciones están disponibles para las máquinas virtuales creadas con los [modelos de implementación clásica y de Resource Manager](../azure-classic-rm.md).

Consulte [Plataformas compatibles con Azure Security Center](security-center-os-coverage.md) para ver una lista de plataformas compatibles.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>¿Por qué Azure Security Center no reconoce la solución antimalware que se ejecuta en mi máquina virtual de Azure?
Azure Security Center solo tiene visibilidad del antimalware instalado mediante extensiones de Azure. Por ejemplo, Security Center no puede detectar antimalware instalado previamente en una imagen que haya proporcionado o si instaló este tipo de herramienta en máquinas virtuales con sus propios procesos (como sistemas de administración de configuración).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>¿Por qué recibo el mensaje Faltan los datos de detección en mi máquina virtual?
Este mensaje aparece cuando no hay datos de examen de una máquina virtual. Los datos de detección pueden tardar algún tiempo (menos de una hora) en rellenarse después de habilitar la recolección de datos en Azure Security Center. Tras el rellenado inicial de los datos de examen, se puede recibir este mensaje porque no hay ningún dato de examen o porque no hay ninguno que sea reciente. Estos análisis no se rellenan para las máquinas virtuales que estén detenidas. Este mensaje también podría aparecer si no se han rellenado datos de examen recientemente (de acuerdo con la directiva de retención del agente de Windows, que tiene un valor predeterminado de 30 días).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>¿Con qué frecuencia Security Center analiza las vulnerabilidades del sistema operativo, las actualizaciones del sistema y los problemas de Endpoint Protection?
La latencia de los análisis de Security Center en busca de vulnerabilidades, actualizaciones y problemas es:

- Configuraciones de seguridad del sistema operativo: los datos se actualizan en un plazo de 48 horas
- Actualizaciones del sistema: los datos se actualizan en un plazo de 24 horas.
- Problemas de Endpoint Protection: los datos se actualizan en un plazo de 8 horas.

Security Center suele buscar datos nuevos cada hora. Los valores de latencia indicados anteriormente son el peor escenario, en el que no se realiza ningún análisis reciente o se producen errores en el análisis.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>¿Por qué recibo el mensaje Falta el agente de máquina virtual?
El agente de VM debe instalarse en VM para habilitar la recopilación de datos. De manera predeterminada, el agente de máquina virtual está instalado en las máquinas virtuales que se implementan desde Azure Marketplace. La entrada de blog sobre [las extensiones y el agente de máquina virtual](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)proporciona información sobre cómo instalar el Agente de máquina virtual.
