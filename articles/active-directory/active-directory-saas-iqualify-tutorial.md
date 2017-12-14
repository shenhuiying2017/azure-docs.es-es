---
title: "Tutorial: Integración de Azure Active Directory con iQualify LMS | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory e iQualify LMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: 6f8a7b7fd155a6ad0df7cb1f9026b4acca2401cb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Tutorial: Integración de Azure Active Directory con iQualify LMS

En este tutorial, aprenderá a integrar iQualify LMS con Azure Active Directory (Azure AD).

La integración de iQualify LMS con Azure AD ofrece las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a iQualify LMS.
- Puede permitir que los usuarios inicien sesión automáticamente en iQualify LMS (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con iQualify LMS, necesita lo siguiente:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en iQualify LMS

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de iQualify LMS desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-iqualify-lms-from-the-gallery"></a>Incorporación de iQualify LMS desde la galería
Para configurar la integración de iQualify LMS en Azure AD, será preciso que agregue iQualify LMS desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar iQualify LMS desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **iQualify LMS**, seleccione **iQualify LMS** en el panel de resultados y haga clic en el botón **Agregar** para agregar la aplicación.

    ![iQualify LMS en la lista de resultados](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con iQualify LMS con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de iQualify LMS para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de iQualify LMS.

Para establecer la relación de vínculo en iQualify LMS, asigne el valor de **nombre de usuario** de Azure AD como valor del **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con iQualify LMS, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de iQualify LMS](#create-an-iqualify-lms-test-user)**: para tener un homólogo de Britta Simon en iQualify LMS que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación iQualify LMS.

**Para configurar el inicio de sesión único de Azure AD con iQualify LMS, siga estos pasos:**

1. En la página de integración de la aplicación **iQualify LMS** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_samlbase.png)

3. En la sección **Dominio y direcciones URL de iQualify LMS**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por IDP:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: 
    | |
    |--|--|
    | Entorno de producción: `https://<yourorg>.iqualify.com/`|
    | Entorno de prueba: `https://<yourorg>.iqualify.io`|
    
    b. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con el siguiente patrón: 
    | |
    |--|--|
    | Entorno de producción: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Entorno de prueba: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:
    | |
    |--|--|
    | Entorno de producción: `https://<yourorg>.iqualify.com/login` |
    | Entorno de prueba: `https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de iQualify LMS](https://www.iqualify.com) para obtener estos valores. 

5. La aplicación iQualify LMS espera que las aserciones del Lenguaje de marcado de aserción de seguridad (SAML) se muestren en un formato concreto. Configure las notificaciones y administre los valores de los atributos en la sección **Atributos de usuario** de la página de integración de la aplicación iQualify, tal y como se muestra en la siguiente captura de pantalla:
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-iqualify-tutorial/atb.png)

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, para cada fila que se muestra en la tabla siguiente, realice los pasos siguientes:
    
    | Nombre del atributo | Valor de atributo |
    | --- | --- |    
    | email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "su atributo" | 

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-iqualify-tutorial/atb2.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-iqualify-tutorial/atb3.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.

    e. Repita los pasos "a" a "d" para las filas de la tabla siguientes. 

    > [!Note]
    > La repetición de los pasos "a" a "d" para el atributo **person_id** es **opcional**

7. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_certificate.png) 

8. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-iqualify-tutorial/tutorial_general_400.png)
    
9. En la sección **Configuración de iQualify LMS**, haga clic en **Configurar iQualify LMS** para abrir la ventana **Configurar inicio de sesión**. Copie los valores **Sign-Out URL y SAML Single Sign-On Service URL** (Dirección URL de cierre de sesión y Dirección URL del servicio de inicio de sesión único de SAML) de la **sección de referencia rápida**.

    ![Configuración de iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_configure.png) 

10.  Abra una nueva ventana del explorador e inicie sesión en el entorno de iQualify como administrador.

11. Una vez que haya iniciado sesión, haga clic en su avatar en la esquina superior derecha, haga clic en **"Configuración de la cuenta".**

    ![Configuración de cuenta](./media/active-directory-saas-iqualify-tutorial/setting1.png) 
12. En el área de configuración de cuenta, haga clic en el menú de la cinta de opciones de la izquierda y haga clic en **"INTEGRACIONES".**
    
    ![INTEGRACIONES](./media/active-directory-saas-iqualify-tutorial/setting2.png)

13. En INTEGRACIONES, haga clic en el icono de **SAML**.

    ![Icono de SAML](./media/active-directory-saas-iqualify-tutorial/setting3.png)

14. En el cuadro de diálogo **Configuración de la autenticación SAML** , realice los pasos siguientes:

    ![Configuración de la autenticación SAML](./media/active-directory-saas-iqualify-tutorial/setting4.png)

    a. En el cuadro **SAML SINGLE SIGN-ON SERVICE URL** (Dirección URL del servicio de Inicio de sesión único SAML), pegue el valor de **Dirección URL del servicio de inicio de sesión único de SAML** que copió de la ventana de configuración de la aplicación Azure AD.
    
    b. En el cuadro **SAML LOGOUT URL** (Dirección URL del cierre de sesión de SAML), pegue el valor de **Dirección URL del cierre de sesión** que copió de la ventana de configuración de la aplicación Azure AD.
    
    c. Abra el certificado descargado en el Bloc de notas, copie el contenido y péguelo en el cuadro **CERTIFICADO PÚBLICO**.
    
    d. En **LOGIN BUTTON LABEL** (Etiqueta del botón de inicio de sesión) escriba el nombre del botón que se mostrará en la página de inicio de sesión.
    
    e. Haga clic en **GUARDAR**.

    f. Haga clic en **ACTUALIZAR**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-iqualify-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-iqualify-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-iqualify-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-iqualify-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-an-iqualify-lms-test-user"></a>Creación de un usuario de prueba de iQualify LMS

En esta sección, se crea un usuario llamado a Britta Simon en iQualify. iQualify LMS admite el aprovisionamiento de usuario Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe en aún en iQualify LMS, se crea uno nuevo cuando se intenta acceder a iQualify LMS.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a iQualify LMS.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a iQualify LMS, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **iQualify LMS**.

    ![Vínculo a iQualify LMS en la lista de aplicaciones](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de iQualify LMS del panel de acceso, debería entrar en la página de inicio de sesión de la aplicación iQualify LMS. 

   ![página de inicio de sesión](./media/active-directory-saas-iqualify-tutorial/login.png) 

Haga clic en el botón **Sign in with Azure AD** (Iniciar sesión con Azure AD), con ello debería iniciar sesión automáticamente en su aplicación iQualify LMS.

Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_203.png

