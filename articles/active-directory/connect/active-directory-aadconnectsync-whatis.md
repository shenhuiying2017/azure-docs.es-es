---
title: "Sincronización de Azure AD Connect: descripción y personalización de la sincronización | Microsoft Docs"
description: "Se explica cómo funciona la sincronización de Azure AD Connect y cómo personalizarla."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.openlocfilehash: 20614aaabc05513e93eb001c1676a73f85003947
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Sincronización de Azure AD Connect: comprender y personalizar la sincronización
Los servicios de sincronización de Azure Active Directory Connect (sincronización de Azure AD Connect) es un componente principal de Azure AD Connect. Se encargan de todas las operaciones relacionadas con la sincronización de datos de identidad entre el entorno local y Azure AD. Sincronización de Azure AD Connect es el sucesor de DirSync, Sincronización de Azure AD y Forefront Identity Manager con Conector Azure Active Directory configurado.

Este tema es la principal referencia sobre la **sincronización de Azure AD Connect** (que también se conoce como **motor de sincronización**) y en el que se muestran vínculos a todos los otros demás temas relacionados con ella. Para obtener vínculos para Azure AD Connect, consulte [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

El servicio de sincronización consta de dos componentes, el componente de **sincronización de Azure AD Connect** local y el lado del servicio en Azure AD llamado **servicio de sincronización de Azure AD Connect**. 

## <a name="azure-ad-connect-sync-topics"></a>Temas de sincronización de Azure AD Connect
| Tema. | ¿Qué aspectos cubre y cuándo debe leerlo? |
| --- | --- |
| **Fundamentos de la sincronización de Azure AD Connect** | |
| [Comprensión de la arquitectura](active-directory-aadconnectsync-understanding-architecture.md) |Para quienes no estén familiarizados con el motor de sincronización y quieran obtener información sobre la arquitectura y los términos empleados. |
| [Conceptos técnicos](active-directory-aadconnectsync-technical-concepts.md) |Una versión abreviada del tema de la arquitectura que explica brevemente los términos empleados. |
| [Topologías de Azure AD Connect](active-directory-aadconnect-topologies.md) |Describe las distintas topologías y escenarios que admite el motor de sincronización. |
| **Configuración personalizada** | |
| [Sincronización de Azure AD Connect: ejecución por segunda vez del Asistente para la instalación](active-directory-aadconnectsync-installation-wizard.md) |Explica qué opciones tiene disponibles si ejecuta de nuevo el Asistente para instalación de Azure AD Connect. |
| [Conocimiento del aprovisionamiento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md) |Describe el modelo de configuración denominado aprovisionamiento declarativo. |
| [Explicación de las expresiones declarativas de aprovisionamiento](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |Describe la sintaxis para el lenguaje de expresiones que se usa en el aprovisionamiento declarativo. |
| [Introducción a la configuración predeterminada](active-directory-aadconnectsync-understanding-default-configuration.md) |Describe las reglas no incluidas y la configuración predeterminada. También describe cómo colaboran las reglas para que los escenarios no incluidos funcionen. |
| [Descripción de usuarios y contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md) |Continúa desde el tema anterior y describe cómo la configuración de los usuarios y contactos funciona conjuntamente, especialmente en un entorno de bosques múltiples. |
| [How to make a change to the default configuration (Realización de un cambio en la configuración predeterminada)](active-directory-aadconnectsync-change-the-configuration.md) |En él se explica cómo cambiar una configuración común para los flujos de atributo. |
| [Prácticas recomendadas de cambio de la configuración predeterminada](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |En él se describen las limitaciones de compatibilidad y se proporcionan directrices para realizar cambios en la configuración de fábrica. |
| [Configuración del filtrado](active-directory-aadconnectsync-configure-filtering.md) |Describe las distintas opciones para limitar qué objetos se sincronizan con Azure AD y cómo configurar estas opciones paso a paso. |
| **Características y escenarios** | |
| [Evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |En él se describe la característica para *evitar eliminaciones accidentales* y se explica cómo configurarla. |
| [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) |En él se describe el programador integrado que permite importar, sincronizar y exportar datos. |
| [Implementación de la sincronización de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md) |Describe cómo funciona la sincronización de contraseñas, cómo implementarla y cómo utilizarla y solucionar problemas. |
| [Escritura diferida de dispositivos](active-directory-aadconnect-feature-device-writeback.md) |Describe cómo funciona la reescritura de dispositivos en Azure AD Connect. |
| [Extensiones de directorio](active-directory-aadconnectsync-feature-directory-extensions.md) |Describe cómo extender el esquema de Azure AD con sus propios atributos personalizados. |
| **Servicio de sincronización** | |
| [Características del servicio de sincronización de Azure AD Connect](active-directory-aadconnectsyncservice-features.md) |Describe el lado del servicio de sincronización y cómo cambiar la configuración de sincronización de Azure AD. |
| [Resistencia de atributos duplicados](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) |En él se describe cómo habilitar y usar la resistencia de valores de atributos duplicados de **userPrincipalName** y **proxyAddresses**. |
| **Operaciones e interfaz de usuario** | |
| [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) |En él se describe la interfaz de usuario de Synchronization Service Manager, incluidas las pestañas [Operations](active-directory-aadconnectsync-service-manager-ui-operations.md) (Operaciones), [Connectors](active-directory-aadconnectsync-service-manager-ui-connectors.md) (Conectores), [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) (Diseñador de metaverso) y [Metaverse Search](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) (Búsqueda de metaverso). |
| [Tareas y consideraciones operativas](active-directory-aadconnectsync-operations.md) |Describe las preocupaciones operativas, como la recuperación ante desastres. |
| **Procedimiento para:** | |
| [Restablecer la cuenta de Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md) |Procedimiento para restablecer las credenciales de la cuenta de servicio usada para conectarse desde el servicio de sincronización de Azure AD Connect a Azure AD. |
| **Más información y referencias** | |
| [Puertos](active-directory-aadconnect-ports.md) |Enumera los puertos que necesita abrir entre el motor de sincronización y los directorios locales y Azure AD. |
| [Atributos sincronizados con Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) |Enumera todos los atributos que se sincronizan entre los AD locales y Azure AD. |
| [Referencia de funciones](active-directory-aadconnectsync-functions-reference.md) |Enumera todas las funciones disponibles en el aprovisionamiento declarativo. |

## <a name="additional-resources"></a>Recursos adicionales
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

