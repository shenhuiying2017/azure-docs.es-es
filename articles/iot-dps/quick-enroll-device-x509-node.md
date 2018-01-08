---
title: "Inscripción de un dispositivo X.509 al servicio Azure Device Provisioning con Node.js | Microsoft Docs"
description: "Inicio rápido de Azure: Inscripción de un dispositivo X.509 al servicio Azure IoT Hub Device Provisioning mediante el SDK del servicio de Node.js"
services: iot-dps
keywords: 
author: JimacoMS2
ms.author: v-jamebr
ms.date: 12/21/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 549c67d0e0033a98c7ddf96778ad5bd2c75742b8
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="enroll-x509-devices-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Inscripción de dispositivos X.509 al servicio IoT Hub Device Provisioning mediante el SDK del servicio de Node.js
> [!div class="op_single_selector"]
> * [Java](quick-enroll-device-x509-java.md)
> * [Node.js](quick-enroll-device-x509-node.md)

En estos pasos se muestra cómo crear mediante programación un grupo de inscripción para un certificado de entidad de certificación x.509 intermedio o raíz mediante el [SDK del servicio de Node.js](https://github.com/Azure/azure-iot-sdk-node) y un ejemplo de Node.js. Aunque estos pasos funcionan en máquinas Windows y Linux, en este artículo se usa una máquina de desarrollo de Windows.
 

## <a name="prerequisites"></a>requisitos previos

- Asegúrese de completar los pasos descritos en [Configuración de un servicio Azure IoT Hub Device Provisioning con Azure Portal](./quick-setup-auto-provision.md). 

 
- Asegúrese de que tiene [Node.js v4.0 o posterior](https://nodejs.org) instalado en su máquina.


- Necesita un archivo .pem que contenga un certificado de entidad emisora X.509 intermedio o raíz cargado y comprobado con su servicio de aprovisionamiento. El **SDK de Azure IoT para C** contiene herramientas que pueden ayudarle a crear una cadena de certificados X.509, cargar un certificado raíz o intermedio a partir de esa cadena y realizar la prueba de posesión con el servicio para verificar el certificado. Para usar estas herramientas, clone el [SDK de Azure IoT para C](https://github.com/Azure/azure-iot-sdk-c) y siga los pasos descritos en [azure-iot-sdk-c\tools\CACertificates\CACertificateOverview.md](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) en su máquina.

## <a name="create-the-enrollment-group-sample"></a>Crear el ejemplo del grupo de inscripción 

 
1. Desde una ventana de comandos en la carpeta de trabajo, ejecute:
  
     ```cmd\sh
     npm install azure-iot-provisioning-service
     ```  

2. Con un editor de texto, cree un archivo **create_enrollment_group.js** en la carpeta de trabajo. Agregue el siguiente código al archivo y guárdelo:

    ```
    'use strict';
    var fs = require('fs');

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);

    var enrollment = {
      enrollmentGroupId: 'first',
      attestation: {
        type: 'x509',
        x509: {
          signingCertificates: {
            primary: {
              certificate: fs.readFileSync(process.argv[3], 'utf-8').toString()
            }
          }
        }
      },
      provisioningStatus: 'disabled'
    };

    serviceClient.createOrUpdateEnrollmentGroup(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the group enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
        enrollmentResponse.provisioningStatus = 'enabled';
        serviceClient.createOrUpdateEnrollmentGroup(enrollmentResponse, function(err, enrollmentResponse) {
          if (err) {
            console.log('error updating the group enrollment: ' + err);
          } else {
            console.log("updated enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
          }
        });
      }
    });
    ````

## <a name="run-the-enrollment-group-sample"></a>Ejecutar el ejemplo del grupo de inscripción
 
1. Para ejecutar el ejemplo, necesita la cadena de conexión para el servicio de aprovisionamiento. 
    1. Inicie sesión en Azure Portal, haga clic en el botón **Todos los recursos** situado en el menú izquierdo y abra el servicio Device Provisioning. 
    2. Haga clic en **Directivas de acceso compartido** y, a continuación, haga clic en la directiva de acceso que quiere usar para abrir sus propiedades. En la ventana **Directiva de acceso**, copie y tome nota de la cadena de conexión de la clave principal. 

    ![Obtención de la cadena de conexión del servicio de aprovisionamiento desde el portal](./media/quick-enroll-device-x509-node/get-service-connection-string.png) 


3. Como se indica en [Requisitos previos](#prerequisites), necesita un archivo .pem que contenga un certificado de entidad emisora X.509 intermedio o raíz cargado y verificado previamente con su servicio de aprovisionamiento. Para comprobar que el certificado se ha cargado y verificado, en la página de resumen del servicio Device Provisioning de Azure Portal, haga clic en **Certificados**. Busque el certificado que quiere usar para la inscripción de grupo y asegúrese de que su valor de estado sea *verificado*.

    ![Certificado verificado en el portal](./media/quick-enroll-device-x509-node/verify-certificate.png) 

1. Para crear un grupo de inscripción para su certificado, ejecute el comando siguiente (incluidas las comillas alrededor de los argumentos de comando):
 
     ```cmd\sh
     node create_enrollment_group.js "<the connection string for your provisioning service>" "<your certificate's .pem file>"
     ```
 
3. Tras una creación correcta, la ventana de comandos muestra las propiedades del grupo de inscripción nuevo.

    ![Propiedades de la inscripción en la salida del comando](./media/quick-enroll-device-x509-node/sample-output.png) 

4. Compruebe que se ha creado el grupo de inscripción. En Azure Portal, en la hoja de resumen del servicio Device Provisioning, seleccione **Administrar inscripciones**. Seleccione la pestaña **Grupos de inscripción** y compruebe que haya la nueva entrada de inscripción (*primera*).

    ![Propiedades de la inscripción en el portal](./media/quick-enroll-device-x509-node/verify-enrollment-portal.png) 
 
## <a name="clean-up-resources"></a>Limpieza de recursos
Si tiene previsto explorar los ejemplos del servicio de Node.js, no elimine los recursos que se crearon con esta guía de inicio rápido. Si no va a continuar, use el siguiente procedimiento para eliminar todos los recursos de Azure creados con esta guía de inicio rápido.
 
1. Cierre la ventana de salida de ejemplo de Node.js en su máquina.
2. Navegue a su servicio Device Provisioning en Azure Portal, haga clic en **Administrar inscripciones** y, a continuación, seleccione la pestaña **Grupos de inscripción**. Seleccione el *id. de registro* para la entrada de inscripción que creó en esta guía de inicio rápido y haga clic en el botón **Eliminar** situado en la parte superior de la hoja.  
3. Desde el servicio Device Provisioning en Azure Portal, haga clic en **Certificados**, en el certificado que cargó para esta guía de inicio rápido y en el botón **Eliminar** situado en la parte superior de la ventana **Detalles del certificado**.  
 
## <a name="next-steps"></a>pasos siguientes
En esta guía de inicio rápido, ha creado una inscripción de grupo para un certificado de entidad de certificación X.509 intermedio o raíz mediante el servicio Azure IoT Hub Device Provisioning. Para más información acerca del aprovisionamiento de dispositivos, continúe con el tutorial para instalar el servicio Device Provisioning en Azure Portal. 
 
> [!div class="nextstepaction"]
> [Tutoriales del servicio Azure IoT Hub Device Provisioning](./tutorial-set-up-cloud.md)