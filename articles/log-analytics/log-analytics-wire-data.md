<properties
    pageTitle="Solución Datos de conexión en Log Analytics | Microsoft Azure"
    description="Datos de conexión son datos consolidados de red y rendimiento de los equipos con agentes de OMS, como agentes conectados con Windows y Operations Manager. Los datos de red se combinan con los datos de registro para ayudar a correlacionar datos."
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
    ms.date="08/11/2016"
    ms.author="banders"/>


# <a name="wire-data-solution-in-log-analytics"></a>Solución Datos de conexión en Log Analytics

Datos de conexión son datos consolidados de red y rendimiento de los equipos con agentes de OMS, como agentes conectados con Windows y Operations Manager. Los datos de red se combinan con los datos de registro para ayudar a correlacionar datos. Los agentes de OMS instalados en los equipos en la infraestructura de TI supervisan los datos de red enviados a dichos equipos y desde estos para los niveles 2 y 3 de la red en el [modelo OSI](https://en.wikipedia.org/wiki/OSI_model) , incluidos los distintos protocolos y puertos utilizados.

>[AZURE.NOTE] La solución Datos de conexión no está actualmente disponible para agregarse a áreas de trabajo. Los clientes que ya tienen la solución Datos de conexión habilitada pueden continuar utilizándola.

De forma predeterminada, OMS recopila datos registrados de CPU, memoria, disco y los datos de rendimiento de red de contadores integrados en Windows. La recopilación de datos de red y de otros datos se realiza en tiempo real para cada agente, incluidas las subredes y los protocolos de nivel de aplicación utilizados por el equipo. Puede agregar otros contadores de rendimiento en la página Configuración de la pestaña Registros.

Si ha utilizado [sFlow](http://www.sflow.org/) u otro software con el [protocolo NetFlow de Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), las estadísticas y los datos que verá de los datos de conexión le resultarán familiares.

Algunos de los tipos de consultas de búsqueda de registros integradas son:

- Agentes que proporcionan datos de conexión
- Dirección IP de los agentes que proporcionan datos de conexión
- Comunicaciones salientes por direcciones IP
- Número de bytes enviados por protocolos de aplicación
- Número de bytes enviados por un servicio de aplicación
- Bytes recibidos por distintos protocolos
- Número total de bytes enviados y recibidos por dirección IP
- Direcciones IP que se han comunicado con agentes en la subred 10.0.0.0/8
- Promedio de latencia de las conexiones que se midieron de forma fiable
- Procesos de equipo que iniciaron o recibieron tráfico de red
- Cantidad de tráfico de red de un proceso

Cuando realice una búsqueda con datos de conexión, puede filtrar y agrupar datos para ver información sobre los principales agentes y protocolos. O puede buscar cuándo determinados equipos (direcciones IP y MAC) se comunicaron entre sí, durante cuánto tiempo y cuántos datos se enviaron (básicamente, ve metadatos sobre el tráfico de red, que se basa en la búsqueda).

Sin embargo, puesto que está viendo los metadatos, no es necesariamente útil para la solución de problemas detallada. Los datos de conexión de OMS no son una captura completa de los datos de red. Por tanto, no están diseñados para solución de problemas profunda en el ámbito de paquete.
La ventaja de utilizar el agente, en comparación con otros métodos de recopilación, es que no tiene que instalar dispositivos, volver a configurar los conmutadores de red o realizar configuraciones complicadas. Los datos de conexión simplemente se basan en agente: instala el agente en un equipo y supervisará su propio tráfico de red. Otra ventaja es cuando desea supervisar cargas de trabajo que se ejecutan en proveedores de nube o que hospedan el proveedor de servicio o Microsoft Azure, donde el usuario no tiene la capa de tejido.

En cambio, no tiene visibilidad completa de lo que ocurre en la red si no instala agentes en todos los equipos de su infraestructura de red.

## <a name="installing-and-configuring-the-solution"></a>Instalación y configuración de la solución
Utilice la siguiente información para instalar y configurar la solución.

- La solución Datos de conexión adquiere datos desde equipos que ejecutan Windows Server 2012 R2, Windows 8.1 y sistemas operativos posteriores.
- Se requiere Microsoft .NET Framework 4.0 o posterior en equipos de los que desea adquirir datos de conexión.
- Agregue la solución Datos de conexión al área de trabajo de OMS mediante el proceso descrito en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).  No es necesario realizar ninguna configuración más.
- Si desea ver los datos de conexión de una solución específica, debe tener la solución ya agregada a su área de trabajo de OMS.

## <a name="wire-data-data-collection-details"></a>Detalles de la recopilación de Datos de conexión

La solución Datos de conexión recopila metadatos sobre el tráfico de red con los agentes habilitados.

La siguiente tabla muestra los métodos de recolección de datos y otros detalles sobre cómo se recopilan los datos para Datos de conexión.


| plataforma | Agente directo | Agente de SCOM | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
|---|---|---|---|---|---|---|
|Windows (2012 R2 / 8.1 o posterior)|![Sí](./media/log-analytics-wire-data/oms-bullet-green.png)|![Sí](./media/log-analytics-wire-data/oms-bullet-green.png)|![No](./media/log-analytics-wire-data/oms-bullet-red.png)|            ![No](./media/log-analytics-wire-data/oms-bullet-red.png)|![No](./media/log-analytics-wire-data/oms-bullet-red.png)| cada 1 minuto|


## <a name="combining-wire-data-with-other-solution-data"></a>Combinación de datos de conexión con otros datos de la solución

Los datos devueltos de las consultas integradas anteriores podrían ser interesantes por sí mismos. Sin embargo, la utilidad de los datos de conexión se realiza cuando se combinan con información de otras soluciones de OMS. Por ejemplo, puede utilizar datos de eventos de seguridad recopilados por la solución Seguridad y auditoría y combinarlos con los datos de conexión para buscar intentos de inicio de sesión de red inusuales para procesos con nombre.  En este ejemplo, usaría los operadores IN y DISTINCT para unir puntos de datos en la consulta de búsqueda.

Requisitos: Para poder utilizar el ejemplo siguiente, debe tener instalada la solución Seguridad y auditoría. Sin embargo, puede usar datos desde otras soluciones para combinar con los datos de conexión para lograr resultados similares.

### <a name="to-combine-wire-data-with-security-events"></a>Combinación de datos de conexión con eventos de seguridad

1. En la página Información general, haga clic en el icono **WireData** .
2. En la lista de **Common WireData Queries** (Consultas comunes de WireData), haga clic en **Amount of Network Traffic (in Bytes) by Process** (Cantidad de tráfico de red (en bytes) por proceso) para ver la lista de procesos devueltos.
    ![consultas de datos de conexión](./media/log-analytics-wire-data/oms-wiredata-01.png)
3. Si la lista de procesos es demasiado grande para verla fácilmente, puede modificar la consulta de búsqueda a algo similar a lo siguiente:

    ```
    Type WireData | measure count() by ProcessName | where AggregatedValue <40
    ```
    Lo que se muestra en el ejemplo siguiente es un proceso denominado DancingPigs.exe, que podría parecer sospechoso.
    ![resultados de búsqueda de datos de conexión](./media/log-analytics-wire-data/oms-wiredata-02.png)

4. Con los datos devueltos en la lista, haga clic en un proceso con nombre. En este ejemplo, se hizo clic en DancingPigs.exe. Los resultados que se muestran a continuación describen el tipo de tráfico de red, como la comunicación saliente sobre diversos protocolos.
    ![resultados de datos de conexión que muestran un proceso con nombre](./media/log-analytics-wire-data/oms-wiredata-03.png)

5. Dado que se instala la solución Seguridad y auditoría, puede investigar los eventos de seguridad que tienen el mismo valor de campo ProcessName modificando la consulta de búsqueda mediante los operadores de consulta de búsqueda IN y DISTINCT. Puede hacer eso cuando tanto los datos de conexión como otros registros de soluciones tienen valores con el mismo formato. Modifique la consulta de búsqueda para que sea similar a lo siguiente:

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName}
    ```    

    ![resultados de datos de conexión que muestran datos combinados](./media/log-analytics-wire-data/oms-wiredata-04.png)
6. En los resultados anteriores, verá que se muestra la información de la cuenta. Ahora puede refinar la consulta de búsqueda para averiguar con qué frecuencia el proceso utiliza la cuenta, mostrando datos de Seguridad y auditoría, con una consulta parecida a lo siguiente:        

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName} | measure count() by Account
    ```

    ![resultados de datos de conexión que muestran datos de cuenta](./media/log-analytics-wire-data/oms-wiredata-05.png)



## <a name="next-steps"></a>Pasos siguientes

- [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para más información sobre cómo ver registros de búsqueda de datos de conexión detallados.
- Consulte la [entrada de blog Using Wire Data in Operations Management Suite Log Search](http://blogs.msdn.com/b/dmuscett/archive/2015/09/09/using-wire-data-in-operations-management-suite.aspx) (Uso de Datos de conexión en la búsqueda de registros de Operations Management Suite) donde encontrará información adicional sobre la frecuencia con la que se recopilan los datos y cómo puede modificar las propiedades de la colección de agentes de Operations Manager.



<!--HONumber=Oct16_HO2-->


