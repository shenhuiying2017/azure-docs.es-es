---
title: "Optimización del entorno de System Center Operations Manager con Azure Log Analytics | Microsoft Docs"
description: "Puede usar periódicamente la solución Evaluación de System Center Operations Manager para evaluar el riesgo y el estado de los entornos de servidor."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4992d98397da409f7c1cfbdeb40fdb0cdd0d2f19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-assessment-preview-solution"></a>Optimización del entorno con la solución Evaluación de System Center Operations Manager (versión preliminar)

![Símbolo de System Center Operations Manager Assessment](./media/log-analytics-scom-assessment/scom-assessment-symbol.png)

Puede usar periódicamente la solución Evaluación de System Center Operations Manager para evaluar el riesgo y el estado de los entornos de servidor de System Center Operations Manager. Este artículo le ayudará a instalar, configurar y usar la solución para que pueda tomar acciones correctivas en caso de posibles problemas.

Esta solución proporciona una lista priorizada de recomendaciones específicas para su infraestructura de servidor implementada. Las recomendaciones se clasifican en cuatro áreas de enfoque que ayudan a comprender rápidamente el riesgo y a tomar la acción correctiva.

Las recomendaciones efectuadas se basan en los conocimientos y la experiencia adquiridos por los ingenieros de Microsoft fruto de miles de visitas de clientes. En cada recomendación, se explica por qué podría ser importante para usted un problema y se proporcionan instrucciones sobre cómo implementar los cambios sugeridos.

Puede elegir las áreas de enfoque que sean más importantes para su organización y realizar un seguimiento del progreso hacia la consecución de un entorno libre de riesgos y en buen estado.

Después de agregar la solución y completar una evaluación, se muestra información resumida sobre las áreas de enfoque en el panel **Evaluación de System Center Operations Manager** para la infraestructura. Las secciones siguientes describen cómo usar la información que aparece en el panel **Evaluación de System Center Operations Manager**, donde puede ver y ejecutar las acciones recomendadas para su infraestructura de SCOM.

![icono de la solución System Center Operations Manager](./media/log-analytics-scom-assessment/scom-tile.png)

![panel de Evaluación de System Center Operations Manager](./media/log-analytics-scom-assessment/scom-dashboard01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalación y configuración de la solución

La solución funciona con Microsoft System Operations Manager 2012 R2 y 2012 SP1.

Utilice la siguiente información para instalar y configurar la solución.

 - Antes de que pueda usar una solución de evaluación en OMS, debe tener instalada la solución. Instale la solución desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview) o siguiendo las instrucciones del artículo sobre [incorporación de soluciones de Log Analytics desde la Galería de soluciones](log-analytics-add-solutions.md).

 - Después de agregar la solución al área de trabajo, el icono de Evaluación de System Center Operations Manager en el panel muestra el mensaje que indica que es necesario realizar una configuración adicional. Haga clic en el icono y siga los pasos de configuración mencionados en la página.

 ![Icono del panel de System Center Operations Manager](./media/log-analytics-scom-assessment/scom-configrequired-tile.png)

 La configuración de System Center Operations Manager se puede realizar a través del script siguiendo los pasos mencionados en la página de configuración de la solución en OMS.

 En su lugar, para configurar la evaluación a través de la consola de SCOM, siga estos pasos en el mismo orden:
1. [Establecimiento de la cuenta de ejecución de System Center Operations Manager](#operations-manager-run-as-accounts-for-oms)  
2. [Configuración de la regla de System Center Operations Manager](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>Información detallada sobre la recopilación de datos de Evaluación de System Center Operations Manager

Evaluación de System Center Operations Manager recopila datos de WMI, datos del Registro, datos del registro de eventos, datos de Operations Manager mediante Windows PowerShell, consultas SQL, el recopilador de información de archivo usando el servidor que se ha habilitado.

En la siguiente tabla se muestran los métodos de recopilación de datos de Evaluación de System Center Operations Manager, y la frecuencia con la que un agente recopila datos.

| plataforma | Agente directo | Agente de SCOM | Azure Storage | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | | | | &#8226; | | siete días |

## <a name="operations-manager-run-as-accounts-for-oms"></a>Cuentas de ejecución de Operations Manager para OMS

OMS se basa en paquetes de administración de cargas de trabajo para proporcionar servicios de valor añadido. Cada carga de trabajo requiere privilegios específicos de carga de trabajo para ejecutar paquetes de administración en un contexto de seguridad diferente, como una cuenta de dominio. Configure una cuenta de ejecución de Operations Manager para proporcionar la información de las credenciales.

Use la siguiente información para establecer la cuenta de ejecución de Operations Manager para Evaluación de System Center Operations Manager.

### <a name="set-the-run-as-account"></a>Establecimiento de la cuenta de ejecución

1. En la consola de Operations Manager, vaya a la pestaña **Administración**.
2. En **Configuración de ejecución**, haga clic en **Cuentas**.
3. Siga los pasos del asistente para crear una cuenta de Windows y crear la cuenta de ejecución. La cuenta que debe usar es la identificada y con los requisitos previos siguientes:

    >[!NOTE]
    La cuenta de ejecución debe cumplir los siguientes requisitos:
    - Un miembro de la cuenta de dominio del grupo de administradores local en todos los servidores en el entorno (todos los roles de Operations Manager - Servidor de administración, base de datos de OpsMgr, almacenamiento de datos, informes, consola web, puerta de enlace)
    - Rol de administrador de Operation Manager para el grupo de administración que se va a evaluar
    - Ejecute el [script](#sql-script-to-grant-granular-permissions-to-the-run-as-account) para conceder permisos específicos a la cuenta en la instancia de SQL usada por Operations Manager.
      Nota: Si esta cuenta ya tiene derechos sysadmin, omita la ejecución del script.

4. En **Seguridad de distribución**, seleccione **Más seguro**.
5. Especifique el servidor de administración donde se distribuye la cuenta.
3. Vuelva a Configuración de ejecución y haga clic en **Perfiles**.
4. Busque *SCOM Assessment Profile* (Perfil de evaluación de SCOM).
5. El nombre del perfil deberá ser: *Microsoft System Center Advisor SCOM Assessment Run As Profile* (Perfil de ejecución de Evaluación de Microsoft System Center Advisor SCOM).
6. Haga clic con el botón derecho, actualice sus propiedades y agregue la cuenta de ejecución que creó en el paso 3.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Concesión de permisos específicos a la cuenta de ejecución mediante un script SQL

Ejecute el siguiente script SQL para conceder los permisos necesarios a la cuenta de ejecución en la instancia de SQL usada por Operations Manager.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```


### <a name="configure-the-assessment-rule"></a>Configuración de la regla de evaluación

El módulo de administración de la solución Evaluación de System Center Operations Manager incluye una regla llamada *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule* (Regla de Evaluación de Microsoft System Center Advisor SCOM). Esta regla es responsable de ejecutar la evaluación. Para habilitar la regla y configurar la frecuencia, use los procedimientos siguientes.

De forma predeterminada, la regla Microsoft System Center Advisor SCOM Assessment Run Assessment Rule está deshabilitada. Para ejecutar la evaluación, debe habilitar la regla en un servidor de administración. Para ello, siga los pasos que se describen a continuación.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Habilitación de la regla para un servidor de administración específico

1. En el área de trabajo **Creación** de la consola de Operations Manager, busque la regla *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule* (Regla de Evaluación de Microsoft System Center Advisor SCOM) en el panel **Reglas**.
2. En los resultados de búsqueda, seleccione el que incluye el texto *Tipo: servidor de administración*.
3. Haga clic con el botón derecho en la regla y, a continuación, haga clic en **Invalidaciones** > **Para un objeto de clase específico: servidor de administración**.
4.  En la lista de servidores de administración disponibles, seleccione el servidor de administración donde se debe ejecutar la regla.
5.  Asegúrese de cambiar el valor de la invalidación a **True** para el valor del parámetro **Habilitado**.  
    ![invalidar parámetro](./media/log-analytics-scom-assessment/rule.png)

Mientras sigue en esta ventana, configure la frecuencia de la ejecución con el procedimiento siguiente.

#### <a name="configure-the-run-frequency"></a>Configuración de la frecuencia de ejecución

La evaluación está configurada para ejecutarse cada 10.080 minutos (o siete días), el intervalo predeterminado. Puede cambiar el valor por el valor mínimo de 1440 minutos (o un día). El valor representa el intervalo de tiempo mínimo necesario entre ejecuciones de evaluación sucesivas. Para invalidar el intervalo, siga estos pasos.

1. En el área de trabajo **Creación** de la consola de Operations Manager, busque la regla *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule* (Regla de Evaluación de Microsoft System Center Advisor SCOM) en el panel **Reglas**.
2. En los resultados de búsqueda, seleccione el que incluye el texto *Tipo: servidor de administración*.
3. Haga clic con el botón derecho en la regla y, a continuación, haga clic en **Invalidar la regla** > **Para todos los objetos de clase: servidor de administración**.
4. Cambie el valor del parámetro **Intervalo** por el valor de intervalo deseado. En el ejemplo siguiente, el valor se establece en 1440 minutos (un día).  
    ![parámetro de intervalo](./media/log-analytics-scom-assessment/interval.png)  
    Si el valor se establece en menos de 1440 minutos, la regla se ejecuta en un intervalo de un día. En este ejemplo, la regla omite el valor del intervalo y se ejecuta con una frecuencia de un día.


## <a name="understanding-how-recommendations-are-prioritized"></a>Cómo se establecen prioridades entre las recomendaciones

A cada recomendación efectuada se le asigna un valor de ponderación que identifica su importancia relativa. Se muestran solo las diez recomendaciones más importantes.

### <a name="how-weights-are-calculated"></a>Cálculo de las ponderaciones

Las ponderaciones son valores agregados en función de tres factores principales:

- La *probabilidad* de que un asunto identificado pueda causar problemas. Una probabilidad más alta equivale a una puntuación total mayor para la recomendación.
- El *impacto* del asunto en su organización en caso de que se produzca un problema. Un mayor impacto equivale a una puntuación total mayor para la recomendación.
- El *esfuerzo* necesario para implementar la recomendación. Un mayor esfuerzo equivale a una puntuación total menor para la recomendación.

La ponderación de cada recomendación se expresa como un porcentaje de la puntuación total disponible para cada área de enfoque. Por ejemplo, si una recomendación en el área de enfoque Disponibilidad y continuidad empresarial tiene una puntuación del 5 %, implementar esa recomendación aumenta la puntuación total de Disponibilidad y continuidad empresarial un 5 %.

### <a name="focus-areas"></a>Áreas de enfoque

**Disponibilidad y continuidad empresarial** : este apartado muestra recomendaciones relacionadas con la disponibilidad de servicio, la resistencia de la infraestructura y la protección del negocio.

**Rendimiento y escalabilidad** : este apartado muestra recomendaciones que ayudarán a crecer a la infraestructura de TI de su organización y garantizarán que el entorno de TI cumple los requisitos de rendimiento vigentes y que, además, puede responder a las cambiantes necesidades de infraestructura.

**Actualización, migración e implementación**. Esta área de enfoque muestra recomendaciones que lo ayudarán a actualizar, migrar e implementar SQL Server en la infraestructura existente.

**Operaciones y supervisión**. Esta área de enfoque muestra recomendaciones para ayudar a simplificar las operaciones de TI, implementar el mantenimiento preventivo y maximizar el rendimiento.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>¿Debe tratar de conseguir una puntuación del 100 % en cada área de enfoque?

No necesariamente. Las recomendaciones se basan en los conocimientos y las experiencias adquiridos por los ingenieros de Microsoft producto de miles de visitas de clientes. Sin embargo, no hay dos infraestructuras de servidores que sean iguales, y es posible que determinadas recomendaciones puedan ser más o menos relevantes para usted. Por ejemplo, algunas recomendaciones de seguridad pueden ser menos pertinentes si las máquinas virtuales no están expuestas a Internet. Algunas recomendaciones de disponibilidad pueden ser menos relevantes para los servicios que proporcionan informes y recopilaciones de datos ad hoc de baja prioridad. Los problemas que son importantes para un negocio maduro pueden no serlo para otro que esté en sus inicios. Puede que desee identificar qué áreas de enfoque son prioritarias para usted y, posteriormente, observar cómo cambian las puntuaciones con el tiempo.

Cada recomendación incluye pautas que indican por qué es importante. Use estas directrices para evaluar si es adecuado o no implementar la recomendación, en función de la naturaleza de los servicios de TI y las necesidades empresariales de su organización.

## <a name="use-assessment-focus-area-recommendations"></a>Uso de las recomendaciones de área de enfoque de evaluación

Antes de que pueda usar una solución de evaluación en OMS, debe tener instalada la solución. Para más información sobre cómo instalar las soluciones, consulte [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md). Una vez instalados, puede ver el resumen de las recomendaciones mediante el icono Evaluación de System Center Operations Manager en el panel Información general en OMS.

Consulte un resumen de las evaluaciones de cumplimiento para su infraestructura y, a continuación, profundice las recomendaciones.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Visualización de las recomendaciones para un área de enfoque y adopción de las medidas correctivas

1. En la página **Información general**, haga clic en el icono **Evaluación de System Center Operations Manager**.
2. En la página **Evaluación de System Center Operations Manager**, revise la información resumida de una de las hojas de las áreas de enfoque y, a continuación, haga clic en una de ellas para ver las recomendaciones para dicha área de enfoque.
3. En cualquiera de las páginas de área de enfoque, puede ver las recomendaciones priorizadas que se han efectuado para su entorno. Haga clic en una recomendación en **Objetos afectados** para ver los detalles sobre por qué se realiza la recomendación.  
    ![área de enfoque](./media/log-analytics-scom-assessment/focus-area.png)
4. Puede tomar las medidas correctivas que se sugieren en **Acciones sugeridas**. Cuando se haya ocupado del asunto, las evaluaciones posteriores registrarán las acciones recomendadas que se han realizado y aumentará su calificación de cumplimiento normativo. Los asuntos que se hayan corregido aparecerán en **Objetos superados**.

## <a name="ignore-recommendations"></a>Omisión de las recomendaciones

Si desea omitir ciertas recomendaciones, puede crear un archivo de texto que OMS usará para evitar que aparezcan las recomendaciones en los resultados de la evaluación.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Para identificar las recomendaciones que desea omitir

1. Inicie sesión en su área de trabajo y abra Búsqueda de registros. Use la siguiente consulta para mostrar las recomendaciones para los equipos que presentan errores en el entorno.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Captura de pantalla que muestra la consulta de Búsqueda de registros:   
    ![búsqueda de registros](./media/log-analytics-scom-assessment/scom-log-search.png)

2. Elija las recomendaciones que desea omitir. Usará los valores para RecommendationId en el procedimiento siguiente.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para crear y usar un archivo de texto IgnoreRecommendations.txt

1. Cree un archivo llamado IgnoreRecommendations.txt.
2. Pegue o escriba cada RecommendationId de cada recomendación que desee que OMS omita en una línea independiente y luego guarde y cierre el archivo.
3. Coloque el archivo en la carpeta siguiente en cada equipo donde desea que OMS omita las recomendaciones.
4. En el servidor de administración de Operations Manager: *UnidadDelSistema*:\Archivos de programa\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Para comprobar que se omiten las recomendaciones

1. Después de que se ejecute la siguiente evaluación programada, de forma predeterminada cada siete días, las recomendaciones especificadas se marcan como omitidas y no aparecerán en el panel de evaluación.
2. Puede usar las consultas de búsqueda de registros siguientes para enumerar todas las recomendaciones omitidas.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

3. Si posteriormente decide que desea ver las recomendaciones omitidas, quite todos los archivos IgnoreRecommendations.txt. También puede quitar RecommendationID de ellos.

## <a name="system-center-operations-manager-assessment-solution-faq"></a>Preguntas más frecuentes sobre la solución Evaluación de System Center Operations Manager

*He agregado la solución de evaluación agrega a mi área de trabajo de OMS. Pero no veo las recomendaciones. ¿Por qué no?* Después de agregar la solución, utilice los pasos siguiente para ver las recomendaciones del panel de OMS.  

- [Establecimiento de la cuenta de ejecución de System Center Operations Manager](#operations-manager-run-as-accounts-for-oms)  
- [Configuración de la regla de System Center Operations Manager](#configure-the-assessment-rule)


*¿Se puede configurar la frecuencia con la que se realiza la evaluación?* Sí. Consulte [Configuración de la frecuencia de ejecución](#configure-the-run-frequency).

*Si se detecta otro servidor después de haber agregado la solución Evaluación de System Center Operations Manager, ¿se evaluará?* Sí, después de la detección, se evaluará cada siete días de forma predeterminada.

*¿Cuál es el nombre del proceso que realiza la recopilación de datos?* AdvisorAssessment.exe

*¿Dónde se ejecuta el proceso AdvisorAssessment.exe?* AdvisorAssessment.exe se ejecuta en el servicio de mantenimiento del servidor de administración donde está habilitada la regla de evaluación. Con ese proceso, la detección de todo el entorno se realiza mediante la recopilación de datos remotos.

*¿Cuánto tiempo tarda en recopilar datos?* La recopilación de datos en el servidor tarda aproximadamente una hora. Puede tardar más en entornos que tienen muchas instancias o bases de datos de Operations Manager.

*¿Qué ocurre si establezco el intervalo de la evaluación en menos de 1440 minutos?* La evaluación está preconfigurada para que se ejecute un máximo de una vez al día. Si invalida el valor de intervalo en un valor inferior a 1440 minutos, la evaluación usa 1440 minutos como valor de intervalo.

*¿Cómo se sabe si se cumplen los requisitos previos?* Si la evaluación se ejecutó y no ve los resultados, es probable que no cumpla algunos de los requisitos previos de la evaluación. Puede ejecutar consultas: `Type=Operation Solution=SCOMAssessment` y `Type=SCOMAssessmentRecommendation FocusArea=Prerequisites` en la búsqueda de registros para ver qué requisitos previos no se cumplen.

*Los requisitos previos que no se cumplen muestran el mensaje `Failed to connect to the SQL Instance (….).`. ¿Cuál es el problema?* AdvisorAssessment.exe el proceso que recopila los datos, se ejecuta en el servicio de mantenimiento del servidor de administración. Como parte de la evaluación, el proceso intenta conectarse a la instancia de SQL Server donde se encuentra la base de datos de Operations Manager. Este error puede producirse cuando las reglas de firewall bloquean la conexión a la instancia de SQL Server.

*¿Qué tipo de datos se recopila?* Se recopilan los siguientes tipos de datos: datos de WMI, datos del Registro, datos del registro de eventos, datos de Operations Manager mediante Windows PowerShell, consultas SQL y el recopilador de información de consultas y archivos.

*¿Por qué es necesario configurar una cuenta "ejecutar como"?* Para un servidor de Operations Manager, se ejecutan varias consultas SQL. Para que se ejecuten, debe usar una cuenta de ejecución con los permisos necesarios. Además, para consultar WMI, se necesitan credenciales de administrador local.

*¿Por qué se muestran solo las 10 recomendaciones principales?* En lugar de darle una lista exhaustiva y abrumadora de tareas, se recomienda centrarse primero en las recomendaciones prioritarias. Después de aplicarlas, se mostrarán más recomendaciones. Si prefiere ver una lista detallada, puede ver todas las recomendaciones mediante Búsqueda de registros.

*¿Se puede hacer caso omiso de una recomendación?* Sí, consulte [Omisión de las recomendaciones](#Ignore-recommendations).


## <a name="next-steps"></a>Pasos siguientes

- [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para más información sobre cómo ver recomendaciones y datos de Evaluación de System Center Operations Manager Assessment.
