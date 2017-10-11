---
title: "Tutorial: Integración de Azure Active Directory con Workday | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Workday."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 164d5c644f120fa86e2b690649241892764b64b7
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Tutorial: Integración de Azure Active Directory con Workday

En este tutorial, obtendrá información sobre cómo integrar Workday con Azure Active Directory (Azure AD).

La integración de Workday con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Workday.
- Puede permitir que los usuarios inicien sesión automáticamente en Workday (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Workday, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Workday

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Workday desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-workday-from-the-gallery"></a>Incorporación de Workday desde la galería
Para configurar la integración de Workday en Azure AD, hay que agregar Workday desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Workday desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Workday**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-workday-tutorial/tutorial_workday_search.png)

5. En el panel de resultados, seleccione **Workday** y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, puede configurar y probar el inicio de sesión único de Azure AD con Workday con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Workday para un usuario de Azure AD. Es decir, hay que establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Workday.

Para establecer esta relación de vínculo, se asigna el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Workday.

Para configurar y probar el inicio de sesión único de Azure AD con Workday, hay que completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Workday](#creating-a-workday-test-user)**: para tener un homólogo de Britta Simon en Workday que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en su aplicación Workday.

**Para configurar el inicio de sesión único de Azure AD con Workday, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Workday**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. En la sección **Dominio y direcciones URL de Workday**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba el valor como: `https://impl.workday.com/<tenant>/login-saml2.htmld`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://impl.workday.com/<tenant>/login-saml.htmld`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. La URL de respuesta debe tener un subdominio (por ejemplo, www, wd2, wd3, wd3-impl, wd5 y wd5-impl). Usar algo como "*http://www.myworkday.com*" funciona, pero "*http://myworkday.com*" no funciona. Póngase en contacto con el [equipo de soporte técnico de Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obtener estos valores. 
 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Workday**, haga clic en **Configurar Workday** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 
<CS>
7. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Workday como administrador.

8. Vaya a **Menú \> Workbench**.
   
    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

9. Vaya a **Administración de cuentas**.
   
    ![Administración de cuentas](./media/active-directory-saas-workday-tutorial/IC782924.png "Administración de cuentas")

10. Vaya a **Editar configuración de inquilino: Seguridad**.
   
    ![Edición de seguridad del inquilino](./media/active-directory-saas-workday-tutorial/IC782925.png "Edición de seguridad del inquilino")

11. En la sección **URL de redireccionamiento** , siga estos pasos:
   
    ![Direcciones URL de redirección](./media/active-directory-saas-workday-tutorial/IC7829581.png "Direcciones URL de redirección")
   
    a. Haga clic en **Add Row**(Agregar fila).
   
    b. En los cuadros de texto **Dirección URL de redireccionamiento de inicio de sesión** y **URL de redireccionamiento móvil**, escriba la **dirección URL de inicio de sesión** que ha especificado en la sección **Dominio y direcciones URL de Workday** de Azure Portal.
   
    c. En la ventana **Configurar inicio de sesión único** de Azure Portal, copie la **dirección URL de cierre de sesión único** y péguela en el cuadro de texto **Logout Redirect URL** (URL de redireccionamiento de cierre de sesión).
   
    d.  En el cuadro de texto **Entorno** , escriba el nombre del entorno.  

    >[!NOTE]
    > El valor del atributo Entorno está vinculado con el valor de la URL del inquilino:  
    >Si el nombre de dominio de la URL de inquilino de Workday empieza por impl (por ejemplo, *https://impl.workday.com/\<inquilino\>/login-saml2.htmld*), el atributo **Entorno** tiene que establecerse en Implementación.  
    >Si el nombre de dominio empieza por otra cosa, deberá ponerse en contacto con el [equipo de soporte técnico de Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obtener el valor de **Entorno** coincidente.

12. En la sección **Configuración de SAML** , realice los pasos siguientes:
   
    ![Configuración de SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "Configuración de SAML")
   
    a.  Seleccione **Enable SAML Authentication**(Habilitar autenticación SAML).
   
    b.  Haga clic en **Add Row**(Agregar fila).

13. En la sección Proveedores de identidades SAML, realice los pasos siguientes:
   
    ![Proveedores de identidades SAML](./media/active-directory-saas-workday-tutorial/IC7829271.png "Proveedores de identidades SAML")
   
    a. En el cuadro de texto Nombre del proveedor de identidad, escriba un nombre de proveedor (por ejemplo, *SPInitiatedSSO*).
   
    b. En la ventana **Configurar inicio de sesión único** de Azure Portal, copie el valor de **SAML Entity ID** (Identificador de entidad de SAML) y, luego, péguelo en el cuadro de texto **Emisor**.
   
    c. Seleccione **Enable Workday Initiated Logout** (Habilitar el cierre de sesión iniciado de Workday).
   
    d. En la ventana **Configurar inicio de sesión único** de Azure Portal, copie el valor de **Dirección URL de cierre de sesión** y péguela en el cuadro de texto **Logout Request URL** (URL de solicitud de cierre de sesión).

    e. Haga clic en **Identity Provider Public Key Certificate** (Certificado de clave pública de proveedor de identidades) y, después, en **Crear**. 

    ![Crear](./media/active-directory-saas-workday-tutorial/IC782928.png "Crear")

    f. Haga clic en **Create x509 Public Key**(Crear clave pública x509). 

    ![Crear](./media/active-directory-saas-workday-tutorial/IC782929.png "Crear")


14. En la sección **View x509 Public Key** (Ver clave pública x509), siga estos pasos: 
   
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
   
    f.  Haga clic en **Aceptar**.

15. Lleve a cabo los siguiente pasos: 
   
    ![Configuración de SSO](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "Configuración de SSO")
   
    a.  Habilite el **par de claves privadas x509**.
   
    b.  En el cuadro de texto **Id. de proveedor de servicios**, escriba **http://www.workday.com**.
   
    c.  Seleccione **Habilitar autenticación SAML iniciada por el proveedor de servicios**.
   
    d.  En la ventana **Configurar inicio de sesión único** de Azure Portal, copie el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) y péguelo en el cuadro de texto **IdP SSO Service URL** (URL de servicio SSO de IdP).
   
    e. Seleccione **No desinflar la solicitud de autenticación iniciada por el SP**.
   
    f. Como **Método de firma de solicitud de autenticación**, seleccione **SHA256**. 
   
    ![Método de firma de solicitud de autenticación](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "Método de firma de solicitud de autenticación") 
   
    g. Haga clic en **Aceptar**. 
   
    ![ACEPTAR](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")
<CE>

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
>

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-workday-test-user"></a>Creación de un usuario de prueba de Workday

Para obtener un usuario de prueba aprovisionado en Workday, deberá ponerse en contacto con el [equipo de soporte técnico de Workday](https://www.workday.com/en-us/partners-services/services/support.html).
El [equipo de soporte técnico de Workday](https://www.workday.com/en-us/partners-services/services/support.html) creará el usuario.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Workday.

![Asignar usuario][200] 

**Para asignar Britta Simon a Workday, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Workday**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del aprovisionamiento de usuarios](active-directory-saas-workday-inbound-tutorial.md)



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

