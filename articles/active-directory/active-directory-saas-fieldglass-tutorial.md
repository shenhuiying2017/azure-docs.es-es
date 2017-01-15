---
title: "Tutorial: Integración de Azure Active Directory con Fieldglass | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Fieldglass."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2510195f-d5b1-4684-b3da-283fb8619df2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4f7a99e82c73d446c928a821f80fc640a1994d67
ms.openlocfilehash: 837e96e69e4855daf36e0c85e48cf68daaee8925


---
# <a name="tutorial-azure-active-directory-integration-with-fieldglass"></a>Tutorial: Integración de Azure Active Directory con Fieldglass
El objetivo de este tutorial es mostrar cómo integrar Fieldglass con Azure Active Directory (Azure AD).

La integración de Fieldglass con Azure AD proporciona las siguientes ventajas:

* En Azure AD se puede controlar quién tiene acceso a Fieldglass
* Puede permitir que los usuarios inicien sesión automáticamente en Fieldglass (inicio de sesión único) con sus cuentas de Azure AD
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Fieldglass se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para inicio de sesión único en Fieldglass

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

1. Incorporación de Fieldglass desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-fieldglass-from-the-gallery"></a>Incorporación de Fieldglass desde la galería
Para configurar la integración de Fieldglass en Azure AD, será preciso que agregue Fieldglass desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Fieldglass desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Fieldglass**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_01.png)
7. En el panel de resultados, seleccione **Fieldglass** y haga clic en **Completar** para agregar la aplicación.
   
    ![Selección de la aplicación en la galería](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Fieldglass con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Fieldglass para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Fieldglass.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en Fieldglass.

Para configurar y probar el inicio de sesión único de Azure AD con Fieldglass, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en Fieldglass](#creating-a-fieldglass-test-user)**: para tener un homólogo de Britta Simon en Fieldglass que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurará el inicio de sesión único en la aplicación Fieldglass.

**Para configurar el inicio de sesión único de Azure AD con Fieldglass, realice los pasos siguientes:**

1. En el Portal clásico, en la página de integración de aplicaciones de **Fieldglass**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en Fieldglass?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_03.png) 
3. En el cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_04.png)
   
    a. En el cuadro de texto **Identificador**, escriba la dirección URL `https://www.fieldglass.com` o siga el patrón: `https://<company name>.fgvms.com`
   
    b. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con los siguientes patrones: 
   
    - `https://<company name>.fgvms.com/<company name>`
    - `https://www.fieldglass.net/<company name>`
     
    c. Haga clic en **Siguiente**
     
    > [!NOTE]
    > Tenga en cuenta que estos no son valores reales. Tendrá que actualizar los valores con el identificador y la dirección URL de respuesta reales. Para obtener estos valores, póngase en contacto con [FieldGlass](http://www.fieldglass.com/solutions/support).
    > 
    >
     
4. En la página **Configurar inicio de sesión único en FieldGlass**, lleve a cabo estos pasos y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_05.png)
   
    a. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
   
    b. Haga clic en **Siguiente**.
5. Para configurar el SSO para su aplicación, póngase en contacto con el equipo de soporte técnico de Fieldglass y proporcione lo siguiente: 
   
    - El archivo de **certificado descargado**
   
    - El **identificador de entidad**
   
    - La **dirección URL del servicio de cierre de sesión único**
6. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_09.png)
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_03.png)
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_04.png)
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_05.png)
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_06.png)
   
    a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
    b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
    d. En la lista **Rol**, seleccione **Usuario**.
   
    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_07.png)
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-fieldglass-tutorial/create_aaduser_08.png)
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-fieldglass-test-user"></a>Creación de un usuario de prueba en Fieldglass
El objetivo de esta sección es crear un usuario llamado Britta Simon en Fieldglass. Colabore con el equipo de soporte técnico para agregar los usuarios a la cuenta de Fieldglass.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es que Britta Simon pueda usar el inicio de sesión único de Azure concediéndole acceso a Fieldglass.

![Asignar usuario][200]

**Para asignar Britta Simon a Fieldglass, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **Fieldglass**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-fieldglass-tutorial/tutorial_fieldglass_50.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Fieldglass en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Fieldglass.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-fieldglass-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


