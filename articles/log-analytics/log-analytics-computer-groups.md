---
title: "Grupos de equipos en búsquedas de registros de Azure Log Analytics | Microsoft Docs"
description: "Los grupos de equipos en Log Analytics permiten delimitar las búsquedas de registros a un conjunto concreto de equipos.  En este artículo se describen los distintos métodos que puede utilizar para crear grupos de equipos y cómo usar estos grupos en una búsqueda de registros."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: bwren
ms.openlocfilehash: 4d6a80082711f09e9c189d53fb4fda00a7d73c29
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Grupos de equipos en búsquedas de registros en Log Analytics

Los grupos de equipos en Log Analytics permiten delimitar las [búsquedas de registros](log-analytics-log-search-new.md) a un conjunto concreto de equipos.  Cada grupo se rellena con equipos mediante una consulta que defina o a través de la importación de grupos de diferentes orígenes.  Cuando el grupo se incluye en una búsqueda de registros, los resultados se limitan a los registros que coinciden con los equipos del grupo.

## <a name="creating-a-computer-group"></a>Creación de un grupo de equipos
Puede crear un grupo de equipos en Log Analytics mediante cualquiera de los métodos de la tabla siguiente.  En las secciones siguientes se proporcionan detalles sobre cada método. 

| Método | DESCRIPCIÓN |
|:--- |:--- |
| Búsqueda de registros |Cree una búsqueda de registros que devuelva una lista de equipos. |
| API de búsqueda de registros |Use la API de búsqueda de registros para crear mediante programación un grupo de equipos basándose en los resultados de una búsqueda de registros. |
| Active Directory |Analice automáticamente la pertenencia al grupo de cualquier equipo agente que sea miembro de un dominio de Active Directory y cree un grupo en Log Analytics para cada grupo de seguridad. |
| Administrador de configuración | Importe colecciones desde System Center Configuration Manager y cree un grupo en Log Analytics para cada una. |
| Windows Server Update Services |Analice automáticamente clientes o servidores WSUS para grupos de destino y cree un grupo en Log Analytics para cada uno. |

### <a name="log-search"></a>Búsqueda de registros
Los grupos de equipos creados a partir de una búsqueda de registros contendrán todos los equipos devueltos por una consulta que defina.  Esta consulta se ejecuta cada vez que se usa el grupo de equipos de forma que se reflejen todos los cambios que se hayan producido desde la creación del grupo.  

Puede usar cualquier consulta para un grupo de equipos, pero debe devolver un conjunto distinto de equipos mediante el uso de `distinct Computer`.  A continuación, se muestra una búsqueda de ejemplo típica que puede usar para un grupo de equipos.

    Heartbeat | where Computer contains "srv" | distinct Computer

En la tabla siguiente, se describen las propiedades que definen un grupo de equipos.

| Propiedad | DESCRIPCIÓN |
|:---|:---|
| Display Name (Nombre para mostrar)   | Nombre de la búsqueda que se va a mostrar en el portal. |
| Categoría       | Categoría para organizar las búsquedas en el portal. |
| Consultar          | La consulta para el grupo de equipos. |
| Alias de función | Un alias único usado para identificar el grupo de equipos en una consulta. |

Utilice el procedimiento siguiente para crear un grupo de equipos a partir de una búsqueda de registros en Azure Portal.

2. Abra **Búsqueda de registros** y, a continuación, haga clic en **Búsquedas guardadas** en la parte superior de la pantalla.
3. Haga clic en **Agregar** y proporcione valores para cada propiedad del grupo de equipos.
4. Seleccione **Guardar esta consulta como un grupo de equipos** y haga clic en **Aceptar**.


Utilice el procedimiento siguiente para crear un grupo de equipos a partir de una búsqueda de registros en el portal de OMS.

1. Abra **Búsqueda de registros** y cree la búsqueda de registros para el grupo de equipos.  
2. Haga clic en el botón **Guardar** de la parte superior de la pantalla.
3. Seleccione **Sí** para **Guardar esta consulta como un grupo de equipos**.
5. Proporcione valores para cada propiedad del grupo de equipos. 


>[!NOTE]
> Si el área de trabajo sigue utilizando el [lenguaje de consulta de Log Analytics heredado](log-analytics-log-search-upgrade.md), utilice el mismo procedimiento para crear un grupo de equipos, pero debe usar la sintaxis del lenguaje de consulta heredado.


### <a name="log-search-api"></a>API de búsqueda de registros
Los grupos de equipos creados con la API de búsqueda de registros son los mismos que las búsquedas creadas con una búsqueda de registros.  Para más información sobre la creación de un grupo de equipos con la API de búsqueda de registros, consulte [Grupos de equipos en API de REST de búsqueda de registros de Log Analytics](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>Active Directory
Al configurar Log Analytics para importar pertenencias a grupos de Active Directory, analiza la pertenencia al grupo de todos los equipos unidos al dominio con el agente de OMS.  En Log Analytics se crea un grupo de equipos para cada grupo de seguridad de Active Directory y cada equipo se agrega a los grupos de equipos correspondientes a los grupos de seguridad de los que son miembros.  Esta pertenencia se actualiza continuamente cada 4 horas.  

Configure Log Analytics para importar los grupos de seguridad de Active Directory en **Configuración avanzada** de Log Analytics en Azure Portal.  Seleccione **Grupos de equipos**, **Active Directory** y, a continuación, **Importar pertenencias a grupos de Active Directory de los equipos**.  No es necesario realizar ninguna configuración más.

![Grupos de equipos de Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Una vez importados los grupos, el menú muestra el número de equipos con la pertenencia a grupos detectada y el número de grupos de importados.  Puede hacer clic en cualquiera de estos vínculos para devolver los registros de **ComputerGroup** con esta información.

### <a name="windows-server-update-service"></a>Windows Server Update Services
Al configurar Log Analytics para importar pertenencias a grupos de WSUS, analiza la pertenencia al grupo de destino de todos los equipos con el agente de OMS.  Si utiliza destinatarios del lado cliente, se importará a Log Analytics la pertenencia a grupos de todos los equipos conectados a Log Analytics que formen parte de cualquier grupo de destino de WSUS. Si usa destinatarios del lado servidor, le recomendamos que instale el agente de OMS en el servidor WSUS para que se importe la información de pertenencia a grupos a Log Analytics.  Esta pertenencia se actualiza continuamente cada 4 horas. 

Configure Log Analytics para importar los grupos de WSUS en **Configuración avanzada** de Log Analytics en Azure Portal.  Seleccione **Grupos de equipos**, **WSUS** y, a continuación, **Importar pertenencias a grupos de WSUS**.  No es necesario realizar ninguna configuración más.

![Grupos de equipos de WSUS](media/log-analytics-computer-groups/configure-wsus.png)

Una vez importados los grupos, el menú muestra el número de equipos con la pertenencia a grupos detectada y el número de grupos de importados.  Puede hacer clic en cualquiera de estos vínculos para devolver los registros de **ComputerGroup** con esta información.

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Al configurar Log Analytics para importar miembros de la colección de Configuration Manager, este crea un grupo de equipos para cada colección.  La información de los miembros de la colección se recupera cada tres horas para mantener actualizados los grupos de equipos. 

Para poder importar colecciones de Configuration Manager, debe [conectar Configuration Manager a Log Analytics](log-analytics-sccm.md).  A continuación, puede configurar la importación en **Configuración avanzada** de Log Analytics en Azure Portal.  Seleccione **Grupos de equipos**, **SCCM** y, a continuación, **Importar miembros de la colección de Configuration Manager**.  No es necesario realizar ninguna configuración más.

![Grupos de equipos de SCCM](media/log-analytics-computer-groups/configure-sccm.png)

Una vez importadas las colecciones, el menú muestra el número de equipos con la pertenencia a grupos detectada y el número de grupos de importados.  Puede hacer clic en cualquiera de estos vínculos para devolver los registros de **ComputerGroup** con esta información.

## <a name="managing-computer-groups"></a>Administración de grupos de equipos
Puede ver los grupos de equipos que se crearon a partir de una búsqueda de registros o la API de búsqueda de registros en **Configuración avanzada** de Log Analytics en Azure Portal.  Seleccione **Grupos de equipos** y, a continuación, **Grupos guardados**.  

Haga clic en la **x** de la columna **Quitar** para eliminar el grupo de equipos.  Haga clic en el icono **Ver miembros** de un grupo para ejecutar la búsqueda de registros del grupo que devuelve sus miembros.  No se puede modificar un grupo de equipos, se debe eliminar y, a continuación, volver a crear con la configuración modificada.

![Grupos de equipos guardados](media/log-analytics-computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-search"></a>Uso de un grupo de equipos de una búsqueda de registros
Puede utilizar un grupo de equipos creado a partir de una búsqueda de registro en una consulta tratando su alias como una función, normalmente con la sintaxis siguiente:

  `Table | where Computer in (ComputerGroup)`

Por ejemplo, podría usar lo siguiente para devolver registros UpdateSummary solo para los equipos de un grupo de equipos llamado mycomputergroup.
 
  `UpdateSummary | where Computer in (mycomputergroup)`


Los grupos de equipos importados y sus equipos incluidos se almacenan en la tabla **ComputerGroup**.  Por ejemplo, la siguiente consulta devolvería una lista de equipos en el grupo Equipos del dominio de Active Directory. 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

La siguiente consulta devolvería registros UpdateSummary solo para equipos del grupo Equipos del dominio.

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```



  

>[!NOTE]
> Si el área de trabajo sigue usando el [lenguaje de consulta de Log Analytics heredado](log-analytics-log-search-upgrade.md), use la siguiente sintaxis para hacer referencia a un grupo de equipos en una búsqueda de registros.  La **Categoría** es opcional y solo se necesita si tiene grupos de equipos con el mismo nombre en diferentes categorías. 
>
>    `$ComputerGroups[Category: Name]`
>
>Los grupos de equipos se utilizan normalmente con la cláusula **IN** en la búsqueda de registros, como en el ejemplo siguiente:
>
>    `Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]`



## <a name="computer-group-records"></a>Registros de grupos de equipos
En el área de trabajo de Log Analytics se crea un registro para cada pertenencia a grupos de equipos creada mediante Active Directory o WSUS.  Estos registros tienen el tipo **ComputerGroup** y sus propiedades son las que aparecen en la tabla siguiente.  Para los grupos de equipos basados en búsquedas de registros no se crean registros.

| Propiedad | DESCRIPCIÓN |
|:--- |:--- |
| type |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Equipo |Nombre del equipo miembro. |
| Grupo |Nombre del grupo. |
| GroupFullName |Ruta de acceso completa al grupo, incluidos el origen y el nombre de origen. |
| GroupSource |Origen desde el que se ha recopilado el grupo. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |Nombre del origen desde el que se recopiló el grupo.  En el caso de Active Directory, es el nombre del dominio. |
| ManagementGroupName |Nombre del grupo de administración de agentes SCOM.  En el caso de los otros agentes, es AOI-\<id. de área de trabajo\>. |
| TimeGenerated |Fecha y hora en la que se creó o actualizó el grupo de equipos. |

## <a name="next-steps"></a>pasos siguientes
* Obtenga información acerca de las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados de soluciones y orígenes de datos.  

