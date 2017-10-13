---
title: "Administración de las inscripciones de dispositivos para Azure IoT Hub | Microsoft Docs"
description: "Administración de las inscripciones de dispositivos para el servicio DPS en Azure Portal"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c230e73f83d8acd0f142e037f70a80c9e0e4107e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-device-enrollments-in-the-iot-hub-device-provisioning-service"></a>Administración de las inscripciones de dispositivos en el servicio IoT Hub Device Provisioning

Una *inscripción de dispositivos* crea un registro de un único dispositivo o grupo de dispositivos que, en algún momento, se registrará en el servicio Azure IoT Hub Device Provisioning. El registro de inscripción contiene la configuración inicial preferida para los dispositivos como parte de la inscripción, incluida la instancia de IoT. En este artículo se muestra cómo administrar las inscripciones de dispositivos para el servicio de aprovisionamiento.


## <a name="create-a-device-enrollment"></a>Creación de una inscripción de dispositivos

Hay dos maneras de inscribir los dispositivos con el servicio de aprovisionamiento:

1. Un **grupo inscripción** es una entrada para un grupo de dispositivos que comparten un mecanismo de atestación común de los certificados X.509, firmado por la misma entidad de certificación raíz. Se recomienda usar un grupo de inscripción para un gran número de dispositivos que compartan la configuración inicial deseada o para dispositivos que vayan todos al mismo inquilino. Tenga en cuenta que solo se pueden inscribir los dispositivos que usen el mecanismo de atestación de X.509 como *grupos de inscripción*. 

    Puede crear un grupo de inscripción en el portal para un grupo de dispositivos mediante los siguientes pasos.

    1. Inicie sesión en Azure Portal y haga clic en **Todos los recursos**, menú izquierdo.
    2. Haga clic en el servicio de aprovisionamiento de dispositivos donde desee inscribir su dispositivo en la lista de recursos.
    3. En el servicio de aprovisionamiento, haga clic en **Manage enrollments** (Administrar inscripciones) y seleccione la pestaña **Enrollment Groups** (Grupos de inscripción).
    4. Haga clic en el botón **Agregar** de la parte superior y escriba la información necesaria para la entrada en la lista de inscripción. Cargue el certificado raíz para el grupo de dispositivos. 
    5. Haga clic en **Guardar**. Si el grupo de inscripción se creó correctamente, verá el nombre del grupo aparecer en la pestaña **Enrollment Groups** (Grupos de inscripción). 

        ![Grupo de inscripción en el portal](./media/how-to-manage-enrollments/group-enrollment.png)

    
2. Una **inscripción individual** es una entrada para el registro de un único dispositivo. Las inscripciones individuales pueden usar certificados X.509 o tokens de SAS (en un módulo de plataforma segura real o virtual) como mecanismos de atestación. Se recomiendan las inscripciones individuales para los dispositivos que requieran configuraciones iniciales únicas o para los dispositivos que solo se puedan utilizar tokens de SAS en módulos de plataforma segura reales o virtuales como mecanismo de atestación. En las inscripciones individuales se puede especificar el identificador de dispositivo del centro de IoT deseado.

    Puede crear una inscripción individual en el portal mediante los siguientes pasos. 

    1. Inicie sesión en Azure Portal y haga clic en **Todos los recursos**, menú izquierdo.
    2. Haga clic en el servicio de aprovisionamiento de dispositivos donde desee inscribir su dispositivo en la lista de recursos.
    3. En el servicio de aprovisionamiento, haga clic en **Manage enrollments** (Administrar inscripciones) y seleccione la pestaña **Individual Enrollments** (Inscripciones individuales).
    4. Haga clic en el botón **Agregar** de la parte superior. 
    5. Seleccione el mecanismo de seguridad para el dispositivo y escriba la información necesaria para la entrada en la lista de inscripción. Cargue un certificado firmado si el dispositivo implementa X.509. 
    6. Haga clic en **Guardar**. Si el grupo de inscripción se creó correctamente, verá el nombre del dispositivo aparecer en la pestaña **Individual Enrollments** (Inscripciones individuales). 

        ![Inscripción individual en el portal](./media/how-to-manage-enrollments/individual-enrollment.png)


## <a name="update-an-enrollment-entry"></a>Actualización de una entrada de inscripción
Puede actualizar una entrada de inscripción existente en el portal mediante los siguientes pasos.

1. Abra el servicio de aprovisionamiento de dispositivos en Azure Portal y haga clic en **Manage Enrollments** (Administrar inscripciones). 
2. Navegue a la entrada de inscripción que desee modificar. Haga clic en la entrada para abrir un resumen de la información sobre la inscripción del dispositivo. 
3. En esta página podrá modificar otros elementos distintos del tipo de seguridad y las credenciales, como la instancia de IoT Hub a la que vincular el dispositivo y el identificador de este. También podrá modificar el estado inicial del dispositivo gemelo. 
4. Al terminar, haga clic en **Guardar** para actualizar la inscripción del dispositivo. 

    ![Actualización de la inscripción en el portal](./media/how-to-manage-enrollments/update-enrollment.png)


## <a name="remove-a-device-enrollment"></a>Eliminación de una inscripción de dispositivo
Cuando no sea necesario aprovisionar dispositivos en alguna instancia de IoT Hub, puede eliminar la entrada de inscripción asociada en el portal mediante los siguientes pasos.

1. Abra el servicio de aprovisionamiento de dispositivos en Azure Portal y haga clic en **Manage Enrollments** (Administrar inscripciones). 
2. Navegue a la entrada de inscripción que desee eliminar y selecciónela. 
3. Haga clic en el botón **Eliminar** de la parte superior y seleccione **Sí** cuando se le pida confirmación. 
5. Una vez completada la acción, verá que se ha eliminado la entrada de la lista de inscripciones de dispositivos. 
 
    ![Eliminación de la inscripción en el portal](./media/how-to-manage-enrollments/remove-enrollment.png)



