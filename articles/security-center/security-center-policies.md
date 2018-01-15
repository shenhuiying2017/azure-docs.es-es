---
title: Establecimiento de directivas de seguridad en Azure Security Center | Microsoft Docs
description: Este documento le ayuda a configurar directivas de seguridad en Azure Security Center.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2018
ms.author: yurid
ms.openlocfilehash: 4d1a30b046c0c398d934291a907af891e9ac7fdf
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="set-security-policies-in-azure-security-center"></a>Establecimiento de directivas de seguridad en Azure Security Center
Este artículo le ayuda a configurar directivas de seguridad en Security Center.

## <a name="how-security-policies-work"></a>¿Cómo funcionan las directivas de seguridad?
Security Center crea automáticamente una directiva de seguridad predeterminada para cada una de las suscripciones de Azure. En Security Center, puede editar las directivas y supervisar su cumplimiento.

> [!NOTE]
> Ahora puede ampliar las directivas de Security Center con Azure Policy, que se encuentra en versión preliminar limitada. Para unirse a la versión preliminar, vaya a [Sign up for Azure Policy](https://aka.ms/getpolicy) (Suscripción a Azure Policy). Para más información, consulte [Integración de las directivas de seguridad de Security Center con Azure Policy](security-center-azure-policy.md).

Puede que los requisitos de seguridad de los recursos que se usan en desarrollo o pruebas varíen con respecto a los requisitos de los recursos que se usan en las aplicaciones de producción. Es posible que las aplicaciones que usan datos regulados, como la información de identificación personal, requieran un mayor nivel de seguridad. Las directivas de seguridad habilitadas en Azure Security Center controlan las recomendaciones de seguridad y la supervisión para ayudarle a identificar posibles vulnerabilidades y mitigar las amenazas. Consulte [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md) para más información sobre cómo determinar la opción más adecuada en su caso.

## <a name="edit-security-policies"></a>Edición de directivas de seguridad
Puede editar la directiva de seguridad predeterminada de cada una de las suscripciones de Azure en Security Center. Para modificar una directiva de seguridad, debe ser propietario, colaborador o administrador de seguridad de esa suscripción. Para configurar directivas de seguridad en Security Center, haga lo siguiente:

1. Inicie sesión en el Portal de Azure.

2. En el panel **Security Center**, en **General**, seleccione **Directiva de seguridad**.

3. Seleccione la suscripción para la que quiere habilitar una directiva de seguridad.

4. En la sección **Componentes de la directiva**, seleccione **Directiva de seguridad**.  
    Esta es la directiva predeterminada asignada por Security Center. Puede activar o desactivar las recomendaciones de seguridad disponibles.

5. Cuando termine de editar, seleccione **Guardar**.

## <a name="available-security-policy-definitions"></a>Definiciones de directivas de seguridad disponibles

Para comprender las definiciones de directiva que están disponibles en la directiva de seguridad predeterminada, consulte la tabla siguiente:

| Directiva | Funcionamiento de la directiva |
| --- | --- |
| Actualizaciones del sistema |Recupera una lista diaria de las actualizaciones críticas y de seguridad disponibles en Windows Update o Windows Server Update Services. La lista recuperada depende del servicio que está configurado para su máquina virtual y recomienda aplicar las actualizaciones que faltan. En los sistemas Linux, la directiva utiliza el sistema de administración de paquetes proporcionado por la distribución para determinar los paquetes que tienen actualizaciones disponibles. También comprueba las actualizaciones de seguridad y críticas de las máquinas virtuales de [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Configuraciones de seguridad |Analiza las configuraciones del sistema operativo diariamente para determinar los problemas que podrían hacer que la máquina virtual sea vulnerable a ataques. La directiva también recomienda cambios en la configuración para afrontar estas vulnerabilidades. Consulte la [lista de líneas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para más información sobre las configuraciones específicas que se están supervisando. (En este momento, Windows Server 2016 no es totalmente compatible). |
| Endpoint Protection |Recomienda configurar Endpoint Protection para todas las máquinas virtuales (VM) Windows para facilitar la identificación y eliminación de virus, spyware y otro software malintencionado. |
| Cifrado de discos |Se recomienda habilitar el cifrado de disco en todas las máquinas virtuales para mejorar la protección de datos en reposo. |
| Grupos de seguridad de red |Recomienda configurar los [grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md) para controlar el tráfico entrante y saliente de máquinas virtuales que tienen puntos de conexión públicos. Todas las interfaces de red de máquina virtual heredan los grupos de seguridad de red configurados para una subred, a menos que se especifique lo contrario. Además de comprobar que se ha configurado un grupo de seguridad de red, esta directiva evalúa las reglas de seguridad de entrada para identificar aquellas que permiten el tráfico entrante. |
| Firewall de aplicaciones web |Recomienda configurar un firewall de aplicaciones web en máquinas virtuales cuando se cumple una de las siguientes condiciones: <ul><li>Se usa una [dirección IP pública a nivel de instancia](../virtual-network/virtual-networks-instance-level-public-ip.md) y las reglas de seguridad de entrada del grupo de seguridad de red asociado se configuran para permitir el acceso al puerto 80/443.</li><li>Se usa una dirección IP de carga equilibrada y el equilibrio de carga asociado y las reglas de traducción de direcciones de red (NAT) entrantes están configuradas para permitir el acceso al puerto 80 o 443. Para más información, consulte [Compatibilidad de Azure Resource Manager con el equilibrador de carga](../load-balancer/load-balancer-arm.md).</li> |
| Firewall de próxima generación |Amplía las medidas de protección de la red más allá de los grupos de seguridad de red, que están integrados en Azure. Security Center detecta las implementaciones para las que se recomienda un firewall de próxima generación, y a continuación puede configurar una aplicación virtual. |
| Auditoría y detección de amenazas de SQL |Recomienda que se habilite la auditoría del acceso a SQL Database, con fines de cumplimiento, así como la detección avanzada de amenazas, con fines de investigación. |
| Cifrado de SQL |Recomienda que se habilite el cifrado en reposo para SQL Database, las copias de seguridad asociadas y los archivos de registro de transacciones. De esta forma, aunque haya infracción de datos, no se podrán leer. |
| Evaluación de vulnerabilidades |Se recomienda instalar una solución de evaluación de vulnerabilidades en la máquina virtual. |
| Cifrado de almacenamiento |Actualmente, esta característica está disponible para blobs y Azure Files. Después de habilitar el Cifrado del servicio Storage, solo se cifrarán los datos nuevos, mientras que los archivos existentes en esta cuenta de almacenamiento permanecerán sin cifrar. |
| Acceso de red JIT |Cuando el acceso a la red Just-In-Time está habilitado, Security Center bloquea el tráfico entrante a las máquinas virtuales de Azure mediante la creación de una regla de grupo de seguridad de red. Seleccione los puertos de la máquina virtual a los que se debe bloquear el tráfico entrante. Para más información, consulte [Administrar el acceso a máquina virtual mediante Just-In-Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |


## <a name="next-steps"></a>pasos siguientes
En este artículo, ha aprendido a configurar directivas de seguridad en Security Center. Para más información sobre Security Center, consulte los siguientes artículos:

* [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md): aprenda a planear y comprender las consideraciones de diseño sobre Azure Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): aprenda a administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas frecuentes acerca de Azure Security Center](security-center-faq.md): obtenga respuestas a las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.
