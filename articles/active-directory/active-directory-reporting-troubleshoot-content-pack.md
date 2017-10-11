---
title: "Solución de errores de los paquetes de contenido de los registros de actividad de Azure Active Directory | Microsoft Docs"
description: Proporciona una lista de mensajes de error del paquete de contenido de la actividad de Azure Active Directory y los pasos para corregirlos.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: c880e9eb6d48bd1e38075fbd867d3906ec67b547
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Solución de errores de los paquetes de contenido de los registros de actividad de Azure Active Directory 


Cuando se trabaja con el paquete de contenido de Power BI para la versión preliminar de Azure Active Directory, es posible que se encuentre con los siguientes errores: 

- [Error al actualizar](active-directory-reporting-troubleshoot-content-pack.md#refresh-failed) 
- [Error al actualizar las credenciales del origen de datos ](active-directory-reporting-troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [La importación de datos tarda demasiado](active-directory-reporting-troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
Este tema proporciona información sobre las posibles causas y cómo corregir estos errores.
 
## <a name="refresh-failed"></a>Error al actualizar 
 
**¿Cómo aparece este error**: correo electrónico desde Power BI o estado de error en el historial de actualización. 


| Causa | Solución |
| ---   | ---        |
| Los errores de actualización pueden producirse cuando se han restablecido las credenciales de los usuarios que se conectan al paquete de contenido, pero no se actualizan en la configuración de conexión del paquete de contenido. | En Power BI, busque el conjunto de datos correspondiente en el panel de registros de actividad de Azure Active Directory, elija programar la actualización y, a continuación, escriba sus credenciales de Azure AD. |
| Una actualización puede producir un error debido a problemas de datos en el paquete de contenido subyacente. | Abra una incidencia de soporte técnico. Para más detalles, consulte [Obtención de soporte técnico para Azure Active Directory](active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Error al actualizar las credenciales del origen de datos 
 
**¿Cómo aparece este error**: en Power BI, cuando se conecta al paquete de contenido de registros de actividad (versión preliminar) de Azure Active Directory. 

| Causa | Solución |
| ---   | ---        |
| El usuario que se conecta no es un administrador global, un lector de seguridad ni un administrador de seguridad. | Utilice una cuenta que sea administrador global, lector de seguridad o administrador de seguridad para tener acceso a los paquetes de contenido. |
| El inquilino no es Premium o no tiene al menos un usuario con un archivo de licencia Premium. | Abra una incidencia de soporte técnico. Para más detalles, consulte [Obtención de soporte técnico para Azure Active Directory](active-directory-troubleshooting-support-howto.md).|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>La importación de datos tarda demasiado 
 
**¿Cómo aparece este error**: en Power BI, una vez que se ha conectado el paquete de contenido, el proceso de importación de datos comienza a preparar su panel para el registro de actividad de Azure Active Directory. Verá el mensaje: "*Importando datos...* ”  

| Causa | Solución |
| ---   | ---        |
| Según el tamaño de su inquilino, este paso puede tardar desde algunos minutos hasta media hora. | Tenga paciencia. Si el mensaje no cambia para mostrar el panel dentro de una hora, registre una incidencia de soporte técnico. Para más detalles, consulte [Obtención de soporte técnico para Azure Active Directory](active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Pasos siguientes

Para instalar el paquete de contenido de Power BI para Azure Active Directory (versión preliminar), haga clic [aquí](https://powerbi.microsoft.com/en-us/blog/azure-active-directory-meets-power-bi/).


