---
title: "Tutorial: Integración de Azure Active Directory con Marketo | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Marketo."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1a2ff324cfa65977685f6a0eee9cd398b0717519
ms.openlocfilehash: 20f3a8b006e45e3a94e95b516bca292a82c5fd03


---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Tutorial: integración de Azure Active Directory con Marketo
En este tutorial, aprenderá a integrar Marketo con Azure Active Directory (Azure AD).

Integrar Marketo con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Marketo.
* Puede permitir que los usuarios inicien sesión automáticamente en Marketo (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Marketo, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en Marketo

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Marketo desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-marketo-from-the-gallery"></a>Incorporación de Marketo desde la galería
Para configurar la integración de Marketo en Azure AD, deberá agregar Marketo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Marketo desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Marketo**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_01.png)
7. En el panel de resultados, seleccione **Marketo** y haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Marketo con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Marketo para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Marketo.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Marketo.

Para configurar y probar el inicio de sesión único de Azure AD con Marketo, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Marketo](#creating-a-predictix-price-reporting-test-user)**: para tener un homólogo de Britta Simon en Marketo que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Marketo.

**Para configurar el inicio de sesión único de Azure AD con Marketo, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de aplicaciones de **Marketo**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en Marketo?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_03.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_04.png) 
   
    a. En el cuadro de texto **Identificador**, escriba la dirección URL con el siguiente patrón: `https://saml.marketo.com/sp`.
   
    b. En el cuadro de texto **URL de respuesta**, escriba la dirección URL con el siguiente patrón: `https://login.marketo.com/saml/assertion/\<munchkinid\>`.
   
    c. click **Siguiente**
4. En la página **Configurar inicio de sesión único en Marketo** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_05.png)
   
    a. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
   
    b. Haga clic en **Siguiente**.
5. Para obtener el identificador de Munchkin de la aplicación, inicie sesión con credenciales de administrador de Marketo y realizar las siguientes acciones:
   
    a. Inicie sesión en la aplicación de Marketo con credenciales de administrador.
   
    b. Haga clic en el botón Administrador en el panel de navegación superior.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Vaya al menú Integración y haga clic en el vínculo Munchkin
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copie el identificador de Munchkin mostrado en la pantalla y complete la dirección URL de respuesta en el asistente para configuración de Azure AD.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png)
6. Para configurar el SSO en la aplicación, siga estos pasos:
   
    a. Inicie sesión en la aplicación de Marketo con credenciales de administrador.
   
    b. Haga clic en el botón Administrador en el panel de navegación superior.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Vaya al menú Integración y haga clic en Inicio de sesión único.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Para habilitar la configuración de SAML, haga clic en el botón Editar.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Habilite** el inicio de sesión único.
   
    f. Escriba el identificador del emisor, que ha copiado desde el asistente para configuración de Azure AD.
   
    g. En el cuadro de texto Id. de entidad, escriba la dirección URL como **http://saml.marketo.com/sp**
   
    h. Seleccione la ubicación del identificador de usuario como **elemento Identificador de nombre**
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Si su identificador de usuario no es el valor UPN, cambie el valor en la pestaña Atributo.
    > 
    > 
   
    i. Cargue el certificado que descargó en el asistente para configuración de Azure AD. Guarde la configuración
   
    j. Edite la configuración de Página de redireccionamiento.
   
    k. Copie la dirección URL de inicio de sesión del asistente para configuración de Azure AD en el cuadro de texto **URL de inicio de sesión** .
   
    l. Copie la dirección URL de cierre de sesión del Asistente para configuración de Azure AD en el cuadro de texto **URL de cierre de sesión** .
   
    m. En URL del error, copie la dirección URL de la instancia de Marketo y haga clic en el botón Guardar para guardar la configuración.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

7. Para habilitar el SSO para los usuarios, complete las siguientes acciones:
   
    a. Inicie sesión en la aplicación de Marketo con credenciales de administrador.
   
    b. Haga clic en el botón **Administrador** en el panel de navegación superior.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Desplácese hasta el menú **Seguridad** y haga clic en **Login Settings** (Configuración de inicio de sesión). 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)
   
    d. Active la opción **Require SSO** (Requerir SSO) y guarde la configuración.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)
8. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
9. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 
5. En el cuadro de diálogo **Proporcione información sobre este usuario**, siga estos pasos:  ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En el cuadro de diálogo **Perfil de usuario**, siga estos pasos:  ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_06.png) 
   
    a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
    b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
    d. En la lista **Rol**, seleccione **Usuario**.
   
    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-marketo-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-an-marketo-test-user"></a>Creación de un usuario de prueba de Marketo
En esta sección, creará un usuario llamado Britta Simon en Marketo. Siga estos pasos para crear un usuario en la plataforma Marketo.

1. Inicie sesión en la aplicación de Marketo con credenciales de administrador.
2. Haga clic en el botón **Administrador** en el panel de navegación superior.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
3. Desplácese hasta el menú **Seguridad** y haga clic en **Usuarios y roles**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  
4. Haga clic en el vínculo **Invitar a un usuario nuevo** en la pestaña Usuarios.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 
5. En el asistente Invitar a un usuario nuevo, rellene la siguiente información.
   
    a. Especifique la dirección de **Correo electrónico** del usuario en el cuadro de texto.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)
   
    b. Escriba el **Nombre** en el cuadro de texto.
   
    c. Escriba los **Apellidos** en el cuadro de texto.
   
    d. Haga clic en Siguiente.
6. En la pestaña **Permisos** , seleccione los Roles de usuario y haga clic en Siguiente.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)
7. Haga clic en el botón Enviar para enviar la invitación al usuario.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)
8. El usuario recibirá la notificación de correo electrónico y tendrá que hacer clic en el vínculo y cambiar la contraseña para activar la cuenta. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Marketo.

![Asignar usuario][200] 

**Para asignar Britta Simon a Marketo, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **Marketo**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Marketo en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Marketo.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO3-->


