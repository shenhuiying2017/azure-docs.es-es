---

title: "Introducción a la licencia de Azure Active Directory | Microsoft Docs"
description: "Descripción de la licencia de Azure Active Directory, cómo funciona, cómo comenzar y procedimientos recomendados, incluidos Office 365, Microsoft Intune y las ediciones Basic y Premium de Azure Active Directory"
services: active-directory
keywords: Licencias de Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 8e9d6900910f45b5f17600fc5608191871c5ab21
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---

# <a name="license-yourself-and-your-users-in-azure-active-directory"></a>Obtención de una licencia para usted y sus usuarios en Azure Active Directory

> [!div class="op_single_selector"]
> * [Portal de Azure](active-directory-licensing-get-started-azure-portal.md)
> * [Portal de Azure clásico](active-directory-licensing-what-is.md)
>
>

Azure Active Directory (Azure AD) es la plataforma y la solución de  identidad como un servicio de Microsoft. Azure AD se ofrece en distintas versiones técnicas o funcionales que van desde Azure AD Free, que se encuentra disponible con cualquier servicio de Microsoft como Office 365, Dynamics, Microsoft Intune y Azure (Azure AD no genera ningún gasto de consumo en este modo), hasta las versiones de pago de Azure AD como Enterprise Mobility Suite (EMS), Azure AD Premium (P1 y P2) y Azure AD Basic, y Azure Multi-Factor Authentication (MFA). Al igual que muchos de los servicios en línea de Microsoft, la mayoría de las versiones de pago de Azure AD se proporcionan a través de derechos por usuario, como ocurre en Office 365, Microsoft Intune y Azure AD. En estos casos, la compra de servicio se representa con una o varias suscripciones, y cada suscripción incluye un número de preventa de licencias en el inquilino. Los derechos de usuario se logran a través de la asignación de licencias, creando un vínculo entre el usuario y el producto, habilitando los componentes del servicio para el usuario y consumiendo una de las licencias de prepagadas.

[Probar Azure AD Premium ahora.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

Para más información sobre las funcionalidades de servicio de Azure AD, consulte [¿Qué es Azure AD?](https://azure.microsoft.com/en-us/documentation/articles/active-directory-whatis/). Para más información, consulte la página de Acuerdos de Nivel de Servicio.

> [!NOTE]
> Las suscripciones de Azure de pago por uso de Azure son distintas: a pesar de que también se representan en el directorio, estas suscripciones permiten la creación de recursos de Azure y asignarlos a la forma de pago. En este caso, NO hay licencias asociadas a la suscripción. La asociación de los usuarios con la suscripción, el acceso de los usuarios a los recursos de suscripción de administración, se consigue mediante la concesión de permisos para que funcione en recursos de Azure asignados a la suscripción.

## <a name="how-does-azure-ad-licensing-work"></a>¿Cómo funcionan las licencias de Azure AD?

Los servicios de Azure AD basados en licencia funcionan mediante la activación de una suscripción en el inquilino del servicio/directorio de Azure AD. Una vez activa la suscripción, los administradores de servicios/directorios pueden gestionar las funcionalidades del servicio y los usuarios con licencia pueden usarlas.

Cuando compra o activa Enterprise Mobility Suite, Azure AD Premium o Azure AD Basic, el directorio se actualiza con la suscripción, incluido el período de validez y las licencias de prepago. La información de suscripción, incluido el número de licencias asignadas o disponibles, se encuentra en Azure Portal, en el icono &gt; **Licencias** para el directorio específico de Azure Active Directory. Es también el mejor lugar para administrar de las asignaciones de licencia.

Cada suscripción consta de uno o varios planes de servicio, que representan el nivel funcional incluye del tipo de servicio; por ejemplo, Azure AD, Azure MFA, Microsoft Intune, Exchange Online o SharePoint Online.  La administración de licencias de Azure AD NO requiere administración a nivel del plan de servicio. Esto es diferente de Office 365, que se basa en este modo de configuración avanzada para administrar el acceso a los servicios incluidos. Azure AD se basa en la configuración del servicio para habilitar las características y administrar los permisos individuales.

> [!IMPORTANT]
> Azure AD Premium y Basic, así como las suscripciones de Enterprise Mobility Suite, se limitan a su inquilino/directorio aprovisionado. Las suscripciones no se pueden dividir entre directorios ni usarse para autorizar a los usuarios en otros directorios. Es posible mover una suscripción entre directorios, pero requiere el envío de una incidencia de soporte técnico o la cancelación y compra de nuevo en caso de compras directas.
>
> Al adquirir Azure AD o Enterprise Mobility Suite a través del programa de licencias por volumen, la activación de la suscripción se realizará automáticamente cuando el contrato incluya otros servicios de Microsoft Online, como por ejemplo, Office 365.

### <a name="assigning-licenses"></a>Asignación de licencias

Cuando obtiene una suscripción, todo lo que tiene que hacer es configurar las opciones de pago. El uso de características de pago de Azure AD requiere la distribución de licencias a las personas adecuadas. Por regla general, se debe asignar una licencia a los usuarios que deban disponer de acceso o que puedan administrarse a través de una característica de pago de Azure AD. Una asignación de licencia es una asignación entre un usuario y un servicio comprado, como Azure AD Premium, Basic o Enterprise Mobility Suite.

Administrar qué usuarios del directorio deben tener una licencia es sencillo. Puede realizarse mediante la asignación de licencias a grupos en Azure Portal (esta característica se encuentra en versión preliminar pública) o directamente a las personas adecuadas a través del portal, PowerShell o las API. Al asignar licencias a un grupo, se asignará una licencia a todos los miembros del grupo. Si se agregan usuarios o se quitan de un grupo, se asignarán a la licencia adecuada o se quitarán de ella. La asignación de grupo puede utilizar cualquier administración de grupo disponible para usted y que sea coherente con la asignación basada en grupo para las aplicaciones. Con ese enfoque, puede configurar reglas como que todos los usuarios del directorio se asignen automáticamente, garantizar que todas las personas con el puesto adecuado dispongan de licencia o incluso delegar la decisión a otros administradores de la organización. 

Para una explicación detallada de la asignación de licencias a grupos, incluidos los escenarios avanzados y los de licencia de Office 365, consulte [este artículo](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-group-assignment-azure-portal).

## <a name="getting-started-with-azure-ad-licensing"></a>Introducción a licencias de Azure AD

La introducción a Azure AD es sencilla; siempre puede crear un directorio como parte del registro en una prueba gratuita de Azure. [Obtenga más información acerca del registro como organización](https://azure.microsoft.com/en-us/documentation/articles/sign-up-organization/). La siguiente información puede ayudarle a asegurarse de que su directorio se alinea mejor con otros servicios de Microsoft que esté consumiendo o que piense consumir, y sus objetivos en cuanto a la obtención del servicio.

Estas son algunas prácticas recomendadas:

- Si ya está usando servicios organizativos de Microsoft, ya dispone de un directorio de Azure AD. En este caso, debe continuar usando el mismo directorio para otros servicios, por lo que puede utilizarse en los servicios la administración de identidades principal, incluido el aprovisionamiento y el inicio de sesión único (SSO) híbrido. Los usuarios dispondrán una experiencia de inicio de sesión único y se beneficiarán de funcionalidades más completas en los servicios. Como resultado, si decide comprar un servicio de pago de Azure AD para su personal, se recomienda que para ello utilice el mismo directorio.

- Si piensa usar Azure AD para un conjunto distinto de usuarios (asociados, clientes, etc.), si quiere evaluar los servicios de Azure AD y desea hacerlo independientemente del servicio de producción o si busca configurar un entorno de espacio aislado para los servicios, se recomienda crear primero un directorio a través del Portal de Azure clásico. Encontrará más información acerca de la creación de un directorio de Azure AD en el Portal de Azure clásico. El nuevo directorio se creará con su cuenta como un usuario externo con permisos de administrador globales. Cuando inicie sesión en Azure Portal con esta cuenta, podrá ver este directorio y acceder a todas las tareas de administración de directorios.

> [!NOTE]
> Azure AD admite "usuarios externos", que son cuentas de usuario en una instancia de Azure AD que se generaron con una identidad de Azure AD o cuenta Microsoft (MSA) desde otro directorio. Puesto que estamos ocupados ampliando esta capacidad a todos los servicios organizacionales de Microsoft, estas cuentas no son actualmente compatibles en algunas de las experiencias de servicios; por ejemplo, el Portal de administración de Office 365 no es compatible actualmente con estos usuarios. Por lo tanto, los usuarios externos con cuentas Microsoft no podrán acceder al Portal de administración de Office 365 y los usuarios externos de otros directorios de Azure AD se ignorarán. En el último caso, solo se puede acceder a través de estas experiencias a la cuenta local del usuario, el directorio de Azure AD u Office 365 en el que se creara originalmente el usuario.

Como se indica, Azure AD dispone de distintas versiones de pago. Estas versiones apenas se diferencian en cuanto a disponibilidad de compra:

|  Producto       |              EA/VL  | Abrir  | CSP |  Derechos de uso de MPN  | Compra directa |  Versión de prueba |
|  -------------- | ------------- | ------- | ------ | ----- | ---------------- | ----------------- | ------- |
|  Enterprise Mobility Suite  | X   |    X  |    X  |     X | &nbsp;  | X |
|  Azure AD Premium P2     |    X    |   X   |   X   | &nbsp;  |  X  |   X  
|  Azure AD Premium P1     |    X   |    X    |  X   |  &nbsp; |  X  |  &nbsp; |             
|  Azure AD Basic          |    X   |    X   |  X  |  X  | &nbsp; | &nbsp;  |

### <a name="select-one-or-more-license-trials"></a>Seleccione una o más versiones de prueba de licencia

Puede activar una suscripción de prueba de Azure AD Premium o Enterprise Mobility Suite en Azure Active Directory &gt; **Inicio rápido**.

![selección de licencia de prueba](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

Las licencias de prueba ahora estará disponibles en la hoja **Licencias**.

### <a name="assign-licenses-to-users-and-groups"></a>Asignación de licencias a usuarios y grupos

Una vez que la suscripción esté activa, debe asignarse una licencia a usted mismo y actualizar el explorador para asegurarse de que ve todas las características. El siguiente paso es asignar licencias a los usuarios que necesitarán tener acceso a características de pago de pago de Azure AD o que tendrán que incluirse en ellas. Como se mencionó anteriormente en [Asignación de licencias](#assigning-licenses), la mejor forma de hacerlo es identificar el grupo que representa a la audiencia deseada y asignarle la licencia; de esta forma, los usuarios que se agreguen o se quiten del grupo durante su vigencia se asignarán a la licencia o se eliminarán, respectivamente.

> [!NOTE]
> Algunos servicios de Microsoft no están disponibles en todas las ubicaciones; antes de asignar una licencia a un usuario, el administrador debe especificar la propiedad "Ubicación de uso" del usuario. Esto puede hacerse en la sección Usuario &gt; Perfil &gt; Configuración de Azure Portal. Cuando se utiliza la asignación de licencias de grupo, los usuarios sin ubicación de uso especificada heredarán la ubicación del directorio.

Para asignar una licencia a un grupo o a usuarios individuales, en Azure Active Directory &gt; Licencias &gt; Todos los productos, seleccione uno o varios productos y haga clic en el botón **Asignar** de la barra de comandos.

![selección de una licencia para asignarla](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

Se abrirá una nueva hoja donde podrá elegir varios usuarios o un grupo y, opcionalmente, deshabilitar planes de servicio del producto. La barra de búsqueda de la parte superior se puede utilizar para buscar nombres de usuario y grupo.

![selección de un usuario o grupo para la asignación de licencias](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

Al asignar una licencia al grupo, puede llevar un tiempo que todos los usuarios hereden la licencia, depende del número de usuarios del grupo. El estado de procesamiento se puede comprobar en la hoja del grupo, en el icono **Licencias**.

![estado de asignación de las licencias](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

Se pueden producir errores de asignación durante la asignación de licencias de Azure AD, pero son relativamente poco frecuentes al administrar productos de Azure AD y EMS. Los posibles errores de asignación se limitan a los siguientes:
- Conflicto de asignación: cuando se asignó previamente a un usuario una licencia incompatible con la actual. En este caso, para asignar la nueva licencia será necesario quitar la anterior.
- Excedido el número de licencias disponibles: cuando el número de usuarios en grupos asignados supera las licencias disponibles, el estado de asignación de los usuarios reflejará un error de asignación debido a que faltan licencias.

Encontrará información detallada acerca de la asignación de licencias de grupo en este artículo.

### <a name="view-assigned-licenses"></a>Visualización de licencias asignadas

En Azure Active Directory &gt; **Licencias** &gt; **Todos los productos** se muestra una vista de resumen de las licencias asignadas y disponibles.

![visualización del resumen de licencias](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

Al hacer clic en un producto específico aparece una lista detallada de los usuarios y grupos asignados. **Usuarios con licencia** muestra todos los usuarios actualmente con licencia, tanto si la licencia se asignó directamente al usuario como si se heredó de un grupo.

![visualización de los detalles de licencia](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

De forma similar, **Grupos con licencias** con licencia asignada. Haga clic en el usuario o grupo en estas vistas y se abrirá la hoja **Licencias** con todas las licencias asignadas al objeto.

### <a name="removing-a-license"></a>Eliminación de una licencia

Para quitar una licencia, vaya al usuario o grupo y abra el icono **Licencias**. Seleccione la licencia y haga clic en **Remove** (Quitar).

![eliminación de una licencia](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

Tenga en cuenta que las licencias heredadas de un grupo no se pueden quitar a un usuario directamente. En su lugar, quite el usuario del grupo desde el que haya heredado la licencia.

### <a name="extending-trials"></a>Ampliación de pruebas

Las extensiones de prueba para los clientes están disponibles como autoservicio a través del Portal de Office 365. Un administrador de clientes puede navegar al Portal de Office (el acceso depende de los permisos para el Portal de Office) y seleccionar la prueba de Azure AD Premium. Al hacer clic en el vínculo **Extend trial** (Ampliar prueba) se inicia el proceso de extensión. Se necesita una tarjeta de crédito, pero no se realizarán cargos.

![ampliación de la prueba en Azure Portal](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre escenarios avanzados de administración de licencias a través de grupos, consulte este artículo.

Ahora ya está preparado para configurar y usar algunas características de Azure AD Premium

* [Restablecimiento de la contraseña de autoservicio](active-directory-manage-passwords.md)
* [Administración de grupos de autoservicio](active-directory-accessmanagement-self-service-group-management.md)
* [Estado de Azure AD Connect](active-directory-aadconnect-health.md)
* [Asignación de grupo a aplicaciones](active-directory-manage-groups.md)
* [Asignación de licencias a un grupo](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [Compra directa de licencias de Azure AD Premium](http://aka.ms/buyaadp)

