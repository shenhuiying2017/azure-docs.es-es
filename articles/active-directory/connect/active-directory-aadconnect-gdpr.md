---
title: "Azure AD Connect y el Reglamento general de protección de datos | Microsoft Docs"
description: "En este documento se describe cómo obtener el cumplimiento con el GDPR en Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: c3956dd379961b119f65bdebe1f5a8038c4fa8f0
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect"></a>Cumplimiento del GDPR y Azure AD Connect 

En mayo de 2018, entrará en vigor una ley de privacidad europea, el [Reglamento general de protección de datos (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm). El RGPD impone nuevas reglas sobre las empresas, agencias gubernamentales, entidades sin ánimo de lucro y otras organizaciones que ofrecen bienes y servicios a personas de la Unión Europea (UE) o que recopilan y analizan datos vinculados a residentes en la UE. El GDPR se aplica con independencia de la ubicación. 

En la actualidad, hay productos y servicios de Microsoft para ayudarle a cumplir los requisitos de GDPR. Puede encontrar más información sobre la directiva de privacidad de Microsoft en [Trust Center](https://www.microsoft.com/trustcenter).

>[!NOTE] 
>Este artículo trata del cumplimiento de Azure AD Connect y GDPR.  Para más información sobre Azure AD Connect Health y el cumplimiento del GDPR, consulte el artículo [aquí](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md).

El cumplimiento del Reglamento general de protección de datos para instalaciones de Azure AD Connect se puede conseguir de dos maneras:

1.  Previa solicitud, extraer los datos de una persona y quitarlos de las instalaciones.
2.  Asegurarse de que ningún dato se conserva más de 48 horas.

El equipo de Azure AD Connect recomienda la segunda opción, ya que es mucho más fácil de implementar y mantener.

Un servidor de Azure AD Connect Sync almacena los datos siguientes que están en el ámbito del cumplimiento del GDPR:
1.  Datos sobre una persona en la **base de datos de Azure AD Connect**
2.  Datos de los archivos del **registro de eventos de Windows** que pueden contener información sobre una persona
3.  Datos de los **archivos de registro de instalación de Azure AD Connect** que pueden contener información sobre una persona.

Para cumplir el GDPR, los clientes de Azure AD Connect deben usar las siguientes directrices:
1.  Elimine el contenido de la carpeta que contiene los archivos de registro de instalación de Azure AD Connect con regularidad, al menos cada 48 horas.
2.  Este producto también puede crear registros de eventos.  Para más información sobre los registros de eventos, consulte la [documentación aquí](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Los datos sobre una persona se eliminan automáticamente de la base de datos de Azure AD Connect cuando se quitan del sistema de origen de donde proceden. No es necesaria ninguna acción específica por parte de los administradores para el cumplimiento del GDPR.  Sin embargo, es necesario que los datos de Azure AD Connect se sincronicen con el origen de datos al menos cada dos días.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Eliminación del contenido de la carpeta de archivos de registro de instalación de Azure AD Connect
Compruebe y elimine periódicamente el contenido de la carpeta **c:\programdata\aadconnect**, excepto el archivo **PersistedState.Xml**. Este archivo mantiene el estado de la instalación anterior de Azure AD Connect y se usa cuando se realiza una instalación de actualizaciones. Este archivo no contiene ningún dato sobre una persona y no debe eliminarse.

>[!IMPORTANT]
>No elimine el archivo PersistedState.xml.  Este archivo no contiene ninguna información del usuario y mantiene el estado de la instalación anterior.

Estos archivos se pueden revisar y eliminar mediante el Explorador de Windows, o bien puede usar un script como el siguiente para realizar las acciones necesarias:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Programación de este script para ejecutarse cada 48 horas
Use los pasos siguientes para programar que el script se ejecute cada 48 horas.

1.  Guarde el script en un archivo con la extensión **&#46;PS1** y luego abra el Panel de control y haga clic en **Sistemas y seguridad**.
    ![Sistema](media\active-directory-aadconnect-gdpr\gdpr2.png)

2.  En el encabezado Herramientas administrativas, haga clic en **Programar tareas**.
    ![Task](media\active-directory-aadconnect-gdpr\gdpr3.png)
3.  En el Programador de tareas, haga clic con el botón derecho en **Biblioteca del Programador de tareas** y haga clic en **Crear tarea básica...**
4.  Escriba el nombre de la nueva tarea y haga clic en **Siguiente**.
5.  Seleccione **Diariamente** para el desencadenador de tareas y haga clic en **Siguiente**.
6.  Establezca la repetición en **2 días** y haga clic en **Siguiente**.
7.  Seleccione **Iniciar un programa** como la acción y haga clic en **Siguiente**.
8.  En el cuadro Programa o script, escriba **PowerShell** y, en el cuadro **Agregar argumentos (opcional)**, escriba la ruta de acceso completa al script que creó anteriormente y, luego, haga clic en **Siguiente**.
9.  En la siguiente pantalla se muestra un resumen de la tarea que se dispone a crear. Compruebe los valores y haga clic en **Finalizar** para crear la tarea.



## <a name="next-steps"></a>pasos siguientes
- [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
- [Azure AD Connect Health y GDPR](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)
