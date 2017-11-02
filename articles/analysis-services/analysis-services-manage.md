---
title: "Administración de Azure Analysis Services | Microsoft Docs"
description: Aprenda a administrar un servidor de Analysis Services en Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 79491d0b-b00d-4e02-9ca7-adc99bc02fdb
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: cd452ff46f8a87fd04eaca037b6bc0120ca86e4d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2017
---
# <a name="manage-analysis-services"></a>Administración de Analysis Services
Una vez que se ha creado un servidor de Analysis Services en Azure, es posible que haya que realizar varias tareas de administración inmediatamente o en algún momento del futuro. Por ejemplo, ejecutar el procesamiento de los datos de la actualización, controlar quién puede acceder a los modelos del servidor o supervisar el estado del servidor. Varias de estas tareas solo se pueden realizar en Azure Portal, otras en SQL Server Management Studio (SSMS) y otras se pueden realizar indistintamente en ambos.

## <a name="azure-portal"></a>Portal de Azure
[Azure Portal](http://portal.azure.com/) es el lugar donde se puede crear y eliminar servidores, supervisar los recursos de los servidores, y cambiar el tamaño de los servidores y administrar quién tiene acceso a ellos.  Si tiene problemas, también puede enviar una solicitud de soporte técnico.

![Obtención del nombre del servidor en Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
En Azure, la conexión a los servidores se realiza de la misma forma que la conexión a una instancia de servidor en una organización. Desde SSMS se pueden realizar muchas de las mismas tareas, como procesar datos o crear una script de procesamiento, administrar roles y usar PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Descarga e instalación de SSMS
Para obtener las últimas características y la experiencia más completa al conectarse al servidor de Azure Analysis Services, asegúrese de estar usando la versión más reciente de SSMS. 

[Descargue SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Para conectarse con SSMS
 Si se usa SSMS, antes de conectarse al servidor por primera vez, asegúrese de que su nombre de usuario se incluye en el grupo de administradores de Analysis Services. Para más información, consulte la sección[Administradores de servidor](#server-administrators) en este mismo artículo.

1. Antes de establecer la conexión, es necesario obtener el nombre del servidor. En **Azure Portal** > servidor > **Información general** > **Nombre de servidor**, copie el nombre del servidor.
   
    ![Obtención del nombre del servidor en Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. En SSMS > **Explorador de objetos**, haga clic en **Conectar** > **Analysis Services**.
3. En el cuadro de diálogo **Conectarse al servidor**, pegue el nombre del servidor y en **Autenticación**, elija uno de los siguientes tipos de autenticación:
   
    **Autenticación de Windows** para usar las credenciales de dominio o nombre de usuario y contraseña de Windows.

    **Autenticación de contraseña de Active Directory** para usar una cuenta de organización. Por ejemplo, al establecer conexión desde un equipo unido que no es de dominio.

    **Autenticación Universal de Active Directory** para usar [autenticación multifactor o no interactiva](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Conectar en SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Administradores del servidor y usuarios de la base de datos
En Azure Analysis Services hay dos tipos de usuarios: los administradores del servidor y los usuarios de la base de datos. Ambos tipos de usuarios deben estar en su instancia de Azure Active Directory y se deben especificar mediante la dirección de correo electrónico profesional o UPN. Para más información, consulte [Permisos de usuario y autenticación](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Solución de problemas de conexión
Cuando se conecta mediante SSMS, si experimenta problemas, puede que deba borrar la caché de inicio de sesión. Nada se almacena en caché en el disco. Para borrar la caché, cierre y reinicie el proceso de conexión. 

## <a name="next-steps"></a>Pasos siguientes
Si aún no ha implementado un modelo tabular en el nuevo servidor, ahora es buen momento para hacerlo. Para más información, consulte [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Implementación en Azure Analysis Services).

Si ha implementado un modelo en un servidor, está listo para conectarse a él mediante un cliente o un explorador. Para más información, consulte [Get data from Azure Analysis Services](analysis-services-connect.md) (Obtención de datos del servidor de Azure Analysis Services).

