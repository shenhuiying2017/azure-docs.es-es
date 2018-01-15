---
title: "Integración de las directivas de seguridad de Azure Security Center con Azure Policy | Microsoft Docs"
description: "Este documento le ayuda a configurar la integración de directivas de seguridad en Azure Security Center con Azure Policy."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2018
ms.author: yurid
ms.openlocfilehash: 5dedad4fa3695d1b210e1174d8f29966d2259889
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="integrate-security-center-security-policies-with-azure-policy"></a>Integración de las directivas de seguridad de Security Center con Azure Policy
Este artículo le ayuda a configurar las directivas de seguridad de Azure Security Center, que se basan en Azure Policy.

## <a name="how-security-policies-work"></a>¿Cómo funcionan las directivas de seguridad?
Security Center crea automáticamente una directiva de seguridad predeterminada para cada una de las suscripciones de Azure. Puede modificar las directivas de Security Center o usar [Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) para hacer lo siguiente:
* Crear nuevas definiciones de directiva.
* Asignar directivas entre grupos de administración, que pueden representar una organización entera o una unidad de negocio dentro de la organización.
* Supervisar el cumplimiento de las directivas.

> [!NOTE]
> Azure Policy se encuentra en versión preliminar limitada. Para unirse, vaya a [Sign up for Azure Policy](https://aka.ms/getpolicy) (Suscripción a Azure Policy). Para más información sobre Azure Policy, consulte [Creación y administración de directivas para aplicar el cumplimiento](http://docs.microsoft.com/azure/azure-policy/create-manage-policy).

## <a name="edit-security-policies"></a>Edición de directivas de seguridad
Puede editar la directiva de seguridad predeterminada de cada una de las suscripciones de Azure en Security Center. Para modificar una directiva de seguridad, debe ser propietario, colaborador o administrador de seguridad de la suscripción o del grupo de administración que la contiene. Para ver las directivas de seguridad en Security Center, haga lo siguiente:

1. Inicie sesión en el Portal de Azure.

2. En el panel **Security Center**, en **General**, seleccione **Directiva de seguridad**.

    ![El panel Administración de directivas](./media/security-center-azure-policy/security-center-policies-fig10.png)

3. Seleccione la suscripción para la que quiere habilitar una directiva de seguridad.  

4. En la sección **Componentes de la directiva**, seleccione **Directiva de seguridad**.  
    Se abre la ventana **Aspectos básicos**.

    ![Componentes de la directiva](./media/security-center-azure-policy/security-center-policies-fig12.png)

5. Para eliminar una definición de directiva, en **Policies and Parameters** (Directivas y parámetros), al lado de la definición que quiere eliminar, seleccione **Eliminar**.

6. Haga clic en **Save**(Guardar).  
    Se abre la ventana **Definiciones disponibles**, que muestra la directiva predeterminada que se asigna a Security Center mediante Azure Policy.

7. (Opcional) En la ventana **Definiciones disponibles**, realice una de las siguientes acciones:

    * Para agregar una definición de directiva, seleccione el signo más (+) situado junto a la definición.

    ![Definiciones de directiva disponibles](./media/security-center-azure-policy/security-center-policies-fig11.png)

    * Para obtener una explicación detallada de una directiva, selecciónela.  
    Se abre una ventana de **vista previa** de la definición. En ella se muestra una descripción de la definición y un vínculo al código JSON que proporciona la estructura de la [definición de directiva](../azure-policy/policy-definition.md).

    ![La ventana de vista previa de la definición](./media/security-center-azure-policy/security-center-policies-fig14.png)

7. Cuando termine de editar, seleccione **Guardar**.

## <a name="available-security-policy-definitions"></a>Definiciones de directivas de seguridad disponibles

Para comprender las definiciones de directiva que están disponibles en la directiva de seguridad predeterminada, consulte la tabla siguiente:

| Directiva | Lo que hace la directiva habilitada |
| --- | --- |
| Actualizaciones del sistema |Recupera una lista diaria de las actualizaciones críticas y de seguridad disponibles en Windows Update o Windows Server Update Services. La lista recuperada depende del servicio que está configurado para su máquina virtual y recomienda aplicar las actualizaciones que faltan. En los sistemas Linux, la directiva utiliza el sistema de administración de paquetes proporcionado por la distribución para determinar los paquetes que tienen actualizaciones disponibles. También comprueba las actualizaciones de seguridad y críticas de las máquinas virtuales de [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Configuraciones de seguridad |Analiza las configuraciones del sistema operativo diariamente para determinar los problemas que podrían hacer que la máquina virtual sea vulnerable a ataques. La directiva también recomienda cambios en la configuración para afrontar estas vulnerabilidades. Consulte la [lista de líneas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para más información sobre las configuraciones específicas que se están supervisando. (En este momento, Windows Server 2016 no es totalmente compatible). |
| Endpoint Protection |Recomienda configurar Endpoint Protection para todas las máquinas virtuales (VM) Windows para facilitar la identificación y eliminación de virus, spyware y otro software malintencionado. |
| Cifrado de discos |Se recomienda habilitar el cifrado de disco en todas las máquinas virtuales para mejorar la protección de datos en reposo. |
| Grupos de seguridad de red |Recomienda configurar los [grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md) para controlar el tráfico entrante y saliente de máquinas virtuales que tienen puntos de conexión públicos. Todas las interfaces de red de máquina virtual heredan los grupos de seguridad de red configurados para una subred, a menos que se especifique lo contrario. Además de comprobar que se ha configurado un grupo de seguridad de red, esta directiva evalúa las reglas de seguridad de entrada para identificar aquellas que permiten el tráfico entrante. |
| Firewall de aplicaciones web |Recomienda configurar un firewall de aplicaciones web en máquinas virtuales cuando se cumple una de las siguientes condiciones: <ul><li>Se usa una [dirección IP pública a nivel de instancia](../virtual-network/virtual-networks-instance-level-public-ip.md) y las reglas de seguridad de entrada del grupo de seguridad de red asociado se configuran para permitir el acceso al puerto 80/443.</li><li>Se usa una dirección IP de carga equilibrada y el equilibrio de carga asociado y las reglas de traducción de direcciones de red (NAT) entrantes están configuradas para permitir el acceso al puerto 80 o 443. Para más información, consulte [Compatibilidad de Azure Resource Manager con el equilibrador de carga](../load-balancer/load-balancer-arm.md).</li> |
| Firewall de próxima generación |Amplía las medidas de protección de la red más allá de los grupos de seguridad de red, que están integrados en Azure. Security Center detecta las implementaciones para las que se recomienda un firewall de próxima generación, y a continuación puede configurar una aplicación virtual. |
| Auditoría y detección de amenazas de SQL |Recomienda que se habilite la auditoría del acceso a Azure Database, con fines de cumplimiento, así como la detección avanzada de amenazas, con fines de investigación. |
| Cifrado de SQL |Recomienda que se habilite el cifrado en reposo para Azure SQL Database, las copias de seguridad asociadas y los archivos de registro de transacciones. De esta forma, aunque haya infracción de datos, no se podrán leer. |
| Evaluación de vulnerabilidades |Se recomienda instalar una solución de evaluación de vulnerabilidades en la máquina virtual. |
| Cifrado de almacenamiento |Actualmente, esta característica está disponible para Azure Blob Storage y Azure Files. Después de habilitar el Cifrado del servicio Storage, solo se cifrarán los datos nuevos, mientras que los archivos existentes en esta cuenta de almacenamiento permanecerán sin cifrar. |
| Acceso de red JIT |Cuando el acceso a la red Just-In-Time está habilitado, Security Center bloquea el tráfico entrante a las máquinas virtuales de Azure mediante la creación de una regla de grupo de seguridad de red. Seleccione los puertos de la máquina virtual a los que se debe bloquear el tráfico entrante. Para más información, consulte [Administrar el acceso a máquina virtual mediante Just-In-Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |


## <a name="next-steps"></a>pasos siguientes
En este artículo, ha aprendido a configurar directivas de seguridad en Security Center. Para más información sobre Security Center, consulte los siguientes artículos:

* [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md): aprenda a planear y comprender las consideraciones de diseño sobre Azure Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): aprenda a administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas frecuentes acerca de Azure Security Center](security-center-faq.md): obtenga respuestas a las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.
