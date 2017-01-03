---
title: "Tutorial: Integración de Azure Active Directory con Questetra BPM Suite | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Questetra BPM Suite."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a17069b9992e36b36bad71941b0f8062d7594e95
ms.openlocfilehash: 74959c5694ebb2a6d83b76b76f69d2fd0bb1d885


---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Tutorial: Integración de Azure Active Directory con Questetra BPM Suite
El objetivo de este tutorial es mostrar cómo integrar Questetra BPM Suite con Azure Active Directory (Azure AD).  
La integración de Questetra BPM Suite con Azure AD proporciona las siguientes ventajas: 

* Puede controlar en Azure AD quién tiene acceso a Questetra BPM Suite 
* Puede permitir que los usuarios inicien sesión automáticamente en Questetra BPM Suite (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Questetra BPM Suite, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en [Questetra BPM Suite](https://senbon-imadegawa-988.questetra.net/)

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.  
El escenario descrito en este tutorial consta de tres bloques de creación principales:

1. Incorporación de Questetra BPM Suite desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Incorporación de Questetra BPM Suite desde la galería
Para configurar la integración de Questetra BPM Suite en Azure AD, deberá agregar Questetra BPM Suite desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Questetra BPM Suite desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1]

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Questetra BPM Suite**.
   
    ![Aplicaciones][5]

7. En el panel de resultados, seleccione **Questetra BPM Suite** y, después, haga clic en **Completar** para agregar la aplicación.

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Questetra BPM Suite con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Questetra BPM Suite para un usuario de Azure AD. Es decir, se requiere establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Questetra BPM Suite.  
Para establecer esta relación de vínculo, se asigna el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Questetra BPM Suite.

Para configurar y probar el inicio de sesión único de Azure AD con Questetra BPM Suite, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Questetra BPM Suite](#creating-a-questetra-bpm-suite-test-user)** : para tener un homólogo de Britta Simon en Questetra BPM Suite que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Questetra BPM Suite.

**Para configurar el inicio de sesión único de Azure AD con Questetra BPM Suite, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Questetra BPM Suite**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][8]

2. En la página **¿Cómo desea que los usuarios inicien sesión en Questetra BPM Suite?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][9]

3. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de **Questetra BPM Suite** como administrador.

4. En el menú de la parte superior, haga clic en **Configuración del sistema**. 
   
    ![Inicio de sesión único de Azure AD ][10]

5. Para abrir la página **SingleSignOnSAML**, haga clic en **SSO (SAML)**. 
   
    ![Inicio de sesión único de Azure AD ][11]

6. En el Portal de Azure clásico, en la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes: 
   
    ![Configurar las opciones de la aplicación][13]
   
    a. En el sitio de la compañía de **Questetra BPM Suite**, en la sección de información de SP, copie la **URL de ACS** y, después, péguela en el cuadro de texto **URL de inicio de sesión**.
   
    b. En el sitio de la compañía de **Questetra BPM Suite**, en la sección de información de SP, copie el valor de **Entity ID** (Id. de entidad) y, después, péguelo en el cuadro de texto **URL del emisor**.
   
    c. En el sitio de la compañía de **Questetra BPM Suite**, en la sección de información de SP, copie el valor de **URL de ACS** y, después, péguelo en el cuadro de texto **URL de respuesta**.
   
    d. Haga clic en **Siguiente**.

7. En la página **Configurar inicio de sesión único en Questetra BPM Suite**, haga clic en **Descargar certificado** y guarde el archivo de certificado de forma local en el equipo.
   
    ![Configurar inicio de sesión único][14]

8. En el sitio de la compañía **Questetra BPM Suite** , realice los pasos siguientes: 
   
    ![Configurar inicio de sesión único][15]
   
    a. Seleccione **Enable Single Sign-On**(Habilitar inicio de sesión único).
   
    b. En el Portal de Azure clásico, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Id. de entidad**.
   
    c. En el Portal de Azure clásico, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Dirección URL de la página de inicio de sesión**.
   
    d. En el Portal de Azure clásico, copie el valor de **Dirección URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **Dirección URL de la página de cierre de sesión**.
   
    e. En el cuadro de texto **NameID format** (Formato de id. de nombre), escriba **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    f. Cree un archivo codificado en base 64 a partir del certificado descargado. 

    >[!TIP] 
    >Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    g. Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y, a continuación, péguelo en el cuadro de texto **Certificado de validación** . 

    h. Haga clic en **Save**.

1. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. 
   
    ![Qué es Azure AD Connect][17]

2. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Qué es Azure AD Connect][18]


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD][100] 

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD][101] 

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. 
   
    ![Creación de un usuario de prueba de Azure AD][102] 

5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD][103]
   
    a. En **Tipo de usuario**, seleccione **Nuevo usuario de la organización**.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en Siguiente.

6. En la página de diálogo **Perfil de usuario** , realice los siguientes pasos: 
   
    ![Creación de un usuario de prueba de Azure AD][104] 
   
    a. En el cuadro de texto **Nombre**, escriba **Britta**. 
   
    b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
    d. En la lista **Rol**, seleccione **Usuario**.
   
    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD][105]  

8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD][106]   
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-questetra-bpm-suite-test-user"></a>Creación de un usuario de prueba de Questetra BPM Suite
El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Questetra BPM Suite.

**Para crear un usuario llamado Britta Simon en Questetra BPM Suite, realice los pasos siguientes:**

1. Inicie sesión en el sitio de la compañía de Questetra BPM Suite como administrador.
2. Vaya a **System Settings > User List > New User** (Configuración del sistema > Lista de usuarios > Nuevo usuario). 
3. En el cuadro de diálogo Nuevo usuario, realice los pasos siguientes: 
   
    ![Creación de un usuario de prueba][300] 
   
    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de usuario de Britta en Azure AD.
   
    b. En el cuadro de texto **Email** (Correo electrónico), escriba el nombre de usuario de Britta en Azure AD.
   
    c. En el cuadro de texto **Password** (Contraseña), escriba una contraseña.

4. Haga clic en **Add new user**(Agregar nuevo usuario).

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure al concederle acceso a Questetra BPM Suite.

![Qué es Azure AD Connect][200]

**Para asignar Britta Simon a Questetra BPM Suite, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en la opción **Aplicaciones** del menú superior.
   
    ![Qué es Azure AD Connect][201]
2. En la lista de aplicaciones, seleccione **Questetra BPM Suite**.
   
    ![Qué es Azure AD Connect][205]
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Qué es Azure AD Connect][202]
4. En la lista Usuarios, seleccione **Britta Simon**.
   
    ![Qué es Azure AD Connect][203]
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Qué es Azure AD Connect][204]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  
Al hacer clic en el icono Questetra BPM Suite en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de Questetra BPM Suite.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_09.png 
[101]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_10.png 
[102]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_11.png 
[103]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_12.png 
[104]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_13.png 
[105]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_14.png 
[106]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_15.png 


[200]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_16.png 
[201]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_17.png 
[202]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 



<!--HONumber=Jan17_HO1-->


