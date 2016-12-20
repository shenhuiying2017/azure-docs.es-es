---
title: "Tutorial: Integración de Azure Active Directory con Cisco Spark | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Cisco Spark."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d809d2c29b7f3405dcd1619e96778e4d5af8e4aa


---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Tutorial: integración de Azure Active Directory con Cisco Spark
En este tutorial, aprenderá a integrar Cisco Spark con Azure Active Directory (Azure AD).

Integrar Cisco Spark con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Cisco Spark.
* Puede permitir que los usuarios inicien sesión automáticamente en Cisco Spark (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Cisco Spark, se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en **Cisco Spark**

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Cisco Spark desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-cisco-spark-from-the-gallery"></a>Adición de Cisco Spark desde la galería
Para configurar la integración de Cisco Spark en Azure AD, será preciso que agregue Cisco Spark desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Cisco Spark desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Cisco Spark**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)
7. En el panel de resultados, seleccione **Cisco Spark** y, a continuación, haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Cisco Spark con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Cisco Spark para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Cisco Spark.
Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **Nombre de usuario** en Cisco Spark. Para configurar y probar el inicio de sesión único de Azure AD con Cisco Spark, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Cisco Spark](#creating-a-cisco-spark-test-user)**: para tener un homólogo de Britta Simon en Cisco Spark que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Cisco Spark.

La aplicación de Cisco Spark espera que las aserciones SAML contengan atributos específicos. Configure los siguientes atributos para esta aplicación. Puede administrar el valor de estos atributos desde la pestaña **"Atributo"** de la aplicación. La siguiente captura de pantalla le muestra un ejemplo de esto.

![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**Para configurar el inicio de sesión único de Azure AD con Cisco Spark, siga estos pasos:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Cisco Spark**, en el menú de la parte superior, haga clic en **Atributos**.
   
    ![Configurar inicio de sesión único][5]
2. En el cuadro de diálogo **Atributos de token de SAML** , realice los siguientes pasos:
   
    a. Haga clic en **agregar atributo de usuario** para abrir el cuadro de diálogo **Agregar atributo de usuario**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
   
    b. En el cuadro de texto **Nombre de atributo**, escriba **uid**.
   
    c. En la lista **Valor de atributo**, seleccione **user.userprincipal**.
   
    d. Haga clic en **Complete**. Haga clic en **Aplicar cambios** en la parte inferior de la página.
3. En el menú de la parte superior, haga clic en **Inicio rápido**.
   
    ![Configurar inicio de sesión único][6]
4. En el Portal clásico, en la página de integración de aplicaciones de **Cisco Spark**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][7] 
5. En la página **¿Cómo desea que los usuarios inicien sesión en Cisco Spark?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)
6. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)

    a. En el cuadro de texto URL de inicio de sesión, escriba una dirección URL con el siguiente patrón: `https://web.ciscospark.com/#/signin`.

    b. Haga clic en **Siguiente**.


1. En la página **Configuración de inicio de sesión único en Cisco Spark**, haga clic en **Descargar metadatos** y luego guarde el archivo en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)
2. Inicie sesión en [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) (Administración de colaboración en la nube de Cisco) con sus credenciales completas de administrador.
3. Seleccione **Settings** (Configuración) y en la sección **Authentication** (Autenticación), haga clic en **Modify** (Modificar).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)
4. Seleccione **Integrate a 3rd-party identity provider. (Advanced)** (Integrar un proveedor de identidades de terceros [avanzado]).y vaya a la pantalla siguiente.
5. Haga clic en **Download Metadata File** (Descargar archivo de metadatos) y después guarde el archivo en el equipo.
6. En la página **Import Idp Metadata** (Importar metadatos de LDP) arrastre y coloque el archivo de metadatos de Azure AD en la página o utilice la opción de explorador de archivos para buscar y cargar el archivo de metadatos de Azure AD. Después, seleccione **Require certificate signed by a certificate authority in Metadata (more secure)** (Requerir certificado firmado por una entidad de certificación en metadatos [más seguro]) y haga clic en **Next** (Siguiente). 
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)
7. Seleccione **Test SSO Connection** (Probar SSO de conexión) y cuando se abra una nueva pestaña de explorador, autentíquese con Azure AD mediante el inicio de sesión.
8. Vuelva a la pestaña del explorador de **Cisco Cloud Collaboration Management**. Si la prueba se realizó correctamente, seleccione **This test was successful. Enable Single Sign-On option** (Esta prueba se realizó correctamente. Habilite la opción de inicio de sesión único) y haga clic en **Next** (Siguiente).
9. En el Portal de Azure AD clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
10. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
    
     ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-cisco-spark-test-user"></a>Creación de un usuario de prueba de Cisco Spark
En esta sección, creará un usuario llamado Britta Simon en Cisco Spark. En esta sección, creará un usuario llamado Britta Simon en Cisco Spark.

1. Vaya a [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) con sus credenciales de administración completas.
2. Haga clic en **Users** (Usuarios) y después en **Manage Users** (Administrar usuarios).
   
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

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **Cisco Spark**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Todos los usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

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


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


