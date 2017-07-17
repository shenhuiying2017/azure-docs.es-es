---
title: "Tutorial: Integración de Azure Active Directory con SAP Cloud for Customer | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAP Cloud for Customer."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 22665107cdf689042a5b0eee34eaeacc0434fbc4
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Tutorial: Integración de Azure Active Directory con SAP Cloud for Customer
En este tutorial, aprenderá a integrar SAP Cloud for Customer con Azure Active Directory (Azure AD).

La integración de SAP Cloud for Customer con Azure AD proporciona las siguientes ventajas:

* En Azure AD se puede controlar quién tiene acceso a SAP Cloud for Customer.
* Es posible habilitar que los usuarios inicien sesión automáticamente en SAP Cloud for Customer (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con SAP Cloud for Customer, se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción a SAP Cloud for Customer con inicio de sesión único habilitado

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de SAP Cloud for Customer desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Adición de SAP Cloud for Customer desde la galería
Para configurar la integración de SAP Cloud for Customer en Azure AD, es preciso agregar SAP Cloud for Customer desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SAP Cloud for Customer desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Active Directory][1]

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **SAP Cloud for Customer**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_01.png)

7. En el panel de resultados, seleccione **SAP Cloud for Customer** y luego haga clic en **Completar** para agregar la aplicación.
   
    ![Active Directory](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, se configura y se prueba el inicio de sesión único de Azure AD con SAP Cloud for Customer con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo de SAP Cloud for Customer para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SAP Cloud for Customer.

Para configurar y probar el inicio de sesión único de Azure AD con SAP Cloud for Customer, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SAP Cloud for Customer](#creating-an-sap-business-bydesign-test-user)**: para tener un homólogo de Britta Simon en SAP Cloud for Customer que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación SAP Cloud for Customer. 

**Para configurar el inicio de sesión único de Azure AD con SAP Cloud for Customer, siga estos pasos:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **SAP Cloud for Customer**, en el menú de la parte superior, haga clic en **Atributos**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_80.png) 

2. En la lista de atributos de token de SAML, seleccione el atributo nameidentifier y haga clic en **Editar**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_84.png) 

3. En el cuadro de diálogo **Editar atributo de usuario** , siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_85.png) 

    a. En la lista **Valor del atributo**, seleccione la función **ExtractMailPrefix()**.

    b. En la lista **Correo** , seleccione el atributo de usuario que desea usar en su implementación. 
    Por ejemplo, si quiere usar EmployeeID como identificador de usuario único y ha almacenado el valor del atributo en ExtensionAttribute2, seleccione user.extensionattribute2. 

    c. Haga clic en **Complete**. 


1. En el portal clásico, en la página de integración de aplicaciones de **SAP Cloud for Customer**, haga clic en **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 

2. En la página **¿Cómo desea que los usuarios inicien sesión en SAP Cloud for Customer?**, seleccione **Inicio de sesión único de Azure AD** y luego haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_03.png) 

3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_04.png) 
   
    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que usan los usuarios para iniciar sesión en la aplicación SAP Cloud for Customer con el siguiente patrón: `https://<server name>.crm.ondemand.com`
   
    b. Haga clic en **Siguiente**

4. En la página **Configurar inicio de sesión único en SAP Cloud for Customer** , siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_05.png)
   
    a. Haga clic en **Descargar metadatos**y luego guarde el archivo en el equipo.
   
    b. Haga clic en **Siguiente**.

5. Para configurar SSO, siga estos pasos:
   
    a. Inicie sesión en el portal de SAP Cloud for Customer con derechos de administrador.
   
    b. Navegue hasta **Application and User Management Common Task** (Tarea común de administración de usuarios y aplicaciones) y haga clic en la pestaña **Proveedor de identidades**.
   
    c. Haga clic en **New Identity Provider** (Nuevo proveedor de identidades) y seleccione el archivo XML de metadatos que ha descargado del Portal de Azure clásico. Al importar los metadatos, el sistema carga automáticamente el certificado de firma y el certificado de cifrado necesarios.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Azure AD requiere la URL del Servicio de consumidor de aserciones del elemento en la solicitud SAML, así que active la casilla **Include Assertion Consumer Service URL** (Incluir URL del Servicio de consumidor de aserciones).
   
    e. Haga clic en **Activate Single Sign-On**(Activar inicio de sesión único).
   
    f. Guarde los cambios.
   
    g. Haga clic en la pestaña **My System** (Mi sistema).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. Copie la **dirección URL de SSO** y péguela en el cuadro de texto **URL de inicio de sesión de Azure AD**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. Especifique si el empleado puede elegir manualmente entre iniciar sesión con el Id. de usuario y una contraseña o mediante SSO, para lo que debe seleccionar **Manual Identity Provider Selection** (Selección manual de proveedor de identidades).
   
    j. En la sección **SSO URL** (URL de SSO), especifique la dirección URL que deben utilizar los empleados para iniciar sesión en el sistema. 
    En la lista **URL Sent to Employee** (URL que se envía a empleado), puede elegir una de las siguientes opciones:
   
    **Non-SSO URL**
   
    El sistema solo envía la dirección URL del sistema normal al empleado. El empleado no puede iniciar sesión mediante SSO y debe usar una contraseña o un certificado en su lugar.
   
    **SSO URL** 
   
    El sistema solo envía la dirección URL de SSO al empleado. El empleado puede iniciar sesión mediante SSO. La solicitud de autenticación se redirige a través de IdP.
   
    **Automatic Selection**
   
    SI SSO no está activo, el sistema envía la dirección URL del sistema normal al empleado. Si SSO está activo, el sistema comprueba si el empleado tiene contraseña. Si existe una contraseña, se envían al empleado tanto la URL de SSO como la URL no de SSO. Sin embargo, si el empleado no tiene contraseña, solo se envía la primera al empleado.
   
    k. Guarde los cambios.

6. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, se crea un usuario de prueba llamado Britta Simon en el Portal de Azure clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_09.png) 

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_04.png) 

5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.

6. En el cuadro de diálogo **Perfil de usuario**, siga estos pasos:  ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_06.png) 
   
    a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
    b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
    d. En la lista **Rol**, seleccione **Usuario**.
   
    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-an-sap-cloud-for-customer-test-user"></a>Creación de un usuario de prueba de SAP Cloud for Customer
En esta sección, creará un usuario llamado Britta Simon en SAP Cloud for Customer. Trabaje con el equipo de soporte técnico de SAP Cloud for Customer para agregar usuarios en la plataforma SAP Cloud for Customer. 

> [!NOTE]
> Asegúrese de que el valor de NameID coincide con el campo de nombre de usuario de la plataforma SAP Cloud for Customer.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure, para lo que le concede acceso a SAP Cloud for Customer.

![Asignar usuario][200] 

**Para asignar Britta Simon a SAP Cloud for Customer, siga estos pasos:**

1. En el Portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SAP Cloud for Customer**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_50.png) 

3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]

4. En la lista Usuarios, seleccione **Britta Simon**.

5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de SAP Cloud for Customer en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación SAP Cloud for Customer.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_205.png

