---
title: "Problema al crear una aplicación de proxy de aplicación | Microsoft Docs"
description: "Cómo solucionar los problemas que surgen al crear aplicaciones de proxy de aplicación en el portal de administración de Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: fe56f56162ba7186f1b660a5b44fcef38f1dee9d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="problem-creating-an-application-proxy-application"></a>Problema al crear una aplicación de proxy de aplicación 

A continuación, se muestran algunos de los problemas habituales a los que se enfrentan los usuarios cuando crean una aplicación de proxy de aplicación.

## <a name="recommended-documents"></a>Documentos recomendados 

Para más información sobre la creación de una aplicación de proxy de aplicación mediante el portal de administración, consulte [Publicación de aplicaciones mediante el proxy de aplicación de Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal).

Si está siguiendo los pasos de ese documento y obtiene un error al crear la aplicación, consulte los detalles del error para obtener información y sugerencias para corregir la aplicación. La mayoría de los mensajes de error incluyen una sugerencia de corrección. 

## <a name="specific-things-to-check"></a>Aspectos específicos que comprobar

Para evitar errores habituales, compruebe que:

-   Sea un administrador con permiso para crear una aplicación de proxy de aplicación;

-   La dirección URL interna sea única;

-   La dirección URL externa sea única;

-   Las direcciones URL empiecen por http o https y terminen en "/";

-   La dirección URL debe ser un nombre de dominio y no una dirección IP.

El mensaje de error debería aparecer en la esquina superior derecha cuando cree la aplicación. También puede seleccionar el icono de notificación para ver los mensajes de error.

   ![Mensaje de notificación](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Pasos siguientes
[Habilitación del proxy de aplicación en Azure Portal](active-directory-application-proxy-enable.md)
