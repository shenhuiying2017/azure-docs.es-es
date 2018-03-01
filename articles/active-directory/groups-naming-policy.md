---
title: "Configuración de la directiva de nombre de grupo para grupos de Office 365 en Azure Active Directory (versión preliminar) | Microsoft Docs"
description: "Configuración de la expiración de grupos de Office 365 en Azure Active Directory (versión preliminar)"
services: active-directory
documentationcenter: 
author: curtand
manager: michael.tillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 02/20/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: dce848d75e2ce89e8e6003108f1cd5371cbb0f31
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="enforce-a-naming-policy-for-office-365-groups-in-azure-active-directory-preview"></a>Aplicación de una directiva de nomenclatura para grupos de Office 365 en Azure Active Directory (versión preliminar)

Para aplicar convenciones de nomenclatura coherentes a los grupos de Office 365 creados o editados por los usuarios, configure una directiva de nomenclatura de grupos para sus inquilinos en Azure Active Directory (Azure AD). Por ejemplo, podría usar la directiva de nomenclatura para comunicar la función de un grupo, la pertenencia, la región geográfica o quién creó el grupo. También puede usarla para ayudar a clasificar los grupos en la libreta de direcciones. O, para impedir que palabras específicas se usen en los nombres y alias de grupo.

> [!IMPORTANT]
> Para usar la versión preliminar de la directiva de nomenclatura de grupos de Office 365, es necesario que cada usuario único que sea miembro de uno o varios grupos de Office 365 tenga una licencia Premium P1 de Azure Active Directory.

La directiva de nomenclatura se aplica a la creación o edición de grupos creados en cargas de trabajo (por ejemplo, Outlook, Microsoft Teams, SharePoint, Exchange o Planner). Se aplica tanto al nombre como al alias del grupo. Si configuró su directiva de nomenclatura en Azure AD y tiene una directiva de nomenclatura de grupos de Exchange existente, se aplica la directiva de nomenclatura de Azure AD.

## <a name="naming-policy-features"></a>Características de la directiva de nomenclatura
Puede aplicar la directiva de nomenclatura a grupos de Office 365 de dos maneras diferentes:

-   **Directiva de nomenclatura de prefijo-sufijo**: puede definir prefijos o sufijos que se agregan automáticamente para aplicar una convención de nomenclatura a los grupos (por ejemplo, en el nombre de grupo "GRP\_JAPAN\_My Group\_Engineering", GRP\_JAPAN\_ es el prefijo y \_Engineering es el sufijo). 

-   **Palabras bloqueadas personalizadas**: puede cargar un conjunto de palabras bloqueadas específicas de su organización para que se bloqueen en los grupos creados por los usuarios (por ejemplo, "CEO, Nómina, RRHH").

### <a name="prefix-suffix-naming-policy"></a>Directiva de nomenclatura de prefijo-sufijo

La estructura general de la convención de nomenclatura es "Prefijo[NombreDeGrupo]Sufijo". Aunque puede definir varios prefijos y sufijos, solo puede tener una instancia del [NombreDeGrupo] en la configuración. Los prefijos o sufijos pueden ser cadenas fijas o atributos de usuario como \[Department\], que se sustituyen en función del usuario que crea el grupo. El número total de caracteres permitidos para las cadenas de prefijo y sufijo combinadas es de 53. 

Los prefijos y sufijos pueden contener caracteres especiales que se admiten en el nombre y el alias del grupo. Los caracteres de prefijo o sufijo que no se admiten en el alias de grupo se siguen aplicando al nombre de grupo, pero se quitan del alias de grupo. Debido a esta restricción, los prefijos y sufijos aplicados al nombre de grupo pueden ser diferentes de los que se aplican al alias de grupo. 

#### <a name="fixed-strings"></a>Cadenas fijas

Puede usar cadenas para que sea más fácil examinar y diferenciar grupos en la lista global de direcciones y en los vínculos de navegación izquierdos de las cargas de trabajo de grupo. Algunos de los prefijos comunes son palabras clave como "Grp\_Name", "\#Name", "\_Name".

#### <a name="user-attributes"></a>Atributos de usuario

Puede usar atributos como ayuda para identificar para qué departamento, oficina o región geográfica se creó el grupo. Por ejemplo, si define la directiva de nomenclatura como `PrefixSuffixNamingRequirement = “GRP [GroupName] [Department]”` y `User’s department = Engineering`, un nombre de grupo aplicado podría ser "GRP My Group Engineering". Atributos de Azure AD admitidos son \[Department\], \[Company\], \[Office\], \[StateOrProvince\], \[CountryOrRegion\], \[Title\]. Los atributos de usuario no admitido se tratan como cadenas fijas; por ejemplo, "\[postalCode\]". No se admiten atributos de extensión ni atributos personalizados.

Se recomienda usar atributos con valores ya rellenados para todos los usuarios de la organización y no usar atributos con valores largos.

### <a name="custom-blocked-words"></a>Palabras bloqueadas personalizadas

Una lista de palabras bloqueadas es una lista separada por comas de frases que se bloquearán en los nombres y alias de grupo. No se realiza ninguna búsqueda de subcadenas. Para desencadenar un error se requiere una coincidencia exacta entre el nombre de grupo y una o varias de las palabras bloqueadas personalizadas. Como no se realiza la búsqueda de subcadenas, los usuarios pueden usar palabras comunes como "Class" aunque "lass" sea una palabra bloqueada.

Reglas de la lista de palabras bloqueadas:
- Las palabras bloqueadas no distinguen mayúsculas de minúsculas.
- Cuando un usuario escribe una palabra bloqueada que forma parte de un nombre de grupo, verá un mensaje de error con la palabra bloqueada.
- No hay ninguna restricción de caracteres en las palabras bloqueadas.
- Hay un límite de 5000 frases que se pueden configurar en la lista de palabras bloqueadas. 

### <a name="administrator-override"></a>Invalidación del administrador

Algunos administradores pueden quedar excluidos de estas directivas, en todas las cargas de trabajo de grupo y puntos de conexión, de forma que pueden crear grupos mediante palabras bloqueadas y con sus propias convenciones de nomenclatura. A continuación se muestra la lista de roles de administrador excluidos de la directiva de nomenclatura de grupos.

- Administrador global
- Soporte técnico de asociado de nivel 1
- Soporte técnico de asociado de nivel 2
- Administrador de cuenta de usuario
- Escritores de directorios

## <a name="install-powershell-cmdlets-to-configure-a-naming-policy"></a>Instalación de cmdlets de PowerShell para configurar una directiva de nomenclatura

Asegúrese de desinstalar cualquier versión anterior del módulo Azure Active Directory PowerShell for Graph para Windows PowerShell y de instalar [Azure Active Directory PowerShell for Graph - Public Preview Release 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) antes de ejecutar los comandos de PowerShell. 

1. Abra la aplicación Windows PowerShell como administrador.
2. Desinstale cualquier versión anterior de AzureADPreview.
  
  ````
  Uninstall-Module AzureADPreview
  ````
3. Instale la versión más reciente de AzureADPreview.
  
  ````
  Install-Module AzureADPreview
  ````
Si se le pregunta sobre si desea acceder a un repositorio en el que no se confía, escriba **S**. El nuevo módulo puede tardar varios minutos en instalarse.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-ad-powershell"></a>Configuración de la directiva de nomenclatura de grupos para un inquilino mediante Azure AD PowerShell

1. Abra una ventana de Windows PowerShell en el equipo. La puede abrir sin privilegios elevados.

2. Ejecute los comandos siguientes para prepararse para ejecutar los cmdlets.
  
  ````
  Import-Module AzureADPreview
  Connect-AzureAD
  ````
En la pantalla **Sign in to your Account** (Iniciar sesión en su cuenta) que se abre, escriba la cuenta y la contraseña de administrador para conectarse al servicio y seleccione **Sign in** (Iniciar sesión).

### <a name="view-the-current-settings"></a>Visualización de la configuración actual

1. Obtenga la directiva de nomenclatura actual para ver la configuración actual.
  
  ````
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ````
  
2. Muestre la configuración actual del grupo.
  
  ````
  $Setting.Values
  ````
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Establecimiento de la directiva de nomenclatura y de las palabras bloqueadas personalizadas

1. Establezca los prefijos y sufijos de nombre de grupo en Azure AD PowerShell.
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
  ````
  
2. Establezca las palabras bloqueadas personalizadas que quiere restringir. En el ejemplo siguiente se muestra cómo puede agregar sus propias palabras personalizadas.
  
  ````
  $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
  ````
  
3. Guarde la configuración de la nueva directiva para que se aplique, como en el ejemplo siguiente.
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````
  
Eso es todo. Ha establecido la directiva de nomenclatura y ha agregado sus palabras bloqueadas.

## <a name="export-or-import-the-list-of-custom-blocked-words"></a>Exportación o importación de la lista de palabras bloqueadas personalizadas

Para más información, consulte el artículo [Cmdlets de Azure Active Directory para configurar las opciones de grupo](active-directory-accessmanagement-groups-settings-cmdlets.md).

Este es un script de PowerShell de ejemplo para exportar varias palabras bloqueadas:

````
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
````

Este es un script de PowerShell de ejemplo para importar varias palabras bloqueadas:

````
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
$Settings["EnableMSStandardBlockedWords"] = $True
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
````

## <a name="naming-policy-experiences-across-office-365-apps"></a>Experiencias de la directiva de nomenclatura en aplicaciones de Office 365

Después de establecer una directiva de nomenclatura de grupo en Azure AD, cuando un usuario crea un grupo en una aplicación de Office 365, puede ver: 

* Una vista previa del nombre de acuerdo con la directiva de nomenclatura (con prefijos y sufijos) tan pronto como el usuario escribe el nombre del grupo
* Si el usuario escribe palabras bloqueadas, verá un mensaje de error para que pueda quitar las palabras bloqueadas.

Carga de trabajo | Cumplimiento normativo
----------- | -------------------------------
Portales de Azure Active Directory | El portal de Azure AD y el portal Panel de acceso muestran la directiva de nomenclatura aplicada cuando el usuario escribe un nombre de grupo al crear o editar un grupo. Cuando un usuario escribe una palabra bloqueada personalizada, aparece un mensaje de error con la palabra bloqueada para que el usuario pueda quitarla.
Outlook Web Access (OWA) | Outlook Web Access muestra el nombre aplicado por la directiva de nomenclatura cuando el usuario escribe un nombre o alias de grupo. Cuando un usuario escribe una palabra bloqueada personalizada, se muestra un mensaje de error en la interfaz de usuario junto con la palabra bloqueada para que el usuario pueda quitarla.
Escritorio de Outlook | Los grupos creados en el escritorio de Outlook son compatibles con la configuración de directivas de nomenclatura. La aplicación de escritorio de Outlook no muestra aún la vista previa del nombre de grupo aplicado y no devuelve errores de palabras bloqueadas personalizadas cuando el usuario escribe el nombre del grupo. Sin embargo, la directiva de nomenclatura se aplica automáticamente al crear o editar un grupo, y los usuarios ven mensajes de error si hay palabras bloqueadas personalizadas en el nombre o alias del grupo.
Equipos de Microsoft | Microsoft Teams muestra el nombre aplicado por la directiva de nomenclatura de grupo cuando el usuario escribe un nombre de equipo. Cuando un usuario escribe una palabra bloqueada personalizada, se muestra un mensaje de error junto con la palabra bloqueada para que el usuario pueda quitarla.
SharePoint  |  SharePoint muestra el nombre aplicado por la directiva de nomenclatura cuando el usuario escribe el nombre de un sitio o la dirección de correo electrónico de un grupo. Cuando un usuario escribe una palabra bloqueada personalizada, se muestra un mensaje de error, junto con la palabra bloqueada para que el usuario pueda quitarla.
Microsoft Stream | Microsoft Stream muestra el nombre aplicado por la directiva de nomenclatura de grupos cuando el usuario escribe el nombre de un grupo o el alias de la dirección de correo electrónico del grupo. Cuando un usuario escribe una palabra bloqueada personalizada, se muestra un mensaje de error con la palabra bloqueada para que el usuario pueda quitarla.
Aplicación de Outlook iOS y Android | Los grupos creados en las aplicaciones de Outlook son compatibles con la directiva de nomenclatura configurada. La aplicación móvil de Outlook no muestra aún la vista previa del nombre aplicado por la directiva de nomenclatura y no devuelve errores de palabras bloqueadas personalizadas cuando el usuario escribe el nombre del grupo. Sin embargo, la directiva de nomenclatura se aplica automáticamente al hacer clic para crear o editar, y los usuarios ven mensajes de error si hay palabras bloqueadas personalizadas en el nombre o alias del grupo.
Aplicación móvil Grupos | Los grupos creados en la aplicación móvil Grupos son compatibles con la directiva de nomenclatura. Esta aplicación no muestra la vista previa de la directiva de nomenclatura y no devuelve errores de palabras bloqueados personalizadas cuando el usuario escribe el nombre del grupo. Sin embargo, la directiva de nomenclatura se aplica automáticamente al crear o editar un grupo, y los usuarios ven los errores correspondientes si hay palabras bloqueadas personalizadas en el nombre o alias del grupo.
Planner | Planner es compatible con la directiva de nomenclatura. Planner muestra la versión preliminar de la directiva de nomenclatura al escribir el nombre del plan. Cuando un usuario escribe una palabra bloqueada personalizada, se muestra un mensaje de error al crear el plan.
Dynamics 365 for Customer Engagement | Dynamics 365 for Customer Engagement es compatible con la directiva de nomenclatura. Dynamics 365 muestra el nombre aplicado por la directiva de nomenclatura cuando el usuario escribe el nombre o el alias de correo electrónico de un grupo. Cuando el usuario escribe una palabra bloqueada personalizada, se muestra un mensaje de error con la palabra bloqueada para que el usuario pueda quitarla.
School Data Sync (SDS) | Los grupos creados mediante SDS cumplen la directiva de nomenclatura, pero esta no se aplica automáticamente. Los administradores de SDS tienen que anexar los prefijos y sufijos a los nombres de clase para los que es necesario crear grupos y luego cargarlos en SDS. De lo contrario, la creación o edición de los grupos produciría error.
Outlook Customer Manager (OCM) | Outlook Customer Manager es compatible con la directiva de nomenclatura, que se aplica automáticamente al grupo creado en Outlook Customer Manager. Si se detecta una palabra bloqueada personalizada, la creación de grupos en OCM se bloquea y se impide al usuario usar la aplicación OCM.
Aplicación Aula | Los grupos creados en la aplicación Aula cumplen la directiva de nomenclatura, pero esta no se aplica automáticamente y los usuarios no tienen una vista previa de ella al escribir el nombre de un grupo del aula. Los usuarios deben escribir el nombre de grupo del aula aplicado con prefijos y sufijos. Si no, las operaciones para crear o editar el grupo del aula producirán error.
Power BI | Las áreas de trabajo de Power BI son compatibles con la directiva de nomenclatura.    
Yammer | Los grupos conectados de Yammer no aplican la directiva de nomenclatura configurada. En las organizaciones que tienen habilitada la directiva de nomenclatura, Yammer crea grupos de Yammer heredados que no se conectan a Office 365 si los grupos no guardan conformidad con la directiva de nomenclatura.
StaffHub  | Los equipos de StaffHub no siguen la directiva de nomenclatura, pero el grupo de Office 365 subyacente sí lo hace. El nombre de equipo de StaffHub no aplica los prefijos y sufijos y no busca palabras bloqueadas personalizadas. Sin embargo, StaffHub aplica los prefijos y sufijos y quita las palabras bloqueadas del grupo de Office 365 subyacente.
Exchange PowerShell | Los cmdlets de Exchange PowerShell son compatibles con la directiva de nomenclatura. Los usuarios reciben los correspondientes mensajes de error con sugerencias de prefijos y sufijos y con las palabras bloqueadas personalizadas si no siguen la directiva de nomenclatura en el nombre o alias del grupo (mailNickname).
Cmdlets de PowerShell para Azure Active Directory | Los cmdlets de PowerShell para Azure Active Directory son compatibles con la directiva de nomenclatura. Los usuarios reciben los correspondientes mensajes de error con sugerencias de prefijos y sufijos y con las palabras bloqueadas personalizadas si no siguen la convención de nomenclatura tanto en los nombres de grupo como en los alias de grupo.
Centro de administración de Exchange | El centro de administración de Exchange es compatible con la directiva de nomenclatura. Los usuarios reciben los correspondientes mensajes de error con sugerencias de prefijos y sufijos y con las palabras bloqueadas personalizadas si no siguen la convención de nomenclatura en el nombre o alias del grupo.
Centro de administración de Office 365 | El centro de administración de Office 365 es compatible con la directiva de nomenclatura. Cuando un usuario crea o edita nombres de grupo, la directiva de nomenclatura se aplica automáticamente y los usuarios reciben los correspondientes errores al escribir palabras bloqueadas personalizadas. El centro de administración de Office 365 no muestra aún una vista previa de la directiva de nomenclatura y no devuelve errores de palabras bloqueadas personalizadas cuando el usuario escribe el nombre del grupo.

## <a name="next-steps"></a>pasos siguientes
En estos artículos se proporciona información adicional sobre los grupos de Azure AD.

* [Ver los grupos existentes](active-directory-groups-view-azure-portal.md)
* [Directiva de caducidad para grupos de Office 365](active-directory-groups-lifecycle-azure-portal.md)
* [Administración de la configuración de un grupo](active-directory-groups-settings-azure-portal.md)
* [Administrar miembros de un grupo](active-directory-groups-members-azure-portal.md)
* [Administrar la pertenencia a grupos](active-directory-groups-membership-azure-portal.md)
* [Administrar reglas dinámicas de los usuarios de un grupo](active-directory-groups-dynamic-membership-azure-portal.md)
