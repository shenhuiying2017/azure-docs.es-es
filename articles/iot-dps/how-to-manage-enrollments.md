---
title: "Administración de inscripciones de dispositivos con Azure Portal | Microsoft Docs"
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
ms.openlocfilehash: 06cc215e5c4087c7a38937de10eaa066037ac444
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Administración de inscripciones de dispositivos con Azure Portal

Una *inscripción de dispositivos* crea un registro de un único dispositivo o grupo de dispositivos que, en algún momento, se registrará en el servicio Azure IoT Hub Device Provisioning. El registro de inscripción contiene la configuración inicial preferida para los dispositivos como parte de la inscripción, incluida la instancia de IoT. En este artículo se muestra cómo administrar las inscripciones de dispositivos para el servicio de aprovisionamiento.


## <a name="create-a-device-enrollment"></a>Creación de una inscripción de dispositivos

Hay dos maneras de inscribir los dispositivos con el servicio de aprovisionamiento:

* Un **grupo de inscripción** es una entrada de un grupo de dispositivos que comparten un mecanismo de atestación común de certificados X.509, firmado por el mismo certificado de firma, que puede ser el [certificado raíz](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) o el [certificado intermedio](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), y que se usa para generar un certificado de dispositivo en un dispositivo físico. Se recomienda usar un grupo de inscripción para un gran número de dispositivos que compartan la configuración inicial deseada o para dispositivos que vayan todos al mismo inquilino. Tenga en cuenta que solo se pueden inscribir los dispositivos que usen el mecanismo de atestación de X.509 como *grupos de inscripción*. 

    Se puede crear un grupo de inscripción en el portal para un grupo de dispositivos mediante los siguientes pasos:

    1. Inicie sesión en Azure Portal y haga clic en **Todos los recursos** en el menú izquierdo.
    2. Haga clic en el servicio de aprovisionamiento de dispositivos donde desee inscribir su dispositivo en la lista de recursos.
    3. En el servicio de aprovisionamiento, haga clic en **Manage enrollments** (Administrar inscripciones) y seleccione la pestaña **Enrollment Groups** (Grupos de inscripción).
    4. Haga clic en el botón **Agregar** de la parte superior y escriba la información necesaria para la entrada en la lista de inscripción. Cargue el certificado raíz para el grupo de dispositivos. 
    5. Haga clic en **Save**(Guardar). Si el grupo de inscripción se creó correctamente, verá el nombre del grupo aparecer en la pestaña **Enrollment Groups** (Grupos de inscripción). 

        ![Grupo de inscripción en el portal](./media/how-to-manage-enrollments/group-enrollment.png)

    
* Una **inscripción individual** es una entrada para el registro de un único dispositivo. Las inscripciones individuales pueden usar certificados X.509 o tokens de SAS (en un módulo de plataforma segura real o virtual) como mecanismos de atestación. Se recomiendan las inscripciones individuales para los dispositivos que requieran configuraciones iniciales únicas o para los dispositivos que solo se puedan utilizar tokens de SAS en módulos de plataforma segura reales o virtuales como mecanismo de atestación. En las inscripciones individuales se puede especificar el identificador de dispositivo del centro de IoT deseado.

    Para crear una inscripción individual en el portal, siga estos pasos:

    1. Inicie sesión en Azure Portal y haga clic en **Todos los recursos** en el menú izquierdo.
    2. Haga clic en el servicio de aprovisionamiento de dispositivos donde desee inscribir su dispositivo en la lista de recursos.
    3. En el servicio de aprovisionamiento, haga clic en **Manage enrollments** (Administrar inscripciones) y seleccione la pestaña **Individual Enrollments** (Inscripciones individuales).
    4. Haga clic en el botón **Agregar** de la parte superior. 
    5. Seleccione el mecanismo de seguridad para el dispositivo y escriba la información necesaria para la entrada en la lista de inscripción. Cargue un certificado firmado si el dispositivo implementa X.509. 
    6. Haga clic en **Save**(Guardar). Si el grupo de inscripción se creó correctamente, verá el nombre del dispositivo aparecer en la pestaña **Individual Enrollments** (Inscripciones individuales). 

        ![Inscripción individual en el portal](./media/how-to-manage-enrollments/individual-enrollment.png)


## <a name="update-an-enrollment-entry"></a>Actualización de una entrada de inscripción
Para actualizar una entrada de inscripción existente, siga estos pasos:

1. Abra el servicio de aprovisionamiento de dispositivos en Azure Portal y haga clic en **Manage Enrollments** (Administrar inscripciones). 
2. Navegue a la entrada de inscripción que desee modificar. Haga clic en la entrada para abrir un resumen de la información sobre la inscripción del dispositivo. 
3. En esta página podrá modificar otros elementos distintos del tipo de seguridad y las credenciales, como la instancia de IoT Hub a la que vincular el dispositivo y el identificador de este. También podrá modificar el estado inicial del dispositivo gemelo. 
4. Al terminar, haga clic en **Guardar** para actualizar la inscripción del dispositivo. 

    ![Actualización de la inscripción en el portal](./media/how-to-manage-enrollments/update-enrollment.png)


## <a name="remove-a-device-enrollment"></a>Eliminación de una inscripción de dispositivo
Cuando no sea necesario aprovisionar dispositivos en ninguna instancia de IoT Hub, para eliminar la entrada de inscripción asociada en el portal siga estos pasos:

1. Abra el servicio de aprovisionamiento de dispositivos en Azure Portal y haga clic en **Manage Enrollments** (Administrar inscripciones). 
2. Navegue a la entrada de inscripción que desee eliminar y selecciónela. 
3. Haga clic en el botón **Eliminar** de la parte superior y seleccione **Sí** cuando se le pida confirmación. 
5. Una vez completada la acción, verá que se ha eliminado la entrada de la lista de inscripciones de dispositivos. 
 
    ![Eliminación de la inscripción en el portal](./media/how-to-manage-enrollments/remove-enrollment.png)



