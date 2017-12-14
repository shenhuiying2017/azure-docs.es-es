---
title: "Concesión de permisos a una aplicación personalizada | Microsoft Docs"
description: "Aprenda a conceder permisos a una aplicación personalizada mediante el portal de Azure AD o un parámetro de dirección URL."
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 0f13efd5e629bde592a3ab3a3e2db4a22180b5bb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>Concesión de permisos a una aplicación personalizada

Si desea otorgar consentimiento de forma preferente en la aplicación o se produce un error por no otorgar consentimiento a una aplicación, pruebe lo siguiente.

## <a name="how-to-perform-admin-consent-for-your-application"></a>Concesión del consentimiento del administrador en la aplicación

Con este procedimiento, se otorga consentimiento a la aplicación para todos los usuarios de la organización.

1. Acceda a la hoja **Registros de aplicaciones** como **administrador global** y seleccione la aplicación.

2. Seleccione **Permisos necesarios** y, en la parte superior de la hoja, haga clic en el botón **Conceder permisos**.

Si lo desea, también puede generar una solicitud para *login.microsoftonline.com* con sus propias configuraciones de aplicación y anexarla a *&prompt=admin\_consent*. Una vez que la sesión se haya iniciado con las credenciales del administrador, la aplicación tendrá consentimiento para todos los usuarios.

## <a name="how-to-force-user-consent-for-your-application"></a>Concesión forzosa del consentimiento del usuario en la aplicación

* Anexe *&prompt=consent* a las solicitudes de autorización que requieran el consentimiento del usuario final con cada autenticación.

## <a name="next-steps"></a>Pasos siguientes

[Consentimiento e integración de aplicaciones en Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

[Consentimiento y permisos para las aplicaciones convergentes v2.0 de Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD en StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
