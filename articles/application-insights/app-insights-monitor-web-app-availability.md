---
title: "Supervisión de la disponibilidad y la capacidad de respuesta de cualquier sitio web | Microsoft Docs"
description: Configure pruebas web en Application Insights. Obtenga alertas si un sitio web deja de estar disponible o responde con lentitud.
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/25/2017
ms.author: bwren
ms.openlocfilehash: 6c7f52fc3998b0b29301206ffbc6a5a0c4134f6a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Supervisión de la disponibilidad y la capacidad de respuesta de cualquier sito web
Después de haber implementado la aplicación web o el sitio web en cualquier servidor, puede configurar pruebas para supervisar su disponibilidad y capacidad de respuesta. [Azure Application Insights](app-insights-overview.md) envía solicitudes web a su aplicación a intervalos regulares desde puntos de todo el mundo. Le alerta si la aplicación no responde o lo hace lentamente.

Puede configurar pruebas de disponibilidad para cualquier punto de conexión HTTP o HTTPS que sea accesible desde la red pública de Internet. No tiene que agregar nada en el sitio web que se está probando. Ni siquiera tiene que ser su sitio: puede probar un servicio de API de REST de los que dependa.

Hay dos tipos de pruebas de disponibilidad:

* [Prueba de ping de la dirección URL](#create): una prueba sencilla que se puede crear en el portal de Azure.
* [Prueba web de varios pasos](#multi-step-web-tests): que se crea en Visual Studio Enterprise y se carga en el portal.

Puede crear hasta 25 pruebas de disponibilidad por recurso de aplicación.

## <a name="create"></a>1. Apertura de un recurso para los informes de pruebas de disponibilidad

**Si ya ha configurado Application Insights** para la aplicación web, abra el recurso de Application Insights en [Azure Portal](https://portal.azure.com).

**O, si desea ver los informes en un nuevo recurso,**  suscríbase a [Microsoft Azure](http://azure.com), vaya a [Azure Portal](https://portal.azure.com) y cree un recurso de Application Insights.

![New > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Haga clic en **Todos los recursos** para abrir la hoja Información general del nuevo recurso.

## <a name="setup"></a>2. Creación de una prueba de ping de la dirección URL
Abra la hoja Disponibilidad y agregue una prueba.

![Fill at least the URL of your website](./media/app-insights-monitor-web-app-availability/13-availability.png)

* **La dirección URL** puede ser cualquier página web que desee probar, pero debe ser visible desde la red pública de Internet. La dirección URL puede incluir una cadena de consulta. Así, por ejemplo, se puede ejercitar un poco la base de datos. Si la dirección URL se resuelve en una redirección, la seguimos, hasta 10 redirecciones.
* **Analizar solicitudes dependientes**: si se activa esta opción, la prueba solicitará imágenes, scripts, archivos de estilo y otros archivos que forman parte de la página web en pruebas. El tiempo de respuesta registrado incluye el tiempo dedicado a obtener estos archivos. La prueba da error si todos estos recursos no se pueden descargar correctamente dentro del tiempo de espera de la prueba entera. 

    Si la opción no está activada, la prueba solo solicita el archivo en la dirección URL que especificó.
* **Habilitar reintentos**: si esta opción está activa, cuando la prueba da error, se reintenta tras un corto intervalo. Se notifica un error únicamente si los tres intentos sucesivos producen un error. Las sucesivas pruebas se realizan según la frecuencia habitual de la prueba. El reintento se suspende temporalmente hasta que uno se complete correctamente. Esta regla se aplica independientemente en cada ubicación de la prueba. Se recomienda esta opción. Como media, cerca del 80 % de los errores desaparecen al reintentar.
* **Frecuencia de prueba**: establece la frecuencia con que se ejecuta la prueba desde cada ubicación de prueba. Con una frecuencia de cinco minutos y cinco ubicaciones de prueba, el sitio se prueba, de media, cada minuto.
* **ubicaciones de prueba** son los lugares desde donde nuestros servidores envían solicitudes web a la dirección URL. Elija más de una de tal forma que pueda distinguir los problemas del sitio web a partir de los problemas de red. Puede seleccionar hasta 16 ubicaciones.
* **Criterios de éxito**:

    **Tiempo de espera de prueba**: reduzca este valor para recibir una alerta sobre las respuestas lentas. La prueba se considera un error si no se han recibido respuestas de su sitio dentro de este período. Si seleccionó **Analizar solicitudes dependientes**, todas las imágenes, archivos de estilo, scripts y otros recursos dependientes se deben haber recibido durante este período.

    **Respuesta HTTP**: el código de estado devuelto que se considera correcto. 200 es el código que indica que se ha devuelto una página web normal.

    **Coincidencia de contenido**: una cadena, como "Bienvenido". Probamos que se produce una coincidencia exacta entre mayúsculas y minúsculas en todas las respuestas. Debe ser una cadena sin formato, sin caracteres comodín. No se olvide de que si el contenido cambia, es posible que tenga que actualizarla.
* **alertas** cuando hay errores en tres ubicaciones durante cinco minutos. Es probable que un error en una ubicación sea un problema de red y no un problema con su sitio. No obstante, puede cambiar el umbral a más o menos sensible, y también puede cambiar las personas a quienes se deben enviar los correos electrónicos.

    Puede configurar un [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) que se llama cuando se genera una alerta. (Pero tenga en cuenta que, en la actualidad, los parámetros de consulta no se pasan como propiedades).

### <a name="test-more-urls"></a>Prueba de más URL
Agregue más pruebas. Por ejemplo, además de probar la página principal, puede asegurarse de que la base de datos se está ejecutando si prueba la URL con una búsqueda.


## <a name="monitor"></a>3. Visualización de los resultados de las pruebas de disponibilidad

Después de unos minutos, haga clic en **Actualizar** para ver los resultados de las pruebas. 

![Summary results on the home blade](./media/app-insights-monitor-web-app-availability/14-availSummary-3.png)

En el gráfico de dispersión se muestran ejemplos de los resultados de las pruebas, que incluyen detalles sobre los pasos de las pruebas de diagnóstico. El motor de pruebas almacena los detalles de diagnóstico de las pruebas con errores. En el caso de las pruebas correctas, los detalles de diagnóstico se almacenan para un subconjunto de las ejecuciones. Mantenga el puntero sobre cualquiera de los puntos rojos o verdes para ver la marca de tiempo, duración, ubicación y nombre de la prueba. Haga clic en cualquier punto del gráfico de dispersión para ver los detalles del resultado de la prueba.  

Seleccione una prueba o una ubicación determinadas, o bien reduzca el período de tiempo para ver más resultados del período de tiempo que le interese. Use el Explorador de búsqueda para ver los resultados de todas las ejecuciones, o use consultas de Analytics para ejecutar informes personalizados en estos datos.

Además de los resultados sin formato, hay dos métricas de disponibilidad en el Explorador de métricas: 

1. Disponibilidad: porcentaje de las pruebas que obtuvieron resultados satisfactorios en todas las ejecuciones de prueba. 
2. Duración de la prueba: duración media de las pruebas en todas las ejecuciones de prueba.

Puede aplicar filtros en el nombre de la prueba y la ubicación para analizar las tendencias de una prueba o ubicación determinadas.

## <a name="edit"></a> Inspección y edición de pruebas

En la página de resumen, seleccione una prueba concreta. Allí, puede ver sus resultados específicos y editarla o deshabilitarla temporalmente.

![Edit or disable a web test](./media/app-insights-monitor-web-app-availability/19-availEdit-3.png)

Tal vez le interese deshabilitar las pruebas de disponibilidad o las reglas de alerta asociadas a ellas mientras esté realizando el mantenimiento del servicio. 

## <a name="failures"></a>Si ve errores
Haga clic en un punto rojo.

![Click a red dot](./media/app-insights-monitor-web-app-availability/open-instance-3.png)


Desde un resultado de la prueba de disponibilidad, puede hacer lo siguiente:

* Inspeccionar la respuesta recibida desde el servidor.
* Abrir la telemetría enviada por la aplicación de servidor durante el procesamiento de la instancia de solicitud con error.
* Registrar un problema o elemento de trabajo en GIT o VSTS para realizar un seguimiento del problema. El error contiene un vínculo a este evento.
* Abra el resultado de la prueba web en Visual Studio.


*¿Parece que se ha completado correctamente pero se notifica como un error?* Compruebe todas las imágenes, los scripts, las hojas de estilo y cualquier otro archivo cargado que haya cargado la página. Si se produce un error en cualquiera de ellos, se notifica que la prueba ha concluido con errores, incluso si la página html principal se carga correctamente.

*¿No hay elementos relacionados?* Si tiene Application Insights configurado para la aplicación de servidor, esto puede deberse a que se está llevando a cabo un [muestreo](app-insights-sampling.md). 

## <a name="multi-step-web-tests"></a>Pruebas web de varios pasos
Puede supervisar un escenario que implique una secuencia de direcciones URL. Por ejemplo, si está supervisando un sitio web de ventas, puede probar que la incorporación de elementos al carro de la compra funciona correctamente.

> [!NOTE] 
> Las pruebas web de varios pasos conllevan un coste. [Esquema de precios](http://azure.microsoft.com/pricing/details/application-insights/).
> 

Para crear una prueba de varios pasos, grabe el escenario con Visual Studio Enterprise y, a continuación, cargue la grabación en Application Insights. Application Insights reproduce el escenario a intervalos y comprueba las respuestas.

> [!NOTE]
> No puede usar funciones codificadas ni bucles en las pruebas. La prueba debe estar contenida completamente en el script .webtest. Sin embargo, puede usar complementos estándar.
>

#### <a name="1-record-a-scenario"></a>1. Grabar un escenario
Utilice Visual Studio Enterprise para grabar una sesión web.

1. Cree un proyecto de prueba de rendimiento web.

    ![En Visual Studio Enterprise, cree un proyecto a partir de la plantilla de prueba de carga y rendimiento web.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

 * *¿No ve la plantilla de prueba de carga y rendimiento web?* - Cierre Visual Studio Enterprise. Abra **Visual Studio Installer** para modificar la instalación de Visual Studio Enterprise. En **Componentes individuales**, seleccione **Herramientas de prueba de carga y rendimiento web**.

2. Abra el archivo .webtest y empiece a grabar.

    ![Abra el archivo .webtest y haga clic en Grabar.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Realice las acciones del usuario que desea simular en la prueba: abra el sitio web, agregue un producto al carro, etc. Después, detenga la prueba.

    ![Se ejecuta la grabadora de la prueba web en Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    No cree un escenario largo. Existe un límite de 100 pasos y 2 minutos.
4. Edite la prueba para:

   * Agregar validaciones que comprueben el texto recibido y los códigos de respuesta.
   * Quite las interacciones que sean superfluas. También puede quitar las solicitudes dependientes relacionadas con imágenes o con sitios de anuncios o de seguimiento.

     Recuerde que solo se puede editar el script de prueba: no se puede agregar código personalizado ni llamar a otras pruebas web. No inserte bucles en la prueba. Puede utilizar complementos de prueba web estándar.
5. Ejecute la prueba en Visual Studio para asegurarse de que funciona.

    El ejecutor de pruebas web abre un explorador web y repite las acciones grabadas. Asegúrese de que funciona como se esperaba.

    ![En Visual Studio, abra el archivo .webtest y haga clic en Ejecutar.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2. Cargar la prueba web en Application Insights
1. En el portal de Application Insights, cree una prueba web.

    ![En la hoja de pruebas web, seleccione Agregar.](./media/app-insights-monitor-web-app-availability/16-another-test.png)
2. Seleccione la prueba de varios pasos y cargue el archivo .webtest.

    ![Seleccione la prueba web de varios pasos.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    Establezca las ubicaciones de prueba, la frecuencia y los parámetros de alerta de la misma manera que para las pruebas de ping.

#### <a name="3-see-the-results"></a>3. Consultar los resultados

Vea los resultados y los errores de la prueba igual que hace con las pruebas de una sola dirección URL.

Además, puede descargar los resultados de la prueba para verlos en Visual Studio.

#### <a name="too-many-failures"></a>¿Hay demasiados errores?

* Un motivo habitual de error es que la prueba se ejecuta durante demasiado tiempo. No deben ejecutar durante más de dos minutos.

* No olvide que todos los recursos de una página se deben cargar correctamente para que la prueba sea correcta, incluidos los scripts, las hojas de estilo, las imágenes, etc.

* La prueba web debe estar contenida totalmente en el script .webtest: no puede usar las funciones codificadas en la prueba.

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>Conexión de tiempo y números aleatorios a su prueba de varios pasos
Suponga que está probando una herramienta que obtiene datos que dependen del tiempo, como acciones de una fuente externa. Cuando se graba la prueba web, debe utilizar horas específicas, pero las establece como parámetros de la prueba, StartTime y EndTime.

![Una prueba web con parámetros.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

Al ejecutar la prueba, le gustaría que EndTime fuera siempre la hora actual y que StartTime fuera 15 minutos menos.

Los complementos de prueba web proporcionan la manera de parametrizar los tiempos.

1. Agregue un complemento de prueba de web a cada valor variable que desee. En la barra de herramientas de pruebas web, elija **Agregar complemento de prueba web**.

    ![Elija Agregar complemento de prueba web y seleccione un tipo.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    En este ejemplo, usamos dos instancias del complemento Date Time Plug-in. Una instancia es para "hace 15 minutos" y otra para "ahora".
2. Abra las propiedades de cada complemento. Asígnele un nombre y configúrelo para utilizar la hora actual. En cada uno de ellos, establezca Añadir minutos = -15.

    ![Establezca el nombre, Usar hora actual y Agregar minutos.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. En los parámetros de prueba en la web, utilice {{plug-in name}} para hacer referencia al nombre de un complemento.

    ![En el parámetro de la prueba, utilice {{plug-in name}}.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Ahora puede cargar la prueba en el portal. Utiliza los valores dinámicos en cada ejecución de la prueba.

## <a name="dealing-with-sign-in"></a>Tratamiento del inicio de sesión
Si los usuarios inician sesión en la aplicación, tiene varias opciones para simular el inicio de sesión a fin de poder probar páginas detrás del inicio de sesión. El enfoque que utilice dependerá del tipo de seguridad proporcionada por la aplicación.

En todos los casos, debe crear una cuenta en su aplicación para realizar pruebas. Si es posible, restrinja los permisos de esta cuenta de prueba para que sea totalmente imposible que las pruebas web afecten a usuarios reales.

### <a name="simple-username-and-password"></a>Nombre de usuario y la contraseña simples
Grabe una prueba web de la forma habitual. Elimine las cookies en primer lugar.

### <a name="saml-authentication"></a>Autenticación SAML
Utilice el complemento SAML que está disponible para las pruebas web.

### <a name="client-secret"></a>Secreto del cliente
Si la aplicación tiene una ruta de inicio de sesión que implica un secreto de cliente, utilícela. Azure Active Directory (AAD) es un ejemplo de un servicio que proporciona inicio de sesión del secreto de cliente. En AAD, el secreto de cliente es la clave de la aplicación.

Esta es una prueba web de ejemplo de una aplicación web de Azure que usa una clave de aplicación:

![Ejemplo de secreto de cliente](./media/app-insights-monitor-web-app-availability/110.png)

1. Obtener el token de AAD mediante el secreto de cliente (AppKey).
2. Extraer el token de portador de la respuesta.
3. Llamar a la API mediante el token de portador del encabezado de la autorización.

Asegúrese de que la prueba web es un cliente real (es decir, tiene su propia aplicación en AAD) y utilice su clientId + appkey. El servicio que se prueba también tiene su propia aplicación en AAD: el identificador URI de esta aplicación, appID, se refleja en la prueba web en el campo "resource".

### <a name="open-authentication"></a>Autenticación abierta
Un ejemplo de autenticación abierta es el iniciar sesión con una cuenta de Microsoft o Google. Muchas aplicaciones que utilizan OAuth proporcionan la alternativa del secreto de cliente, por lo que la primera táctica sería investigar dicha posibilidad.

Si la prueba debe iniciar sesión con OAuth, el enfoque general es el siguiente:

* Utilice una herramienta como Fiddler para examinar el tráfico entre el explorador web, el sitio de autenticación y la aplicación.
* Realice dos o más inicios de sesión mediante distintas máquinas o exploradores o en intervalos largos de tiempo (para permitir que los tokens expiren).
* Mediante la comparación de diferentes sesiones, identifique el token pasado desde el sitio de autenticación que, a continuación, se pasa al servidor de aplicaciones después de iniciar sesión.
* Grabe una prueba web con Visual Studio.
* Parametrice los tokens, estableciendo el parámetro cuando se devuelve el token desde el autenticador y utilizándolo en la consulta al sitio.
  (Visual Studio intenta parametrizar la prueba pero no parametriza correctamente los tokens).


## <a name="performance-tests"></a>Pruebas de rendimiento
Puede ejecutar una prueba de carga en el sitio web. Al igual que la prueba de disponibilidad, puede enviar solicitudes sencillas o solicitudes de varios pasos desde nuestros puntos de todo el mundo. A diferencia de una prueba de disponibilidad, se envían muchas solicitudes, que simulan a varios usuarios simultáneos.

En la hoja Información general, abra **Configuración**, **Pruebas de rendimiento**. Cuando crea una prueba, se le invitará a conectarse o a crear una cuenta de Visual Studio Team Services.

Una vez finalizada la prueba, se muestran los tiempos de respuesta y las tasas de éxito.


![Prueba de rendimiento](./media/app-insights-monitor-web-app-availability/perf-test.png)

> [!TIP]
> Para observar los efectos de una prueba de rendimiento, utilice [Live Stream](app-insights-live-stream.md) y el [Generador de perfiles](app-insights-profiler.md).
>

## <a name="automation"></a>Automation
* [Use scripts de PowerShell para configurar una prueba de disponibilidad](app-insights-powershell.md#add-an-availability-test) automáticamente.
* Configure un [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) que se llama cuando se genera una alerta.

## <a name="qna"></a>¿Tiene preguntas? ¿Tiene problemas?
* *¿Puedo llamar el código desde mi prueba web?*

    No. Los pasos de la prueba deben encontrarse en el archivo .webtest. Y no se puede llamar a otras pruebas web ni utilizar bucles. Pero hay varios complementos que pueden resultarle útiles.
* *¿Se admite HTTPS?*

    Admitimos TLS 1.1 y TLS 1.2.
* *¿Existe alguna diferencia entre las "pruebas web" y las "pruebas de disponibilidad"?*

    Los dos términos se pueden usar indistintamente. El término "pruebas de disponibilidad" es más genérico, e incluye las pruebas de ping de dirección URL única y las pruebas web de varios pasos.
* *Me gustaría usar pruebas de disponibilidad en nuestro servidor interno que se ejecuta detrás de un firewall.*

    Hay dos soluciones posibles:
    
    * Configure el firewall para que permita las solicitudes entrantes de las [direcciones IP de los agentes de prueba web](app-insights-ip-addresses.md).
    * Escriba su propio código para comprobar periódicamente el servidor interno. Ejecute el código como un proceso en segundo plano en un servidor de prueba detrás del firewall. El proceso de prueba puede enviar sus resultados a Application Insights mediante la API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) en el paquete de SDK principal. Para ello, es necesario que el servidor de prueba tenga acceso saliente al punto de conexión de ingesta de Application Insights, pero plantea un riesgo de seguridad mucho menor que la alternativa de permitir las solicitudes entrantes. Los resultados no se mostrarán en las hojas de las pruebas web de disponibilidad, pero sí aparecerán como resultados de disponibilidad en Analytics, Search y en el Explorador de métricas.
* *Al cargar una prueba web de varios pasos, se produce un error*

    Hay un límite de tamaño de 300 000.

    No se admiten los bucles.

    No se admiten referencias a otras pruebas web.

    No se admiten orígenes de datos.
* *No se completa la prueba de varios pasos*

    Hay un límite de 100 solicitudes por prueba.

    La prueba se detiene si se ejecuta durante más de dos minutos.
* *¿Cómo se puede ejecutar una prueba con certificados de cliente?*

    Lo sentimos, pero eso no está admitido.


## <a name="next"></a>Pasos siguientes
[Búsqueda de registros de diagnóstico][diagnostic]

[Solución de problemas][qna]

[Direcciones IP de agentes de pruebas web](app-insights-ip-addresses.md)

<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
