---
title: Preguntas más frecuentes sobre el Centro de seguridad de Azure | Microsoft Docs
description: Estas preguntas más frecuentes responden a las preguntas sobre el Centro de seguridad de Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2016
ms.author: terrylan

---
# Preguntas más frecuentes sobre el Centro de seguridad de Azure
Estas preguntas más frecuentes responden a preguntas sobre el Centro de seguridad de Azure, un servicio que le ayuda a evitar, detectar y responder a amenazar con mayor visibilidad y control sobre la seguridad de los recursos de Microsoft Azure.

## Preguntas generales
### ¿Qué es el Centro de seguridad de Azure?
El Centro de seguridad de Azure ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en sus recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

### ¿Cómo puedo obtener el Centro de seguridad de Azure?
Azure Security Center se habilita con la suscripción de Microsoft Azure y se accede a él desde el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/). ([Inicie sesión en el portal](https://portal.azure.com), seleccione **Examinar** y desplácese al **Centro de seguridad**).

## Facturación
### ¿Cómo funciona la facturación para el Centro de seguridad de Azure?
Security Center se ofrece en dos niveles: Gratis y Estándar.

El nivel Gratis permite establecer directivas de seguridad, así como recibir alertas de seguridad, incidentes y recomendaciones que lo guiarán por el proceso de configuración de los controles necesarios. También puede supervisar el estado de seguridad de los recursos de Azure y las soluciones de asociados integradas en la suscripción de Azure.

El nivel Estándar proporciona las características de Gratis y detecciones avanzadas: información sobre amenazas, análisis de comportamiento, análisis de bloqueos y detección de anomalías. Hay disponible una versión de prueba gratuita de 90 días del nivel Estándar. Para actualizar a este nivel, seleccione el plan de tarifa en la [directiva de seguridad](security-center-policies.md#setting-security-policies-for-subscriptions). Consulte la [página de precios](https://azure.microsoft.com/pricing/details/security-center/) para obtener más información.

## Colección de datos
Security Center recopila datos de las máquinas virtuales para evaluar su estado de seguridad, proporcionar recomendaciones de seguridad y avisarle de las amenazas. La primera vez que se accede al Centro de seguridad la recopilación de datos se habilita en todas las máquinas virtuales de la suscripción. Se recomienda utilizar la recopilación de datos, pero se puede cancelar [desactivando la recopilación de datos](#how-do-i-disable-data-collection) en la directiva de Security Center.

### ¿Cómo se puede deshabilitar la recolección de datos?
Puede deshabilitar la **recopilación de datos** de una suscripción en la directiva de seguridad en cualquier momento. ([Inicie sesión en el Portal de Azure](https://portal.azure.com), seleccione **Examinar**, **Security Center** y, luego, **Directiva**.) Cuando se selecciona una suscripción, se abre una hoja nueva que le brinda la opción de deshabilitar la **colección de datos**. Seleccione la opción **Eliminar agentes** en la barra de herramientas superior para quitar los agentes de las máquinas virtuales existentes.

> [!NOTE]
> Las directivas de seguridad se pueden establecer en el nivel de suscripción y el nivel de grupo de recursos de Azure, pero tiene que seleccionar una suscripción para desactivar la recolección de datos.
> 
> 

### ¿Cómo se puede habilitar la recolección de datos?
Puede habilitar la colección de datos de las suscripciones de Azure en la directiva de seguridad. Para habilitar la colección de datos, [inicie sesión en el Portal de Azure](https://portal.azure.com), seleccione **Examinar**, **Security Center** y, luego, **Directiva**. Establezca **Colección de datos** en **Habilitada** y configure las cuentas de almacenamiento donde desea colectar los datos (consulte la pregunta "[¿Dónde se almacenan los datos?](#where-is-my-data-stored)"). Una vez habilitada la **colección de datos**, colecta automáticamente información de eventos y configuración de seguridad de todas las máquinas virtuales compatibles de la suscripción.

> [!NOTE]
> Las directivas de seguridad se pueden establecer en el nivel de suscripción y el nivel de grupo de recursos de Azure, pero la configuración de la recopilación de datos tiene lugar solo en el nivel de suscripción.
> 
> 

### ¿Qué sucede cuando se habilita la colección de datos?
La colección de datos se habilita a través de la extensión Supervisión de seguridad de Azure y el Agente de supervisión de Azure. La extensión Supervisión de seguridad de Azure detecta diversas configuraciones de seguridad importantes y las envía a [Seguimiento de eventos para Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Además, el sistema operativo crea las entradas del registro de eventos. El Agente de supervisión de Azure lee las entradas de los registros de eventos y los seguimientos de ETW y los copia en la cuenta de almacenamiento para su análisis. Esta es la cuenta de almacenamiento que configuró en la directiva de seguridad. Para obtener más información sobre la cuenta de almacenamiento, consulte "[¿Dónde se almacenan los datos?](#where-is-my-data-stored)".

### ¿El Agente de supervisión o la extensión Supervisión de seguridad afecta el rendimiento de los servidores?
El agente y la extensión utilizan una cantidad simbólica de los recursos del sistema y tienen un impacto menor en el rendimiento.

### ¿Dónde se almacenan los datos?
Para cada región en la que disponga de máquinas virtuales en funcionamiento, elija la cuenta de almacenamiento en la que se almacenan los datos recopilados de esas máquinas virtuales. Esto facilita el mantenimiento de los datos en la misma zona geográfica para fines de privacidad y soberanía de datos. Elija la cuenta de almacenamiento para una suscripción en la directiva de seguridad. ([Inicie sesión en el Portal de Azure](https://portal.azure.com), seleccione **Examinar**, **Security Center** y, luego, **Directiva**). Cuando hace clic en una suscripción, se abre una hoja nueva. Seleccione **Elija las cuentas de almacenamiento** para seleccionar una región.

> [!NOTE]
> Las directivas de seguridad se pueden establecer en el nivel de suscripción y el nivel de grupo de recursos de Azure, pero la selección de una región para la cuenta de almacenamiento tiene lugar solo en el nivel de suscripción.
> 
> 

Para obtener más información sobre las cuentas de almacenamiento y el almacenamiento de Azure, consulte [Documentación de almacenamiento](https://azure.microsoft.com/documentation/services/storage/) y [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md).

## Uso del Centro de seguridad de Azure
### ¿Qué es una directiva de seguridad?
Una directiva de seguridad define el conjunto de controles recomendados para los recursos en la suscripción o el grupo de recursos especificados. En Azure Security Center, se definen las directivas para sus suscripciones y grupos de recursos de Azure de acuerdo con los requisitos de seguridad de la compañía y el tipo de aplicaciones o la confidencialidad de los datos de cada suscripción.

Por ejemplo, es posible que los recursos usados para el desarrollo o las pruebas tengan distintos requisitos de seguridad que los de aquellos que se emplean para aplicaciones de producción. Del mismo modo, es posible que las aplicaciones con datos regulados como información de identificación personal (PII) requieran un mayor nivel de seguridad. Las directivas de seguridad habilitadas en el Centro de seguridad de Azure generarán la supervisión y recomendaciones de seguridad. Para obtener más información sobre las directivas de seguridad, consulte [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md).

> [!NOTE]
> En caso de un conflicto entre la directiva del nivel de suscripción y la directiva del nivel de grupo de recursos, la del nivel de grupo de recursos tiene prioridad.
> 
> 

### ¿Quién puede modificar una directiva de seguridad?
Las directivas de seguridad se configuran para cada suscripción o grupo de recursos. Para modificar una directiva de seguridad en el nivel de suscripción o el nivel de grupo de recursos, debe ser el propietario de la suscripción o un colaborador de esta.

Para obtener información sobre cómo configurar una directiva de seguridad, consulte [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md).

### ¿Qué es una recomendación de seguridad?
El Centro de seguridad de Azure analiza el estado de seguridad de los recursos de Azure. Las recomendaciones se crean una vez que se identifican las posibles vulnerabilidades de seguridad. Las recomendaciones le guían en el proceso de configurar el control necesario. Algunos ejemplos son:

* Aprovisionamiento de antimalware para ayudar a identificar y eliminar el software malintencionado.
* Configuración de reglas y [grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md) para controlar el tráfico a las máquinas virtuales.
* Aprovisionamiento de un firewall de aplicaciones web para ayudar a defenderse contra ataques dirigidos a las aplicaciones web.
* Implementación de actualizaciones del sistema que faltan.
* Resolución de las configuraciones de sistema operativo que no coinciden con las líneas base recomendadas.

Aquí solo se muestran las recomendaciones habilitadas en las directivas de seguridad.

### ¿Cómo puedo ver el estado de seguridad actual de los recursos de Azure?
Un icono **Estado de los recursos** en la hoja **Centro de seguridad** muestra la posición de seguridad general del entorno, desglosada por máquinas virtuales, aplicaciones web y otros recursos. Cada recurso tiene un indicador que muestra si se identificó alguna posible vulnerabilidad de seguridad. Si hace clic en el icono Estado de los recursos, aparecen los recursos y se identifica dónde se requiere poner atención o los problemas que puedan existir.

### ¿Qué desencadena una alerta de seguridad?
El Centro de seguridad de Azure recopila, analiza y combina automáticamente los datos de registro de los recursos de Azure, la red y soluciones de asociados como firewalls y antimalware. Cuando se detecten amenazas, se creará una alerta de seguridad. Como ejemplos se incluye la detección de:

* Máquinas virtuales en peligro que se comunican con direcciones IP malintencionadas conocidas.
* Malware avanzado detectado mediante la generación de informes de errores de Windows.
* Ataques por fuerza bruta contra máquinas virtuales.
* Alertas de seguridad de soluciones de seguridad integradas de socio, como antimalware o Firewall de aplicaciones web.

### ¿Qué diferencia hay entre las amenazas detectadas y advertidas por el Centro de respuestas de seguridad de Microsoft y las comunicadas por Azure Security Center?
El Centro de respuestas de seguridad de Microsoft (MSRC) lleva a cabo una selecta supervisión de seguridad de la red e infraestructura de Azure y recibe información sobre amenazas y quejas sobre abusos de terceros. Cuando MSRC se da cuenta de que un usuario ilegítimo o no autorizado ha tenido acceso a los datos del cliente o de que el uso de Azure por parte del cliente no cumple las condiciones de uso aceptable, un administrador de incidentes de seguridad notifica al cliente. La notificación suele consistir en el envío de un correo electrónico a los contactos de seguridad especificados en Azure Security Center o el propietario de la suscripción de Azure en caso de no especificarse un contacto de seguridad.

Azure Security Center es un servicio de Azure que no deja de supervisar el entorno de Azure del cliente y aplica análisis para detectar de forma automática una amplia variedad de actividades potencialmente malintencionadas. Estas detecciones aparecen como alertas de seguridad en el panel de Azure Security Center.

### ¿Cómo se administran los permisos en el Centro de seguridad de Azure?
Azure Security Center admite el acceso basado en rol. Para obtener más información sobre el control de acceso basado en rol (RBAC) de Azure, consulte [Control de acceso basado en roles de Azure Active Directory](../active-directory/role-based-access-control-configure.md).

Cuando un usuario abre Security Center, solo se verán las recomendaciones de alertas relacionadas con los recursos a los que el usuario tiene acceso. Esto significa que el usuario verá solo los elementos relacionados con los recursos donde el usuario tiene asignado el rol de Propietario, Colaborador o Lector a la suscripción o el grupo de recursos al que pertenece un recurso.

Si necesita:

* **Editar una directiva de seguridad**, debe ser propietario o colaborador de la suscripción.
* **Aplicar una directiva de seguridad**, debe ser propietario o colaborador de la suscripción.
* **Tener visibilidad del estado de seguridad de todas sus suscripciones**, debe ser propietario, colaborador o lector (administrador de TI; equipo de seguridad) de cada suscripción.
* **Tener visibilidad del estado de seguridad de los recursos**, debe ser propietario, colaborador o lector (DevOps) de un grupo de recursos.

### ¿Qué recursos de Azure supervisa Azure Security Center?
Azure Security Center supervisa los siguientes recursos de Azure:

* Máquinas virtuales (se incluyen [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Redes virtuales de Azure
* Servicio de SQL Azure
* Soluciones de asociados integradas en su suscripción de Azure, como un firewall de aplicaciones web en las máquinas virtuales y en [App Service Environment](../app-service/app-service-app-service-environments-readme.md)

## Máquinas virtuales
### ¿Qué tipos de máquinas virtuales serán compatibles?
Está disponible la supervisión del mantenimiento de seguridad y las recomendaciones para las máquinas virtuales creadas con los [modelos de implementación clásica y de Resource Manager](../azure-classic-rm.md).

Máquinas virtuales de Windows admitidas:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2

Máquinas virtuales de Linux compatibles:

* Versiones de Ubuntu 12.04, 14.04, 15.10, 16.04
* Versiones de Debian 7, 8
* Versiones de CentOS 6.\*, 7.*
* Versiones de Red Hat Enterprise Linux (RHEL) 6.\*, 7.*
* Versiones de SUSE Linux Enterprise Server (SLES) 11.\*, 12.*

También se admiten máquinas virtuales que se ejecuten en un servicio en la nube. Se supervisan los roles de web y de trabajo de servicios en la nube que se ejecutan en espacios de producción. Para aprender más sobre el servicio en la nube, consulte la [información general sobre los servicios en la nube](../cloud-services/cloud-services-choose-me.md).

### ¿Por qué Azure Security Center no reconoce la solución antimalware que se ejecuta en mi máquina virtual de Azure?
Azure Security Center solo tiene visibilidad del antimalware instalado a través de extensiones de Azure. Por ejemplo, Security Center no puede detectar antimalware instalado previamente en una imagen que haya proporcionado o si instaló este tipo de herramienta en máquinas virtuales con sus propios procesos (como sistemas de administración de configuración).

### ¿Por qué recibo el mensaje Faltan los datos de detección en mi máquina virtual?
Los datos de detección pueden tardar algún tiempo (normalmente, menos de una hora) en rellenarse después de habilitar la recolección de datos en Azure Security Center. Estos datos no se rellenarán para las máquinas virtuales que estén detenidas.

### ¿Por qué recibo el mensaje Falta el agente de máquina virtual?
El agente de máquina virtual debe instalarse en máquinas virtuales para habilitar la recopilación de datos. De manera predeterminada, el agente de máquina virtual está instalado en las máquinas virtuales que se implementan desde Azure Marketplace. La entrada de blog sobre [las extensiones y el agente de máquina virtual](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) proporciona información sobre cómo instalar el Agente de máquina virtual.

<!---HONumber=AcomDC_0928_2016-->