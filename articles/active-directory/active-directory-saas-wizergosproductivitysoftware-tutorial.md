---
title: 'Tutorial: integración de Azure Active Directory con Wizergos Productivity Software | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Wizergos Productivity Software.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2016
ms.author: jeedes

---
# Tutorial: integración de Azure Active Directory con Wizergos Productivity Software
El objetivo de este tutorial es mostrar cómo integrar Wizergos Productivity Software con Azure Active Directory (Azure AD).

La integración de Wizergos Productivity Software con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Wizergos Productivity Software.
* Puede permitir que los usuarios inicien sesión automáticamente en Wizergos Productivity Software (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos
Para configurar la integración de Azure AD con Wizergos Productivity Software, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Un suscripción habilitada para el inicio de sesión único en Wizergos Productivity Software

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Wizergos Productivity Software desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## Incorporación de Wizergos Productivity Software desde la galería
Para configurar la integración de Wizergos Productivity Software en Azure AD, deberá agregar Wizergos Productivity Software desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Wizergos Productivity Software desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.
   
    ![Aplicaciones][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Wizergos Productivity Software**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_01.png)
7. En el panel de resultados, seleccione **Wizergos Productivity Software** y haga clic en **Completar** para agregar la aplicación.
   
    ![Selección de la aplicación en la galería](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_001.png)

## Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Wizergos Productivity Software con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Wizergos Productivity Software para un usuario de Azure AD. En otras palabras, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Wizergos Productivity Software.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Wizergos Productivity Software.

Para configurar y probar el inicio de sesión único de Azure AD con Wizergos Productivity Software, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Wizergos Productivity Software](#creating-a-wizergos-productivity-software-test-user)**: para tener un homólogo de Britta Simon en Wizergos Productivity Software que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Wizergos Productivity Software.

**Para configurar el inicio de sesión único de Azure AD con Wizergos Productivity Software, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de aplicaciones de **Wizergos Productivity Software**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6]
2. En la página **¿Cómo desea que los usuarios inicien sesión en Wizergos Productivity Software?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_03.png)
3. En la página de diálogo **Configurar las opciones de la aplicación**, si desea configurar la aplicación en el **modo iniciado por el proveedor de identidades**, siga estos pasos y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_04.png)
   
    a. En el cuadro de texto **Identificador**, escriba `https://www.wizergos.net`.
   
    b. En el cuadro de texto **URL de respuesta**, escriba: `https://www.wizergos.net/register_users/saml2/`
   
    c. Haga clic en **Siguiente**.
4. Si quiere configurar la aplicación en el **modo iniciado por el proveedor de servicios**, en la página de diálogo **Configurar las opciones de la aplicación**, haga clic en **"Mostrar la configuración avanzada (opcional)"** y después escriba la **URL de inicio de sesión** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_05.png)
   
    a. En el cuadro de texto **URL de inicio de sesión**, escriba `https://www.wizergos.net`.
   
    b. Haga clic en **Siguiente**.
5. En la página **Configurar inicio de sesión único en Wizergos Productivity Software**, lleve a cabo estos pasos y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_06.png)
   
    a. Haga clic en **Descargar certificado** y después guarde el archivo en el equipo.
   
    b. Haga clic en **Next**.
6. En otra ventana del explorador web, inicie sesión en su inquilino de Wizergos Productivity Software como administrador.
7. En el menú lateral, seleccione **Admin**.
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)
8. En el menú de la izquierda de la página Admin, seleccione **AUTHENTICACIÓN** y haga clic en **Azure AD**.
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)
9. En la sección **AUTHENTICACIÓN**, realice los pasos siguientes:
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
   
    a. Abra el certificado descargado de Azure AD y copie la huella digital del certificado. A continuación, péguelo en el cuadro de texto **Huella digital de certificado**.
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_004.png)
   
    b. En el cuadro de texto **URL del emisor**, coloque el valor de **URL del emisor** del Asistente para configuración de aplicaciones de Azure AD.
   
    c. En el cuadro de texto **Dirección URL de inicio de sesión único**, coloque el valor de **Dirección URL del servicio de inicio de sesión único** en el Asistente para configuración de aplicaciones de Azure AD.
   
    d. En el cuadro de texto **Single Sign-Out URL** (Dirección URL de cierre de sesión único), coloque el valor de **Dirección URL del servicio de cierre de sesión único** en el Asistente para configuración de aplicaciones de Azure AD.
   
    e. Haga clic en el botón **Guardar**.
   
   > [!NOTE]
   > Tenga en cuenta que la huella digital de certificado no debe contener ningún espacio entre medias.
   > 
   > 
10. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
    
     ![Inicio de sesión único de Azure AD][10]
11. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
    
     ![Inicio de sesión único de Azure AD][11]

### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_09.png)
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_03.png)
4. Para abrir el diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_04.png)
5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_05.png)
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.
   
    c. Haga clic en **Next**.
6. En la página de diálogo **Perfil de usuario**, realice los siguientes pasos:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_06.png)
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_07.png)
8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_08.png)
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.

### Creación de un usuario de prueba de Wizergos Productivity Software
En esta sección, creará un usuario llamado Britta Simon en Wizergos Productivity Software. Trabaje con el equipo de soporte técnico de Wizergos Productivity Software mediante [support@wizergos.com](emailTo:support@wizergos.com) para agregar los usuarios a la plataforma Wizergos Productivity Software.

### Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Wizergos Productivity Software.

   ![Asignar usuario][200]

**Para asignar a Britta Simon a Wizergos Productivity Software, realice los pasos siguientes:**

1. En el Portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **Wizergos Productivity Software**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_50.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### Prueba del inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Wizergos Productivity Software del panel de acceso, debería iniciar sesión automáticamente en su aplicación Wizergos Productivity Software.

## Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->