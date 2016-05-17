<properties
	pageTitle="Optimización del entorno con la solución de evaluación de SQL en Log Analytics | Microsoft Azure"
	description="Puede usar periódicamente la solución de evaluación de SQL para evaluar el riesgo y el estado de los entornos de servidor."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Optimización del entorno con la solución de evaluación de SQL en Log Analytics


Puede usar periódicamente la solución de evaluación de SQL para evaluar el riesgo y el estado de los entornos de servidor. Este artículo le ayudará a instalar la solución para que puede tomar acciones correctivas para posibles problemas.

Esta solución proporciona una lista priorizada de recomendaciones específicas para su infraestructura de servidor implementada. Las recomendaciones se clasifican en seis áreas de enfoque que ayudan a comprender rápidamente el riesgo y a tomar la acción correctiva.

Las recomendaciones efectuadas se basan en los conocimientos y la experiencia adquiridos por los ingenieros de Microsoft fruto de miles de visitas de clientes. En cada recomendación, se explica por qué podría ser importante para usted un problema y se proporcionan instrucciones sobre cómo implementar los cambios sugeridos.

Puede elegir las áreas de enfoque que sean más importantes para su organización y realizar un seguimiento del progreso hacia la consecución de un entorno libre de riesgos y en buen estado.

Después de haber agregado la solución y completar una evaluación, se muestra información resumida sobre las áreas de enfoque en el panel **Evaluación de SQL** para la infraestructura del entorno. Las secciones siguientes describen cómo usar la información que aparece en el panel **Evaluación de SQL **, donde puede ver y ejecutar las acciones recomendadas para su infraestructura de servidor.

![imagen del icono de evaluación de SQL](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![imagen del panel de evaluación de SQL](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## Instalación y configuración de la solución
La evaluación de SQL funciona con todas las versiones compatibles actualmente de SQL Server para las ediciones Standard, Developer y Enterprise.

Utilice la siguiente información para instalar y configurar la solución.

- La solución de evaluación de SQL requiere que .NET Framework 4 esté instalado en cada equipo que tenga un agente de OMS.
- Cuando use el agente de Operations Manager con la evaluación de SQL, debe utilizar una cuenta de ejecución de Operations Manager. Consulte [Cuentas de ejecución de Operations Manager para OMS](#operations-manager-run-as-accounts-for-oms) más adelante para obtener más información.

    >[AZURE.NOTE] El agente de MMA no admite cuentas de ejecución de Operations Manager.

- Agregue la solución de evaluación de SQL al área de trabajo de OMS mediante el proceso descrito en [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Incorporación de soluciones de Log Analytics desde la galería de soluciones). No es necesario realizar ninguna configuración más.

>[AZURE.NOTE] Después de agregar la solución, el archivo AdvisorAssessment.exe se agrega a servidores con agentes. Los datos de configuración se leen y, luego, se envían al servicio de OMS en la nube para su procesamiento. Se aplica la lógica a los datos recibidos y el servicio de nube registra los datos.

## Detalles de la recopilación de datos de la evaluación de SQL

En la siguiente tabla se muestran los métodos de recopilación de datos para agentes, si se necesita Operations Manager (SCOM) y la frecuencia con la que un agente recopila datos.

| plataforma | Agente directo | Agente de SCOM | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
|---|---|---|---|---|---|---|
|Windows|![Sí](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Sí](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![No](./media/log-analytics-sql-assessment/oms-bullet-red.png)|	![No](./media/log-analytics-sql-assessment/oms-bullet-red.png)|![Sí](./media/log-analytics-sql-assessment/oms-bullet-green.png)|	7 días|

## Cuentas de ejecución de Operations Manager para OMS

Log Analytics en OMS usa el agente y el grupo de administración de Operations Manager para recopilar y enviar datos al servicio de OMS. OMS se basa en paquetes de administración de cargas de trabajo para proporcionar servicios de valor añadido. Cada carga de trabajo requiere privilegios específicos de carga de trabajo para ejecutar paquetes de administración en un contexto de seguridad diferente, como una cuenta de dominio. Debe proporcionar información de credenciales mediante la configuración de una cuenta de ejecución de Operations Manager.

Utilice la siguiente información para establecer la cuenta de ejecución de Operations Manager para la evaluación de SQL.

### Definición de la cuenta de ejecución para la evaluación de SQL

 Si ya está usando el paquete de administración de SQL Server, debe utilizar esa cuenta de ejecución.

#### Para configurar la cuenta de ejecución de SQL en la consola de Operations, siga estos pasos:

1. En Operations Manager, abra la Consola del operador y haga clic en **Administración**.

2. En **Run As Configuration** (Configuración de ejecución), haga clic en **Perfiles** y abra **OMS SQL Assessment Run As Profile** (Perfil de ejecución de evaluación de SQL para OMS).

3. En la página **Cuentas de ejecución**, haga clic en **Agregar**.

4. Seleccione una cuenta de ejecución de Windows que contenga las credenciales necesarias para SQL Server o haga clic en **Nuevo** para crear una.
	>[AZURE.NOTE] El tipo de cuenta de ejecución debe ser Windows. La cuenta de ejecución también debe ser parte del grupo de administradores locales en todos los servidores de Windows que hospedan instancias de SQL Server.

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
#### To configure the SQL Run As account using Windows PowerShell

Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## Understanding how recommendations are prioritized

Every recommendation made is given a weighting value that identifies the relative importance of the recommendation. Only the ten most important recommendations are shown.

### How weights are calculated

Weightings are aggregate values based on three key factors:

- The *probability* that an issue identified will cause problems. A higher probability equates to a larger overall score for the recommendation.

- The *impact* of the issue on your organization if it does cause a problem. A higher impact equates to a larger overall score for the recommendation.

- The *effort* required to implement the recommendation. A higher effort equates to a smaller overall score for the recommendation.

The weighting for each recommendation is expressed as a percentage of the total score available for each focus area. For example, if a recommendation in the Security and Compliance focus area has a score of 5%, implementing that recommendation will increase your overall Security and Compliance score by 5%.

### Focus areas

**Security and Compliance** - This focus area shows recommendations for potential security threats and breaches, corporate policies, and technical, legal and regulatory compliance requirements.

**Availability and Business Continuity** - This focus area shows recommendations for service availability, resiliency of your infrastructure, and business protection.

**Performance and Scalability** - This focus area shows recommendations to help your organization's IT infrastructure grow, ensure that your IT environment meets current performance requirements, and is able to respond to changing infrastructure needs.

**Upgrade, Migration and Deployment** - This focus area shows recommendations to help you upgrade, migrate, and deploy SQL Server to your existing infrastructure.

**Operations and Monitoring** - This focus area shows recommendations to help streamline your IT operations, implement preventative maintenance, and maximize performance.

**Change and Configuration Management** - This focus area shows recommendations to help protect day-to-day operations, ensure that changes don't negatively affect your infrastructure, establish change control procedures, and to track and audit system configurations.

### Should you aim to score 100% in every focus area?

Not necessarily. The recommendations are based on the knowledge and experiences gained by Microsoft engineers across thousands of customer visits. However, no two server infrastructures are the same, and specific recommendations may be more or less relevant to you. For example, some security recommendations might be less relevant if your virtual machines are not exposed to the Internet. Some availability recommendations may be less relevant for services that provide low priority ad hoc data collection and reporting. Issues that are important to a mature business may be less important to a start-up. You may want to identify which focus areas are your priorities and then look at how your scores change over time.

Every recommendation includes guidance about why it is important. You should use this guidance to evaluate whether implementing the recommendation is appropriate for you, given the nature of your IT services and the business needs of your organization.

## Use assessment focus area recommendations

Before you can use an assessment solution in OMS, you must have the solution installed. To read more about installing solutions, see [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md). After it is installed, you can view the summary of recommendations by using the SQL Assessment tile on the Overview page in OMS.

View the summarized compliance assessments for your infrastructure and then drill-into recommendations.

### To view recommendations for a focus area and take corrective action

1. On the **Overview** page, click the **SQL Assessment** tile.
2. On the **SQL Assessment** page, review the summary information in one of the focus area blades and then click one to view recommendations for that focus area.
3. On any of the focus area pages, you can view the prioritized recommendations made for your environment. Click a recommendation under **Affected Objects** to view details about why the recommendation is made.  
    ![image of SQL Assessment recommendations](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. You can take corrective actions suggested in **Suggested Actions**. When the item has been addressed, later assessments will record that recommended actions were taken and your compliance score will increase. Corrected items appear as **Passed Objects**.

## Ignore recommendations

If you have recommendations that you want to ignore, you can create a text file that OMS will use to prevent recommendations from appearing in your assessment results.

### To identify recommendations that you will ignore

1.	Sign in to your workspace and open Log Search. Use the following query to list recommendations that have failed for computers in your environment.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Here's a screen shot showing the Log Search query:
    ![failed recommendations](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)

2.	Elija las recomendaciones que desea omitir. Usará los valores para RecommendationId en el procedimiento siguiente.


### Para crear y usar un archivo de texto IgnoreRecommendations.txt

1.	Cree un archivo llamado IgnoreRecommendations.txt.
2.	Pegue o escriba cada RecommendationId de cada recomendación que desee que OMS omita en una línea independiente y luego guarde y cierre el archivo.
3.	Coloque el archivo en la carpeta siguiente en cada equipo donde desea que OMS omita las recomendaciones.
    - En equipos con Microsoft Monitoring Agent (conectado directamente o a través de Operations Manager): *UnidadDelSistema*:\\Archivos de programa\\Microsoft Monitoring Agent\\Agent
    - En el servidor de administración de Operations Manager: *UnidadDelSistema*:\\Archivos de programa\\Microsoft System Center 2012 R2\\Operations Manager\\Server

### Para comprobar que se omiten las recomendaciones

1.	Después de que se ejecute la siguiente evaluación programada, de forma predeterminada cada 7 días, las recomendaciones especificadas se marcan como omitidas y no aparecerán en el panel de evaluación.
2.	Puede usar las consultas de búsqueda de registros siguientes para enumerar todas las recomendaciones omitidas.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
3.	Si posteriormente decide que desea ver las recomendaciones omitidas, quite todos los archivos IgnoreRecommendations.txt. También puede quitar RecommendationID de ellos.

## Preguntas más frecuentes sobre soluciones de evaluación de SQL

*¿Con qué frecuencia se ejecuta una evaluación?*
- La evaluación se realiza cada 7 días.

*¿Se puede configurar la frecuencia con la que se realiza la evaluación?*
- De momento, no.

*Si se detecta otro servidor después de haber agregado una solución de evaluación de SQL, ¿se evaluará?*
- Sí, una vez que se detecte, se evaluará cada 7 días a partir de entonces.

*Si se retira un servidor, ¿cuándo dejará de incluirse en la evaluación?*
- Si un servidor no envía datos durante 3 semanas, se quitará.

*¿Cuál es el nombre del proceso que realiza la recopilación de datos?*
- AdvisorAssessment.exe

*¿Cuánto tiempo tarda en recopilar datos?*
- La recopilación de datos reales en el servidor tarda aproximadamente 1 hora. Puede tardar más en servidores que tengan un gran número de bases de datos o instancias de SQL.

*¿Qué tipo de datos se recopila?*
- Se recopilan los siguientes tipos de datos:
    - WMI
    - Registro
    - Contadores de rendimiento
    - Vistas de administración dinámica (DMV) de SQL

*¿Se puede configurar el momento en que se recopilan los datos?*
- De momento, no.

*¿Por qué es necesario configurar una cuenta "ejecutar como"?*
- Para SQL Server, se ejecuta un número reducido de consultas SQL. Para que se ejecuten, hay que utilizar una cuenta "ejecutar como" que disponga de los permisos VIEW SERVER STATE para SQL. Además, para consultar WMI, se necesitan credenciales de administrador local.

*¿Por qué se muestran solo las 10 recomendaciones principales?*
- En lugar de darle una lista exhaustiva y abrumadora de tareas, se recomienda centrarse primero en las recomendaciones prioritarias. Después de aplicarlas, se mostrarán más recomendaciones. Si prefiere ver una lista detallada, puede ver todas las recomendaciones mediante la búsqueda de registros de OMS.

*¿Se puede hacer caso omiso de una recomendación?*
- Sí, consulte la sección [Omisión de recomendaciones](#ignore-recommendations) anterior.



## Pasos siguientes

- [Busque registros](log-analytics-log-searches.md) para ver recomendaciones y datos de evaluación de SQL detallados.

<!---HONumber=AcomDC_0504_2016-->