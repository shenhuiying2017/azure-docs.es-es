---
title: "Requisitos previos para acceder a la API de creación de informes de Azure AD | Microsoft Docs"
description: "Conozca los requisitos previos para acceder a la API de generación de informes de Azure AD."
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b0f23cc578fb946247d492b9dee92cf7a9daafa1


---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Requisitos previos para acceder a la API de generación de informes de Azure AD
Las [API de generación de informes de Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) proporcionan acceso mediante programación a los datos a través de un conjunto de API de REST. Estas API pueden llamarse desde una variedad de lenguajes de programación y herramientas.

La API de generación de informes utiliza [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar el acceso a las API web. 

Para preparar el acceso a la API de generación de informes, debe hacer lo siguiente:

1. Crear una aplicación en el inquilino de Azure AD 
2. Conceder los permisos adecuados de aplicación para acceder a los datos de Azure AD
3. Recopilar los valores de configuración del directorio

Para ver preguntas, problemas o comentarios, póngase en contacto con el equipo de [ayuda de informes de AAD](mailto:aadreportinghelp@microsoft.com).

## <a name="create-an-azure-ad-application"></a>Creación de una aplicación de Azure AD
Si quiere configurar el directorio para que tenga acceso a la API de generación de informes de Azure AD, debe iniciar sesión en el Portal de Azure clásico con una cuenta de administrador de suscripción de Azure que también sea miembro del rol de directorio Administrador global en su inquilino de Azure AD.

> [!IMPORTANT]
> Las aplicaciones que se ejecutan con credenciales con privilegios administrativos como este pueden ser muy avanzadas, así que asegúrese de mantener seguras las credenciales del secreto o el identificador de la aplicación.
> 
> 

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/01.png) 
2. En la lista de **directorios activos** , seleccione el directorio.
3. En el menú superior, haga clic en **Aplicaciones**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/02.png) 
4. En la barra inferior, haga clic en **Agregar**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/03.png) 
5. En la página de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación que mi organización está desarrollando**. 
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/04.png) 
6. En la cuadro de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes: 
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/05.png) 
   
    a. En el cuadro de texto **Nombre** , escriba un nombre (por ejemplo: aplicación de API de generación de informes).
   
    b. Seleccione **Aplicación web y/o API web**.
   
    c. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Agregar propiedades** , realice los pasos siguientes: 
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/06.png) 
   
    a. En el cuadro de texto **URL de inicio de sesión**, escriba `https://localhost`.
   
    b. En el cuadro de texto **URI de id. de aplicación**, escriba ```https://localhost/ReportingApiApp```.
   
    c. Haga clic en **Complete**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Conceder a la aplicación permiso para usar la API
1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/01.png) 
2. En la lista de **directorios activos** , seleccione el directorio.
3. En el menú superior, haga clic en **Aplicaciones**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/02.png)
4. En la lista de aplicaciones, seleccione la aplicación que acaba de crear.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/07.png)
5. En el menú de la parte superior, haga clic en **Configurar**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/08.png)
6. En la sección **Permisos para otras aplicaciones**, en el recurso **Azure Active Directory**, haga clic en la lista desplegable **Permisos de la aplicación** y seleccione **Leer datos de directorio**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/09.png)
7. En la barra inferior, haga clic en **Guardar**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/10.png)

## <a name="gather-configuration-settings-from-your-directory"></a>Recopilar los valores de configuración del directorio
En esta sección se muestra cómo obtener la siguiente configuración del directorio:

* Nombre de dominio
* id. de cliente
* Secreto del cliente

Necesitará estos valores al configurar llamadas a la API de generación de informes. 

### <a name="get-your-domain-name"></a>Obtención del nombre de dominio
1. En el [Portal de Azure clásico](https://manage.windowsazure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/01.png) 
2. En la lista de **directorios activos** , seleccione el directorio.
3. En el menú de la parte superior, haga clic en **Dominios**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/11.png) 
4. Escriba el nombre del dominio en el campo **Nombre de dominio** .
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/12.png) 

### <a name="get-the-applications-client-id"></a>Obtención del id. de cliente de la aplicación
1. En el [Portal de Azure clásico](https://manage.windowsazure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/01.png) 
2. En la lista de **directorios activos** , seleccione el directorio.
3. En el menú superior, haga clic en **Aplicaciones**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/02.png) 
4. En la lista de aplicaciones, seleccione la aplicación que acaba de crear.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/07.png)
5. En el menú de la parte superior, haga clic en **Configurar**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/08.png)
6. Copie el **id. de cliente**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/13.png)

### <a name="get-the-applications-client-secret"></a>Obtención del secreto de cliente de la aplicación
Para obtener el secreto de cliente de la aplicación, debe crear una nueva clave y guardar su valor después, ya que no es posible recuperarla más tarde.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/01.png) 
2. En la lista de **directorios activos** , seleccione el directorio.
3. En el menú superior, haga clic en **Aplicaciones**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/02.png) 
4. En la lista de aplicaciones, seleccione la aplicación que acaba de crear.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/07.png)
5. En el menú de la parte superior, haga clic en **Configurar**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/08.png)
6. En la sección **Claves** , realice los siguientes pasos: 
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/14.png)
   
    a. En la lista de duración, seleccione una duración.
   
    b. En la barra inferior, haga clic en **Guardar**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites/10.png)
   
    c. Copie el valor de la clave.

## <a name="next-steps"></a>Pasos siguientes
* ¿Quiere obtener acceso mediante programación a los datos de la API de generación de informes de Azure AD? Consulte [Introducción a la API de informes de Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Si quiere obtener más información sobre informes de Azure Active Directory, consulte la [guía de generación de informes de Azure Active Directory](active-directory-reporting-guide.md).  




<!--HONumber=Dec16_HO4-->


