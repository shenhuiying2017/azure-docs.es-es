---
title: "Preguntas más frecuentes sobre la migración desde el portal clásico a Azure Portal | Microsoft Docs"
description: "Proporciona respuestas a las preguntas más frecuentes sobre la migración de dispositivos de StorSimple desde el portal clásico a Azure Portal."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: ac57894e4f180f42f80479d2031f4dd5ddfdb1be
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="move-storsimple-device-manager-service-from-classic-to-azure-portal-frequently-asked-questions-faq"></a>Migración del servicio Administrador de dispositivos de StorSimple desde el portal clásico a Azure Portal: preguntas más frecuentes (P+F)

## <a name="overview"></a>Información general

A continuación, aparecen las preguntas y respuestas que pueden surgir cuando se migra el servicio Administrador de dispositivos de StorSimple que se ejecuta en el Portal de Azure clásico a Azure Portal.

Las preguntas y respuestas se organizan en las categorías siguientes:

* Migración del servicio Administrador de dispositivos StorSimple
* Migración de cuentas de almacenamiento
* Uso de cmdlets basados en Azure Resource Manager
* Uso del servicio StorSimple Data Manager
* Varios

## <a name="moving-storsimple-device-manager-service"></a>Migración del servicio Administrador de dispositivos StorSimple

### <a name="once-i-have-moved-to-azure-portal-can-i-still-create-a-storsimple-manager-service-in-the-classic-portal"></a>Una vez que migré a Azure Portal, ¿puedo seguir creando un servicio StorSimple Manager en el portal clásico?

Nº Una vez que migra el servicio StorSimple Manager a Azure Portal, no puede crear un servicio nuevo en el portal clásico. Además, [el portal clásico no estará disponible a partir del 8 de enero de 2018](https://azure.microsoft.com/updates/azure-portal-updates-for-classic-portal-users). 

### <a name="i-have-multiple-storsimple-managers-running-in-the-classic-portal-can-i-choose-which-ones-to-move-to-the-azure-portal"></a>Tengo varias instancias de StorSimple Manager en ejecución en el portal clásico. ¿Puedo elegir cuáles migrar a Azure Portal?

Nº No puede elegir las instancias de StorSimple Manager que desea migrar a Azure Portal. Se migran todas las instancias de StorSimple Manager de su suscripción.


### <a name="i-initiated-the-migration-of-my-service-to-the-new-azure-portal-should-i-delete-the-storsimple-manager-from-the-classic-portal"></a>Inicié la migración del servicio al nuevo Azure Portal. ¿Debo eliminar StorSimple del portal clásico? 

Nº No intente eliminar ningún servicio que haya migrado desde el portal clásico. Espere a que finalice la migración, una vez que todas las instancias de StorSimple Manager estén en el portal nuevo, ya no será necesario que elimine ningún servicio del portal clásico. Con el tiempo, el portal clásico quedará en desuso.

### <a name="can-i-rename-my-storsimple-device-manager-service-in-the-azure-portal"></a>¿Puedo cambiar el nombre del servicio Administrador de dispositivos de StorSimple en Azure Portal?

Nº El nombre del servicio no se puede cambiar una vez que el servicio se crea en Azure Portal. El mismo comportamiento ocurre con otras entidades, como dispositivos, volúmenes, contenedores de volúmenes y directivas de copia de seguridad.

### <a name="can-i-create-the-80108020-storsimple-cloud-appliances-with-azure-resource-manager-deployment-model"></a>¿Puedo crear instancias de StorSimple Cloud Appliance 8010/8020 con el modelo de implementación de Azure Resource Manager?

Sí. Puede crear instancias de StorSimple Cloud Appliance 8010/8020 en el modelo de implementación de Azure Resource Manager. Las máquinas virtuales subyacentes de estos dispositivos en la nube también están en el modelo de implementación de Resource Manager.

### <a name="when-we-migrate-subscriptions-to-the-azure-portal-will-the-underlying-vms-for-the-storsimple-cloud-appliances-also-migrate-to-azure-resource-management-deployment-model"></a>Cuando migremos las suscripciones a Azure Portal, ¿las máquinas virtuales subyacentes de las instancias de StorSimple Cloud Appliance también se migran al modelo de implementación de administración de recursos de Azure?

La migración del servicio StorSimple es independiente de la administración de las máquinas virtuales para las instancias de StorSimple Cloud Appliance. Puede administrar completamente las máquinas virtuales para las instancias de StorSimple Cloud Appliance tanto en el portal clásico como en Azure Portal incluso hoy.

### <a name="can-i-manage-existing-classic-80108020-storsimple-cloud-appliance-from-the-azure-portal"></a>¿Puedo administrar una instancia de StorSimple Cloud Appliance 8010/8020 clásica de Azure Portal?

Sí. Las máquinas virtuales asociadas con dispositivos en la nube 8010/8020 existentes se pueden administrar en Azure Portal.

Si creó instancias del modelo 8010/8020 de StorSimple Cloud Appliance que ejecutan Update 3.0 o versiones superiores, el servicio no se verá afectado con la migración al nuevo Azure Portal. Debería poder administrar completamente los dispositivos en la nube sin ningún problema. 

Si tiene dispositivos en la nube que ejecutan versiones anteriores a Update 3.0 en el portal clásico, solo tiene disponible una funcionalidad limitada. Para más información, vaya a la [lista de operaciones no admitidas en los dispositivos que ejecutan versiones anteriores a Update 3](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50).

No se puede actualizar un dispositivo en la nube. Use la versión más reciente del software para crear un dispositivo en la nube nuevo y después realizar una conmutación por error de los contenedores de volúmenes existentes en el dispositivo en la nube recién creado. Para más información, vaya a [Conmutación por error al dispositivo de nube](storsimple-8000-cloud-appliance-u2.md#fail-over-to-the-cloud-appliance)


### <a name="my-storsimple-8000-series-device-is-running-update-20-i-migrated-my-service-to-new-azure-portal-my-device-connected-successfully-but-it-seems-that-i-am-not-able-to-fully-manage-my-device-how-do-i-resolve-this-behavior"></a>Mi dispositivo de la serie StorSimple 8000 ejecuta Update 2.0. Migré el servicio a Azure Portal. El dispositivo se conectó correctamente, pero me parece que no puedo administrarlo completamente. ¿Cómo se resuelve este comportamiento?

El nuevo Azure Portal solo es compatible con dispositivos StorSimple que ejecutan Update 3.0 y versiones superiores. Si el dispositivo ejecutaba Update 2.0, solo tiene disponible una funcionalidad limitada en este dispositivo. Para más información, vaya a la [lista de operaciones no admitidas en los dispositivos que ejecutan versiones anteriores a Update 3](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50).

Para administrar completamente el dispositivo, instale la actualización más reciente. Para más información, vaya a la [instalación de Update 5](storsimple-8000-install-update-5.md).

### <a name="i-just-moved-my-storsimple-manager-service-to-the-azure-portal-i-am-seeing-some-alerts-related-to-my-device-is-this-behavior-related-to-the-move"></a>Acabo de migrar el servicio StorSimple Manager a Azure Portal. Veo algunas alertas relacionadas con el dispositivo. ¿Este comportamiento tiene que ver con la migración?

Nº La migración no debería generar errores ni alertas. Siga las recomendaciones de la alerta para resolverla.

### <a name="i-am-using-a-storsimple-50007000-series-device-are-these-also-supported-in-the-azure-portal"></a>Uso un dispositivo de la serie StorSimple 5000/7000. ¿Estos también son compatibles con Azure Portal?

Nº Los dispositivos de la serie StorSimple 5000/7000 no son compatibles con Azure Portal.

### <a name="i-am-planning-to-migrate-data-from-storsimple-50007000-series-device-to-a-storsimple-8000-series-device-how-does-moving-a-service-to-azure-portal-impact-my-data-migration"></a>Tengo la intención de migrar datos de un dispositivo de la serie StorSimple 5000/7000 a uno de la serie StorSimple 8000. ¿Cómo afecta a la migración de datos la migración de un servicio a Azure Portal? 

Puede migrar datos del dispositivo de la serie StorSimple 5000/7000 a uno de la serie StorSimple 8000 que se ejecute en Azure Portal. Para permitir la migración de datos de la serie 5000/7000 a la serie 8000, debe [crear una incidencia con Soporte técnico de Microsoft](storsimple-8000-contact-microsoft-support.md).


## <a name="moving-subscriptions-storage-accounts-resource-groups"></a>Migración de suscripciones, cuentas de almacenamiento y grupos de recursos

### <a name="can-i-move-storsimple-device-manager-from-one-subscription-to-another-subscription-in-the-azure-portal"></a>¿Puedo migrar Administrador de dispositivos de StorSimple de una suscripción a otra en Azure Portal?

Nº No se admite la migración del servicio Administrador de dispositivos de StorSimple de una suscripción a otra. Puede llevar a cabo un proceso manual que consta de los pasos siguientes:

* Migre datos desde el dispositivo StorSimple.
* Restablezca el dispositivo a los valores de fábrica. De este modo se eliminan todos los datos locales del dispositivo.
* Registre el dispositivo con la suscripción nueva a un servicio Administrador de dispositivos de StorSimple.
* Migre los datos de vuelta al dispositivo.

### <a name="do-i-have-to-migrate-the-storage-account-to-azure-resource-manager-deployment-model"></a>¿Debo migrar la cuenta de almacenamiento al modelo de implementación de Azure Resource Manager?

Nº Las cuentas de almacenamiento clásico se pueden administrar completamente desde Azure Portal. Cuando migra el dispositivo StorSimple a Azure Portal, la cuenta de almacenamiento sigue funcionando como antes.

### <a name="what-happens-to-the-storage-account-after-the-service-is-migrated-to-the-azure-portal"></a>¿Qué ocurre con la cuenta de almacenamiento después de que el servicio se migra a Azure Portal?

La migración del servicio no está relacionada con la administración de la cuenta de almacenamiento. Tanto las cuentas de almacenamiento clásico como las cuentas de almacenamiento de Azure Resource Manager se pueden administrar completamente en Azure Portal. Una vez que migra el servicio a Azure Portal, el dispositivo deberá seguir ejecutándose esta cuenta de almacenamiento y los datos no se verán afectados.

### <a name="can-i-migrate-storsimple-device-manager-from-one-resource-group-to-another"></a>¿Puedo migrar una instancia de Administrador de dispositivos de StorSimple de un grupo de recursos a otro?

Nº No puede migrar una instancia de Administrador de dispositivos de StorSimple creada con un grupo de recursos a otro grupo de recursos.

## <a name="using-azure-resource-manager-based-cmdlets"></a>Uso de cmdlets basados en Azure Resource Manager

### <a name="i-moved-to-the-new-azure-portal-now-my-scripts-based-on-azure-classic-deployment-model-powershell-cmdlets-are-not-working-what-do-i-need-to-do"></a>Realicé la migración al nuevo Azure Portal. Ahora los scripts basados en los cmdlets de PowerShell del modelo de implementación clásica de Azure no funcionan. ¿Qué tengo que hacer?

Los cmdlets de PowerShell del modelo de implementación clásica de Azure no son compatibles con Azure Portal. Actualice los scripts para administrar los dispositivos a través de Azure Resource Manager. Para más información sobre cómo actualizar los scripts, vaya a los [ejemplos del nuevo Azure Portal](https://github.com/anoobbacker/storsimpledevicemgmttools).

### <a name="i-just-moved-to-the-azure-portal-and-needed-to-roll-over-the-service-data-encryption-key-where-is-this-option-in-the-azure-portal"></a>Acabo de migrar a Azure Portal y fue necesario sustituir la clave de cifrado de datos del servicio. ¿Dónde encuentro esta opción en Azure Portal?

La opción para sustituir la clave de cifrado de datos del servicio no aparece en Azure Portal. Para más información sobre cómo hacer esta sustitución en Azure Portal, vaya a [Cambiar la clave de cifrado de datos del servicio](storsimple-8000-manage-service.md#change-the-service-data-encryption-key).

### <a name="i-am-using-windows-powershell-for-storsimple-cmdlets-on-the-storsimple-device-to-perform-operations-such-extract-a-support-package-are-these-cmdlets-affected-when-i-move-to-the-new-azure-portal"></a>Uso cmdlets de Windows PowerShell para StorSimple en el dispositivo de StorSimple para realizar operaciones como extraer un paquete de soporte. ¿La migración al nuevo Azure Portal afecta a estos cmdlets?

Nº Con la migración del servicio al nuevo Azure Portal, no debería haber ningún impacto en los cmdlets de Windows PowerShell para StorSimple asociados con el dispositivo de StorSimple local (que tampoco se ve afectado con la migración). Puede seguir usando estos cmdlets para crear un paquete de soporte sin ningún problema, incluso en Azure Portal. Esta migración solo afecta los cmdlets de PowerShell del modelo de implementación clásica de Azure.

## <a name="moving-storsimple-data-manager-service"></a>Migración del servicio StorSimple Data Manager

### <a name="i-am-using-storsimple-data-manager-service-in-classic-azure-portal-how-should-i-proceed-with-this-move"></a>Uso el servicio StorSimple Data Manager en Azure Portal clásico. ¿Cómo debo seguir con esta migración?

Si usa el servicio StorSimple Data Manager, se le ha migrado automáticamente a Azure Portal.

## <a name="miscellaneous"></a>Varios

### <a name="i-am-running-storsimple-snapshot-manager-for-my-8000-series-device-is-there-any-impact-on-storsimple-snapshot-manager-when-i-move-to-azure-portal"></a>Ejecuto StorSimple Snapshot Manager para un dispositivo de la serie 8000. ¿La migración a Azure Portal afecta a StorSimple Snapshot Manager?

Nº La migración de un servicio a Azure Portal no afecta a StorSimple Snapshot Manager. El dispositivo y StorSimple Snapshot Manager seguirán funcionando como antes.

### <a name="can-i-rename-my-storsimple-device-volume-containers-or-volumes"></a>¿Puedo cambiar el nombre del dispositivo StorSimple, los contenedores de volúmenes o los volúmenes?

Nº No se puede cambiar el nombre de los dispositivos, volúmenes, contenedores de volúmenes ni directivas de copia de seguridad en Azure Portal.

## <a name="next-steps"></a>pasos siguientes

Obtenga más información acerca de [Operaciones admitidas en dispositivos que ejecutan versiones anteriores a Update 5.0](storsimple-8000-manage-service.md#supported-operations-on-devices-running-versions-prior-to-update-50).



