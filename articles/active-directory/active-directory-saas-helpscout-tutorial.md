---
title: "Tutorial: Integración de Azure Active Directory con Help Scout | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Help Scout."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 84cee39c28a0f7e6b9878441e504131795673020
ms.contentlocale: es-es
ms.lasthandoff: 08/28/2017

---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Tutorial: integración de Azure Active Directory con Help Scout

En este tutorial, obtendrá información sobre cómo integrar Help Scout con Azure Active Directory (Azure AD).

La integración de Help Scout con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a Help Scout.
- Puede iniciar sesión automáticamente con los usuarios de Help Scout mediante un inicio de sesión único y una cuenta de Azure AD del usuario.
- Puede administrar sus cuentas en una única ubicación central: Azure Portal.

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Help Scout, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de Help Scout, con inicio de sesión único activado 

> [!NOTE]
> Si va a probar los pasos de este tutorial, no se recomienda hacerlo en un entorno de producción.

Recomendaciones para probar los pasos de este tutorial:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba gratuita durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. 

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Help Scout desde la galería.
2. Configuración y prueba del inicio de sesión único en Azure AD.

## <a name="add-help-scout-from-the-gallery"></a>Incorporación de Help Scout desde la galería
Para configurar la integración de Help Scout con Azure AD, en la galería, agréguelo a la lista de aplicaciones SaaS administradas.

Para agregar Help Scout desde la galería:

1. En el menú izquierdo de [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Página Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Help Scout**. En los resultados de búsqueda, seleccione **Help Scout** y, luego, **Agregar**.

    ![Help Scout en la lista de resultados](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Help Scout mediante un usuario de prueba llamado *Britta Simon*.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Azure AD en Help Scout. Es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Help Scout.

Para establecer la relación de vínculo, en Help Scout, asigne el valor de **nombre de usuario** como valor de **nombre de usuario** de Azure AD.

Para configurar y probar el inicio de sesión único de Azure AD con Help Scout, es preciso completar las siguientes tareas:

1. [Configuración del inicio de sesión único de Azure AD](#set-up-azure-ad-single-sign-on). Configura un usuario para usar esta característica.
2. [Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user). Prueba un inicio de sesión único de Azure AD con el usuario Britta Simon.
3. [Creación de un usuario de prueba de Help Scout](#create-a-help-scout-test-user). Crea un homólogo de Britta Simon en Help Scout que está vinculado a la representación del usuario en Azure AD.
4. [Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user). Configura Britta Simon para usar un inicio de sesión único de Azure AD.
5. [Prueba de inicio de sesión único](#test-single-sign-on). Comprueba que la configuración funciona.

### <a name="set-up-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, configura el inicio de sesión único de Azure AD en Azure Portal. A continuación, configura el inicio de sesión único en la aplicación Help Scout.

Para configurar el inicio de sesión único en Azure AD con Help Scout:

1. En Azure Portal, en la página de integración de la aplicación **Help Scout**, seleccione **Inicio de sesión único**.
 
    ![Configuración de vínculo de inicio de sesión único][4]

2. En la página **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML**.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. En **Dominio y direcciones URL de Help Scout**, si quiere configurar la aplicación en el modo iniciado por IDP, complete los siguientes pasos:

    1. En el cuadro **Identificador**, escriba una dirección URL con el siguiente formato: `urn:auth0:helpscout:<instancename>`

    2. En el cuadro **URL de respuesta**, escriba una dirección URL con el siguiente formato: `https://helpscout.auth0.com/login/callback?connection=<instancename>`

    ![Información de dominio y direcciones URL de inicio de sesión único de Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

4. Si desea configurar la aplicación con el modo SP iniciado, seleccione la casilla **Mostrar configuración avanzada de URL** y haga lo siguiente:

    * En el cuadro **URL de inicio de sesión**, escriba una dirección URL con el siguiente formato: `https://secure.helpscout.net/members/login/`

    ![Información de dominio y direcciones URL de inicio de sesión único de Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)
 
    > [!NOTE] 
    > Los valores de estas direcciones URL se muestran solo con fines demostrativos. Actualice los valores con las direcciones URL reales de identificador y de respuesta. Para obtener estos valores, póngase en contacto con el [equipo de soporte técnico de Help Scout](mailto:help@helpscout.com). 

5. En la sección **Certificado de firma de SAML**, seleccione **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. Seleccione **Guardar**.

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)
    
7. Para configurar un inicio de sesión único en Help Scout, envíe el archivo XML de metadatos descargado al [equipo de soporte técnico de Help Scout](mailto:help@helpscout.com). El equipo de soporte técnico de Help Scout aplica esta configuración para que la conexión de inicio de sesión único de SAML esté correctamente configurada en ambos lados.

> [!TIP]
> Puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación. Después de agregar la aplicación seleccionando **Active Directory** > **Aplicaciones empresariales**, elija la pestaña **Inicio de sesión único**. Puede acceder a la documentación insertada en la sección **Configuración** en la parte inferior de la página. Para más información, consulte la [documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, en Azure Portal, creará un usuario de prueba llamado Britta Simon.

![Creación de un usuario de prueba de Azure AD][100]

Para crear un usuario de prueba en Azure AD:

1. En el menú izquierdo de Azure Portal, seleccione **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, seleccione **Usuarios y grupos** y, luego, **Todos los usuarios**.

    ![Seleccionar Usuarios y grupos y, luego, Todos los usuarios](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. Para abrir el cuadro de diálogo **Usuario**, en la parte superior de la página **Todos los usuarios**, seleccione **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario**, complete los pasos siguientes:

    1. En el cuadro **Nombre**, escriba **BrittaSimon**.

    2. En el cuadro **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    3. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    4. Seleccione **Crear**.

        ![Cuadro de diálogo Usuario](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

 
### <a name="create-a-help-scout-test-user"></a>Creación de un usuario de prueba de Help Scout

El objeto de esta sección es crear un usuario llamado Britta Simon en Help Scout. Help Scout admite el aprovisionamiento Just-In-Time (JIT), que está activado de forma predeterminada.

En esta sección, no hay ninguna acción o tarea que completar. Si el usuario no existe en Help Scout, se crea uno nuevo cuando se intenta acceder a Help Scout.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará al usuario Britta Simon para que use el inicio de sesión único de Azure AD concediéndole acceso de cuenta de usuario a Help Scout.

![Asignación del rol de usuario][200] 

Para asignar a Britta Simon a Help Scout:

1. En Azure Portal, abra la vista de aplicaciones y vaya a la vista de directorio. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Help Scout**.

    ![Vínculo a Help Scout en la lista de aplicaciones](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. En el menú de la izquierda, seleccione **Usuarios y grupos**.

    ![Vínculo Usuarios y grupos][202]

4. Seleccione **Agregar**. Después, en la página **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Panel Agregar asignación][203]

5. En la página **Usuarios y grupos**, en la lista de usuarios, seleccione **Britta Simon**.

6. En la página **Usuarios y grupos**, seleccione **Seleccionar**.

7. En la página **Agregar asignación**, seleccione **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el panel de acceso.

Al seleccionar el icono de Help Scout en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Help Scout.

Para más información sobre el panel de acceso, consulte [Introducción al panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png


