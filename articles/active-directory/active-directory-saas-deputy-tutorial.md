---
title: "Tutorial: Integración de Azure Active Directory con Deputy | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Deputy."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: c853d61257493d73cd0f8a51a15f0389e1e83cf4


---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Tutorial: integración de Azure Active Directory con Deputy
El objetivo de este tutorial es mostrar cómo integrar Deputy con Azure Active Directory (Azure AD).

Integrar Deputy con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Deputy.
* Puede permitir que los usuarios inicien sesión automáticamente en Deputy (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Deputy, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en Deputy

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

1. Adición de Deputy desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-deputy-from-the-gallery"></a>Adición de Deputy desde la galería
Para configurar la integración de Deputy en Azure AD, es preciso agregar Deputy desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Deputy desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Deputy**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_01.png)
7. En el panel de resultados, seleccione **Deputy** y haga clic en **Completar** para agregar la aplicación.
   
    ![Selección de la aplicación en la galería](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Deputy con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Deputy para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Deputy.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Deputy.

Para configurar y probar el inicio de sesión único de Azure AD con Deputy, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de usuario de prueba de Deputy](#creating-a-deputy-test-user)** : para tener un homólogo de Britta Simon en Deputy que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Deputy.

**Para configurar el inicio de sesión único de Azure AD con Deputy, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de aplicaciones de **Deputy**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en Deputy?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_03.png)
3. En el cuadro de diálogo **Configurar las opciones de la aplicación**, si desea configurar la aplicación en el **modo iniciado por el proveedor de identidades**, realice los pasos siguientes y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_04.png)
  1. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<your-subdomain>.<region>.deputy.com`.
  2. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<your-subdomain>.<region>.deputy.com/exec/devapp/samlacs`.
  3. Haga clic en **Siguiente**.
4. Si quiere configurar la aplicación en el **modo iniciado por el proveedor de servicios**, en la página de diálogo **Configurar las opciones de la aplicación**, haga clic en **"Mostrar la configuración avanzada (opcional)"**, escriba la **URL de inicio de sesión** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_05.png)
   1. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<your-subdomain>.<region>.deputy.com`.
   2. Haga clic en **Siguiente**.
   
   > [!NOTE]
   > El sufijo de la región de Deputy es opcional o debe usar uno de los siguientes: au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an
   > 
   > 
5. En la página **Configurar inicio de sesión único en Deputy**, realice los pasos siguientes y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_06.png)
   1. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
6. Vaya a la siguiente dirección URL: https://(your-subdomain).deputy.com/exec/config/system_config. Vaya a **Configuración de seguridad** y haga clic en **Editar**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_004.png)
7. En el Portal de Azure clásico, en la configuración de inicio de sesión único en la página de Deputy, copie la dirección URL de SSO de SAML. 
8. En esta página de **configuración de seguridad** , siga estos pasos.
   
![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_005.png)

   1. Habilite el **inicio de sesión social**.
   2. Abra el certificado codificado en base&64; en el Bloc de notas, copie su contenido en el portapapeles y luego péguelo en el cuadro de texto **Certificado OpenSSL**.
   3. En el cuadro de texto Dirección URL de inicio de sesión único de SAML, escriba `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
   4. En el cuadro de texto URL de SSO de SAM, reemplace `<your subdomain>` por el subdominio.
   5. En el cuadro de texto Dirección URL de inicio de sesión único de SAML, reemplace `<saml sso url>` por la URL de SSO de SAML que haya copiado desde el Portal de Azure clásico.
   6. Haga clic en **Guardar configuración**.
9. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
10. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
    
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_09.png)
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_03.png)
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_04.png)
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_05.png)

   1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_06.png)
   
   1. En el cuadro de texto **Nombre**, escriba **Britta**.  
   2. En el cuadro de texto **Apellidos**, escriba **Simon**.
   3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   4. En la lista **Rol**, seleccione **Usuario**.
   5. Haga clic en **Siguiente**.
   
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_07.png)
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-deputy-tutorial/create_aaduser_08.png)
   
   1. Anote el valor del campo **Nueva contraseña**.
   2. Haga clic en **Completo**.   

### <a name="creating-a-deputy-test-user"></a>Creación de usuario de prueba de Deputy
Para permitir que los usuarios de Azure AD inicien sesión en Deputy, deben aprovisionarse en Deputy. En el caso de Deputy, el aprovisionamiento es una tarea manual.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:
1. Inicie sesión en su sitio de la compañía de Deputy como administrador.
2. En la parte superior del panel de navegación, haga clic en **Contactos**.
   
   ![Personas](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_001.png "Personas")
3. Haga clic en el botón **Agregar personas** y en **Agregar una sola persona**.
   
   ![Agregar personas](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_002.png "Agregar personas")
4. Realice los pasos siguientes y haga clic en **Guardar e invitar**.
   
   ![Nuevo usuario](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_003.png "Nuevo usuario")
   
  1. En el cuadro de texto **Nombre**, escriba **Britta** y **Simon**.  
  2. En el cuadro de texto **Correo electrónico** , escriba la dirección de correo electrónico de la cuenta de Azure AD que quiera aprovisionar.
  3. En el cuadro de texto **Trabaja en** , escriba el nombre de la empresa.
  4. Haga clic en el botón **Guardar e invitar**.
   
   > [!NOTE]
   > El titular de la cuenta de AAD recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta para que se active. Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Deputy que proporcione Deputy para aprovisionar cuentas de usuario de AAD.
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Deputy.

![Asignar usuario][200]

**Para asignar a Britta Simon a Deputy, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **Deputy**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_50.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Deputy en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Deputy.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO1-->


