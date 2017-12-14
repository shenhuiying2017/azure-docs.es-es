---
title: "Tutorial: Integración de Azure Active Directory con NetSuite | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y NetSuite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: jeedes
ms.openlocfilehash: 23e39f20f5b3faa4f6cfba57508d1649e2bbdaa3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Tutorial: Integración de Azure Active Directory con NetSuite

En este tutorial, obtendrá información sobre cómo integrar NetSuite con Azure Active Directory (Azure AD).

La integración de NetSuite con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a NetSuite.
- Puede permitir que los usuarios inicien sesión automáticamente en NetSuite (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con NetSuite, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en NetSuite

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de NetSuite desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-netsuite-from-the-gallery"></a>Incorporación de NetSuite desde la galería
Para configurar la integración de NetSuite en Azure AD, deberá agregar NetSuite desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar NetSuite desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **NetSuite**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_search.png)

5. En el panel de resultados, seleccione **NetSuite** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con NetSuite con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de NetSuite para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de NetSuite.

Para establecer esta relación de vínculo, en NetSuite, se asigna el valor del **nombre de usuario** en Azure AD como el valor del **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con NetSuite, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de NetSuite](#creating-a-netsuite-test-user)**: para tener un homólogo de Britta Simon en NetSuite vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación NetSuite.

**Para configurar el inicio de sesión único de Azure AD con NetSuite, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **NetSuite**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_samlbase.png)

3. En la sección **Dominio y direcciones URL de NetSuite**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_url.png)

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.netsuite.com/saml2/acs` `https://<tenant-name>.na1.netsuite.com/saml2/acs` `https://<tenant-name>.na2.netsuite.com/saml2/acs` `https://<tenant-name>.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con la URL de respuesta real. Póngase en contacto con el [equipo de soporte técnico de NetSuite](http://www.netsuite.com/portal/services/support.shtml) para obtener estos valores.
 
4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo XML en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-netsuite-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de NetSuite**, haga clic en **Configurar NetSuite** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_configure.png) 

7. Abra una nueva pestaña en el explorador e inicie sesión en el sitio de la empresa Netsuite como administrador.

8. En la barra de herramientas en la parte superior de la página, haga clic en **Configuración** y haga clic en **Administrador de instalación**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

9. En la lista **Tareas de configuración**, seleccione **Integración**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-Netsuite-tutorial/ns-integration.png)

10. En la sección **Administrar autenticación**, haga clic en **Inicio de sesión único de SAML**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-Netsuite-tutorial/ns-saml.png)

11. En la página **Configuración de SAML** , realice los pasos siguientes:
   
    a. Copie el valor de la **dirección URL del servicio de inicio de sesión único de SAML** de la sección de **referencia rápida** de **Configuración de inicio de sesión** y péguelo en el campo **Identity Provider Login Page** (Página de inicio de sesión del proveedor de identidades) en NetSuite.

    ![Configurar inicio de sesión único](./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png)
  
    b. En NetSuite, seleccione **Primary Authentication Method** (Método de autenticación principal).

    c. Para el campo con la etiqueta **Metadatos del proveedor de identidades de SAMLV2**, seleccione **Cargar archivo de metadatos de IDP**. A continuación, haga clic en **Examinar** para cargar el archivo de metadatos que descargó de Azure Portal.

    ![Configurar inicio de sesión único](./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png)

    d. Haga clic en **Enviar**.

12. En Azure AD, haga clic en la casilla **Ver y editar todos los atributos de usuario** y agregue el atributo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-Netsuite-tutorial/ns-attributes.png)

13. Para el campo **Nombre de atributo**, escriba `account`. Para el campo **Valor de atributo**, escriba el identificador de cuenta de NetSuite. Este valor es constante y cambia con la cuenta. A continuación se incluyen instrucciones sobre cómo encontrar el identificador de la cuenta:

      ![Configurar inicio de sesión único](./media/active-directory-saas-Netsuite-tutorial/ns-add-attribute.png)

    a. En NetSuite, haga clic en **Setup** (Configurar) en el menú de navegación superior.

    b. A continuación, haga clic en la sección **Tareas de configuración** del menú de navegación izquierdo, seleccione la sección **Integración** y haga clic en **Preferencias de servicios web**.

    c. Copie el identificador de la cuenta NetSuite y péguelo en el campo **Valor del atributo** en Azure AD.

    ![Configurar inicio de sesión único](./media/active-directory-saas-Netsuite-tutorial/ns-account-id.png)

14. Antes de que los usuarios puedan realizar el inicio de sesión único en NetSuite, se les deben asignar primero los permisos adecuados en NetSuite. Siga las instrucciones siguientes para asignar estos permisos.

    a. En el menú de navegación superior, haga clic en **Configuración** y en **Administrador de instalación**.
      
      ![Configurar inicio de sesión único](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    b. En el menú de navegación izquierdo, seleccione **Usuarios/Roles** y haga clic en **Administrar roles**.
      
      ![Configurar inicio de sesión único](./media/active-directory-saas-Netsuite-tutorial/ns-manage-roles.png)

    c. Haga clic en **Nuevo rol**.

    d. Escriba un **Nombre** para el nuevo rol y active la casilla **Solo inicio de sesión único**.
      
      ![Configurar inicio de sesión único](./media/active-directory-saas-Netsuite-tutorial/ns-new-role.png)

    e. Haga clic en **Guardar**.

    f. En el menú de la parte superior, haga clic en **Permisos**. A continuación, haga clic en **Configuración**.
      
       ![Configurar inicio de sesión único](./media/active-directory-saas-Netsuite-tutorial/ns-sso.png)

    g. Seleccione **Configurar inicio de sesión único de SAM** y haga clic en **Agregar**.

    h. Haga clic en **Guardar**.

    i. En el menú de navegación superior, haga clic en **Configuración** y en **Administrador de instalación**.
      
       ![Configurar inicio de sesión único](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    j. En el menú de navegación izquierdo, seleccione **Usuarios/Roles** y haga clic en **Administrar usuarios**.
      
       ![Configurar inicio de sesión único](./media/active-directory-saas-Netsuite-tutorial/ns-manage-users.png)

    k. Seleccione un usuario de prueba. A continuación, haga clic en **Editar**.
      
       ![Configurar inicio de sesión único](./media/active-directory-saas-Netsuite-tutorial/ns-edit-user.png)

    l. En el cuadro de diálogo Roles, seleccione el rol que se ha creado y haga clic en **Agregar**.
      
       ![Configurar inicio de sesión único](./media/active-directory-saas-Netsuite-tutorial/ns-add-role.png)

    m. Haga clic en **Guardar**.
    
> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_01.png) 

2.  Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**. 

### <a name="creating-a-netsuite-test-user"></a>Creación de un usuario de prueba de NetSuite

En esta sección se creará un usuario llamado Britta Simon en NetSuite. NetSuite admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.
No hay ningún elemento de acción para usted en esta sección. Si el usuario ya no existe en NetSuite, se crea uno nuevo cuando se intenta acceder a NetSuite.


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a NetSuite.

![Asignar usuario][200] 

**Para asignar a Britta Simon a NetSuite, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **NetSuite**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Para probar la configuración de inicios de sesión únicos, abra el Panel de acceso en [https://myapps.microsoft.com](https://myapps.microsoft.com/), inicie sesión en la cuenta de prueba y haga clic en **NetSuite**.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del aprovisionamiento de usuarios](active-directory-saas-netsuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_203.png

