---
title: "Sincronización de Azure AD Connect: tareas y consideraciones operativas | Microsoft Docs"
description: "En este tema se describen las tareas operativas de la sincronización de Azure AD Connect y cómo prepararse para utilizar este componente."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 0dfdae45ef7508337a1233c651d355d83b9f0430
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Sincronización de Azure AD Connect: tareas y consideraciones operativas
El objetivo de este tema es describir las tareas operativas de la sincronización de Azure AD Connect.

## <a name="staging-mode"></a>Modo provisional
Se puede usar el modo provisional para distintos escenarios como:

* Alta disponibilidad.
* Prueba e implementación de nuevos cambios de configuración.
* Introducción de un nuevo servidor y baja del antiguo.

Con un servidor en modo provisional puede realizar cambios en la configuración y obtener una vista previa de los cambios antes de activar el servidor. También permite ejecutar una importación y sincronización completas para comprobar que se esperan todos los cambios antes de realizarlos en el entorno de producción.

Durante la instalación puede seleccionar que el servidor esté en **modo provisional**. Esta acción activará el servidor para la importación y sincronización, pero no realizará ninguna exportación. Un servidor en modo provisional no ejecutará la sincronización de contraseñas ni habilitará la escritura diferida de contraseñas, aunque se seleccionen estas características durante la instalación. Cuando se deshabilita el modo provisional, el servidor iniciará la exportación, y habilitará la sincronización de contraseñas y la escritura diferida de contraseñas.

> [!NOTE]
> Suponga que tiene una instancia de Azure AD Connect con la característica de sincronización de hash de contraseña habilitada. Cuando se habilita el modo de almacenamiento provisional, el servidor deja de sincronizar los cambios de contraseña desde AD local. Cuando se deshabilita el modo de almacenamiento provisional, el servidor reanuda la sincronización de los cambios de contraseña desde AD local. Si el servidor se mantiene en modo de almacenamiento provisional durante un largo período de tiempo, el servidor puede tardar unos minutos en sincronizar todos los cambios de contraseña que se hubieran producido durante ese período de tiempo.
>
>

Todavía puede forzar una exportación mediante el administrador del servicio de sincronización.

Un servidor en modo provisional seguirá recibiendo cambios de Active Directory y Azure AD. Siempre tendrá una copia de los cambios más recientes y podrá asumir muy rápidamente las responsabilidades de otro servidor. Si realiza cambios de configuración en el servidor principal, es su responsabilidad realizar los mismos cambios en el servidor o servidores en modo provisional.

Para aquellos con conocimientos de tecnologías de sincronización anteriores, el modo provisional es diferente, ya que el servidor tiene su propia base de datos SQL. Esta arquitectura permite que el servidor en modo provisional se encuentre en otro centro de datos.

### <a name="verify-the-configuration-of-a-server"></a>Comprobación de la configuración de un servidor
Para aplicar este método, siga estos pasos:

1. [Preparación](#prepare)
2. [Configuración](#configuration)
3. [Importación y sincronización](#import-and-synchronize)
4. [Verify](#verify)
5. [Cambio de servidor activo](#switch-active-server)

#### <a name="prepare"></a>Preparación
1. Instale Azure AD Connect, seleccione el **modo provisional** y anule la selección de **Iniciar la sincronización** en la última página del Asistente para instalación. Esto permitirá ejecutar el motor de sincronización de forma manual.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Cierre o inicie la sesión y, en el menú de inicio, seleccione **Servicio de sincronización**.

#### <a name="configuration"></a>Configuración
Si ha realizado cambios personalizados en el servidor principal y desea comparar la configuración con el servidor de almacenamiento provisional, utilice el [documentador de configuración de Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importación y sincronización
1. Seleccione **Conectores** y elija el primer conector de tipo **Active Directory Domain Services**. Haga clic en **Ejecutar** y seleccione **Importación completa** y **Aceptar**. Realice estos pasos para todos los conectores de este tipo.
2. Seleccione el conector de tipo **Azure Active Directory (Microsoft)**. Haga clic en **Ejecutar** y seleccione **Importación completa** y **Aceptar**.
3. Asegúrese de que sigue seleccionada la pestaña Conectores. Para cada conector de tipo **Active Directory Domain Services**, haga clic en **Ejecutar**, y seleccione **Sincronización diferencial** y **Aceptar**.
4. Seleccione el conector de tipo **Azure Active Directory (Microsoft)**. Haga clic en **Ejecutar**, seleccione **Sincronización diferencial** y, después, **Aceptar**.

Ahora ha almacenado provisionalmente los cambios de exportación en Azure AD y AD local (si está usando la implementación híbrida de Exchange). Los siguientes pasos le permiten inspeccionar lo que está a punto de cambiar antes de que comience realmente la exportación a los directorios.

#### <a name="verify"></a>Verify
1. Inicie un símbolo del sistema y vaya a `%ProgramFiles%\Microsoft Azure AD Sync\bin`.
2. Ejecute: `csexport "Name of Connector" %temp%\export.xml /f:x` El nombre del conector puede encontrarse en el Servicio de sincronización. Tendrá un nombre similar a contoso.com – AAD en Azure AD.
3. Ejecute: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` Ahora tiene un archivo en %temp% denominado "export.csv" que se puede examinar en Microsoft Excel. Este archivo contiene todos los cambios que se van a exportar.
4. Realice los cambios necesarios en los datos o la configuración y ejecute estos pasos de nuevo (Importación y sincronización y Comprobación) hasta que se esperen los cambios que se van a exportar.

**Descripción del archivo export.csv** La mayor parte del archivo se explica por sí solo. Algunas abreviaturas para comprender el contenido:
* OMODT: tipo de modificación de objeto. Indica si la operación en un nivel de objeto es Agregar, Actualizar o Eliminar.
* AMODT: tipo de modificación de atributo. Indica si la operación en un nivel de atributo es Agregar, Actualizar o Eliminar.

**Recuperación de identificadores comunes** El archivo export.csv contiene todos los cambios que se van a exportar. Cada fila corresponde a un cambio de un objeto en el espacio del conector y el objeto se identifica mediante el atributo DN. El atributo DN es un identificador único asignado a un objeto en el espacio del conector. Cuando haya muchas filas o cambios en el archivo export.csv que analizar, puede ser difícil averiguar para qué objetos son los cambios con solo el atributo DN. Para simplificar el proceso de análisis de los cambios, use el script de PowerShell csanalyzer.ps1. El script recupera los identificadores comunes (por ejemplo, displayName o userPrincipalName) de los objetos. Para usar el script:
1. Copie el script de PowerShell de la sección [CSAnalyzer](#appendix-csanalyzer) en un archivo denominado "`csanalyzer.ps1`".
2. Abra una ventana de PowerShell y vaya a la carpeta donde se creó el script de PowerShell.
3. Ejecute `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Ahora tiene un archivo denominado "**processedusers1.csv**" que se puede examinar en Microsoft Excel. Tenga en cuenta que el archivo proporciona una asignación del atributo DN a identificadores comunes (por ejemplo, displayName y userPrincipalName). Actualmente no incluye los cambios de atributo reales que se van a exportar.

#### <a name="switch-active-server"></a>Cambio de servidor activo
1. En el servidor actualmente activo, desactive el servidor (DirSync/FIM/Azure AD Sync) para que no exporte a Azure AD o establézcalo en modo provisional (Azure AD Connect).
2. Ejecute el Asistente para instalación en el servidor en **modo provisional** y deshabilite el **modo provisional**.
   ![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Recuperación ante desastres
Parte del diseño de implementación es planear qué hacer en caso de que se produzca un desastre en el que se pierda el servidor de sincronización. Hay diferentes modelos de uso y cuál es el que se debe usar dependerá de varios factores como los siguientes:

* ¿Cuál es su grado de tolerancia por no ser capaz de realizar cambios en objetos en Azure AD durante el tiempo de inactividad?
* Si usa la sincronización de contraseñas, ¿aceptarán los usuarios que tienen que usar la antigua contraseña en Azure AD en caso de que la cambien de forma local?
* ¿Tiene una dependencia en operaciones en tiempo real, como la escritura diferida de contraseñas?

En función de las respuestas a estas preguntas y de la directiva de su organización, puede implementar una de las estrategias siguientes:

* Recompilación si es necesario.
* Servidor en espera de reserva, conocido como **modo provisional**.
* Uso de máquinas virtuales.

Si no utiliza la base de datos de SQL Express integrada, también debe revisar la sección sobre [alta disponibilidad de SQL](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Recompilación si es necesario
Una estrategia viable es planear una recompilación del servidor si es necesario. Normalmente, la instalación del motor de sincronización y la realización de la importación y sincronización iniciales se pueden completar en unas pocas horas. Si no hay un servidor de reserva disponible, es posible usar temporalmente un controlador de dominio para hospedar el motor de sincronización.

El servidor del motor de sincronización no almacena ningún estado acerca de los objetos, por lo que se puede recompilar la base de datos a partir de los datos de Active Directory y Azure AD. El atributo **sourceAnchor** se usa para unir los objetos de local y de la nube. Si recompila el servidor con objetos existentes locales y en la nube, el motor de sincronización los hará coincidir de nuevo en la reinstalación. Las acciones que necesita documentar y guardar son los cambios de configuración realizados en el servidor, como reglas de filtrado y de sincronización. Estas configuraciones personalizadas deben volver a aplicarse antes de iniciar la sincronización.

### <a name="have-a-spare-standby-server---staging-mode"></a>Servidor en espera de reserva - modo provisional
Si tiene un entorno más complejo, se recomienda tener uno o más servidores en espera. Durante la instalación puede habilitar un servidor que esté en **modo provisional**.

Para obtener más información, consulte [Modo provisional](#staging-mode).

### <a name="use-virtual-machines"></a>Uso de máquinas virtuales
Un método común y admitido es ejecutar el motor de sincronización en una máquina virtual. En caso de que el host tenga un problema, la imagen con el servidor del motor de sincronización se puede migrar a otro servidor.

### <a name="sql-high-availability"></a>alta disponibilidad de SQL
Si no utiliza la versión de SQL Server Express que se incluye con Azure AD Connect, también debe tener en cuenta la alta disponibilidad para SQL Server. Las soluciones de alta disponibilidad compatibles incluyen la agrupación en clústeres de SQL y AOA (Grupos de disponibilidad AlwaysOn). Entre las soluciones no admitidas se incluye la creación de reflejo.

Se agregó la compatibilidad de AOA de SQL a Azure AD Connect en la versión 1.1.524.0. Debe habilitar AOA de SQL antes de instalar Azure AD Connect. Durante la instalación, Azure AD Connect detecta si la instancia de SQL proporcionada está habilitada para AOA de SQL o no. Si AOA de SQL está habilitada, Azure AD Connect averigua si AOA de SQL está configurada para usar la replicación sincrónica o asincrónica. Cuando configure la escucha de grupo de disponibilidad, se recomienda que establezca la propiedad RegisterAllProvidersIP en 0. Esto se debe a que Azure AD Connect actualmente usa SQL Native Client para conectarse a SQL y SQL Native Client no admite el uso de la propiedad MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Apéndice: CSAnalyzer
Vea la sección de [comprobación](#verify) sobre cómo usar este script.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as as CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>pasos siguientes
**Temas de introducción**  

* [Sincronización de Azure AD Connect: comprender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)  
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)  
