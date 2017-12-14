---
title: "Tutorial: Integración de Azure Active Directory con Cisco Spark | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Cisco Spark."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: c7ee96abacffc6599187ded9a5049cc3bf672e0a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Tutorial: integración de Azure Active Directory con Cisco Spark

En este tutorial, aprenderá a integrar Cisco Spark con Azure Active Directory (Azure AD).

Integrar Cisco Spark con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Cisco Spark.
- Puede permitir que los usuarios inicien sesión automáticamente en Cisco Spark (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Cisco Spark, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Cisco Spark

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Cisco Spark desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-cisco-spark-from-the-gallery"></a>Adición de Cisco Spark desde la galería
Para configurar la integración de Cisco Spark en Azure AD, será preciso que agregue Cisco Spark desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Cisco Spark desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Cisco Spark**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_search.png)

5. En el panel de resultados, seleccione **Cisco Spark** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, va a configurar y probar el inicio de sesión único de Azure AD con Cisco Spark con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Cisco Spark para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Cisco Spark.

Para establecer la relación de vínculo, en Cisco Spark, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Cisco Spark, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Cisco Spark](#creating-a-cisco-spark-test-user)**: para tener un homólogo de Britta Simon en Cisco Spark que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Cisco Spark.

**Para configurar el inicio de sesión único de Azure AD con Cisco Spark, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Cisco Spark**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_samlbase.png)

3. En la sección **Dominio y direcciones URL de Cisco Spark**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una URL como: `https://web.ciscospark.com/#/signin`

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://idbroker.webex.com/<companyname>`

    > [!NOTE] 
    > Este valor no es real. Actualícelo con el identificador real. Póngase en contacto con el [equipo de soporte técnico de Cisco Spark](https://support.ciscospark.com/) para obtener este valor. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_certificate.png) 

5. La aplicación de Cisco Spark espera que las aserciones SAML contengan atributos específicos. Configure los siguientes atributos para esta aplicación. Puede administrar los valores de estos atributos en la sección **Atributos de usuario** de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_07.png) 

6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo  | Valor de atributo |
    | --------------- | -------------------- |    
    |   uid    | user.userprincipalname |   

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_attribute_04.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_attribute_05.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.

7. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_400.png)

8. Inicie sesión en [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) (Administración de colaboración en la nube de Cisco) con sus credenciales completas de administrador.

9. Seleccione **Settings** (Configuración) y en la sección **Authentication** (Autenticación), haga clic en **Modify** (Modificar).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)
    
10. Seleccione **Integrate a 3rd-party identity provider. (Advanced)** (Integrar un proveedor de identidades de terceros [avanzado]).y vaya a la pantalla siguiente.

11. En la página **Import Idp Metadata** (Importar metadatos de LDP) arrastre y coloque el archivo de metadatos de Azure AD en la página o utilice la opción de explorador de archivos para buscar y cargar el archivo de metadatos de Azure AD. Después, seleccione **Require certificate signed by a certificate authority in Metadata (more secure)** (Requerir certificado firmado por una entidad de certificación en metadatos [más seguro]) y haga clic en **Next** (Siguiente). 
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)

12. Seleccione **Test SSO Connection** (Probar SSO de conexión) y cuando se abra una nueva pestaña de explorador, autentíquese con Azure AD mediante el inicio de sesión.

13. Vuelva a la pestaña del explorador de **Cisco Cloud Collaboration Management**. Si la prueba se realizó correctamente, seleccione **This test was successful. Enable Single Sign-On option** (Esta prueba se realizó correctamente. Habilite la opción de inicio de sesión único) y haga clic en **Next** (Siguiente).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-cisco-spark-test-user"></a>Creación de un usuario de prueba de Cisco Spark

En esta sección, creará un usuario llamado Britta Simon en Cisco Spark. En esta sección, creará un usuario llamado Britta Simon en Cisco Spark.

1. Vaya a [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) con sus credenciales de administración completas.

2. Haga clic en **Usuarios** y después en **Administrar usuarios**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. En la ventana **Manage User** (Administrar usuario), seleccione **Manually add or modify users** (Agregar o modificar usuarios manualmente) y haga clic en **Next** (Siguiente).

4. Seleccione **Names and Email address** (Nombres y direcciones de correo electrónico). Después, rellene el cuadro de texto de la forma siguiente:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
    
    a. En el cuadro de texto **Nombre**, escriba **Britta**. 
    
    b. En el cuadro de texto **Apellidos**, escriba **Simon**.
    
    c. En el cuadro de texto **Dirección de correo electrónico**, escriba **britta.simon@contoso.com**.

5. Haga clic en el signo más para agregar a Britta Simon. A continuación, haga clic en **Siguiente**.

6. En la ventana **Add Services for Users** (Agregar servicios para los usuarios), haga clic en **Save** (Guardar) y después en **Finish** (Finalizar).

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Cisco Spark.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Cisco Spark, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Cisco Spark**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Cisco Spark en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Cisco Spark.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[100]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png

