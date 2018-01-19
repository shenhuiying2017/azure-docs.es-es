---
title: "Configuración de LDAP seguro (LDAPS) en Azure AD Domain Services | Microsoft Docs"
description: "Configuración de LDAP seguro (LDAPS) para un dominio administrado con Servicios de dominio de Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: maheshu
ms.openlocfilehash: d55abe651f69e3539e7584b40a7aedf419bccda1
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2017
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configuración de LDAP seguro (LDAPS) para un dominio administrado con Azure AD Domain Services

## <a name="before-you-begin"></a>Antes de empezar
Asegúrese de que ha completado la [Tarea 2: Exportación del certificado LDAP seguro a un archivo .PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Tarea 3: Habilitación de LDAP seguro para el dominio administrado mediante Azure Portal
Realice los siguientes pasos de configuración para habilitar LDAP seguro:

1. Vaya a **[Azure Portal](https://portal.azure.com)**.

2. Busque 'servicios de dominio de' en el cuadro de búsqueda **Buscar recursos**. En el resultado de la búsqueda, seleccione **Azure AD Domain Services**. La página **Azure AD Domain Services** muestra el dominio administrado.

    ![Búsqueda del dominio administrado que se va aprovisionar](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Haga clic en el nombre del dominio administrado (por ejemplo, "contoso100.com") para ver más detalles sobre el dominio.

    ![Domain Services: estado de aprovisionamiento](./media/getting-started/domain-services-provisioning-state.png)

3. Haga clic en **LDAP seguro** en el panel de navegación.

    ![Servicios de dominio: página LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. De forma predeterminada, se deshabilita el acceso de LDAP seguro a su dominio administrado. Cambie **LDAP seguro** a **Habilitar**.

    ![Habilitación de LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. De forma predeterminada, el acceso de LDAP seguro al dominio administrado sobre Internet está deshabilitado. Cambie **Permitir el acceso mediante LDAP seguro a través de Internet** a **Habilitar**, si lo desea. 

    > [!WARNING]
    > Cuando se habilita el acceso LDAP seguro a través de Internet, el dominio es susceptible de recibir ataque por búsqueda de clave para la contraseña por Internet. Por lo tanto, se recomienda configurar un NSG para bloquear el acceso a los intervalos de direcciones IP de origen necesarios. Consulte las instrucciones para [bloquear el acceso LDAPS al dominio administrado a través de Internet](#task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Haga clic en el icono de la carpeta que sigue al archivo **.PFX con el certificado LDAP seguro**. Especifique la ruta de acceso al archivo PFX con el certificado para el acceso de LDAP seguro al dominio administrado.

7. Especifique la **contraseña para descifrar el archivo .PFX**. Proporcione la misma contraseña que usó al exportar el certificado al archivo PFX.

8. Cuando haya terminado, haga clic en el botón **Guardar**.

9. Verá una notificación que le informará de que el LDAP seguro se ha configurado para el dominio administrado. Hasta que se complete la operación, no podrá modificar otras configuraciones para el dominio.

    ![Configuración del LDAP seguro para el dominio administrado](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Se tardará entre unos 10 y 15 minutos en habilitar LDAP seguro para el dominio administrado. Si el certificado LDAP seguro proporcionado no coincide con los criterios necesarios, no se habilitará LDAP seguro para el directorio y aparecerá un mensaje de error. Por ejemplo, se indicará que el nombre de dominio es incorrecto, que el certificado ha expirado o que lo hará pronto. En este caso, vuelva a intentarlo con un certificado válido.
>
>

<br>

## <a name="task-4---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tarea 4: Configuración de DNS para acceder al dominio administrado desde Internet
> [!NOTE]
> **Tarea opcional** : omita esta tarea de configuración si no piensa acceder al dominio administrado con LDAPS a través de Internet.
>
>

Antes de comenzar esta tarea, asegúrese de haber completado los pasos que se describen en la [tarea 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

Una vez habilitado el acceso LDAP seguro a través de Internet para el dominio administrado, debe actualizar el DNS para que los equipos cliente puedan encontrar este dominio administrado. Al final de la tarea 3, se muestra una dirección IP externa en la pestaña **Propiedades** en **DIRECCIÓN IP EXTERNA PARA EL ACCESO LDAPS**.

Configure el proveedor de DNS externo para que el nombre DNS del dominio administrado (p. ej., ldaps.contoso100.com) señale a esta dirección IP externa. En este ejemplo, cree la entrada DNS siguiente:

    ldaps.contoso100.com  -> 52.165.38.113

Eso es todo: ya está listo para conectarse al dominio administrado mediante LDAP seguro a través de Internet.

> [!WARNING]
> Recuerde que los equipos cliente deben confiar en el emisor del certificado LDAPS para poder conectarse correctamente al dominio administrado mediante LDAPS. Si usa una entidad de certificación de confianza pública, no tendrá que realizar ninguna acción, ya que los equipos cliente confiarán en los emisores de certificados. Si utiliza un certificado autofirmado, instale la parte pública del certificado autofirmado en el almacén de certificados de confianza del equipo cliente.
>
>


## <a name="task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Tarea 5: Bloqueo del acceso LDAP seguro del dominio administrado a través de Internet
> [!NOTE]
> Si no ha habilitado el acceso LDAPS en el dominio administrado a través de Internet, omita esta tarea de configuración.
>
>

Antes de comenzar esta tarea, asegúrese de haber completado los pasos que se describen en la [tarea 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview).

La exposición del dominio administrado para el acceso LDAPS a través de Internet representa una amenaza de seguridad. Se puede alcanzar el dominio administrado desde Internet en el puerto usado para LDAP seguro (es decir, el puerto 636). Por lo tanto, puede elegir restringir el acceso al dominio administrado a direcciones IP conocidas específicas. Para mejorar la seguridad, cree un grupo de seguridad de red (NSG) y asócielo a la subred en la que habilitó Azure AD Domain Services.

En la siguiente tabla se muestra un NSG de ejemplo que puede configurar para bloquear el acceso LDAP seguro a través de Internet. El NSG contiene un conjunto de reglas que permiten el acceso LDAP seguro de entrada a través del puerto TCP 636 solo desde un conjunto especificado de direcciones IP. La regla predeterminada 'DenyAll' se aplica a todo el tráfico de entrada de Internet. La regla NSG para permitir el acceso LDAPS a través de Internet desde direcciones IP especificadas tiene una prioridad mayor que la regla NSG DenyAll.

![NSG de muestra para el acceso LDAPS seguro a través de Internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Más información** - [Grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md).

<br>


## <a name="troubleshooting"></a>solución de problemas
Si tiene problemas para conectarse al dominio administrado mediante LDAP seguro, siga los pasos siguientes para la solución de problemas:
* Asegúrese de que la cadena del emisor del certificado LDAP seguro es de confianza en el cliente. Puede optar por agregar la entidad de certificación raíz en el almacén de certificados raíz de confianza en el cliente para establecer la confianza.
* Compruebe que el certificado LDAP seguro no está emitido por una entidad de certificación intermedia que no es de confianza de forma predeterminada en un equipo de Windows nuevo.
* Compruebe que el cliente LDAP (por ejemplo, ldp.exe) se conecta al punto de conexión de LDAP seguro con un nombre DNS, no con la dirección IP.
* Compruebe que el nombre DNS al que se conecta el cliente LDAP se resuelve como la dirección IP pública para LDAP seguro en el dominio administrado.
* Compruebe que el certificado LDAP seguro para el dominio administrado tiene el nombre DNS en el atributo Sujeto o Nombres alternativos del sujeto.
* Si se conecta a través de LDAP seguro a través de Internet, asegúrese de que la configuración del grupo de seguridad de red para la red virtual permite que llegue tráfico al puerto 636 desde Internet.

Si sigue teniendo problemas para conectarse al dominio administrado mediante LDAP seguro, [póngase en contacto con el equipo del producto](active-directory-ds-contact-us.md) para obtener ayuda. Incluya la siguiente información para que podamos diagnosticar el problema mejor:
* Una captura de pantalla de ldp.exe cundo establece la conexión y cuando genera un error.
* El identificador de inquilino de Azure AD y el nombre de dominio DNS del dominio administrado.
* El nombre de usuario exacto utilizado para tratar de enlazar.


## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Administración de un dominio administrado con Servicios de dominio de Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Administración de directiva de grupo en un dominio administrado de Azure AD Domain Services](active-directory-ds-admin-guide-administer-group-policy.md)
* [Grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md)
* [Creación de un grupo de seguridad de red](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
