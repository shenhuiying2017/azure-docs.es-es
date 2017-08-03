---
title: "Configuración de LDAP seguro (LDAPS) en Azure AD Domain Services | Microsoft Docs"
description: "Configuración de LDAP seguro (LDAPS) para un dominio administrado con Servicios de dominio de Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: 04e6c03dfd10c7dab50299f8ff0a5313274e4c60
ms.contentlocale: es-es
ms.lasthandoff: 06/14/2017


---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configuración de LDAP seguro (LDAPS) para un dominio administrado con Servicios de dominio de Azure AD
Este artículo muestra cómo puede habilitar el protocolo ligero de acceso a directorios seguro (LDAPS) para el dominio administrado con Servicios de dominio de Azure AD. LDAP seguro también se denomina "protocolo ligero de acceso a directorios (LDAP) en la Capa de sockets seguros (SSL)/Seguridad de la capa de transporte (TLS)".

## <a name="before-you-begin"></a>Antes de empezar
Para realizar las tareas enumeradas en este artículo, necesita lo siguiente:

1. Una **suscripción de Azure**válida.
2. Un **directorio de Azure AD** : sincronizado con un directorio local o solo en la nube.
3. **Servicios de dominio de Azure AD** deben estar habilitado en el directorio de Azure AD. Si no lo ha hecho, siga todas las tareas descritas en [Servicios de dominio de Azure AD (vista previa): introducción](active-directory-ds-getting-started.md).
4. Un **certificado que se usará para habilitar LDAP seguro**.

   * **Recomendado**: obtenga un certificado de una entidad de certificación pública de confianza. Esta opción de configuración es más segura.
   * Si lo prefiere, puede elegir [crear un certificado autofirmado](#task-1---obtain-a-certificate-for-secure-ldap) , tal y como se muestra más adelante en este artículo.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Requisitos para el certificado LDAP seguro
Antes de habilitar LDAP seguro, adquiera un certificado válido de acuerdo con las instrucciones siguientes. Encontrará errores si intenta habilitar LDAP seguro para el dominio administrado con un certificado no válido o incorrecto.

1. **Emisor de confianza** : el certificado debe ser emitido por una autoridad de confianza para los equipos que necesitan conectarse al dominio mediante LDAP seguro. Esta entidad puede ser una entidad de certificación pública de confianza para estos equipos.
2. **Duración** : el certificado debe ser válido al menos para los próximos 3-6 meses. El acceso LDAP seguro a su dominio administrado se interrumpe cuando expira el certificado.
3. **Nombre del firmante** : el nombre del firmante del certificado debe ser un comodín para el dominio administrado. Por ejemplo, si el dominio se denomina "contoso100.com", el nombre del firmante del certificado debe ser "*.contoso100.com". Establezca el nombre DNS (nombre alternativo del firmante) en este nombre con caracteres comodín.
4. **Uso de la clave** : el certificado debe configurarse para los usos siguientes: firmas digitales y cifrado de claves.
5. **Propósito del certificado** : el certificado debe ser válido para la autenticación del servidor SSL.

> [!NOTE]
> **Entidades de certificación empresariales:** Azure AD Domain Services no admite actualmente el uso de certificados de LDAP seguros emitidos por la entidad de certificación empresarial de su organización. Esta restricción se debe a que el servicio no confía en la entidad de certificación empresarial como una entidad de certificación raíz. Tenemos pensado agregar compatibilidad con las entidades de certificación empresariales en el futuro. Si debe usar obligatoriamente certificados emitidos por su entidad de certificación empresarial [póngase en contacto con nosotros](active-directory-ds-contact-us.md) para obtener ayuda.
>
>

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Tarea 1: Obtener un certificado para LDAP seguro
La primera tarea implica la obtención de un certificado que se utilizará para el acceso LDAP seguro al dominio administrado. Tiene dos opciones:

* Obtención de un certificado de una entidad de certificación. La entidad puede ser una entidad de certificación pública.
* Creación de un certificado autofirmado.

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Opción A (recomendada): obtención de un certificado LDAP seguro desde una entidad de certificación
Si su organización obtiene los certificados de una entidad de certificación pública, debe obtener de esta el certificado LDAP seguro.

Al solicitar un certificado, asegúrese de seguir los requisitos descritos en [Requisitos para el certificado LDAP seguro](#requirements-for-the-secure-ldap-certificate).

> [!NOTE]
> Los equipos cliente que necesitan conectarse al dominio administrado mediante LDAP seguro deben confiar en el emisor del certificado de LDAP seguro.
>
>

### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Opción B: creación de un certificado autofirmado para LDAP seguro
Si prevé que no usará un certificado de una entidad de certificación pública, puede crear un certificado autofirmado para LDAP seguro.

**Creación de un certificado autofirmado con PowerShell**

En el equipo de Windows, abra una nueva ventana de PowerShell como **Administrador** y escriba los comandos siguientes para crear un nuevo certificado autofirmado.

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

En el ejemplo anterior, reemplace '*.contoso100.com' con el nombre de dominio DNS de su dominio administrado de Azure AD Domain Services (por ejemplo, si ha creado el nombre de dominio DNS para Azure AD Domain Services 'contoso100.onmicrosoft.com', deseará reemplazar '*.contoso100.com' en el script anterior con '*.contoso100.onmicrosoft.com').

![Selección de un directorio de Azure AD](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

El certificado autofirmado recién creado se colocará en el almacén de certificados del equipo local.

## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Tarea 2: Exportar el certificado LDAP seguro a un archivo .PFX
Antes de iniciar esta tarea, asegúrese de haber obtenido el certificado de LDAP seguro de una entidad de certificación pública, o bien haber creado un certificado autofirmado.

Realice los pasos siguientes para exportar el certificado LDAPS a un archivo PFX.

1. Pulse el botón **Inicio** y escriba **E**. En el cuadro de diálogo **Ejecutar**, escriba **mmc** y haga clic en **Aceptar**.

    ![Inicio de la consola MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. En el aviso **Control de cuentas de usuario**, haga clic en **Sí** para iniciar MMC (Microsoft Management Console) como administrador.
3. En el menú **Archivo**, haga clic en **Agregar o quitar complemento**.

    ![Adición del complemento a la consola MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. En el cuadro de diálogo **Agregar o quitar complemento**, seleccione el complemento **Certificados** y haga clic en el botón **Agregar >**.

    ![Adición del complemento Certificados a la consola MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. En el Asistente para el **complemento Certificados**, seleccione **Cuenta de equipo** y haga clic en **Siguiente**.

    ![Adición del complemento Certificados a la cuenta de equipo](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. En la página **Seleccionar equipo**, seleccione **Equipo local**: (el equipo en el que se está ejecutando esta consola) y haga clic en **Finalizar**.

    ![Adición del complemento Certificados: seleccionar equipo](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. En el cuadro de diálogo **Agregar o quitar complementos**, haga clic en **Aceptar** para agregar el complemento de certificados a MMC.

    ![Adición del complemento Certificados a la consola MMC: listo](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. En la ventana de MMC, haga clic para expandir **Raíz de consola**. Debería ver el complemento Certificados cargado. Haga clic en **Certificados (equipo local)** para expandirlo. Haga clic para expandir el nodo **Personal** y después el nodo **Certificados**.

    ![Abrir el almacén de certificados personal](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. Debería ver el certificado autofirmado que se creó. Puede examinar las propiedades del certificado para asegurarse de que la huella digital coincide con lo que notificó en las ventanas de PowerShell cuando creó el certificado.
10. Seleccione el certificado autofirmado y **haga clic con el botón derecho**. En el menú contextual, seleccione **Todas las tareas** y **Exportar**.

    ![Exportación de certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. En **Asistente para exportar certificados**, haga clic en **Siguiente**.

    ![Asistente para exportación de certificados](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. En la página **Exportar la clave privada**, seleccione **Exportar la clave privada** y haga clic en **Siguiente**.

    ![Exportación de certificados: clave privada](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > DEBE exportar la clave privada junto con el certificado. Se producirá un error al habilitar LDAP seguro para el dominio administrado si proporciona un archivo PFX que no contiene la clave privada del certificado.
    >
    >
13. En la página **Formato de archivo de exportación**, seleccione **Intercambio de información personal: PKCS #12 (.PFX)** como formato de archivo para el certificado exportado.

    ![Exportación de certificados: formato de archivos](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Solo se admite el formato de archivo .PFX. No exporte el certificado al formato de archivo .CER.
    >
    >
14. En la página **Seguridad**, seleccione la opción **Contraseña** y escriba una contraseña para proteger el archivo PFX. Recuerde esta contraseña, ya que se necesitará en la siguiente tarea. Haga clic en **Siguiente** para continuar.

    ![Contraseña para la exportación de certificados ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Anote esta contraseña. La necesitará al habilitar LDAP seguro para este dominio administrado en [Tarea 3: Habilitar LDAP seguro para el dominio administrado](#task-3---enable-secure-ldap-for-the-managed-domain)
    >
    >
15. En la página **Archivo que se va a exportar** , especifique el nombre de archivo y la ubicación donde desea exportar el certificado.

    ![Ruta para la exportación de certificados](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. En la página siguiente, haga clic en **Finalizar** para exportar el certificado a un archivo PFX. Debería ver el cuadro de diálogo de confirmación cuando se haya exportado el certificado.

    ![Exportación de certificados: listo](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)

## <a name="task-3---enable-secure-ldap-for-the-managed-domain"></a>Tarea 3: Habilitar LDAP seguro para el dominio administrado
Realice los siguientes pasos de configuración para habilitar LDAP seguro:

1. Vaya al **[Portal de Azure clásico](https://manage.windowsazure.com)**.
2. En el panel izquierdo, seleccione **Active Directory** .
3. Seleccione el directorio de Azure AD (también denominado "inquilino"), para el que se ha habilitado Servicios de dominio de Azure AD.

    ![Selección de un directorio de Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. Haga clic en la pestaña **Configure** .

    ![Pestaña Configurar del directorio](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. Desplácese hacia abajo hasta la sección **Servicios de dominio**. Verá una opción denominada **LDAP seguro (LDAPS)** como se muestra en la captura de pantalla siguiente:

    ![Sección de configuración de los Servicios de dominio](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)
6. Haga clic en el botón **Configurar certificado** para abrir el cuadro de diálogo **Configurar certificado para LDAP seguro**.

    ![Configurar certificado para LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)
7. Haga clic en el icono de carpeta que encontrará bajo **ARCHIVO PFX CON CERTIFICADO** para especificar el archivo PFX, que contiene el certificado que desea usar para el acceso LDAP seguro al dominio administrado. Escriba también la contraseña especificada al exportar el certificado al archivo PFX. A continuación, haga clic en el botón Listo de la parte inferior.

    ![Especificar la contraseña y archivos PFX para LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)
8. La sección **Servicios de dominio** de la pestaña **Configurar** debería aparecer en gris y con el estado **Pendiente...** unos minutos. Durante este período, se verifica la precisión del certificado LDAPS y se configura LDAP seguro para el dominio administrado.

    ![LDAP seguro: estado pendiente](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

   > [!NOTE]
   > Se tardará entre unos 10 y 15 minutos en habilitar LDAP seguro para el dominio administrado. Si el certificado LDAP seguro proporcionado no coincide con los criterios necesarios, no se habilitará LDAP seguro para el directorio y aparecerá un mensaje de error. Por ejemplo, se indicará que el nombre de dominio es incorrecto, que el certificado ha expirado o que lo hará pronto.
   >
   >

9. Cuando LDAP seguro se haya habilitado correctamente para su dominio administrado, el mensaje **Pendiente** debería desaparecer. Debería ver la huella digital del certificado.

    ![LDAP seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>

## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>Tarea 4: Habilitar el acceso LDAP seguro a través de Internet
**Tarea opcional** : omita esta tarea de configuración si no piensa acceder al dominio administrado con LDAPS a través de Internet.

Antes de comenzar esta tarea, asegúrese de haber completado los pasos que se describen en la [tarea 3](#task-3---enable-secure-ldap-for-the-managed-domain).

1. Debería ver la opción **HABILITAR EL ACCESO LDAP SEGURO A TRAVÉS DE INTERNET** en la sección **Servicios de dominio** de la página **Configurar**. Dicha opción tendrá el valor **NO** de forma predeterminada, ya que el acceso por Internet al dominio administrado a través de LDAP seguro está deshabilitado de forma predeterminada.

    ![LDAP seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)
2. Cambie **HABILITAR EL ACCESO LDAP SEGURO A TRAVÉS DE INTERNET** a **SÍ**. Haga clic en el botón **GUARDAR** del panel inferior.
    ![LDAP seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)
3. La sección **Servicios de dominio** de la pestaña **Configurar** debería aparecer en gris y con el estado **Pendiente...** unos minutos. Pasado unos instantes, se habilitará el acceso por Internet al dominio administrado mediante LDAP seguro.

    ![LDAP seguro: estado pendiente](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

   > [!NOTE]
   > Habilitar el acceso por Internet mediante LDAP seguro para el dominio administrado llevará unos 10 minutos.
   >
   >
4. Si el acceso mediante LDAP seguro a su dominio administrado a través de Internet se habilita correctamente, el mensaje **Pendiente** debería desaparecer. Debería ver la dirección IP externa que puede utilizarse para acceder a su directorio mediante LDAPS en el campo **DIRECCIÓN IP EXTERNA PARA EL ACCESO LDAPS**.

    ![LDAP seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tarea 5: Configurar DNS para acceder al dominio administrado desde Internet
**Tarea opcional** : omita esta tarea de configuración si no piensa acceder al dominio administrado con LDAPS a través de Internet.

Antes de comenzar esta tarea, asegúrese de haber completado los pasos que se describen en la [tarea 4](#task-4---enable-secure-ldap-access-over-the-internet).

Una vez habilitado el acceso LDAP seguro a través de Internet para el dominio administrado, debe actualizar el DNS para que los equipos cliente puedan encontrar este dominio administrado. Al final de la tarea 4, aparece una dirección IP externa en la pestaña **Configurar**, en **DIRECCIÓN IP EXTERNA PARA EL ACCESO LDAPS**.

Configure el proveedor de DNS externo para que el nombre DNS del dominio administrado (p. ej., ldaps.contoso100.com) señale a esta dirección IP externa. En este ejemplo, es necesario crear la entrada DNS siguiente:

    ldaps.contoso100.com  -> 52.165.38.113

Eso es todo: ya está listo para conectarse al dominio administrado mediante LDAP seguro a través de Internet.

> [!WARNING]
> Recuerde que los equipos cliente deben confiar en el emisor del certificado LDAPS para poder conectarse correctamente al dominio administrado mediante LDAPS. Si usa una entidad de certificación empresarial o una entidad de certificación de confianza pública, no tendrá que realizar ninguna acción, ya que los equipos cliente confiarán en los emisores de certificados. Si utiliza un certificado autofirmado, tendrá que instalar la parte pública del certificado autofirmado en el almacén de certificados de confianza del equipo cliente.
>
>

<br>

## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Administer an Azure AD Domain Services managed domain (Administración de un dominio administrado con Servicios de dominio de Azure AD)](active-directory-ds-admin-guide-administer-domain.md)
* [Administración de directiva de grupo en un dominio administrado de Azure AD Domain Services](active-directory-ds-admin-guide-administer-group-policy.md)

