---
title: "Tutorial: Integración de Azure Active Directory con Workday | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Workday."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: jeedes
ms.openlocfilehash: 1dfe319e708e6a4e815413da1a7bf635f4d0a53d
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Tutorial: Integración de Azure Active Directory con Workday

En este tutorial, obtendrá información sobre cómo integrar Workday con Azure Active Directory (Azure AD).

La integración de Workday con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Workday.
- Puede permitir que los usuarios inicien sesión automáticamente en Workday (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Workday, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Workday

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Workday desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-workday-from-the-gallery"></a>Incorporación de Workday desde la galería
Para configurar la integración de Workday en Azure AD, hay que agregar Workday desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Workday desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Workday**, seleccione **Workday** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Workday en la lista de resultados](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, puede configurar y probar el inicio de sesión único de Azure AD con Workday con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Workday para un usuario de Azure AD. Es decir, hay que establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Workday.

Para establecer la relación de vínculo, en Workday, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Workday, hay que completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Workday](#create-a-workday-test-user)**: para tener un homólogo de Britta Simon en Workday que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en su aplicación Workday.

**Para configurar el inicio de sesión único de Azure AD con Workday, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Workday**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. En la sección **Dominio y direcciones URL de Workday**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Workday](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://impl.workday.com/<tenant>/login-saml2.htmld`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL como: `http://www.workday.com`

4. Active la casilla **Mostrar configuración avanzada de URL** y realice el siguiente paso:

    ![Información de dominio y direcciones URL de inicio de sesión único de Workday](./media/active-directory-saas-workday-tutorial/tutorial_workday_url1.png)

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://impl.workday.com/<tenant>/login-saml.htmld`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. La URL de respuesta debe tener un subdominio (por ejemplo, www, wd2, wd3, wd3-impl, wd5 y wd5-impl). Usar algo como "*http://www.myworkday.com*" funciona, pero "*http://myworkday.com*" no funciona. Póngase en contacto con el [equipo de soporte técnico de Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obtener estos valores.  

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)
    
7. En la sección **Configuración de Workday**, haga clic en **Configurar Workday** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de Workday](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 

8. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Workday como administrador.

9. En el **cuadro de búsqueda**, escriba el nombre **Edit Tenant Setup – Security** (Editar configuración de inquilino – Seguridad) en la parte superior izquierda de la página principal.
   
    ![Edición de seguridad del inquilino](./media/active-directory-saas-workday-tutorial/IC782925.png "Edición de seguridad del inquilino")

10. En la sección **URL de redireccionamiento** , siga estos pasos:
   
    ![Direcciones URL de redirección](./media/active-directory-saas-workday-tutorial/IC7829581.png "Direcciones URL de redirección")
   
    a. Haga clic en **Add Row**(Agregar fila).
   
    b. En los cuadros de texto **Dirección URL de redireccionamiento de inicio de sesión** y **URL de redireccionamiento móvil**, escriba la **dirección URL de inicio de sesión** que ha especificado en la sección **Dominio y direcciones URL de Workday** de Azure Portal.
   
    c. En la ventana **Configurar inicio de sesión único** de Azure Portal, copie la **dirección URL de cierre de sesión único** y péguela en el cuadro de texto **Logout Redirect URL** (URL de redireccionamiento de cierre de sesión).

    d. En el cuadro de texto **Used for Environments** (Se usa en entornos), seleccione el nombre del entorno.  

    >[!NOTE]
    > El valor del atributo Entorno está vinculado con el valor de la URL del inquilino:  
    >Si el nombre de dominio de la URL de inquilino de Workday empieza por impl (por ejemplo, *https://impl.workday.com/\<inquilino\>/login-saml2.htmld*), el atributo **Entorno** tiene que establecerse en Implementación.  
    >Si el nombre de dominio empieza por otra cosa, deberá ponerse en contacto con el [equipo de soporte técnico de Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obtener el valor de **Entorno** coincidente.

11. En la sección **Configuración de SAML** , realice los pasos siguientes:
   
    ![Configuración de SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "Configuración de SAML")
   
    a.  Seleccione **Enable SAML Authentication**(Habilitar autenticación SAML).
   
    b.  Haga clic en **Add Row**(Agregar fila).

12. En la sección **Proveedores de identidades SAML**, realice los pasos siguientes:
   
    ![Proveedores de identidades SAML](./media/active-directory-saas-workday-tutorial/IC7829271.png "Proveedores de identidades SAML")
   
    a. En el cuadro de texto **Nombre del proveedor de identidades**, escriba un nombre de proveedor (por ejemplo, *SPInitiatedSSO*).
   
    b. En la ventana **Configurar inicio de sesión único** de Azure Portal, copie el valor de **SAML Entity ID** (Identificador de entidad de SAML) y, luego, péguelo en el cuadro de texto **Emisor**.

    ![Proveedores de identidades SAML](./media/active-directory-saas-workday-tutorial/IC7829271(1).png "Proveedores de identidades SAML")
   
    c. Seleccione **Enable Workday Initiated Logout** (Habilitar el cierre de sesión iniciado de Workday).
   
    d. En la ventana **Configurar inicio de sesión único** de Azure Portal, copie el valor de **Dirección URL de cierre de sesión** y péguelo en el cuadro de texto **Logout Request URL** (URL de solicitud de cierre de sesión).

    e. En la ventana **Configurar inicio de sesión único** de Azure Portal, copie el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) y péguelo en el cuadro de texto **IdP SSO Service URL** (URL de servicio SSO de IdP).

    f. En el cuadro de texto **Used for Environments** (Se usa en entornos), seleccione el nombre del entorno.

    g. Haga clic en **Identity Provider Public Key Certificate** (Certificado de clave pública de proveedor de identidades) y, después, en **Crear**. 

    ![Crear](./media/active-directory-saas-workday-tutorial/IC782928.png "Crear")

    h. Haga clic en **Create x509 Public Key**(Crear clave pública x509). 

    ![Crear](./media/active-directory-saas-workday-tutorial/IC782929.png "Crear")

13. En la sección **View x509 Public Key** (Ver clave pública x509), siga estos pasos: 
   
    ![Visualización de clave pública x509](./media/active-directory-saas-workday-tutorial/IC782930.png "Visualización de clave pública x509") 
   
    a. En el cuadro de texto **Nombre**, escriba el nombre del certificado (por ejemplo, *PPE\_SP*).
   
    b. En el cuadro de texto **Válido desde** , escriba el valor del atributo Válido desde del certificado.
   
    c.  En el cuadro de texto **Válido hasta** , escriba el valor del atributo Válido hasta del certificado.
   
    > [!NOTE]
    > Puede obtener la fecha válido desde y la fecha válido hasta del certificado descargado haciendo doble clic en él.  Las fechas se muestran bajo la pestaña **Detalles** .
    > 
    >
   
    d.  Abra el certificado codificado en base 64 en el Bloc de notas y luego copie el contenido del mismo.
   
    e.  En el cuadro de texto **Certificado** , pegue el contenido del portapapeles.
   
    f.  Haga clic en **OK**.

14. Lleve a cabo los siguiente pasos: 
   
    ![Configuración de SSO](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "Configuración de SSO")
   
    a.  En el cuadro de texto **Id. de proveedor de servicios**, escriba **http://www.workday.com**.
   
    b. Seleccione **No desinflar la solicitud de autenticación iniciada por el SP**.
   
    c. Como **Método de firma de solicitud de autenticación**, seleccione **SHA256**. 
   
    ![Método de firma de solicitud de autenticación](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "Método de firma de solicitud de autenticación") 
   
    d. Haga clic en **OK**. 
   
    ![Aceptar](./media/active-directory-saas-workday-tutorial/IC782933.png "Aceptar")

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-workday-test-user"></a>Crear un usuario de prueba de Workday

En esta sección, creará un usuario llamado Britta Simon en Workday. Trabaje con el [equipo de soporte técnico al cliente de Workday](https://www.workday.com/en-us/partners-services/services/support.html) para agregar los usuarios a la plataforma de Workday. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Workday.

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a Workday, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Workday**.

    ![Vínculo a Workday en la lista de aplicaciones](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Workday en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Workday.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workday-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workday-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workday-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workday-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workday-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workday-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workday-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workday-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workday-tutorial/tutorial_general_203.png

