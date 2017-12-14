---
title: "Tutorial: Integración de Azure Active Directory con iLMS | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y iLMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: 2764a109e92d3eabf2b7064ce7cd2e428256c8b8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Tutorial: Integración de Azure Active Directory con iLMS

En este tutorial, obtendrá información sobre cómo integrar iLMS con Azure Active Directory (Azure AD).

Integrar iLMS con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a iLMS.
- Puede permitir que los usuarios inicien sesión automáticamente en iLMS (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con iLMS, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en iLMS

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de iLMS desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-ilms-from-the-gallery"></a>Adición de iLMS desde la galería
Para configurar la integración de iLMS en Azure AD, debe agregar iLMS desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar iLMS desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Nueva aplicación** en la parte superior del cuadro de diálogo para agregar la nueva aplicación.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **iLMS**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_search.png)

5. En el panel de resultados, seleccione **iLMS** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con iLMS con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de iLMS para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de iLMS.

Para establecer esta relación de vínculo, se asigna el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en iLMS.

Para configurar y probar el inicio de sesión único de Azure AD con iLMS, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de iLMS](#creating-an-ilms-test-user)**: para tener un homólogo de Britta Simon en iLMS que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación iLMS.

**Para configurar el inicio de sesión único de Azure AD con iLMS, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **iLMS**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_samlbase.png)

3. En la sección **Dominio y direcciones URL de iLMS**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url.png)

    a. En el cuadro de texto **Identificador**, pegue el valor del **identificador** que ha copiado de la sección **Proveedor de servicios** de la configuración de SAML en el portal de administración de iLMS.

    b. En el cuadro de texto **URL de respuesta**, pegue el valor de **Endpoint (URL)** (Punto de conexión [URL]) que ha copiado de la sección **Proveedor de servicios** de la configuración de SAML en el portal de administración de iLMS, que tiene el siguiente patrón `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`.

    >[!Note]
    >"123456" es un valor de ejemplo del identificador.

4. Active **Mostrar configuración avanzada de URL**, si desea volver a configurar la aplicación en modo iniciado por **SP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, pegue el valor de **Endpoint (URL)** (Punto de conexión [URL]) que ha copiado de la sección **Proveedor de servicios** de la configuración de SAML en el portal de administración de iLMS, como `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`.     

5. Para habilitar el aprovisionamiento JIT, la aplicación iLMS espera las aserciones de SAML en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección **Atributos de usuario** de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-ilms-tutorial/4.png)
    
    Cree los atributos **department, region** y **division** y agregue el nombre de estos atributos en iLMS. Todos estos atributos mostrados anteriormente son obligatorios.  

    > [!NOTE] 
    > Debe habilitar **Create Un-recognized User Account** (Crear una cuenta de usuario no reconocida) en iLMS para asignar estos atributos. Siga las instrucciones [aquí](http://support.inspiredelearning.com/customer/portal/articles/2204526) para hacerse una idea de la configuración de los atributos.

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo | Valor de atributo |
    | ---------------| --------------- |    
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_05.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.

7. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo XML en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_certificate.png) 

8. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-iLMS-tutorial/tutorial_general_400.png)

9. En otra ventana del explorador web, regístrese en el **Portal de administración de iLMS** como administrador.

10. Haga clic en **SSO:SAML** en **Settings** (Configuración) para abrir la configuración de SAML y realice los siguientes pasos:
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-ilms-tutorial/1.png) 

    a. Expanda la sección **Service Provider** (Proveedor de servicios) y copie los valores **Identifier** (Identificador) y **Endpoint (URL)** (Punto de conexión [URL]).

    ![Configurar inicio de sesión único](./media/active-directory-saas-ilms-tutorial/2.png) 

    b. En la sección **Identity Provider** (Proveedor de identidades), haga clic en **Import Metadata** (Importar metadatos).
    
    c. Seleccione el archivo de **metadatos** descargado de Azure Portal en la sección **Certificado de firma de SAML**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. Si desea habilitar el aprovisionamiento JIT para crear cuentas de iLMS para usuarios no reconocidos, realice los pasos siguientes:
        
       - Active **Create Un-recognized User Account** (Crear cuenta de usuario no reconocido).
       
       ![Configurar inicio de sesión único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Asigne los atributos de Azure AD a los atributos de iLMS. En la columna de atributos, especifique el nombre de los atributos o el valor predeterminado.

    e. Vaya a la pestaña **Business Rules** (Reglas empresariales) y realice los pasos siguientes: 
        
       ![Configurar inicio de sesión único](./media/active-directory-saas-ilms-tutorial/5.png)

       - Active **Create Un-recognized Regions, Divisions and Departments** (Crear regiones, divisiones y departamentos no reconocidos) para crear regiones, divisiones y departamentos que todavía no existen en el momento del inicio de sesión único.
        
       - Active **Update User Profile During Sign-in** (Actualizar perfil de usuario durante el inicio de sesión) para especificar si es necesario actualizar el perfil del usuario con cada inicio de sesión único. 
        
       - Si la opción **"Update Blank Values for Non Mandatory Fields in User Profile"** (Actualizar valores en blanco para campos no obligatorios del perfil de usuario), los campos opcionales del perfil que estén en blanco al iniciar sesión darán lugar a que el perfil del usuario de iLMS contenga valores en blanco en dichos campos.
        
       - Active **Send Error Notification Email** (Enviar correo electrónico de notificación de errores) y escriba la dirección de correo electrónico del usuario en la que desea recibir el correo electrónico de notificación de errores.

11. Haga clic en el botón **Save** (Guardar) para guardar la configuración.

    ![Configurar inicio de sesión único](./media/active-directory-saas-ilms-tutorial/save.png)

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
    
### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ilms-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-an-ilms-test-user"></a>Creación de un usuario de prueba de iLMS

La aplicación admite aprovisionamiento de usuarios Just-In-Time y, tras la autenticación, los usuarios se crean automáticamente en la aplicación. JIT funcionará si ha marcado la casilla **Create Un-recognized User Account** (Crear cuenta de usuario no reconocido) durante la configuración de SAML en el portal de administración de iLMS.

Si debe crear un usuario manualmente, siga estos pasos:

1. Regístrese en el sitio de la compañía de iLMS como administrador.

2. Haga clic en **"Register User"** (Registrar usuario) en la pestaña **Users** (Usuarios) para abrir la página **Register User** (Registrar usuario). 
   
   ![Agregar empleado](./media/active-directory-saas-ilms-tutorial/3.png)

3. En la página **"Register User"** (Registrar usuario), realice los pasos siguientes.

    ![Agregar empleado](./media/active-directory-saas-ilms-tutorial/create_testuser_add.png)

    a. En el cuadro de texto **First Name** (Nombre), escriba el nombre Britta.
   
    b. En el cuadro de texto **Last Name** (Apellido), escriba el apellido Simon.

    c. En el cuadro de texto **Email ID** (Id. de correo electrónico), escriba la dirección de correo electrónico de la cuenta de Britta Simon.

    d. En el menú desplegable **Region** (Región), seleccione el valor de la región.

    e. En el menú desplegable **Division** (División), seleccione el valor de la división.

    f. En el menú desplegable **Department** (Departamento), seleccione el valor del departamento.

    g. Haga clic en **Guardar**.

    > [!NOTE] 
    > Puede enviar un correo electrónico de registro al usuario si selecciona la casilla **Send Registration Mail** (Enviar correo electrónico de registro).

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a iLMS.

![Asignar usuario][200] 

**Para asignar el usuario Britta Simon a iLMS, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **iLMS**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de iLMS en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de iLMS.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_203.png

