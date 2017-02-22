---
title: "Administración de Azure Analysis Services | Microsoft Docs"
description: Aprenda a administrar un servidor de Analysis Services en Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 79491d0b-b00d-4e02-9ca7-adc99bc02fdb
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/20/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 13eb8ab1bf3c218f14b4c23ca1a46e9552d55b25
ms.openlocfilehash: a5db6cccf6c3dc55ee2cda59cb9e2ecd2292fcb5


---
# <a name="manage-analysis-services"></a>Administración de Analysis Services
Una vez que se ha creado un servidor de Analysis Services en Azure, es posible que haya que realizar varias tareas de administración inmediatamente o en algún momento del futuro. Por ejemplo, ejecutar el procesamiento de los datos de la actualización, controlar quién puede acceder a los modelos del servidor o supervisar el estado del servidor. Varias de estas tareas solo se pueden realizar en Azure Portal, otras en SQL Server Management Studio (SSMS) y otras se pueden realizar indistintamente en ambos.

## <a name="azure-portal"></a>Azure Portal
[Azure Portal](http://portal.azure.com/) es el lugar donde se pueden crear y eliminar servidores, supervisar los recursos de los servidores, cambiar el tamaño de los servidores y administrar quién tiene acceso a ellos.  Si tiene problemas, también puede enviar una solicitud de soporte técnico.

![Obtención del nombre del servidor en Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
En Azure, la conexión a los servidores se realiza de la misma forma que la conexión a una instancia de servidor en una organización. Desde SSMS se pueden realizar muchas de las mismas tareas, como procesar datos o crear una script de procesamiento, administrar roles y usar PowerShell. [Descargue e instale la versión más reciente de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

 Una de las mayores diferencias es la autenticación que se utiliza para conectarse al servidor. Para conectarse al servidor de Azure Analysis Services, es preciso seleccionar **Autenticación de contraseña de Active Directory**.

 Si se usa SSMS, antes de conectarse al servidor por primera vez, asegúrese de que su nombre de usuario se incluye en el grupo de administradores de Analysis Services. Para más información, consulte la sección[Administradores de servidor](#server-administrators) en este mismo artículo.

### <a name="to-connect-with-ssms"></a>Para conectarse con SSMS
1. Antes de establecer la conexión, es necesario obtener el nombre del servidor. En **Azure Portal** > servidor > **Información general** > **Nombre de servidor**, copie el nombre del servidor.
   
    ![Obtención del nombre del servidor en Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. En SSMS > **Explorador de objetos**, haga clic en **Conectar** > **Analysis Services**.
3. En el cuadro de diálogo **Conectar con el servidor**, pegue el nombre del servidor y luego, en **Autenticación**, elija una de las siguientes opciones:
   
    **Autenticación integrada de Active Directory** para utilizar el inicio de sesión único con Active Directory para la federación de Azure Active Directory.
   
    **Autenticación de contraseña de Active Directory** para usar una cuenta de organización. Por ejemplo, al establecer conexión desde un equipo unido que no es de dominio.
   
    Nota: Si no ve la autenticación de Active Directory, debe actualizar a la [versión más reciente de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).
   
    ![Conectar en SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Dado que la administración de un servidor en Azure mediante SSMS es muy parecida a la de un servidor local, no vamos a entrar en detalles aquí. Toda la ayuda que necesita se puede encontrar en [Administración de una instancia de Analysis Services](https://msdn.microsoft.com/library/hh230806.aspx) en MSDN.

## <a name="server-administrators-and-database-users"></a>Administradores del servidor y usuarios de la base de datos
En Azure Analysis Services hay dos tipos de usuarios: los administradores del servidor y los usuarios de la base de datos. Ambos tipos de usuarios deben estar en su instancia de Azure Active Directory y se deben especificar mediante la dirección de correo electrónico profesional o UPN. Esto es diferente de las bases de datos locales de modelo tabular que admiten administradores del servidor y usuarios de la base de datos según los nombres de usuario de dominio de Windows. Para más información, consulte [Administración de usuarios en Azure Analysis Services](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Solución de problemas de conexión
Al conectarse a un servidor mediante SSMS, si (en el paso 3) intenta iniciar sesión con una cuenta no federada o una cuenta que no está en su Azure Active Directory y no se puede conectar, es posible que tenga que borrar la caché de inicio de sesión. Cierre SSMS antes de seguir estos pasos.

1. En el Explorador de archivos, navegue a `C:\Users\<user_name>\AppData\Local\`.
2. Elimine la carpeta **AADCacheOM**.
3. Busque en la carpeta **Local** archivos .dat que comiencen por **omlibs-tokens-cache.** Si encuentra alguno, elimínelo.
4. Abra SSMS y repita los pasos de la sección [Para conectarse con SSMS](#to-connect-with-ssms).

## <a name="next-steps"></a>Pasos siguientes
Si aún no ha implementado un modelo tabular en el nuevo servidor, ahora es buen momento para hacerlo. Para más información, consulte [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Implementación en Azure Analysis Services).

Si ha implementado un modelo en un servidor, está listo para conectarse a él mediante un cliente o un explorador. Para más información, consulte [Get data from Azure Analysis Services](analysis-services-connect.md) (Obtención de datos del servidor de Azure Analysis Services).




<!--HONumber=Feb17_HO2-->


