---
title: "Tutorial: Integración de Azure Active Directory con SAP HANA| Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAP HANA."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: a7e73f6ee763d1005ad85935cf2d8f6b24ecf116
ms.contentlocale: es-es
ms.lasthandoff: 07/27/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Tutorial: Integración de Azure Active Directory con SAP HANA

En este tutorial, obtendrá información sobre cómo integrar SAP HANA con Azure Active Directory (Azure AD).

La integración de SAP HANA con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a SAP HANA.
- Puede habilitar que los usuarios inicien sesión automáticamente en SAP HANA (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SAP HANA, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en SAP HANA
- Instancia HANA en ejecución en cualquier IaaS pública, instancias locales, VM de Azure o instancias grandes de SAP en Azure
- La interfaz web de administración de XSA, así como HANA Studio instalados en la instancia HANA.

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción de SAP HANA. Pruebe primero la integración en el entorno de desarrollo o de ensayo de la aplicación y, después, use el entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de SAP HANA desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-sap-hana-from-the-gallery"></a>Adición de SAP HANA desde la galería
Para configurar la integración de SAP HANA en Azure AD, será preciso que agregue SAP HANA desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar SAP HANA desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **SAP HANA**, seleccione **SAP HANA** en el panel de resultados y, luego, haga clic en el botón **Agregar** para añadir la aplicación. 

    ![Nueva aplicación](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con SAP HANA con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de SAP HANA para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SAP HANA.

Para establecer la relación de vínculo, en SAP HANA, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con SAP HANA, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SAP HANA](#creating-a-sap-hana-test-user)**: el objetivo es tener un homólogo de Britta Simon en SAP HANA que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación SAP HANA.

**Para configurar el inicio de sesión único de Azure AD con SAP HANA, siga estos pasos:**

1. En la página de integración de la aplicación **SAP HANA** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_samlbase.png)

3. En la sección **Dominio y direcciones URL de SAP HANA**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_url.png)

    a. En el cuadro de texto **Identificador**, escriba: `HA100` 

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte al cliente de SAP HANA](https://cloudplatform.sap.com/contact.html) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >Si el certificado no está activo, actívelo haciendo clic en la casilla "Make new certificate active" (Activar nuevo certificado) en Azure AD. 

5. La aplicación SAP HANA espera las aserciones de SAML en un formato específico. La siguiente captura de pantalla le muestra un ejemplo de esto. Aquí hemos asignado el **identificador de usuario** con la función **ExtractMailPrefix()** de **user.mail**. Esto da como resultado el valor de prefijo del correo electrónico del usuario que es el identificador de usuario único. Este se envía a la aplicación SAP HANA con cada respuesta correcta.

    ![Configurar inicio de sesión único](./media/active-directory-saas-saphana-tutorial/attribute.png)

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**:

    a. En la lista desplegable **Identificador de usuario**, seleccione **ExtractMailPrefix**.
    
    b. En la lista desplegable **Correo**, seleccione **user.mail**.

7. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-saphana-tutorial/tutorial_general_400.png)
    
8. Para configurar el inicio de sesión único en **SAP HANA**, inicie sesión en la **consola web de HANA XSA** examinando el extremo HTTPS respectivo.

    > [!Note]
    > En la configuración predeterminada, la dirección URL redirige la solicitud a una pantalla de inicio de sesión, lo que requiere las credenciales de un usuario de base de datos de SAP HANA autenticado complete el proceso de inicio de sesión. El usuario que inicia sesión debe tener los privilegios necesarios para realizar tareas de administración de SAML.

9. En la interfaz web de XSA, vaya al **proveedor de identidades SAML** y desde allí, haga clic en el botón **"+"** en la parte inferior de la pantalla para mostrar el panel de adición de información del proveedor de identidades y realice los pasos siguientes:

    ![Agregación del proveedor de identidades](./media/active-directory-saas-saphana-tutorial/sap1.png)

    a. En el panel de **adición de información del proveedor de identidades**, pegue el contenido del XML de metadatos, que ha descargado desde Azure Portal en el cuadro de texto **Metadatos**.

    ![Adición de la configuración del proveedor de identidades](./media/active-directory-saas-saphana-tutorial/sap2.png)

    b. Si el contenido del documento XML es válido, el proceso de análisis extrae la información necesaria que insertar en los campos **Asunto, Id. de entidad y Emisor** en el área de la pantalla Datos generales y los campos Dirección URL en el área de pantalla Destino, por ejemplo,  **dirección URL base y dirección URL de SingleSignOn (*)**.

    ![Adición de la configuración del proveedor de identidades](./media/active-directory-saas-saphana-tutorial/sap3.png)

    c. En el cuadro Nombre del área de la pantalla Datos generales, escriba un nombre para el nuevo proveedor de identidades de SSO de SAML.

    > [!Note]
    > El nombre del IDP de SAML es obligatorio y debe ser único; aparece en la lista de IDP de SAML disponibles que se muestra, si selecciona SAML como método de autenticación para aplicaciones de SAP HANA XS, por ejemplo, en el área de la pantalla Autenticación de la herramienta XS Artifact Administration.

10. Guarde los detalles del proveedor de identidades de SAML nuevo. Elija **Guardar** para guardar los detalles del proveedor de identidades de SAML y agregue el nuevo SAML IDP a la lista de IDP de SAML conocidos.

    ![Botón Guardar](./media/active-directory-saas-saphana-tutorial/sap4.png)

11. En HANA Studio, dentro de las propiedades del sistema de la pestaña **Configuración**, simplemente filtre la configuración mediante **saml** y ajuste **assertion_timeout** de **10 s** a **120 s**.

    ![assertion_timeout setting](./media/active-directory-saas-saphana-tutorial/sap7.png)

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-saphana-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-saphana-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Botón Agregar](./media/active-directory-saas-saphana-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Cuadro de diálogo Usuario](./media/active-directory-saas-saphana-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-sap-hana-test-user"></a>Creación de un usuario de prueba en SAP HANA

Para permitir que los usuarios de Azure AD inicien sesión en SAP HANA, tienen que aprovisionarse en SAP HANA.
SAP HANA admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

Si tiene que crear manualmente un usuario, siga los pasos siguientes:

>[!Note]
>Puede cambiar la autenticación externa utilizada por el usuario.
Los usuarios externos se autentican utilizando un sistema externo, por ejemplo un sistema de Kerberos. Para obtener información detallada acerca de las identidades externas, póngase en contacto con su [administrador de dominio](https://cloudplatform.sap.com/contact.html).

1. Abra [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) como administrador y active el usuario de base de datos de SSO de SAML.

    ![crear usuario](./media/active-directory-saas-saphana-tutorial/sap5.png)

2. Marque la casilla invisible a la izquierda del **SAML** y siga el vínculo Configurar.

3. Haga clic en **Agregar** el IDP de SAML y haga clic en **Aceptar** seleccionando el IDP de SAML adecuado.

4. Agregue la **identidad externa** (p. ej. BrittaSimon aquí) o elija **"Cualquiera"** y haga clic en **Aceptar**.

    >[!Note]
    >Si no está activada la casilla "CUALQUIERA", el nombre de usuario en HANA debe coincidir exactamente con el nombre del usuario en el UPN antes del sufijo de dominio (es decir, BrittaSimon@contoso.com se convertiría en BrittaSimon en HANA).

5. Para realizar pruebas, asigne todos los roles de **"XS"** para el usuario.

    ![Asignación de roles](./media/active-directory-saas-saphana-tutorial/sap6.png)

    > [!TIP]
    > Debe conceder solo los permisos adecuados para los casos de uso.

6. Guarde el usuario.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a SAP HANA.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a SAP HANA, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SAP HANA**.

    ![Asignar usuario](./media/active-directory-saas-saphana-tutorial/tutorial_saphana_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de SAP HANA en el panel de acceso, debería iniciar sesión automáticamente en la aplicación SAP HANA.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-saphana-tutorial/tutorial_general_203.png


