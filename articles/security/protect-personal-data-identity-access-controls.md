---
title: "Protección de datos personales con controles de identidad y acceso de Azure | Microsoft Docs"
description: Uso de controles de identidad y acceso de Azure para ayudar a proteger sus datos personales
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 7c66a95d5a056f59e0f28dba4e0880e72e74dc3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-and-multi-factor-authentication-protect-personal-data-with-identity-and-access-controls"></a>Azure Active Directory y Multi-Factor Authentication: protección de datos personales con controles de identidad y acceso

En este artículo se proporcionan información y procedimientos que puede usar para proteger datos personales mediante servicios y características de seguridad de Azure Active Directory y Multi-Factor Authentication.

## <a name="scenario"></a>Escenario

Una gran empresa de cruceros, con sede en Estados Unidos, se encuentra en proceso de expansión de sus operaciones para ofrecer itinerarios en los mares Mediterráneo, Adriático y Báltico, así como en las Islas Británicas. Para apoyar esos esfuerzos, ha adquirido varias líneas de cruceros más pequeñas establecidas en Italia, Alemania, Dinamarca y Reino Unido. 

La empresa usa Microsoft Azure para almacenar datos corporativos en la nube. Esto incluye información de identificación personal como nombres, direcciones, números de teléfono e información de la tarjeta de crédito de su clientela global. También incluye información tradicional de recursos humanos como direcciones, números de teléfono, números de identificación fiscal y otra información sobre los empleados de la empresa de todas las ubicaciones. La línea de cruceros también conserva una gran base de datos de miembros del programa de recompensa y lealtad que incluye información personal para hacer un seguimiento de las relaciones con clientes actuales y antiguos.

Los empleados corporativos tienen acceso a la red de las oficinas remotas de la empresa, mientras que los agentes de viajes repartidos por todo el mundo tienen acceso a algunos recursos empresariales.

## <a name="problem-statement"></a>Declaración del problema

La empresa debe proteger la privacidad de los datos personales de los clientes y los empleados frente a atacantes que pretenden usar identidades comprometidas para obtener acceso. También debe garantizar que el acceso a datos personales por parte de usuarios legítimos quede restringido a solo aquellos que lo necesiten para realizar sus trabajos.

## <a name="company-goal"></a>Objetivo de la empresa

El objetivo de la empresa es garantizar que el acceso a los datos personales esté estrictamente controlado. Es fundamental que las identidades de los usuarios con acceso a los datos personales estén protegidas mediante una autenticación sólida. Una directiva de [privilegios mínimos] (https://en.wikipedia.org/wiki/Principle_of_least_privilege) debe aplicarse para que los usuarios legítimos tengan solo el nivel de acceso que necesitan, no más.

## <a name="solutions"></a>Soluciones

Microsoft Azure proporciona herramientas de administración de identidad y acceso para ayudar a las empresas a controlar quién tiene acceso a recursos que contienen datos personales.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (AAD) administra identidades y controla el acceso a Azure, así como otros recursos locales y otros recursos de nube, datos y aplicaciones. [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access) ayuda a los administradores de Azure a minimizar el número de personas con acceso a información determinada como, por ejemplo, datos personales. Les permite detectar, restringir y supervisar identidades con privilegios y su acceso a recursos. También les permite asignar derechos administrativos Just-In-Time (JIT) temporales a usuarios aptos. Asimismo, proporciona información sobre quién tiene privilegios administrativos de AAD.

Entre las actividades implicadas en el uso de AAD PIM se incluyen:

- Habilitación de Privileged Identity Management para el directorio

- Uso del panel del administrador de Privileged Identity Management para ver información importante de un vistazo

- Administración de las identidades con privilegios (administradores) agregando o quitando administradores permanentes o aptos en cada rol

- Configuración de las opciones de activación de rol

- Activación de roles

- Revisión de la actividad de un rol

#### <a name="how-do-i-enable-aad-pim"></a>¿Cómo se puede habilitar AAD PIM?

Para empezar a usar PIM para su directorio, realice lo siguiente:

1. Inicie sesión en Azure Portal como administrador global de su directorio.

2. Si su organización tiene más de un directorio, seleccione su nombre de usuario en la esquina superior derecha del Portal de Azure. Seleccione el directorio donde va a usar Privileged Identity Management de Azure AD.

3. Seleccione **Más servicios** y use el cuadro de texto **Filtro** para buscar Azure AD Privileged Identity Management.

4. Active **Anclar al panel** y haga clic en **Crear**. Se abre la aplicación Privileged Identity Management.

Después de configurar Azure AD Privileged Identity Management, verá la hoja de navegación cada vez que abra la aplicación.

![](media/protect-personal-data-identity-access-controls/azure-pim.png)

Para obtener más información e instrucciones sobre cómo empezar a trabajar con AAD PIM, consulte [Comenzar a usar Azure AD Privileged Identity Management.](https://docs.microsoft.com/active-directory/active-directory-privileged-identity-management-getting-started)

### <a name="azure-role-based-access-control"></a>Control de acceso basado en rol de Azure

[Control de acceso basado en rol de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (RBAC) ayuda a los administradores de Azure a administrar el acceso a recursos de Azure habilitando la concesión de acceso en función del rol asignado del usuario. Puede separar los deberes de un equipo y conceder a los usuarios, grupos y aplicaciones únicamente el acceso que necesiten para su trabajo.

El acceso basado en rol se puede conceder a los usuarios que utilicen el Portal de Azure, las herramientas de la línea de comandos de Azure o las API de administración de Azure.

Para obtener más información sobre los aspectos básicos de RBAC de Azure, consulte [Introducción al control de acceso basado en rol en Azure Portal.](https://docs.microsoft.com/active-directory/role-based-access-control-what-is)

#### <a name="how-do-i-manage-azure-rbac-with-powershell"></a>¿Cómo se puede administrar RBAC de Azure con PowerShell?

Puede usar cmdlets de PowerShell para administrar RBAC de Azure, incluidas las siguientes tareas de administración:

- Lista de roles

- Consulta de quién ha accedido

- Conceder acceso

- Quitar acceso

- Crear un rol personalizado

- Acciones Get para un proveedor de recursos

- Modificación de un rol personalizado

- Eliminación de un rol personalizado

- Lista de roles personalizados

Para obtener instrucciones sobre cómo administrar RBAC de Azure con PowerShell, consulte [Administración del acceso basado en rol con Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/) (MFA) es una solución de verificación en dos pasos que ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece una autenticación segura a través de una gran variedad de métodos de comprobación (como la comprobación mediante llamadas telefónicas, mensajes de texto o aplicaciones móviles).

Para implementar MFA en la nube de Azure, debe habilitarlo primero y, a continuación, activar la verificación en dos pasos para los usuarios.

#### <a name="how-do-i-enable-azure-to-use-mfa"></a>¿Cómo se puede habilitar Azure para usar MFA?

Si los usuarios tienen licencias que incluyen Azure Multi-Factor Authentication, no hay nada que se pueda hacer para activar Azure MFA. Si no las tienen, necesita crear un Proveedor de Multi-Factor Authentication en su directorio. Para ello, siga estos pasos.

1. Seleccione **Active Directory** en el Portal de Azure clásico (conectado como administrador).

2. Seleccione **Proveedores de Multi-Factor Authentication.**

3. Seleccione **Nuevo** y, a continuación, en **App Services,** seleccione **Proveedor de Multi-Factor Authentication.**

4. Seleccione **Creación rápida.**

5. Rellene el campo de nombre y seleccione un modelo de uso (por autenticación o por usuario habilitado).

6. Designe un directorio con el que se asocie el Proveedor de MFA.

7. Haga clic en el botón **Crear** .

![](media/protect-personal-data-identity-access-controls/quick-create.png)

Para obtener más instrucciones sobre cómo administrar el Proveedor de Multi-Factor Authentication, consulte [Introducción al Proveedor de Azure Multi-Factor Authentication.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-auth-provider)

#### <a name="how-do-i-turn-on-two-step-verification-for-users"></a>¿Cómo se puede activar la verificación en dos pasos para los usuarios?

Puede exigir la verificación en dos pasos para todos los inicios de sesión o crear directivas de acceso condicional para exigir la verificación en dos pasos únicamente cuando se apliquen condiciones específicas.

Habilitar Azure MFA mediante el cambio de estado del usuario es el enfoque tradicional para exigir la verificación en dos pasos. Todos los usuarios que habilite tendrán el mismo requisito para realizar la verificación en dos pasos cada vez que inicien sesión. La habilitación de un usuario invalida las directivas de acceso condicional que pudieran afectar a dicho usuario.

Habilitar Azure MFA con una directiva de acceso condicional es un enfoque más flexible para exigir la verificación en dos pasos. Puede crear directivas de acceso condicional que se aplican a grupos, así como a usuarios individuales. Los grupos de alto riesgo pueden recibir más restricciones que los grupos de bajo riesgo o bien la verificación en dos pasos puede ser necesaria solo para aplicaciones de alto riesgo en la nube y omitida para las de bajo riesgo. Sin embargo, el acceso condicional es una característica de pago de Azure Active Directory.

Para habilitar MFA cambiando el estado del usuario, realice lo siguiente:

1. Inicie sesión en Azure Portal como administrador.
2. Vaya a **Azure Active Directory \> Usuarios y grupos \> Todos los usuarios**.
3. Seleccione **Multi-Factor Authentication**.
4. Busque el usuario que desea habilitar para Azure MFA. Puede que necesite cambiar la vista en la parte superior.
5. Active la casilla situada junto al nombre del usuario.
6. En el lado derecho, bajo pasos rápidos, elija **Habilitar**.

   ![](media/protect-personal-data-identity-access-controls/quick-create.png)

7. Confirme la selección en la ventana emergente que se abre.  Se pedirá a los usuarios para los que se ha habilitado MFA que se registren la próxima vez que inicien sesión.

Para habilitar Azure MFA con una directiva de acceso condicional, realice lo siguiente:

1. Inicie sesión en Azure Portal como administrador.

2. Vaya a **Azure Active Directory \> Acceso condicional**.

3. Seleccione **Nueva directiva**.

4. En **Asignaciones**, seleccione **Usuarios y grupos**. Use las pestañas **Incluir** y     **Excluir** para especificar qué usuarios y grupos se administrarán con la directiva.

5. En **Asignaciones**, seleccione **Aplicaciones en la nube.** Elija **incluir Todas las aplicaciones en la nube**.
6.  En **Controles de acceso**, seleccione **Conceder**. Seleccione **Requerir autenticación multifactor**.
7.  Cambie **Habilitar directiva** a **Activar** y, a continuación, seleccione **Guardar**.

Para obtener información sobre cómo establecer la configuración de Azure MFA para instalar alertas de fraude, crear una omisión por única vez, usar mensajes de voz personalizados, configurar el almacenamiento en caché, especificar direcciones IP de confianza, crear contraseñas de aplicación, habilitar el recuerdo de MFA para dispositivos que cuentan con la confianza de los usuarios y seleccionar métodos de verificación, consulte [Configuración de Azure Multi-Factor Authentication.](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)

## <a name="next-steps"></a>Pasos siguientes

- [Protección del acceso con privilegios en Azure AD](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)

- [Preguntas más frecuentes relacionadas con Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-faq)

- [Solución de problemas del control de acceso basado en rol](https://docs.microsoft.com/azure/active-directory/role-based-access-control-troubleshooting)

- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
