---
title: 'Tutorial: Integración de Azure Active Directory con Qumu Cloud | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Qumu Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8c4a97b-4de6-49d4-b64e-42222c2ec6c9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: jeedes
ms.openlocfilehash: fc4529d88d393ff18ef36415b09c0e21c97cdcf8
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-azure-active-directory-integration-with-qumu-cloud"></a>Tutorial: Integración de Azure Active Directory con Qumu Cloud

En este tutorial, aprenderá a integrar Qumu Cloud con Azure Active Directory (Azure AD).

La integración de Qumu Cloud con Azure AD ofrece las ventajas siguientes:

- Puede controlar en Azure AD quién tiene acceso a Qumu Cloud.
- Puede permitir que los usuarios inicien sesión automáticamente en Qumu Cloud (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Qumu Cloud se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Qumu Cloud

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Qumu Cloud desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-qumu-cloud-from-the-gallery"></a>Adición de Qumu Cloud desde la galería
Para configurar la integración de Qumu Cloud en Azure AD, necesita agregar Qumu Cloud desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Qumu Cloud desde la galería, siga este procedimiento:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Qumu Cloud**, seleccione **Qumu Cloud** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Qumu Cloud en la lista de resultados](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, va a configurar y probar el inicio de sesión único de Azure AD con Qumu Cloud con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD necesita saber cuál es el usuario homólogo de Qumu Cloud para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Qumu Cloud.

Para configurar y probar el inicio de sesión único de Azure AD con Qumu Cloud, es necesario completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Qumu Cloud](#create-a-qumu-cloud-test-user)**: para tener un homólogo de Britta Simon en Qumu Cloud que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, va a habilitar el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Qumu Cloud.

**Para configurar el inicio de sesión único de Azure AD con Qumu Cloud, siga este procedimiento:**

1. En la página de integración de la aplicación **Qumu Cloud** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_samlbase.png)

3. En la sección **Qumu Cloud Domain and URLs** (Dominio y direcciones URL de Qumu Cloud), realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Qumu Cloud](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.qumucloud.com/saml/SSO`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.qumucloud.com/saml/SSO`.

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Qumu Cloud](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.qumucloud.com`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Qumu Cloud](mailto:support@qumu.com) para obtener estos valores.

4. La aplicación Qumu Cloud espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-qumucloud-tutorial/attribute.png)
    
5. En la sección **Atributos de usuario**, active la casilla **Ver y editar todos los demás atributos de usuario** para expandir los atributos. Realice los pasos siguientes en cada uno de los atributos mostrados:

    | Nombre del atributo | Valor de atributo |
    | ---------------| --------------- |    
    | urn:oid:2.5.4.42 | user.givenname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |

    a. Haga clic en el atributo para abrir la ventana **Editar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-qumucloud-tutorial/tutorial_attribute_04.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    ![Configurar inicio de sesión único](./media/active-directory-saas-qumucloud-tutorial/tutorial_attribute_05.png)

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. Deje el cuadro de texto **Espacio de nombres** en blanco.
    
    e. Haga clic en **Aceptar**.

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-qumucloud-tutorial/tutorial_general_400.png)
    
7. Para configurar el inicio de sesión único en **Qumu Cloud**, es preciso enviar los datos descargados de **XML de metadatos** al [equipo de soporte técnico de Qumu Cloud](mailto:support@qumu.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-qumucloud-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-qumu-cloud-test-user"></a>Creación de un usuario de prueba de Qumu Cloud

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Qumu Cloud. Qumu Cloud admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a Qumu Cloud se creará un nuevo usuario, en caso de que no exista.
>[!Note]
>Si necesita crear manualmente un usuario, es preciso que se ponga contacto con el [equipo de soporte técnico de Qumu Cloud](mailto:support@qumu.com).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a habilitar a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Qumu Cloud.

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a Qumu Cloud, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Qumu Cloud**.

    ![Vínculo a Qumu Cloud en la lista de aplicaciones](./media/active-directory-saas-qumucloud-tutorial/tutorial_qumucloud_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Qumu Cloud en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Qumu Cloud.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qumucloud-tutorial/tutorial_general_203.png

