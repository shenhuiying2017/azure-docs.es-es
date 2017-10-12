---
title: "Tutorial: Integración de Azure Active Directory con Lifesize Cloud | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Lifesize Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 7542360f9c75786bf400553090ba0a891d9c2fcc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Tutorial: Integración de Azure Active Directory con Lifesize Cloud

En este tutorial, aprenderá a integrar Lifesize Cloud con Azure Active Directory (Azure AD).

La integración de Lifesize Cloud con Azure AD ofrece las ventajas siguientes:

- En Azure AD se puede controlar quién tiene acceso a Lifesize Cloud.
- Puede permitir que los usuarios inicien sesión automáticamente en Lifesize Cloud (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Lifesize Cloud se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Lifesize Cloud

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar Lifesize Cloud desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Agregar Lifesize Cloud desde la galería
Para configurar la integración de Lifesize Cloud en Azure AD, necesita agregar Lifesize Cloud desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Lifesize Cloud desde la galería, siga este procedimiento:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Lifesize Cloud**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_search.png)

5. En el panel de resultados, seleccione **Lifesize Cloud** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Lifesize Cloud con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD necesita saber cuál es el usuario homólogo de Lifesize Cloud para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Lifesize Cloud.

Para establecer la relación de vínculo, en Lifesize Cloud, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Lifesize Cloud, es necesario completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Lifesize Cloud](#creating-a-lifesize-cloud-test-user)**: el objetivo es tener un homólogo de Britta Simon en Lifesize Cloud que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Lifesize Cloud.

**Para configurar el inicio de sesión único de Azure AD con Lifesize Cloud, siga este procedimiento:**

1. En la página de integración de la aplicación **Lifesize Cloud** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_samlbase.png)

3. En la sección **Dominio y direcciones URL de Lifesize Cloud**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://login.lifesizecloud.com/ls/?acs`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://login.lifesizecloud.com/<companyname>`

     
4. Active la casilla **Mostrar configuración avanzada de URL** y realice el siguiente paso:    
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_url1.png)

    En el cuadro de texto **Estado de la retransmisión**, escriba una dirección URL que siga este patrón:`https://webapp.lifesizecloud.com/?ent=<identifier>`
   
   > [!NOTE] 
   >Tenga en cuenta que estos no son valores reales. Deberá actualizar estos valores con la dirección URL de inicio de sesión, el estado de la retransmisión y el identificador reales. Póngase en contacto con el [equipo de soporte técnico de Lifesize Cloud Client](https://www.lifesize.com/support) para obtener los valores de dirección URL de inicio de sesión e identificador; el valor de estado de retransmisión lo puede obtener de la configuración del inicio de sesión único que se explica más adelante en este tutorial.

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Lifesize Cloud**, haga clic en **Configurar Lifesize Cloud** para abrir la ventana **Configurar inicio de sesión**. Copie los valores de **SAML Entity ID y SAML Single Sign-On Service URL** (Identificador de entidad de SAML y URL del servicio de inicio de sesión único de SAML) de la sección de **referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_configure.png) 

7. Para configurar SSO para su aplicación, inicie sesión en la aplicación de Lifesize Cloud con privilegios de administrador.

8. En la esquina superior derecha, haga clic en su nombre y, después, en **Configuración avanzada**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

9. En Configuración avanzada, haga clic en el vínculo **Configuración de SSO**. Se abrirá la página de configuración de SSO de la instancia.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

10. Ahora, configure los valores siguientes en la interfaz de configuración de SSO.    
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
    
    a. En el cuadro de texto **Emisor de proveedor de identidades**, pegue el valor de **id. de entidad de SAML** que ha copiado de Azure Portal.

    b.  En el cuadro de texto **Dirección URL de inicio de sesión**, pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    c. Abra el certificado codificado en base 64 descargado de Azure Portal en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **Certificado X.509**.
  
    d. En la asignación de atributos de SAML, en el cuadro de texto Nombre, escriba el valor **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    
    e. En la asignación de atributos de SAML, en el cuadro de texto **Apellidos**, escriba el valor **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    
    f. En la asignación de atributos de SAML, en el cuadro de texto **Correo electrónico**, escriba el valor **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

11. Para comprobar la configuración, puede hacer clic en el botón **Probar**.
   
    >[!NOTE]
    >Para realizar una prueba correctamente, necesita completar el asistente para configuración en Azure AD y, además, proporcionar acceso los usuarios o grupos que pueden realizar la prueba.

12. Para habilitar SSO, seleccione el botón **Habilitar SSO**.

13. Ahora, haga clic en el botón **Actualizar** para guardar la configuración. Se generará el valor RelayState. Copie el valor de RelayState, que se genera en el cuadro de texto y péguelo en el cuadro de texto **Estado de la retransmisión** en la sección **Dominio y direcciones URL de Lifesize Cloud**. 

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-lifesize-cloud-test-user"></a>Creación de un usuario de prueba de Lifesize Cloud

En esta sección, creará el usuario Britta Simon en Lifesize Cloud. Lifesize Cloud no admite el aprovisionamiento de usuarios automático. Después de autenticarse correctamente en Azure AD, el usuario se aprovisionará automáticamente en la aplicación. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Lifesize Cloud.

![Asignar usuario][200] 

**Para asignar Britta Simon a Lifesize Cloud, siga este procedimiento:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Lifesize Cloud**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Lifesize Cloud en el Panel de acceso, debería llegar a la página de inicio de sesión de la aplicación Lifesize Cloud.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png

