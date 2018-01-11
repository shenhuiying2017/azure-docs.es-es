---
title: "Tutorial: Integración de Azure Active Directory con SilkRoad Life Suite | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y SilkRoad Life Suite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 0d6af7af7d6b28ff3ea9d518a65b8267a83b71b4
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Tutorial: Integración de Azure Active Directory con SilkRoad Life Suite

En este tutorial, aprenderá a integrar SilkRoad Life Suite con Azure Active Directory (Azure AD).

La integración de SilkRoad Life Suite con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a SilkRoad Life Suite.
- Puede permitir que los usuarios inicien sesión automáticamente en SilkRoad Life Suite (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SilkRoad Life Suite, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en SilkRoad Life Suite

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de SilkRoad Life Suite desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Adición de SilkRoad Life Suite desde la galería
Para configurar la integración de SilkRoad Life Suite en Azure AD, deberá agregar SilkRoad Life Suite desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SilkRoad Life Suite desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **SilkRoad Life Suite**, seleccione **SilkRoad Life Suite** en el panel de resultados y, después, haga clic en el botón **Agregar** para agregar la aplicación.

    ![SilkRoad Life Suite en la lista de resultados](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con SilkRoad Life Suite con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de SilkRoad Life Suite para un usuario de Azure AD. Es decir, hay que establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SilkRoad Life Suite.

Para establecer la relación de vínculo, en SilkRoad Life Suite, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con SilkRoad Life Suite, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SilkRoad Life Suite](#create-a-silkroad-life-suite-test-user)** : para tener un homólogo de Britta Simon en SilkRoad Life Suite que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación SilkRoad Life Suite.

**Para configurar el inicio de sesión único de Azure AD con SilkRoad Life Suite, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **SilkRoad Life Suite**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

3. En la sección **SilkRoad Life Suite Domain and URLs** (Dominio y direcciones URL de SilkRoad Life Suite), lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de SilkRoad Life Suite](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.silkroad-eng.com/Authentication/`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente modelo: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con el siguiente patrón: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de SilkRoad Life Suite](https://www.silkroad.com/locations/) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_400.png)
    
6. En la sección **SilkRoad Life Suite Configuration** (Configuración de SilkRoad Life Suite), haga clic en **Configure SilkRoad Life Suite** (Configurar SilkRoad Life Suite) para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de SilkRoad Life Suite](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

7. Inicie sesión en su sitio de la compañía de SilkRoad Life Suite como administrador. 
 
    >[!NOTE] 
    > Para obtener acceso a la aplicación de autenticación de SilkRoad Life Suite para configurar la federación con Microsoft Azure AD, póngase en contacto con el soporte técnico o el representante de servicios de SilkRoad.

8. Vaya a **Proveedor de servicios** y, luego, haga clic en **Detalles de federación**. 
   
    ![Inicio de sesión único de Azure AD ][10]

9. Haga clic en **Download Federation Metadata** (Descargar los metadatos de federación). Después, guarde el archivo de metadatos en el equipo.
   
    ![Inicio de sesión único de Azure AD ][11] 

10. En la aplicación **SilkRoad**, haga clic en **Authentication Sources** (Orígenes de autenticación).
   
    ![Inicio de sesión único de Azure AD ][12] 

11. Haga clic en **Add Authentication Source**(Agregar origen de autenticación). 
   
    ![Inicio de sesión único de Azure AD][13] 

12. En la sección **Add Authentication Source** (Agregar origen de autenticación), realice los siguientes pasos: 
   
    ![Inicio de sesión único de Azure AD][14]
  
    a. En **Option 2 - Metadata File** (Opción 2 - Archivo de metadatos), haga clic en **Examinar** para cargar el archivo de metadatos descargado de Azure Portal.
  
    b. Haga clic en **Create Identity Provider using File Data**(Crear proveedor de identidades con los datos del archivo).

13. En la sección **Authentication Sources** (Orígenes de autenticación), haga clic en **Edit** (Editar). 
    
     ![Inicio de sesión único de Azure AD ][15] 

14. En el cuadro de diálogo **Edit Authentication Source** (Editar origen de autenticación), realice los siguientes pasos: 
    
     ![Inicio de sesión único de Azure AD][16] 

    a. En **Enabled** (Habilitado), seleccione **Yes** (Sí).

    b. En el cuadro de texto **EntityId**, pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que copió de Azure Portal.
   
    c. En el cuadro de texto **IdP Description** (Descripción de IdP), escriba una descripción para la configuración (por ejemplo, *SSO de Azure AD*).

    d. En el cuadro de texto **Archivo de metadatos**, cargue el archivo de **metadatos** que descargó de Azure Portal.
  
    e. En el cuadro de texto **IdP Name** (Nombre de IdP), escriba un nombre que sea específico para su configuración (por ejemplo, *Azure SP*).
  
    f. En el cuadro de texto **Logout Service URL** (Dirección URL del servicio de cierre de sesión), pegue el valor de la **dirección URL de cierre de sesión** que copió de Azure Portal.

    g. En el cuadro de texto **sign-on service URL** (URL de servicio de inicio de sesión), pegue el valor de **SAML Single Sign-On Service URL** (URL de servicio de inicio de sesión único de SAML) que copió de Azure Portal.

    h. Haga clic en **Guardar**.

15. Deshabilite todos los demás orígenes de autenticación. 
    
     ![Inicio de sesión único de Azure AD ][17]

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-silkroad-life-suite-test-user"></a>Creación de un usuario de prueba de SilkRoad Life Suite

En esta sección, creará el usuario Britta Simon en SilkRoad Life Suite. Trabaje con el [equipo de atención al cliente de SilkRoad Life Suite](https://www.silkroad.com/locations/) para agregar los usuarios a la plataforma SilkRoad Life Suite. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a SilkRoad Life Suite.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a SilkRoad Life Suite, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SilkRoad Life Suite**.

    ![Vínculo de SilkRoad Life Suite en la lista de aplicaciones](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono SilkRoad Life Suite en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de SilkRoad Life Suite.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
