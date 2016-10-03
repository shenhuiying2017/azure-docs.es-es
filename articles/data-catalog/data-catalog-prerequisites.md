<properties
   pageTitle="Requisitos previos del Catálogo de datos de Azure | Microsoft Azure"
   description="Requisitos previos del Catálogo de datos de Azure: qué se necesita para empezar a usar el Catálogo de datos de Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# Requisitos previos de Catálogo de datos de Azure

## ¿Qué es necesario para comenzar a usar el Catálogo de datos de Azure?

Hay algunas cosas de las que debe encargarse para poder configurar **Catálogo de datos de Azure**. No se preocupe: no tardará mucho.

## Suscripción de Azure
Para configurar el Catálogo de datos de Azure, debe ser propietario o copropietario de una suscripción de Azure.

Las suscripciones de Azure le ayudan a organizar el acceso a los recursos de servicio en la nube como Catálogo de datos de Azure. También le ayudan a controlar cómo se informa, factura y paga el uso de recursos. Cada suscripción puede tener una configuración de facturación y pago diferente, por lo que puede tener varias suscripciones y planes diferentes por departamento, proyecto, oficina regional, etc. Cada servicio en la nube pertenece a una suscripción, y debe tener una suscripción para poder configurar el Catálogo de datos de Azure. Para más información, consulte [Administración de cuentas, suscripciones y roles administrativos](../active-directory/active-directory-assign-admin-roles.md).

## Azure Active Directory
Para configurar el Catálogo de datos de Azure, debe haber iniciado sesión con una cuenta de usuario de Azure Active Directory.

Azure Active Directory (Azure AD) proporciona una manera fácil para la empresa de administrar la identidad y acceso, tanto en la nube y como local. Los usuarios pueden usar una única cuenta profesional o educativa para efectuar el inicio de sesión único en cualquier aplicación web en la nube y local. Catálogo de datos de Azure usa Azure AD para autenticar el inicio de sesión. Para obtener más información, consulte [¿Qué es Azure Active Directory?](../active-directory/active-directory-whatis.md)

> [AZURE.NOTE] [Azure Portal](http://portal.azure.com/) permite a los usuarios iniciar sesión con una cuenta personal de Microsoft o una cuenta profesional o educativa de Azure Active Directory. Para configurar Azure Data Catalog mediante Azure Portal o mediante el [portal de Data Catalog](http://www.azuredatacatalog.com) debe haber iniciado sesión con una cuenta de Azure Active Directory, no con una cuenta personal.

## Configuración de directivas de Active Directory

En ocasiones, es posible que los usuarios se encuentren en una situación en la que puedan iniciar sesión en el portal del Catálogo de datos de Azure, pero cuando intenten iniciar sesión en la herramienta de registro de orígenes de datos reciban un mensaje de error que les impida iniciar sesión. El problema de este comportamiento puede ocurrir solamente cuando el usuario esté en la red de la empresa, o bien solo cuando el usuario se conecte desde fuera de la red de empresa.

La herramienta de registro de orígenes de datos usa la autenticación de formularios para validar los inicios de sesión de usuario en Active Directory. Para iniciar sesión correctamente, la autenticación de formularios debe ser habilitada en la directiva de autenticación global por un administrador de Active Directory.

La directiva de autenticación global permite habilitar los métodos de autenticación de forma independiente para las conexiones de extranet y de intranet, como se indica más adelante. Es posible que se produzcan errores de inicio de sesión si no está habilitada la autenticación de formularios para la red desde la que se conecta el usuario.

 ![Directiva de autenticación global de Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Para obtener más información, vea [Configuración de directivas de autenticación](https://technet.microsoft.com/library/dn486781.aspx).

<!---HONumber=AcomDC_0921_2016-->