---
title: 'Referencia de Azure Active Directory B2C: marcos de confianza | Microsoft Docs'
description: Un tema acerca de las directivas personalizadas de Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 019dd66d5abe4242ffb6cdc3276929dcd7f04466
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---

# <a name="defining-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definición de marcos de confianza con el marco de experiencia de identidad de Azure AD B2C

Las directivas personalizadas de Azure AD B2C que aprovechan el marco de experiencia de identidad ofrecen a su organización un servicio centralizado que reduce la complejidad de la federación de identidades en una comunidad grande de interés a una única relación de confianza y un único intercambio de datos.

Estas directivas van a permitirle responder a las siguientes preguntas.

- *¿Cuáles son las directivas de seguridad, privacidad, legales y de protección de datos que es necesario cumplir?*
- *¿Quiénes son los contactos y cuáles son los procesos para convertirse en un participante acreditado?*
- *¿Quiénes son los proveedores de información de identidad acreditados (también conocidos como proveedores de notificaciones) y qué ofrecen?*
- *¿Quiénes son los usuarios de confianza acreditados y, opcionalmente, qué necesitan?*
- *¿Cuáles son los requisitos técnicos de interoperabilidad en la red para los participantes?*
- *¿Cuáles son las reglas de "tiempo de ejecución" operativas que se deben aplicar para intercambiar información de identidad digital?*

Para responder a estas preguntas, las directivas personalizadas de Azure AD B2C que aprovechan el marco de experiencia de identidad usan la construcción del marco de confianza (TF). Vamos a considerar esta construcción y lo que proporciona en este sentido.

## <a name="understanding-the-trust-framework-and-federation-management-foundation"></a>Descripción del marco de confianza y la base de administración de federación

Dicha construcción debe entenderse como una especificación escrita de las directivas de identidad, seguridad, privacidad y protección de datos que deben cumplir los participantes en una comunidad de interés.

La identidad federada proporciona una base para conseguir la garantía de identidad del usuario final a escala de Internet.  Al delegar la administración de identidades en terceros, una única identidad digital de un usuario final se puede reutilizar con varios usuarios de confianza.  

De hecho, la garantía de identidad requiere que los proveedores de identidades (IdP) y los proveedores de atributos (AtPs) cumplan directivas y prácticas de seguridad, privacidad y operativas específicas.  En ausencia de capacidad para realizar inspecciones directas, los usuarios de confianza (RP) deben desarrollar relaciones de confianza con los IdP y AtP con los que elijan trabajar.  A medida que el número de consumidores y proveedores de información de identidad digital se multiplica, resulta insostenible continuar la administración pareja de estas relaciones de confianza, o incluso el intercambio parejo de los metadatos técnicos necesarios para la conectividad de red.  Los centros de federación solo han logrado un éxito limitado a la hora de resolver estos problemas.

Los TF son el eje del modelo de marco de confianza de Open Identity Exchange (OIX), en el que cada comunidad de interés se rige por una especificación determinada de TF. Esta especificación de TF define:

- Las métricas de seguridad y privacidad de la comunidad de interés con la definición de:
    - Los niveles de seguridad (LOA) que ofrecen o necesitan los participantes; es decir, un conjunto ordenado de clasificaciones de confianza de la autenticidad de la información de identidad digital.
    - Los niveles de protección (LOP) que ofrecen o necesitan los participantes; es decir, un conjunto ordenado de clasificaciones de confianza para la protección de la información de identidad digital controladas por los participantes de la comunidad de interés.
- La descripción de la información de identidad digital que ofrecen o necesitan los participantes.
- Las directivas técnicas para producción y consumo de la información de identidad digital y, por tanto, para la medición de LOA y LOP. Estas directivas escritas incluyen normalmente las siguientes categorías:
    - Directivas de corrección de identidad: *lo fuerte que se examina la información de identidad de una persona*
    - Directivas de seguridad: *lo fuerte que se protege la integridad y la confidencialidad de la información*
    - Directivas de privacidad: *el control que tiene un usuario sobre la información personal identificable (PII)*
    - Directivas de supervivencia: continuidad y protección de PII si un proveedor suspende sus operaciones.
- Los perfiles técnicos de producción y consumo de información de identidad digital. Estos perfiles:
    - Abarcan las interfaces para las que la información de identidad digital está disponible en el LOA especificado.
    - Describen los requisitos técnicos para la interoperabilidad en la red.
- Las descripciones de los diversos roles que pueden desempeñar los participantes de la comunidad junto con las cualificaciones necesarias para cumplir estos roles.

Por lo tanto, una especificación de TF controla cómo se intercambia la información de identidad entre los participantes de la comunidad de interés: usuarios de confianza, proveedores de atributos e identidades y comprobadores de atributos.

En el lenguaje de este modelo OIX TF, una especificación de TF se constituye como uno o varios documentos que sirven de referencia para el gobierno de la comunidad de interés que regulan la aserción y el consumo de información de identidad digital dentro de la comunidad. Esto significa un conjunto de directivas y procedimientos, diseñados para establecer la confianza en las identidades digitales usadas para las transacciones en línea entre los miembros de una comunidad de interés.  **Una especificación de TF define las reglas para la creación de un ecosistema de identidad federada viable para algunas comunidades.**

Hoy por hoy, existe un consenso generalizado sobre al beneficio de un enfoque así, y no hay duda de que las especificaciones del marco de confianza facilitarán el desarrollo de ecosistemas de identidad digital con características comprobables de seguridad, garantía y privacidad, de forma que se puedan reutilizar en varias comunidades de interés.

Por este motivo, las directivas personalizadas de Azure AD B2C que aprovechan el marco de experiencia de identidad usan la especificación como base de su representación de datos de un TF a fin de facilitar la interoperabilidad.  

Las directivas personalizadas de Azure AD B2C que aprovechan el marco de experiencia de identidad representan una especificación de TF como una mezcla de datos legibles por el ojo humano y la máquina: algunas secciones de este modelo (normalmente las más orientadas al gobierno) se representan como referencias a documentación de directivas de seguridad y privacidad publicada, junto con los procedimientos relacionados, si los hay; otras, describen de forma detallada los metadatos de configuración y las reglas de tiempo de ejecución para facilitar la automatización operativa.

## <a name="understanding-trust-framework-policies"></a>Descripción de las directivas del marco de confianza

En términos de implementación, las especificaciones de TF anteriores constan de directivas personalizadas de Azure AD B2C que aprovechan el marco de experiencia de identidad en un conjunto de directivas que permiten controlar completamente los comportamientos y las experiencias de identidad.  Las directivas personalizadas de Azure AD B2C que aprovechan el marco de experiencia de identidad permiten de hecho crear su propio marco de confianza mediante tales directivas declarativas que pueden definir y configurar:

- Las referencias a documentos que definen el ecosistema de identidad federada de la comunidad relacionada con el TF. Son vínculos a la documentación de TF. Las reglas de "tiempo de ejecución" operativas (predefinidas) o los recorridos de usuario que automatizan o controlan el intercambio y el uso de las notificaciones. Estos recorridos están asociados con un LOA (y un LOP). Una directiva puede tener por lo tanto recorridos de usuario con LOA (y LOP) variables.
- Los proveedores de identidades y atributos, o los proveedores de notificaciones de la comunidad de interés y los perfiles técnicos que admiten, junto con la acreditación de LOA/LOP (fuera de banda) relacionada con ellos.
- La integración con comprobadores de atributos o los proveedores de notificaciones.
- Los usuarios de confianza de la comunidad (mediante inferencia).
- Los metadatos para establecer comunicaciones de red entre los participantes. Estos metadatos, junto con los perfiles técnicos, se usarán en el curso de una transacción para asociar la interoperabilidad en la red entre el usuario de confianza y otros participantes de la comunidad.
- La conversión de protocolo, si la hay (SAML, OAuth2, WS-Federation y OpenID Connect).
- Los requisitos de autenticación.
- La orquestación multifactor, si la hay.
- Un esquema compartido para todas las notificaciones disponibles y asignaciones a participantes de una comunidad de interés.
- Todas las transformaciones de notificación, junto con la posible minimización de datos en este contexto, para sostener el intercambio y el uso de notificaciones.
- El enlace y el cifrado.
- El almacenamiento de notificaciones

> [!NOTE]
> Nos referimos en conjunto a todos los tipos posibles de información de identidad que se pueden intercambiar como notificaciones: notificaciones sobre las credenciales de autenticación de un usuario final, examen de identidades, dispositivo de comunicación, ubicación física, atributos de identificación personal, etc.  
>
> Usamos el término *notificaciones*, en lugar de atributos (que es un subconjunto), porque en el caso de transacciones en línea, no son hechos que pueda comprobar directamente el usuario de confianza, sino que son aserciones o notificaciones sobre hechos para los que el usuario de confianza debe desarrollar confianza suficiente para conceder la transacción solicitada del usuario final.  
>
> Se debe también al hecho de que las directivas personalizadas de Azure AD B2C que aprovechan el marco de experiencia de identidad están diseñadas para simplificar el intercambio de todos los tipos de información de identidad digital de una manera coherente, sin importar si el protocolo subyacente está definido para la autenticación de usuarios o la recuperación de atributos.  Del mismo modo, usaremos el término proveedor de notificaciones para referirnos en conjunto a proveedores de identidades, proveedores de atributos y comprobadores de atributos cuando no queremos distinguir entre sus funciones específicas.   

Por lo tanto, estas determinan cómo la información se intercambia entre un usuario de confianza, los proveedores de identidades y atributos y los comprobadores de atributos. Controlan qué proveedores de identidades y atributos se requieren para la autenticación de un usuario de confianza. Se deben considerar como un lenguaje específico del dominio (DSL), es decir, un lenguaje informático especializado para un dominio de aplicación adicional con herencia, instrucciones *if* y polimorfismo.

Estas directivas constituyen la parte legible por la máquina de la construcción de TF en directivas personalizadas de Azure AD B2C que aprovechan el marco de experiencia de identidad con todos los detalles operativos, incluidos los metadatos de los proveedores de notificaciones y la definición de esquema de notificaciones de perfiles técnicos, las funciones de transformación de notificación y los recorridos de usuario rellenos para facilitar la orquestación y la automatización operacionales.  

Se consideran *documentos vivos* en las directivas personalizadas de Azure AD B2C que aprovechan el marco de experiencia de identidad, dado que es muy probable que su contenido cambie con el tiempo con respecto a los participantes activos declarados en las directivas y también, posiblemente, en algunas situaciones con respecto a los términos y condiciones para ser un participante.  
La configuración y el mantenimiento de la federación se simplifican enormemente al proteger a los usuarios de confianza de las reconfiguraciones de confianza saliente y conectividad a medida que diferentes proveedores o comprobadores de notificaciones se unen a la comunidad representada por el conjunto de directivas, o la dejan.

La interoperabilidad es otro desafío importante dada la necesidad de integrar proveedores o comprobadores de notificaciones adicionales, puesto que no resulta probable que los usuarios de confianza admitan todos los protocolos necesarios. Las directivas personalizadas de Azure AD B2C que aprovechan el marco de experiencia de identidad resuelven este problema al admitir protocolos estándar del sector y aplicar recorridos de usuario específicos para trasladar solicitudes cuando los usuarios de confianza y los proveedores de atributos no admiten el mismo protocolo.  

Los recorridos de usuario incluyen perfiles de protocolo y metadatos que se usarán para asociar la interoperabilidad en la red entre los usuarios de confianza y otros participantes.  También existen reglas de tiempo de ejecución operativas que se aplicarán a los mensajes de solicitud y respuesta de intercambio de información de identidad para exigir el cumplimiento de las directivas publicadas como parte de la especificación de TF. La idea de recorridos de usuario resulta clave para la personalización de la experiencia del cliente y arroja luz sobre cómo funciona el sistema en el nivel de protocolo.

Sobre esa base, las aplicaciones y portales de usuarios de confianza pueden invocar, dependiendo del contexto, directivas personalizadas de Azure AD B2C que aprovechan el marco de experiencia de identidad pasando el nombre de una directiva específica y obtener de forma exacta el comportamiento y el intercambio de información que desean de manera sencilla, organizada y sin riesgo.

