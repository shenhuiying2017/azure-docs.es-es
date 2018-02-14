---
title: "Cómo deshacer el aprovisionamiento de dispositivos inscritos en el servicio Azure IoT Hub Device Provisioning | Microsoft Docs"
description: "Cómo deshacer el aprovisionamiento de dispositivos inscritos por el servicio DPS en Azure Portal"
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 01/08/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 1d057a4df43cf25e6817672d198207d9a50e462e
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-unprovision-devices-enrolled-by-your-provisioning-service"></a>Cómo deshacer el aprovisionamiento de dispositivos inscritos por el servicio de aprovisionamiento

Puede que sea necesario deshacer el aprovisionamiento de dispositivos que se han aprovisionado a través del servicio Device Provisioning. Por ejemplo, un dispositivo puede venderse, trasladarse a otra instancia de IoT Hub, perderse, robarse o verse en peligro de otro modo. 

En general, deshacer el aprovisionamiento de un dispositivo implica dos pasos:

1. Revocar el acceso del dispositivo al servicio de aprovisionamiento. En función de si desea revocar el acceso de forma temporal o permanente, o en el caso del mecanismo de atestación X.509, en la jerarquía de los grupos de inscripción existentes, puede que sea aconsejable deshabilitar o eliminar una entrada de inscripción. 
 
   - Para obtener información sobre cómo revocar el acceso de dispositivos a través del portal, vea [Revocación de acceso a dispositivos](how-to-revoke-device-access-portal.md).
   - Para obtener información sobre cómo revocar el acceso de dispositivos a través de programación con uno de los SDK de servicio de aprovisionamiento, vea [Administración de inscripciones de dispositivos con los SDK del servicio](how-to-manage-enrollments-sdks.md).

2. Deshabilite o elimine la entrada del dispositivo en el registro de identidades para la instancia de IoT Hub en que se aprovisionó. Para obtener más información, vea [Administración de identidades de dispositivo](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) en la documentación de Azure IoT Hub. 

Los pasos exactos que se realizan para deshacer el aprovisionamiento de un dispositivo dependen de su mecanismo de atestación y de su entrada de inscripción aplicable con el servicio de aprovisionamiento.

## <a name="individual-enrollments"></a>Inscripciones individuales
Los dispositivos que usan la atestación de TPM o la atestación de X.509 con un certificado de hoja se aprovisionan mediante una entrada de inscripción individual. 

Para deshacer el aprovisionamiento de un dispositivo que tiene una inscripción individual, siga estos pasos: 
1. En el caso de dispositivos que usan la atestación de TPM, elimine la entrada de inscripción individual para revocar permanentemente el acceso del dispositivo al servicio de aprovisionamiento o deshabilite la entrada para revocar temporalmente su acceso. En dispositivos que usan la atestación de X.509, puede eliminar o deshabilitar la entrada. Tenga en cuenta, sin embargo, que si elimina una inscripción individual de un dispositivo que usa la atestación de X.509 y existe un grupo de inscripción habilitado para un certificado de firma en la cadena de certificados de ese dispositivo, se puede volver a inscribir el dispositivo. En estos dispositivos, puede ser más seguro deshabilitar la entrada de inscripción. De esta forma, evita que el dispositivo se vuelva a inscribir, con independencia de si existe un grupo de inscripción habilitado para uno de sus certificados de firma.
2. Deshabilite o elimine el dispositivo del registro de identidades de la instancia de IoT Hub en que se aprovisionó. 


## <a name="enrollment-groups"></a>Grupos de inscripción
Con la atestación X.509, también se pueden aprovisionar los dispositivos a través de un grupo de inscripción. Los grupos de inscripción se configuran con un certificado de firma, ya sea un certificado de entidad de certificación raíz o intermedia, y controlan el acceso al servicio de aprovisionamiento para los dispositivos con ese certificado en su cadena de certificados. Para más información sobre los grupos de inscripción y los certificados X.509 en el servicio de aprovisionamiento, consulte [Certificados X.509](concepts-security.md#x509-certificates). 

Para ver una lista de dispositivos que se han aprovisionado a través de un grupo de inscripción, puede visualizar los detalles del grupo de inscripción. Se trata de una manera fácil de comprender a qué instancia de IoT Hub se ha aprovisionado cada dispositivo. Para ver la lista de dispositivos: 

1. Inicie sesión en Azure Portal y haga clic en **Todos los recursos** en el menú de la izquierda.
2. Haga clic en el servicio de aprovisionamiento en la lista de recursos.
3. En el servicio de aprovisionamiento, haga clic en **Manage enrollments** (Administrar inscripciones) y seleccione la pestaña **Enrollment Groups** (Grupos de inscripción).
4. Haga clic en el grupo de inscripción para abrirlo.

   ![Ver la entrada del grupo de inscripción en el portal](./media/how-to-unprovision-devices/view-enrollment-group.png)

Hay dos escenarios que se deben tener en cuenta en relación con los grupos de inscripción:

- Para deshacer el aprovisionamiento de todos los dispositivos que se han aprovisionado a través de un grupo de inscripción, siga estos pasos:
  1. Deshabilite el grupo de inscripción para incluir su certificado de firma en la lista de no permitidos. 
  2. Use la lista de dispositivos aprovisionados de ese grupo de inscripción para deshabilitar o eliminar todos los dispositivos del registro de identidades de su respectiva instancia de IoT Hub. 
  3. Después de deshabilitar o eliminar todos los dispositivos de sus respectivas instancias de IoT Hub, puede eliminar opcionalmente el grupo de inscripción. Sin embargo, tenga en cuenta que, si elimina el grupo de inscripción y hay un grupo de inscripción habilitado para un certificado de firma más arriba en la cadena de certificados de uno o varios de los dispositivos, estos dispositivos se podrán volver a inscribir. 
- Para deshacer el aprovisionamiento de un único dispositivo de un grupo de inscripción, siga estos pasos:
  1. Cree una inscripción individual deshabilitada para su certificado de hoja (dispositivo). Esto revoca el acceso al servicio de aprovisionamiento para dicho dispositivo mientras se sigue permitiendo el acceso para otros dispositivos que tienen el certificado de firma del grupo de inscripción en su cadena. No elimine la inscripción individual deshabilitada para el dispositivo. Si lo hace, permitirá al dispositivo volver a inscribirse a través del grupo de inscripción. 
  2. Use la lista de dispositivos aprovisionados para ese grupo de inscripción para buscar la instancia de IoT Hub en que se ha aprovisionado el dispositivo y deshabilitarlo o eliminarlo del registro de identidades de dicha instancia. 
  
  










