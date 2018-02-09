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
ms.openlocfilehash: 12aebf3a56aa7469a765ab6fc67aa65b254db71a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>Revocación del acceso de los dispositivos a su servicio de aprovisionamiento en Azure Portal

La administración adecuada de las credenciales del dispositivo es fundamental para los sistemas de alto perfil como las soluciones de IoT. Un procedimiento recomendado para estos sistemas es tener un plan claro de cómo revocar el acceso de los dispositivos en los casos en que sus credenciales, tanto un token de firmas de acceso compartido (SAS) como un certificado X.509, puedan suponer un riesgo. En este artículo se describe cómo revocar el acceso de dispositivo en el paso de aprovisionamiento.

Para obtener información sobre cómo revocar el acceso de los dispositivos a una instancia de IoT Hub después de aprovisionar el dispositivo, vea [Deshabilitar dispositivos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices).

> [!NOTE] 
> Tenga en cuenta la directiva de reintentos de los dispositivos para los que se revoca el acceso. Por ejemplo, un dispositivo que tiene una directiva de reintentos infinitos puede reintentar continuamente registrarse en el servicio de aprovisionamiento. Esta situación consume recursos del servicio y posiblemente afecta al rendimiento.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Inclusión en una lista negra de los dispositivos con una entrada de inscripción individual

Las inscripciones individuales son aplicables a un solo dispositivo y pueden usar certificados X.509 o tokens de SAS (en un módulo de plataforma segura real o virtual) como mecanismos de atestación. (Los dispositivos que usan tokens de SAS como mecanismo de atestación solo se puede aprovisionar mediante una inscripción individual). Para poner en la lista negra a un dispositivo que tenga una inscripción individual, puede deshabilitar o eliminar su entrada de inscripción. 

Para colocar el dispositivo temporalmente en la lista negra, deshabilite su entrada de inscripción: 

1. Inicie sesión en Azure Portal y seleccione **Todos los recursos** en el menú a la izquierda.
2. En la lista de recursos, seleccione el servicio de aprovisionamiento para el que desee incluir en la lista negra a su dispositivo.
3. En el servicio de aprovisionamiento, seleccione **Administrar inscripciones** y después, la pestaña **Inscripciones individuales**.
4. Seleccione la entrada de inscripción del dispositivo que desee colocar en la lista negra. 
5. Seleccione **Deshabilitar** en el conmutador **Habilitar entrada** y después seleccione **Guardar**.  

   ![Deshabilitación de una entrada de inscripción individual en el portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
Para incluir el dispositivo de forma permanente en la lista negra, elimine su entrada de inscripción:

1. Inicie sesión en Azure Portal y seleccione **Todos los recursos** en el menú a la izquierda.
2. En la lista de recursos, seleccione el servicio de aprovisionamiento para el que desee incluir en la lista negra a su dispositivo.
3. En el servicio de aprovisionamiento, seleccione **Administrar inscripciones** y después, la pestaña **Inscripciones individuales**.
4. Seleccione la casilla que se encuentra junto a la entrada de inscripción del dispositivo que desee colocar en la lista negra. 
5. Seleccione **Eliminar** en la parte superior de la ventana y después seleccione **Sí** para confirmar que desea eliminar la inscripción. 

   ![Eliminación de una entrada de inscripción individual en el portal](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
Una vez completado el procedimiento, debe ver que la entrada se ha eliminado de la lista de inscripciones de individuales.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Inclusión en una lista negra de un certificado de entidad de certificación X.509 intermedia o raíz usando un grupo de inscripción

Los certificados X.509 normalmente están organizados en una cadena de certificados de confianza. Si un certificado en cualquier fase de una cadena está en peligro, se rompe la confianza. El certificado debe estar en la lista negra para evitar que el servicio Device Provisioning aprovisione dispositivos en un nivel inferior de cualquier cadena que contiene ese certificado. Para más información sobre los certificados X.509 y cómo se utilizan con el servicio de aprovisionamiento, consulte [certificados X.509](./concepts-security.md#x509-certificates). 

Un grupo de inscripción es una entrada para dispositivos que comparten un mecanismo de atestación común de certificados X.509, firmados por la misma entidad de certificación intermedia o raíz. La entrada del grupo de inscripción se configura con el certificado X.509 asociado con la entidad de certificación intermedia o raíz. La entrada también se configura con cualquiera de los valores de configuración, como estado gemelo y conexión de IoT Hub compartidos por los dispositivos con ese certificado en su cadena de certificados. Para incluir en una lista negra el certificado, puede deshabilitar o eliminar su grupo de inscripción.

Para colocar al certificado temporalmente en la lista negra, deshabilite su grupo de inscripción: 

1. Inicie sesión en Azure Portal y seleccione **Todos los recursos** en el menú a la izquierda.
2. En la lista de recursos, seleccione el servicio de aprovisionamiento para el que desee inscribir en la lista negra el certificado de firma.
3. En el servicio de aprovisionamiento, seleccione **Administrar inscripciones** y después, la pestaña **Grupos de inscripción**.
4. Seleccione el grupo de inscripción del certificado que desee colocar en la lista negra.
5. En la entrada del grupo de inscripción, seleccione **Editar grupo**.
6. Seleccione **Deshabilitar** en el conmutador **Habilitar entrada** y después seleccione **Guardar**.  

   ![Deshabilitación de entrada de grupo de inscripción en el portal](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Para incluir al certificado de forma permanente en la lista negra, elimine su grupo de inscripción:

1. Inicie sesión en Azure Portal y seleccione **Todos los recursos** en el menú a la izquierda.
2. En la lista de recursos, seleccione el servicio de aprovisionamiento para el que desee incluir en la lista negra a su dispositivo.
3. En el servicio de aprovisionamiento, seleccione **Administrar inscripciones** y después, la pestaña **Grupos de inscripción**.
4. Seleccione la casilla que se encuentra junto al grupo de inscripción del certificado que desee colocar en la lista negra. 
5. Seleccione **Eliminar** en la parte superior de la ventana y después seleccione **Sí** para confirmar que desea eliminar el grupo de inscripción. 

   ![Eliminación de la entrada de grupo de inscripción en el portal](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Una vez completado el procedimiento, debe ver que la entrada se ha eliminado de la lista de grupos de inscripción.  

> [!NOTE]
> Si elimina un grupo de inscripción de un certificado, los dispositivos que tienen el certificado en su cadena de certificados pueden seguir realizando inscripciones si existe un grupo de inscripción habilitado para el certificado raíz u otro certificado intermedio más arriba en la cadena de certificados.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Inclusión en una lista negra de dispositivos específicos en un grupo de inscripción

Los dispositivos que implementen el mecanismo de atestación de X.509 usan la cadena de certificados del dispositivo y la clave privada para autenticar. Cuando un dispositivo se conecta y se autentica con el servicio Device Provisioning, el servicio busca primero una inscripción individual que coincida con las credenciales del dispositivo. El servicio busca después los grupos de inscripción para determinar si se puede aprovisionar el dispositivo. Si el servicio detecta una inscripción individual deshabilitada para el dispositivo, impide que el dispositivo se conecte. El servicio impide la conexión incluso si existe un grupo de inscripción habilitado para una entidad de certificación intermedia o raíz en la cadena de certificados del dispositivo. 

Para incluir en una lista negra a un dispositivo individual en un grupo de inscripción, siga estos pasos:

1. Inicie sesión en Azure Portal y seleccione **Todos los recursos** en el menú a la izquierda.
2. En la lista de recursos, seleccione el servicio de aprovisionamiento que contiene el grupo de inscripción para el dispositivo que desee colocar en la lista negra.
3. En el servicio de aprovisionamiento, seleccione **Administrar inscripciones** y después, la pestaña **Inscripciones individuales**.
4. Seleccione el botón **Agregar** de la parte superior. 
5. Seleccione **X.509** como el mecanismo de seguridad para el dispositivo y cargue el certificado del dispositivo. Este es el certificado de entidad final firmado instalado en el dispositivo. El dispositivo lo usa para generar certificados para la autenticación.
6. En **Id. de dispositivo de IoT Hub**, escriba el identificador del dispositivo. 
7. Seleccione **Deshabilitar** en el conmutador **Habilitar entrada** y después seleccione **Guardar**. 

   ![Deshabilitación de una entrada de inscripción individual en el portal](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Si la inscripción se crea correctamente, verá que el dispositivo aparece en la pestaña **Inscripciones individuales**.


