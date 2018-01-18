---
title: "Administración del acceso de dispositivos para el servicio Azure IoT Hub Device Provisioning | Microsoft Docs"
description: "Cómo revocar el acceso de los dispositivos a su servicio de DPS en Azure Portal"
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 12/22/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 6f1dad648b228163219c8f722eed3897f4ba4d22
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>Cómo revocar el acceso de los dispositivos a su servicio de aprovisionamiento en Azure Portal

La administración adecuada de las credenciales del dispositivo es fundamental para los sistemas de alto perfil como las soluciones de IoT. Un procedimiento recomendado para estos sistemas es tener un plan claro de cómo revocar el acceso para los dispositivos en los casos en los que sus credenciales, tanto un token de SAS como un certificado X.509, puedan suponer un riesgo. En este artículo se describe cómo revocar el acceso de dispositivo en el paso de aprovisionamiento.

Para obtener información acerca de cómo revocar el acceso de los dispositivos a una instancia de IoT Hub después de aprovisionar el dispositivo. Consulte [Deshabilitar dispositivos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices).

> [!NOTE] 
> Tenga en cuenta la directiva de reintentos de los dispositivos para los que se revoca el acceso. Por ejemplo, un dispositivo con una directiva de reintento infinita, puede seguir intentando registrarse continuamente con el servicio de aprovisionamiento, consumiendo recursos del servicio y posiblemente afectando al rendimiento.

## <a name="blacklist-devices-with-an-individual-enrollment-entry"></a>Inclusión en una lista negra de los dispositivos con una entrada de inscripción individual

Las inscripciones individuales son aplicables a un solo dispositivo y pueden usar certificados X.509 o tokens de SAS (en un módulo de plataforma segura real o virtual) como mecanismos de atestación. (Los dispositivos que usan tokens de SAS como mecanismo de atestación solo se puede aprovisionar mediante una inscripción individual). Para poner en la lista negra a un dispositivo que tenga una inscripción individual, puede deshabilitar o eliminar su entrada de inscripción: 

- Para colocar al dispositivo temporalmente en la lista negra, deshabilite su entrada de inscripción. 

    1. Inicie sesión en Azure Portal y haga clic en **Todos los recursos** en el menú a la izquierda.
    2. En la lista de recursos haga clic en el servicio de aprovisionamiento para el que desee inscribir en la lista negra a su dispositivo.
    3. En el servicio de aprovisionamiento, haga clic en **Manage enrollments** (Administrar inscripciones) y seleccione la pestaña **Individual Enrollments** (Inscripciones individuales).
    4. Haga clic en la entrada de inscripción para el dispositivo que desee colocar en la lista negra para abrirlo. 
    5. Haga clic en **Deshabilitar** en la parte inferior de la entrada de la lista de inscripción, y a continuación haga clic en **Guardar**.  

        ![Deshabilitación de una entrada de inscripción individual en el portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
- Para colocar al dispositivo de forma permanente en la lista negra, puede eliminar su entrada de inscripción.

    1. Inicie sesión en Azure Portal y haga clic en **Todos los recursos** en el menú a la izquierda.
    2. En la lista de recursos haga clic en el servicio de aprovisionamiento para el que desee inscribir en la lista negra a su dispositivo.
    3. En el servicio de aprovisionamiento, haga clic en **Manage enrollments** (Administrar inscripciones) y seleccione la pestaña **Individual Enrollments** (Inscripciones individuales).
    4. Active la casilla situada junto a la entrada de inscripción para el dispositivo que desee colocar en la lista negra. 
    5. Haga clic en **Eliminar** en la parte superior de la ventana, a continuación haga clic en **Sí** para confirmar que desea eliminar la inscripción. 

        ![Eliminación de una entrada de inscripción individual en el portal](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
    6. Una vez completada la acción, verá que se ha eliminado la entrada de la lista de inscripciones de individuales.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-using-an-enrollment-group"></a>Inclusión en una lista negra de un certificado de entidad de certificación X.509 intermedia o raíz usando un grupo de inscripción

Los certificados X.509 normalmente están organizados en una cadena de certificados de confianza. Si un certificado en cualquier fase de una cadena está en peligro, se interrumpe la cadena de confianza y el certificado tiene que colocarse en una lista negra para evitar que el servicio Device Provisioning aprovisione los dispositivos en un nivel inferior en cualquier cadena que contenga ese certificado. Para más información sobre los certificados X.509 y cómo se utilizan con el servicio de aprovisionamiento, consulte [certificados X.509](./concepts-security.md#x509-certificates). 

Un grupo de inscripción es una entrada para dispositivos que comparten un mecanismo de atestación común de certificados X.509, firmados por la misma entidad de certificación intermedia o raíz. La entrada de grupo de inscripción se configura con el certificado X.509 asociado con la entidad de certificación raíz o intermedia, así como los valores de configuración, como estado gemelo y conexión de IoT Hub compartidos por los dispositivos con ese certificado en su cadena de certificado. Para incluir en una lista negra el certificado, puede deshabilitar o eliminar su grupo de inscripción:

- Para colocar al certificado temporalmente en la lista negra, deshabilite su grupo de inscripción. 

    1. Inicie sesión en Azure Portal y haga clic en **Todos los recursos** en el menú a la izquierda.
    2. En la lista de recursos haga clic en el servicio de aprovisionamiento para el que desee inscribir en la lista negra el certificado de firma.
    3. En el servicio de aprovisionamiento, haga clic en **Manage enrollments** (Administrar inscripciones) y seleccione la pestaña **Enrollment Groups** (Grupos de inscripción).
    4. Haga clic en el grupo de inscripción para el certificado que desee colocar en la lista negra para abrirlo.
    5. Haga clic en **Editar grupo** en la parte superior izquierda de la entrada del grupo de inscripción.
    6. Para deshabilitar la entrada de inscripción, seleccione **Deshabilitar** en el conmutador **Habilitar entrada**, y a continuación haga clic en **Guardar**.  

        ![Deshabilitación de entrada de grupo de inscripción en el portal](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
- Para incluir al certificado de forma permanente en la lista negra, puede eliminar su grupo de inscripción.

    1. Inicie sesión en Azure Portal y haga clic en **Todos los recursos** en el menú a la izquierda.
    2. En la lista de recursos haga clic en el servicio de aprovisionamiento para el que desee inscribir en la lista negra a su dispositivo.
    3. En el servicio de aprovisionamiento, haga clic en **Manage enrollments** (Administrar inscripciones) y seleccione la pestaña **Enrollment Groups** (Grupos de inscripción).
    4. Active la casilla situada junto al grupo de inscripción para el certificado que desee colocar en la lista negra. 
    5. Haga clic en **Eliminar** en la parte superior de la ventana, a continuación haga clic en **Sí** para confirmar que desea eliminar el grupo de inscripción. 

        ![Eliminación de la entrada de grupo de inscripción en el portal](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

    6. Una vez completada la acción, verá que se ha eliminado la entrada de la lista de grupos de inscripción.  

> [!NOTE]
> Si elimina un grupo de inscripción de un certificado, los dispositivos que tienen ese certificado en su cadena de certificados pueden seguir realizando inscripciones si existe un grupo de inscripción habilitado para el certificado raíz u otro certificado intermedio más arriba en la cadena de certificados.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Inclusión en una lista negra de dispositivos específicos en un grupo de inscripción

Los dispositivos que implementen el mecanismo de atestación de X.509 usan la cadena de certificados del dispositivo y la clave privada para autenticar. Cuando un dispositivo se conecta y se autentica con el servicio Device Provisioning, el servicio busca primero una inscripción individual que coincida con las credenciales del dispositivo antes de buscar en los grupos de inscripción para determinar si se puede aprovisionar el dispositivo. Si el servicio detecta una inscripción individual deshabilitada para el dispositivo, evita que el dispositivo se conecte, incluso si existe un grupo de inscripción habilitado para una entidad de certificación raíz o intermedia en la cadena de certificados del dispositivo. Para incluir en una lista negra a un dispositivo individual en un grupo de inscripción, siga estos pasos:

1. Inicie sesión en Azure Portal y haga clic en **Todos los recursos**, menú izquierdo.
2. En la lista de recursos, haga clic en el servicio de aprovisionamiento que contiene el grupo de inscripción para el dispositivo que desee colocar en la lista negra.
3. En el servicio de aprovisionamiento, haga clic en **Manage enrollments** (Administrar inscripciones) y seleccione la pestaña **Individual Enrollments** (Inscripciones individuales).
4. Haga clic en el botón **Agregar** de la parte superior. 
5. Seleccione **X.509** como el mecanismo de seguridad para el dispositivo y cargue el certificado del dispositivo. Este es el certificado de entidad final firmado instalado en el dispositivo, que utiliza para generar certificados para la autenticación.
6. Escriba el **identificador de dispositivo de IoT Hub** para el dispositivo. 
7. Para deshabilitar la entrada de inscripción, seleccione **Deshabilitar** en el conmutador **Habilitar entrada**. 
8. Haga clic en **Save**(Guardar). Si la inscripción se creó correctamente, verá el nombre del dispositivo aparecer en la pestaña **Inscripciones individuales**. 

    ![Deshabilitación de una entrada de inscripción individual en el portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)




