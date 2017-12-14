---
title: "Administración de aprovisionamiento de usuarios para aplicaciones empresariales en Azure Active Directory | Microsoft Docs"
description: Aprenda a administrar el aprovisionamiento de cuentas de usuario para aplicaciones empresariales con Azure Active Directory.
services: active-directory
documentationcenter: 
author: asmalser
manager: mtillman
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 122dbdb838377a36020f9ec692b38544004e676c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Administración del aprovisionamiento de cuentas de usuario para aplicaciones empresariales en el portal de Azure
En este artículo se describe cómo utilizar [Azure Portal](https://portal.azure.com) para administrar el aprovisionamiento automático de cuentas de usuario y el desaprovisionamiento para las aplicaciones que lo admiten, especialmente aquellas que se han agregado en la categoría "destacada" de la Galería de aplicaciones de [Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Para más información sobre el aprovisionamiento automático de cuentas de usuario, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Búsqueda de aplicaciones en el portal
Todas las aplicaciones que se han configurado para el inicio de sesión único en un directorio, por un administrador de directorio mediante la [Galería de aplicaciones de Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery), se pueden ver y administrar en el [portal de Azure](https://portal.azure.com). Las aplicaciones pueden encontrarse en la sección **Más servicios** &gt; **Aplicaciones empresariales** del portal. Las aplicaciones empresariales son aplicaciones que se implementan y se usan dentro de su organización.

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
Aquí es donde se indican las credenciales necesarias para que Azure AD se conecte a la API de administración de usuarios de la aplicación. La entrada necesaria varía dependiendo de la aplicación. Para más información sobre los requisitos y tipos de credenciales para aplicaciones específicas, consulte el [tutorial de configuración de la aplicación específica](active-directory-saas-app-provisioning.md).

Si se selecciona el botón **Probar conexión** , puede probar las credenciales al hacer que Azure AD intente conectarse a la aplicación de aprovisionamiento de la aplicación con las credenciales proporcionadas.

### <a name="mappings"></a>Asignaciones
Aquí es donde los administradores pueden ver y modificar el flujo de atributos de usuario entre Azure AD y la aplicación de destino, cuando las cuentas de usuario se aprovisionan o se actualizan.

Hay un conjunto preconfigurado de asignaciones entre los objetos de usuario de Azure AD y los objetos de usuario de cada aplicación SaaS. Algunas aplicaciones administran otros tipos de objetos, como grupos o contactos. Si se selecciona una de estas asignaciones en la tabla, se muestra el editor de asignaciones a la derecha, donde se pueden ver y personalizar.

![Hoja Recursos de aplicación][2]

Las personalizaciones compatibles incluyen:

* Habilitar y deshabilitar asignaciones para objetos específicos, como el objeto de usuario de Azure AD en el objeto de usuario de la aplicación SaaS.
* Editar los atributos que fluyen desde el objeto de usuario de Azure AD al objeto de usuario de la aplicación. Para más información sobre la asignación de atributos, consulte la sección [Información sobre los tipos de asignaciones de atributos](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).
* Filtre las acciones de aprovisionamiento que realiza Azure AD en la aplicación de destino. En lugar de que Azure AD sincronice totalmente los objetos, puede limitar las acciones realizadas. Por ejemplo, si solo se selecciona **Actualizar**, Azure AD únicamente actualiza las cuentas de usuario existentes en una aplicación y no crea otras nuevas. Si solo se selecciona **Crear**, Azure únicamente crea nuevas cuentas de usuario, pero no actualiza los existentes. Esta característica permite a los administradores crear asignaciones diferentes para la creación de cuentas y la actualización de los flujos de trabajo.

### <a name="settings"></a>Configuración
Esta sección permite a los administradores iniciar y detener el servicio de aprovisionamiento de Azure AD para la aplicación seleccionada, así como borrar opcionalmente la caché de aprovisionamiento y reiniciar el servicio.

Si el aprovisionamiento se habilita por primera vez para una aplicación, active el servicio mediante el cambio de **Estado de aprovisionamiento** a **Activado**. Esto hace que el servicio de aprovisionamiento de Azure AD realice una sincronización inicial, que lee los usuarios asignados en la sección **Usuarios y grupos**, consulta la aplicación de destino para ellos y, después, realiza las acciones de aprovisionamiento definidas en la sección **Asignaciones** de Azure AD. Durante este proceso, el servicio de aprovisionamiento almacena datos en caché sobre las cuentas de usuario que está administrando, para que las cuentas no administradas dentro de las aplicaciones de destino que nunca estaban en el ámbito de asignación no se vean afectadas por las operaciones de desaprovisionamiento. Después de la sincronización inicial, el servicio de aprovisionamiento sincroniza automáticamente los objetos de grupo y usuario en un intervalo de diez minutos.

Si se cambia el valor de **Estado de aprovisionamiento** a **Desactivado**, simplemente se pausa el servicio de aprovisionamiento. En este estado, Azure no crea, actualizar ni quita ningún objeto de grupo o usuario en la aplicación. El cambio el estado a activado hace que el servicio retome donde se quedó.

Si activa la casilla **Borrar estado actual y reiniciar sincronización** y detiene el servicio de aprovisionamiento, se vuelcan los datos en caché sobre las cuentas que Azure AD está administrando, se reinician los servicios y se realiza la sincronización inicial de nuevo. Esta opción permite a los administradores iniciar el proceso de implementación de aprovisionamiento de nuevo.

### <a name="synchronization-details"></a>Detalles de la sincronización
En esta sección se ofrecen detalles adicionales sobre la operación del servicio de aprovisionamiento, incluidos las primeras y últimas veces que se ejecutó el servicio de aprovisionamiento en la aplicación y cuántos objetos de grupo y usuario se administran.

Se proporcionan vínculos al **informe de actividad de aprovisionamiento**, que proporciona un registro de todos los usuarios y grupos creados, actualizados y quitados entre Azure AD y la aplicación de destino, y al **informe de error de aprovisionamiento**, que proporciona mensajes de error más detallados para los objetos de grupo y usuario que no se pudieron leer, crear, actualizar o quitar. 

##<a name="feedback"></a>Comentarios

Esperamos que le guste su experiencia de Azure AD. Envíenos sus comentarios. Publique sus comentarios y sugerencias para la mejora en la sección **Portal de administración** de nuestro [foro de comentarios](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Nos entusiasma crear cosas nuevas todos los días y usamos sus comentarios para dar forma y definir qué será lo próximo que haremos.


[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG
