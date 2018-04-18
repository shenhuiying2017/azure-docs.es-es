---
title: Guía de administración para el complemento SSO de Azure Active Directory | Microsoft Docs
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Jira/Confluence.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: d34ff6021816c73fb064a3ce73b7fcf3ae22dbd1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="admin-guide-for-the-azure-active-directory-sso-plug-in"></a>Guía de administración para el complemento SSO de Azure Active Directory

## <a name="overview"></a>Información general

El complemento de inicio de sesión único (SSO) de Azure Active Directory (Azure AD) permite a los clientes de Microsoft Azure AD usar su cuenta profesional o educativa para iniciar sesión en Atlassian Jira y en los productos basados en Confluence Server. Implementa el SSO basado en SAML 2.0.

## <a name="how-it-works"></a>Cómo funciona

Cuando los usuarios deseen iniciar sesión en la aplicación Confluence o Atlassian Jira, verán el botón **Login with Azure AD** (Iniciar sesión con Azure AD) en la página de inicio de sesión. Cuando se selecciona, deben iniciar sesión mediante la página de inicio de sesión de la organización de Azure AD (es decir, su cuenta profesional o educativa).

Una vez que los usuarios se autentican, deberían poder iniciar sesión en la aplicación. Si ya se han autenticado con el identificador y la contraseña de su cuenta profesional o educativa, inician sesión directamente en la aplicación. 

El inicio de sesión funciona en Jira y en Confluence. Si los usuarios iniciaron sesión en la aplicación Jira y Confluence también está abierto en la misma ventana del explorador, no tienen que proporcionar las credenciales para la otra aplicación. 

Los usuarios también pueden obtener los productos de Atlassian a través de Mis aplicaciones en la cuenta profesional o educativa. Deberían iniciar sesión sin que se les pidan las credenciales.

> [!NOTE]
> El aprovisionamiento de usuarios no se realiza con este complemento.

## <a name="audience"></a>Público

Los administradores de Jira y Confluence pueden usar el complemento para habilitar SSO con Azure AD.

## <a name="assumptions"></a>Supuestos

* Las instancias de Jira y Confluence están habilitadas para HTTPS.
* Los usuarios ya se crearon en Jira o Confluence.
* Los usuarios tienen roles asignados en Jira o Confluence.
* Los administradores tienen acceso a la información necesaria para configurar el complemento.
* Jira o Confluence también están disponibles fuera de la red de la empresa.
* El complemento funciona solo con las versiones locales de Jira y Confluence.

## <a name="prerequisites"></a>requisitos previos

Antes de instalar el complemento, tenga en cuenta la siguiente información:

* Jira y Confluence están instalados en una versión de Windows de 64 bits.
* Las versiones de Jira y Confluence están habilitadas para HTTPS.
* Jira y Confluence están disponibles en Internet.
* Las credenciales de administrador están en vigor para Jira y Confluence.
* Las credenciales de administrador están en vigor para Azure AD.
* WebSudo está deshabilitado en Jira y Confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Versiones admitidas de Jira y Confluence

El complemento admite las versiones siguientes en Jira y Confluence:

* Jira Core y Software: de la versión 6.0 a la 7.2.0
* Jira Service Desk: de la versión 3.0 a la 3.2
* Confluence: de la versión 5.0 a la 5.10

## <a name="installation"></a>Instalación

Para instalar el complemento, siga estos pasos:

1. Inicie sesión en la instancia de Jira o Confluence como administrador.
    
2. Vaya a la consola de administración de Jira o Confluence y seleccione **Add-ons** (Complementos).
    
3. En Atlassian Marketplace, busque **Microsoft SAML SSO Plugin** (Complemento de SSO de Microsoft SAML).
 
   La versión adecuada del complemento aparece en los resultados de búsqueda.
 
5. Seleccione el complemento y el Administrador de complementos universal (UPM) lo instala.
 
Una vez instalado el complemento, aparece en la sección **Complementos instalados por el usuario** de **Administrar complemento**.
    
## <a name="plug-in-configuration"></a>Configuración de complementos

Para empezar a usar el complemento, debe configurarlo. Seleccione el complemento, seleccione el botón **Configurar** y proporcione los detalles de configuración.

En la imagen siguiente se muestra la pantalla de configuración tanto en Jira como en Confluence:
    
![Pantalla de configuración de complementos](./media/ms-confluence-jira-plugin-adminguide/jira.png)

*   **Metadata URL** (Dirección URL de metadatos): dirección URL para obtener los metadatos de federación de Azure AD.
 
*   **Identifiers** (Identificadores): dirección URL que Azure AD usa para validar el origen de la solicitud. Se asigna al elemento **Identificador** de Azure AD. El complemento deduce automáticamente esta dirección URL como https://*<dominio:puerto>*/.
 
*   **Reply URL** (Dirección URL de respuesta): la dirección URL de respuesta en el proveedor de identidades (IdP) que inicia el inicio de sesión de SAML. Se asigna al elemento **Dirección URL de respuesta** de Azure AD. El complemento deduce automáticamente esta dirección URL como https://*<dominio:puerto>*/plugins/servlet/saml/auth.
 
*   **Sign On URL** (Dirección URL de inicio de sesión): la dirección URL de inicio de sesión en el IdP que inicia el inicio de sesión de SAML. Se asigna al elemento **Inicio de sesión** de Azure AD. El complemento deduce automáticamente esta dirección URL como https://*<dominio:puerto>*/plugins/servlet/saml/auth.
 
*   **IdP Entity ID** (Identificador de la entidad de IdP): el identificador de entidad que el IdP usa. Este campo se rellena cuando se resuelve la dirección URL de metadatos.
 
*   **Login URL** (Dirección URL de inicio de sesión): la dirección URL de inicio de sesión del IdP. Este cuadro se rellena desde Azure AD cuando se resuelve la dirección URL de metadatos.
 
*   **Log out URL** (Dirección URL de cierre de sesión): la dirección URL de cierre de sesión del IdP. Este cuadro se rellena desde Azure AD cuando se resuelve la dirección URL de metadatos.
 
*   **X.509 Certificate** (Certificado X.509): el certificado X.509 del IdP. Este cuadro se rellena desde Azure AD cuando se resuelve la dirección URL de metadatos.
 
*   **Login Button Name** (Nombre del botón de inicio de sesión): el nombre del botón de inicio de sesión que la organización quiere que los usuarios vean en la página de inicio de sesión.
 
*   **SAML User ID Locations** (Ubicaciones de Id. de usuario de SAML): la ubicación donde se espera el identificador de usuario de Jira o Confluence en la respuesta de SAML. Puede estar en **NameID** o en un nombre de atributo personalizado.
 
*   **Attribute name** (Nombre de atributo): nombre del atributo donde se espera el identificador del usuario.
 
*   **Enable Home Realm Discovery** (Habilitar la detección del territorio principal): la selección que hay que realizar si la compañía usa el inicio de sesión basado en Servicios de federación de Active Directory (AD FS).
 
*   **Domain Name** (Nombre de dominio): el nombre del dominio si el inicio de sesión se basa en AD FS.
 
*   **Enable Single Signout** (Habilitar cierre de sesión único): la selección que hay que realizar si desea cerrar la sesión de Azure AD cuando un usuario cierra la sesión de Jira o Confluence.

## <a name="troubleshooting"></a>solución de problemas

* **Está recibiendo varios errores de certificado**: inicie sesión en Azure AD y quite los múltiples certificados que están disponibles en la aplicación. Asegúrese de que haya solo un certificado.

* **Un certificado está a punto de expirar en Azure AD**: los complementos se encargan de la sustitución automática del certificado. Cuando hay un certificado a punto de expirar, se debe marcar como activo un certificado nuevo y se deben eliminar los certificados sin usar. Cuando un usuario intenta iniciar sesión en Jira en este escenario, el complemento captura el certificado nuevo y lo guarda.

* **Desea deshabilitar WebSudo (deshabilitar la sesión segura del administrador)**:
    
  * En Jira, las sesiones seguras del administrador (es decir, con confirmación de contraseña antes de acceder a las funciones de administración) están habilitadas de manera predeterminada. Si desea quitar esta capacidad en la instancia de Jira, especifique la siguiente línea en el archivo jira-config.properties: `ira.websudo.is.disabled = true`
    
  * En Confluence, siga los pasos del [sitio de soporte técnico de Confluence](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html).

* **Los campos que se supone que deberían rellenarse con la dirección URL de metadatos no se están rellenando**:
    
  * Compruebe que la dirección URL sea la correcta. Revise si asignó el inquilino y el identificador de aplicación correctos.
    
  * Escriba la dirección URL en el explorador y vea si recibe el archivo XML de metadatos de federación.

* **Hay un error interno del servidor**: revise los registros en el directorio de registro de la instalación. Si recibe el error cuando el usuario intenta iniciar sesión mediante el SSO de Azure AD, puede compartir los registros con el equipo de soporte técnico.

* **Hay un error "User ID not found" cuando el usuario intenta iniciar sesión**: cree el identificador de usuario en Jira o Confluence.

* **Hay un error "App not found" en Azure AD**: vea si la dirección URL apropiada se asigna a la aplicación en Azure AD.

* **Necesita soporte técnico**: póngase en contacto con el [equipo de integración de SSO de Azure AD ](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). El equipo responde en un plazo de entre 24 y 48 horas laborales.
    
  También puede generar una incidencia de soporte técnico con Microsoft a través del canal de Azure Portal.