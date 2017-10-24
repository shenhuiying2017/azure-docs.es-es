---
title: "Guía de la directiva de compatibilidad y retirada del SO invitado de Azure | Microsoft Docs"
description: "Proporciona información acerca del soporte ofrecido por Microsoft respecto al SO invitado de Azure usado por los servicios en la nube."
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: 4bc2d57cf4c7d6e0981aa1a5c7c989860600b897
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Directiva de compatibilidad y retirada del SO invitado de Azure
La información de esta página se relaciona con el sistema operativo invitado de Azure ([SO invitado](cloud-services-guestos-update-matrix.md)) para los roles web y de trabajo de los servicios en la nube (PaaS). No se aplica a las máquinas virtuales (IaaS).

Microsoft ha publicado una directiva de [soporte técnico para el SO invitado](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). La página actual detalla cómo se implementa la directiva.

La directiva determina que:

1. Microsoft proporcionará soporte para **al menos las dos últimas familias del SO invitado**. Cuando se retire una familia, los clientes dispondrán de 12 meses a partir de la fecha de retirada oficial para actualizarse a una familia del SO invitado más reciente que sea compatible.
2. Microsoft proporcionará soporte técnico para **al menos las dos últimas versiones de las familias de SO invitado compatibles**.
3. Microsoft proporcionará soporte técnico para **al menos las dos últimas versiones del SDK de Azure**. Cuando se retire una versión del SDK, los clientes dispondrán de 12 meses desde la fecha de retirada oficial para actualizar a una nueva versión.

En ocasiones, es posible que se proporcione soporte técnico para más de dos familias o versiones. La información de soporte oficial para el SO invitado aparecerá en la [Matriz de compatibilidad del SDK y las versiones del SO invitado de Azure](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-family-or-version-is-retired"></a>Cuando se retira una familia o versión del SO invitado
Se introduce una nueva **familia** del SO invitado un tiempo después del lanzamiento de una nueva versión oficial del sistema operativo Windows Server. Siempre que se introduce una nueva familia del SO invitado, Microsoft retira la familia anterior de dicho sistema.

Se ofrecen nuevas **versiones** del SO invitado aproximadamente cada mes para incorporar las actualizaciones más recientes de MSRC. Debido a las actualizaciones mensuales periódicas, una versión del SO invitado se deshabilita normalmente 60 días después de su lanzamiento. Esta actividad mantiene al menos dos versiones del SO invitado para cada familia disponibles para su uso.

### <a name="process-during-a-guest-os-family-retirement"></a>Proceso durante la retirada de una familia del SO invitado
Una vez anunciada la retirada, los clientes tienen un período de "transición" de 12 meses antes de que la familia anterior se retire oficialmente del servicio. Este período de transición puede ampliarse a discreción de Microsoft. Las actualizaciones se publicarán en la [Matriz de compatibilidad del SDK y las versiones del SO invitado de Azure](cloud-services-guestos-update-matrix.md).

Seis (6) meses después del período de transición, comenzará un proceso gradual de retirada. Durante este tiempo:

1. Microsoft notificará la retirada a los clientes.
2. La versión más reciente del SDK de Azure no será compatible con la familia del SO invitado retirada.
3. No se permitirán nuevas implementaciones ni reimplementaciones de servicios en la nube en la familia retirada.

Microsoft seguirá ofreciendo nuevas versiones del SO invitado que incorporen las actualizaciones más recientes de MSRC hasta el último día del período de transición, conocido como "fecha de caducidad". En la fecha de expiración, los servicios en la nube que aún se ejecuten dejarán de tener soporte técnico según el SLA de Azure. Microsoft tiene el derecho a forzar la actualización, eliminar o detener dichos servicios después de esa fecha.

### <a name="process-during-a-guest-os-version-retirement"></a>Proceso durante la retirada de una versión del SO invitado
Si los clientes establecen el SO invitado de forma que se actualice automáticamente, no tendrán que preocuparse nunca por las distintas versiones de este, ya que siempre usarán la versión más reciente.

Se publican versiones del SO invitado cada mes. Debido a la frecuencia de publicaciones regulares, cada versión tiene una duración determinada.

A los 60 días, la versión queda "*deshabilitada*". "Deshabilitada" significa que la versión se quita del portal. La versión tampoco se puede establecer desde el archivo de configuración CSCFG. Las implementaciones existentes sigan ejecutándose. Sin embargo, no se permitirán nuevas implementaciones ni actualizaciones del código y la configuración de las implementaciones existentes.

Después de que se haya "deshabilitado", la versión del SO invitado "*expira*" y se fuerza la actualización de todas las instalaciones en las que aún se use dicha versión y se establece la actualización automática del SO invitado en el futuro. La expiración se produce por lotes, por lo que el período de tiempo desde la deshabilitación hasta la expiración puede variar.

Estos períodos se pueden prolongar a discreción de Microsoft para facilitar las transiciones de los clientes. Todos los cambios se comunicarán en la [Matriz de compatibilidad del SDK y las versiones del SO invitado de Azure](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Notificaciones durante la retirada
* **Retirada de la familia** <br>Microsoft usará las entradas de los blogs y las notificaciones del portal. A los clientes que sigan utilizando una familia del SO invitado que se haya retirado se les notificará directamente (correo electrónico, mensajes del portal, llamada telefónica) a través de los administradores de servicios asignados. Todos los cambios se publicarán en la [Matriz de compatibilidad del SDK y versiones del SO invitado de Azure](cloud-services-guestos-update-matrix.md).
* **Retirada de la versión** <br>Todos los cambios y las fechas en que ocurren se publican en la [Matriz de compatibilidad del SDK y versiones del SO invitado de Azure](cloud-services-guestos-update-matrix.md), como lanzamiento, deshabilitación y expiración. Los administradores de los servicios recibirán correos electrónicos si tienen implementaciones que se ejecutan en una versión o familia del SO invitado que se ha deshabilitado. El momento en que se envían estos correos electrónicos puede variar. Suelen ser al menos un mes antes de la deshabilitación, aunque este tiempo no es un contrato de nivel de servicio oficial.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes
**¿Cómo puedo mitigar los efectos de la migración?**

Se recomienda utilizar la familia del SO invitado más reciente para diseñar los servicios en la nube.

1. Inicie la planificación de la migración a una nueva familia cuanto antes.
2. Configure implementaciones de pruebas temporales para probar la ejecución del servicio en la nube en la nueva familia.
3. Establezca la versión del SO invitado en **Automática** (osVersion=* en el archivo [.cscfg](cloud-services-model-and-package.md#cscfg) ) para que la migración a las nuevas versiones del SO invitado se realice automáticamente.

**¿Qué ocurre si mi aplicación web requiere una integración más profunda con el sistema operativo?**

Si la arquitectura de la aplicación web depende de las características subyacentes del sistema operativo, utilice las funcionalidades admitidas por la plataforma, como las [tareas de inicio](cloud-services-startup-tasks.md) u otros mecanismos de extensibilidad. Como alternativa, también puede usar [máquinas virtuales de Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS, Infraestructura como servicio), donde usted es responsable de mantener el sistema operativo subyacente.

## <a name="next-steps"></a>Pasos siguientes
Revise las [versiones del SO invitado](cloud-services-guestos-update-matrix.md)más recientes.
