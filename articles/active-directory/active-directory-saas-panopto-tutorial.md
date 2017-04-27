---
title: "Tutorial: Integración de Azure Active Directory con Panopto | Microsoft Docs"
description: "Aprenda cómo usar Panopto con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 89c88e23-93ce-4970-9baa-1104c4e8fe4a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 078a2ea0db006cf976f89a55c65a536a7b9f04aa
ms.lasthandoff: 04/03/2017


---

# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>Tutorial: Integración de Azure Active Directory con Panopto
El objetivo de este tutorial es mostrar la integración de Azure y Panopto. 

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Panopto

Después de completar este tutorial, los usuarios de Azure AD asignados a Panopto podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía Panopto (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Panopto
2. Configuración del inicio de sesión único (SSO)
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-panopto-tutorial/IC777665.png "Escenario")

## <a name="enable-the-application-integration-for-panopto"></a>Habilitación de la integración de aplicaciones para Panopto
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Panopto.

**Siga estos pasos para habilitar la integración de aplicaciones para Panopto:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-panopto-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-panopto-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-panopto-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **Panopto**.
   
   ![Galería de Appkication](./media/active-directory-saas-panopto-tutorial/IC777666.png "Galería de Appkication")
7. En el panel de resultados, seleccione **Panopto** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![Panopto](./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
   
## <a name="configure-single-sign-on"></a>Configurar inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Panopto con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  

Como parte de este procedimiento, es necesario crear un archivo de certificado codificado en base 64. 

Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Para configurar el inicio de sesión único (SSO), realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Panopto**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-panopto-tutorial/IC777667.png "Configurar inicio de sesión único")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Panopto?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-panopto-tutorial/IC777668.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **Dirección URL de inicio de sesión de Panopto**, escriba la dirección URL con el siguiente patrón "*https://\<nombre-inquilino\>. Panopto.com*" y, luego, haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-panopto-tutorial/IC777528.png "Configurar dirección URL de la aplicación")
4. En la página **Configuración de inicio de sesión único en Panopto**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-panopto-tutorial/IC777669.png "Configurar inicio de sesión único")
5. En otra ventana del explorador web, inicie sesión en el sitio de la compañía Panopto como administrador.
6. En la barra de herramientas de la izquierda, haga clic en **Sistema** y, luego, en **Proveedores de identidades**.
   
   ![Sistema](./media/active-directory-saas-panopto-tutorial/IC777670.png "Sistema")
7. Haga clic en **Agregar proveedor**.
   
   ![Proveedores de identidades](./media/active-directory-saas-panopto-tutorial/IC777671.png "Proveedores de identidades")
8. En la sección de del proveedor SAML, lleve a cabo estos pasos:
   
   ![Configuración de SaaS](./media/active-directory-saas-panopto-tutorial/IC777672.png "Configuración de SaaS")
   
   1. En la lista **Tipo de proveedor**, seleccione **SAML20**.
   2. En el cuadro de texto **Nombre de instancia** , escriba un nombre para la instancia.
   3. En el cuadro de texto **Descripción detallada** , escriba una descripción detallada.
   4. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Panopto**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Emisor**.
   5. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Panopto**, copie el valor de **URL de inicio de sesión único de SAML** y péguelo en el cuadro de texto **Bounce Page Url** (Dirección URL de página de devolución).
   6. Cree un archivo **codificado en base 64** a partir del certificado descargado.    
   
      >[!TIP]
      >Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).
      >
      
   7. Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **PublicKey** .
   8. Haga clic en **Guardar**.

 ![Guardar](./media/active-directory-saas-panopto-tutorial/IC777673.png "Guardar")
9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
  ![Configurar inicio de sesión único](./media/active-directory-saas-panopto-tutorial/IC777674.png "Configurar inicio de sesión único")
   
## <a name="configure-user-provisioning"></a>Configurar aprovisionamiento de usuarios

No hay elemento de acción para que configure el aprovisionamiento de usuarios en Panopto.  
Cuando un usuario asignado intenta iniciar sesión en Panopto desde el panel de acceso, Panopto comprueba si el usuario existe.  

Si no hay cuentas de usuario disponibles, Panopto crea una automáticamente.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Panopto ofrecida por Panopto para aprovisionar cuentas de usuario de Azure AD.
>
>

## <a name="assign-users"></a>Asignar usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

**Para asignar usuarios a Panopto, lleve a cabo los siguientes pasos:**

1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Panopto**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-panopto-tutorial/IC777675.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-panopto-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único (SSO), abra el panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


