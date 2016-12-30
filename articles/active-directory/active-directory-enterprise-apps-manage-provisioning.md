---
title: "Administración de aprovisionamiento de usuarios para aplicaciones empresariales en la versión preliminar de Azure Active Directory | Microsoft Docs"
description: "Aprenda a administrar el aprovisionamiento de cuentas de usuario para aplicaciones empresariales con la versión preliminar de Azure Active Directory."
services: active-directory
documentationcenter: 
author: asmalser
manager: femila
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/12/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 103eade46452d979705e06dd77441f42d7a514b8


---
# <a name="preview-managing-user-account-provisioning-for-enterprise-apps-in-the-new-azure-portal"></a>Versión preliminar: Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales en el nuevo Azure Portal
En este artículo se describe cómo utilizar [Azure Portal](https://portal.azure.com) para administrar el aprovisionamiento automático de cuentas de usuario y el desaprovisionamiento para las aplicaciones que lo admiten, especialmente aquellas que se han agregado en la categoría "destacada" de la Galería de aplicaciones de [Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Esta experiencia de administración en el nuevo Azure Portal está actualmente en versión preliminar pública y en este artículo se describen las nuevas características, así como algunas limitaciones temporales que tienen lugar durante el período de versión preliminar. [¿Qué hay en la versión preliminar?](active-directory-preview-explainer.md)

Para más información sobre el aprovisionamiento automático de cuentas de usuario, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md).

## <a name="finding-your-apps-in-the-new-portal"></a>Búsqueda de las aplicaciones en el nuevo portal
A partir de septiembre de 2016, todas las aplicaciones que se han configurado para un inicio de sesión único en un directorio, por un administrador de directorio mediante la [Galería de aplicaciones de Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) dentro del [Portal de Azure clásico](https://manage.windowsazure.com), ahora se pueden ver y administrar en el nuevo Azure Portal.

Estas aplicaciones se pueden encontrar en la sección **Aplicaciones empresariales** del nuevo Azure Portal, al que se puede acceder a través del menú **Más servicios** en el área de navegación izquierdo. Las aplicaciones empresariales son aplicaciones que se han implementado y se están utilizando por los usuarios de su organización.

![Hoja Aplicaciones empresariales][0]

Si se selecciona el vínculo **Todas las aplicaciones** de la izquierda, se muestra una lista de todas las aplicaciones que se han configurado, incluidas las aplicaciones que se han agregado desde la galería. Si selecciona una aplicación, se carga la hoja de recursos para esa aplicación, donde se pueden ver los informes de dicha aplicación y se pueden administrar diversas opciones de configuración.

La configuración de aprovisionamiento de cuentas de usuario puede administrarse mediante la selección de **Aprovisionamiento** a la izquierda.

![Hoja Recursos de aplicación][1]

## <a name="provisioning-modes"></a>Modos de aprovisionamiento
La hoja **Aprovisionamiento** comienza con un menú **Modo**, que muestra los modos de aprovisionamiento que se admiten para una aplicación empresarial y les permite la configuración. Las opciones disponibles incluyen:

* **Automático** : esta opción aparece si Azure AD admite el aprovisionamiento automático basado en API o el desaprovisionamiento de cuentas de usuario para esta aplicación. Si se selecciona este modo, se muestra una interfaz que guía a los administradores a través de la configuración de Azure AD para conectarse a la API de administración de usuario de la aplicación, mediante la creación de asignaciones de cuentas y flujos de trabajo que definen cómo deben fluir los datos de la cuenta de usuario entre Azure AD y la aplicación, y administrar el servicio de aprovisionamiento de Azure AD.
* **Manual** : esta opción se muestra si Azure AD no admite el aprovisionamiento automático de cuentas de usuario para esta aplicación. Esta opción significa que los registros de cuenta de usuario almacenados en la aplicación deben administrarse mediante un proceso externo, según las funcionalidades de aprovisionamiento y administración de usuarios proporcionadas por la aplicación (que puede incluir el aprovisionamiento Just-In-Time de SAML).

## <a name="configuring-automatic-user-account-provisioning"></a>Configuración del aprovisionamiento automático de cuentas de usuario
Si se seleccione la opción **Automático** , se muestra una pantalla dividida en cuatro secciones:

### <a name="admin-credentials"></a>Credenciales de administrador
Aquí es donde se indican las credenciales necesarias para que Azure AD se conecte a la API de administración de usuarios de la aplicación. La entrada necesaria varía dependiendo de la aplicación. Para más información sobre los requisitos y tipos de credenciales para aplicaciones específicas, consulte el [tutorial de configuración de la aplicación específica](active-directory-saas-app-provisioning.md#list-of-apps-that-support-automated-user-provisioning).

Si se selecciona el botón **Probar conexión** , puede probar las credenciales al hacer que Azure AD intente conectarse a la aplicación de aprovisionamiento de la aplicación con las credenciales proporcionadas.

### <a name="mappings"></a>Asignaciones
Aquí es donde los administradores pueden ver y modificar el flujo de atributos de usuario entre Azure AD y la aplicación de destino, cuando las cuentas de usuario se aprovisionan o se actualizan.

Hay un conjunto preconfigurado de asignaciones entre los objetos de usuario de Azure AD y los objetos de usuario de cada aplicación SaaS. Algunas aplicaciones administran otros tipos de objetos, como grupos o contactos. Si se selecciona una de estas asignaciones en la tabla, se muestra el editor de asignaciones a la derecha, donde se pueden ver y personalizar.

![Hoja Recursos de aplicación][2]

Entre las personalizaciones admitidas durante la primera versión preliminar se incluyen:

* Habilitar y deshabilitar asignaciones para objetos específicos, como el objeto de usuario de Azure AD en el objeto de usuario de la aplicación SaaS.
* Editar los atributos que fluyen desde el objeto de usuario de Azure AD al objeto de usuario de la aplicación. Para más información sobre la asignación de atributos, consulte la sección [Información sobre los tipos de asignaciones de atributos](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).
* Filtrar qué acciones de aprovisionamiento debe realizar Azure AD en la aplicación de destino, que es una característica nueva de Azure Portal. En lugar de que Azure AD sincronice totalmente los objetos, puede limitar las acciones realizadas. Por ejemplo, si solo se selecciona **Actualizar**, Azure AD únicamente actualiza las cuentas de usuario existentes en una aplicación y no crea otras nuevas. Si solo se selecciona **Crear**, Azure únicamente crea nuevas cuentas de usuario, pero no actualiza los existentes. Esta característica permite a los administradores crear asignaciones diferentes para la creación de cuentas y la actualización de los flujos de trabajo. Más adelante, en el período de versión preliminar, está prevista la capacidad completa para crear varias asignaciones por aplicación.

### <a name="settings"></a>Settings
Esta sección permite a los administradores iniciar y detener el servicio de aprovisionamiento de Azure AD para la aplicación seleccionada, así como borrar opcionalmente la caché de aprovisionamiento y reiniciar el servicio.

Si el aprovisionamiento se habilita por primera vez para una aplicación, active el servicio mediante el cambio de **Estado de aprovisionamiento** a **Activado**. Esto hace que el servicio de aprovisionamiento de Azure AD realice una sincronización inicial, que lee los usuarios asignados en la sección **Usuarios y grupos**, consulta la aplicación de destino para ellos y, después, realiza las acciones de aprovisionamiento definidas en la sección **Asignaciones** de Azure AD. Durante este proceso, el servicio de aprovisionamiento almacena datos en caché sobre las cuentas de usuario que está administrando, para que las cuentas no administradas dentro de las aplicaciones de destino que nunca estaban en el ámbito de asignación no se vean afectadas por las operaciones de desaprovisionamiento. Después de la sincronización inicial, el servicio de aprovisionamiento sincroniza automáticamente los objetos de grupo y usuario en un intervalo de diez minutos.

Si se cambia el valor de **Estado de aprovisionamiento** a **Desactivado**, simplemente se pausa el servicio de aprovisionamiento. En este estado, Azure no crea, actualizar ni quita ningún objeto de grupo o usuario en la aplicación. El cambio el estado a activado hace que el servicio retome donde se quedó.

Si activa la casilla **Borrar estado actual y reiniciar sincronización** y detiene el servicio de aprovisionamiento, se vuelcan los datos en caché sobre las cuentas que Azure AD está administrando, se reinician los servicios y se realiza la sincronización inicial de nuevo. Esta opción permite a los administradores iniciar el proceso de implementación de aprovisionamiento de nuevo.

### <a name="synchronization-details"></a>Detalles de la sincronización
En esta sección se ofrecen detalles adicionales sobre la operación del servicio de aprovisionamiento, incluidos las primeras y últimas veces que se ejecutó el servicio de aprovisionamiento en la aplicación y cuántos objetos de grupo y usuario se administran.

Se proporcionan vínculos al **informe de actividad de aprovisionamiento**, que proporciona un registro de todos los usuarios y grupos creados, actualizados y quitados entre Azure AD y la aplicación de destino, y al **informe de error de aprovisionamiento**, que proporciona mensajes de error más detallados para los objetos de grupo y usuario que no se pudieron leer, crear, actualizar o quitar. 

[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG



<!--HONumber=Dec16_HO4-->


