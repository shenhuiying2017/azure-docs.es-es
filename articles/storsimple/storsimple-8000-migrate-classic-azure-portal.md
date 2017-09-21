---
title: "Migración de cuentas de almacenamiento y suscripciones para el servicio Administrador de dispositivos de StorSimple | Microsoft Docs"
description: "Obtenga información sobre cómo migrar las suscripciones y las cuentas de almacenamiento para el servicio Administrador de dispositivos de StorSimple 8000."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: a64cf0ba2a4646a03c864193fb93de4f54f38039
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migración de suscripciones y cuentas de almacenamiento asociadas con el servicio Administrador de dispositivos de StorSimple

Puede que tenga que mover el servicio StorSimple a una inscripción o a una suscripción nueva. Estos escenarios de migración son cambios de cuenta o cambios del centro de datos. Use la tabla siguiente para saber cuáles de estos escenarios se admiten, incluidos los pasos detallados para el movimiento.

## <a name="account-changes"></a>Cambios de cuenta

| Puede mover…| Compatible| Tiempo de inactividad| Proceso de soporte técnico de Azure| Enfoque|
|-----|-----|-----|-----|-----|
| Toda una suscripción (incluidas cuentas de almacenamiento y el servicio StorSimple) a otra inscripción. | Sí       | No       | **Transferencia de inscripción**<br>Se usa:<li>Cuando adquiere un compromiso de Azure en virtud de un contrato nuevo.</li><li>Cuando quiere migrar todas las cuentas y suscripciones de la inscripción anterior a la nueva. Esto incluye todos los servicios de Azure de la suscripción anterior.</li> | **Paso 1: Abrir una incidencia de soporte técnico de operaciones de Azure Enterprise.**<li>Vaya a [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt).</li><li> Seleccione **Enrollment Administraiton** (Administración de inscripción) y luego seleccione **Transfer from one enrollment to a new enrollment** (Transferir de una inscripción a una inscripción nueva).<br>**Paso 2: Proporcionar la información solicitada**<br>Incluya:<li>el número de inscripción de origen</li><li> el número de inscripción de destino</li><li>la fecha de vigencia de la transferencia|
| El servicio StorSimple de una cuenta existente a una inscripción nueva.    | Sí       | No       | **Transferencia de cuenta**<br>Uso:<li>Cuando no se desea hacer una transferencia de inscripción completa.</li><li>Cuando solo desea mover cuentas específicas a una inscripción nueva.</li>| **Paso 1: Abrir una incidencia de soporte técnico de operaciones de Azure Enterprise.**<li>Vaya a [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt).</li><li>Seleccione **Enrollment Administration** (Administración de inscripción) y luego seleccione **Transfer an EA Account to a new enrollment** (Transferir una cuenta de EA a una inscripción nueva).<br>**Paso 2: Proporcionar la información solicitada**<br>Incluya:<li>el número de inscripción de origen</li><li> el número de inscripción de destino</li><li>la fecha de vigencia de la transferencia|
| El servicio StorSimple de una suscripción a otra.      | No        |    Sí         | Ninguno, se trata de un proceso manual|<li>Migre datos desde el dispositivo StorSimple.</li><li>Restablezca el dispositivo a los valores de fábrica. De este modo se eliminan todos los datos locales del dispositivo.</li><li>Registre el dispositivo con la suscripción nueva a un servicio Administrador de dispositivos de StorSimple.</li><li>Migre los datos de vuelta al dispositivo.|
| Un dispositivo StorSimple de un servicio Administrador de dispositivos StorSimple a otro servicio en una región distinta.      | No        | Sí            | Ninguno, se trata de un proceso manual |Igual que el anterior.|

## <a name="datacenter-changes"></a>Cambios en el centro de datos

| Puede mover…| Compatible|Tiempo de inactividad| Proceso de soporte técnico de Azure| Enfoque|
|-----|-----|-----|-----|-----|-----|
| Un servicio StorSimple de un centro de datos de Azure a otro. | No | Sí |Ninguno, se trata de un proceso manual  |<li>Migre datos desde el dispositivo StorSimple.</li><li>Restablezca el dispositivo a los valores de fábrica. De este modo se eliminan todos los datos locales del dispositivo.</li><li>Registre el dispositivo con la suscripción nueva a un servicio Administrador de dispositivos de StorSimple nuevo.</li><li>Migre los datos de vuelta al dispositivo.|
| Una cuenta de StorSimple de un centro de datos de Azure a otro. | No |Sí  |Ninguno, se trata de un proceso manual  | Igual que el anterior.|

## <a name="next-steps"></a>Pasos siguientes

* [Implementación del servicio Administrador de dispositivos StorSimple](storsimple-8000-manage-service.md)
* [Implementación del dispositivo StorSimple 8000 en Azure Portal](storsimple-8000-deployment-walkthrough-u2.md)
