---
title: "Administración de inscripciones de dispositivos con los SDK del servicio Azure Device Provisioning | Microsoft Docs"
description: "Administración de inscripciones de dispositivos en el servicio IoT Hub Device Provisioning con los SDK de servicio"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 12/01/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: arjmands
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a3d763009c7a7f45ddce96732977a79567f7ef44
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Cómo administrar inscripciones de dispositivos con los SDK del servicio Azure Device Provisioning
La *inscripción de un dispositivo* crea un registro de un único dispositivo o de grupo de ellos que, en algún momento, pueden registrarse en el servicio Device Provisioning. El registro de inscripción contiene la configuración inicial preferida para los dispositivos como parte de la inscripción, incluida la instancia de IoT. En este artículo se muestra cómo administrar inscripciones de dispositivos para el servicio de aprovisionamiento mediante programación con los SDK del servicio de aprovisionamiento de Azure IoT.  Los SDK están disponibles en GitHub en el mismo repositorio que los SDK de Azure IoT.

## <a name="samples"></a>Muestras
En este artículo se revisan los conceptos de alto nivel para administrar inscripciones de dispositivos para el servicio de aprovisionamiento mediante programación con los SDK del servicio de aprovisionamiento de Azure IoT.  Las llamadas API exactas pueden ser diferentes debido a diferencias del lenguaje.  Revise los ejemplos que se proporcionan en GitHub para más información:
* [Ejemplos de cliente del servicio de aprovisionamiento de Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
* [Ejemplos de cliente del servicio de aprovisionamiento de Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)

## <a name="prerequisites"></a>Requisitos previos
* Cadena de conexión de una instancia del servicio Device Provisioning
* Artefactos de seguridad de dispositivos:
    * [**TPM**](https://docs.microsoft.com/azure/iot-dps/concepts-security):
        * Inscripción individual: identificador de registro y clave de aprobación de TPM desde el dispositivo físico o el simulador de TPM.
        * El grupo de inscripción no se aplica a la atestación de TPM.
    * [**X.509**](https://docs.microsoft.com/azure/iot-dps/concepts-security):
        * Inscripción individual: el [certificado de hoja](https://docs.microsoft.com/azure/iot-dps/concepts-security#leaf-certificate) desde el dispositivo físico o el emulador de DICE.
        * Grupo de inscripción: el [certificado raíz](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) o el [certificado intermedio](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), que se usan para generar el certificado de dispositivo en un dispositivo físico.  También puede generarse desde el emulador de DICE.

## <a name="create-a-device-enrollment"></a>Creación de una inscripción de dispositivos

Hay dos maneras de inscribir los dispositivos con el servicio de aprovisionamiento:

* Un **grupo de inscripción** es una entrada para un grupo de dispositivos que comparten un mecanismo de atestación común de certificados X.509, firmados por el [certificado raíz](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) o el [certificado intermedio](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). Se recomienda usar un grupo de inscripción para un gran número de dispositivos que compartan la configuración inicial deseada o para dispositivos que vayan todos al mismo inquilino. Tenga en cuenta que solo se pueden inscribir los dispositivos que usen el mecanismo de atestación de X.509 como *grupos de inscripción*. 

    Puede crear un grupo de inscripción con los SDK mediante este flujo de trabajo:

    1. En el grupo de inscripción, el mecanismo de atestación emplea el certificado raíz X.509.  Llame a la API ```X509Attestation.createFromRootCertificate``` del SDK de servicio con el certificado raíz para crear la atestación de la inscripción.  El certificado raíz X.509 se proporciona en un archivo PEM o como una cadena.
    1. Cree una nueva variable ```EnrollmentGroup``` mediante el elemento ```attestation``` creado y un valor único de ```enrollmentGroupId```.  También tiene la opción de definir parámetros como ```Device ID```, ```IoTHubHostName``` o ```ProvisioningStatus```.
    2. Llame a la API ```createOrUpdateEnrollmentGroup``` del SDK de servicio de la aplicación back-end con ```EnrollmentGroup``` para crear un grupo de inscripción.

* Una **inscripción individual** es una entrada para el registro de un único dispositivo. Las inscripciones individuales pueden usar certificados X.509 o tokens de SAS (en un módulo de plataforma segura real o virtual) como mecanismos de atestación. Se recomiendan las inscripciones individuales para los dispositivos que requieran configuraciones iniciales únicas o para los dispositivos que solo se puedan utilizar tokens de SAS en módulos de plataforma segura reales o virtuales como mecanismo de atestación. En las inscripciones individuales se puede especificar el identificador de dispositivo del centro de IoT deseado.

    Puede crear una inscripción individual con los SDK mediante este flujo de trabajo:
    
    1. Elija su mecanismo de ```attestation```, que puede ser TPM o X.509.
        1. **TPM**: puede llamar a la API ```TpmAttestation``` del SDK de servicio usando como entrada la clave de aprobación desde un dispositivo físico o el emulador de TPM, para crear la atestación para la inscripción. 
        2. **X.509**: puede llamar a la API ```X509Attestation.createFromClientCertificate``` del SDK de servicio usando como entrada el certificado de cliente, para crear la atestación de la inscripción.
    2. Cree una nueva variable ```IndividualEnrollment``` con el mecanismo de ```attestation``` creado y una entrada de ```registrationId```, que se encuentra en el dispositivo o que se ha generado con el simulador de TPM.  También tiene la opción de definir parámetros como ```Device ID```, ```IoTHubHostName``` o ```ProvisioningStatus```.
    3. Llame a la API ```createOrUpdateIndividualEnrollment``` del SDK de servicio de la aplicación back-end con ```IndividualEnrollment``` para crear una inscripción individual.

Después de haber creado correctamente una inscripción, el servicio Device Provisioning devolvería el resultado de la inscripción.

Este flujo de trabajo se muestra en los [ejemplos](#samples).

## <a name="update-an-enrollment-entry"></a>Actualización de una entrada de inscripción

Después de haber creado una entrada de inscripción, puede que desee actualizar la inscripción.  Posibles escenarios incluyen actualizar la propiedad deseada, actualizar el método de atestación o revocar el acceso a los dispositivos.  Existen distintas API para inscripciones individuales y de grupo, pero no hay distinción para los mecanismos de atestación.

Puede actualizar una entrada de inscripción mediante este flujo de trabajo:
* **Inscripción individual**:
    1. Obtenga primero la inscripción más reciente del servicio de aprovisionamiento con la API ```getIndividualEnrollment``` del SDK de servicio.
    2. Modifique el parámetro de la inscripción más reciente, según sea necesario. 
    3. Mediante la inscripción más reciente, llame a la API ```createOrUpdateIndividualEnrollment``` del SDK de servicio para actualizar la entrada de inscripción.
* **Inscripción de grupo**:
    1. Obtenga primero la inscripción más reciente del servicio de aprovisionamiento con la API ```getEnrollmentGroup``` del SDK de servicio.
    2. Modifique el parámetro de la inscripción más reciente, según sea necesario.
    3. Mediante la inscripción más reciente, llame a la API ```createOrUpdateEnrollmentGroup``` del SDK de servicio para actualizar la entrada de inscripción.

Este flujo de trabajo se muestra en los [ejemplos](#samples).

## <a name="remove-an-enrollment-entry"></a>Eliminación de la entrada de una inscripción

* La **inscripción individual** se puede eliminar mediante la llamada a la API ```deleteIndividualEnrollment``` del SDK de servicio con ```registrationId```.
* La **inscripción de grupo** se puede eliminar mediante la llamada a la API ```deleteEnrollmentGroup``` del SDK de servicio con ```enrollmentGroupId```.

Este flujo de trabajo se muestra en los [ejemplos](#samples).

## <a name="bulk-operation-on-individual-enrollments"></a>Operación masiva sobre inscripciones individuales

Puede realizar operaciones masivas para crear, actualizar o eliminar varias inscripciones individuales mediante este flujo de trabajo:

1. Cree una variable que contenga varios elementos ```IndividualEnrollment```.  La implementación de esta variable es diferente en cada lenguaje.  Revise el ejemplo de operación masiva en GitHub para más información.
2. Llame a la API ```runBulkOperation``` del SDK de servicio con ```BulkOperationMode``` para realizar la operación deseada e implementar su variable para inscripciones individuales. Se admiten cuatro modos: crear, actualizar, updateIfMatchEtag y eliminar.

Después de haber realizado correctamente una operación, el servicio Device Provisioning devolvería el resultado de una operación masiva.

Este flujo de trabajo se muestra en los [ejemplos](#samples).
