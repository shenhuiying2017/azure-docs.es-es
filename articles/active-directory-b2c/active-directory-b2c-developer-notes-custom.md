---
title: 'Azure Active Directory B2C: notas del desarrollador acerca del uso de directivas personalizadas|Microsoft Docs'
description: "Notas para los desarrolladores sobre configuración y mantenimiento de Azure AD B2C con directivas personalizadas"
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
ms.date: 05/05/2017
ms.author: joroja
ms.openlocfilehash: a5f222e5b11e05286152a9f1cc55d2c3fc27a9dc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Notas de la versión preliminar de la directiva personalizada de Azure Active Directory B2C
El conjunto de características de la directiva personalizada está disponible para su evaluación en versión preliminar pública para todos los clientes de Azure Active Directory B2C (Azure AD B2C). Este conjunto de características está destinado a desarrolladores de identidades avanzados que crean las soluciones de identidad más complejas.  

En la actualidad, este conjunto de características requiere que los desarrolladores configuren el marco de experiencia de identidad directamente a través de la edición de archivos XML. Este método de configuración es eficaz y complejo. Los desarrolladores de identidades avanzados que usen el marco de experiencia de identidad deben planear invertir un tiempo en completar los tutoriales y leer los documentos de referencia. 

## <a name="features-included-in-this-public-preview"></a>Características que se incluyen en la versión preliminar pública
Con las nuevas características introducidas en la versión preliminar pública los desarrolladores pueden realizar las siguientes tareas:<br>

* Crear y cargar recorridos del usuario de autenticación personalizada mediante directivas personalizadas. 
   * Describir los recorridos del usuario paso a paso como intercambios entre proveedores de notificaciones. 
   * Definir la creación de ramas condicional en recorridos del usuario. 
* Integrar servicios con la API de REST habilitada en los recorridos del usuario de autenticación personalizada.  
* Agregar federación con proveedores de identidades compatibles con el estándar de OpenIDConnect. <br>
* Agregar federación con proveedores de identidades que cumplen el protocolo SAML 2.0. 

## <a name="terms-of-the-public-preview"></a>Términos de la versión preliminar pública

* Se aconseja usar las nuevas características solo con fines de evaluación.<br>
* Las nuevas características no están pensadas para que se usen en entornos de producción.<br>
* Los Acuerdos de Nivel de Servicio (SLA) no se aplican a las nuevas características. <br>
* Las solicitudes de soporte técnico pueden enviarse a través de los canales de soporte técnico habituales. <br>
* No se puede garantizar ninguna fecha de disponibilidad general.<br>
* Microsoft se reserva el derecho, a su entera discreción y por cualquier motivo, de marcar y rechazar o restringir aquellos escenarios y recorridos del usuario que superen el ámbito de la carta de producto de Azure AD B2C para actuar como plataforma de administración de acceso e identidad de clientes (CIAM).

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilidades de los desarrolladores de conjunto de características de directivas personalizadas
La configuración manual de directivas concede un acceso de nivel inferior a la plataforma subyacente de Azure AD B2C y da como resultado la creación de un marco de confianza único y totalmente personalizable. Las posibles permutaciones de proveedores de identidades personalizados, las relaciones de confianza, las integraciones con servicios externos y los flujos de trabajo paso a paso exigen más a los desarrolladores avanzados que los utilizan.

Para aprovechar de forma completa la versión preliminar pública, se recomienda que los desarrolladores que utilizan el conjunto de características de directivas personalizadas cumplan las directrices siguientes:
* Conocer el lenguaje de configuración de Identity Experience Engine y la administración de claves/secretos.
* Tomar posesión de escenarios e integraciones personalizadas.
* Realizar pruebas metódicas de los escenarios.
* Seguir los procedimientos recomendados de desarrollo de software y almacenamiento provisional con un mínimo de un entorno de desarrollo y prueba, y un entorno de producción.
* Mantenerse informado sobre los nuevos desarrollos de los proveedores de identidad y los servicios con los que está integrado. Por ejemplo, realizar un seguimiento de cambios en los secretos y los cambios programados y no programados en el servicio.
* Configurar la supervisión activa y supervisar la capacidad de respuesta de los entornos de producción.
* Mantener actualizadas las direcciones de correo electrónico de contacto y responder a los correos mensajes de correo electrónico del equipo del sitio activo de Microsoft.
* Realizar las acciones pertinentes cuando se lo indique el equipo del sitio activo de Microsoft. 


>[!NOTE]
>Estas características pueden llegar a incluirse en las directivas integradas de Azure AD, lo que hace que todos los desarrolladores puedan acceder más fácilmente a ellas.

## <a name="next-steps"></a>Pasos siguientes
[Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).
