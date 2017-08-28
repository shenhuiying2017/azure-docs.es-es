---
title: "Tutorial: Integración de Azure Active Directory con SAP Business Object Cloud | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAP Business Object Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: a09e965b6fc9b89023c09092860fcf79773a4518
ms.contentlocale: es-es
ms.lasthandoff: 08/12/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Tutorial: Integración de Azure Active Directory con SAP Business Object Cloud

En este tutorial, aprenderá a integrar SAP Business Object Cloud con Azure Active Directory (Azure AD).

La integración de SAP Business Object Cloud con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a SAP Business Object Cloud.
- Puede permitir que los usuarios inicien sesión automáticamente en SAP Business Object Cloud (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SAP Business Object Cloud, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en SAP Business Object Cloud

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de SAP Business Object Cloud desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Adición de SAP Business Object Cloud desde la galería
Para configurar la integración de SAP Business Object Cloud en Azure AD, es preciso agregar SAP Business Object Cloud desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SAP Business Object Cloud desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **SAP Business Object Cloud**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. En el panel de resultados, seleccione **SAP Business Object Cloud** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con SAP Business Object Cloud con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo de SAP Business Object Cloud para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SAP Business Object Cloud.

Para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario** en SAP Business Object Cloud.

Para configurar y probar el inicio de sesión único de Azure AD con SAP Business Object Cloud, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SAP Business Object Cloud](#creating-an-sap-business-object-cloud-test-user)**: el objetivo es tener un homólogo de Britta Simon en SAP Business Object Cloud que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación SAP Business Object Cloud.

**Para configurar el inicio de sesión único de Azure AD con SAP Business Object Cloud, siga estos pasos:**

1. En la página de integración de la aplicación **SAP Business Object Cloud** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. En la sección **Dominio y direcciones URL de SAP Business Object Cloud Domain**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de SAP Business Object Cloud](https://www.sap.com/product/analytics/cloud-analytics.support.html) para obtener la dirección URL de inicio de sesión; para obtener el identificador, descargue los metadatos de SAP Business Object Cloud de la consola de administrador, como se explica más adelante en este tutorial. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de SAP Business Object Cloud como administrador.

7. Vaya a **Menu (Menú) > System (Sistema) > Administration (Administración)**
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-sapboc-tutorial/config1.png)    
8. En la pestaña **Security** (Seguridad), haga clic en **[Edit – pen icon]** [Editar - icono de lápiz].
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-sapboc-tutorial/config2.png)    
9. Seleccione **SAML Single Sign-On (SSO)** (Inicio de sesión único [SSO] de SAML) como método de autenticación.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. Haga clic en **Download** (Descargar) para descargar los metadatos del proveedor de servicios. Recupere el valor de **entityID** del archivo y péguelo en el cuadro de texto **Identificador** de la sección **Dominio y direcciones URL de SAP Business Object Cloud** de Azure Portal.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. En **Upload Identity Provider metadata** (Cargar metadatos del proveedor de identidades), haga clic en el botón **Upload..** (Cargar...) para cargar el archivo de metadatos descargado de Azure Portal. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. En la lista, seleccione el **atributo de usuario** que quiere usar para asignar al proveedor de identidades en la implementación. Use la opción "Custom SAML Mapping" (Asignación de SAML personalizada) para especificar un atributo personalizado en la página de usuarios o seleccione "Email" (Correo electrónico) o "USER ID" (Id. de usuario) como atributo de usuario. Por ejemplo, aquí se selecciona **Email** (Correo electrónico) dado que hemos asignado la notificación de identificador de usuario con el atributo userprincipalname de la sección **"Atributos de usuario"** de Azure Portal, que se envía a la aplicación SAP Business Object Cloud en cada respuesta correcta de SAML.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. Escriba el correo electrónico en el cuadro de texto **Login Credential (Email)** (Credencial de inicio de sesión [correo electrónico]) y haga clic en el botón **Verify Account** (Comprobar cuenta) para permitir que el sistema agregue la credencial de inicio de sesión a su cuenta.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. Haga clic en el icono **Guardar**.

    ![Guardar](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-an-sap-business-object-cloud-test-user"></a>Creación de un usuario de prueba de SAP Business Object Cloud

Para permitir que los usuarios de Azure AD inicien sesión en SAP Business Object Cloud, se deben aprovisionar en SAP Business Object Cloud. Para SAP Business Object Cloud, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión como administrador en su sitio de la compañía de SAP Business Object Cloud.

2. Vaya a **Menu (Menú) > Security (Seguridad) > Users (Usuarios)**.

    ![Agregar empleado](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. En la página **Users** (Usuarios), haga clic en el signo **"+"** para agregar nuevos detalles de usuario y realice los pasos siguientes:

    ![Invitar a contactos](./media/active-directory-saas-sapboc-tutorial/user4.png)

    a. En el cuadro de texto **USER ID** (Id. de usuario), escriba en este caso el identificador de usuario de Britta.

    b. En el cuadro de texto **FIRST NAME** (Nombre), escriba el nombre de usuario, en este caso Britta.

    c. En el cuadro de texto **LAST NAME** (Apellido), escriba el apellido del usuario, en este caso Simon.

    d. En el cuadro de texto **DISPLAY NAME** (Nombre para mostrar), escriba el nombre completo del usuario, en este caso Britta Simon.

    e. En el cuadro de texto **E-MAIL** (Correo electrónico), escriba la dirección de correo electrónico del usuario, en este caso brittasimon@contoso.com.

    f. Seleccione el rol apropiado para el usuario.

      ![Rol](./media/active-directory-saas-sapboc-tutorial/user3.png)

    g. Haga clic en el icono **Save** (Guardar). 


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a SAP Business Object Cloud.

![Asignar usuario][200] 

**Para asignar a Britta Simon a SAP Business Object Cloud, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SAP Business Object Cloud**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de SAP Business Object Cloud en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación SAP Business Object Cloud.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png


