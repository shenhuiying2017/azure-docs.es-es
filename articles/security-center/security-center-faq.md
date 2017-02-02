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
ms.date: 12/09/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 0135732e95279f2e717334d3dd39902b56b0aa90
ms.openlocfilehash: 30a327f59b8149f41c3b5206e0b0c2fc859934a0


---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Preguntas más frecuentes sobre el Centro de seguridad de Azure
Estas preguntas más frecuentes responden a preguntas sobre Azure Security Center, un servicio que le ayuda a evitar y detectar amenazas y a responder a las mismas con mayor visibilidad y control sobre la seguridad de los recursos de Microsoft Azure.

## <a name="general-questions"></a>Preguntas generales
### <a name="what-is-azure-security-center"></a>¿Qué es el Centro de seguridad de Azure?
El Centro de seguridad de Azure ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en sus recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

### <a name="how-do-i-get-azure-security-center"></a>¿Cómo puedo obtener el Centro de seguridad de Azure?
Azure Security Center se habilita con la suscripción de Microsoft Azure y se accede a él desde el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/). ([Inicie sesión en el portal](https://portal.azure.com), seleccione **Examinar** y desplácese al **Security Center**).  

## <a name="billing"></a>Facturación
### <a name="how-does-billing-work-for-azure-security-center"></a>¿Cómo funciona la facturación para el Centro de seguridad de Azure?
Security Center se ofrece en dos niveles: Gratis y Estándar.

El nivel Gratis permite establecer directivas de seguridad, así como recibir alertas de seguridad, incidentes y recomendaciones que lo guiarán por el proceso de configuración de los controles necesarios. Con el nivel Gratis, también puede supervisar el estado de seguridad de los recursos de Azure y las soluciones de asociados integradas en la suscripción de Azure.

El nivel Estándar proporciona las características de Gratis y detecciones avanzadas: información sobre amenazas, análisis de comportamiento, análisis de bloqueos y detección de anomalías. Hay disponible una versión de prueba gratuita de 90 días del nivel Estándar. Para actualizar a este nivel, seleccione el plan de tarifa en la [directiva de seguridad](security-center-policies.md#set-security-policies-for-subscriptions). Para más información, consulte [Precios de Azure Security Center](security-center-pricing.md).

## <a name="permissions"></a>Permisos
Azure Security Center usa el [control de acceso basado en rol (RBAC)](../active-directory/role-based-access-control-configure.md), que proporciona [roles integrados](../active-directory/role-based-access-built-in-roles.md) que se pueden asignar a usuarios, grupos y servicios en Azure.

Security Center evalúa la configuración de los recursos para identificar problemas de seguridad y vulnerabilidades. En Security Center, solo se muestra información relacionada con un recurso cuando tiene asignado el rol de Propietario, Colaborador o Lector a la suscripción o grupo de recursos al que pertenece un recurso.

Consulte [Permisos en Azure Security Center](security-center-permissions.md) para más información sobre los roles y las acciones permitidas en Security Center.

## <a name="data-collection"></a>Colección de datos
Security Center recopila datos de las máquinas virtuales para evaluar su estado de seguridad, proporcionar recomendaciones de seguridad y avisarle de las amenazas. La primera vez que se accede al Centro de seguridad la recopilación de datos se habilita en todas las máquinas virtuales de la suscripción. Se recomienda utilizar la recopilación de datos, pero se puede cancelar [desactivando la recopilación de datos](#how-do-i-disable-data-collection) en la directiva de Security Center.

### <a name="how-do-i-disable-data-collection"></a>¿Cómo se puede deshabilitar la recolección de datos?
Puede deshabilitar la **recopilación de datos** de una suscripción en la directiva de seguridad en cualquier momento. ([Inicie sesión en Azure Portal](https://portal.azure.com), seleccione **Examinar**, **Security Center** y, luego, **Directiva**).  Cuando se selecciona una suscripción, se abre una hoja nueva que le brinda la opción de deshabilitar la opción **Colección de datos**. Para quitar agentes de máquinas virtuales existentes, seleccione la opción **Eliminar agentes** en la cinta de opciones superior.

> [!NOTE]
> Las directivas de seguridad se pueden establecer en el nivel de suscripción y el nivel de grupo de recursos de Azure, pero tiene que seleccionar una suscripción para desactivar la colección de datos.
>
>

### <a name="how-do-i-enable-data-collection"></a>¿Cómo se puede habilitar la recolección de datos?
Puede habilitar la colección de datos de la suscripción de Azure en la directiva de seguridad. Para habilitar la colección de datos, [inicie sesión en Azure Portal](https://portal.azure.com), seleccione **Examinar**, **Security Center** y, luego, **Directiva**. Establezca **Colección de datos** en **Habilitada** y configure las cuentas de almacenamiento donde desea colectar los datos (consulte la pregunta "[¿Dónde se almacenan los datos?](#where-is-my-data-stored)"). Una vez habilitada la **colección de datos** , colecta automáticamente información de eventos y configuración de seguridad de todas las máquinas virtuales compatibles de la suscripción.

> [!NOTE]
> Las directivas de seguridad se pueden establecer en el nivel de suscripción y el nivel de grupo de recursos de Azure, pero la configuración de la recopilación de datos tiene lugar solo en el nivel de suscripción.
>
>

### <a name="what-happens-when-data-collection-is-enabled"></a>¿Qué sucede cuando se habilita la colección de datos?
La colección de datos se habilita a través de la extensión Supervisión de seguridad de Azure y el Agente de supervisión de Azure. La extensión Supervisión de seguridad de Azure detecta diversas configuraciones de seguridad importantes y las envía a [Seguimiento de eventos para Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Además, el sistema operativo crea las entradas del registro de eventos.  El Agente de supervisión de Azure lee las entradas de los registros de eventos y los seguimientos de ETW y los copia en la cuenta de almacenamiento para su análisis.  Esta es la cuenta de almacenamiento que configuró en la directiva de seguridad. Para obtener más información sobre la cuenta de almacenamiento, consulte "[¿Dónde se almacenan los datos?](#where-is-my-data-stored)".

### <a name="does-the-monitoring-agent-or-security-monitoring-extension-impact-the-performance-of-my-servers"></a>¿El Agente de supervisión o la extensión Supervisión de seguridad afecta el rendimiento de los servidores?
El agente y la extensión utilizan una cantidad simbólica de los recursos del sistema y tienen un impacto menor en el rendimiento. Para obtener más información sobre el impacto en el rendimiento, y el agente y la extensión, vea la [guía de planeación y las operaciones](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>¿Dónde se almacenan los datos?
Para cada región en la que disponga de máquinas virtuales en funcionamiento, elija la cuenta de almacenamiento en la que se almacenan los datos recopilados de esas máquinas virtuales. Esto facilita el mantenimiento de los datos en la misma zona geográfica para fines de privacidad y soberanía de datos. Elija la cuenta de almacenamiento para una suscripción en la directiva de seguridad. ([Inicie sesión en Azure Portal](https://portal.azure.com), seleccione **Examinar**, **Security Center** y, luego, **Directiva**). Cuando selecciona una suscripción, se abre una hoja nueva. Para seleccionar una región, seleccione **Elegir cuentas de almacenamiento**.

> [!NOTE]
> Las directivas de seguridad se pueden establecer en el nivel de suscripción y el nivel de grupo de recursos de Azure, pero la selección de una región para la cuenta de almacenamiento tiene lugar solo en el nivel de suscripción.
>
>

Para más información sobre las cuentas de almacenamiento y Azure Storage , consulte [Documentación de almacenamiento](https://azure.microsoft.com/documentation/services/storage/) y [Acerca de las cuentas de Azure Storage ](../storage/storage-create-storage-account.md).

## <a name="using-azure-security-center"></a>Uso del Centro de seguridad de Azure
### <a name="what-is-a-security-policy"></a>¿Qué es una directiva de seguridad?
Las directivas de seguridad definen el conjunto de controles recomendados para los recursos de la suscripción o el grupo de recursos especificados. En Azure Security Center, se definen las directivas para sus suscripciones y grupos de recursos de Azure de acuerdo con los requisitos de seguridad de la compañía y el tipo de aplicaciones o la confidencialidad de los datos de cada suscripción.

Por ejemplo, es posible que los recursos usados para el desarrollo o las pruebas tengan distintos requisitos de seguridad que los de aquellos que se emplean para aplicaciones de producción. Del mismo modo, es posible que las aplicaciones con datos regulados como información de identificación personal (PII) requieran un mayor nivel de seguridad. Las directivas de seguridad habilitadas en Azure Security Center generan la supervisión y recomendaciones de seguridad. Para obtener más información sobre las directivas de seguridad, consulte [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md).

> [!NOTE]
> En caso de un conflicto entre la directiva del nivel de suscripción y la directiva del nivel de grupo de recursos, la del nivel del grupo de recursos tiene prioridad.
>
>

### <a name="who-can-modify-a-security-policy"></a>¿Quién puede modificar una directiva de seguridad?
Las directivas de seguridad se configuran para cada suscripción o grupo de recursos. Para modificar una directiva de seguridad en el nivel de suscripción o el nivel de grupo de recursos, debe ser el propietario de la suscripción o un colaborador de esta.

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
Un icono **Estado de los recursos** en la hoja **Security Center** muestra la posición de seguridad general del entorno, desglosada por máquinas virtuales, aplicaciones web y otros recursos. Cada recurso tiene un indicador que muestra si se identificó alguna posible vulnerabilidad de seguridad. Si hace clic en el icono Estado de los recursos, aparecen los recursos y se identifica dónde se requiere poner atención o los problemas que puedan existir.

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
* Redes virtuales de Azure
* Servicio de SQL Azure
* Soluciones de asociados integradas en su suscripción de Azure, como un firewall de aplicaciones web en las máquinas virtuales y en [App Service Environment](../app-service/app-service-app-service-environments-readme.md)

## <a name="virtual-machines"></a>Virtual Machines
### <a name="what-types-of-virtual-machines-are-supported"></a>¿Qué tipos de máquinas virtuales se admiten?
Está disponible la supervisión del mantenimiento de seguridad y las recomendaciones para las máquinas virtuales creadas con los [modelos de implementación clásica y de Resource Manager](../azure-classic-rm.md).

Máquinas virtuales de Windows admitidas:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2

Máquinas virtuales de Linux compatibles:

* Versiones de Ubuntu 12.04, 14.04 y 16.04 y 16.10
* Versiones de Debian 7, 8
* Versiones de CentOS 6.\*, 7.*
* Versiones de Red Hat Enterprise Linux (RHEL) 6.\*, 7.*
* Versiones de SUSE Linux Enterprise Server (SLES) 11.\*, 12.*
* Versiones de Oracle Linux 6.\*, 7.*

También se admiten máquinas virtuales que se ejecuten en un servicio en la nube. Se supervisan los roles de web y de trabajo de servicios en la nube que se ejecutan en espacios de producción. Para aprender más sobre el servicio en la nube, consulte la [información general sobre los servicios en la nube](../cloud-services/cloud-services-choose-me.md).

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>¿Por qué Azure Security Center no reconoce la solución antimalware que se ejecuta en mi máquina virtual de Azure?
Azure Security Center solo tiene visibilidad del antimalware instalado a través de extensiones de Azure. Por ejemplo, Security Center no puede detectar antimalware instalado previamente en una imagen que haya proporcionado o si instaló este tipo de herramienta en máquinas virtuales con sus propios procesos (como sistemas de administración de configuración).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>¿Por qué recibo el mensaje Faltan los datos de detección en mi máquina virtual?
Los datos de detección pueden tardar algún tiempo (menos de una hora) en rellenarse después de habilitar la recolección de datos en Azure Security Center. Estos datos no se rellenan para las VM que estén detenidas.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>¿Por qué recibo el mensaje Falta el agente de máquina virtual?
El agente de VM debe instalarse en VM para habilitar la recopilación de datos. De manera predeterminada, el agente de máquina virtual está instalado en las máquinas virtuales que se implementan desde Azure Marketplace. La entrada de blog sobre [las extensiones y el agente de máquina virtual](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)proporciona información sobre cómo instalar el Agente de máquina virtual.



<!--HONumber=Dec16_HO2-->


