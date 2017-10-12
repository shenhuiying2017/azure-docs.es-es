---
title: "Introducción a AMQP 1.0 en Azure Service Bus | Microsoft Docs"
description: "Obtenga información acerca de cómo utilizar Advanced Message Queuing Protocol (AMQP) 1.0 en Azure."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: e2d190dddc3b84eb07eaf2ffea1af48bba0ea13a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="amqp-10-support-in-service-bus"></a>Soporte de AMQP 1.0 en el Bus de servicio
Tanto el servicio en la nube de Azure Service Bus como el [Service Bus para Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) local, admiten el protocolo AMQP (Advanced Message Queueing Protocol) 1.0. AMQP le permite construir aplicaciones híbridas, entre plataformas, utilizando un protocolo estándar abierto. Puede construir aplicaciones mediante componentes creados con distintos lenguajes y marcos, y que se ejecutan en diferentes sistemas operativos. Todos estos componentes se pueden conectar al Bus de servicio e intercambiar directamente mensajes empresariales estructurados de manera eficaz y con total fidelidad.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Introducción: ¿Qué es AMQP 1.0 y por qué es tan importante?
Tradicionalmente, los productos de middleware orientados a mensajes utilizaban protocolos propietarios para la comunicación entre las aplicaciones cliente y los agentes. Esto significa que una vez seleccionado el agente de mensajes de un proveedor particular, usted debía utilizar las bibliotecas de dicho proveedor para conectar las aplicaciones cliente con ese agente. Esto ocasionaba un cierto grado de dependencia de ese proveedor, ya que la portabilidad de una aplicación a otro producto requería cambios en la codificación de todas las aplicaciones relacionadas. 

Además, es difícil conectar a los agentes de mensajes de diferentes proveedores. Normalmente, esto requiere el establecimiento de un puente en el nivel de la aplicación para mover los mensajes de un sistema a otro y para traducir entre sus formatos de mensajes en propiedad. Es un requisito habitual, por ejemplo, cuando hay que proporcionar una nueva interfaz unificada para sistemas heterogéneos más antiguos o al integrar sistemas de TI después de una fusión.

La industria del software es un negocio muy dinámico; nuevos lenguajes de programación y marcos de aplicaciones continúan inventándose, a veces a un ritmo enloquecido. De igual forma, los requisitos de los sistemas de TI evolucionan con el tiempo y los desarrolladores desean sacar partido las características de las plataformas más recientes. Sin embargo, a veces el proveedor de mensajes seleccionado no admite estas plataformas. Como los protocolos de mensajería son propietarios, no es posible que otros proporcionen bibliotecas para estas nuevas plataformas. Por lo tanto, hay que utilizar métodos como crear puertas de enlace o puentes para poder seguir usando el producto de mensajería.

Estos problema motivaron el desarrollo del protocolo de colas de mensajes avanzados (AMQP) 1.0. Tuvo su origen en JP Morgan Chase, que, al igual que la mayoría de empresas de servicios financieros, consume una gran cantidad de middleware orientado a mensajes. El objetivo era sencillo: crear un protocolo de mensajes de estándar abierto que hiciera posible crear aplicaciones basadas en mensajes utilizando componentes construidos con otros lenguajes, marcos y sistemas operativos, usando en todos ellos los mejores componentes de una variedad de proveedores.

## <a name="amqp-10-technical-features"></a>Características técnicas de AMQP 1.0
AMQP 1.0 es un protocolo de mensajes a nivel de red, confiable y eficaz que se puede utilizar para crear aplicaciones de mensajes robustas y compatibles entre plataformas. El protocolo tiene un objetivo simple: definir la mecánica de la transferencia segura, confiable y eficaz de mensajes entre dos partes. Los mismos mensajes se codifican usando una representación de datos portátiles que permite que remitentes y receptores heterogéneos intercambien mensajes empresariales estructurados con la máxima fidelidad. A continuación se incluye un resumen de las características más importantes:

* **Eficaz**: AMQP 1.0 es un protocolo orientado a la conexión que utiliza una codificación binaria para las instrucciones de protocolo y los mensajes empresariales transferidos por él. Incorpora avanzados esquemas de control de flujo para potenciar al máximo la utilización de la red y los componentes conectados. Es decir, el protocolo se ha diseñado para lograr un equilibrio entre eficacia, flexibilidad e interoperabilidad.
* **Confiable**: el protocolo AMQP 1.0 permite que se intercambien los mensajes con un rango de garantías de confiabilidad, desde el "enviar y olvidar" a la entrega confiable y confirmada.
* **Flexible**: AMQP 1.0 es un protocolo flexible que se puede usar para admitir distintas topologías. El mismo protocolo se puede utilizar para las comunicaciones cliente-a-cliente, cliente-a-agente y agente-a-agente.
* **Independiente del modelo del agente**: la especificación AMQP 1.0 no impone ningún requisito en el modelo de mensajes utilizado por un agente. Esto significa que es posible que se pueda agregar fácilmente compatibilidad con AMPQ 1.0 a agentes de mensajes existentes.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 es un Estándar (con mayúscula)
AMQP 1.0 es un estándar internacional, aprobado por ISO e IEC como ISO/IEC 19464:2014.

AMQP 1.0 lo ha estado desarrollando desde 2008 un grupo central de 20 compañías, tanto proveedores de tecnología como empresas usuarias. Durante este tiempo, las empresas usuarias han contribuido con sus requisitos empresariales del mundo real y los proveedores de tecnología han hecho evolucionar el protocolo para cumplir con estos requisitos. Durante el proceso, los proveedores han participado en talleres, en los que han colaborado para validar la interoperabilidad entre sus implementaciones.

En octubre de 2011, el trabajo de desarrollo pasó a un Comité técnico dentro de la Organización para el Avance de Estándares de Información Estructurados (OASIS), y el estándar OASIS AMQP 1.0 se publicó en octubre de 2012. Las empresas siguientes participaron en el Comité técnico durante el desarrollo del estándar:

* **Proveedores de tecnología**: Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Firmas de usuario**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Algunos de los beneficios que se mencionan con más frecuencia de los estándares abiertos son los siguientes:

* Menos probabilidad de dependencia con el proveedor
* Interoperabilidad
* Amplia disponibilidad de bibliotecas y herramientas
* Protección contra la obsolescencia
* Disponibilidad de personal experto
* Menor riesgo y más controlable

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 y Bus de servicio
La compatibilidad con AMQP 1.0 en el Bus de servicio de Azure implica que ahora puede sacar partido de sus características de encolamiento del Bus de servicio y de la publicación/suscripción de mensajería asíncrona desde una amplia variedad de plataformas mediante un eficaz protocolo binario. Además, puede desarrollar aplicaciones formadas por componentes creados con una mezcla de lenguajes, marcos y sistemas operativos.

En la siguiente ilustración se muestra una implementación de ejemplo en el que clientes de Java que se ejecutan en Linux, escritos usando la API estándar Java Message Service (JMS), y clientes .NET que se ejecutan en Windows, intercambian mensajes a través del Bus de servicio mediante AMQP 1.0.

![][0]

**Figura 1: Escenario de implementación de ejemplos que muestran mensajes entre plataformas mediante Service Bus y AMQP 1.0**

En este momento las siguientes bibliotecas de cliente están trabajando con el Bus de servicio:

| Idioma | Biblioteca |
| --- | --- |
| Java |Cliente de Java Message Service (JMS) Apache Qpid<br/>Cliente de Java SwiftMQ, de IIT Software. |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-Python |
| C# |AMQP .Net Lite |

**Figura 2: Tabla de bibliotecas de cliente de AMQP 1.0**

## <a name="summary"></a>Resumen
* AMQP 1.0 es un protocolo de mensajes confiable y abierto que se puede utilizar para crear aplicaciones híbridas, entre plataformas. AMQP 1.0 es un estándar de OASIS.
* La compatibilidad con AMQP 1.0 ahora está disponible en el Bus de servicio de Azure, así como en el Bus de servicio para Windows Server (Bus de servicio 1.1). El precio es el mismo que el de los protocolos existentes.

## <a name="next-steps"></a>Pasos siguientes
¿Listo para obtener más información? Consulte los siguientes vínculos:

* [Uso de Service Bus desde .NET con AMQP]
* [Uso de Service Bus desde Java con AMQP]
* [Instalación de Apache Qpid Proton-C en una VM Linux de Azure]
* [AMQP de Service Bus para Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Uso de Service Bus desde .NET con AMQP]: service-bus-amqp-dotnet.md
[Uso de Service Bus desde Java con AMQP]: service-bus-amqp-java.md
[Instalación de Apache Qpid Proton-C en una VM Linux de Azure]: service-bus-amqp-apache.md
[AMQP de Service Bus para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
