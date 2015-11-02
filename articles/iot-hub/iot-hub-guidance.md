<properties
 pageTitle="Temas de guía para usar el Centro de IoT de Azure | Microsoft Azure"
 description="Colección de temas de orientación para desarrollar soluciones que usan el Centro de IoT de Azure."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Orientación sobre el Centro de IoT de Azure

## Aprovisionamiento de dispositivos

Las soluciones IoT mantienen información del dispositivo en muchos sistemas y almacenes diferentes. El [registro de identidades del Centro de IoT][IoT Hub Developer Guide - identity registry] es uno de ellos, entre otros almacenes que mantienen información de dispositivos específica de la aplicación. Llamamos *aprovisionamiento* al proceso de crear la información de dispositivo necesaria en todos estos sistemas.

Hay muchos requisitos y estrategias para el aprovisionamiento de dispositivos (consulte la [Guía de administración de dispositivos del Centro de IoT] para obtener más información). El [registro de identidades del Centro de IoT][IoT Hub Developer Guide - identity registry] proporciona las API necesarias para integrar el Centro de IoT en el proceso de aprovisionamiento.

## Puertas de enlace de campo

Una puerta de enlace de campo es un dispositivo especializado o un software de uso general que actúa como un activador de comunicación y potencialmente, como un sistema de control de dispositivos local y centro de procesamiento de datos de dispositivo. Una puerta de enlace de campo puede realizar funciones de procesamiento y control locales hacia los dispositivos; por otro lado, puede filtrar o agregar la telemetría del dispositivo y, por tanto, reducir la cantidad de datos que se transfieren al back-end.

El ámbito de una puerta de enlace de campo incluye la puerta de enlace de campo en sí y todos los dispositivos conectados a ella. Como su nombre indica, las puertas de enlace de campo actúan fuera de las instalaciones de procesamiento de datos dedicadas y suelen estar enlazadas a la ubicación. Una puerta de enlace de campo es diferente de un enrutador de tráfico simple, que tiene un rol activo en la administración del flujo de información y el acceso. Esto significa que es una entidad dirigida a la aplicación y un terminal de sesión o conexión de red (por ejemplo, en este contexto, las puertas de enlace pueden ayudar en el aprovisionamiento de dispositivos, la transformación de datos, la traducción de protocolos y el procesamiento de reglas de evento). Por el contrario, los dispositivos NAT o los firewalls no se consideran puertas de enlace de campo porque no son terminales de sesión o de conexión explícitos, sino que enrutan o deniegan las conexiones o sesiones que se realizan a través de ellos.

### Puertas de enlace de campo opacas frente a transparentes

Con respecto a las identidades de los dispositivos, las puertas de enlace de campo se denominan *transparentes* si otros dispositivos de su ámbito tienen entradas de identidad de dispositivo en el registro de identidades del Centro de IoT. Se denominan *opacas* si la única identidad en el registro de identidades del Centro de IoT es la identidad de la puerta de enlace de campo.

Es importante tener en cuenta que el uso de puertas de enlace *opacas* impide que el Centro de IoT proporcione [protección contra la suplantación de identidad de dispositivo][IoT Hub Developer Guide - Anti-spoofing]. Además, como todos los dispositivos del ámbito de la puerta de enlace de campo se representan como un único dispositivo en el Centro de IoT, estarán sujetos a las cuotas y limitaciones de un solo dispositivo.

### Otras consideraciones

Al implementar una puerta de enlace de campo puede usar los [SDK de dispositivo IoT de Azure][]. Algunos SDK proporcionan funcionalidad específica de la puerta de enlace de campo, como la capacidad de multiplexar la comunicación de varios dispositivos en la misma conexión al Centro de IoT. Tal y como se explica en la [Guía para desarrolladores del Centro de IoT: Elección del protocolo de comunicación][], debe evitar usar HTTP/1 como protocolo de transporte para puertas de enlace de campo.

## Uso de esquemas y servicios personalizados de autenticación de dispositivos

El Centro de IoT de Azure permite configurar las credenciales de seguridad de cada dispositivo y el control de acceso mediante el uso del [registro de identidades de dispositivo][IoT Hub Developer Guide - identity registry].

Si una solución IoT ya realizó una inversión importante en un esquema de autenticación o registro personalizado de identidades de dispositivo, puede seguir usando otras características del Centro de IoT mediante la creación de un *servicio de token* para el Centro de IoT.

El servicio de token es un servicio en la nube que se implementa automáticamente, que usa una *directiva de acceso compartido* del Centro de IoT con permisos **DeviceConnect** para crear tokens *orientados al dispositivo*.

  ![][img-tokenservice]

Estos son los pasos principales del modelo de servicio de tokens.

1. Cree una [directiva de acceso compartido del Centro de IoT][IoT Hub Developer Guide - Security] con permisos **DeviceConnect** para el Centro de IoT. El servicio de tokens usará esta directiva para firmar los tokens.
2. Cuando un dispositivo desea tener acceso al Centro de IoT, solicita un token firmado al servicio de tokens. El dispositivo puede usar el esquema de autenticación o registro de identidades de dispositivo personalizado.
3. El servicio de tokens devuelve un token, creado tal y como se describe en la [Guía para desarrolladores del Centro de IoT: Seguridad][], usando `/devices/{deviceId}` como `resourceURI`, con `deviceId` como dispositivo que se va a autenticar.
4. El dispositivo usa el token directamente con el Centro de IoT.

El servicio de token puede establecer la caducidad de los tokens como desee. Al caducar, el Centro de IoT sirve la conexión y el dispositivo debe solicitar un nuevo token al servicio de token. Claramente, un tiempo de expiración corto aumentará la carga tanto en el dispositivo como en el servicio de token.

Merece la pena especificar que aún hay que crear la identidad del dispositivo en el Centro de IoT para que el dispositivo pueda conectarse. Esto significa también que el control de acceso por dispositivo (deshabilitando las identidades de dispositivo según se indica en la [Guía para desarrolladores del Centro de IoT: Registro de identidades][]) sigue siendo funcional, aunque el dispositivo se autentique con un token. Esto mitiga la existencia de tokens de larga duración.

### Comparación con una puerta de enlace personalizada

El modelo de servicio de token es el método recomendado para implementar un esquema de registro y autenticación de identidades personalizado en el Centro de IoT, porque permite al Centro de IoT controlar la mayoría del tráfico de la solución. Hay casos, sin embargo, en los que el esquema de autenticación personalizado está tan imbricado con el protocolo (por ejemplo, [TLS-PSK][]) que se necesita un servicio que procese todo el tráfico (*puerta de enlace personalizada*). Consulte el artículo [Puerta de enlace de protocolo][] para obtener más información.

## Pasos siguientes

Siga estos vínculos para obtener más información sobre el Centro de IoT de Azure:

- [Introducción al Centro de IoT (Tutorial)][lnk-get-started]
- [¿Qué es el Centro de IoT de Azure?][]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[IoT Hub Developer Guide - identity registry]: iot-hub-devguide.md#identityregistry
[Guía para desarrolladores del Centro de IoT: Registro de identidades]: iot-hub-devguide.md#identityregistry
[Guía de administración de dispositivos del Centro de IoT]: iot-hub-device-management.md
[IoT Hub Developer Guide - Anti-spoofing]: iot-hub-devguide.md#antispoofing
[SDK de dispositivo IoT de Azure]: iot-hub-sdks-summary.md
[Guía para desarrolladores del Centro de IoT: Elección del protocolo de comunicación]: iot-hub-devguide.md#amqpvshttp
[IoT Hub Developer Guide - Security]: iot-hub-devguide.md#security
[Guía para desarrolladores del Centro de IoT: Seguridad]: iot-hub-devguide.md#security
[TLS-PSK]: https://tools.ietf.org/html/rfc4279
[Puerta de enlace de protocolo]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[¿Qué es el Centro de IoT de Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Oct15_HO4-->