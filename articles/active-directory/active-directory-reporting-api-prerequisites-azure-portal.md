---
title: "Requisitos previos para acceder a la API de generación de informes de Azure AD | Microsoft Docs"
description: "Conozca los requisitos previos para acceder a la API de generación de informes de Azure AD."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 5fafd83c337e3c73260d89cdad7409a01ce5855b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Requisitos previos para acceder a la API de generación de informes de Azure AD

Las [API de generación de informes de Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) proporcionan acceso mediante programación a los datos a través de un conjunto de API de REST. Estas API pueden llamarse desde una variedad de lenguajes de programación y herramientas.

La API de generación de informes utiliza [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar el acceso a las API web. 

Para acceder a los datos de informes a través de la API, debe tener asignado uno de los siguientes roles:

- Lector de seguridad
- Administrador de seguridad
- Administrador global


Para preparar el acceso a la API de generación de informes, debe hacer lo siguiente:

1. Registro de una aplicación 
2. Concesión de permisos 
3. Recopilación de configuraciones 

Si tiene preguntas, problemas o comentarios, [abra una incidencia de soporte técnico](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="register-an-azure-active-directory-application"></a>Registro de una aplicación de Azure Active Directory

Debe registrar una aplicación incluso si accede a la API de generación de informes mediante un script. Así se le proporciona un **id. de la aplicación**, que es necesario para una llamada de autorización y hace posible que el código reciba tokens.

Si quiere configurar el directorio para que acceda a la API de generación de informes de Azure AD, debe iniciar sesión en Azure Portal con una cuenta de administrador de Azure que también sea miembro del rol de directorio **Administrador global** en su inquilino de Azure AD.

> [!IMPORTANT]
> Las aplicaciones que se ejecutan con credenciales con privilegios administrativos como este pueden ser muy avanzadas, así que asegúrese de mantener seguras las credenciales del secreto o el identificador de la aplicación.
> 


**Para registrar una aplicación de Azure Active Directory:**

1. En [Azure Portal](https://portal.azure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. En la hoja **Azure Active Directory**, haga clic en **Registros de aplicaciones**.

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. En la hoja **Registros de aplicaciones**, en la barra de herramientas de la parte superior, haga clic en **Nuevo registro de aplicaciones**.

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. En la hoja **Crear**, siga estos pasos:

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. En el cuadro de texto **Nombre**, escriba `Reporting API application`.

    b. Seleccione **Aplicación web o API** como **Tipo de aplicación**.

    c. En el cuadro de texto **URL de inicio de sesión**, escriba `https://localhost`.

    d. Haga clic en **Crear**. 


## <a name="grant-permissions"></a>Concesión de permisos 

El objetivo de este paso es conceder a la aplicación permisos **Leer datos de directorio** para la API de **Microsoft Azure Active Directory**.

![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/16.png)
 

**Para conceder a la aplicación permiso para usar la API:**

1. En la hoja **Registros de aplicaciones**, en la lista de aplicaciones, haga clic en **Reporting API application** (Aplicación de la API de generación de informes).

2. En la hoja **Reporting API application** (Aplicación de la API de generación de informes), en la barra de herramientas de la parte superior, haga clic en **Configuración**. 

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. En la hoja **Configuración**, haga clic en **Permisos necesarios**. 

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. En la hoja **Permisos necesarios**, en la lista **API**, haga clic en **Microsoft Azure Active Directory**. 

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. En la hoja **Habilitar acceso**, seleccione **Leer datos de directorio**. 

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. En la barra de herramientas de la parte superior, haga clic en **Guardar**.

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

## <a name="gather-configuration-settings"></a>Recopilación de configuraciones 
En esta sección se muestra cómo obtener la siguiente configuración del directorio:

* Nombre de dominio
* id. de cliente
* Secreto del cliente

Necesitará estos valores al configurar llamadas a la API de generación de informes. 

### <a name="get-your-domain-name"></a>Obtención del nombre de dominio

**Para obtener el nombre del dominio:**

1. En [Azure Portal](https://portal.azure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. En la hoja **Azure Active Directory**, haga clic en **Nombres de dominio**.

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Copie el nombre del dominio en la lista de dominios.


### <a name="get-your-applications-client-id"></a>Obtención del id. de cliente de la aplicación

**Para obtener el id. de cliente de la aplicación:**

1. En [Azure Portal](https://portal.azure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. En la hoja **Registros de aplicaciones**, en la lista de aplicaciones, haga clic en **Reporting API application** (Aplicación de la API de generación de informes).

3. En la hoja **Reporting API application** (Aplicación de la API de generación de informes), en **Id. de la aplicación**, haga clic en **Haga clic para copiar**.

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Obtención del secreto de cliente de la aplicación
Para obtener el secreto de cliente de la aplicación, debe crear una nueva clave y guardar su valor después, ya que no es posible recuperarla más tarde.

**Para obtener el secreto de cliente de la aplicación:**

1. En [Azure Portal](https://portal.azure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. En la hoja **Registros de aplicaciones**, en la lista de aplicaciones, haga clic en **Reporting API application** (Aplicación de la API de generación de informes).


3. En la hoja **Reporting API application** (Aplicación de la API de generación de informes), en la barra de herramientas de la parte superior, haga clic en **Configuración**. 

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. En la hoja **Configuración**, en la sección **Acceso de API**, haga clic en **Claves**. 

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. En la hoja **Claves**, siga estos pasos:

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. En el cuadro de texto **Descripción**, escriba `Reporting API`.

    b. En **Expiración**, seleccione **En 2 años**.

    c. Haga clic en **Guardar**.

    d. Copie el valor de la clave.


## <a name="next-steps"></a>Pasos siguientes
* ¿Quiere obtener acceso mediante programación a los datos de la API de generación de informes de Azure AD? Consulte [Introducción a la API de informes de Azure Active Directory](active-directory-reporting-api-getting-started.md).
* Si quiere obtener más información sobre informes de Azure Active Directory, consulte la [guía de generación de informes de Azure Active Directory](active-directory-reporting-guide.md).  

