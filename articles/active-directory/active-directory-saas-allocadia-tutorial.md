---
title: "Tutorial: integración de Azure Active Directory con Allocadia | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Allocadia."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c415fc55-6dc1-49f2-a8a2-2fc6e3790d65
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d7307d4d1823f6422e62ebf2969c3d57bbe6f931


---
# <a name="tutorial-azure-active-directory-integration-with-allocadia"></a>Tutorial: Integración de Azure Active Directory con Allocadia
En este tutorial, obtendrá información sobre cómo integrar Allocadia con Azure Active Directory (Azure AD).

Integración de Allocadia con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Allocadia
* Puede permitir que los usuarios inicien sesión automáticamente en Allocadia (inicio de sesión único) con sus cuentas de Azure AD
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Allocadia, se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en Allocadia

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Allocadia desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-allocadia-from-the-gallery"></a>Incorporación de Allocadia desde la galería
Para configurar la integración de Allocadia en Azure AD, es preciso agregar Allocadia desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Allocadia desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Allocadia**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_01.png)
7. En el panel de resultados, seleccione **Allocadia** y, después, haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_06.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Allocadia con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Allocadia para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Allocadia.
Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en Allocadia.

Para configurar y probar el inicio de sesión único de Azure AD con Allocadia, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Allocadia](#creating-an-allocadia-test-user)** : para tener un homólogo de Britta Simon en Allocadia que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Allocadia.

La aplicación Allocadia espera las aserciones de SAML en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar el valor de estos atributos desde la pestaña **"Atributo"** de la aplicación. La siguiente captura de pantalla le muestra un ejemplo de esto. 

![Configurar inicio de sesión único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_07.png) 

**Para configurar el inicio de sesión único de Azure AD con Hightail, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de la aplicación **Allocadia**, en el menú de la parte superior, haga clic en **Atributos**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-allocadia-tutorial/tutorial_general_80.png) 
2. En el cuadro de diálogo **Atributos de token de SAML** , para cada fila de la tabla siguiente, realice los pasos que se indican a continuación:
   
   | Nombre del atributo | Valor de atributo |
   | --- | --- |
   | firstname |user.givenname |
   | lastname |user.surname |
   | email |user.mail |

    a. Haga clic en **agregar atributo de usuario** para abrir el cuadro de diálogo **Agregar atributo de usuario**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-allocadia-tutorial/tutorial_general_81.png) 


    b. En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo que se muestra para esa fila.

    c. En la lista **Valor de atributo** , seleccione el valor de atributo que se muestra para esa fila.

    d. Haga clic en **Completo**.    


1. En el menú de la parte superior, haga clic en **Inicio rápido**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-allocadia-tutorial/tutorial_general_83.png)  
2. En la página **¿Cómo desea que los usuarios inicien sesión en Allocadia?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_03.png) 
3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_04.png) 
   
    a. En el cuadro IDENTIFICADOR, escriba la dirección URL con el siguiente patrón: para el entorno de prueba, utilice la siguiente dirección URL **"https://na2standby.allocadia.com"**, mientras que para el entorno de producción use **"https://na2.allocadia.com"**
   
    b. En el cuadro URL de respuesta, escriba la dirección URL con el siguiente patrón: para el entorno de prueba, utilice la siguiente dirección URL **"https://na2standby.allocadia.com/allocadia/saml/SSO"**, mientras que para el entorno de producción use **"https://na2.allocadia.com/allocadia/saml/SSO"**
4. En la página **Configurar inicio de sesión único en Allocadia** , siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_05.png) 
   
    a. Haga clic en **Descargar metadatos**y luego guarde el archivo en el equipo.
   
    b. Haga clic en **Next**.
5. Para tener SSO configurado para la aplicación, póngase en contacto con el equipo de [soporte técnico Allocadia](mailTo:support@allocadia.com) , que le ayudará a configurar SSO. Tenga en cuenta que tendrá que enviar correo electrónico y adjuntar el archivo de metadatos descargado para configurar SSO en el lado de Allocadia.
   
   > [!NOTE]
   > Asegúrese de que el equipo de Allocadia establezca el valor de Identificador en el entorno de prueba como **"https://na2standby.allocadia.com"**, mientras que para el entorno de producción, debería ser: **"https://na2.allocadia.com"**
   > 
   > 
6. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.
En la lista Usuarios, seleccione **Britta Simon**.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_06.png) 
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-allocadia-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-an-allocadia-test-user"></a>Creación de un usuario de prueba de Allocadia
En esta sección, creará un usuario denominado Britta Simon en Allocadia. Compatibilidad de aplicaciones de Allocadia para el aprovisionamiento de usuarios justo a tiempo. Si ha configurado las notificaciones como se indicaron anteriormente en la sección **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** , se proporcionarán los usuarios de la aplicación. 

> [!NOTE]
> Si necesita crear un usuario de forma manual o por lotes de los usuarios, póngase en contacto con el equipo de soporte técnico de Allocadia.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Allocadia.

![Asignar usuario][200] 

**Para asignar Britta Simon a Allocadia, realice los pasos siguientes:**

1. En el Portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **Allocadia**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-allocadia-tutorial/tutorial_allocadia_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.
Al hacer clic en el icono de Allocadia en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Allocadia.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-allocadia-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


