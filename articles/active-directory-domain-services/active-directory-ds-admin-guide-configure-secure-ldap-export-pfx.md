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
ms.date: 06/30/2017
ms.author: maheshu
ms.openlocfilehash: 4d495fcef89a68ca544100dcea8e6c55d4709ae0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configuración de LDAP seguro (LDAPS) para un dominio administrado con Azure AD Domain Services

## <a name="before-you-begin"></a>Antes de empezar
Asegúrese de haber completado la [Tarea 1: Obtención de un certificado para LDAP seguro](active-directory-ds-admin-guide-configure-secure-ldap.md).


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Tarea 2: Exportación del certificado LDAP seguro a un archivo .PFX
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
    > Anote esta contraseña. La necesitará al habilitar LDAP seguro para este dominio administrado en la [Tarea 3: Habilitación de LDAP seguro para el dominio administrado](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
    >
    >
15. En la página **Archivo que se va a exportar** , especifique el nombre de archivo y la ubicación donde desea exportar el certificado.

    ![Ruta para la exportación de certificados](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. En la página siguiente, haga clic en **Finalizar** para exportar el certificado a un archivo PFX. Debería ver el cuadro de diálogo de confirmación cuando se haya exportado el certificado.

    ![Exportación de certificados: listo](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="next-step"></a>Paso siguiente
[Tarea 3: Habilitación de LDAP seguro para el dominio administrado](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
