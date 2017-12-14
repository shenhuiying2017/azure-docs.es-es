---
title: 'Azure Active Directory B2C: notas del desarrollador acerca del uso de directivas personalizadas|Microsoft Docs'
description: "Notas para los desarrolladores sobre configuración y mantenimiento de Azure AD B2C con directivas personalizadas"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/13/2017
ms.author: joroja
ms.openlocfilehash: 064ccec58406e08ae68320b59fcf2a96952a41ee
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
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
* Conocer el lenguaje de configuración de Identity Experience Framework y la administración de claves y secretos.
* Tomar posesión de escenarios e integraciones personalizadas.
* Realizar pruebas metódicas de los escenarios.
* Seguir los procedimientos recomendados de desarrollo de software y almacenamiento provisional con un mínimo de un entorno de desarrollo y prueba, y un entorno de producción.
* Mantenerse informado sobre los nuevos desarrollos de los proveedores de identidad y los servicios con los que está integrado. Por ejemplo, realizar un seguimiento de cambios en los secretos y los cambios programados y no programados en el servicio.
* Configurar la supervisión activa y supervisar la capacidad de respuesta de los entornos de producción.
* Mantenga actualizadas las direcciones de correo electrónico de contacto y responda a los correos mensajes de correo electrónico del equipo de Microsoft.
* Realizar las acciones pertinentes cuando se lo indique el equipo del sitio activo de Microsoft. 

## <a name="features-by-stage-and-known-issues"></a>Características por fase y problemas conocidos
Las directiva personalizadas y las funcionalidades de Identity Experience Framework están en desarrollo constante y rápido.  Esta tabla es un índice de disponibilidad de características y componentes.

Publique preguntas en Stack Overflow en [aka.ms/aadb2cso](http://aka.ms/aadb2cso)


### <a name="identity-providers-tokens-protocols"></a>Proveedores de identidades, Tokens, Protocolos
Interfaces con aplicaciones y componentes externos

| Característica | Desarrollo | Vista previa | GA | Notas |
|---------------------------------------------|-------------|---------|----|-------|
| IDP-OpenIDConnect |  | x |  | por ejemplo, Google+ |
| IDP-OAUTH2 |  | x |  | por ejemplo, Facebook  |
| IDP-OAUTH1 |  | x |  | por ejemplo, Twitter |
| IDP-SAML |  | x |  | por ejemplo, Salesforce, ADFS |
| IDP-WSFED | x |  |  |  |
| Usuario de confianza OAUTH |  | x |  |  |
| Usuario de confianza OIDC |  | x |  |  |
| Usuario de confianza SAML | x |  |  |  |
| Usuario de confianza WSFED | x |  |  |  |
| API de REST con autenticación básica y de certificado |  | x |  | por ejemplo, Azure Functions |


### <a name="component-support"></a>Compatibilidad de componentes


| Característica | Desarrollo | Vista previa | GA | Notas |
|-------------------------------------------|-------------|---------|----|-------|
| Autenticación multifactor de Azure |  | x |  |  |
| Azure Active Directory como directorio local |  | x |  |  |
| Subsistema de correo electrónico de Azure para 2FA |  | x |  |  |
| Compatibilidad con varios lenguajes|  | x |  |  |
| Complejidad de la contraseña | x |  |  |  |


### <a name="content-definition"></a>Definición de contenido

| Característica | Desarrollo | Vista previa | GA | Notas |
|-----------------------------------------------------------------------------|-------------|---------|----|-------|
|   Página de error, api.error |  | x |  |  |
|   Página de selección de IDP, api.idpselections |  | x |  |  |
|   Selección de IDP para suscribirse, api.idpselections.signup |  | x |  |  |
|   Ha olvidado la contraseña, api.localaccountpasswordreset |  | x |  |  |
|   Inicio de sesión en cuenta local, api.localaccountsignin |  | x |  |  |
|   Registro en cuenta local, api.localaccountsignup |  | x |  |  |
|   Página de MFA, api.phonefactor |  | x |  |  |
|   Autoaserción: por ejemplo registro en cuenta social, api.selfasserted |  | x |  |  |
|   Actualización de perfil de autoaserción, api.selfasserted.profileupdate |  | x |  |  |
|   Página unificada de registro o inicio de sesión, api.signuporsignin |  | x |  |  |


### <a name="app-ief-integration"></a>Integración de aplicaciones IEF
| Característica | Desarrollo | Vista previa | GA | Notas |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| Parámetro de cadena de consulta id_token_hint | x |  |  |  |
| Parámetro de cadena de consulta domain_hint |  | x |  | disponible como notificación, puede pasarse al IDP |
| Parámetro de cadena de consulta login_hint |  | x |  | disponible como notificación, puede pasarse al IDP |
| Insertar JSON en UserJourney a través de client_assertion | x |  |  | en desuso |
| Insertar JSON en UserJourney como id_token_hint | x |  |  | enfoque de avance para pasar a JSON |


### <a name="session-management"></a>Administración de sesiones

| Característica | Desarrollo | Vista previa | GA | Notas |
|---------------------------------|-------------|---------|----|-------|
| Proveedor de sesión SSO |  | x |  |  |
| Proveedor de sesión de inicio de sesión externa |  | x |  |  |
| Proveedor de sesión de SSO de SAML |  | x |  |  |


### <a name="security"></a>Seguridad
| Característica | Desarrollo | Vista previa | GA | Notas |
|---------------------------------------------|-------------|---------|----|-------|
| Claves de directivas: generar, manual, carga |  | x |  |  |
| Claves de directivas: RSA/Certificado, secretos |  | x |  |  |


### <a name="developer-interface"></a>Interfaz del desarrollador
| Característica | Desarrollo | Vista previa | GA | Notas |
|---------------------------------------------|-------------|---------|----|-------|
| Azure Portal-IEF UX |  | x |  |  |
| Registros de UserJourney de Application Insights  |  | x |  |  |
| Registros de eventos de Application Insights |x|  |  |  |



## <a name="next-steps"></a>Pasos siguientes
[Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md).
