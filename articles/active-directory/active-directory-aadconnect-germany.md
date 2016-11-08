---
title: Azure AD Connect en Microsoft Cloud Germany
description: Azure AD Connect integrará sus directorios locales con Azure Active Directory. Esto le permite proporcionar una identidad común para las aplicaciones de Office 365, Azure y SaaS integradas con Azure AD.
keywords: introducción a Azure AD Connect, información general de Azure AD Connect, qué es Azure AD Connect, instalación de active directory, Alemania, Selva Negra
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/08/2016
ms.author: billmath

---
# Azure AD Connect en Microsoft Cloud Germany- Versión preliminar pública
## Introducción
Azure AD Connect proporciona sincronización entre instancias de Active Directory y Azure Active Directory locales. Actualmente, muchos de los escenarios en [Microsoft Cloud Germany](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) debe realizarlos el operador. Al usar Microsoft Cloud Germany, debe tener en cuenta lo siguiente:

* Las direcciones URL siguientes deben estar abiertas en un servidor proxy para que tenga lugar correctamente la sincronización:
  
  * *. microsoftonline.de
  * *.windows.net
  * * Listas de revocación de certificados
* Al iniciar sesión en el directorio de Azure AD tiene que usar una cuenta en el dominio onmicrosoft.de.
* No están disponibles las características siguientes:
  * Azure AD Connect Health
  * Actualizaciones automáticas
  * Escritura diferida de contraseñas

## Descargar
Puede descargar Azure AD Connect en la hoja Azure AD Connect en el portal. Utilice las instrucciones siguientes para localizar la hoja Azure AD Connect.

### Hoja Azure AD Connect
Cuando haya iniciado sesión en Azure Portal, siga estos pasos:

1. Vaya a Examinar.
2. Seleccione Azure Active Directory.
3. Después, seleccione Azure AD Connect.

Verá lo siguiente:

![Hoja Azure AD Connect](media\\active-directory-aadconnect-germany\\germany1.png)

En la tabla siguiente se describen las características mostradas en la hoja.

| Título | Description |
| --- | --- |
| ESTADO DE SINCRONIZACIÓN |Le permite saber si la sincronización está habilitada o no. |
| ÚLTIMA SINCRONIZACIÓN |La última vez que se realizó correctamente una sincronización. |
| DOMINIOS FEDERADOS |Muestra el número de dominios federados configurados. |

## Instalación
Para instalar Azure AD Connect, puede utilizar [esta](active-directory-aadconnect.md#install-azure-ad-connect) documentación.

## Características avanzadas e información adicional
Para obtener información adicional e instrucciones sobre la configuración personalizada o configuraciones avanzadas, empiece con la [integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md). Esta página proporciona información y vínculos a información adicional.

<!---HONumber=AcomDC_0914_2016-->