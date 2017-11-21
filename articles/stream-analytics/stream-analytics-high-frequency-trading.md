---
title: "Simulación de operación bursátil de alta frecuencia con Stream Analytics | Microsoft Docs"
description: "Cómo realizar el aprendizaje y la puntuación del modelo de regresión lineal en el mismo trabajo de Stream Analytics"
keywords: "aprendizaje automático, análisis avanzados, regresión lineal, simulación, UDA, función definida por el usuario"
documentationcenter: 
services: stream-analytics
author: zhongc
manager: jhubbard
editor: cgronlun
ms.assetid: 997ccfc1-abaf-4c12-bef2-632481140f05
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 11/05/2017
ms.author: zhongc
ms.openlocfilehash: 0a5a1129c5b7fc693ed7c187d928a128650f28b9
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>Simulación de operación bursátil de alta frecuencia con Stream Analytics
La combinación de lenguaje SQL Azure Stream Analytics, UDF de JavaScript y UDA es muy eficaz, ya que permite a los usuarios realizar análisis avanzados, lo que incluye el entrenamiento y la puntuación del aprendizaje automático en línea, así como la simulación de procesos con estado. En este artículo se describe cómo realizar la regresión lineal en un trabajo de Azure Stream Analytics que realiza un entrenamiento y una puntuación continuos en un escenario de operación bursátil de alta frecuencia.

## <a name="high-frequency-trading"></a>Operación bursátil de alta frecuencia
El flujo lógico de una operación bursátil de alta frecuencia es obtener cotizaciones en tiempo real de una bolsa de valores, crear con ellas un modelo predictivo, con el fin de poder anticiparse al movimiento de los precios, y realizar órdenes de compra o venta en consecuencia para obtener dinero gracias a la predicción correcta de los movimientos de los precios. Como consecuencia, se necesita lo siguiente
* Fuente de cotizaciones en tiempo real
* Un modelo predictivo que puede operar con cotizaciones en tiempo real
* Una simulación de operación bursátil que muestra los beneficios y pérdidas del algoritmo de intercambio

### <a name="real-time-quote-feed"></a>Fuente de cotizaciones en tiempo real
IEX ofrece la posibilidad de realizar pujas en tiempo real y pedir cotizaciones de forma gratuita mediante socket.io, https://iextrading.com/developer/docs/#websockets. Se puede escribir un programa de consola simple para recibir cotizaciones en tiempo real e insertarlas en Event Hub como origen de datos. A continuación se muestra el esqueleto del programa. Con el fin de ser breves se omite el control de errores. También tendrá que incluir los paquetes NuGet SocketIoClientDotNet y WindowsAzure.ServiceBus en el proyecto.


    using Quobject.SocketIoClientDotNet.Client;
    using Microsoft.ServiceBus.Messaging;

    var symbols = "msft,fb,amzn,goog";
    var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
    var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");

    socket.On(Socket.EVENT_MESSAGE, (message) =>
    {
        eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
    });

    socket.On(Socket.EVENT_CONNECT, () =>
    {
        socket.Emit("subscribe", symbols);
    });

Estos son algunos eventos de ejemplo que se han generado.

    {"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,"volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,"lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,"volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,"lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,"volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,"lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,"volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,"lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}

>[!NOTE]
>La marca de tiempo del evento es **lastUpdated**, en tiempo epoch.

### <a name="predictive-model-for-high-frequency-trading"></a>Modelo de predicción para el intercambio comercial de alta frecuencia
Para la demostración usamos un modelo lineal que describe Darryl Shen en su documento. http://eprints.maths.ox.ac.uk/1895/1/Darryl%20Shen%20%28for%20archive%29.pdf.

Desequilibrio en el volumen de órdenes (VOI) es una función del precio y volumen de compra/venta, y el volumen/precio de compra/venta desde el último "tick". El documento identifica la correlación entre el VOI y movimiento del precio de los futuros, y genera un modelo lineal entre los cinco últimos valores de VOI y el cambio de precio en los diez "ticks" siguientes. El modelo se entrena con datos del día anterior con la regresión lineal. Luego, el modelo entrenado se utiliza para realizar predicciones de cambios de precios en las cotizaciones del día de las operaciones en bolsa en tiempo real. Cuando se predice un cambio de precio suficientemente grande, se ejecuta una transacción. En función del valor del umbral, se pueden esperar miles de transacciones en un solo valor en un día de bolsa.

![Definición de VOI](./media/stream-analytics-high-frequency-trading/voi-formula.png)

Ahora, vamos a expresar las operaciones de entrenamiento y predicción en un trabajo de Azure Stream Analytics.

En primer lugar, se borran las entradas. El tiempo epoch (o tiempo Unix) se convierte en fecha y hora mediante **DATEADD**. **TRY_CAST** se usa para convertir tipos de datos sin que se produzcan errores en la consulta. Siempre es recomendable convertir los campos de entrada a los tipos de datos esperados, con el fin de que no haya ningún comportamiento inesperado cuando se realice la manipulación o comparación de los campos.

    WITH
    typeconvertedquotes AS (
        /* convert all input fields to proper types */
        SELECT
            System.Timestamp AS lastUpdated,
            symbol,
            DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
            TRY_CAST(bidSize as bigint) AS bidSize,
            TRY_CAST(bidPrice as float) AS bidPrice,
            TRY_CAST(askSize as bigint) AS askSize,
            TRY_CAST(askPrice as float) AS askPrice,
            TRY_CAST(volume as bigint) AS volume,
            TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
            TRY_CAST(lastSalePrice as float) AS lastSalePrice
        FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
    ),
    timefilteredquotes AS (
        /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
        /* cleanup invalid data points */
        SELECT * FROM typeconvertedquotes
        WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
    ),

Después, se usa la función **LAG** función para obtener los valores desde el último "tick". Se elige arbitrariamente una hora del valor **LIMIT DURATION**. Dada la frecuencia de la oferta, es seguro asumir que puede encontrar el "tick" anterior buscando una hora.  

    shiftedquotes AS (
        /* get previous bid/ask price and size in order to calculate VOI */
        SELECT
            symbol,
            (bidPrice + askPrice)/2 AS midPrice,
            bidPrice,
            bidSize,
            askPrice,
            askSize,
            LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
            LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
            LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
            LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
        FROM timefilteredquotes
    ),

Luego, se puede calcular el valor de VOI. Tenga en cuenta que los valores nulos se eliminan si no existe el "tick" anterior, por si acaso.

    currentPriceAndVOI AS (
        /* calculate VOI */
        SELECT
            symbol,
            midPrice,
            (CASE WHEN (bidPrice < bidPricePrev) THEN 0
                ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
             END) -
            (CASE WHEN (askPrice < askPricePrev) THEN askSize
                ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
             END) AS VOI
        FROM shiftedquotes
        WHERE
            bidPrice IS NOT NULL AND
            bidSize IS NOT NULL AND
            askPrice IS NOT NULL AND
            askSize IS NOT NULL AND
            bidPricePrev IS NOT NULL AND
            bidSizePrev IS NOT NULL AND
            askPricePrev IS NOT NULL AND
            askSizePrev IS NOT NULL
    ),

Ahora, se vuelve a usar la función **LAG** para crear una secuencia con dos valores de VOI consecutivos, seguidos de diez valores de precios medio consecutivos.

    shiftedPriceAndShiftedVOI AS (
        /* get 10 future prices and 2 previous VOIs */
        SELECT
            symbol,
            midPrice AS midPrice10,
            LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
            LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
            LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
            LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
            LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
            LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
            LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
            LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
            LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
            LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
            LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
            LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
        FROM currentPriceAndVOI
    ),

Después, se vuelve a dar a los datos la forma de entradas en un modelo lineal de dos variables. Se eliminan los eventos cuando no se tengan todos los datos.

    modelInput AS (
        /* create feature vector, x being VOI, y being delta price */
        SELECT
            symbol,
            (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
            VOI1 AS x1,
            VOI2 AS x2
        FROM shiftedPriceAndShiftedVOI
        WHERE
            midPrice1 IS NOT NULL AND
            midPrice2 IS NOT NULL AND
            midPrice3 IS NOT NULL AND
            midPrice4 IS NOT NULL AND
            midPrice5 IS NOT NULL AND
            midPrice6 IS NOT NULL AND
            midPrice7 IS NOT NULL AND
            midPrice8 IS NOT NULL AND
            midPrice9 IS NOT NULL AND
            midPrice10 IS NOT NULL AND
            midPrice IS NOT NULL AND
            VOI1 IS NOT NULL AND
            VOI2 IS NOT NULL
    ),

Dado que Azure Stream Analytics no tiene una función de regresión lineal integrada, se usan los agregados **SUM** y **AVG** para calcular los coeficientes del modelo lineal.

![Fórmula de regresión lineal](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

    modelagg AS (
        /* get aggregates for linear regression calculation,
         http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
        SELECT
            symbol,
            SUM(x1 * x1) AS x1x1,
            SUM(x2 * x2) AS x2x2,
            SUM(x1 * y) AS x1y,
            SUM(x2 * y) AS x2y,
            SUM(x1 * x2) AS x1x2,
            AVG(y) AS avgy,
            AVG(x1) AS avgx1,
            AVG(x2) AS avgx2
        FROM modelInput
        GROUP BY symbol, TumblingWindow(hour, 24, -4)
    ),
    modelparambs AS (
        /* calculate b1 and b2 for the linear model */
        SELECT
            symbol,
            (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
            (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
            avgy,
            avgx1,
            avgx2
        FROM modelagg
    ),
    model AS (
        /* calculate a for the linear model */
        SELECT
            symbol,
            avgy - b1 * avgx1 - b2 * avgx2 AS a,
            b1,
            b2
        FROM modelparambs
    ),

Para usar el modelo del día anterior para la puntuación del evento actual, deseamos unir las ofertas con el modelo. Sin embargo, aquí, en lugar de usar **UNIR**, se usa **UNION** en los eventos de modelo y eventos de la oferta y después **LAG** para emparejar los eventos con el modelo del día anterior, por lo que podemos obtener exactamente una coincidencia. Dado el fin de semana, es preciso mirar tres días atrás. Si utiliza un sencillo **UNIR**, obtendríamos tres modelos para cada evento de oferta.

    shiftedVOI AS (
        /* get two consecutive VOIs */
        SELECT
            symbol,
            midPrice,
            VOI AS VOI1,        
            LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
        FROM currentPriceAndVOI
    ),
    VOIAndModel AS (
        /* combine VOIs and models */
        SELECT
            'voi' AS type,
            symbol,
            midPrice,
            VOI1,
            VOI2,
            0.0 AS a,
            0.0 AS b1,
            0.0 AS b2
        FROM shiftedVOI
        UNION
        SELECT
            'model' AS type,
            symbol,
            0.0 AS midPrice,
            0 AS VOI1,
            0 AS VOI2,
            a,
            b1,
            b2
        FROM model
    ),
    VOIANDModelJoined AS (
        /* match VOIs with the latest model within 3 days (72 hours, to take weekend into account) */
        SELECT
            symbol,
            midPrice,
            VOI1 as x1,
            VOI2 as x2,
            LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
            LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
            LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
        FROM VOIAndModel
        WHERE type = 'voi'
    ),

Ahora, podemos realizar predicciones y generar señales de compra/ventas basadas en el modelo, con un valor de umbral de 0,02. Un valor de transacción de 10 es comprar, mientras que un valor de transacción de -10 es vender.

    prediction AS (
        /* make prediction if there is a model */
        SELECT
            symbol,
            midPrice,
            a + b1 * x1 + b2 * x2 AS efpc
        FROM VOIANDModelJoined
        WHERE
            a IS NOT NULL AND
            b1 IS NOT NULL AND
            b2 IS NOT NULL AND
            x1 IS NOT NULL AND
            x2 IS NOT NULL
    ),
    tradeSignal AS (
        /* generate buy/sell signals */
        SELECT
            DateAdd(hour, -7, System.Timestamp) AS time,
            symbol,     
            midPrice,
            efpc,
            CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
            DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
        FROM prediction
    ),

### <a name="trading-simulation"></a>Simulación de operación bursátil
Una vez que tenemos las señales de la operación, nos gustaría probar la eficacia de la estrategia bursátil, pero sin realizar ninguna transacción real. Esto se logra con un agregado definido por el usuario (UDA), con una ventana de salto, y realizando un salto por minuto. La agrupación adicional por fecha y la cláusula having permiten que el intervalo de tiempo solo tenga en cuenta los eventos que pertenecen al mismo día. En el caso de una ventana de salto que abarque dos días, fecha **GROUP BY**, separa la agrupación en el día anterior y el día actual. La cláusula **HAVING** elimina los intervalos de tiempo que finalizan el día actual, pero que se agrupan el día anterior.

    simulation AS
    (
        /* perform trade simulation for the past 7 hours to cover an entire trading day, generate output every minute */
        SELECT
            DateAdd(hour, -7, System.Timestamp) AS time,
            symbol,
            date,
            uda.TradeSimulation(tradeSignal) AS s
        FROM tradeSignal
        GROUP BY HoppingWindow(minute, 420, 1), symbol, date
        Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
    )

El UDA de JavaScript inicializa todos los acumuladores de la función init, calcula la transición de estado de cada evento que se agrega a la ventana temporal y devuelve los resultados de simulación al final de la ventana. El proceso general de las operaciones bursátiles es comprar un valor cuando se recibe una señal de compra y no hay "holding" de acciones; y vender un valor cuando se recibe una señal de venta y hay "holding" de acciones o una posición corta si no hay "holding" de acciones. Si hay una posición corta y se recibe una señal de compra, se compra para realizar la cobertura. En esta simulación nunca mantenemos ni estamos cortos de 10 acciones de un valor dado y costo de cada transacción es siempre 8 dólares.


    function main() {
        var TRADE_COST = 8.0;
        var SHARES = 10;

        this.init = function () {
            this.own = false;
            this.pos = 0;
            this.pnl = 0.0;
            this.tradeCosts = 0.0;
            this.buyPrice = 0.0;
            this.sellPrice = 0.0;
            this.buySize = 0;
            this.sellSize = 0;
            this.buyTotal = 0.0;
            this.sellTotal = 0.0;
        }

        this.accumulate = function (tradeSignal, timestamp) {

            if(!this.own && tradeSignal.trade == 10) {
              // Buy to open
              this.own = true;
              this.pos = 1;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.buySize += SHARES;
              this.buyTotal += SHARES * tradeSignal.midprice;
            } else if(!this.own && tradeSignal.trade == -10) {
              // Sell to open
              this.own = true;
              this.pos = -1
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.sellSize += SHARES;
              this.sellTotal += SHARES * tradeSignal.midprice;
            } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
              // Sell to close
              this.own = false;
              this.pos = 0;
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
              this.sellSize += SHARES;
              this.sellTotal += SHARES * tradeSignal.midprice;

              // Sell to open
              this.own = true;
              this.pos = -1;
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.sellSize += SHARES;        
              this.sellTotal += SHARES * tradeSignal.midprice;
            } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
              // Buy to close
              this.own = false;
              this.pos = 0;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
              this.buySize += SHARES;
              this.buyTotal += SHARES * tradeSignal.midprice;

              // Buy to open
              this.own = true;
              this.pos = 1;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.buySize += SHARES;         
              this.buyTotal += SHARES * tradeSignal.midprice;
            }
        }

        this.computeResult = function () {
            var result = {
                "pnl": this.pnl,
                "buySize": this.buySize,
                "sellSize": this.sellSize,
                "buyTotal": this.buyTotal,
                "sellTotal": this.sellTotal,
                "tradeCost": this.tradeCost
                };
            return result;
        }
    }

Por último, la salida se envía al panel de Power BI, que es donde se ve.

    SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
    SELECT
        symbol,
        time,
        date,
        TRY_CAST(s.pnl as float) AS pnl,
        TRY_CAST(s.buySize as bigint) AS buySize,
        TRY_CAST(s.sellSize as bigint) AS sellSize,
        TRY_CAST(s.buyTotal as float) AS buyTotal,
        TRY_CAST(s.sellTotal as float) AS sellTotal
        INTO pnlDashboard
    FROM simulation /* output trade simulation to PBI */

![Intercambios bursátiles](./media/stream-analytics-high-frequency-trading/trades.png)

![PNL](./media/stream-analytics-high-frequency-trading/pnl.png)


## <a name="summary"></a>Resumen
Como puede ver, se puede implementar un modelo de operación bursátil de alta frecuencia con una consulta de un grado de complejidad medio en Azure Stream Analytics. Tenemos que simplificar el modelo de cinco variables de entrada a dos, debido a que no hay ninguna función de regresión lineal integrada. Sin embargo, en el caso de un usuario determinado, los algoritmos más sofisticados y con mayores dimensiones también se pueden implementar como UDA de JavaScript. Lo que merece la pena indicar es que la mayor parte de la consulta, que no sea el UDA de JavaScript, se puede probar y depurar en Visual Studio con [Herramienta Azure Stream Analytics para Visual Studio](stream-analytics-tools-for-visual-studio.md). Una vez escrita la consulta inicial, el autor tardó menos de 30 minutos en probar y depurar la consulta en Visual Studio. Actualmente el UDA no se puede depurar en Visual Studio. Estamos trabajando para habilitarlo con la capacidad de recorrer el código de JavaScript. Además, tenga en cuenta que los nombres de los campos que acceden al UDA se escriben en mayúsculas. Este comportamiento no fue obvio durante las pruebas de las consultas. Sin embargo, con el nivel de compatibilidad 1.1 de Azure Stream Analytics, permitimos que se conserve el uso de mayúsculas y minúsculas, por lo que el comportamiento es más natural.

Esperamos que este artículo sirva de inspiración para todos los usuarios de Azure Stream Analytics, que pueden usar nuestro servicio para realizar análisis avanzados casi en tiempo real de forma continua. Envíenos sugerencias para facilitar la implementación de consultas en escenarios de análisis avanzado.
