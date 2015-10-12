<properties
   pageTitle="Puerta de enlace de protocolos de IoT de Azure | Microsoft Azure"
   description="Describe cómo usar la puerta de enlace de protocolos de IoT de Azure para ampliar las capacidades del Centro de IoT de Azure."
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="kevinmil"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/29/2015"
   ms.author="kdotchkoff"/>

# Compatibilidad con protocolos adicionales para Centro de IoT

El Centro de IoT admite de forma nativa la comunicación a través de los protocolos HTTPS y AMQP. En algunos casos, puede que los dispositivos o las puertas de enlace de campo no puedan usar uno de estos protocolos estándar y requerirán la adaptación del mismo. En estos casos, una puerta de enlace personalizada puede habilitar la adaptación de protocolos para los extremos del Centro de IoT reduciendo el tráfico que se origina o finaliza en el Centro de IoT. La puerta de enlace de protocolos de IoT de Azure habilita la adaptación de protocolos para el Centro de IoT e implementa un adaptador del protocolo MQTT para habilitar la comunicación entre el dispositivo IoT y el Centro de IoT a través del protocolo MQTT.

## Puerta de enlace de protocolos de IoT de Azure

La puerta de enlace de protocolos de IoT de Azure es un marco para la adaptación de protocolos diseñado para la comunicación bidireccional a gran escala de dispositivos con el Centro de IoT. La puerta de enlace de protocolos es un componente de paso a través que acepta conexiones de dispositivos a través de un protocolo específico y establece un puente para el tráfico al Centro de IoT a través de AMQP 1.0. La puerta de enlace de protocolos de IoT está disponible como proyecto de software de código abierto (OSS) que proporciona flexibilidad para agregar compatibilidad con diversos protocolos y versiones de protocolo.

La puerta de enlace de protocolos se puede implementar en Azure de una manera altamente escalable con roles de trabajo de servicios en la nube. Además, la puerta de enlace de protocolos puede implementarse en entornos locales como puertas de enlace de campo.

La puerta de enlace de protocolos de IoT de Azure incluye un adaptador de MQTT para facilitar la comunicación con dispositivos a través del protocolo MQTT v3.1.1. La puerta de enlace de protocolos y la implementación de MQTT se proporcionan como proyecto OSS con flexibilidad para permitir la personalización de la implementación según sea necesario.
   
El adaptador de MQTT también muestra el modelo de programación para la creación de adaptadores de protocolo para otros protocolos. Además, el modelo de programación de puerta de enlace de protocolo de IoT permite conectar componentes personalizados para su procesamiento especializado como, por ejemplo, autenticación personalizada, transformaciones de mensajes, compresión y descompresión o cifrado y descifrado de tráfico entre los dispositivos y el Centro de IoT.

## Pasos siguientes 

Para obtener más información sobre la puerta de enlace de protocolos de IoT de Azure y cómo usarlo e implementarlo como parte de su solución de IoT, vea:

* [Repositorio de puerta de enlace de protocolos de IoT de Azure en GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guía para desarrolladores de puerta de enlace de protocolos de IoT de Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

<!---HONumber=Oct15_HO1-->