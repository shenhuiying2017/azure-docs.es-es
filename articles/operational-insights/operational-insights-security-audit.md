<properties
   pageTitle="Explorar datos de seguridad y auditoría de Visión operativa"
   description="Obtenga información acerca de cómo puede usar la solución Seguridad y auditoría para conseguir una vista completa de la postura de seguridad de TI de su organización con consultas de búsqueda integradas para problemas importantes que requieren su atención."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# Explorar datos de seguridad y auditoría de Visión operativa

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

La solución Seguridad y auditoría de Visión operativa proporciona una vista completa de la postura de seguridad de TI de su organización con consultas de búsqueda integradas para problemas importantes que requieren su atención.

En este artículo, aprenderá lo siguiente:

- Realizar una investigación sencilla para un ejecutable sospechoso
- Comprender las prácticas recomendadas para el análisis forense
- Comprender las prácticas recomendadas para investigaciones del patrón de infracción de seguridad
- Comprender las prácticas recomendadas para escenarios de auditoría

## Realizar una investigación sencilla para un ejecutable sospechoso

1. Inicie sesión en Visión operativa.
2. En la página **Información general**, revise la información que se muestra en el mosaico **Auditoría y seguridad** y, luego, haga clic en él. ![Imagen de la página Información general](./media/operational-insights-security-audit/sec-audit-dash02.png)
3. En la página **Auditoría y seguridad**, revise la información de la hoja **PROBLEMAS IMPORTANTES**. En la imagen del ejemplo, verá seis problemas importantes para hoy y dos de ayer. En este ejemplo, hay un ejecutable sospechoso. Haga clic en **Ejecutables sospechosos** en la hoja **PROBLEMAS IMPORTANTES**. ![Imagen de la página Seguridad y auditoría](./media/operational-insights-security-audit/sec-audit-dash03.png)
4. Búsqueda muestra la consulta y los resultados de los ejecutables sospechosos en los que hizo clic. En el ejemplo, hay un resultado y se muestra el hash del archivo. Haga clic en el id. de **FILEHASH**. ![Imagen del filehash de Resultados de búsqueda](./media/operational-insights-security-audit/sec-audit-search01.png)
5. Búsqueda muestra información adicional sobre el archivo ejecutable, incluido su nombre de proceso y la ruta del archivo. Haga clic en **Procesar & lt; Nombre de archivo&gt;**. En el ejemplo, es HEXEDIT.EXE. ![Imagen del proceso de Resultados de búsqueda](./media/operational-insights-security-audit/sec-audit-search02.png)
6. Búsqueda anexa el nombre del proceso entre comillas a la consulta. "**HEXEDIT.EXE"**, en este ejemplo. ![Imagen de la Consulta de búsqueda](./media/operational-insights-security-audit/sec-audit-search03.png)
7. En el cuadro Consulta de búsqueda, elimine todo excepto el nombre del proceso y las comillas y, luego, haga clic en el icono Buscar. ![Imagen de la información de búsqueda detallada](./media/operational-insights-security-audit/sec-audit-search04.png)
8. Búsqueda muestra información detallada acerca del proceso, incluidos los equipos donde ejecutó el proceso, la cuenta de usuario que ha ejecutado el proceso, y la fecha y hora en que se creó un evento para el proceso.
9. Con la información que encuentre, puede adoptar medidas correctivas según sea necesario. Por ejemplo, si determina que el ejecutable es software malintencionado, entonces deberá tomar medidas para quitarlo de todos los sistemas informáticos a los que afecta. Una vez eliminado el archivo ejecutable y cuando Visión operativa recibe eventos de registro y auditoría actualizados de los sistemas informáticos, los valores en la hoja PROBLEMAS IMPORTANTES cambiarán al día siguiente.

## Prácticas recomendadas para el análisis forense

**Buscar pruebas**

Al realizar un análisis forense mediante la solución Seguridad y auditoría, busca las evidencias que los usuarios potencialmente malintencionados dejan atrás. Independientemente de lo que hacen los usuarios en su entorno de TI, muchas de las actividades en las que participan generan artefactos de seguridad. Tenga en cuenta que los atacantes a menudo borran los registros y suele ser el primer paso para ocultar su ruta de acceso.

Sin embargo, si los usuarios tienen acceso a sus propios equipos o tienen acceso de forma remota, la evidencia sobre el uso se almacena en los registros de eventos. Visión operativa recopila estos artefactos *en cuanto se producen*, antes de que cualquiera pueda alterarlos y le permite realizar diferentes tipos de análisis mediante la correlación de datos entre varios equipos.

**Aprender a configurar y recopilar eventos de auditoría**

Para sacar el máximo provecho a la solución Seguridad y auditoría, debe configurar el nivel de eventos de auditoría recopilados por su entorno de Windows que más se ajuste a sus necesidades, mediante los siguientes recursos.

- [Configuración de la directiva de seguridad](https://technet.microsoft.com/library/dn135243(v=ws.10).aspx)

- [Configuración avanzada de la directiva de auditoría](https://technet.microsoft.com/library/jj852202(v=ws.10).aspx)

- [Recomendaciones de la directiva de auditoría](https://technet.microsoft.com/library/dn487457.aspx)

**Habilitar AppLocker**

También debe habilitar los eventos de AppLocker para obtener información valiosa acerca de las ejecuciones de proceso que se producen en su entorno de TI. Para obtener más información, consulte [Para configurar una directiva de AppLocker solo para auditoría](https://technet.microsoft.com/library/hh994622.aspx)

**Configurar el nivel de auditoría para eventos de Windows**

El entorno informático de Windows le ofrece la capacidad de configurar el nivel de captura de registros relacionados con la seguridad. Por ejemplo, puede configurar su entorno para que cada vez que alguien tiene acceso a un archivo, lee un archivo o abre un archivo, se genere un evento. El nivel de detalle que quiere recopilar variará dependiendo de sus necesidades. Sin embargo, todas las opciones que habilita incluyen algún tipo de costo porque necesita almacenar toda la información que recopile. Por este motivo, en muchos entornos de TI de organizaciones, se decide no habilitar la recopilación de datos de escritura de objeto o de lectura del objeto. Cada vez que alguien tiene acceso a cualquier archivo, se podrían generar miles de eventos, algunos de los cuales son inútiles. El nivel de colección que elija depende de su criterio.

>[AZURE.NOTE]Si usa el agente directo y tiene un servidor proxy en su organización, debe configurarlo para permitir que el agente obtenga acceso a Visión operativa. Para obtener más información, consulte [Configurar las opciones del proxy y el firewall](operational-insights-proxy-firewall.md).

## Prácticas recomendadas para investigaciones del patrón de infracción de seguridad

**Investigar patrones de actividad anómala.**

Las infracciones de seguridad normalmente proceden de credenciales legítimas y requieren que un usuario malintencionado intente obtener privilegios elevados mediante ataques. La solución Seguridad y auditoría no se centra en la detección de intrusiones, pero ayuda a investigar y detectar patrones de actividades anómalas con Problemas importantes. Por ejemplo, debería investigar las siguientes actividades anómalas y cualquier otra que aparezca en **Problemas importantes**.

- inicios de sesión inusuales en un equipo de un usuario que no lo suele usar

- enumeración de red inusual de usuarios o equipos atípicos

- nuevas cuentas de usuarios creadas con derechos administrativos

- cambios realizados en las directivas de seguridad o registro

- archivos ejecutables sospechosos

## Prácticas recomendadas para escenarios de auditoría

Su organización podría tener reglamentos y directivas de cumplimiento de red y equipos que debe cumplir, que requieren unos registros de auditoría amplios. Por ejemplo, si su organización es una empresa de finanzas es posible que tenga que mantener registros que demuestren, en cualquier momento dado en el tiempo, que usuario realizó una operación específica en la red. También es posible que tenga que generar informes que detallen la actividad de un usuario determinado o el servidor seleccionado a petición y volver atrás varios meses o incluso años.

Puede usar la solución Seguridad y auditoría para recopilar datos de auditoría del entorno de TI, independientemente de si los equipos están en un entorno local o en la nube. Todos los datos de auditoría se almacenan, se indizan y se conservan en cada suscripción. Con una suscripción Premium a Visión operativa, se almacena una cantidad ilimitada de datos durante un año. Después, puede ver los datos de auditoría, realizar búsquedas y correlacionar diferentes tipos de datos y equipos para obtener resultados detallados para cualquier intervalo de tiempo desde que se recopilaron los datos.

**Usar Directiva de grupo para recopilar datos de auditoría**

Los datos de auditoría que quiere recopilar y enviar a Visión operativa se administran en su totalidad con la Directiva de grupo. Se usa para definir las configuraciones de seguridad como parte de un objeto de directiva de grupo (GPO) que están vinculadas a contenedores de Active Directory como sitios, dominios y unidades organizativas, y ayudar a administrar la configuración de seguridad. Una vez registrados los datos de la directiva, estos se envían a la Vista operativa.

**Usar AppLocker para recopilar datos de auditoría**

Además de la configuración de la directiva local, si usa AppLocker para recopilar datos de auditoría, Visión operativa recopilará los datos y, luego, podrá verlos.

<!---HONumber=July15_HO2-->