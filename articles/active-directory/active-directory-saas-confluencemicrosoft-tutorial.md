---
title: "Tutorial: Integración de Azure Active Directory con Confluence SAML SSO by Microsoft | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Confluence SAML SSO by Microsoft."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.openlocfilehash: 4151811a74fc0e58f6171e11d77e070f3cb40f95
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Tutorial: Integración de Azure Active Directory con Confluence SAML SSO by Microsoft

En este tutorial obtendrá información sobre cómo integrar Confluence SAML SSO by Microsoft con Azure Active Directory (Azure AD).

La integración de Confluence SAML SSO by Microsoft con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Confluence SAML SSO by Microsoft.
- Puede permitir que los usuarios inicien sesión automáticamente en Confluence SAML SSO by Microsoft (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="description"></a>Description:

Use la cuenta de Microsoft Azure Active Directory con el servidor Confluence de Atlassian para habilitar el inicio de sesión único. De esta forma, todos los usuarios de la organización pueden usar las credenciales de Azure AD para iniciar sesión en la aplicación Confluence. Este complemento usa SAML 2.0 para la federación.

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Confluence SAML SSO by Microsoft, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Aplicación de servidor de Confluence instalada en un servidor Windows de 64 bits (de manera local o en la infraestructura de IaaS en la nube)
- El servidor de Confluence es compatible con HTTPS
- Tenga en cuenta que las versiones admitidas para el complemento Confluence se mencionan en la sección siguiente.
- El servidor de Confluence es accesible en Internet, especialmente a la página de inicio de sesión de Azure AD para la autenticación, y debe poder recibir el token de Azure AD
- Las credenciales de administrador se configuran en Confluence
- WebSudo está deshabilitado en Confluence
- Usuario de prueba creado en la aplicación de servidor de Confluence

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción de Confluence. Pruebe primero la integración en el entorno de desarrollo o de ensayo de la aplicación y, después, use el entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, aquí puede obtener una versión de evaluación de un mes: [Oferta de prueba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Versiones compatibles de Confluence 

En la actualidad se admiten las siguientes versiones de Confluence:

- Confluence: de la versión 5.0 a la 5.10

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregar Confluence SAML SSO by Microsoft desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Agregar Confluence SAML SSO by Microsoft desde la galería
Para configurar la integración de Confluence SAML SSO by Microsoft en Azure AD, deberá agregar Confluence SAML SSO by Microsoft desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Confluence SAML SSO by Microsoft desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![APLICACIONES][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![APLICACIONES][3]

4. En el cuadro de búsqueda, escriba **Confluence SAML SSO by Microsoft**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_search.png)

5. En el panel de resultados, seleccione **Confluence SAML SSO by Microsoft** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección va a configurar y probar el inicio de sesión único de Azure AD con Confluence SAML SSO by Microsoft con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Confluence SAML SSO by Microsoft para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Confluence SAML SSO by Microsoft.

Para configurar y probar el inicio de sesión único de Azure AD con Confluence SAML SSO by Microsoft, es necesario completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Confluence SAML SSO by Microsoft](#creating-a-confluence-saml-sso-by-microsoft-test-user)**: Para tener un homólogo de Britta Simon en Confluence SAML SSO by Microsoft que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Confluence SAML SSO by Microsoft.

**Para configurar el inicio de sesión único de Azure AD con Confluence SAML SSO by Microsoft, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Confluence SAML SSO by Microsoft**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_samlbase.png)

3. En la sección **Dominio y direcciones URL de Confluence SAML SSO by Microsoft**, siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<domain:port>/plugins/servlet/saml/auth`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<domain:port>/`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<domain:port>/plugins/servlet/saml/auth`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. En caso de que sea una dirección URL con nombre, el puerto es opcional. Estos valores se reciben durante la configuración del complemento de Confluence, que se explica más adelante en el tutorial.

4. Para generar la dirección URL de **Metadatos**, lleve a cabo los pasos siguientes:

    a. Haga clic en **Registros de aplicaciones**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-Confluencemicrosoft-tutorial/appregistrations.png)
   
    b. Haga clic en **Puntos de conexión** para abrir el cuadro de diálogo **Puntos de conexión**.  
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-Confluencemicrosoft-tutorial/endpointicon.png)

    c. Haga clic en el botón Copiar para copiar la dirección URL del **DOCUMENTO DE METADATOS DE FEDERACIÓN** y péguela en el Bloc de notas.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-Confluencemicrosoft-tutorial/endpoint.png)
     
    d. Ahora, vaya a la página de propiedades de **Confluence SAML SSO by Microsoft** y copie el **Id. de aplicación** con el botón **Copiar** y péguelo en el Bloc de notas.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-Confluencemicrosoft-tutorial/appid.png)

    e. Genere la **dirección URL de metadatos** con el patrón siguiente: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>` y copie este valor en el Bloc de notas, ya que se usará posteriormente para la configuración del complemento.

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-Confluencemicrosoft-tutorial/tutorial_general_400.png)

6. En otra ventana del explorador web, inicie sesión en la instancia de Confluence como administrador.

7. Mantenga el mouse encima del icono de engranaje y haga clic en **Complementos**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon1.png)

8. Descargue el complemento del [Centro de descarga de Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=56503). Cargue manualmente el complemento proporcionado por Microsoft mediante el menú **Cargar complemento**. La descarga del complemento está contemplada en el [Acuerdo de servicio de Microsoft](https://www.microsoft.com/en-us/servicesagreement/). 
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon12.png)

9. Una vez instalado el complemento, aparece en la sección de complementos **Instalados por el usuario** de **Administrar complemento**. Haga clic en **Configurar** para configurar el nuevo complemento.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon13.png)

10. Siga estos pasos en la página de configuración:

    ![Configurar inicio de sesión único](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon52.png)
 
    > [!TIP]
    > Asegúrese de que hay un solo certificado asignado a la aplicación, de forma que no se produzca ningún error en la resolución de los metadatos. Si hay varios certificados, el administrador recibe un error después de resolver los metadatos.

    a. En **Dirección URL de metadatos** pegue la **dirección URL de metadatos** generada desde Azure AD y haga clic en el botón **Resolver**. Se lee la dirección URL de metadatos de IdP y se rellena toda la información de campos.

    b. Copie los valores **Identificador, Dirección URL de respuesta y Dirección URL de inicio de sesión**, y péguelos en los cuadros de texto **Identificador, Dirección URL de respuesta y Dirección URL de inicio de sesión**, respectivamente, en la sección **Dominio y direcciones URL de Confluence SAML SSO by Microsoft** de Azure Portal.

    c. En **Nombre del botón de inicio de sesión** escriba el nombre del botón que la organización quiere que los usuarios vean en la pantalla de inicio de sesión.

    d. En **SAML User ID Locations** (Ubicaciones de Id. de usuario de SAML), seleccione **User ID is in the NameIdentifier element of the Subject statement** (El Id. de usuario está en el elemento NameIdentifier de la instrucción Subject) o **User ID is in an Attribute element** (El Id. de usuario está en un elemento Attribute).  Este identificador debe ser el identificador de usuario de Confluence. Si el identificador de usuario no coincide, el sistema no permitirá que los usuarios inicien sesión. 

    > [!Note]
    > La ubicación del Id. de usuario de SAML predeterminada es el identificador de nombre. Puede cambiarlo a una opción de atributo y escribir el nombre de atributo adecuado.
    
    e. Si selecciona la opción **User ID is in an Attribute element** (El Id. de usuario está en un elemento Attribute), escriba el nombre del atributo cuando se espera el Id. de usuario en el cuadro de texto **Nombre del atributo**. 

    f. Si se usa el dominio federado (por ejemplo, ADFS, etc.) con Azure AD, haga clic en la opción **Habilitar detección de dominio principal** y configure el **nombre de dominio**.
    
    g. En **Nombre de dominio**, escriba el nombre del dominio en el caso de inicios de sesión basados en ADFS.

    h. Active **Habilitar cierre de sesión único** si quiere que se cierre la sesión de Azure AD cuando un usuario cierre la sesión de Confluence. 

    i. Haga clic en el botón **Save** (Guardar) para guardar la configuración.


> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="creating-a-confluence-saml-sso-by-microsoft-test-user"></a>Creación de un usuario de prueba de Confluence SAML SSO by Microsoft

Para permitir que los usuarios de Azure AD inicien sesión en el servidor local de Confluence, se deben aprovisionar en Confluence SAML SSO by Microsoft. Para Confluence SAML SSO by Microsoft, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión como administrador en el servidor local de Confluence.

2. Mantenga el mouse encima del icono de engranaje y haga clic en **Administración de usuarios**.

    ![Agregar empleado](./media/active-directory-saas-confluencemicrosoft-tutorial/user1.png) 

3. En la sección Usuarios, haga clic en la pestaña **Agregar usuarios**. En la página del cuadro de diálogo **Agregar un usuario**, realice los siguientes pasos:

    ![Agregar empleado](./media/active-directory-saas-confluencemicrosoft-tutorial/user2.png) 

    a. En el cuadro de texto **Nombre de usuario**, escriba el correo electrónico de un usuario, por ejemplo, Britta Simon.

    b. En el cuadro de texto **Nombre completo**, escriba el nombre completo de un usuario, por ejemplo, Britta Simon.

    c. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

    d. En el cuadro de texto **Contraseña**, escriba la contraseña de Britta Simon.

    e. Haga clic en **Confirmar contraseña** y vuelva a escribir la contraseña.
    
    f. Haga clic en el botón **Agregar**.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Confluence SAML SSO by Microsoft.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Confluence SAML SSO by Microsoft, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Confluence SAML SSO by Microsoft**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Confluence SAML SSO by Microsoft en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Confluence SAML SSO by Microsoft.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_203.png

