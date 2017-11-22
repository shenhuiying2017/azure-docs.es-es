---
title: Seguridad en Azure IoT Edge | Microsoft Docs
description: "Seguridad, autenticación y autorización de dispositivos de IoT Edge"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8a5bf1f35fcdd779cf27edeba7dfd5705cbae205
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="securing-azure-iot-edge---preview"></a>Protección de Azure IoT Edge - versión preliminar

Es necesaria la protección del dispositivo perimetral inteligente para otorgar confianza en el funcionamiento de una solución de IoT de un extremo a otro. Azure IoT Edge está pensado para la seguridad que es extensible a diferentes perfiles de riesgo y escenarios de implementación, y ofrece la misma protección que se espera de todos los servicios de Azure.

Azure IoT Edge se ejecuta en un hardware diferente, admite Linux y Windows y se aplica a escenarios de implementación diferentes.  El riesgo evaluado depende de muchas consideraciones, entre las que se incluyen la propiedad de la solución, la geografía de implementación, la confidencialidad de datos, la privacidad, el mercado vertical de aplicación y los requisitos normativos.  En lugar de ofrecer soluciones concretas a escenarios específicos, es más adecuado diseñar un marco de seguridad extensible basado en principios bien fundamentados y diseñados para la escala. 
 
En este artículo se ofrece información general sobre el marco de seguridad. Para más información, consulte la entrada de blog [Securing the intelligent edge][lnk-edge-blog] (Protección del perímetro inteligente).

>[!NOTE]
>El marco de seguridad que se describe a continuación se está agregando al producto ahora y estará disponible en la versión de disponibilidad general de Azure IoT Edge. El producto se encuentra actualmente en versión preliminar pública, una versión diseñada a permitir el desarrollo y la creación de prototipos de soluciones perimetrales, no las implementaciones de producción completas que necesitan el marco de seguridad completo.   

## <a name="standards"></a>Estándares

Los estándares promueven la facilidad de examen y la de implementación, que constituyen el sello distintivo de la seguridad.  Una solución de seguridad bien estructurada debe prestarse al examen bajo evaluación para crear relaciones de confianza y no debe ser un obstáculo para la implementación.  El diseño del marco para proteger a Azure IoT Edge surge de protocolos de seguridad probados en el sector y probados en el tiempo para aprovechar el conocimiento y la reutilización. 

## <a name="authentication"></a>Autenticación

Saber sin lugar a dudas qué actores, dispositivos y componentes participan en la entrega de valor a través de una solución completa de IoT es fundamental para generar confianza.  Este conocimiento ofrece la seguridad de que los participantes rinden cuentas para poder ser admitidos.  Azure IoT Edge alcanza este conocimiento a través de la autenticación.  El mecanismo principal de autenticación para la plataforma de Azure IoT Edge es la autenticación basada en certificados.  Este mecanismo procede de un conjunto de normas que rigen la infraestructura de clave pública (PKiX) de Internet Engineering Task Force (IETF).     

El marco de seguridad de Azure IoT Edge requiere identidades de certificado únicas para todos los dispositivos, módulos (contenedores que encapsulan la lógica dentro del dispositivo) y actores que interactúan con el dispositivo de Azure IoT Edge, ya sea físicamente o a través de una conexión de red.  No todos los escenarios o componentes pueden prestarse a una autenticación basada en certificados para la cual la extensibilidad del marco de seguridad ofrece vías seguras de alojamiento. 

## <a name="authorization"></a>Autorización

La capacidad de delegar autoridad y controlar el acceso es crucial para lograr un principio de seguridad fundamental: el principio del menor privilegio.  Los dispositivos, módulos y actores solo pueden acceder a los recursos y datos dentro del ámbito de su permiso y únicamente cuando sea factible desde el punto de vista de la arquitectura.  Esto significa que algunos permisos se configuran con suficientes privilegios y otros se aplican en función de su arquitectura.  Por ejemplo, mientras que un módulo puede autorizarse a través de una configuración con privilegios para iniciar una conexión a Azure IoT Hub, no hay ningún motivo por el que un módulo en un dispositivo de Azure IoT Edge deba acceder al gemelo de un módulo en otro dispositivo de Azure IoT Edge.  Por este motivo, esto último quedaría arquitectónicamente excluido. 

Otros esquemas de autorización incluyen derechos de firma de certificado y control de acceso basado en rol (RBAC).  La extensibilidad del marco de seguridad permite la adopción de otros esquemas de autorización consolidados. 

## <a name="attestation"></a>Atestación

La atestación garantiza la integridad de los componentes de software.  Es importante para la detección y prevención del malware.  El marco de seguridad de Azure IoT Edge clasifica la atestación en tres categorías principales:

* Atestación estática
* Atestación en tiempo de ejecución
* Atestación de software

### <a name="static-attestation"></a>Atestación estática

La atestación estática es la comprobación de la integridad de todos los componentes de software, incluidos los sistemas operativos, los entornos en tiempo de ejecución y la información de configuración en el arranque del dispositivo.  A menudo se conoce como arranque seguro.  El marco de seguridad para los dispositivos de Azure IoT Edge se extiende a los proveedores de silicio e incorpora funcionalidades seguras de hardware para garantizar procesos de atestación estática. Estos procesos incluyen procesos de arranque seguro y de actualización de firmware.  Trabajar en estrecha colaboración con los proveedores de silicio elimina las capas de firmware superfluas, con lo que se minimiza la superficie de amenaza. 

### <a name="runtime-attestation"></a>Atestación en tiempo de ejecución

Cuando un sistema ha completado un proceso de arranque validado y está en funcionamiento, los sistemas seguros bien diseñados podrían detectar intentos de insertar malware a través de los puertos e interfaces de los sistemas y tomarán las contramedidas apropiadas.  Para los dispositivos perimetrales inteligentes en custodia física de actores malintencionados, es posible insertar contenido malintencionado a través de otros medios que no sean interfaces de dispositivos como la manipulación y los ataques de canales laterales.   Este tipo de contenido malintencionado, que puede ser en forma de malware o de cambios de configuración no autorizados, normalmente no se detecta por el proceso de arranque seguro porque ocurre después de dicho proceso.  Las contramedidas ofrecidas o exigidas por el hardware del dispositivo contribuyen en gran medida a evitar tales amenazas.  El marco de seguridad para Azure IoT Edge pide explícitamente extensiones para combatir amenazas en tiempo de ejecución.     

### <a name="software-attestation"></a>Atestación de software

Todos los sistemas en buen estado, incluidos los sistemas perimetrales inteligentes, deben ser aptos para revisiones y actualizaciones.  La seguridad es importante para los procesos de actualización; de lo contrario, pueden ser vectores potenciales de amenazas.  El marco de seguridad para Azure IoT Edge requiere actualizaciones a través de paquetes medidos y firmados para asegurar la integridad y autenticar el origen de los paquetes.  Esto se aplica a todos los sistemas operativos y componentes de software de la aplicación. 

## <a name="hardware-root-of-trust"></a>Raíz de confianza del hardware

Para muchas implementaciones de dispositivos perimetrales inteligentes, especialmente aquellas implementadas en lugares donde actores malintencionados potenciales tienen acceso físico al dispositivo, la seguridad que ofrece el hardware del dispositivo es la última defensa de protección.  Por esta razón, la confianza de delimitación en el hardware resistente a manipulación es fundamental para tales implementaciones.  El marco de seguridad para Azure IoT Edge implica la colaboración de los proveedores de hardware de silicio seguro para ofrecer distintos tipos de raíz de confianza del hardware para dar cabida a varios perfiles de riesgo y escenarios de implementación. Estos incluyen el uso de silicio seguro que se adhiere a los estándares comunes de protocolos de seguridad como Módulo de plataforma segura (ISO/IEC 11889) y el motor Device Identifier Composition Engine (DICE) de Trusted Computing Group.  También incluyen tecnologías de enclave seguro como TrustZones y Software Guard Extensions (SGX). 

## <a name="certification"></a>Certificación

Para ayudar a los clientes a tomar decisiones informadas cuando adquieren dispositivos de Azure IoT Edge para su implementación, el marco de Azure IoT Edge incluye requisitos de certificación.  La base de estos requisitos son las certificaciones relativas a las notificaciones y certificaciones de seguridad relativas a la validación de la implementación de seguridad.  Por ejemplo, una certificación de la notificación de seguridad podría informar que el dispositivo de IoT Edge utiliza hardware seguro conocido por resistir ataques de arranque. Una certificación de validación podría informar que el hardware seguro se ha implementado correctamente para ofrecer este valor en el dispositivo.  De acuerdo con el principio de simplicidad, el marco ofrece la visión de mantener la carga de la certificación mínima.   

## <a name="extensibility"></a>Extensibilidad

La extensibilidad es un componente de primera clase en el marco de seguridad de Azure IoT Edge.  Con la tecnología IoT que impulsa diferentes tipos de transformaciones de negocio, es lógico que la seguridad evolucione a la par que los escenarios emergentes.  El marco de seguridad de Azure IoT Edge comienza con una base sólida en la que se construye la extensibilidad en diferentes dimensiones para incluir: 

* Servicios de seguridad de primera mano, como el servicio Device Provisioning para Azure IoT Hub.
* Servicios de terceros, como servicios de seguridad administrados para diferentes aplicaciones verticales (como servicios industriales o sanitarios) o enfocados a la tecnología (como la supervisión de seguridad de redes de malla o servicios de atestación de hardware de silicio) a través de una rica red de asociados.
* Los sistemas heredados deben incluir la retroadaptación con estrategias de seguridad alternativas, como el uso de tecnología segura distinta de los certificados para la autenticación y administración de identidades.
* Hardware seguro para la adopción sin fisuras de tecnologías de hardware seguro emergentes y contribuciones de asociados de silicio.

Estos son solo unos pocos ejemplos de dimensiones para la extensibilidad y el marco de seguridad de Azure IoT Edge está diseñado para ser seguro desde el principio para admitir esta extensibilidad. 

Al final, el mayor éxito en asegurar el perímetro inteligente resulta de las contribuciones de colaboración de una comunidad abierta impulsada por el interés común de proteger IoT.  Estas contribuciones podrían consistir en servicios o tecnologías seguros.  El marco de seguridad de Azure IoT Edge ofrece una base sólida para la seguridad que es extensible a la máxima cobertura para ofrecer el mismo nivel de confianza e integridad en el perímetro inteligente que con la nube de Azure.  

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo Azure IoT Edge va a [proteger el perímetro inteligente][lnk-edge-blog].

<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/ 