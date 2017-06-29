---
title: Establecimiento de directivas de seguridad en Azure Security Center | Microsoft Docs
description: Este documento le ha ayudado a configurar directivas de seguridad en el Centro de seguridad de Azure.
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
ms.date: 06/16/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 2593e6846c897644017083b49ad4ba8219696c6c
ms.contentlocale: es-es
ms.lasthandoff: 06/17/2017


---
# <a name="set-security-policies-in-azure-security-center"></a>Establecimiento de directivas de seguridad en Azure Security Center
Este documento le ayuda a configurar las directivas de seguridad en Security Center guiándole por los pasos necesarios para realizar esta tarea.

>[!NOTE] 
>Desde primeros de junio de 2017, Security Center usará Microsoft Monitoring Agent para recopilar y almacenar datos. Consulte [Migración de la plataforma de Azure Security Center](security-center-platform-migration.md) para más información. La información de este artículo representa la funcionalidad de Security Center después de la transición a Microsoft Monitoring Agent.
>

## <a name="what-are-security-policies"></a>¿Qué son las directivas de seguridad?
Una directiva de seguridad define el conjunto de controles recomendados para los recursos en la suscripción específica. En Security Center, se definen directivas para las suscripciones de Azure de acuerdo con las necesidades de seguridad de la compañía y el tipo de aplicaciones o la confidencialidad de los datos de cada suscripción.

Por ejemplo, es posible que los recursos usados para el desarrollo o las pruebas tengan requisitos de seguridad diferentes a los de los recursos que se emplean para aplicaciones de producción. Del mismo modo, es posible que las aplicaciones que usan datos regulados, como la información de identificación personal, requieran un mayor nivel de seguridad. Las directivas de seguridad habilitadas en Azure Security Center controlan las recomendaciones de seguridad y la supervisión para ayudarle a identificar posibles vulnerabilidades y mitigar las amenazas. Consulte [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md) para más información sobre cómo determinar la opción más adecuada para usted.

## <a name="set-security-policies"></a>Configuración de directivas de seguridad
Puede configurar directivas de seguridad para cada suscripción. Para modificar una directiva de seguridad, debe ser propietario o colaborador de esa suscripción. Inicie sesión en Azure Portal y siga estos pasos para configurar directivas de seguridad en Security Center:

1. Haga clic en el icono **Directiva** en el panel de Security Center.
2. En la hoja Directiva de seguridad que se abre, seleccione la suscripción en la que quiere habilitar la directiva de seguridad.

    ![Definición de directiva](./media/security-center-policies/security-center-policies-fig1-ga.png)
3. Se abre la hoja **Directiva de seguridad** correspondiente a la suscripción seleccionada, y muestra un conjunto de opciones. Las opciones disponibles en esta hoja son:

   * **Directiva de prevención**: esta opción permite configurar directivas por suscripción o por grupo de recursos.  
   * **Notificación por correo electrónico**: utilice esta opción para configurar una notificación por correo electrónico que se envía en la primera aparición diaria de una alerta y en las alertas con un nivel de gravedad elevado. Las preferencias de correo electrónico solo pueden configurarse para las directivas de suscripción. Para más información sobre cómo configurar la notificación por correo electrónico, consulte [Proporcionar detalles de contacto de seguridad en Azure Security Center](security-center-provide-security-contact-details.md) .
   * **Plan de tarifa**: utilice esta opción para actualizar al plan de tarifa seleccionado. Para más información acerca de las opciones de precios, consulte los [planes de tarifa de Security Center](security-center-pricing.md).
4. Asegúrese de que la opción **Recopilar datos de máquinas virtuales** está **Activada**. Esta opción permite la recopilación automática de registros para los recursos nuevos y existentes mediante Microsoft Monitoring Agent, que es el mismo agente que usan la solución Operations Management Suite y el servicio Log Analytics. Los datos recopilados por este agente se almacenarán en las áreas de trabajo existentes de Log Analytics asociadas con la suscripción de Azure o en unas nuevas áreas de trabajo, según la región geográfica de la máquina virtual.

5. En la hoja **Directiva de seguridad**, haga clic en **Directiva de prevención** para ver las opciones disponibles. Haga clic en **Activar** para habilitar las recomendaciones de seguridad que son de interés para esta suscripción.

    ![Selección de las directivas de seguridad](./media/security-center-policies/security-center-policies-fig4-newUI.png)

Utilice la tabla siguiente como referencia para entender cada opción:

| Directiva | Cuando el estado es Activado |
| --- | --- |
| Actualizaciones del sistema |Recupera una lista diaria de las actualizaciones críticas y de seguridad disponibles en Windows Update o Windows Server Update Services. La lista recuperada depende del servicio que está configurado para esa máquina virtual y recomienda aplicar las actualizaciones que faltan. Para los sistemas Linux, la directiva utiliza el sistema de administración de paquetes proporcionado por la distribución para determinar los paquetes que tienen actualizaciones disponibles. También comprueba las actualizaciones de seguridad y críticas de las máquinas virtuales de [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure.md). |
| Vulnerabilidades del SO |Analiza las configuraciones del sistema operativo diariamente para determinar los problemas que podrían hacer que la máquina virtual sea vulnerable a ataques. La directiva también recomienda cambios en la configuración para afrontar estas vulnerabilidades. Consulte la [lista de líneas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para más información sobre las configuraciones específicas que se están supervisando. (En este momento, Windows Server 2016 no es totalmente compatible). |
| Endpoint Protection |Recomienda que se aprovisione el servicio Endpoint Protection para todas las máquinas virtuales de Windows para ayudar a identificar y a quitar virus, spyware y otro software malintencionado. |
| Cifrado de discos |Se recomienda habilitar el cifrado de disco en todas las máquinas virtuales para mejorar la protección de datos en reposo. |
| Grupos de seguridad de red |Recomienda configurar los [grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md) para controlar el tráfico entrante y saliente de máquinas virtuales que tienen puntos de conexión públicos. Todas las interfaces de red de máquina virtual heredarán los grupos de seguridad de red configurados para una subred, a menos que se especifique lo contrario. Además de comprobar que se ha configurado un grupo de seguridad de red, esta directiva evalúa las reglas de seguridad de entrada para identificar las reglas que permiten el tráfico entrante. |
| Firewall de aplicaciones web |Recomienda aprovisionar un firewall de aplicaciones web en máquinas virtuales cuando se cumple una de las siguientes acciones: </br></br>[Dirección IP pública a nivel de instancia](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP) se utiliza y las reglas de seguridad de entrada para el grupo de seguridad de red asociado se configuran para permitir el acceso al puerto 80/443.</br></br>Se utiliza la IP de carga equilibrada y el equilibrio de carga asociado y las reglas de traducción de direcciones de red (NAT) entrantes están configuradas para permitir el acceso al puerto 80 o 443. Para más información, consulte [Compatibilidad de Azure Resource Manager con Load Balancer](../load-balancer/load-balancer-arm.md). |
| Firewall de próxima generación |Amplía las medidas de protección de la red más allá de los grupos de seguridad de red, que están integrados en Azure. Security Center detectará las implementaciones para las que se recomienda un firewall de próxima generación y permitirá aprovisionar una aplicación virtual. |
| Auditoría de SQL y detección de amenazas |Se recomienda que se habilite la auditoría del acceso a Azure Database, con fines de cumplimiento, así como la detección avanzada de amenazas, con fines de investigación. |
| Cifrado de SQL |Recomienda que el cifrado en reposo se habilite para Azure SQL Database, las copias de seguridad asociadas y los archivos de registro de transacciones. De esta forma, aunque haya infracción de datos, no se podrán leer. |
| Evaluación de vulnerabilidades |Se recomienda instalar una solución de evaluación de vulnerabilidades en la máquina virtual. |
| Cifrado de almacenamiento |Actualmente, esta característica está disponible para los archivos y blobs de Azure. Tenga en cuenta que, después de habilitar Cifrado del servicio Storage, solo se cifrarán los datos nuevos, mientras que los archivos existentes en esta cuenta de almacenamiento permanecerán sin cifrar. |

Después de configurar todas las opciones, haga clic en **Aceptar** en la hoja **Directiva de seguridad** que tiene las recomendaciones y, después, en **Guardar** en la hoja **Directiva de seguridad** que tiene la configuración inicial.

> [!NOTE]
> El plan de tarifa sigue siendo aplicable al nivel del grupo de recursos. Para más información, visite la [página de precios](https://azure.microsoft.com/pricing/details/security-center/).
>
>

## <a name="see-also"></a>Consulte también
En este documento ha aprendido a configurar directivas de seguridad en el Centro de seguridad de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

* [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md). Aprenda a planificar y entender las consideraciones de diseño para adoptar Azure Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md). Aprenda a supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md). Aprenda a administrar y responder a las alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md). Aprenda cómo supervisar el estado de mantenimiento de las soluciones de sus asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md). Preguntas más frecuentes acerca del uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/). Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.

