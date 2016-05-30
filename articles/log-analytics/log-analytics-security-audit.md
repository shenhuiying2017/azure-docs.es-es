<properties
	pageTitle="Solución Seguridad y auditoría en Log Analytics | Microsoft Azure"
	description="La solución Seguridad y auditoría de Log Analytics proporciona una vista completa de la postura de seguridad de TI de su organización con consultas de búsqueda integradas para problemas importantes que requieren su atención."
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
	ms.date="05/03/2016"
	ms.author="banders"/>

# Solución Seguridad y auditoría en Log Analytics

La solución Seguridad y auditoría de Log Analytics proporciona una vista completa de la postura de seguridad de TI de su organización con consultas de búsqueda integradas para problemas importantes que requieren su atención.

## Instalación y configuración de la solución
Utilice la siguiente información para instalar y configurar la solución.

- Agregue la solución Seguridad y auditoría al área de trabajo de OMS mediante el proceso descrito en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md). No es necesario realizar ninguna configuración más.



## Detalles de recopilación de datos de seguridad y auditoría

En la tabla siguiente se muestran los métodos de recopilación de datos y otros detalles sobre cómo se recopilan los datos para la solución Seguridad y auditoría.

|tipo de datos| plataforma | Agente directo | Agente de SCOM | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
|---|---|---|---|---|---|---|---|
|Registros de eventos de seguridad de Windows|Windows|![Sí](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sí](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sí](./media/log-analytics-security-audit/oms-bullet-green.png)| ![No](./media/log-analytics-security-audit/oms-bullet-red.png)|![No](./media/log-analytics-security-audit/oms-bullet-red.png)| Para Almacenamiento de Azure: 10 minutos; para el agente: a la llegada|
|Registros de Firewall de Windows|Windows|![Sí](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sí](./media/log-analytics-security-audit/oms-bullet-green.png)|![No](./media/log-analytics-security-audit/oms-bullet-red.png)| ![No](./media/log-analytics-security-audit/oms-bullet-red.png)|![No](./media/log-analytics-security-audit/oms-bullet-red.png)| a la llegada|
|Registros de eventos de Windows|Windows|![Sí](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sí](./media/log-analytics-security-audit/oms-bullet-green.png)|![Sí](./media/log-analytics-security-audit/oms-bullet-green.png)| ![No](./media/log-analytics-security-audit/oms-bullet-red.png)|![Sí](./media/log-analytics-security-audit/oms-bullet-green.png)| para Almacenamiento de Azure: 1 minuto; para el agente: a la llegada|


## Prácticas recomendadas para el análisis forense

**Buscar pruebas**

Al realizar un análisis forense mediante la solución Seguridad y auditoría, busca las evidencias que los usuarios potencialmente malintencionados dejan atrás. Independientemente de lo que hacen los usuarios en su entorno de TI, muchas de las actividades en las que participan generan artefactos de seguridad. Tenga en cuenta que los atacantes a menudo borran los registros y suele ser el primer paso para ocultar su ruta de acceso.

Sin embargo, si los usuarios tienen acceso a sus propios equipos o tienen acceso de forma remota, la evidencia sobre el uso se almacena en los registros de eventos. OMS recopila estos artefactos *en cuanto se producen*, antes de que cualquiera pueda alterarlos y le permite realizar diferentes tipos de análisis mediante la correlación de datos entre varios equipos.

**Aprender a configurar y recopilar eventos de auditoría**

Para sacar el máximo provecho a la solución Seguridad y auditoría, debe configurar el nivel de eventos de auditoría recopilados por su entorno de Windows que más se ajuste a sus necesidades, mediante los siguientes recursos web.

- [Configuración de la directiva de seguridad](https://technet.microsoft.com/library/dn135243&#40;v=ws.10&#41;.aspx)
- [Configuración avanzada de la directiva de auditoría](https://technet.microsoft.com/library/jj852202&#40;v=ws.10&#41;.aspx)
- [Recomendaciones de la directiva de auditoría](https://technet.microsoft.com/library/dn487457.aspx)

**Habilitar AppLocker**

También debe habilitar los eventos de AppLocker para obtener información valiosa acerca de las ejecuciones de proceso que se producen en su entorno de TI. Para obtener más información, consulte [Para configurar una directiva de AppLocker solo para auditoría](https://technet.microsoft.com/library/hh994622.aspx)

**Configurar el nivel de auditoría para eventos de Windows**

El entorno informático de Windows le ofrece la capacidad de configurar el nivel de captura de registros relacionados con la seguridad. Por ejemplo, puede configurar su entorno para que cada vez que alguien tiene acceso a un archivo, lee un archivo o abre un archivo, se genere un evento. El nivel de detalle que quiere recopilar variará dependiendo de sus necesidades. Sin embargo, todas las opciones que habilita incluyen algún tipo de costo porque necesita almacenar toda la información que recopile. Por este motivo, en muchos entornos de TI de organizaciones, se decide no habilitar la recopilación de datos de escritura de objeto o de lectura del objeto. Cada vez que alguien tiene acceso a cualquier archivo, se podrían generar miles de eventos, algunos de los cuales son inútiles. El nivel de colección que elija depende de su criterio.

>[AZURE.NOTE] Si usa el agente directo y tiene un servidor proxy en su organización, debe configurarlo para permitir que el agente obtenga acceso a OMS. Para más información, consulte [Configuración del proxy y del firewall (opcional) en Log Analytics](log-analytics-proxy-firewall.md).

## Prácticas recomendadas para investigaciones del patrón de infracción de seguridad

**Investigar patrones de actividad anómala.**

Las infracciones de seguridad normalmente proceden de credenciales legítimas y requieren que un usuario malintencionado intente obtener privilegios elevados mediante ataques. La solución Seguridad y auditoría no se centra en la detección de intrusiones, pero ayuda a investigar y detectar patrones de actividades anómalas con problemas importantes. Por ejemplo, debería investigar las siguientes actividades anómalas y cualquier otra que aparezca en **Problemas importantes**.

- inicios de sesión inusuales en un equipo de un usuario que no lo suele usar
- enumeración de red inusual de usuarios o equipos atípicos
- nuevas cuentas de usuarios creadas con derechos administrativos
- cambios realizados en las directivas de seguridad o registro
- archivos ejecutables sospechosos

## Prácticas recomendadas para escenarios de auditoría

Su organización podría tener reglamentos y directivas de cumplimiento de red y equipos que debe cumplir, que requieren unos registros de auditoría amplios. Por ejemplo, si su organización es una empresa de finanzas es posible que tenga que mantener registros que demuestren, en cualquier momento dado en el tiempo, que usuario realizó una operación específica en la red. También es posible que tenga que generar informes que detallen la actividad de un usuario determinado o el servidor seleccionado a petición y volver atrás varios meses o incluso años.

Puede usar la solución Seguridad y auditoría para recopilar datos de auditoría del entorno de TI, independientemente de si los equipos están en un entorno local o en la nube. Todos los datos de auditoría se almacenan, se indizan y se conservan en cada suscripción. Con una suscripción Premium a OMS, se almacena una cantidad ilimitada de datos durante un año. Después, puede ver los datos de auditoría, realizar búsquedas y correlacionar diferentes tipos de datos y equipos para obtener resultados detallados para cualquier intervalo de tiempo desde que se recopilaron los datos.

**Usar Directiva de grupo para recopilar datos de auditoría**

Los datos de auditoría que quiera recopilar y enviar a OMS se administran en su totalidad con la directiva de grupo. Se usa para definir las configuraciones de seguridad como parte de un objeto de directiva de grupo (GPO) que están vinculadas a contenedores de Active Directory como sitios, dominios y unidades organizativas, y ayudar a administrar la configuración de seguridad. Una vez registrados los datos de la directiva, estos se envían al servicio OMS.

**Usar AppLocker para recopilar datos de auditoría**

Además de la configuración de la directiva local, si usa AppLocker para recopilar datos de auditoría, OMS recopilará los datos y, luego, podrá verlos.

## Realizar una investigación sencilla para un ejecutable sospechoso

1. Inicie sesión en OMS.
2. En la página **Información general**, revise la información que se muestra en el mosaico **Auditoría y seguridad** y, luego, haga clic en él. ![Imagen de la página Información general](./media/log-analytics-security-audit/oms-security-audit-dash02.png)
3. En la página **Auditoría y seguridad**, revise la información de la hoja **PROBLEMAS IMPORTANTES**. En la imagen del ejemplo, verá seis problemas importantes para hoy y dos de ayer. En este ejemplo, hay un ejecutable sospechoso. Haga clic en **Ejecutables sospechosos** en la hoja **PROBLEMAS IMPORTANTES**. ![Imagen de la página Seguridad y auditoría](./media/log-analytics-security-audit/oms-security-audit-dash03.png)
4. Búsqueda muestra la consulta y los resultados de los ejecutables sospechosos en los que hizo clic. En el ejemplo, hay un resultado y se muestra el hash del archivo. Haga clic en el id. de **FILEHASH**. ![Imagen del filehash de Resultados de búsqueda](./media/log-analytics-security-audit/oms-security-audit-search01.png)
5. Búsqueda muestra información adicional sobre el archivo ejecutable, incluido su nombre de proceso y la ruta del archivo. Haga clic en **Procesar & lt; Nombre de archivo&gt;**. En el ejemplo, es HEXEDIT.EXE. ![Imagen del proceso de Resultados de búsqueda](./media/log-analytics-security-audit/oms-security-audit-search02.png)
6. Búsqueda anexa el nombre del proceso entre comillas a la consulta. En este ejemplo, "**HEXEDIT. "EXE"**.![Imagen de la Consulta de búsqueda](./media/log-analytics-security-audit/oms-security-audit-search03.png)
7. En el cuadro Consulta de búsqueda, elimine todo excepto el nombre del proceso y las comillas y, luego, haga clic en el icono Buscar. ![Imagen de la información de búsqueda detallada](./media/log-analytics-security-audit/oms-security-audit-search04.png)
8. Búsqueda muestra información detallada acerca del proceso, incluidos los equipos donde ejecutó el proceso, la cuenta de usuario que ha ejecutado el proceso, y la fecha y hora en que se creó un evento para el proceso.
9. Con la información que encuentre, puede adoptar medidas correctivas según sea necesario. Por ejemplo, si determina que el ejecutable es software malintencionado, entonces deberá tomar medidas para quitarlo de todos los sistemas informáticos a los que afecta. Una vez eliminado el archivo ejecutable y cuando OMS recibe eventos de registro y auditoría actualizados de los sistemas informáticos, los valores de la hoja PROBLEMAS IMPORTANTES cambiarán al día siguiente.

## Investigación de direcciones IP malintencionadas

Un método de proteger los servidores es asegurarse de que no se están comunicando con ninguna dirección IP sospechosa o malintencionada conocida. En OMS, tiene una vista centralizada que muestra todas las direcciones IP malintencionadas conocidas con las que los clientes y servidores administrados podrían estar comunicándose. Trabajando con Microsoft Threat Intelligence Center (MSTIC), OMS obtiene actualizaciones cada hora sobre las direcciones IP malintencionadas conocidas más recientes e informa si cualquiera de los servidores puede estar en peligro. El equipo de MSTIC trabaja con varios asociados de inteligencia de amenazas para recopilar y proporcionar esta lista consolidada al servicio de OMS.

La vista de direcciones IP malintencionadas puede encontrarse dentro de la solución Seguridad y auditoría en el portal OMS, mostrada como **Diferentes direcciones IP sospechosas accedidas**.

Puede abrir el icono y ver la lista completa de las distintas direcciones IP sospechosas con las que podrían comunicarse los dispositivos. OMS examina todos los orígenes de datos que envía al servicio, incluidos:

- Registros de Firewall de Windows
- Registros IIS
- WireData


OMS muestra muchos campos relacionados con direcciones IP sospechosas en resultados de búsqueda de registros. Algunos de las más importantes son:

- **IndicatorThreatType**: Algunos ejemplos son botnet, proxy, darknet, comando de malware y nodo de control.
- **Description**: El tipo de amenaza y nuestro nivel de confianza de que la dirección IP es malintencionada. Se trata de los mismos datos que Microsoft utiliza para protegerse a sí mismo, que están disponibles para los usuarios de OMS.

Para obtener una lista completa de campos de direcciones IP sospechosas, consulte [Campos de búsqueda de registros de direcciones IP malintencionadas](#Malicious-IP-log-search-fields) a continuación.

### Para realizar una investigación de direcciones IP malintencionadas

En este ejemplo, nos fijaremos en una posible dirección IP malintencionada utilizando los datos del registro del Firewall de Windows.

1. Inicie sesión en OMS.
2. En la página **Información general**, revise la información que se muestra en el mosaico **Auditoría y seguridad** y, luego, haga clic en él. ![Imagen de la página Información general](./media/log-analytics-security-audit/oms-security-audit-dash02.png)  
3. En la página **Security and Audit** (Seguridad y auditoría), revise la información de resumen del icono **Distinct Suspicious IP Addresses Accessed** (Diferentes direcciones IP sospechosas accedidas). ![Página Seguridad y auditoría que muestra una dirección IP sospechosa](./media/log-analytics-security-audit/oms-maliciousip-01.png)  
4. En este ejemplo, hay 6 direcciones IP sospechosas. Haga clic en el icono **Distinct Suspicious IP Addresses Accessed** (Diferentes direcciones IP sospechosas accedidas).
5. La búsqueda muestra la consulta y los resultados de las direcciones IP malintencionadas que ha encontrado. En el ejemplo, hay 6 resultados y se muestran las direcciones IP. ![resultados de búsqueda que muestran direcciones IP sospechosas](./media/log-analytics-security-audit/oms-maliciousip-02-revised.png)  
6. En la imagen anterior, observe la última dirección IP. En este ejemplo, es **94.102.56.130**. Haga clic en esa dirección.
7. La búsqueda muestra resultados detallados, con varios datos indicadores de amenazas. **Haga clic en mostrar más** para ver todo el conjunto de resultados. ![resultados de búsqueda que muestran datos detallados de indicadores de amenazas](./media/log-analytics-security-audit/oms-maliciousip-03-revised.png)  
8. Si desea ver una lista de todos los servidores del entorno que podrían estar comunicándose con una dirección IP malintencionada, puede utilizar la siguiente consulta de búsqueda de registros.

    ```
    IsActive=True | measure count() by Computer
    ```

### Ejecución de acciones correctivas para la comunicación de direcciones IP malintencionadas

Si está seguro de que un componente o proceso de la red se comunica con direcciones IP malintencionadas conocidas enviando sus datos, la acción que debe realizar está clara:

- Compruebe que el recurso está enviando datos a la dirección IP malintencionada.
- Posiblemente, quite o bloquee el software del equipo que está enviando los datos y evite la comunicación con las direcciones IP específicas para software específico.
- Determine si las credenciales de usuario que ejecutan un archivo ejecutable o el proceso podrían verse comprometidas investigando todas las cuentas bajo las que se ejecuta el proceso problemático. Determine a qué otros recursos podría haber accedido.
- Determinar cómo se instaló el software en el equipo.
- Deje instalado temporalmente el software problemático para que pueda supervisar su actividad a fondo. Después de una evaluación exhaustiva, tome la acción correctiva según sea necesario.


Sin embargo, si *no está seguro* de que los datos se envían a direcciones IP malintencionadas conocidas, o si la *confianza es cuestionable*, las acciones correctivas son menos claras. En general, puede que desee llevar a cabo la investigación mediante algunos de los pasos enumerados anteriormente. En otros casos, puede que no desee realizar ninguna acción en absoluto. En última instancia, usted es la persona que está más familiarizada con la infraestructura de TI de su organización y la que se encuentra en la mejor posición para determinar cómo tratar las amenazas potenciales.


Los registros del firewall muestran la dirección en la que se mueve los datos. Cuando se configura el firewall para bloquear la comunicación saliente (mostrada como enviada en los registros) a direcciones IP específicas, los intentos de comunicación por parte del software potencialmente comprometido a direcciones IP malintencionadas aparece como *bloqueado* en los resultados de búsqueda de registros. Este es un caso donde debe tomar medidas adicionales (aunque el software comprometido pueda estar bloqueado a una dirección IP específica, dicho software podría intentar comunicarse con otras direcciones IP malintencionados potenciales y desconocidas en el futuro).

Los registros del firewall también muestran cuándo se bloquean los datos entrantes y es posible que vea un poco actividad para esta situación. ¿Debería preocuparle esta situación? Probablemente, no. Cuando los registros del firewall muestran datos entrantes bloqueados, valida que el firewall está haciendo lo que debe: proteger su infraestructura.


### Inteligencia relacionadas con las amenazas de direcciones IP malintencionadas

Microsoft comparte datos con diversas organizaciones para crear una vista de resumen de todos los indicadores conocidos que podrían poner en peligro los datos. El resumen está pensado para identificar los aciertos en conjuntos de datos y para mostrar detalles extendidos de amenazas como vínculos para que se vean con facilidad más detalles. Esta inteligencia relacionada con las amenazas se ve en los resultados de búsqueda de registros de direcciones IP malintencionadas.

Existen 3 métodos mediante los que Microsoft recibe datos sobre amenazas, que se suman para formar un conjunto de datos de inteligencia relacionada con las amenazas. Estos proveedores básicamente se dividen en las siguientes categorías:

- Suscripciones de pago: Las empresas con las que Microsoft ha suscrito un contrato para comprar sus datos.
- Datos de asociados: Compañías de inteligencia de seguridad. Puede considerar estos datos como ‘inteligencia de señal’, porque no contienen informes detallados, sin embargo, se consideran válidos.
- Procesos internos de Microsoft: Principalmente, Microsoft Threat Intelligence Center (MSTIC).

#### Campos de búsqueda de registros de direcciones IP malintencionadas

Los siguientes campos de direcciones IP malintencionadas aparecen en los resultados de búsqueda de registros.

|campo de búsqueda| description |
|---|---|
| **IsActive**| Designa que el indicador está activo y debe considerarse un acierto válido si coincide con una dirección IP encontrada. MSTIC mantiene este indicador.|
|**Confidence**| Muestra un valor entre 1 y 100. MSTIC mantiene este valor y los algoritmos que lo definen. Se trata de un buen valor por el que filtrar los resultados de búsqueda.|
|**TLPLevel**| Nivel de protocolo de semáforo. Los valores son: verde, ámbar y rojo. Estos valores se corresponden con repercusión baja en el negocio, repercusión media en el negocio y repercusión alta en el negocio, respectivamente.|
| **IndicatorThreatType** | Breve descripción del tipo de amenaza que posee el indicador. Estos son algunos: Botnet, DDoS, Malware, Malicious URL, Malicious IP, Phishing, y Spam.|
| **Gravedad** | Muestra un valor entre 0 y 5. Esto indica lo grave que es la amenaza.|
| **MaliciousIP** | Dirección IP del host malintencionado.|
| **CommunicationDirection** | Muestra la dirección en la que se está produciendo el tráfico IP hacia la dirección IP malintencionada.|

## Pasos siguientes

- [Buscar en registros](log-analytics-log-searches.md) para ver datos de seguridad y auditoría detallados.

<!---HONumber=AcomDC_0518_2016-->