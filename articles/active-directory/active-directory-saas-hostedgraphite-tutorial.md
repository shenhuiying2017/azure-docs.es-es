---
title: "Tutorial: Integración de Azure Active Directory con Hosted Graphite | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Hosted Graphite."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f7286285828414000f90cf9d71facb9a9a51afd8


---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Tutorial: Integración de Azure Active Directory con Hosted Graphite
El objetivo de este tutorial es mostrar cómo integrar Hosted Graphite con Azure Active Directory (Azure AD).

La integración de Hosted Graphite con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Hosted Graphite.
* Puede permitir que los usuarios inicien sesión automáticamente en Hosted Graphite (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Hosted Graphite, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en Hosted Graphite

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Hosted Graphite desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-hosted-graphite-from-the-gallery"></a>Adición de Hosted Graphite desde la galería
Para configurar la integración de Hosted Graphite en Azure AD, deberá agregar Hosted Graphite desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Hosted Graphite desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Hosted Graphite**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_01.png)
7. En el panel de resultados, seleccione **Hosted Graphite** y luego haga clic en **Completar** para agregar la aplicación.
   
    ![Selección de la aplicación en la galería](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Hosted Graphite con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Hosted Graphite para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Hosted Graphite.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en Hosted Graphite.

Para configurar y probar el inicio de sesión único de Azure AD con Hosted Graphite, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Hosted Graphite](#creating-a-hosted-graphite-test-user)** : para tener un homólogo de Britta Simon en Hosted Graphite que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Hosted Graphite.

**Para configurar el inicio de sesión único de Azure AD con Hosted Graphite, realice los pasos siguientes:**

1. En el Portal clásico, en la página de integración de la aplicaciones **Hosted Graphite**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en Hosted Graphite?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_03.png)
3. En el cuadro de diálogo **Configurar las opciones de la aplicación**, si desea configurar la aplicación en el **modo iniciado por el proveedor de identidades**, realice los pasos siguientes y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_04.png)
   
    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://www.hostedgraphite.com/metadata/<user id>`
   
    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://www.hostedgraphite.com/complete/saml/<user id>`.
   
    c. Haga clic en **Siguiente**
4. Si quiere configurar la aplicación en el **modo iniciado por el proveedor de servicios**, en la página de diálogo **Configurar las opciones de la aplicación**, haga clic en **"Mostrar la configuración avanzada (opcional)"**, escriba la **URL de inicio de sesión** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)
   
    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://www.hostedgraphite.com/login/saml/<user id>/`.
   
    b. Haga clic en **Siguiente**
   
   > [!NOTE]
   > Tenga en cuenta que estos no son valores reales. Tendrá que actualizar estos valores con la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Para obtener estos valores, puede ir a Acceso -> Configuración de SAML en la aplicación o póngase en contacto con Hosted Graphite.
   > 
   > 
5. En la página **Configurar inicio de sesión único en Hosted Graphite**, lleve a cabo estos pasos y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_05.png)
   
    a. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
   
    b. Haga clic en **Siguiente**.
6. Inicie la sesión en el inquilino de Hosted Graphite como administrador.
7. Vaya a la **página de configuración de SAML** en la barra lateral (**Acceso -> Configuración de SAML**).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)
8. Confirme que estas direcciones URL coinciden con su configuración del paso 3.
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)
9. Copie los valores de **URL del emisor** y **Dirección URL de inicio de sesión único de SAML de Azure AD** a **Id. de entidad o del emisor** y **Dirección URL de inicio de sesión único de SSO** en Hosted Graphite.
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_003.png)
10. Seleccione "**Solo lectura**" en **Default User Role** (Rol de usuario predeterminado).
    
     ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)
11. Copie el contenido del archivo de certificado descargado y luego péguelo en el cuadro de texto **Certificado X.509** .
    
     ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)
12. Haga clic en el botón **Guardar** .
13. En el Portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
    
    ![Inicio de sesión único de Azure AD ][10]
14. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
    
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_09.png)
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_03.png)
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_04.png)
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_05.png)
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_06.png)
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_07.png)
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hostedgraphite-tutorial/create_aaduser_08.png)
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-hosted-graphite-test-user"></a>Creación de un usuario de prueba de Hosted Graphite
El objetivo de esta sección es crear un usuario llamado Britta Simon en Hosted Graphite. Hosted Graphite admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a Hosted Graphite se creará un nuevo usuario, en caso de que no exista.

> [!NOTE]
> Si necesita crear manualmente un usuario, es preciso que se ponga contacto con el equipo de soporte técnico de Hosted Graphite a través de <mailto:help@hostedgraphite.com>.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo que se le concederá acceso a Hosted Graphite.

![Asignar usuario][200]

**Para asignar a Britta Simon a Hosted Graphite, realice los pasos siguientes:**

1. En el Portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **Hosted Graphite**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-hostedgraphite-tutorial/tutorial_hostedgraphite_50.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Hosted Graphite en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Hosted Graphite.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hostedgraphite-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


