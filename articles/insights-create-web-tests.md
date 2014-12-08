<properties title="How to create web test" pageTitle="Creación de una prueba web" description="Learn how to create web tests in Azure." authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-29" ms.author="awills"  />

# Pruebas web para Sitios web de Microsoft Azure
¿Sigue estando su sitio web de Azure en ejecución? ¿Responde de forma correcta y lo suficientemente rápido? Pruebe su sitio web de forma periódica mediante la configuración de una prueba web. Si el sitio deja de estar disponible o responde con lentitud o de forma incorrecta, recibirá una alerta por correo electrónico. Obtendrá gráficos que mostrarán su disponibilidad y su capacidad de respuesta con el paso del tiempo.  

*¿Desea probar otro sitio web? Use la [disponibilidad de][pruebas web de Application Insights] para aplicaciones web que no son Azure.*

![Browse Hub](./media/insights-create-web-tests/Inisghts_WebTestBlade.png)

Puede configurar la supervisión de la disponibilidad para cualquier sitio web de Azure que utilice un plan básico o estándar.  Puede crear hasta tres pruebas web y ejecutar cada una de estas pruebas desde tres ubicaciones geográficas distintas. No es necesario que realice modificaciones en el sitio web.

También puede pausar las pruebas web durante las implementaciones o las interrupciones previstas del servicio para que la disponibilidad general no se vea afectada.  La disponibilidad general se calcula con respecto a todas las pruebas web, incluidas las diferentes ubicaciones seleccionadas.

## Configuración de una prueba web
1. Para configurar una prueba web, asegúrese en primer lugar de que su sitio web sea **básico** o **estándar**.
2. A continuación, seleccione la parte **Prueba web** en el cuadro **Sitio web**:  
    ![Configure Web Tests](./media/insights-create-web-tests/Insights_ConfigurePart.png)
3. En el cuadro **Crear prueba web**, especifique el nombre de la prueba web y la dirección URL para la que se va a ejecutar la prueba.  
    ![Create Web Test](./media/insights-create-web-tests/Insights_CreateTest.png)
4. A continuación, seleccione 3 de las 8 ubicaciones posibles.
5. Especifique los criterios de éxito, incluidas las comprobaciones de código de estado HTTP o el contenido de cadena del propio sitio.
6. A continuación, seleccione la configuración de las alertas, incluida la sensibilidad y el destinatario del correo electrónico.  
    ![Alerts](./media/insights-create-web-tests/Inisghts_AlertCreation.png)
    - Si selecciona una sensibilidad alta, se creará una alerta siempre que se detecte un error de la prueba en una única ubicación.
    - La sensibilidad media requiere que al menos la mitad de las ubicaciones hayan sufrido un error en 10 minutos.
    - La sensibilidad baja requiere que la prueba haya dado error en un lapso de 15 minutos en todas las ubicaciones.

Una vez que haya terminado, haga clic en el botón **Crear**. Después de haber creado la prueba web, se ejecutará cada 5 minutos desde las ubicaciones especificadas, por lo que los datos podrían tardar en aparecer un rato.

### ¿Para qué sirven las ubicaciones?
Enviamos una solicitud al sitio web desde esas ubicaciones, de la misma forma que los usuarios tienen acceso al sitio desde diferentes partes del mundo. Si su sitio deja de estar disponible en EE. UU. pero lo sigue estando en Europa, sabrá que el problema es un error de la red, en vez de un error de su servidor.

### Uso de criterios de éxito
Normalmente, querrá probar que el código de estado HTTP es 200, lo cual indica que el servidor ha reconocido el URI y devolverá una página.

No puede utilizar caracteres comodín en la cadena de coincidencia de contenido, pero puede probar cualquier fragmento de texto sin formato.

## Mi sitio no está disponible
Si la prueba web no supera los criterios de éxito, la prueba se calificará como errónea y se reducirá la disponibilidad general de su sitio web. Las pruebas erróneas (así como las pruebas satisfactorias) se muestran en un apartado del cuadro específico de la prueba web.  

![Failed Test](./media/insights-create-web-tests/Insights_FailedWebTest.png)

Las pruebas erróneas se pueden analizar para determinar el motivo del error.  Entre en una prueba web errónea y descargue y abra el archivo de resultados de la prueba web de Visual Studio para analizarlo y comprender el motivo del error de la prueba.

![Open in VS](./media/insights-create-web-tests/Insights_OpenInVS.png)

[availability]: ../app-insights-monitor-web-app-availability/
