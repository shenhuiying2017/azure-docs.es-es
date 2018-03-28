---
title: Guía de administración del complemento de inicio de sesión único de Microsoft Azure Active Directory | Microsoft Docs
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Microsoft Azure Active Directory single sign-on for JIRA.
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
ms.openlocfilehash: af949d1db8af37a534a16364f9f0763479c436e4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-admin-guide"></a>Guía de administración del complemento de inicio de sesión único de Microsoft Azure Active Directory

## <a name="table-of-contents"></a>Tabla de contenido

1. **[INFORMACIÓN GENERAL](#overview)**
2. **[CÓMO FUNCIONA](#how-it-works)**
3. **[PÚBLICO](#audience)**
4. **[SUPUESTOS](#assumptions)**
5. **[REQUISITOS PREVIOS](#prerequisites)**
6. **[VERSIONES COMPATIBLES DE JIRA Y CONFLUENCE](#supported-versions-of-jira-and-confluence)**
7. **[INSTALACIÓN](#installation)**
8. **[CONFIGURACIÓN DEL COMPLEMENTO](#plugin-configuration)**
9. **[EXPLICACIÓN DE LOS CAMPOS DE LA PANTALLA DE CONFIGURACIÓN DEL COMPLEMENTO:](#field-explanation-for-add---on-configuration-screen:)**
10. **[SOLUCIÓN DE PROBLEMAS](#troubleshooting)**

## <a name="overview"></a>Información general

Estos complementos permiten que los clientes de Microsoft Azure AD usen el nombre de usuario y la contraseña de su organización para iniciar sesión en los productos basados en Atlassian Jira y Confluence Server. Se implementa el SSO basado en SAML 2.0.

## <a name="how-it-works"></a>Cómo funciona

Cuando los usuarios desean iniciar sesión en la aplicación Confluence o Atlassian Jira, verán el botón **Login with Azure AD** (Iniciar sesión con Azure AD) en la página de inicio de sesión. Cuando hagan clic en el botón, se les pedirá iniciar sesión con la página de inicio de sesión de Azure AD de la organización.

Una vez que los usuarios se autentican, deberían poder iniciar sesión en la aplicación. Si ya se autenticaron con la contraseña y el identificador de la organización, inician sesión directamente en la aplicación. Además, tenga en cuenta que el inicio de sesión funciona en JIRA y en Confluence. Si los usuarios iniciaron sesión en la aplicación JIRA y Confluence también está abierto en la misma ventana del explorador, necesitan iniciar sesión una vez y no es necesario que vuelvan a proporcionar las credenciales para otra aplicación. Los usuarios también pueden acceder al producto Atlassian a través de myapps en la cuenta de Azure y deberían iniciar sesión sin que se les pidan las credenciales.

> [!NOTE]
> El aprovisionamiento de usuarios no se realiza con este complemento.

## <a name="audience"></a>Público

Administradores de JIRA y Confluence que planean usar este complemento para habilitar SSO mediante Azure AD.

## <a name="assumptions"></a>Supuestos

* La instancia de JIRA/Confluence está habilitada para HTTPS.
* Los usuarios ya se crearon en JIRA/Confluence.
* Los usuarios tiene roles asignados en JIRA/Confluence.
* Los administradores tienen acceso a la información necesaria para configurar el complemento.
* JIRA/Confluence también están disponibles fuera de la red de la empresa.
* El complemento solo funciona con la versión local de JIRA y Confluence.

## <a name="prerequisites"></a>requisitos previos

Tenga en cuenta los requisitos previos siguientes antes de continuar con la instalación del complemento:

* JIRA/Confluence están instalados en una versión de Windows de 64 bits.
* Las versiones de JIRA/Confluence están habilitadas para HTTPS.
* Observe la versión compatible del complemento en la sección "Versiones compatibles" que aparece más adelante.
* JIRA/Confluence están disponibles en Internet.
* Credenciales de administrador para JIRA/Confluence.
* Credenciales de administrador para Azure AD.
* WebSudo debe estar deshabilitado en JIRA y Confluence.

## <a name="supported-versions-of-jira-and-confluence"></a>Versiones compatibles de JIRA y Confluence.

En la actualidad, se admiten las versiones siguientes de JIRA y Confluence:

* Núcleo y software de JIRA: de la 6.0 a la 7.2.0
* Departamento de servicios de JIRA: de la 3.0 a la 3.2
* Confluence: de la versión 5.0 a la 5.10

## <a name="installation"></a>Instalación

El administrador debe seguir estos pasos para instalar el complemento:

1. Inicie sesión en la instancia de JIRA/Confluence como administrador.
    
2. Vaya a la administración de JIRA/Confluence y haga clic en Complementos.
    
3. En Atlassian Marketplace, busque **Microsoft SAML SSO Plugin** (Complemento de SSO de Microsoft SAML).
 
4. En la búsqueda aparece la versión correspondiente del complemento.
 
5. Seleccione el complemento y UPM lo instala.
 
6. Una vez instalado el complemento, aparece en la sección de complementos Instalados por el usuario de Administrar complemento.
 
7. Debe configurar el complemento antes de empezar a usarlo.
 
8. Haga clic en el complemento para ver un botón de configuración.
 
9. Haga clic en el botón para proporcionar entradas de configuración.
    
## <a name="plugin-configuration"></a>Configuración del complemento

En la imagen siguiente se muestra la pantalla de configuración del complemento tanto en JIRA como en Confluence.
    
![configuración del complemento](./media/ms-confluence-jira-plugin-adminguide/jira.png)

### <a name="field-explanation-for-add-on-configuration-screen"></a>Explicación de los campos de la pantalla de configuración del complemento:

1.   Metadata URL (Dirección URL de metadatos): dirección URL para obtener los metadatos de federación de Azure AD.
 
2.   Identifier (Identificador): Azure AD usa este campo para validar el origen de la solicitud. Se asigna al elemento Identificador de Azure AD. El complemento lo deriva automáticamente como https://<dominio:puerto>/
 
3.   Reply URL (Dirección URL de respuesta): use este campo en el IdP para iniciar el inicio de sesión de SAML. Se asigna al elemento Dirección URL de respuesta de Azure AD. El complemento lo deriva automáticamente como https://<dominio:puerto>/plugins/servlet/saml/auth
 
4.   Sign On URL (Dirección URL de inicio de sesión): use este campo en el IdP para iniciar el inicio de sesión de SAML. Se asigna al elemento Inicio de sesión de Azure AD. El complemento lo deriva automáticamente como https://<dominio:puerto>/plugins/servlet/saml/auth
 
5.   IdP Entity ID (Identificador de la entidad de IdP): el identificador de entidad que el IdP usa. Este campo se rellena cuando se resuelve la dirección URL de metadatos.
 
6.   Login URL (Dirección URL de inicio de sesión): la dirección URL de inicio de sesión del IdP. Este campo se rellena desde Azure AD cuando se resuelve la dirección URL de metadatos.
 
7.   Log out URL (Dirección URL de cierre de sesión): la dirección URL de cierre de sesión del IdP. Este campo se rellena desde Azure AD cuando se resuelve la dirección URL de metadatos.
 
8.   X.509 Certificate (Certificado X.509): el certificado X.509 del IdP. Este campo se rellena desde Azure AD cuando se resuelve la dirección URL de metadatos.
 
9.   Login Button Name (Nombre del botón de inicio de sesión): nombre del botón de inicio de sesión que su organización desea ver. Los usuarios ven este texto en el botón de inicio de sesión de la pantalla de inicio de sesión.
 
10.   SAML User ID Locations (Ubicaciones del identificador de usuario de SAML): ubicaciones en que se espera el identificador del usuario en la respuesta de SAML. Puede estar en NameID o en un nombre de atributo personalizado. Este identificador debe ser el identificador de usuario de JIRA/Confluence.
 
11.   Attribute name (Nombre de atributo): nombre del atributo donde se puede esperar el identificador del usuario.
 
12.   Enable Home Realm Discovery (Habilitar la detección del dominio de inicio): active esta marca si la empresa usa el inicio de sesión basado en ADFS.
 
13.   Domain Name (Nombre de dominio): escriba aquí el nombre del dominio en el caso de inicio de sesión basado en ADFS.
 
14.   Enable Single Sign out (Habilitar cierre de sesión único): active esta marca si desea cerrar sesión en Azure AD cuando un usuario cierra la sesión en JIRA/Confluence.

### <a name="troubleshooting"></a>solución de problemas

* Si recibe varios errores de certificados
    
    * Inicie sesión en Azure AD y quite los distintos certificados disponibles en la aplicación. Asegúrese de que haya solo un certificado.

* El certificado está a punto de expirar en Azure AD.
    
    * Los complementos se encargan de sustituir automáticamente el certificado. Cuando hay un certificado a punto de expirar, se debe marcar como activo un certificado nuevo y el certificado no usado se debe eliminar. Cuando un usuario intenta iniciar sesión en JIRA en este escenario, el complemento captura el certificado nuevo y lo guarda.

* Cómo deshabilitar WebSudo (deshabilitar la sesión segura del administrador)
    
    * JIRA: las sesiones seguras del administrador (es decir, con confirmación de contraseña antes de acceder a las funciones de administración) están habilitadas de manera predeterminada. Si desea deshabilitar esta característica en la instancia de JIRA, puede hacerlo si especifica la línea siguiente en el archivo jira-config.properties: "ira.websudo.is.disabled = true".
    
    * Confluence: siga los pasos que se indican en la dirección URL siguiente https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html

* Los campos que la dirección URL de metadatos debería rellenar no se están rellenando
    
    * Compruebe que la dirección URL sea la correcta. Revise si asignó el inquilino y el identificador de aplicación correspondientes.
    
    * Visite la dirección URL en el explorador y vea si recibe el archivo XML de metadatos de federación.

* Error interno del servidor:
    
    * Recorra los registros que se encuentran en el directorio de registros de la instalación. Si recibe el error cuando el usuario intenta iniciar sesión mediante el SSO de Azure AD, puede compartir los registros con el soporte técnico en la información que se proporciona más adelante en este documento.

* No se encuentra el identificador de usuario cuando el usuario intenta iniciar sesión
    
    * El usuario no está creado en JIRA/Confluence, por lo que debe crearlo.

* No se encuentra la aplicación en Azure AD
    
    * Vea si la dirección URL correspondiente está asignada a la aplicación en Azure AD.

* Detalles de soporte técnico: póngase en contacto con nosotros en: [equipo de integración del SSO de Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Le responderemos dentro de las próximas 24 a 48 horas hábiles.
    
    * También puede generar una incidencia de soporte técnico con Microsoft a través del canal de Azure Portal.