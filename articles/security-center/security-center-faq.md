<properties
   pageTitle="Preguntas más frecuentes sobre el Centro de seguridad de Azure | Microsoft Azure"
   description="Estas preguntas más frecuentes responden a las preguntas sobre el Centro de seguridad de Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/09/2016"
   ms.author="terrylan"/>

# Preguntas más frecuentes sobre el Centro de seguridad de Azure

En este artículo de P+F (preguntas más frecuentes) se ofrecen respuestas a preguntas sobre el Centro de seguridad de Azure.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure.

## Preguntas generales

### ¿Qué es el Centro de seguridad?
 El Centro de seguridad ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en los recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

### ¿Cómo se puede obtener el Centro de seguridad?
 El Centro de seguridad se habilita con la suscripción de Microsoft Azure y es posible tener acceso a él desde el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/). ([Inicie sesión en el portal](https://portal.azure.com), seleccione **Examinar** y luego desplácese al **Centro de seguridad**). Puede consultar algunas recomendaciones de seguridad en el panel inmediatamente. Esto se debe a que el servicio puede evaluar el estado de seguridad de algunos controles según su configuración en Azure. Para acceder al conjunto completo de funcionalidades de supervisión, recomendaciones y alertas de seguridad, deberá [habilitar la recopilación de datos](#data-collection).

## Facturación

### ¿Cómo funciona la facturación para el Centro de seguridad?
Vea [Precios del Centro de seguridad de Azure](https://azure.microsoft.com/pricing/details/security-center/) para información.

## Colección de datos

### ¿Cómo habilito la colección de datos?<a name=data-collection></a>
Puede habilitar la recopilación de datos de las suscripciones de Azure en la directiva de seguridad. Para habilitar la recopilación de datos, [inicie sesión en el Portal de Azure](https://portal.azure.com), seleccione **Examinar**, **Centro de seguridad** y, luego, **Directiva de seguridad**. Establezca **Recopilación de datos** en **Habilitada** y configure las cuentas de almacenamiento donde desea que se recopilen los datos (consulte la pregunta "[¿Dónde se almacenan los datos?](#where-is-my-data-stored)"). Una vez habilitada la **colección de datos**, colecta automáticamente información de eventos y configuración de seguridad de todas las máquinas virtuales compatibles de la suscripción.

### ¿Qué sucede cuando habilito la colección de datos?
La colección de datos se habilita a través de la extensión Supervisión de seguridad de Azure y el Agente de supervisión de Azure. La extensión Supervisión de seguridad de Azure busca diversos eventos y configuraciones de seguridad importantes en seguimientos de [Seguimiento de eventos para Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW).

Además, el sistema operativo genera eventos de registro de eventos en todas las actividades. El Agente de supervisión de seguridad de Azure lee las entradas del registro de eventos y los seguimientos de ETW y luego los copia en la cuenta de almacenamiento para analizarlos. Esta es la cuenta de almacenamiento que configuró en la directiva de seguridad. Para más información sobre la cuenta de almacenamiento, consulte la pregunta "[¿Dónde se almacenan los datos?](#where-is-my-data-stored)".

### ¿El Agente de supervisión o la extensión Supervisión de seguridad influyen en el rendimiento de los servidores?
El agente y la extensión utilizan una cantidad simbólica de los recursos del sistema y lo normal es que tengan un impacto menor en el rendimiento.

### ¿Cómo puedo revertir la habilitación de la recopilación de datos si ya no la quiero?
Puede deshabilitar la recopilación de datos de una suscripción en la directiva de seguridad. ([Inicie sesión en el Portal de Azure](https://portal.azure.com), seleccione **Examinar**, **Centro de seguridad** y, luego, **Directiva de seguridad**). Cuando hace clic en una suscripción, se abre una hoja nueva que le ofrece la opción de desactivar la recopilación de datos. Seleccione la opción **Eliminar agentes** en la barra de herramientas superior para quitar los agentes de las máquinas virtuales existentes.

### ¿Dónde se almacenan los datos?<a name=where-is-my-data-stored></a>
Para cada región en la que disponga de máquinas virtuales en funcionamiento, elija la cuenta de almacenamiento en la que se almacenan los datos recopilados de esas máquinas virtuales. Esto facilita el mantenimiento de los datos en la misma zona geográfica para fines de privacidad y soberanía de datos.

Elija la cuenta de almacenamiento para una suscripción en la directiva de seguridad. ([Inicie sesión en el Portal de Azure](https://portal.azure.com), seleccione **Examinar**, **Centro de seguridad** y, luego, **Directiva de seguridad**). Cuando hace clic en una suscripción, se abre una hoja nueva. Haga clic en **Elegir cuentas de almacenamiento** para seleccionar una región. Los datos recopilados se aíslan lógicamente de otros datos de los clientes por motivos de seguridad.

Para más información sobre las cuentas de almacenamiento y Almacenamiento de Azure, consulte la [documentación sobre almacenamiento](https://azure.microsoft.com/documentation/services/storage/) y [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md).

## Uso del Centro de seguridad

### ¿Qué es una directiva de seguridad?
Una directiva de seguridad define el conjunto de controles que se recomiendan para los recursos en la suscripción especificada. En el Centro de seguridad, se definen directivas para las suscripciones de Azure de acuerdo con los requisitos de seguridad de la empresa, el tipo de aplicaciones usadas o la confidencialidad de los datos de cada suscripción.

Por ejemplo, es posible que los recursos usados para el desarrollo o las pruebas tengan requisitos de seguridad distintos a los empleados para las aplicaciones de producción. Del mismo modo, es posible que las aplicaciones con datos regulados como información de identificación personal (PII) requieran un mayor nivel de seguridad. Las directivas de seguridad habilitadas en el Centro de seguridad controlarán la supervisión y las recomendaciones de seguridad. Para obtener más información sobre las directivas de seguridad, consulte [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md).

### ¿Quién puede modificar una directiva de seguridad?
Las directivas de seguridad se configuran para cada suscripción. Para modificar una directiva de seguridad, debe ser el propietario de la suscripción o contribuir a ella.

Para obtener información sobre cómo configurar una directiva de seguridad, consulte [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md).

### ¿Qué es una recomendación de seguridad?
 El Centro de seguridad analiza el estado de seguridad de los recursos de Azure. Cuando se identifican posibles vulnerabilidades de seguridad, el Centro de seguridad crea recomendaciones. Las recomendaciones le guían en el proceso de configurar el control necesario. Algunos ejemplos son:

- Aprovisionar programas antimalware para ayudar a identificar y eliminar el software malintencionado.
- Configurar reglas y [grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md) para controlar el tráfico a las máquinas virtuales.
- Aprovisionar firewalls de aplicaciones web para ayudar a defenderse contra ataques dirigidos a las aplicaciones web.
- Implementación de actualizaciones del sistema que faltan.
- Resolución de las configuraciones de sistema operativo que no coinciden con las líneas de base recomendadas

Aquí solo se muestran las recomendaciones habilitadas en las directivas de seguridad.

### ¿Cómo puedo ver el estado de seguridad actual de los recursos de Azure?
El icono **Estado de los recursos** de la hoja **Centro de seguridad** muestra la posición de seguridad general del entorno, desglosada en máquinas virtuales, aplicaciones web y otros recursos. Cada recurso tiene un indicador que muestra si se identificó alguna posible vulnerabilidad de seguridad. Al hacer clic en el icono **Estado de los recursos**, aparecen los recursos y se identifica dónde es necesario poner atención o los problemas que puedan existir.

### ¿Qué desencadena una alerta de seguridad?
 El Centro de seguridad recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y las soluciones de asociados como firewalls y programas antimalware. Cuando el Centro de seguridad detecta amenazas, se crea una alerta de seguridad. Como ejemplos se incluye la detección de:

- Máquinas virtuales en peligro que se comunican con direcciones IP malintencionadas conocidas.
- Malware avanzado detectado mediante la generación de informes de errores de Windows.
- Ataques por fuerza bruta contra máquinas virtuales.
- Alertas de seguridad de soluciones de seguridad de asociados integradas, como programas antimalware o firewalls de aplicaciones web.

### ¿Cómo se administran los permisos en el Centro de seguridad?
 El Centro de seguridad admite el acceso basado en rol. Para más información sobre el control de acceso basado en rol (RBAC) de Azure, consulte [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md).

Cuando los usuarios abren el Centro de seguridad, las únicas recomendaciones y alertas que verán serán las relacionadas con los recursos a los que tienen acceso. Esto significa que los usuarios solo verán los elementos relacionados con los recursos para los que tienen el rol de propietario, colaborador o lector en la suscripción o el grupo de recursos a los que pertenece un recurso.

Para editar una directiva de seguridad, debe ser el propietario de una suscripción o contribuir a ella.

### ¿Qué tipos de máquinas virtuales se admiten?
Se admiten las máquinas virtuales [clásicas y las del Administrador de recursos](../azure-classic-rm.md), incluidas las máquinas virtuales que forman parte de los clústeres de Azure Service Fabric.

Las recomendaciones de lista de control de acceso se aplican actualmente a las máquinas virtuales clásicas. Las reglas de grupos de seguridad de red y las recomendaciones de instalación de los firewalls de aplicaciones web solo se aplican actualmente a las máquinas virtuales del Administrador de recursos.

### ¿Las máquinas virtuales de Linux son compatibles?
El Centro de seguridad de Azure ofrece supervisión de línea base para máquinas virtuales de Linux (solo Ubuntu versiones 12.04, 14.04, 14.10 y 15.04). En el futuro, habrá disponible una supervisión del estado de seguridad y colección de datos/análisis adicionales, además de compatibilidad para otras distribuciones de Linux.

<!---HONumber=AcomDC_0218_2016-->