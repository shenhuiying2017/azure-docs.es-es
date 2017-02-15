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
ms.date: 10/31/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 55a016a0943885a3aaa636316808939777afb0f8


---
# <a name="manage-analysis-services"></a>Administración de Analysis Services
Una vez que se ha creado un servidor de Analysis Services en Azure, es posible que haya que realizar varias tareas de administración inmediatamente o en algún momento del futuro. Por ejemplo, ejecutar el procesamiento de los datos de la actualización, controlar quién puede acceder a los modelos del servidor o supervisar el estado del servidor. Varias de estas tareas solo se pueden realizar en Azure Portal, otras en SQL Server Management Studio (SSMS) y otras se pueden realizar indistintamente en ambos.

## <a name="azure-portal"></a>Azure Portal
[Azure Portal](http://portal.azure.com/) es el lugar donde se pueden crear y eliminar servidores, supervisar los recursos de los servidores, cambiar el tamaño de los servidores y administrar quién tiene acceso a ellos.  Si tiene problemas, también puede enviar una solicitud de soporte técnico.

![Obtención del nombre del servidor en Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
En Azure, la conexión a los servidores se realiza de la misma forma que la conexión a una instancia de servidor en una organización. Desde SSMS se pueden realizar muchas de las mismas tareas, como procesar datos o crear una script de procesamiento, administrar roles y usar PowerShell.

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
   
    Nota: Si no ve la autenticación de Azure Active Directory, es posible que sea preciso que [habilite la autenticación de Azure Active Directory](#enable-azure-active-directory-authentication) en SSMS.
   
    ![Conectar en SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Dado que la administración de un servidor en Azure mediante SSMS es muy parecida a la de un servidor local, no vamos a entrar en detalles aquí. Toda la ayuda que necesita se puede encontrar en [Administración de una instancia de Analysis Services](https://msdn.microsoft.com/library/hh230806.aspx) en MSDN.

## <a name="server-administrators"></a>Administradores de servidor
Puede usar **Administradores de Analysis Services** en la hoja de control del servidor en Azure Portal o SSMS para administrar los administradores de servidor. Administradores de Analysis Services son los administradores del servidor de bases de datos con derechos para tareas comunes de administración de bases de datos, como agregar y quitar bases de datos y administrar usuarios. De forma predeterminada, el usuario que crea el servidor en Azure Portal se agrega automáticamente como administrador de Analysis Services.

También debe saber que:

* Windows Live ID no es un tipo de identidad compatible con Azure Analysis Services.  
* Los administradores de Analysis Services deben ser usuarios válidos de Azure Active Directory.
* Si se crea un servidor de Azure Analysis Services mediante plantillas de Azure Resource Manager, Administradores de Analysis Services toma una matriz JSON de los usuarios que deben agregarse como administradores.

Los administradores de Analysis Services pueden no ser los mismos que administradores de recursos de Azure, que pueden administrar los recursos de las suscripciones de Azure. Así se mantiene la compatibilidad con los XMLA y TSML existentes para administrar los comportamientos en Analysis Services y para permitir segregar las obligaciones entre la administración de recursos de Azure y la administración de base de datos de Analysis Services.

Para ver todos los roles y tipos de acceso de su recurso de Azure Analysis Services, utilice Access Control (IAM) en la hoja de control.

## <a name="database-users"></a>Usuarios de base de datos
Los usuarios de la base de datos modelo de Azure Analysis Services deben estar en Azure Active Directory. Los nombres de usuario especificados para la base de datos modelo deben ser por dirección de correo electrónico de organización o UPN. Esto es diferente de las bases de datos modelo locales que admiten usuarios por nombres de usuario de dominio de Windows.

Puede agregar usuarios mediante [asignaciones de roles en Azure Active Directory](../active-directory/role-based-access-control-configure.md) o mediante el [lenguaje de scripting de modelos tabulares](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) en SQL Server Management Studio.

**Script TMSL de ejemplo**

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="enable-azure-active-directory-authentication"></a>Habilitación de la autenticación de Azure Active Directory
Para habilitar la característica de autenticación de Azure Active Directory para SSMS en el registro, cree un archivo de texto denominado EnableAAD.reg y copie y pegue las siguientes líneas:

```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\Microsoft SQL Server\Microsoft Analysis Services\Settings]
"AS AAD Enabled"="True"
```

Guarde el archivo y ejecútelo.

## <a name="troubleshooting-connection-problems"></a>Solución de problemas de conexión
Al conectarse a un servidor mediante SSMS, si (en el paso 3) intenta iniciar sesión con una cuenta no federada o una cuenta que no está en su Azure Active Directory y no se puede conectar, es posible que tenga que borrar la caché de inicio de sesión. Cierre SSMS antes de seguir estos pasos.

1. En el Explorador de archivos, navegue a `C:\Users\<user_name>\AppData\Local\`.
2. Elimine la carpeta **AADCacheOM**.
3. Busque en la carpeta **Local** archivos .dat que comiencen por **omlibs-tokens-cache.** Si encuentra alguno, elimínelo.
4. Abra SSMS y repita los pasos de la sección [Para conectarse con SSMS](#to-connect-with-ssms).

## <a name="next-steps"></a>Pasos siguientes
Si aún no ha implementado un modelo tabular en el nuevo servidor, ahora es buen momento para hacerlo. Para más información, consulte [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Implementación en Azure Analysis Services).

Si ha implementado un modelo en un servidor, está listo para conectarse a él mediante un cliente o un explorador. Para más información, consulte [Get data from Azure Analysis Services](analysis-services-connect.md) (Obtención de datos del servidor de Azure Analysis Services).




<!--HONumber=Nov16_HO3-->


