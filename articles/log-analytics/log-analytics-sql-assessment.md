---
title: "Optimización del entorno de SQL Server con Azure Log Analytics | Microsoft Docs"
description: "Con Azure Log Analytics, puede usar periódicamente la solución de evaluación de SQL para evaluar el riesgo y el estado de los entornos de servidor SQL Server."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d2aed3315fe60ace46dfb4176dc13aa417257b0c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="optimize-your-sql-server-environment-with-the-sql-assessment-solution-in-log-analytics"></a>Optimización del entorno de SQL Server con la solución de evaluación de SQL en Log Analytics

![Símbolo de SQL Assessment](./media/log-analytics-sql-assessment/sql-assessment-symbol.png)

Puede usar periódicamente la solución de evaluación de SQL para evaluar el riesgo y el estado de los entornos de servidor. Este artículo le ayudará a instalar la solución para que puede tomar acciones correctivas para posibles problemas.

Esta solución proporciona una lista priorizada de recomendaciones específicas para su infraestructura de servidor implementada. Las recomendaciones se clasifican en seis áreas de enfoque que ayudan a comprender rápidamente el riesgo y a tomar la acción correctiva.

Las recomendaciones efectuadas se basan en los conocimientos y la experiencia adquiridos por los ingenieros de Microsoft fruto de miles de visitas de clientes. En cada recomendación, se explica por qué podría ser importante para usted un problema y se proporcionan instrucciones sobre cómo implementar los cambios sugeridos.

Puede elegir las áreas de enfoque que sean más importantes para su organización y realizar un seguimiento del progreso hacia la consecución de un entorno libre de riesgos y en buen estado.

Después de haber agregado la solución y completar una evaluación, se muestra información resumida sobre las áreas de enfoque en el panel **Evaluación de SQL** para la infraestructura del entorno. Las secciones siguientes describen cómo usar la información que aparece en el panel **Evaluación de SQL** , donde puede ver y ejecutar las acciones recomendadas para su infraestructura de servidor.

![imagen del icono de evaluación de SQL](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![imagen del panel de evaluación de SQL](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## <a name="installing-and-configuring-the-solution"></a>Instalación y configuración de la solución
La evaluación de SQL funciona con todas las versiones compatibles actualmente de SQL Server para las ediciones Standard, Developer y Enterprise.

Utilice la siguiente información para instalar y configurar la solución.

* Deben instalarse agentes en servidores con SQL Server instalado.
* La solución de evaluación de SQL requiere que esté instalado una versión compatible de .NET Framework 4 en cada equipo que tenga un agente de OMS.
* Para instalar la solución, el usuario debe ser administrador o colaborador en la suscripción de Azure cuando utiliza Azure Portal. Además, el usuario debe ser miembro del rol de administrador o colaborador del área de trabajo de OMS en el portal de OMS.
* Cuando use el agente de Operations Manager con la evaluación de SQL, debe utilizar una cuenta de ejecución de Operations Manager. Para más información, consulte [Cuentas de ejecución de Operations Manager para OMS](#operations-manager-run-as-accounts-for-oms) más adelante.

  > [!NOTE]
  > El agente de MMA no admite cuentas de ejecución de Operations Manager.
  >
  >
* Agregue la solución de evaluación de SQL al área de trabajo de OMS mediante el proceso descrito en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md). No es necesario realizar ninguna configuración más.

> [!NOTE]
> Después de agregar la solución, el archivo AdvisorAssessment.exe se agrega a servidores con agentes. Los datos de configuración se leen y, luego, se envían al servicio de OMS en la nube para su procesamiento. Se aplica la lógica a los datos recibidos y el servicio de nube registra los datos.

## <a name="sql-assessment-data-collection-details"></a>Detalles de la recopilación de datos de la evaluación de SQL
La evaluación de SQL recopila datos WMI, datos del registro, datos de rendimiento y resultados de la vista de administración dinámica de SQL Server con los agentes habilitados.

En la siguiente tabla se muestran los métodos de recopilación de datos para agentes, si se necesita Operations Manager (SCOM) y la frecuencia con la que un agente recopila datos.

| plataforma | Agente directo | Agente de SCOM | Azure Storage | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  | &#8226; |7 días |

## <a name="operations-manager-run-as-accounts-for-oms"></a>Cuentas de ejecución de Operations Manager para OMS
Log Analytics en OMS usa el agente y el grupo de administración de Operations Manager para recopilar y enviar datos al servicio de OMS. OMS se basa en paquetes de administración de cargas de trabajo para proporcionar servicios de valor añadido. Cada carga de trabajo requiere privilegios específicos de carga de trabajo para ejecutar paquetes de administración en un contexto de seguridad diferente, como una cuenta de dominio. Debe proporcionar información de credenciales mediante la configuración de una cuenta de ejecución de Operations Manager.

Utilice la siguiente información para establecer la cuenta de ejecución de Operations Manager para la evaluación de SQL.

### <a name="set-the-run-as-account-for-sql-assessment"></a>Definición de la cuenta de ejecución para la evaluación de SQL
 Si ya está usando el paquete de administración de SQL Server, debe utilizar esa cuenta de ejecución.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Para configurar la cuenta de ejecución de SQL en la consola de Operations, siga estos pasos:
> [!NOTE]
> Si usa el agente directo de OMS en lugar del agente de SCOM, el módulo de administración siempre se ejecuta en el contexto de seguridad de la cuenta de sistema local. Omita los pasos 1 a 5 siguientes y ejecute el código T-SQL o Powershell de ejemplo, especificando NT AUTHORITY\SYSTEM como nombre de usuario.
>
>

1. En Operations Manager, abra la Consola del operador y haga clic en **Administración**.
2. En **Run As Configuration** (Configuración de ejecución), haga clic en **Perfiles** y abra **OMS SQL Assessment Run As Profile** (Perfil de ejecución de evaluación de SQL para OMS).
3. En la página **Cuentas de ejecución**, haga clic en **Agregar**.
4. Seleccione una cuenta de ejecución de Windows que contenga las credenciales necesarias para SQL Server o haga clic en **Nuevo** para crear una.

   > [!NOTE]
   > El tipo de cuenta de ejecución debe ser Windows. La cuenta de ejecución también debe ser parte del grupo de administradores locales en todos los servidores de Windows que hospedan instancias de SQL Server.
   >
   >
5. Haga clic en **Guardar**.
6. Modifique y luego ejecute el siguiente ejemplo de T-SQL en cada instancia de SQL Server para conceder los permisos mínimos que necesita la cuenta de ejecución para realizar la evaluación de SQL. Sin embargo, este paso no es necesario si una cuenta de ejecución ya forma parte del rol de servidor sysadmin en las instancias de SQL Server.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Para configurar la cuenta de ejecución de SQL mediante Windows PowerShell, siga estos pasos:
Abra una ventana de PowerShell y ejecute el siguiente script después de actualizarlo con su información:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Cómo se establecen prioridades entre las recomendaciones
A cada recomendación efectuada se le asigna un valor de ponderación que identifica su importancia relativa. Se muestran solo las diez recomendaciones más importantes.

### <a name="how-weights-are-calculated"></a>Cálculo de las ponderaciones
Las ponderaciones son valores agregados en función de tres factores principales:

* La *probabilidad* de que un asunto identificado pueda causar problemas. Una probabilidad más alta equivale a una puntuación total mayor para la recomendación.
* El *impacto* del asunto en su organización en caso de que se produzca un problema. Un mayor impacto equivale a una puntuación total mayor para la recomendación.
* El *esfuerzo* necesario para implementar la recomendación. Un mayor esfuerzo equivale a una puntuación total menor para la recomendación.

La ponderación de cada recomendación se expresa como un porcentaje de la puntuación total disponible para cada área de enfoque. Por ejemplo, si una recomendación en el área de enfoque de seguridad y cumplimiento tiene una puntuación del 5 %, la implementación de esa recomendación aumentará la puntuación total de seguridad y cumplimiento en un 5 %.

### <a name="focus-areas"></a>Áreas de enfoque
**Seguridad y cumplimiento** : este apartado muestra recomendaciones en caso de posibles amenazas e infracciones de seguridad, directivas corporativas y requisitos de cumplimiento técnico, legal y reglamentario.

**Disponibilidad y continuidad empresarial** : este apartado muestra recomendaciones relacionadas con la disponibilidad de servicio, la resistencia de la infraestructura y la protección del negocio.

**Rendimiento y escalabilidad** : este apartado muestra recomendaciones que ayudarán a crecer a la infraestructura de TI de su organización y garantizarán que el entorno de TI cumple los requisitos de rendimiento vigentes y que, además, puede responder a las cambiantes necesidades de infraestructura.

**Actualización, migración e implementación**. Esta área de enfoque muestra recomendaciones que lo ayudarán a actualizar, migrar e implementar SQL Server en la infraestructura existente.

**Operaciones y supervisión**. Esta área de enfoque muestra recomendaciones para ayudar a simplificar las operaciones de TI, implementar el mantenimiento preventivo y maximizar el rendimiento.

**Administración de cambios y configuración**. Esta área de enfoque muestra recomendaciones para ayudar a proteger las operaciones diarias, garantizar que los cambios no afectan de manera negativa a su infraestructura, establecer procedimientos de control de cambios y realizar un seguimiento y auditar las configuraciones del sistema.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>¿Debe tratar de conseguir una puntuación del 100 % en cada área de enfoque?
No necesariamente. Las recomendaciones se basan en los conocimientos y las experiencias adquiridos por los ingenieros de Microsoft producto de miles de visitas de clientes. Sin embargo, no hay dos infraestructuras de servidores que sean iguales, y es posible que determinadas recomendaciones puedan ser más o menos relevantes para usted. Por ejemplo, algunas recomendaciones de seguridad pueden ser menos pertinentes si las máquinas virtuales no están expuestas a Internet. Algunas recomendaciones de disponibilidad pueden ser menos relevantes para los servicios que proporcionan informes y recopilaciones de datos ad hoc de baja prioridad. Los problemas que son importantes para un negocio maduro pueden no serlo para otro que esté en sus inicios. Puede que desee identificar qué áreas de enfoque son prioritarias para usted y, posteriormente, observar cómo cambian las puntuaciones con el tiempo.

Cada recomendación incluye pautas que indican por qué es importante. Debe utilizar estas directrices para evaluar si es adecuado o no para usted implementar la recomendación, en función de la naturaleza de los servicios de TI y las necesidades empresariales de su organización.

## <a name="use-assessment-focus-area-recommendations"></a>Uso de las recomendaciones de área de enfoque de evaluación
Antes de que pueda usar una solución de evaluación en OMS, debe tener instalada la solución. Para más información sobre cómo instalar las soluciones, consulte [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md). Una vez realizada la instalación, podrá ver el resumen de las recomendaciones mediante el icono de evaluación de SQL en la página Información general de OMS.

Consulte un resumen de las evaluaciones de cumplimiento para su infraestructura y, a continuación, profundice las recomendaciones.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Visualización de las recomendaciones para un área de enfoque y adopción de las medidas correctivas
1. En la página **Información general**, haga clic en el icono de **evaluación de SQL**.
2. En la página de **evaluación de SQL**, revise la información de resumen de una de las hojas de las áreas de enfoque y, a continuación, haga clic en una de ellas para ver las recomendaciones para dicha área de enfoque.
3. En cualquiera de las páginas de área de enfoque, puede ver las recomendaciones priorizadas que se han efectuado para su entorno. Haga clic en una recomendación en **Objetos afectados** para ver los detalles sobre por qué se realiza la recomendación.  
    ![imagen de las recomendaciones de evaluación de SQL](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. Puede tomar las medidas correctivas que se sugieren en **Acciones sugeridas**. Cuando se haya ocupado del asunto, las evaluaciones posteriores registrarán las acciones recomendadas que se han realizado y aumentará su calificación de cumplimiento normativo. Los asuntos que se hayan corregido aparecerán en **Objetos superados**.

## <a name="ignore-recommendations"></a>Omisión de las recomendaciones
Si desea omitir ciertas recomendaciones, puede crear un archivo de texto que OMS usará para evitar que aparezcan recomendaciones en los resultados de la evaluación.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar las recomendaciones que omitirá
1. Inicie sesión en su área de trabajo y abra Búsqueda de registros. Use la siguiente consulta para mostrar las recomendaciones para los equipos que presentan errores en el entorno.

   ```
   Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```

   Captura de pantalla que muestra la consulta de Búsqueda de registros: ![recomendaciones fallidas](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)
2. Elija las recomendaciones que desea omitir. Usará los valores para RecommendationId en el procedimiento siguiente.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para crear y usar un archivo de texto IgnoreRecommendations.txt
1. Cree un archivo llamado IgnoreRecommendations.txt.
2. Pegue o escriba cada RecommendationId de cada recomendación que desee que OMS omita en una línea independiente y luego guarde y cierre el archivo.
3. Coloque el archivo en la carpeta siguiente en cada equipo donde desea que OMS omita las recomendaciones.
   * En equipos con Microsoft Monitoring Agent (conectado directamente o a través de Operations Manager): *UnidadDelSistema*:\Archivos de programa\Microsoft Monitoring Agent\Agent
   * En el servidor de administración de Operations Manager: *UnidadDelSistema*:\Archivos de programa\Microsoft System Center 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para comprobar que se omiten las recomendaciones
1. Después de que se ejecute la siguiente evaluación programada, de forma predeterminada cada 7 días, las recomendaciones especificadas se marcan como omitidas y no aparecerán en el panel de evaluación.
2. Puede usar las consultas de búsqueda de registros siguientes para enumerar todas las recomendaciones omitidas.

   ```
   Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```
3. Si posteriormente decide que desea ver las recomendaciones omitidas, quite todos los archivos IgnoreRecommendations.txt. También puede quitar RecommendationID de ellos.

## <a name="sql-assessment-solution-faq"></a>Preguntas más frecuentes sobre soluciones de evaluación de SQL
*¿Con qué frecuencia se ejecuta una evaluación?*

* La evaluación se realiza cada 7 días.

*¿Se puede configurar la frecuencia con la que se realiza la evaluación?*

* De momento, no.

*Si se detecta otro servidor después de haber agregado una solución de evaluación de SQL, ¿se evaluará?*

* Sí, una vez que se detecte, se evaluará cada 7 días a partir de entonces.

*Si se retira un servidor, ¿cuándo dejará de incluirse en la evaluación?*

* Si un servidor no envía datos durante 3 semanas, se quitará.

*¿Cuál es el nombre del proceso que realiza la recopilación de datos?*

* AdvisorAssessment.exe

*¿Cuánto tiempo tarda en recopilar datos?*

* La recopilación de datos reales en el servidor tarda aproximadamente 1 hora. Puede tardar más en servidores que tengan un gran número de bases de datos o instancias de SQL.

*¿Qué tipo de datos se recopila?*

* Se recopilan los siguientes tipos de datos:
  * WMI
  * Registro
  * Contadores de rendimiento
  * Vistas de administración dinámica (DMV) de SQL

*¿Se puede configurar el momento en que se recopilan los datos?*

* De momento, no.

*¿Por qué es necesario configurar una cuenta "ejecutar como"?*

* Para SQL Server, se ejecuta un número reducido de consultas SQL. Para que se ejecuten, hay que utilizar una cuenta "ejecutar como" que disponga de los permisos VIEW SERVER STATE para SQL.  Además, para consultar WMI, se necesitan credenciales de administrador local.

*¿Por qué se muestran solo las 10 recomendaciones principales?*

* En lugar de darle una lista exhaustiva y abrumadora de tareas, se recomienda centrarse primero en las recomendaciones prioritarias. Después de aplicarlas, se mostrarán más recomendaciones. Si prefiere ver una lista detallada, puede ver todas las recomendaciones mediante la búsqueda de registros de OMS.

*¿Se puede hacer caso omiso de una recomendación?*

* Sí, consulte la sección [Omisión de las recomendaciones](#ignore-recommendations) anterior.

## <a name="next-steps"></a>Pasos siguientes
* [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para más información sobre cómo ver recomendaciones y datos de evaluación de SQL detallados.
