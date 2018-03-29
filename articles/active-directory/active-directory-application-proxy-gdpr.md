---
title: GDPR en Azure Active Directory Application Proxy | Microsoft Docs
description: Aprenda más sobre GDPR en Azure Active Directory Application Proxy.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 5e99fba2f226e1e9b401bd8ef5b3b85a5e8c6a7c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="gdpr-in-the-azure-active-directory-application-proxy"></a>GDPR en Azure Active Directory Application Proxy  

Azure Active Directory (Azure AD) Application Proxy es compatible con GDPR junto con todas las demás características y servicios de Microsoft. Para más información sobre la compatibilidad GDPR de Microsoft, consulte los [términos de licencia y la documentación](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Como esta característica incluye conectores en los equipos, hay algunos eventos que se deben supervisar para seguir siendo compatible con GDPR. Application Proxy crea los tipos de registro siguientes, que puede contener EUII:

- Registros de eventos de conector
- Registros de eventos de Windows

Se puede cumplir con el GDPR de dos formas:

- Eliminación y exportación de las solicitudes cuando surgen

- Desactivación del registro

Si desea:

- Para más información sobre cómo configurar la retención de datos de los registros de eventos de Windows, consulte [Settings for event logs](https://technet.microsoft.com/library/cc952132.aspx) (Configuración de registro de eventos). 
- Para más información sobre el registro de eventos de Windows, consulte [Using Windows Event Log](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx) (Uso del registro de eventos de Windows).


Puede encontrar los registros de eventos de conector en `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. En las secciones siguientes se proporcionan los pasos relacionados para los registros de eventos de conector. Debe completar estos pasos en todos los equipos de conector.
 

## <a name="processing-requests"></a>Procesamiento de solicitudes

Hay tres tipos diferentes de solicitudes de las que es responsable: 

- Eliminar
- Ver
- Exportación
 
Para procesar las solicitudes de vista o exportación, debe pasar por cada uno de los archivos de registro para buscar entradas relacionadas. 

Debido a que los registros son archivos de texto, puede buscar, por ejemplo, mediante el comando `findstr` para encontrar entradas relacionadas con su usuario. Busque los siguientes campos porque podrían estar en los registros: 

- UserId
- El tipo de nombre de usuario configurado para cualquier aplicación que utilice la delegación restringida de Kerberos:
    - Nombre principal de usuario
    - Nombre principal de usuario local
    - Parte del nombre de usuario del nombre principal de usuario
    - Parte del nombre de usuario del nombre principal de usuario local
    - Nombre de cuenta SAM local 

 
A continuación, puede recopilar estos campos y compartirlos con el usuario.
Para procesar las solicitudes de eliminación, debe eliminar los registros relevantes. Puede reiniciar el servicio de conector (conector del proxy de aplicación de Microsoft Azure AD) para generar un nuevo archivo de registro. El nuevo archivo de registro le permite eliminar los archivos de registro antiguos. Después, puede seguir el proceso de vista o exportación para buscar todos los registros relevantes y eliminar de manera selectiva esos campos o archivos. También puede eliminar todos los archivos de registro antiguos si ya no los necesita.


## <a name="turn-off-connector-logs"></a>Desactivación de los registros de conector

Para desactivar los registros de conector, debe ajustar `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` mediante la eliminación de la línea resaltada: 


![Configuración](./media/active-directory-application-proxy-gdpr/01.png)


## <a name="next-steps"></a>Pasos siguientes

Para obtener información general del proxy de aplicación de Azure AD, consulte [Provisión de acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md).

