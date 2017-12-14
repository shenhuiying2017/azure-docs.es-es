---
title: "Instalación de Azure AD Connect mediante una base de datos existente de ADSync | Microsoft Docs"
description: "En este tema se describe cómo utilizar una base de datos existente de ADSync."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.reviewer: cychua
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: billmath
ms.openlocfilehash: d65e12350e6302b0f95091f48f71cdc7d4610e2c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Instalación de Azure AD Connect mediante una base de datos existente de ADSync
Azure AD Connect requiere una base de datos de SQL Server para almacenar datos. Puede usar la instancia predeterminada de LocalDB incluida en SQL Server 2012 Express que se instala con Azure AD Connect o utilizar su propia versión completa de SQL. Anteriormente, al instalar Azure AD Connect, se creaba siempre una base de datos denominada ADSync. Con Azure AD Connect versión 1.1.613.0 (o posterior), tiene la opción de instalar Azure AD Connect haciendo que apunte a una base de datos existente de ADSync.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Ventajas de usar una base de datos de ADSync ya existente
Si apunta a una base de datos de ADSync ya existente:

- Excepto la información de las credenciales, la configuración de sincronización que se almacena en la base de datos de ADSync (incluidas las reglas de sincronización personalizadas, los conectores, el filtrado y la configuración de características opcionales) se recupera automáticamente y se usa durante la instalación. Las credenciales que usa Azure AD Connect para sincronizar los cambios con implementaciones locales de AD y Azure AD están cifradas y solo se puede acceder a ellas mediante el servidor de Azure AD Connect anterior.
- También se recuperan todos los datos de identidad (asociados con los espacios del conector y el metaverso) y las cookies de sincronización almacenadas en la base de datos de ADSync. El servidor recién instalado de Azure AD Connect puede seguir la sincronización desde donde lo dejó el anterior, en lugar de tener que realizar una sincronización completa.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Escenarios donde es útil usar una base de datos existente de ADSync
Estas ventajas son útiles en los escenarios siguientes:


- Tiene una implementación existente de Azure AD Connect. El servidor de Azure AD Connect existente ya no está funcionando pero el servidor de SQL Server que contiene la base de datos de ADSync todavía está en funcionamiento. Puede instalar un nuevo servidor de Azure AD Connect y hacer que apunte a la base de datos existente de ADSync. 
- Tiene una implementación existente de Azure AD Connect. El servidor de SQL Server que contiene la base de datos de ADSync ha dejado de funcionar. Sin embargo, tiene una copia de seguridad reciente de la base de datos. Puede restaurar la base de datos de ADSync a un nuevo servidor de SQL Server en primer lugar. Transcurrido ese período, puede instalar un nuevo servidor de Azure AD Connect y hacer que apunte a la base de datos restaurada de ADSync.
- Tiene una implementación existente de Azure AD Connect que utiliza LocalDB. Debido al límite de 10 GB impuesto por LocalDB, le gustaría migrar a una implementación completa de SQL. Puede realizar la copia de seguridad de la base de datos de ADSync desde LocalDB y restaurarla en un servidor SQL Server. Transcurrido ese período, puede volver a instalar un nuevo servidor de Azure AD Connect y hacer que apunte a la base de datos restaurada de ADSync.
- Está intentando configurar un servidor de ensayo y desea asegurarse de que su configuración coincide con la del servidor activo actual. Puede realizar la copia de seguridad de la base de datos de ADSync y restaurarla en otro servidor SQL Server. Transcurrido ese período, puede volver a instalar un nuevo servidor de Azure AD Connect y hacer que apunte a la base de datos restaurada de ADSync.

## <a name="prerequisite-information"></a>Información sobre requisitos previos

Notas importantes a tener en cuenta antes de continuar:

- Asegúrese de revisar los requisitos previos para la instalación de Azure AD Connect en la sección de hardware y requisitos previos, y la cuenta y los permisos necesarios para la instalación de Azure AD Connect. Los permisos necesarios para la instalación de Azure AD Connect mediante el modo "Usar base de datos existente" son los mismos que para la instalación "personalizada".
- La implementación de Azure AD Connect en una base de datos de ADSync existente solo es compatible con SQL completo. No se admite con la base de datos local de SQL Express LocalDB. Si tiene una base de datos de ADSync existente en LocalDB que desee usar, primero debe realizar una copia de seguridad de la base de datos de ADSync (LocalDB) y restaurarla en SQL completa. Después, puede implementar Azure AD Connect en la base de datos restaurada con este método.
- La versión de Azure AD Connect usada para la instalación debe cumplir los siguientes criterios:
    - 1.1.613.0 o superior Y
    - Una versión igual o superior a la versión de Azure AD Connect que se utilizó la última vez con la base de datos de ADSync. Si la versión de Azure AD Connect usada para la instalación es superior a la versión que se utilizó por última vez con la base de datos de ADSync, puede que sea necesario realizar una sincronización completa.  Es necesario realizar una sincronización completa si hay cambios en los esquemas o las reglas de sincronización entre las dos versiones. 
- La base de datos de ADSync utilizada debe contener un estado de sincronización que sea relativamente reciente. La última actividad de sincronización con la base de datos existente de ADSync se debe haber producido en las tres últimas semanas.
- Al instalar Azure AD Connect con el método "Usar base de datos existente", no se conserva el método de inicio de sesión configurado en el servidor de Azure AD Connect anterior. Es más, no puede configurar el método de inicio de sesión durante la instalación. Solo puede configurar el método de inicio de sesión una vez completada la instalación.
- No puede hacer que varios servidores de Azure AD Connect compartan la misma base de datos de ADSync. El método "Usar base de datos existente" le permite reutilizar una base de datos de ADSync existente con un nuevo servidor de Azure AD Connect. No admite el uso compartido.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Pasos para instalar Azure AD Connect con el modo "Usar base de datos existente"
1.  Descargue el instalador de Azure AD Connect (AzureADConnect.MSI) en el servidor de Windows. Haga doble clic en el instalador de Azure AD Connect para iniciar la instalación de este.
2.  Una vez finalizada la instalación del MSI, se inicia el asistente de Azure AD Connect con la configuración del modo rápido. Cierre la pantalla haciendo clic en el icono de salida.
![Pantalla de bienvenida](media/active-directory-aadconnect-existing-database/db1.png)
3.  Inicie un nuevo símbolo del sistema o sesión de PowerShell. Vaya a la carpeta <drive>\archivos de programa\Microsoft Azure AD Connect. Ejecute el comando .\AzureADConnect.exe /useexistingdatabase para iniciar el asistente de Azure AD Connect en el modo de configuración "Usar base de datos existente".
![PowerShell](media/active-directory-aadconnect-existing-database/db2.png)
4.  Aparece la pantalla de bienvenida a Azure AD Connect. Una vez que acepte los términos de licencia y el aviso de privacidad, haga clic en **Continuar**.
![Pantalla de bienvenida](media/active-directory-aadconnect-existing-database/db3.png)
5.  En la pantalla **Instalar componentes necesarios**, está habilitada la opción **Usar un SQL Server existente**. Especifique el nombre del servidor de SQL Server que hospeda la base de datos de ADSync. Si la instancia del motor SQL utilizada para hospedar la base de datos de ADSync no es la instancia predeterminada del servidor de SQL Server, deberá especificar el nombre de la instancia del motor SQL. Además, si no está habilitada la exploración de SQL, también deberá especificar el número de puerto de la instancia del motor SQL. Por ejemplo:         
![Pantalla de bienvenida](media/active-directory-aadconnect-existing-database/db4.png)           

6.  En la pantalla **Conectar con Azure AD**, debe proporcionar las credenciales de un administrador global de su directorio de Azure AD. Se recomienda utilizar una cuenta en el dominio predeterminado onmicrosoft.com. Esta cuenta solo se usa para crear una cuenta de servicio en Azure AD y no se utiliza una vez completado el asistente.
![Conexión](media/active-directory-aadconnect-existing-database/db5.png)
 
7.  En la pantalla **Conectar sus directorios**, el bosque de AD existente configurado para la sincronización de directorios se muestra con un icono de cruz roja junto a él. Para sincronizar los cambios desde un bosque de AD local, se necesita una cuenta de AD DS. El asistente de Azure AD Connect no puede recuperar las credenciales de la cuenta de AD DS almacenada en la base de datos de ADSync porque las credenciales están cifradas y solo las puede descifrar el anterior servidor de Azure AD Connect. Haga clic en **Cambiar credenciales** para especificar la cuenta de AD DS para el bosque de AD.
![Directorios](media/active-directory-aadconnect-existing-database/db6.png)
 
 
8.  En el cuadro de diálogo emergente, puede (i) proporcionar credenciales de administrador de la organización y dejar que Azure AD Connect cree la cuenta de AD DS en su lugar, o (ii) crear la cuenta de AD DS usted mismo y proporcionar sus credenciales a Azure AD Connect. Una vez que haya seleccionado una opción y proporcionado las credenciales necesarias, haga clic en **Aceptar** para cerrar el cuadro de diálogo emergente.
![Pantalla de bienvenida](media/active-directory-aadconnect-existing-database/db7.png)
 
 
9.  Una vez que se hayan proporcionado las credenciales, el icono de cruz roja se reemplazará por un icono de marca de verificación verde. Haga clic en **Siguiente**.
![Pantalla de bienvenida](media/active-directory-aadconnect-existing-database/db8.png)
 
 
10. En la pantalla **Listo para configurar**, haga clic en **Instalar**.
![Pantalla de bienvenida](media/active-directory-aadconnect-existing-database/db9.png)
 
 
11. Una vez completada la instalación, el servidor de Azure AD Connect se habilita automáticamente para el modo de ensayo. Se recomienda que revise la configuración del servidor y las exportaciones pendientes para detectar cambios inesperados antes de deshabilitar el modo de ensayo. 

## <a name="next-steps"></a>Pasos siguientes

- Ahora que ha instalado Azure AD Connect, puede [comprobar la instalación y asignar licencias](active-directory-aadconnect-whats-next.md).
- Para aprender más acerca de estas características que se habilitaron con la instalación, consulte la información sobre: [cómo evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) y [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).
- Obtenga información acerca de estos temas habituales: [el programador y cómo desencadenar la sincronización](active-directory-aadconnectsync-feature-scheduler.md).
- Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
