<properties
   pageTitle="Comprender la factura de Azure"
   description="Comprender la factura de Azure"
   services=""
   documentationCenter="Azure"
   authors="kareni"
   manager="jocho"
   editor=""
   tags="billing"/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/07/2015"
   ms.author="kareni"/>


# Comprender la factura de Microsoft Azure

Los cargos de las suscripciones de Microsoft Azure varían según el plan de tarifas. Algunos planes de tarifas, como Visual Studio Ultimate con MSDN, incluyen créditos mensuales que puede usar en cualquier servicio de Azure según sus necesidades.

Tenga en cuenta que es posible informar un uso latente de hasta 24 horas correspondiente al período de facturación anterior en el período de facturación actual.

Para obtener más información acerca del consumo, el uso y los planes de tarifas, consulte la [página Opciones de compra de Microsoft Azure](http://azure.microsoft.com/pricing/purchase-options/).

## Contenido:

Este tema le ayuda con las siguientes tareas cuando lea la factura.

-  Ver o descargar una factura de Azure
-  Información del cliente
-  Comprender el resumen de factura
-  Comprender los cargos actuales
-  Información de pie de página
-  Comprender la información adicional
-  Comprender los cargos de uso detallados
-  Analizar los datos de uso diario

### Ver o descargar una factura de Microsoft Azure:

En el [portal Uso y facturación](https://account.windowsazure.com/subscriptions), puede ver la factura actual y descargar facturas anteriores.

Para ver o descargar una factura:

1. Inicie sesión en el [portal Uso y facturación](https://account.windowsazure.com/subscriptions) con su identificador de cuenta de Microsoft o su identificador de cuenta profesional o educativa.

2. Haga clic en la suscripción para la que desea consultar los detalles y el uso.

3. Haga clic en Historial de **facturación**

    ![Resumen: historial de facturación: 1](./media/azure-billing-understand-your-bill/ContentViewaBillforMA1.png)


4. La sección **Historial de facturación** muestra los extractos correspondientes a los seis últimos períodos de facturación, además del período actual no facturado. El extracto del período actual es una estimación de los cargos en el momento en que se generó la estimación. Esta información solo se actualiza diariamente y es posible que no incluya todo el uso en el que se ha incurrido a la fecha. La factura mensual puede ser distinta a esta estimación.

    ![Resumen: historial de facturación: 2](./media/azure-billing-understand-your-bill/ContentViewaBillforMA2.png)

5. Haga clic en **Ver extracto actual** para ver una estimación de los cargos en el momento en que se generó la estimación. Esta información solo se actualiza diariamente y es posible que no incluya todo el uso en el que se ha incurrido a la fecha. La factura mensual puede ser distinta a esta estimación.

    ![Resumen: historial de facturación: 3](./media/azure-billing-understand-your-bill/ContentViewaBillforMA3.png)

    ![Resumen: historial de facturación: 4](./media/azure-billing-understand-your-bill/ContentViewaBillforMA4.png)

6. Haga clic en **Descargar factura** para ver una copia de la factura anterior.

    ![Resumen: historial de facturación: 5](./media/azure-billing-understand-your-bill/ContentViewaBillforMA5.png)



**A continuación, se muestran facturas de las ofertas de Visual Studio Ultimate con MSDN y de pago por uso.**

*Los créditos mensuales no utilizados no se pueden transferir a los meses subsiguientes.

 **NOMBRE DE LA OFERTA** | **TIPO DE OFERTA** | **INCLUYE SERVICIOS GRATUITOS** | **DESCARGA** |
 :--------- |:-------- | :----------------: | :-------|
Azure MSDN-Visual Studio Ultimate| Programa de beneficios | Sí\* | [Archivo de muestra](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_MSDN_Sample.pdf)
Pay-As-You-Go | Consumo | No| [Archivo de muestra](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_PAYG_Sample.pdf)
Uso detallado - csv | N/D | N/D | [Archivo de muestra](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_Detailed_Usage_v1_csv.xlsx)


## Encabezado - Información del cliente

La sección de información del cliente identifica la información pertinente respecto del uso y del perfil.
 ![encabezado](./media/azure-billing-understand-your-bill/Header.png)

### Número de factura
Identificador único de factura con fines de seguimiento

### Ciclo de facturación
El período de tiempo en que se realizó el uso.

### Fecha de la factura
Fecha en que se generó la factura.

### Método de pago
Tipo de pago que se utiliza en la cuenta \(es decir, factura o tarjeta de crédito\).

### Dirección de facturación
Dirección de pagos de Microsoft Azure.

### Correo electrónico del propietario de cuenta
La dirección de correo electrónico de la cuenta con que está registrada la cuenta de Microsoft Azure.



## Comprender el resumen de factura
La sección Resumen de factura de la factura resume las transacciones desde la última factura y los cargos de uso actuales.

![resumen de factura](./media/azure-billing-understand-your-bill/InvoiceSummary.png)

La sección Saldos, pagos y otros créditos de la factura resume las transacciones desde la última factura.

### Saldo anterior
El saldo anterior es el importe total debido en la última factura.

### Pagos
Los pagos son los pagos totales aplicados a la última factura.

### Saldo pendiente \(desde el ciclo de facturación anterior\)
Todos los ajustes de factura \(créditos o saldos\) aplicados a la cuenta desde la última factura.


## Comprender los cargos actuales
La sección Cargos actuales de la factura contiene detalles acerca de los cargos mensuales. Los vínculos están organizados en las siguientes subsecciones.

### Cargos de uso
Los cargos de uso son los cargos mensuales totales de una suscripción. Se le facturará con un mes de retraso según el uso que haya realizado el mes anterior.

### Descuentos
Los descuentos de servicio en el uso se verían reflejados en este elemento de línea y se aplican a la factura actual.

### Ajustes
Los ajustes varios son cargos pendientes o créditos varios que se aplican a la factura actual. Por ejemplo, si tiene la oferta de Visual Studio Ultimate con MSDN, podría ver un crédito mensual en este elemento de línea. Si cancela la suscripción, podría ver los cargos por el uso mensual que excedan el crédito mensual incluido en la oferta desde el comienzo del período de facturación actual hasta la fecha de cancelación de la suscripción.

## Información de pie de página
![pie de página](./media/azure-billing-understand-your-bill/footerinformation.png)

## Comprender la información adicional
La página de información adicional le brinda referencias a otros recursos para poder comprender su factura y vínculos para ver el uso, además de otra información importante relacionada con su factura.

![información adicional](./media/azure-billing-understand-your-bill/AdditionalInformation.png)

### Uso detallado
Un vínculo en la descripción bajo Uso detallado le dirige al portal Uso y facturación de Azure, donde puede consultar el uso detallado correspondiente a esta suscripción.

### Información adicional y recursos útiles
Esta sección cuenta con vínculos a preguntas sencillas relacionadas con los tamaños de las instancias de proceso, los cargos de Base de datos SQL y vínculos útiles para permitirle responder cualquier otra pregunta.

### Dirección de venta
Se rellena previamente con la dirección del perfil de la cuenta.

### Instrucciones de pago
En esta sección se encuentran las instrucciones de pago de dónde enviar cheques, transferencias bancarias o cheques urgentes si el método de pago es la factura.

## Comprender los cargos de uso detallados

Los cargos de uso son los cargos mensuales totales de una suscripción menos cualquier crédito o descuento. Se le facturará con un mes de retraso según el uso que haya realizado el mes anterior. Cuando consulta una lista de cargos de uso en la factura, las siguientes columnas muestran los detalles de lo que le están facturando. Puede descargar un [archivo de muestra](https://understandingyourbill.blob.core.windows.net/appendices/UnderstandDetailedUsageCharges.xlsx) con los valores de cada columna.

### Nombre de servicio
Identifica el servicio de nivel superior al que pertenece este uso.

### Tipo de servicio
El servicio de Azure se puede definir por tipo en esta columna, lo que puede afectar la tarifa.

### Recurso
Identifica la unidad de medida del recurso que se está utilizando.

### Región
Las regiones a las que se aplica el uso y, principalmente, se refiere a servicios en la nube y máquinas virtuales y a transferencias de datos \(excepto CDN\), dado que estas tarifas pueden variar por región. La región para la asignación de CDN a la ubicación del centro de datos donde se realizó el tráfico.

### Consumida
Contiene la cantidad consumida del recurso durante el período de facturación.

### Incluida
Identifica la cantidad incluida en su oferta cada mes.

### Facturable
Si la cantidad Consumida supera la cantidad incluida, esta columna muestra la diferencia. Se le facturará esta cantidad. En el caso de ofertas de pago por uso que no incluyen cantidad en la oferta, este total será igual a la cantidad Consumida.

### Tarifa
Muestra la tarifa que se le cobra por unidad facturable.

### Valor
Muestra el resultado de multiplicar la columna Facturable por la columna Tarifa. Si la cantidad Consumida no supera la cantidad incluida, no habrá cargo en esta columna.

## Analizar los datos de uso diario
Dependiendo del uso, puede haber miles de filas de datos de uso diario. Si desea analizar estos datos, haga clic en Descargar uso para exportar los datos de uso diario correspondientes al período de facturación actual a un archivo variable delimitado por comas \(CSV\), que luego se puede ver en Microsoft Office Excel y otros programas. Como referencia, puede descargar un archivo CSV de muestra.


![Resumen de pago por uso](./media/azure-billing-understand-your-bill/AnalyzeDailyUsageData1.png)

En el archivo CSV, los elementos se desglosan para mostrar una lista de la cantidad de cada recurso que se consumió dentro del período de facturación actual.

![instantánea de csv](./media/azure-billing-understand-your-bill/csvsnapshotportal.png)

Las siguientes columnas muestran detalles que afectan las tarifas al comienzo del período de facturación:

**NOMBRE DE LA COLUMNA** | **DESCRIPCIÓN** |
:---------------| :----------------|
Período de facturación | El período de facturación en que se consumió el recurso.
Nombre | Identifica el nombre del servicio que se utilizó.
Tipo | En algunos casos, un servicio de Azure se puede definir por tipo en esta columna, lo que puede afectar la tarifa.
Recurso | Identifica el tipo de recurso que se ha consumido. Por ejemplo, las transferencia de datos, horas de proceso y transacciones de almacenamiento son tipos de recurso.
Región | Identifica la ubicación del centro de datos para ciertos servicios cuyos precios se establecen según la ubicación del centro de datos \(por ejemplo, transferencias de datos\).
SKU | Identifica el identificador único de sistema de cada recurso de Azure.
Unidad | Identifica la unidad en que se cobra el servicio. Por ejemplo, GB, horas, por 10.000
Consumida | Contiene la cantidad del recurso consumida durante ese día.
Incluida | Contiene la cantidad del recurso incluida sin cargo en el período de facturación actual.
Facturable | Contiene la cantidad del recurso facturable en el período de facturación actual.
Dentro del compromiso | Contiene los cargos de recurso que se restan del importe comprometido asociado a su oferta de 6 o 12 meses. Observe que los cargos de recurso se restan del importe comprometido en orden cronológico.
Moneda | Identifica la moneda reflejada en el período de facturación actual.
Superávit | Contiene los cargos de recurso que superan el importe comprometido asociado a su oferta de 6 o 12 meses.
Tarifa de compromiso | Contiene la tarifa de compromiso basada en el importe comprometido total asociado a su oferta de 6 o 12 meses.
Tarifa | Contiene la tarifa de pago por uso del recurso.
Valor | Multiplica la tarifa por la columna Facturable para calcular el coste total.

Consulte las descripciones detalladas de las columnas anteriores en la sección **Cargos de uso** para obtener explicaciones completas de estas columnas. Las siguientes columnas contienen información adicional que puede resultar útil. Dependiendo del recurso, algunas de estas columnas pueden estar vacías.

### Fecha de uso
La fecha en que se emitió el uso.

### ResourceGUID
El GUID del medidor facturado.

### Subregión
Identifica la ubicación específica del servicio que se omite \(es decir, la ubicación del centro de datos\).

### Servicio
Esta columna se utiliza para hacer un seguimiento del servicio individual de la plataforma Azure que puede no estar específicamente identificado en la columna Nombre. Esta columna Servicio indicará el servicio específico al que pertenece el uso.

### Componente
Donde corresponda, esta columna identifica el recurso que se consume. Por ejemplo, en el caso de servicios de proceso hospedados, esta columna informa el tamaño de la máquina virtual, si todavía no está establecido en Recursos.

### Información de servicio 1
Esta columna proporciona el nombre del proyecto al que pertenecen los servicios de la suscripción.

### Información de servicio 2
Esta columna captura información específica de terceros.

### Información adicional
Esta columna muestra información adicional sobre algunos de los servicios.

Consulte este archivo de fuentes de información adicionales para obtener más detalles. Estos archivos se actualizan mensualmente, se publican el día 25 del mes siguiente y estarán disponibles desde mayo de 2015 en adelante. A continuación se muestra la dirección URL del archivo base de los campos de descarga del servicio:

>>  https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_v1_MMYYYY.xlsx


Para buscar la versión más reciente, escriba el año y el mes \(por ejemplo, para buscar el archivo correspondiente a mayo de 2015, escriba **052015** en la sección _**“MMYYYY”**_ de la dirección URL anterior\). En esta hoja de cálculo aparece una lista de todas las combinaciones posibles de los campos relacionados con el servicio en el **informe de descarga de uso**.


<!--Image references-->

<!--HONumber=52-->
