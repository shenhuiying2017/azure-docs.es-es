---
title: "Grupos de equipos en búsquedas de registros en Log Analytics | Microsoft Docs"
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
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6c0affd0f5ea600f979cfcc87e2435658c8dab14


---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Grupos de equipos en búsquedas de registros en Log Analytics
Los grupos de equipos en Log Analytics permiten delimitar las [búsquedas de registros](log-analytics-log-searches.md) a un conjunto concreto de equipos.  Cada grupo se rellena con equipos mediante una consulta que defina o a través de la importación de grupos de diferentes orígenes.  Cuando el grupo se incluye en una búsqueda de registros, los resultados se limitan a los registros que coinciden con los equipos del grupo.

## <a name="creating-a-computer-group"></a>Creación de un grupo de equipos
Puede crear un grupo de equipos en Log Analytics mediante cualquiera de los métodos de la tabla siguiente.  En las secciones siguientes se proporcionan detalles sobre cada método. 

| Método | Descripción |
|:--- |:--- |
| Búsqueda de registros |Cree una búsqueda de registros que devuelva una lista de equipos y guarde los resultados como un grupo de equipos. |
| API de búsqueda de registros |Use la API de búsqueda de registros para crear mediante programación un grupo de equipos basándose en los resultados de una búsqueda de registros. |
| Active Directory |Analice automáticamente la pertenencia al grupo de cualquier equipo agente que sea miembro de un dominio de Active Directory y cree un grupo en Log Analytics para cada grupo de seguridad. |
| WSUS |Analice automáticamente clientes o servidores WSUS para grupos de destino y cree un grupo en Log Analytics para cada uno. |

### <a name="log-search"></a>Búsqueda de registros
Los grupos de equipos creados a partir de una búsqueda de registros contendrán todos los equipos devueltos por una consulta de búsqueda que defina.  Esta consulta se ejecuta cada vez que se usa el grupo de equipos de forma que se reflejen todos los cambios que se hayan producido desde la creación del grupo.

Utilice el procedimiento siguiente para crear un grupo de equipos a partir de una búsqueda de registros.

1. [Cree una búsqueda de registros](log-analytics-log-searches.md) que devuelva una lista de equipos.  La búsqueda debe devolver un conjunto determinado de equipos, para lo que debe emplearse una instrucción como **Distinct Computer** o **measure count() by Computer** en la consulta.  
2. Haga clic en el botón **Guardar** de la parte superior de la pantalla.
3. Seleccione **Sí** para **Guardar esta consulta como un grupo de equipos:**.
4. Especifique un **nombre** y una **categoría** para el grupo.  Si ya existe una búsqueda con el mismo nombre y la misma categoría, se le pedirá que la sobrescriba.  Puede tener varias búsquedas con el mismo nombre en diferentes categorías. 

A continuación se incluyen ejemplos de búsquedas que puede guardar como un grupo de equipos.

    Computer="Computer1" OR Computer="Computer2" | distinct Computer 
    Computer=*srv* | measure count() by Computer

### <a name="log-search-api"></a>API de búsqueda de registros
Los grupos de equipos creados con la API de búsqueda de registros son los mismos que las búsquedas creadas con una búsqueda de registros.

Para más información sobre la creación de un grupo de equipos con la API de búsqueda de registros, consulte [Grupos de equipos en API de REST de búsqueda de registros de Log Analytics](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>Active Directory
Al configurar Log Analytics para importar pertenencias a grupos de Active Directory, analizará la pertenencia al grupo de todos los equipos unidos al dominio con el agente de OMS.  En Log Analytics se crea un grupo de equipos para cada grupo de seguridad de Active Directory y cada equipo se agrega a los grupos de equipos correspondientes a los grupos de seguridad de los que son miembros.  Esta pertenencia se actualiza continuamente cada 4 horas.  

Log Analytics se configura para importar grupos de seguridad de Active Directory desde el menú **Grupos de equipos** de la **Configuración** de Log Analytics.  Seleccione **Automatización** y, a continuación, **Importar pertenencias a grupos de Active Directory desde los equipos**.  No es necesario realizar ninguna configuración más.

![Grupos de equipos de Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Una vez importados los grupos, el menú mostrará el número de equipos con la pertenencia a grupos detectada y el número de grupos de importados.  Puede hacer clic en cualquiera de estos vínculos para devolver los registros de **ComputerGroup** con esta información.

### <a name="windows-server-update-service"></a>Windows Server Update Services
Al configurar Log Analytics para importar pertenencias a grupos de WSUS, analizará la pertenencia al grupo de destino de todos los equipos con el agente de OMS.  Si utiliza destinatarios del lado cliente, se importará a Log Analytics la pertenencia a grupos de todos los equipos conectados a OMS que formen parte de cualquier grupo de destino de WSUS. Si usa destinatarios del lado servidor, debería instalarse el agente de OMS en el servidor WSUS para que se importe la información de pertenencia a grupos a OMS.  Esta pertenencia se actualiza continuamente cada 4 horas. 

Log Analytics se configura para importar grupos de seguridad de Active Directory desde el menú **Grupos de equipos** de la **Configuración** de Log Analytics.  Seleccione **Active Directory** y, a continuación, **Importar pertenencias a grupos de Active Directory desde los equipos**.  No es necesario realizar ninguna configuración más.

![Grupos de equipos de Active Directory](media/log-analytics-computer-groups/configure-wsus.png)

Una vez importados los grupos, el menú mostrará el número de equipos con la pertenencia a grupos detectada y el número de grupos de importados.  Puede hacer clic en cualquiera de estos vínculos para devolver los registros de **ComputerGroup** con esta información.

## <a name="managing-computer-groups"></a>Administración de grupos de equipos
Puede ver grupos de equipos creados a partir de una búsqueda de registros o de la API de búsqueda de registros desde el menú **Grupos de equipos** de la **Configuración** de Log Analytics.  Haga clic en la **x** de la columna **Quitar** para eliminar el grupo de equipos.  Haga clic en el icono **Ver miembros** de un grupo para ejecutar la búsqueda de registros del grupo que devuelve sus miembros. 

![Grupos de equipos guardados](media/log-analytics-computer-groups/configure-saved.png)

Para modificar un grupo, cree un nuevo grupo con la misma **categoría** y el mismo **nombre** para sobrescribir el grupo original.

## <a name="using-a-computer-group-in-a-log-search"></a>Uso de un grupo de equipos de una búsqueda de registros
Para hacer referencia a un grupo de equipos de una búsqueda de registros se utiliza la siguiente sintaxis.  La **categoría** puede especificarse opcionalmente, salvo si tiene grupos de equipos con el mismo nombre en distintas categorías, en cuyo caso es necesario especificarla. 

    $ComputerGroups[Category: Name]

Cuando se ejecuta una búsqueda, los miembros de los grupos de equipos incluidos en la búsqueda se resuelven en primer lugar.  Si el grupo está basado en una búsqueda de registros, esa búsqueda se ejecuta para devolver a los miembros del grupo antes de realizar la búsqueda de registros de nivel superior.

Los grupos de equipos se utilizan normalmente con la cláusula **IN** en la búsqueda de registros, como en el ejemplo siguiente.

    Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]

## <a name="computer-group-records"></a>Registros de grupos de equipos
En el repositorio de OMS se crea un registro para cada pertenencia a grupos de equipos creada mediante Active Directory o WSUS.  Estos registros tienen el tipo **ComputerGroup** y sus propiedades son las que aparecen en la tabla siguiente.  Para los grupos de equipos basados en búsquedas de registros no se crean registros.

| Propiedad | Descripción |
|:--- |:--- |
| Tipo |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| Equipo |Nombre del equipo miembro. |
| Grupo |Nombre del grupo. |
| GroupFullName |Ruta de acceso completa al grupo, incluidos el origen y el nombre de origen. |
| GroupSource |Origen desde el que se ha recopilado el grupo. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |Nombre del origen desde el que se recopilaron los grupos.  En el caso de Active Directory, es el nombre del dominio. |
| ManagementGroupName |El nombre del grupo de administración para los agentes de SCOM.  En el caso de los otros agentes, es AOI-\<id. de área de trabajo\>. |
| TimeGenerated |Fecha y hora en la que se creó o actualizó el grupo de equipos. |

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre las [búsquedas de registros](log-analytics-log-searches.md) para analizar los datos recopilados desde soluciones y orígenes de datos.  




<!--HONumber=Nov16_HO3-->


