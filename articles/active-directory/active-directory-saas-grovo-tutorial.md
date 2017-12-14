---
title: "Tutorial: Integración de Azure Active Directory con Grovo | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Grovo."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 399cecc3-aa62-4914-8b6c-5a35289820c1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: jeedes
ms.openlocfilehash: 9deb4c9bd6719e7cf86883fba1306c435de0ebb9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-grovo"></a>Tutorial: Integración de Azure Active Directory con Grovo

En este tutorial, aprenderá a integrar Grovo con Azure Active Directory (Azure AD).

La integración de Grovo con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Grovo.
- Puede permitir que los usuarios inicien sesión automáticamente en Grovo (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Grovo, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Grovo

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Grovo desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-grovo-from-the-gallery"></a>Adición de Grovo desde la galería
Para configurar la integración de Grovo en Azure AD, deberá agregar Grovo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Grovo desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Grovo**, seleccione **Grovo** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Grovo en la lista de resultados](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Grovo con un usuario de prueba llamado "Britta Simon."

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Grovo para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Grovo.

Para establecer la relación de vínculo, en Grovo, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Grovo, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Grovo](#create-a-grovo-test-user)** : para tener un homólogo de Britta Simon en Grovo que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Grovo.

**Para configurar el inicio de sesión único de Azure AD con Grovo, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Grovo**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_samlbase.png)

3. En la sección **Dominio y direcciones URL de Grovo**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.grovo.com/sso/saml2/metadata`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`.

4.  Active la casilla **Mostrar configuración avanzada de URL** y realice los siguientes pasos:  

    ![Información de dominio y direcciones URL de inicio de sesión único de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url1.png)

    a. En el cuadro de texto **Estado de la retransmisión**, escriba una dirección URL que siga este patrón:`https://<subdomain>.grovo.com`

    b. Si desea configurar la aplicación en modo iniciado por **SP**, realice los siguientes pasos:

    ![Información de dominio y direcciones URL de inicio de sesión único de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_url2.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    > [!NOTE] 
    > Estos valores no son reales. Actualícelos con el identificador real, la dirección URL de respuesta, la dirección URL de inicio de sesión y el estado de la retransmisión. Póngase en contacto con el [equipo de soporte técnico de Grovo](https://www.grovo.com/contact-us) para obtener estos valores.
 
5. La aplicación Grovo espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.
    
    ![Configuración del atributo de inicio de sesión único](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_attribute.png)
    
6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo token de SAML como muestra la imagen y siga estos pasos:
    
    | Nombre del atributo | Valor de atributo |
    | ------------------- | -------------------- |    
    | Nombre de usuario              | user.givenname |
    | Apellidos               | user.surname |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configuración del atributo de inicio de sesión único](./media/active-directory-saas-grovo-tutorial/tutorial_attribute_04.png)

    ![Configuración del atributo de inicio de sesión único](./media/active-directory-saas-grovo-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.


7. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_certificate.png) 

8. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-grovo-tutorial/tutorial_general_400.png)

9. En la sección **Configuración de Grovo**, haga clic en **Configurar Grovo** para abrir la ventana **Configurar inicio de sesión**. Copie **SAML Entity ID and SAML Single Sign-On Service URL** (URL del servicio de inicio de sesión único de SAML e Identificador de entidad de SAML) de la sección **Referencia rápida**.

    ![Configuración de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_configure.png) 

10. En otra ventana del explorador web, inicie sesión en Grovo como administrador.

11. Vaya a **ADMIN** (Administración)  > **Integrations** (Integraciones).
 
    ![Configuración de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_admin.png) 

12. Haga clic en **SET UP** (Configuración) en la sección **SP Initiated SAML 2.0** (SAML 2.0 iniciado por SP).

    ![Configuración de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_setup.png)

13. En la ventana emergente **SP Initiated SAML 2.0** (SAML 2.0 iniciado por SP), siga estos pasos:

    ![Configuración de Grovo](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_saml.png)

    a. En el cuadro de texto **Entity id** (Identificador de entidad), pegue el valor de **Identificador de entidad de SAML** que copió de Azure Portal.

    b. En el cuadro de texto **Single sign on service endpoint** (Punto de conexión del servicio de inicio de sesión único), pegue el valor de **Dirección URL del servicio de inicio de sesión único de SAML** que copió de Azure Portal.

    c. Seleccione **Single sign on service endpoint binding** (Enlace de punto de conexión del servicio de inicio de sesión único) como `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect`.
    
    d. Abra el **certificado codificado en Base64** descargado desde Azure Portal en el Bloc de notas y péguelo en el cuadro de texto **Public key** (Clave pública).

    e. Haga clic en **Siguiente**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-grovo-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-grovo-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-grovo-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-grovo-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
  
### <a name="create-a-grovo-test-user"></a>Creación de un usuario de prueba de Grovo

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Grovo. Grovo admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a Grovo, se crea un nuevo usuario, en caso de que no exista.
>[!Note]
>Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de Grovo](https://www.grovo.com/contact-us).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Grovo.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Grovo, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Grovo**.

    ![Vínculo a Grovo en la lista de aplicaciones](./media/active-directory-saas-grovo-tutorial/tutorial_grovo_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Grovo en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Grovo.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-grovo-tutorial/tutorial_general_203.png

