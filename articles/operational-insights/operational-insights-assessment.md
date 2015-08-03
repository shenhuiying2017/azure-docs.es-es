<properties
   pageTitle="Optimización del entorno con soluciones de evaluación"
   description="Use periódicamente soluciones de evaluación para evaluar el riesgo y el mantenimiento de los entornos de servidor."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2015"
   ms.author="banders" />

# Optimización del entorno con soluciones de evaluación

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Puede usar periódicamente soluciones de evaluación para evaluar el riesgo y el estado de los entornos de servidor. Estos proporcionan una lista priorizada de recomendaciones específicas para su infraestructura de servidor implementada. Las recomendaciones se clasifican en seis áreas de enfoque que permiten entender rápidamente el riesgo y el estado de su infraestructura y facilitan la adopción de medidas para reducir el riesgo y mejorar el estado.

Actualmente, están disponibles las soluciones de evaluación siguientes:

- Active Directory

- SQL Server

Las recomendaciones efectuadas se basan en los conocimientos y las experiencias adquiridas por los ingenieros de Microsoft a partir de miles de visitas de clientes. En cada recomendación, se explica por qué podría ser importante para usted cada problema y se proporcionan instrucciones sobre cómo implementar los cambios sugeridos.

Puede elegir las áreas de enfoque que sean más importantes para su organización y realizar un seguimiento del progreso hacia la consecución de un entorno libre de riesgos y en buen estado.

Instale las soluciones para actualizar el agente Operations Manager y el módulo de configuración básica para Visión operativa. Posteriormente, agregue el archivo AdvisorAssessment.exe a los servidores supervisados. Los datos de configuración se leen y se envían al servicio de Visión operativa en la nube para su procesamiento. Se aplica la lógica a los datos recibidos y el servicio de nube registra los datos. Cuando finalizan las evaluaciones, se muestra información resumida sobre las áreas de enfoque en el panel **Evaluación** para la infraestructura de su entorno. Con la información que aparece en el panel **Evaluación**, puede ver y ejecutar las acciones recomendadas para su infraestructura de servidor.

![imagen del icono de evaluación de SQL](./media/operational-insights-assessment/overview-sql-assess.png)

![imagen del panel de evaluación de SQL](./media/operational-insights-assessment/gallery-ad-01.png)


## Preguntas más frecuentes sobre las soluciones de evaluación

*¿Con qué frecuencia se ejecuta una evaluación?*<br> La evaluación se realiza cada 7 días.

*¿Se puede configurar la frecuencia con la que se realiza la evaluación?*<br> De momento, no.

*Si se detecta otro servidor después de haber agregado una solución de evaluación, ¿se evaluará?*<br> Sí, una vez que se detecte, se evaluará cada 7 días a partir de entonces.

*Si se retira un servidor, ¿cuándo dejará de incluirse en la evaluación?*<br> Si un servidor no envía datos durante 3 semanas, se quitará.

*¿Cuál es el nombre del proceso que realiza la recopilación de datos?*<br> AdvisorAssessment.exe

*¿Cuánto tiempo se tarda en recopilar datos?*<br> La recopilación de datos reales en el servidor tarda aproximadamente 1 hora. Puede tardar más en servidores que tengan un gran número de servidores de Active Directory, instancias de SQL o bases de datos.

*¿Qué tipo de datos se recopilan?*<br> Se recopilan los siguientes tipos de datos:

- WMI
- Registro
- Contadores de rendimiento
- Vistas de administración dinámica (DMV) de SQL

*¿Se puede configurar el momento en que se recopilan los datos?*<br> De momento, no.

*¿Por qué es necesario configurar una cuenta de ejecución?*<br> Para SQL Server, se ejecuta un número reducido de consultas SQL. Para que se ejecuten, hay que utilizar una cuenta "ejecutar como" que disponga de los permisos VIEW SERVER STATE para SQL. Además, para consultar WMI, se necesitan credenciales de administrador local.

*¿Por qué se muestran solo las 10 recomendaciones principales?*<br> En lugar de darle una lista exhaustiva y abrumadora de tareas, se recomienda centrarse primero en las recomendaciones prioritarias. Después de aplicarlas, se mostrarán más recomendaciones. Si prefiere ver una lista detallada, puede ver todas las recomendaciones mediante la búsqueda de registros de Visión operativa.

*¿Se puede hacer caso omiso de una recomendación?*<br> De momento, no.

## Cómo se establecen prioridades entre las recomendaciones

A cada recomendación efectuada se le asigna un valor de ponderación que identifica su importancia relativa. Se muestran solo las diez recomendaciones más importantes.

### Cómo se calculan las ponderaciones

Las ponderaciones son valores agregados en función de tres factores principales:

- La *probabilidad* de que un asunto identificado pueda causar problemas. Una probabilidad más alta equivale a una puntuación total mayor para la recomendación.

- El *impacto* del asunto en su organización en caso de que se produzca un problema. Un mayor impacto equivale a una puntuación total mayor para la recomendación.

- El *esfuerzo* necesario para implementar la recomendación. Un mayor esfuerzo equivale a una puntuación total menor para la recomendación.

La ponderación de cada recomendación se expresa como un porcentaje de la puntuación total disponible para cada área de enfoque. Por ejemplo, si una recomendación en el área de enfoque de seguridad y cumplimiento tiene una puntuación del 5 %, la implementación de esa recomendación aumentará la puntuación total de seguridad y cumplimiento en un 5 %.

### Áreas de enfoque

**Seguridad y cumplimiento**: proteja la reputación de su organización defendiéndose frente a amenazas de seguridad y las infracciones, aplique las directivas corporativas y cumpla con los requisitos normativos, legales y técnicos.

**Disponibilidad y continuidad del negocio**: mantenga la disponibilidad de los servicios y la rentabilidad de su negocio garantizando la resistencia de la infraestructura y manteniendo el nivel adecuado de protección del negocio en caso de desastre.

**Rendimiento y escalabilidad**: ayude a su organización a crecer e innovar asegurándose de que su entorno de TI pueda cumplir los requisitos de rendimiento actuales, así como responder rápidamente ante las cambiantes necesidades empresariales.

**Actualización, migración e implementación**: dote a su departamento de TI de todo lo necesario para que sea el impulsor clave del cambio y la innovación. Para ello, aproveche al máximo las nuevas tecnologías de habilitación a fin de obtener más valor empresarial para las unidades organizativas, los empleados y los clientes.

**Operaciones y supervisión**: reduzca el presupuesto de mantenimiento de TI mediante la optimización de sus operaciones de TI y la implementación de un programa de mantenimiento preventivo completo para maximizar el rendimiento empresarial.

**Administración de cambios y configuraciones**: proteja las operaciones diarias de su organización y asegúrese de que los cambios no afecten negativamente al negocio mediante el establecimiento de procedimientos de control de cambios y la realización de seguimientos y auditorías de las configuraciones del sistema.

### ¿Debe tratar de conseguir una puntuación del 100 % en cada área de enfoque?

No necesariamente. Las recomendaciones efectuadas se basan en los conocimientos y las experiencias adquiridas por los ingenieros de Microsoft fruto de miles de visitas de clientes. Sin embargo, no hay dos infraestructuras de servidores que sean iguales, y es posible que determinadas recomendaciones puedan ser más o menos relevantes para usted. Por ejemplo, algunas recomendaciones de seguridad pueden ser menos relevantes si las máquinas virtuales no están expuestas a Internet. Algunas recomendaciones de disponibilidad pueden ser menos relevantes para los servicios que proporcionan informes y recopilaciones de datos ad hoc de baja prioridad. Los problemas que son importantes para un negocio maduro pueden no serlo para otro que esté en sus inicios. Puede que desee identificar qué áreas de enfoque son prioritarias para usted y, posteriormente, observar cómo cambian las puntuaciones con el tiempo.

Cada recomendación efectuada incluye información acerca de por qué podría ser importante. Debe utilizar estas directrices para evaluar si es adecuado o no para usted implementar la recomendación, en función de la naturaleza de los servicios de TI y las necesidades empresariales de su organización.

## Uso de las recomendaciones de área de enfoque de evaluación

Para poder utilizar una solución de evaluación en Visión operativa de Microsoft Azure, debe tener la solución instalada. Para obtener más información sobre cómo instalar soluciones, vea [Uso de la Galería de soluciones para agregar o quitar soluciones](operational-insights-add-solution.md). Una vez instalados, puede ver el resumen de las recomendaciones mediante el icono Evaluación en el panel Información general de Visión operativa.

Puede resumir las evaluaciones de cumplimiento para su infraestructura y, a continuación, profundizar en las recomendaciones.

![imagen de las recomendaciones de evaluación de SQL](./media/operational-insights-assessment/gallery-ad-03.png)



### Visualización de las recomendaciones para un área de enfoque y adopción de las medidas correctivas

1. En la página **Información general**, haga clic en el icono **Evaluación** correspondiente a su infraestructura de servidor.

2. En la página **Evaluación**, revise la información de resumen de una de las hojas de las áreas de enfoque y, a continuación, haga clic en una de ellas para ver las recomendaciones para dicha área de enfoque.

3. En cualquiera de las páginas de área de enfoque, puede ver las recomendaciones priorizadas que se han efectuado para su entorno. Haga clic en una recomendación para ver sus detalles sobre por qué se realiza la recomendación, y aparecerá en **Objetos afectados**.

4. Adopte las medidas correctivas sugeridas en **Acciones sugeridas**. Cuando se haya ocupado del asunto, las evaluaciones posteriores registrarán las acciones recomendadas que se han realizado y aumentará su calificación de cumplimiento normativo. Los asuntos que se hayan corregido aparecerán en **Objetos superados**.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=July15_HO4-->