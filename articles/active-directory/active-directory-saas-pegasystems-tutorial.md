---
title: "Tutorial: Integración de Azure Active Directory con Pega Systems | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Pega Systems."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 924abe287b5e39bb77a418b56b5c33195095e07a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Tutorial: integración de Azure Active Directory con Pega Systems

En este tutorial, aprenderá a integrar Pega Systems con Azure Active Directory (Azure AD).

La integración de Pega Systems con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Pega Systems.
- Puede permitir que los usuarios inicien sesión automáticamente en Pega Systems (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Pega Systems, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Pega Systems

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Pega Systems desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-pega-systems-from-the-gallery"></a>Adición de Pega Systems desde la galería
Para configurar la integración de Pega Systems en Azure AD, será preciso que agregue Pega Systems desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Pega Systems desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Pega Systems**, seleccione **Pega Systems** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Pega Systems en la lista de resultados](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Pega Systems con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Pega Systems para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Pega Systems.

Para establecer la relación de vínculo en Pega Systems, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Pega Systems, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en Pega Systems](#create-a-pega-systems-test-user)**: para tener un homólogo de Britta Simon en Pega Systems que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Pega Systems.

**Para configurar el inicio de sesión único de Azure AD con Pega Systems, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Pega Systems** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

3. En la sección **Dominio y direcciones URL de Pega Systems**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Pega Systems](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`.

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Pega Systems](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_url1.png)

    En el cuadro de texto **Estado de la retransmisión**, escriba una dirección URL que siga este patrón: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`
     
    > [!NOTE] 
    > Estos valores no son reales. Actualícelos con el identificador real, la dirección URL de respuesta y la dirección URL de estado de la retransmisión. Puede encontrar los valores de Identificador y URL de respuesta de la aplicación Pega que se explica más adelante en este tutorial. Para Estado de la retransmisión, póngase en contacto con el [equipo de soporte técnico del cliente de Pega Systems](https://www.pega.com/contact-us) para obtener el valor. 

5. La aplicación Pega Systems espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. Estas notificaciones son específicas del cliente y dependen de sus requisitos. Las siguientes notificaciones opcionales son un ejemplo que puede configurar para la aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute.png)

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo del token de SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo | Valor de atributo |
    | ------------------- | -------------------- |    
    | uid | *********** |
    | cn  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | organization | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | workgroup | *********** |
    | Teléfono | *********** |

    > [!NOTE]
    > Se trata de valores específicos del cliente. Proporcione los valores adecuados.

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-pegasystems-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.

7. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
8. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-pegasystems-tutorial/tutorial_general_400.png)
    
9. Para configurar el inicio de sesión único en **Pega Systems**, abra el **portal de Pega** con cuenta de administrador en otra ventana del explorador.

10. Seleccione **Create** -> **SysAdmin** -> **Authentication Service** (Crear > SysAdmin > Servicio de autenticación).

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
11. Realice las acciones siguientes en la pantalla **Create Authentication Service** (Crear servicio de autenticación):

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Seleccione **SAML 2.0** en Tipo.

    b. En el cuadro de texto **Nombre**, escriba cualquier nombre, por ejemplo, SSO de Azure AD.

    c. En el cuadro de texto **Descripción breve**, escriba cualquier descripción.  

    d. Haga clic en **Create and open** (Crear y abrir). 
    
12. En la sección **Identity Provider (IdP) information** (Información de proveedor de identidades [IdP]), haga clic en **Import IdP metadata** (Importar metadatos de IdP) y busque el archivo de metadatos que ha descargado desde Azure Portal. Haga clic en **Enviar** para cargar los metadatos.

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
13. Esto rellenará los datos del proveedor de identidades tal como se muestra a continuación.

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
14. Realice las siguientes acciones en la sección **Service Provider (SP) settings** (Configuración de proveedor de servicio [SP]):

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Copie el valor de **Entity Identification** (Identificación de la entidad) y péguelo de nuevo en el cuadro de texto **Identificador** de Azure Portal.

    b.  Copie el valor de **Assertion Consumer Service (ACS) location** (Ubicación del servicio de consumidor de aserciones [ACS]) y péguelo de nuevo en el cuadro de texto **URL de respuesta** de Azure Portal.

    c. Seleccione **Disable request signing** (Deshabilitar firma de solicitudes).

15. Haga clic en **Guardar**
    
> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-pegasystems-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-pega-systems-test-user"></a>Creación de un usuario de prueba de Pega Systems

El objetivo de esta sección es crear un usuario llamado Britta Simon en Pega Systems. Trabaje con el [equipo de soporte técnico del cliente de Pega Systems](https://www.pega.com/contact-us) para crear usuarios en Pega Systems.


### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Pega Systems.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Pega Systems, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Pega Systems**.

    ![Vínculo a Pega Systems en la lista de aplicaciones](./media/active-directory-saas-pegasystems-tutorial/tutorial_pegasystems_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Pega Systems en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de Pega Systems.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pegasystems-tutorial/tutorial_general_203.png

