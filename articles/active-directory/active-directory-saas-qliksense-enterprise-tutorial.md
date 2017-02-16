---
title: "Tutorial: Integración de Azure Active Directory con Qlik Sense Enterprise | Microsoft Doc"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Qlik Sense Enterprise."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 91f8a95bdab98f079b748b5391e9b611378c6e79
ms.openlocfilehash: ce18993832ce4953a1c3b21707206cdd06580edd


---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Tutorial: Integración de Azure Active Directory con Qlik Sense Enterprise
En este tutorial, aprenderá a integrar Qlik Sense Enterprise con Azure Active Directory (Azure AD).

La integración de Qlik Sense Enterprise con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Qlik Sense Enterprise.
* Puede permitir que los usuarios inicien sesión automáticamente en Qlik Sense Enterprise (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Qlik Sense Enterprise, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en Qlik Sense Enterprise

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Qlik Sense Enterprise desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Adición de Qlik Sense Enterprise desde la galería
Para configurar la integración de Qlik Sense Enterprise en Azure AD, deberá agregar esta solución desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Qlik Sense Enterprise desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Active Directory][1]

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Qlik Sense Enterprise**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. En el panel de resultados, seleccione **Qlik Sense Enterprise** y, después, haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Qlik Sense Enterprise utilizando un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Qlik Sense Enterprise para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Qlik Sense Enterprise.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Qlik Sense Enterprise.

Para configurar y probar el inicio de sesión único de Azure AD con Qlik Sense Enterprise, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Qlik Sense Enterprise](#creating-a-qliksense-enterprise-test-user)**: para tener un homólogo de Britta Simon en Qlik Sense Enterprise que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Qlik Sense Enterprise.

**Para configurar el inicio de sesión único de Azure AD con Qlik Sense Enterprise, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Qlik Sense Enterprise** del Portal de Azure clásico, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 

2. En la página **¿Cómo desea que los usuarios inicien sesión en Qlik Sense Enterprise?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png) 

3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png) 
   
    a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL que los usuarios usan para iniciar sesión en la aplicación Qlik Sense Enterprise con el siguiente patrón: **https://\<Nombre de host completo de Qlik Sense\>:443/<Prefijo de proxy virtual\>/samlauthn/**.
   
    > [!NOTE]
    > No se olvide de escribir la barra diagonal al final de este URI,  ya que es obligatorio.
    > 
    > 
   
    b. click **Siguiente**

4. En la página **Configurar inicio de sesión único en Qlik Sense Enterprise** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)
   
    a. Haga clic en **Descargar metadatos**y luego guarde el archivo en el equipo.  A continuación, tendremos que editar este archivo de metadatos para poder cargarlo en el servidor de Qlik Sense.
   
    b. Haga clic en **Next**.

5. Prepare el archivo XML de metadatos de federación para que pueda cargarse en el servidor de Qlik Sense.
   
    > [!NOTE]
    > Antes de cargar los metadatos de IdP en el servidor de Qlik Sense, hay que editar el archivo para quitar información y, de este modo, garantizar que la conexión entre Azure AD y el servidor de Qlik se realice sin problemas.
    > 
    > 
   
    ![QlikSense][qs24]
   
    a. Abra el archivo FederationMetaData.xml descargado de Azure en un editor de texto.
   
    b. Busque el valor **RoleDescriptor**.  Verá cuatro entradas (dos pares de etiquetas de elementos de apertura y cierre).
   
    c. Elimine del archivo las etiquetas de RoleDescriptor y toda la información intermedia.
   
    d. Guarde el archivo y téngalo a mano, ya que vamos a utilizarlo más adelante en este documento.
6. Acceda a Qlik Sense Qlik Management Console (QMC) como un usuario que pueda crear configuraciones de proxy virtual.
7. En QMC, haga clic en el elemento de menú Virtual Proxy (Proxy virtual).
   
    ![QlikSense][qs6] 
8. Haga clic en el botón Crear nuevo situado en la parte inferior de la pantalla.
   
    ![QlikSense][qs7]
9. Aparece la pantalla de edición Virtual Proxy (Proxy virtual).  En la parte derecha de la pantalla hay un menú con el que puede hacer que se muestren las opciones de configuración.
   
    ![QlikSense][qs9]
10. Active la opción de menú Identificación y escriba la información de identificación de la configuración de proxy virtual de Azure.
    
    ![QlikSense][qs8]  
    
    a. El campo Descripción es un nombre descriptivo de la configuración de proxy virtual.  Escriba un valor para este campo.
    
    b. El campo Prefijo identifica el punto de conexión de proxy virtual para conectarse a Qlik Sense a través del inicio de sesión único de Azure AD.  Escriba un nombre de prefijo único para este proxy virtual.
    
    c. Session inactivity timeout (minutes) (Tiempo de espera de inactividad de la sesión [minutos]) es el tiempo de espera de las conexiones que se establecen a través de este proxy virtual.
    
    d. Session cookie header name (Nombre de encabezado de cookie de sesión) es el nombre de la cookie que almacena el identificador de la sesión de Qlik Sense que recibe un usuario tras autenticarse correctamente.  Este nombre debe ser único.
11. Haga clic en la opción de menú Autenticación para que se muestre.  Aparecerá la pantalla Autenticación.
    
    ![QlikSense][qs10]
    
    a. El menú desplegable **Anonymous access mode** (Modo de acceso anónimo) determina si los usuarios anónimos pueden acceder a Qlik Sense a través del proxy virtual.  La opción predeterminada es No anonymous user (Ningún usuario anónimo).
    
    b. El menú desplegable **Método de autenticación** determina el esquema de autenticación del proxy virtual que se utilizará.  Seleccione SAML en la lista desplegable.  Se mostrarán más opciones.
    
    c. En el campo **SAML host URI**(URI de host SAML), especifique el nombre de host que tendrán que escribir los usuarios para acceder a Qlik Sense a través de este proxy virtual de SAML.  El nombre de host es el URI del servidor de Qlik Sense.
    
    d. En el campo **SAML entity ID** (Id. de entidad SAML), escriba el mismo valor que especificó en el campo de URI de host SAML.
    
    e. **SAML IdP metadata** (Metadatos de IdP de SAML) es el archivo que se editó anteriormente en la sección de **modificación de los metadatos de federación desde la configuración de Azure AD**.  **Antes de cargar los metadatos de IdP, hay que editar el archivo** para quitar información y, de este modo, garantizar que la conexión entre Azure AD y el servidor de Qlik se realice sin problemas.  **Consulte las instrucciones anteriores si aún no ha modificado el archivo.**   Si ya lo ha hecho, haga clic en el botón Examinar y seleccione el archivo de metadatos editado para cargarlo en la configuración de proxy virtual.
    
    f. Escriba el nombre de atributo o la referencia de esquema del atributo SAML que representa el **id. de usuario** que Azure AD enviará al servidor de Qlik Sense.  La información de referencia del esquema está disponible en las pantallas de la aplicación de Azure que aparecen tras el proceso de configuración.  Para usar el atributo de nombre, **escriba: http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    
    g. Escriba el valor del **directorio de usuarios** que se asociará a los usuarios cuando se autentiquen en el servidor de Qlik Sense a través de Azure AD.  Los valores codificados deben estar entre **corchetes**.  Para utilizar un atributo enviado en la aserción SAML de Azure AD, escriba el nombre del atributo en este cuadro de texto **sin** corchetes.
    
    h. El **algoritmo de firma SAML** establece la firma de certificados del proveedor de servicios (en este caso, el servidor de Qlik Sense) en la configuración de proxy virtual.  Si el servidor de Qlik Sense utiliza un certificado de confianza generado mediante el proveedor de servicios criptográficos AES y RSA mejorado de Microsoft, cambie el algoritmo de firma SAML a **SHA-256**.
    
    i. La sección de asignación de atributos SAML permite enviar otros atributos, como grupos, a Qlik Sense para utilizarse en reglas de seguridad.
12. Haga clic en la opción de menú Equilibrio de carga para que se muestre.  Aparecerá la pantalla Equilibrio de carga.
    
    ![QlikSense][qs11]
13. Haga clic en el botón Add new server node (Agregar nuevo nodo de servidor), seleccione los nodos de motor a los que Qlik Sense enviará sesiones para equilibrar la carga y, luego, haga clic en el botón Agregar.
    
    ![QlikSense][qs12]
14. Haga clic en la opción de menú Opciones avanzadas para que se muestre. Aparece la pantalla Opciones avanzadas.
    
    ![QlikSense][qs13]
    
    a. La lista blanca de Host identifica los nombres de host que se aceptan al conectarse al servidor de Qlik Sense.  **Escriba el nombre de host que especificarán los usuarios al conectarse al servidor de Qlik sentido.**  El nombre de host es el mismo valor que el URI de host SAML, pero sin "https://".
15. Haga clic en el botón Aplicar.
    
    ![QlikSense][qs14]
16. Haga clic en Aceptar para aceptar el mensaje de advertencia que indica que se reiniciará el proxy vinculado al proxy virtual.
    
    ![QlikSense][qs15]
17. En la parte derecha de la pantalla, aparece el menú de elementos Asociado.  Haga clic en la opción de menú Servidores proxy.
    
    ![QlikSense][qs16]
18. Aparecerá la pantalla Servidores proxy.  Haga clic en el botón Vincular de la parte inferior para vincular un proxy al proxy virtual.
    
    ![QlikSense][qs17]
19. Seleccione el nodo de proxy que admitirá esta conexión de proxy virtual y haga clic en el botón Vincular.  Cuando termine el proceso de vinculación, el proxy aparecerá debajo de los servidores proxy asociados.
    
    ![QlikSense][qs18]
    ![QlikSense][qs19]
20. Cuando pasen entre unos cinco y diez segundos, se mostrará el mensaje Actualizar QMC.  Haga clic en el botón Actualizar QMC.
    
    ![QlikSense][qs20]
21. Cuando se actualice QMC, haga clic en el elemento de menú Virtual proxies (Servidores proxy virtuales). La nueva entrada SAML virtual proxy (Proxy virtual de SAML) se muestra en la tabla que aparece en la pantalla.  Haga clic en la entrada de proxy virtual.
    
    ![QlikSense][qs51]
22. En la parte inferior de la pantalla, se activará el botón Download SP metadata (Descargar metadatos de SP).  Haga clic en el botón Download SP metadata (Descargar metadatos de SP) para guardar los metadatos en un archivo.
    
    ![QlikSense][qs52]
23. Abra el archivo de metadatos de SP.  Observe las entradas **entityID** y **AssertionConsumerService**.  Estos valores son equivalentes a los de **Identificador** y **Dirección URL de inicio de sesión** de la configuración de la aplicación de Azure AD. Si no coinciden, debe reemplazarlos en el Asistente para configuración de la aplicación de Azure AD.
    
    ![QlikSense][qs53]
24. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
    
    ![Inicio de sesión único de Azure AD ][10]
25. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
    
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png) 

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png) 

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png) 

5. En el cuadro de diálogo **Proporcione información sobre este usuario**, siga estos pasos:  ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.

6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png) 
   
    a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
    b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
    d. En la lista **Rol**, seleccione **Usuario**.
   
    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png) 

8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Complete**.   

### <a name="creating-an-qlik-sense-enterprise-test-user"></a>Creación de un usuario de prueba de Qlik Sense Enterprise
En esta sección, creará un usuario llamado "Britta Simon" en Qlik Sense Enterprise. Colabore con el equipo de soporte técnico de Qlik Sense Enterprise para agregar los usuarios en esta plataforma.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Qlik Sense Enterprise.

![Asignar usuario][200] 

**Para asignar el usuario Britta Simon a Qlik Sense Enterprise, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Qlik Sense Enterprise**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png) 

3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]

4. En la lista Usuarios, seleccione **Britta Simon**.

5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

## <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único
En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Qlik Sense Enterprise del panel de acceso, debería iniciar sesión automáticamente en la aplicación Qlik Sense Enterprise.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png



<!--HONumber=Nov16_HO4-->


