---
title: "Tutorial: Integración de Azure Active Directory con FileCloud | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y FileCloud."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f39f0ddd-b504-4562-971f-77b88d1e75fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: b672133e0604e35d6e398fbee4db303f70158111
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>Tutorial: integración de Azure Active Directory con FileCloud
El objetivo de este tutorial es mostrar cómo integrar FileCloud con Azure Active Directory (Azure AD).

La integración de FileCloud con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a FileCloud.
* Puede permitir que los usuarios inicien sesión automáticamente en FileCloud mediante inicio de sesión único (SSO) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con FileCloud se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el SSO en FileCloud

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de FileCloud desde la galería
2. Configuración y prueba del inicio de sesión único de Azure AD

## <a name="add-filecloud-from-the-gallery"></a>Adición de FileCloud desde la galería
Para configurar la integración de FileCloud en Azure AD, será preciso que agregue FileCloud desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar FileCloud desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1]

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **FileCloud**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_01.png)

7. En el panel de resultados, seleccione **FileCloud** y luego haga clic en **Completar** para agregar la aplicación.
   
    ![Selección de la aplicación en la galería](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el SSO de Azure AD con FileCloud mediante un usuario de prueba llamado "Britta Simon".

Para que el SSO funcione, Azure AD debe saber cuál es el usuario homólogo en FileCloud de un usuario de Azure AD. Es decir, hay que establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de FileCloud.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en FileCloud.

Para configurar y probar el SSO de Azure AD con FileCloud, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de FileCloud](#creating-a-filecloud-test-user)**: para tener un homólogo de Britta Simon en FileCloud que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el SSO de Azure AD en el portal clásico y lo configurará en la aplicación FileCloud.

**Para configurar el inicio de sesión único de Azure AD con FileCloud, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de aplicaciones de **FileCloud**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 

2. En la página **¿Cómo desea que los usuarios inicien sesión en FileCloud?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_03.png)

3. En el cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_04.png)
  1. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.filecloudhosted.com`.
  2. En el cuadro de texto **Identificador**, escriba: `https://<subdomain>.filecloudhosted.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`.
  3. Haga clic en **Siguiente**.
   
    >[!NOTE]
    >Tenga en cuenta que tiene que actualizar estos valores con los valores reales de URL de inicio de sesión e Identificador. Para obtener estos valores, póngase en contacto con el equipo de soporte técnico de FileCloud a través de <mailto:support@codelathe.com>.
    >  

4. En la página **Configurar inicio de sesión único en FileCloud**, lleve a cabo estos pasos y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_05.png)
 1. Haga clic en **Descargar metadatos**y luego guarde el archivo en el equipo.
 2. Haga clic en **Siguiente**.

5. En otra ventana del explorador web, inicie sesión en el inquilino de FileCloud como administrador.

6. En la barra de navegación de la izquierda, haga clic en **Settings**(Configuración). 
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_000.png)

7. Haga clic en la ficha **SSO** de la sección de configuración. 
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_001.png)

8. Seleccione **SAML** como **Default SSO Type** (Tipo de SSO predeterminado) en el panel **Configuración de inicio de sesión único (SSO)**.
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_002.png)

9. En el cuadro de texto **IdP End Point URL** (URL de punto de conexión IdP), coloque el valor de **Id. de entidad** del Asistente para configuración de aplicaciones de Azure AD en el panel **Configuración de SAML**.
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_003.png)

10. Abra el archivo de metadatos descargado en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **IdP Meta Data** (Metadatos de IdP) en el panel **Configuración de SAML**.
    
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_004.png)

11. Haga clic en el botón **Guardar** .

12. En el Portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
    
    ![Inicio de sesión único de Azure AD ][10]

13. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
    
    ![Inicio de sesión único de Azure AD ][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_09.png)

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_03.png)

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_05.png)
 1. En Tipo de usuario, seleccione Nuevo usuario de la organización.  
 2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**. 
 3. Haga clic en **Siguiente**.

6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_06.png) 
 1. En el cuadro de texto **Nombre**, escriba **Britta**.  
 2. En el cuadro de texto **Apellidos**, escriba **Simon**. 
 3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**. 
 4. En la lista **Rol**, seleccione **Usuario**. 
 5. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_07.png)

8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_08.png) 
 1. Anote el valor del campo **Nueva contraseña**.  
 2. Haga clic en **Completo**.   

### <a name="create-a-filecloud-test-user"></a>Creación de un usuario de prueba de FileCloud
El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en FileCloud. FileCloud admite el aprovisionamiento just-in-time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a FileCloud se creará un nuevo usuario, en caso de que no exista. 

>[!NOTE]
>Si necesita crear manualmente un usuario, es preciso que se ponga en contacto con el equipo de soporte técnico de FileCloud. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el SSO de Azure concediéndole acceso a FileCloud.

![Asignar usuario][200]

**Para asignar Britta Simon a FileCloud, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **FileCloud**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_50.png)

3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]

4. En la lista Usuarios, seleccione **Britta Simon**.

5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de FileCloud en el panel de acceso, debería iniciar sesión automáticamente en la aplicación FileCloud.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_205.png

