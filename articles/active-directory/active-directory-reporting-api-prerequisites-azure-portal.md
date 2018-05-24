---
title: Requisitos previos para acceder a la API de generación de informes de Azure Active Directory | Microsoft Docs
description: Conozca los requisitos previos para acceder a la API de generación de informes de Azure AD.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ab05907f1f23c3856b41a941c1b95992ed5a79a4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Requisitos previos para acceder a la API de generación de informes de Azure Active Directory

Las [API de generación de informes de Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) proporcionan acceso mediante programación a los datos a través de un conjunto de API de REST. Estas API pueden llamarse desde una variedad de lenguajes de programación y herramientas.

La API de generación de informes utiliza [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar el acceso a las API web.

Para preparar el acceso a la API de generación de informes, necesita lo siguiente:

1. Asignación de roles
2. Registro de una aplicación
3. Concesión de permisos
4. Recopilación de configuraciones



## <a name="assign-roles"></a>Asignación de roles

Para acceder a los datos de informes a través de la API, debe tener asignado uno de los siguientes roles:

- Lector de seguridad

- Administrador de seguridad

- Administrador global




## <a name="register-an-application"></a>Registro de una aplicación

Debe registrar una aplicación, incluso si accede a la API de generación de informes mediante un script. Así se le proporciona un **id. de la aplicación**, que es necesario para una llamada de autorización y hace posible que el código reciba tokens.

Si quiere configurar el directorio para que acceda a la API de generación de informes de Azure AD, debe iniciar sesión en Azure Portal con una cuenta de administrador de Azure que también sea miembro del rol de directorio **Administrador global** en su inquilino de Azure AD.

> [!IMPORTANT]
> Las aplicaciones que se ejecutan con credenciales con privilegios administrativos como este pueden ser muy avanzadas, así que asegúrese de mantener seguras las credenciales del secreto o el identificador de la aplicación.
> 


**Para registrar una aplicación de Azure Active Directory:**

1. En el panel de navegación izquierdo de [Azure Portal](https://portal.azure.com), haga clic en **Azure Active Directory**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. En la página **Azure Active Directory**, haga clic en **Registros de aplicaciones**.

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. En la página **Registros de aplicaciones**, en la barra de herramientas de la parte superior, haga clic en **Nuevo registro de aplicaciones**.

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. En la página **Crear**, realice los pasos siguientes:

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. En el cuadro de texto **Nombre**, escriba `Reporting API application`.

    b. Seleccione **Aplicación web o API** como **Tipo de aplicación**.

    c. En el cuadro de texto **URL de inicio de sesión**, escriba `https://localhost`.

    d. Haga clic en **Create**(Crear). 


## <a name="grant-permissions"></a>Concesión de permisos 

En función de la API a la que desea acceder, debe conceder los siguientes permisos a la aplicación:  

| API | Permiso |
| --- | --- |
| Microsoft Azure Active Directory | Leer datos de directorio |
| Microsoft Graph | Leer todos los datos del registro de auditoría |


![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/36.png)


En la siguiente sección se enumeran los pasos para ambas API. Si no desea acceder a una de las API, puede omitir los pasos relacionados.
 

**Para conceder a la aplicación permisos para usar las API:**

1. En la página **Registros de aplicaciones**, en la lista de aplicaciones, haga clic en **Reporting API application** (Aplicación de la API de generación de informes).

2. En la página **Reporting API application** (Aplicación de la API de generación de informes), en la barra de herramientas de la parte superior, haga clic en **Configuración**. 

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. En la página **Configuración**, haga clic en **Permisos necesarios**. 

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. En la página **Permisos necesarios**, en la lista **API**, haga clic en **Microsoft Azure Active Directory**. 

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. En la página **Habilitar acceso**, seleccione **Leer datos de directorio** y anule la selección de **Iniciar sesión y leer el perfil del usuario**. 

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. En la barra de herramientas de la parte superior, haga clic en **Guardar**.

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. En la página **Permisos necesarios**, en la barra de herramientas de la parte superior, haga clic en **Agregar**.

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/32.png)

8. En la página **Agregar acceso de API**, haga clic en **Seleccionar una API**.

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/31.png)

9. En la página **Seleccionar una API**, haga clic en **Microsoft Graph** y, a continuación, haga clic en **Seleccionar**.

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/33.png)

10. En la página **Habilitar acceso**, seleccione **Read all audit log data** (Leer todos los datos del registro de auditoría) y, a continuación, haga clic en **Seleccionar**.  

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/34.png)


11. En la página **Agregar acceso de API**, haga clic en **Listo**.  

12. En la página **Permisos necesarios**, en la barra de herramientas de la parte superior, haga clic en **Conceder permisos** y, a continuación, haga clic en **Sí**.

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Recopilación de configuraciones 

En esta sección se muestra cómo obtener la siguiente configuración del directorio:

- Nombre de dominio
- id. de cliente
- Secreto del cliente

Necesitará estos valores al configurar llamadas a la API de generación de informes. 

### <a name="get-your-domain-name"></a>Obtención del nombre de dominio

**Para obtener el nombre del dominio:**

1. En el panel de navegación izquierdo de [Azure Portal](https://portal.azure.com), haga clic en **Azure Active Directory**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. En la página **Azure Active Directory**, haga clic en **Nombres de dominio personalizados**.

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Copie el nombre del dominio en la lista de dominios.


### <a name="get-your-applications-client-id"></a>Obtención del id. de cliente de la aplicación

**Para obtener el id. de cliente de la aplicación:**

1. En el panel de navegación izquierdo de [Azure Portal](https://portal.azure.com), haga clic en **Azure Active Directory**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. En la página **Registros de aplicaciones**, en la lista de aplicaciones, haga clic en **Reporting API application** (Aplicación de la API de generación de informes).

3. En la página **Reporting API application** (Aplicación de la API de generación de informes), en **Id. de la aplicación**, haga clic en **Haga clic para copiar**.

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Obtención del secreto de cliente de la aplicación
Para obtener el secreto de cliente de la aplicación, debe crear una nueva clave y guardar su valor después, ya que no es posible recuperarla más tarde.

**Para obtener el secreto de cliente de la aplicación:**

1. En el panel de navegación izquierdo de [Azure Portal](https://portal.azure.com), haga clic en **Azure Active Directory**.
   
    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. En la página **Registros de aplicaciones**, en la lista de aplicaciones, haga clic en **Reporting API application** (Aplicación de la API de generación de informes).


3. En la página **Reporting API application** (Aplicación de la API de generación de informes), en la barra de herramientas de la parte superior, haga clic en **Configuración**. 

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. En la página **Configuración**, en la sección **Acceso de API**, haga clic en **Claves**. 

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. En la página **Claves**, siga estos pasos:

    ![Registre la aplicación](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. En el cuadro de texto **Descripción**, escriba `Reporting API`.

    b. En **Expiración**, seleccione **En 2 años**.

    c. Haga clic en **Save**(Guardar).

    d. Copie el valor de la clave.


## <a name="next-steps"></a>Pasos siguientes

- [Obtención de datos mediante Reporting API de Azure Active Directory con certificados](active-directory-reporting-api-with-certificates.md)

- [Obtención de una primera impresión de Reporting API](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Creación de una solución propia](active-directory-reporting-api-getting-started-azure-portal.md#customize)

