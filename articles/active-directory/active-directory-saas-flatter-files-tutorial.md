---
title: "Tutorial: integración de Azure Active Directory con Flatter Files | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Flatter Files."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 488a3c5f0aa05c5b71bf5d72539cbc4b7c6de1b5
ms.openlocfilehash: 062878ad877b501ce7f0d5c4f8ce9ca939ffe64d
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Tutorial: integración de Azure Active Directory con Flatter Files
El objetivo de este tutorial es mostrar cómo integrar Flatter Files con Azure Active Directory (Azure AD).  

Integrar Flatter Files con Azure AD le proporciona las siguientes ventajas: 

* Puede controlar en Azure AD quién tiene acceso a Flatter Files. 
* Puede permitir que los usuarios inicien sesión automáticamente en el inicio de sesión único (SSO) de Flatter Files con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Flatter Files, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en Flatter Files

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.  

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Flatter Files desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-flatter-files-from-the-gallery"></a>Agregación de Flatter Files desde la galería
Para configurar la integración de Flatter Files en Azure AD, deberá agregar Flatter Files desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Flatter Files desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Flatter Files**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)

7. En el panel de resultados, seleccione **Flatter Files** y, luego, haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Flatter Files con una usuaria de prueba llamada "Britta Simon".

Para que el SSO funcione, Azure AD debe saber cuál es el usuario homólogo de Flatter Files para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Flatter Files.  

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Flatter Files.

Para configurar y probar el inicio de sesión único de Azure AD con Flatter Files, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Flatter Files](#creating-a-halogen-software-test-user)** : para tener un homólogo de Britta Simon en CS Stars que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure AD clásico y configurar el inicio de sesión único en la aplicación Flatter Files. Como parte de este procedimiento, se requiere crear un archivo de certificado codificado en base&64;. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).
Para configurar el inicio de sesión único para Flatter Files, se necesita un dominio registrado. Si no dispone de un dominio registrado, póngase en contacto con el equipo de soporte técnico de Flatter Files a través de [support@flatterfiles.com](mailto:support@flatterfiles.com).  

**Para configurar el inicio de sesión único de Azure AD con Flatter Files, realice los pasos siguientes:**

1. En el Portal de Azure AD clásico, en la página de integración de la aplicación **Flatter Files**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en Flatter Files?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.
       ![Configurar inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación**, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png) 
   >[!NOTE]
   >Flatter Files usa la misma dirección URL de inicio de sesión SSO para todos los clientes: [https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/).
   > 
   
4. En la página **Configurar inicio de sesión único en Flatter Files** , siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png)  
    1. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
    2. Haga clic en **Siguiente**.
5. Inicie sesión en su aplicación de Flatter Files como administrador.
6. Haga clic en Dashboard (Panel). 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)  
7. Haga clic en **Settings** (Configuración) y, después, siga estos pasos siguientes en la pestaña **Company** (Compañía): 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)  
    1. Seleccione **Usar SAML 2.0 para autenticación**.
    2. Haga clic en **Configurar SAML**.
8. En el cuadro de diálogo **Configuración de SAML** , realice los siguientes pasos: 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)  
   1. En el cuadro de texto Dominio, escriba el dominio registrado.
   
    >[!NOTE]
    >Si no dispone de un dominio registrado, póngase en contacto con el equipo de soporte técnico de Flatter Files a través de [support@flatterfiles.com] (mailto:support@flatterfiles.com). 
    >    
   2. En el Portal de Azure clásico, en el cuadro de diálogo Configurar inicio de sesión único en Flatter Files, copie el valor de la Dirección URL del servicio de inicio de sesión único y péguelo en el cuadro de texto Identity Provider URL (URL de proveedor de identidades).
   3.  Cree un archivo **codificado en base&64;** a partir del certificado descargado.  
 
   >[!TIP]
   >Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).
   >  
   4.  Abra el certificado codificado en base&64; en el Bloc de notas, copie su contenido en el Portapapeles y, a continuación, péguelo en el cuadro de texto **Certificado de proveedor de identidades de Flatter Files** .
   5. Haga clic en **Update**(Actualizar).
9. En el Portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. 
   
    ![Inicio de sesión único de Azure AD ][10]
10. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
    
     ![Inicio de sesión único de Azure AD ][11]

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes: 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)  
   1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes: 
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) 
   1. En el cuadro de texto **Nombre**, escriba **Britta**.  
   2. En el cuadro de texto **Apellidos**, escriba **Simon**.
   3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   4. En la lista **Rol**, seleccione **Usuario**.
   5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) 
   1. Anote el valor del campo **Nueva contraseña**.
   2. Haga clic en **Complete**.   

### <a name="create-a-flatter-files-test-user"></a>Creación de un usuario de prueba de Flatter Files
El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Flatter Files.

**Para crear una usuaria llamada Britta Simon en Flatter Files, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la compañía de **Flatter Files** como administrador.
2. En el panel de navegación de la izquierda, haga clic en **Settings** (Configuración) y, luego, en la pestaña **Users** (Usuarios).
   
    ![Creación de un usuario de Flatter Files](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)
3. Haga clic en **Agregar usuario**. 
4. En el cuadro de diálogo **Agregar usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de Flatter Files](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)
   1. En el cuadro de texto **Nombre**, escriba **Britta**.
   2. En el cuadro de texto **Apellidos**, escriba **Simon**. 
   3. En el cuadro de texto **Dirección de correo electrónico** , escriba la dirección de correo electrónico de Britta en el Portal de Azure clásico.
   4. Haga clic en **Enviar**.   

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Flatter Files.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Flatter Files, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **Flatter Files**.
   
    ![Asignar usuario](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  

Al hacer clic en el icono de Flatter Files en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Flatter Files.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png







