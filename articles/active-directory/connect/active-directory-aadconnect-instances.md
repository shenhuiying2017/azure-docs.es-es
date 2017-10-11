---
title: "Azure AD Connect: instancias de servicio de sincronización | Microsoft Docs"
description: "Esta página documenta las consideraciones especiales para instancias de Azure AD."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: e8321c3d16253226a5931cacbce6fa5d50b697bd
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: consideraciones especiales para instancias
Azure AD Connect se utiliza habitualmente con la instancia mundial de Azure AD y Office 365. Pero también hay otras instancias y estas tienen requisitos diferentes para las direcciones URL y otras consideraciones especiales.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
[Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) es una nube soberana operada por un administrador de datos alemán.

| Direcciones URL para abrir en el servidor proxy |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +Listas de revocación de certificados |

Al iniciar sesión en el inquilino de Azure AD tiene que usar una cuenta en el dominio onmicrosoft.de.

Características que actualmente no están presentes en Microsoft Cloud Germany:

* **Azure AD Connect Health** no está disponible.
* Las **actualizaciones automáticas** no están disponibles.
* La **escritura diferida de contraseñas** está disponible en versión preliminar con Azure AD Connect versión 1.1.570.0 y posteriores.
* Otros servicios de Azure AD Premium no están disponibles.

## <a name="microsoft-azure-government-cloud"></a>Microsoft Azure Government Cloud
[Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/) es una nube para el gobierno de Estados Unidos.

Esta nube ha sido compatible con versiones anteriores de DirSync. A partir de la compilación 1.1.180 de Azure AD Connect la próxima generación de la nube es compatible. Esta generación utiliza solo puntos de conexión basados en EE. UU. y tiene una lista de direcciones URL diferentes para abrir en el servidor proxy.

| Direcciones URL para abrir en el servidor proxy |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.gov.us.microsoftonline.com |
| +Listas de revocación de certificados |

Azure AD Connect no puede detectar automáticamente que el inquilino de Azure AD se encuentra en la nube de administración pública. En su lugar, tiene realizar las siguientes acciones al instalar Azure AD Connect.

1. Inicie la instalación de Azure AD Connect.
2. Cuando vea la primera página en donde se supone que tiene que aceptar el CLUF, no continúe y deje que el Asistente para instalación se continúe ejecutando.
3. Inicie regedit y cambie la clave del registro `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` al valor `2`.
4. Vuelva al Asistente para instalación de Azure AD Connect, acepte el CLUF y continúe. Durante la instalación, asegúrese de usar la ruta de acceso de instalación de **configuración personalizada** (y no la instalación rápida). Luego continúe la instalación normalmente.

Características que actualmente no están presentes en Microsoft Azure Government Cloud:

* **Azure AD Connect Health** no está disponible.
* Las **actualizaciones automáticas** no están disponibles.
* La **escritura diferida de contraseñas** está disponible en versión preliminar con Azure AD Connect versión 1.1.570.0 y posteriores.
* Otros servicios de Azure AD Premium no están disponibles.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
