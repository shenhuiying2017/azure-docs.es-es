---
title: "Tutorial: Integración de Azure Active Directory con SAP Business Object Cloud | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAP Business Object Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: b0ea48aea2e13027c5af273a416489e48987b44a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Tutorial: Integración de Azure Active Directory con SAP Business Object Cloud

En este tutorial, aprenderá a integrar SAP Business Object Cloud con Azure Active Directory (Azure AD).

La integración de SAP Business Object Cloud con Azure AD le proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a SAP Business Object Cloud.
- Puede iniciar sesión automáticamente con los usuarios en SAP Business Object Cloud mediante un inicio de sesión único y una cuenta de Azure AD del usuario.
- Puede administrar sus cuentas en una única ubicación central: Azure Portal.

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SAP Business Object Cloud, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una instancia de SAP Business Object Cloud con el inicio de sesión único habilitado

> [!NOTE]
> Si va a probar los pasos de este tutorial, no se recomienda hacerlo en un entorno de producción.

Recomendaciones para probar los pasos de este tutorial:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba gratuita durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. 

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de SAP Business Object Cloud desde la galería.
2. Configuración y prueba del inicio de sesión único en Azure AD.

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>Incorporación de SAP Business Object Cloud desde la galería
Para configurar la integración de SAP Business Object Cloud en Azure AD, en la galería, agregue SAP Business Object Cloud a la lista de aplicaciones SaaS administradas.

Para agregar SAP Business Object Cloud desde la galería:

1. En el menú izquierdo de [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Página Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **SAP Business Object Cloud**.

    ![El cuadro de búsqueda](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. En el panel de resultados, seleccione **SAP Business Object Cloud** y seleccione **Agregar**.

    ![SAP Business Object Cloud en la lista de resultados](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con SAP Business Object Cloud mediante un usuario de prueba llamado *Britta Simon*.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Azure AD en SAP Business Object Cloud. Se debe establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SAP Business Object Cloud.

Para establecer la relación de vínculo, en SAP Business Object Cloud, asigne el valor de **nombre de usuario** como valor de **nombre de usuario** de Azure AD.

Para configurar y probar el inicio de sesión único de Azure AD con SAP Business Object Cloud, complete los siguientes bloques de creación:

1. [Configuración del inicio de sesión único de Azure AD](#set-up-azure-ad-single-sign-on). Configura un usuario para usar esta característica.
2. [Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user). Prueba un inicio de sesión único de Azure AD con el usuario Britta Simon.
3. [Creación de un usuario de prueba de SAP Business Object Cloud](#create-an-sap-business-object-cloud-test-user). Crea un homólogo de Britta Simon en SAP Business Object Cloud que está vinculado a la representación del usuario en Azure AD.
4. [Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user). Configura Britta Simon para usar un inicio de sesión único de Azure AD.
5. [Prueba de inicio de sesión único](#test-single-sign-on). Comprueba que la configuración funciona.

### <a name="set-up-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, activa el inicio de sesión único de Azure AD en Azure Portal. A continuación, configura el inicio de sesión único en la aplicación SAP Business Object Cloud.

Para configurar el inicio de sesión único en Azure AD con SAP Business Object Cloud:

1. En la página de integración de la aplicación **SAP Business Object Cloud** de Azure Portal, seleccione **Inicio de sesión único**.

    ![Seleccionar Inicio de sesión único][4]

2. En la página **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML**.
 
    ![Seleccionar Inicio de sesión basado en SAML](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. En **Dominio y direcciones URL de SAP Business Object Cloud**, complete los pasos siguientes:

    1. En el cuadro **URL de inicio de sesión**, escriba una dirección URL con el siguiente formato: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. En el cuadro **Identificador**, escriba una dirección URL con el siguiente formato:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![Direcciones URL de la página Dominio y direcciones URL de SAP Business Object Cloud](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > Los valores de estas direcciones URL se muestran solo con fines demostrativos. Debe actualizarlos con las direcciones URL reales de inicio de sesión único y de identificador. Para obtener la dirección URL de inicio de sesión, póngase en contacto con el [equipo de soporte técnico de SAP Business Object Cloud](https://www.sap.com/product/analytics/cloud-analytics.support.html). Para obtener la dirección URL de identificador, descargue los metadatos de SAP Business Object Cloud desde la consola de administración. Esto se explica posteriormente en este tutorial. 

4. En **Certificado de firma de SAML**, seleccione **XML de metadatos**. Luego, guarde el archivo de metadatos en el equipo.

    ![Seleccionar XML de metadatos](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Seleccione **Guardar**.

    ![Seleccionar Guardar](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de SAP Business Object Cloud como administrador.

7. Seleccione **Menu** > **System** > **Administration** (Menú > Sistema > Administración).
    
    ![Seleccionar Menu (Menú), System (Sistema) y, luego, Administration (Administración)](./media/active-directory-saas-sapboc-tutorial/config1.png)

8. En la pestaña **Security** (Seguridad), seleccione el icono **Edit** (Editar) (lápiz).
    
    ![En la pestaña Security (Seguridad), seleccionar el icono Edit (Editar)](./media/active-directory-saas-sapboc-tutorial/config2.png)  

9. Como **Authentication Method** (Método de autenticación), seleccione **SAML Single Sign-On (SSO)** (Inicio de sesión único [SSO] de SAML).

    ![Seleccionar SAML Single Sign-On (SSO) (Inicio de sesión único [SSO] de SAML) como método de autenticación](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. Para descargar los metadatos del proveedor de servicios (Paso 1), haga clic en **Download** (Descargar). En el archivo de metadatos, busque y copie el valor **entityID**. En Azure Portal, en **Dominio y direcciones URL de SAP Business Object Cloud**, pegue el valor en el cuadro **Identificador**.

    ![Copiar y pegar el valor entityID](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. Para cargar los metadatos del proveedor de servicios (Paso 2) en el archivo que descargó desde Azure Portal en **Upload Identity Provider metadata** (Cargar metadatos del proveedor de identidades), seleccione **Upload** (Cargar).  

    ![En Upload Identity Provider metadata (Cargar metadatos del proveedor de identidades), seleccionar Upload (Cargar)](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. En la lista **User Attribute** (Atributo de usuario), seleccione el atributo de usuario (Paso 3) que quiere usar en su implementación. Este atributo de usuario se asigna al proveedor de identidades. Para escribir un atributo personalizado en la página del usuario, use la opción **Custom SAML Mapping** (Personalizar asignación de SAML). O bien, puede seleccionar **Email** (Correo electrónico) o **USER ID** (Id. de usuario) como atributo de usuario. En nuestro ejemplo, hemos seleccionado **Email** (Correo electrónico) porque hemos asignado la notificación de identificador de usuario con el atributo **userprincipalname** en la sección **Atributos de usuario** de Azure Portal. Esto proporciona un correo electrónico de usuario único, que se envía a la aplicación SAP Business Object Cloud en cada respuesta SAML correcta.

    ![Seleccionar Atributos de usuario](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. Para comprobar la cuenta con el proveedor de identidades (Paso 4), en el cuadro **Login Credential (Email)** [Credenciales de inicio de sesión (correo electrónico)], escriba la dirección de correo electrónico del usuario. A continuación, seleccione **Verify Account** (Comprobar cuenta). El sistema agrega credenciales de inicio de sesión a la cuenta de usuario.

    ![Escribir Email (Correo electrónico) y seleccionar Verify Account (Comprobar cuenta)](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. Seleccione el icono **Save** (Guardar).

    ![Icono Save (Guardar)](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> Puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación. Después de agregar la aplicación seleccionando **Active Directory** > **Aplicaciones empresariales**, elija la pestaña **Inicio de sesión único**. Puede acceder a la documentación insertada en la sección **Configuración** en la parte inferior de la página. Para más información, consulte la [documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en Azure Portal.

Para crear un usuario de prueba en Azure AD:

1. En el menú izquierdo de Azure Portal, seleccione **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, seleccione **Usuarios y grupos** y, luego, **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, seleccione **Agregar**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. En el cuadro de diálogo **Usuario**, complete los pasos siguientes:
 
    1. En el cuadro **Nombre**, escriba **BrittaSimon**.

    2. En el cuadro **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    3. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    4. Seleccione **Crear**.

        ![Cuadro de diálogo Usuario](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    ![Creación de un usuario de Azure AD][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>Creación de un usuario de prueba de SAP Business Object Cloud

Se debe aprovisionar a los usuarios de Azure AD en SAP Business Object Cloud antes de que puedan iniciar sesión en SAP Business Object Cloud. En SAP Business Object Cloud, el aprovisionamiento es una tarea manual.

Para aprovisionar una cuenta de usuario:

1. Inicie sesión como administrador en su sitio de la compañía de SAP Business Object Cloud.

2. Seleccione **Menu** > **Security** > **Users** (Menú > Seguridad > Usuarios).

    ![Agregar empleado](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. En la página **Users** (Usuarios), para agregar nuevos detalles de usuario, seleccione **+**. 

    ![Página Add Users (Agregar usuarios)](./media/active-directory-saas-sapboc-tutorial/user4.png)

    Después, complete los siguientes pasos:

    1. En el cuadro **USER ID** (Id. de usuario), escriba el identificador del usuario, en este caso **Britta**.

    2. En el cuadro **FIRST NAME** (Nombre), escriba el nombre del usuario, en este caso **Britta**.

    3. En el cuadro **LAST NAME** (Apellido), escriba el apellido del usuario, en este caso **Simon**.

    4. En el cuadro **DISPLAY NAME** (Nombre para mostrar), escriba el nombre completo del usuario, en este caso **Britta Simon**.

    5. En el cuadro **E-MAIL** (Correo electrónico), escriba la dirección de correo electrónico del usuario, en este caso **brittasimon@contoso.com**.

    6. En la página **Select Roles** (Seleccionar roles), seleccione el rol adecuado para el usuario y, luego, **OK** (Aceptar).

      ![Seleccionar rol](./media/active-directory-saas-sapboc-tutorial/user3.png)

    7. Seleccione el icono **Save** (Guardar).    


### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará al usuario Britta Simon para que use el inicio de sesión único de Azure AD concediéndole acceso de cuenta de usuario a SAP Business Object Cloud.

Para asignar a Britta Simon a SAP Business Object Cloud:

1. En Azure Portal, abra la vista de aplicaciones y vaya a la vista de directorio. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SAP Business Object Cloud**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. En el menú de la izquierda, seleccione **Usuarios y grupos**.

    ![Seleccionar Usuarios y grupos][202] 

4. Seleccione **Agregar**. Después, en la página **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Página Agregar asignación][203]

5. En la página **Usuarios y grupos**, en la lista de usuarios, seleccione **Britta Simon**.

6. En la página **Usuarios y grupos**, seleccione **Seleccionar**.

7. En la página **Agregar asignación**, seleccione **Asignar**.

![Asignación del rol de usuario][200] 
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el panel de acceso.

Al seleccionar el icono de SAP Business Object Cloud en el panel de acceso, debería iniciar sesión automáticamente en su aplicación SAP Business Object Cloud.

Para más información sobre el panel de acceso, consulte [Introducción al panel de acceso](active-directory-saas-access-panel-introduction.md).

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

