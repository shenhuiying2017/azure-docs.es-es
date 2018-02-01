---
title: Uso de las herramientas proporcionadas en los SDK de Azure IoT Hub Device Provisioning para simplificar el desarrollo
description: En este documento se analizan las herramientas de desarrollo proporcionadas en el SDK de Azure IoT Hub Device Provisioning Service
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 01/18/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 76c6f64dea202f661691fafaa78a6d77b4a40f14
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Uso de las herramientas proporcionadas en los SDK para simplificar el desarrollo del aprovisionamiento
IoT Hub Device Provisioning Service simplifica el proceso de aprovisionamiento con un aprovisionamiento Just-In-Time sin intervención del usuario de una manera segura y escalable.  Se requiere una atestación de seguridad mediante el certificado X.509 o el Módulo de plataforma segura (TPM).  Microsoft también está asociado con [otros asociados de hardware de seguridad](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) para mejorar la confiabilidad en la protección de la implementación de IoT. A los desarrolladores les puede resultar bastante complicado conocer el requisito de seguridad de hardware. Se proporciona un conjunto de SDK de Azure IoT Provisioning Service para que los desarrolladores puedan usar un nivel de comodidad para los clientes de escritura que se comunican con el servicio de aprovisionamiento. Los SDK también proporcionan ejemplos para escenarios comunes, así como un conjunto de herramientas para simplificar la atestación de seguridad en el desarrollo.

## <a name="trusted-platform-module-tpm-simulator"></a>Simulador del Módulo de plataforma segura (TPM)
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security#trusted-platform-module-tpm) puede hacer referencia a un estándar para almacenar de forma segura las claves para autenticar la plataforma, o puede hacer referencia a la interfaz de E/S que se utiliza para interactuar con los módulos que implementan el estándar. Los módulos de plataforma segura pueden existir como hardware discreto, hardware integrado, basados en firmware o basados en software.  En producción, TPM se encuentra en el dispositivo, ya sea como hardware discreto, hardware integrado o basado en el firmware. En la fase de prueba, se proporciona a los desarrolladores un simulador de TPM basado en software.  Este simulador solo está disponible por ahora para el desarrollo en la Plataforma de Windows.

Los pasos para utilizar el simulador de TPM son:
1. [Prepare el entorno de desarrollo](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment) y clone el repositorio de GitHub:
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Navegue hasta la carpeta del simulador de TPM en ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Ejecute Simulator.exe antes de ejecutar cualquier aplicación cliente para aprovisionar el dispositivo.
4. Deje que el simulador se ejecute en segundo plano durante el proceso de aprovisionamiento para obtener el identificador de registro y la clave de aprobación.  Ambos valores solo son válidos para una instancia de la ejecución.

## <a name="x509-certificate-generator"></a>Generador de certificados X.509
Los [certificados X.509](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) pueden utilizarse como un mecanismo de atestación para escalar la producción y simplificar el aprovisionamiento de dispositivos.  Hay [varias formas](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) de obtener un certificado X.509:
* Para un entorno de producción, se recomienda adquirir un certificado de entidad de certificación X.509 de una entidad de certificación raíz pública.
* Para entornos de pruebas, puede generar un certificado raíz X.509 o una cadena de certificados X.509 mediante:
    * OpenSSL: esta [guía de procedimientos](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-create-certificates) lo orienta mediante scripts de PowerShell de ejemplo que usan [OpenSSL](https://www.openssl.org/) para crear y firmar certificados X.509.  Además, también puede usar el script en otros idiomas para la generación de certificados:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Emulador de Device Identity Composition Engine (DICE): DICE puede usarse para la atestación e identidad del dispositivo cifradas basadas en el protocolo TLS y los certificados de cliente X.509.  [Obtenga más información](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) sobre la identidad del dispositivo con DICE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Uso del generador de certificados X.509 con el emulador de DICE
Los SDK proporcionan un generador de certificados X.509 con el emulador de DICE, ubicado en el [SDK de Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Este generador tiene un funcionamiento multiplataforma.  El certificado generado puede usarse para el desarrollo en otros lenguajes.

Actualmente, el emulador de DICE genera un certificado raíz, un certificado intermedio, un certificado de hoja y una clave privada asociada.  Sin embargo, no se puede usar el certificado raíz ni el intermedio para firmar un certificado de hoja independiente.  Si desea probar el escenario de inscripción de grupo donde se utiliza un certificado de firma para firmar los certificados de hoja de varios dispositivos, puede usar OpenSSL para generar una cadena de certificados.

Para generar el certificado X.509 mediante este generador:
1. [Prepare el entorno de desarrollo](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment) y clone el repositorio de GitHub:
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Cambie la raíz a azure-iot-sdk-java.
3. Ejecute ```mvn install -DskipTests=true``` para descargar todos los paquetes necesarios y compilar el SDK.
4. Navegue a la raíz del generador de certificados X.509 en ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. Realice la compilación con ```mvn clean install```.
6. Ejecute la herramienta con los comandos siguientes:
```
cd target
java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
```
7. Cuando se le solicite, puede especificar opcionalmente un _nombre común_ para los certificados.
8. La herramienta genera un **certificado de cliente**, la **clave privada del certificado de cliente**, un **certificado intermedio** y el **certificado raíz** de forma local.

El **certificado de cliente** es el certificado de hoja del dispositivo.  El **certificado de cliente** y la **clave privada del certificado cliente** asociada son necesarios en el cliente de dispositivo. En función del lenguaje que elija, el mecanismo para incluir esto en la aplicación cliente puede variar.  Para obtener más información, consulte los [tutoriales de inicio rápido](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) sobre cómo crear dispositivos simulados con X.509.

El certificado raíz o el intermedio pueden usarse para crear un grupo de inscripción o una inscripción individual [mediante programación](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) o mediante el [portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>pasos siguientes
* Desarrollo con el [SDK de IoT de Azure]( https://github.com/Azure/azure-iot-sdks) para Azure IoT Hub y Azure IoT Hub Device Provisioning Service