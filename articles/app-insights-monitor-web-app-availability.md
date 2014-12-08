<properties title="Monitor any web site's availability and responsiveness" pageTitle="Supervise la disponibilidad y capacidad de respuesta de cualquier sitio web" description="Set up web tests in Application Insights. Get alerts if a website becomes unavailable or responds slowly." metaKeywords="analytics web test availability" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-10-30" ms.author="awills" />
 
# Supervise la disponibilidad y capacidad de respuesta de cualquier sitio web

Después de haber implementado la aplicación web, puede configurar pruebas web para supervisar su disponibilidad y capacidad de respuesta. Application Insights enviará solicitudes web a intervalos regulares desde puntos de todo el mundo y puede alertarle si la aplicación responde lentamente o no responde en absoluto.

![Web test example](./media/appinsights/appinsights-10webtestresult.png)

Puede configurar las pruebas web para cualquier extremo de HTTP que sea accesible desde la red pública de Internet.

*¿Es un sitio web de Azure? Simplemente[cree la prueba web en el ][azurewebtest] de la hoja del sitio web.*


1. [¿Crear un nuevo recurso?](#create)
1. [Configuración de una prueba web](#setup)
1. [Ver resultados](#monitor)
2. [Si ve errores...](#failures)
2. [Pruebas web de varios pasos](#multistep)
1. [Modificación o deshabilitación de una prueba](#edit)


 [Vídeo](#video)
 [Pasos siguientes](#next)

## Configuración de una prueba web

### <a name="create"></a>1. ¿Crear un nuevo recurso?

Omita este paso si ya ha [configurado un ][inicio] de recurso de Application Insights para esta aplicación y desea ver los datos de disponibilidad en el mismo lugar.

Suscríbase a [Microsoft Azure](http://azure.com), vaya al [portal de vista previa](https://portal.azure.com) y cree un nuevo recurso de Application Insights. 

![New > Application Insights](./media/appinsights/appinsights-11newApp.png)

### <a name="setup"></a>2. Crear una prueba web

En la hoja de información general de la aplicación, haga clic en el icono de pruebas web. 

![Click the empty availability test](./media/appinsights/appinsights-12avail.png)

*¿Ya tiene pruebas web? Haga clic en el icono de las pruebas web y, a continuación, elija Agregar pruebas web.*

Configure los detalles de la prueba.

![Fill at least the URL of your website](./media/appinsights/appinsights-13availChoices.png)

- **La dirección URL** debe ser visible desde la red pública de Internet. Puede incluir una cadena de consulta, así por ejemplo se podría ejercitar un poco la base de datos. Si la dirección URL se resuelve en una redirección, la seguiremos, con un máximo de 10 redirecciones.

- **Las ubicaciones de prueba** son los lugares desde donde nuestros servidores envían solicitudes web a la URL. Elija dos o tres de tal forma que pueda distinguir los problemas del sitio web de los problemas de red. No puede seleccionar más de tres.

- **Criterios de éxito**:
    **Códigos de retorno de HTTP**: 200 es lo normal. 

Cadena de     **coincidencia de contenido**, como "Bienvenido". Realizaremos una prueba que tenga lugar en todas las respuestas. Debe ser una cadena sin formato, sin caracteres comodín. No se olvide de que si el contenido cambia, es posible que tenga que actualizarla.

- **Alertas**: de manera predeterminada, las alertas se envían si hay fallos repetidos cada 15 minutos. No obstante, puede cambiar este ajuste para aumentar su sensibilidad, y también puede cambiar las direcciones de correo electrónico de notificación.

#### Prueba de más URL

Puede agregar más pruebas para tantas URL como desee. Por ejemplo, además de probar la página principal, podría asegurarse de que la base de datos se está ejecutando probando la URL con una búsqueda.

![On the web tests blade, choose Add](./media/appinsights/appinsights-16anotherWebtest.png)


### <a name="monitor"></a>3. Ver informes de disponibilidad

Transcurridos 1 o 2 minutos, haga clic en Actualizar en la hoja de información general. (En esta versión, no se actualiza automáticamente).

![Summary results on the home blade](./media/appinsights/appinsights-14availSummary.png)

El gráfico de la hoja de información general combina resultados de todas las pruebas web de esta aplicación.

#### Componentes de la página

Como parte de la prueba se solicitan imágenes, hojas de estilos, scripts y otros componentes estáticos.  

El tiempo de respuesta registrado es el tiempo necesario para que se complete la carga de todos los componentes.

Si no se puede cargar alguno de los componentes, la prueba se marca con errores.

## <a name="failures"></a>Si ve errores...

Haga clic en la hoja de pruebas web para ver los resultados independientes de cada prueba.

Abra una prueba web específica.

![Click a specific webtest](./media/appinsights/appinsights-15webTestList.png)

Desplácese hacia abajo hasta **las pruebas con errores** y elija un resultado.

![Click a specific webtest](./media/appinsights/appinsights-17-availViewDetails.png)

El resultado muestra el motivo del error.

![Webtest run result](./media/appinsights/appinsights-18-availDetails.png)

Para obtener más información, descargue el archivo de resultados e inspecciónelo en Visual Studio.



##<a name="multistep"></a>Pruebas web de varios pasos

Puede supervisar un escenario que implique una secuencia de direcciones URL. Por ejemplo, si está supervisando un sitio web de ventas, puede probar que la incorporación de elementos al carro de la compra funciona correctamente. 

Para crear una prueba de varios pasos, grabe el escenario con Visual Studio y, a continuación, cargue la grabación en Application Insights. Application Insights reproducirá el escenario a intervalos y comprobará las respuestas.

#### 1. Grabar un escenario

Utilice Visual Studio Ultimate para grabar una sesión web.

1. Cree un proyecto de prueba de rendimiento web.
    ![In Visual Studio, create a new project from the Web Performance and Load Test template.](./media/appinsights/appinsights-71webtest-multi-vs-create.png)
2. Abra el archivo .webtest y empiece a grabar.
    ![Open the .webtest file and click Record.](./media/appinsights/appinsights-71webtest-multi-vs-start.png)
3. Realice las acciones de usuario que desea simular en la prueba: abra el sitio web, agregue un producto al carro, etc. Después, detenga la prueba. 
    ![The web test recorder runs in Internet Explorer.](./media/appinsights/appinsights-71webtest-multi-vs-record.png)
    No cree un escenario largo. Existe un límite de 100 pasos y 2 minutos.
4. Ejecute la prueba en Visual Studio para asegurarse de que funciona.
    El ejecutor de pruebas web abre un explorador web y repite las acciones grabadas. Asegúrese de que funciona como se esperaba. 
    ![In Visual Studio, open the .webtest file and click Run.](./media/appinsights/appinsights-71webtest-multi-vs-run.png)
 

(No inserte bucles en el código de prueba web).

#### 2. Cargar la prueba web en Application Insights

En el portal de Application Insights, cree una nueva prueba web.

![On the web tests blade, choose Add.](./media/appinsights/appinsights-16anotherWebtest.png)

Seleccione la prueba de varios pasos y cargue el archivo .webtest.

![Select multi-step webtest.](./media/appinsights/appinsights-71webtestUpload.png)

Vea los resultados y los errores de la prueba igual que hace con las pruebas de una sola dirección URL. 

Es habitual que el error se deba a que la prueba se ejecuta durante demasiado tiempo; dos minutos debería ser el máximo.


### Conexión de tiempo y números aleatorios a su prueba de varios pasos

Suponga que está probando una herramienta que obtiene datos que dependen del tiempo, como acciones de una fuente externa. Cuando se graba la prueba web, debe utilizar horas específicas, pero las establece como parámetros de la prueba, StartTime y EndTime.

![A web test with parameters.](./media/appinsights/appinsights-72webtest-parameters.png)

Al ejecutar la prueba, le gustaría que EndTime fuera siempre la hora actual y que StartTime fuera 15 minutos menos.

Los complementos de prueba web proporcionan la manera de hacerlo.

1. Agregue un complemento de prueba de web a cada valor variable que desee. En la barra de herramientas de pruebas web, elija **Agregar complemento de prueba web**.

    ![Choose Add Web Test Plugin and select a type.](./media/appinsights/appinsights-72webtest-plugins.png)

    En este ejemplo, vamos a utilizar dos instancias del complemento de tiempo de fecha. Una instancia es para "hace 15 minutos" y otra para "ahora". 

2. Abra las propiedades de cada complemento. Póngales un nombre y configúrelos para utilizar el tiempo actual. En cada uno de ellos, establezca Añadir minutos = -15.

    ![Set name, Use Current Time, and Add Minutes.](./media/appinsights/appinsights-72webtest-plugin-parameters.png)

3. En los parámetros de prueba en la web, utilice {{plug-in name}} para hacer referencia al nombre de un complemento.

    ![In the test parameter, use {{plug-in name}}.](./media/appinsights/appinsights-72webtest-plugin-name.png)

Ahora puede cargar la prueba en el portal. Utilizará los valores dinámicos en cada ejecución de la prueba.

## <a name="edit"></a> Modificación o deshabilitación de una prueba

Abra una prueba individual para editarla o deshabilitarla.

![Edit or disable a web test](./media/appinsights/appinsights-19-availEdit.png)

Es posible que desee deshabilitar las pruebas web mientras está realizando un mantenimiento en el servicio.

## <a name="video"></a>Vídeo

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>Pasos siguientes

[Buscar registros de diagnóstico][diagnostic]

[Solución de problemas][qna]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




[azurewebtest]: ../insights-create-web-tests/
