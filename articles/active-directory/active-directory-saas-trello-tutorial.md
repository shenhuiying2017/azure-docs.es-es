---
title: "Tutorial: integración de Azure Active Directory con Trello | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Trello."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: 39ea5cc19e73cb6e9ea6da9757f5e8013d1a5ffe
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Tutorial: integración de Azure Active Directory con Trello

En este tutorial, obtendrá información sobre cómo integrar Trello con Azure Active Directory (Azure AD).

Integrar Trello con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Trello.
- Puede permitir que los usuarios inicien sesión automáticamente en Trello (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Trello, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en Trello

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Trello desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-trello-from-the-gallery"></a>Adición de Trello desde la galería
Para configurar la integración de Trello en Azure AD, deberá agregar Trello desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Trello desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Trello**, seleccione **Trello** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Trello en la lista de resultados](./media/active-directory-saas-trello-tutorial/tutorial_trello_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Trello con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Trello para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Trello.

Para establecer la relación de vínculo, en Trello, asigne el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Trello, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de usuario de prueba de Trello](#create-a-trello-test-user)**: para tener un homólogo de Britta Simon en Trello que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en su aplicación Trello.

>[!NOTE]
>Debe obtener el campo de datos dinámico **\<enterprise\>** desde Trello. Si no tiene el valor del campo de datos dinámico, póngase en contacto con el [equipo de soporte técnico de Trello](mailto:support@trello.com) para obtenerlo.
    > 

**Para configurar el inicio de sesión único de Azure AD con Trello, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Trello**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_samlbase.png)

3. En la sección **Dominio y direcciones URL de Trello**, si quiere configurar la aplicación en el modo iniciado por **IDP**, realice los siguientes pasos:

    ![Información de dominio y direcciones URL de inicio de sesión único de Trello](./media/active-directory-saas-trello-tutorial/tutorial_trello_url.png)
    
    a. En el cuadro de texto **Identificador**, escriba la siguiente dirección URL: `https://trello.com/auth/saml/metadata`
    
    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://trello.com/auth/saml/consume/<enterprise>`.

4. Si quiere configurar la aplicación en **modo iniciado por SP**, realice los siguientes pasos:

    ![Información de dominio y direcciones URL de inicio de sesión único de Trello](./media/active-directory-saas-trello-tutorial/tutorial_trello_url1.png)

    a. Active **Mostrar configuración avanzada de URL**.

    b. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://trello.com/auth/saml/login/<enterprise>`. 

5. La aplicación de Trello espera que las aserciones SAML contengan un atributo denominado "email". Configure los siguientes atributos para esta aplicación. Puede administrar el valor de estos atributos desde la pestaña "**Atributos de usuario**" de la aplicación. La siguiente captura de pantalla le muestra un ejemplo de esto.

    ![Configurar inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_attribute.png)

6. En el cuadro de diálogo **Atributos de token de SAML** , para cada fila de la tabla siguiente, realice los pasos que se indican a continuación:
 
    | Nombre del atributo | Valor de atributo |
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_officespace_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila. 

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**. 

7. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-trello-tutorial/tutorial_trello_certificate.png) 

8. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_general_400.png)
    
9. En la sección **Configuración de Trello**, haga clic en **Configurar Trello** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configuración de Trello](./media/active-directory-saas-trello-tutorial/tutorial_trello_configure.png) 

10. Para configurar el inicio de sesión único en **Trello**, es preciso ir a la página de [configuración SSO empresarial de Trello](https://trello.com/sso-configuration) para enviar el **certificado (Base64)** descargado y la **dirección URL del servicio de inicio de sesión único de SAML** al [equipo de soporte técnico de Trello](mailto:support@trello.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-trello-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-trello-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-trello-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-trello-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-trello-test-user"></a>Creación de usuario de prueba de Trello

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Trello. Trello admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a Trello, se crea un nuevo usuario, en caso de que no exista.

>[!Note]
>Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de Trello](mailto:support@trello.com).


### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Trello.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a Trello, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Trello**.

    ![Vínculo a Trello en la lista de aplicaciones](./media/active-directory-saas-trello-tutorial/tutorial_trello_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Trello en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Trello.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-trello-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trello-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trello-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trello-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-trello-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trello-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trello-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-trello-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-trello-tutorial/tutorial_general_203.png

