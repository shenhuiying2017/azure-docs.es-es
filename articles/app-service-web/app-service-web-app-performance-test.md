---
title: "Prueba del rendimiento de una aplicación web de Azure | Microsoft Docs"
description: "Ejecute pruebas de rendimiento de aplicación web de Azure para la manera en que su aplicación controla la carga del usuario. Mida el tiempo de respuesta y encuentre los errores que puedan indicar problemas."
services: app-service\web
documentationcenter: 
author: ecfan
manager: douge
editor: jimbe
ms.assetid: e4858f40-038d-4039-a3d4-beee7960ac7d
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2016
ms.author: estfan; manasma; ahomer
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 71f0a6e8d63e8f9ebb9307cce051010649a31a53


---
# <a name="performance-test-your-azure-web-app-under-load"></a>Realizar una prueba de tu aplicación web de Azure bajo carga
Compruebe el rendimiento de su aplicación web antes de iniciarla o de implementar actualizaciones en la producción. De esa manera, puede evaluar mejor si su aplicación está lista para publicarse. Siéntase más seguro de que su aplicación puede controlar el tráfico durante el uso máximo o en su siguiente inserción de marketing.

Durante la vista previa pública, puede probar el rendimiento de su aplicación de forma gratuita en el Portal de Azure.
Estas pruebas simulan la carga del usuario en la aplicación durante un período de tiempo específico y miden la respuesta de la aplicación. Por ejemplo, los resultados de las pruebas muestran la rapidez de la aplicación para responder a un número específico de usuarios. También muestran cuántas solicitudes tienen error, lo que podría indicar problemas con su aplicación.      

![Encontrar problemas de rendimiento en su aplicación web](./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png)

## <a name="before-you-start"></a>Antes de comenzar
* Necesitará una [suscripción de Azure](https://account.windowsazure.com/subscriptions), si no tiene ya una. Aprenda cómo puede [abrir una cuenta de Azure de forma gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
* Necesitará una cuenta de [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) para mantener su historial de pruebas de rendimiento. Se creará automáticamente una cuenta adecuada al configurar la prueba de rendimiento. O bien puede crear una cuenta nueva o utilizar una existente si esta última le pertenece. 
* Implemente su aplicación para realizar pruebas en un entorno que no sea de producción. 
  Haga que su aplicación use un plan de Servicio de aplicaciones que no sea el plan usado en la producción. 
  De esa manera, no afectará a los clientes existentes ni ralentizará la aplicación en producción. 

## <a name="set-up-and-run-your-performance-test"></a>Configurar y ejecutar la prueba de rendimiento
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com). 
   Para usar una cuenta de Visual Studio Team Services que posea, inicie sesión como propietario de la cuenta.
2. Vaya a la aplicación web.
   
   ![Ir a Examinar todo, Aplicaciones web, su aplicación web](./media/app-service-web-app-performance-test/azure-np-web-apps.png)
3. Vaya a **Prueba de rendimiento**.
   
   ![Ir a Herramientas, Prueba de rendimiento](./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png)
4. Ahora vinculará una cuenta de [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) para mantener su historial de pruebas de rendimiento.
   
    Si tiene una cuenta de Team Services que pueda usar, seleccione esa cuenta. Si no es así, cree una cuenta nueva.
   
    ![Seleccionar la cuenta de Team Services existente o crear una cuenta nueva](./media/app-service-web-app-performance-test/azure-np-no-vso-account.png)
5. Crear su prueba de rendimiento. Establezca los detalles y ejecute la prueba. 

Puede ver los resultados en tiempo real mientras se ejecuta la prueba.

Por ejemplo, supongamos que tenemos una aplicación que repartió cupones en las ventas delas  vacaciones del año pasado. Este evento duró 15 minutos con una carga máxima de 100 clientes simultáneos. Queremos duplicar el número de clientes este año. También queremos mejorar la satisfacción del cliente, reduciendo el tiempo de carga de página de 5 a 2 segundos. Por tanto, probaremos el rendimiento de nuestra aplicación actualizada con 250 usuarios durante 15 minutos.

Simularemos la carga en nuestra aplicación generando usuarios virtuales (clientes) que visitan nuestro sitio web a la vez. Esto nos mostrará cuántas solicitudes generan error o responden lentamente.

  ![Crear, configurar y ejecutar la prueba de rendimiento](./media/app-service-web-app-performance-test/azure-np-new-performance-test.png)

* La dirección URL predeterminada de su aplicación web se agrega automáticamente. 
  Puede cambiar la dirección URL para probar otras páginas (solo solicitudes HTTP GET).
* Para simular las condiciones locales y reducir la latencia, seleccione una ubicación más cercana a los usuarios para generar la carga.
  
  Esta es la prueba en curso. Durante el primer minuto, nuestra página se carga con mayor lentitud de la deseada.
  
  ![Prueba de rendimiento en curso con datos en tiempo real](./media/app-service-web-app-performance-test/azure-np-running-perf-test.png)
  
  Después de realiza la prueba, nos damos cuenta de que la página se carga con mayor rapidez después del primer minuto. Esto ayuda a identificar dónde podemos querer iniciar la solución del problema.
  
  ![La prueba de rendimiento completada muestra resultados, incluidas solicitudes con error](./media/app-service-web-app-performance-test/azure-np-perf-test-done.png)

## <a name="test-multiple-urls"></a>Prueba de varias direcciones URL
También puede ejecutar pruebas de rendimiento en las que se incluyan varias direcciones URL que representen un escenario de usuario de un extremo a otro cargando un archivo de prueba web de Visual Studio. Estas son algunos de los procedimientos que puede seguir para crear un archivo de prueba web de Visual Studio:

* [Capturar el tráfico mediante Fiddler y exportarlo como un archivo de prueba web de Visual Studio](http://docs.telerik.com/fiddler/Save-And-Load-Traffic/Tasks/VSWebTest)
* [Crear un archivo de prueba de carga en Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)

Para cargar y ejecutar un archivo de prueba web de Visual Studio:

1. Siga los pasos anteriores para abrir la hoja **Nueva prueba de rendimiento** .
   En esta hoja, elija la opción CONFIGURE TEST USING (CONFIGURAR PRUEBA CON) para abrir la hoja **Configure test using** (Configurar prueba con).  
   
    ![Apertura de la hoja Configure load testing (Configurar prueba de carga)](./media/app-service-web-app-performance-test/multiple-01-authoring-blade.png)
2. Compruebe que el campo TEST TYPE (TIPO DE PRUEBA) esté establecido en **Visual Studio Web Test** (Prueba web de Visual Studio) y seleccione el archivo de almacenamiento de HTTP.
    Utilice el icono de la carpeta para abrir el cuadro de diálogo del selector de archivos.
   
    ![Carga de un archivo de prueba web de Visual Studio con varias URL](./media/app-service-web-app-performance-test/multiple-01-authoring-blade2.png)
   
    Una vez que se haya cargado el archivo, verá la lista de direcciones URL que se van a probar en la sección URL DETAILS (DETALLES DE URL).
3. Especifique la carga de usuarios y la duración de la prueba; después, elija **Ejecutar prueba**.
   
    ![Selección de la carga de usuarios y la duración](./media/app-service-web-app-performance-test/multiple-01-authoring-blade3.png)
   
    Una vez que se haya completado la prueba, verá los resultados en dos paneles. En el panel izquierdo se muestra la información de rendimiento como una serie de gráficos.
   
    ![El panel de resultados de rendimiento](./media/app-service-web-app-performance-test/multiple-01a-results.png)
   
    En el panel derecho se muestra una lista de solicitudes erróneas, con el tipo de error y el número de veces que se ha producido.
   
    ![El panel de errores de solicitud](./media/app-service-web-app-performance-test/multiple-01b-results.png)
4. Vuelva a ejecutar la prueba eligiendo el icono **Volver a ejecutar** situado en la parte superior del panel derecho.
   
    ![Nueva ejecución de la prueba](./media/app-service-web-app-performance-test/multiple-rerun-test.png)

## <a name="q--a"></a>Preguntas y respuestas
#### <a name="q-is-there-a-limit-on-how-long-i-can-run-a-test"></a>P: ¿Hay un límite en el tiempo durante el cual puedo ejecutar una prueba?
**R**: Sí, puede ejecutar la prueba durante una hora, como máximo, en el Portal de Azure.

#### <a name="q-how-much-time-do-i-get-to-run-performance-tests"></a>P: ¿Cuánto tiempo puedo ejecutar las pruebas de rendimiento?
**R**: Tras la versión preliminar pública, obtendrá 20 000 minutos de usuarios virtuales (VUM) de manera gratuita cada mes con su cuenta de Visual Studio Team Services. Un VUM es el número de usuarios virtuales multiplicados por el número de minutos de su prueba. Si sus necesidades superan el límite gratuito, puede adquirir más tiempo y pagar solo por lo que use.

#### <a name="q-where-can-i-check-how-many-vums-ive-used-so-far"></a>P: ¿Dónde puedo comprobar cuántos VUM he usado hasta ahora?
**R**: Puede comprobar esta cantidad en el Portal de Azure.

![Ir a la cuenta de Team Services](./media/app-service-web-app-performance-test/azure-np-vso-accounts.png)

![Comprobar los VUM usados](./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png)

#### <a name="q-what-is-the-default-option-and-are-my-existing-tests-impacted"></a>P.: ¿Cuál es la opción predeterminada? ¿Se verán afectadas las pruebas existentes?
**R**: La opción predeterminada para las pruebas de carga de rendimiento es una prueba manual (la misma que antes de que se agregara la opción de prueba de varias URL al portal).
Las pruebas existentes seguirán utilizando la dirección URL configurada y funcionarán como antes.

#### <a name="q-what-features-not-supported-in-the-visual-studio-web-test-file"></a>P.: ¿Qué características no se admiten en el archivo de prueba web de Visual Studio?
**R**: En este momento, esta característica no admite complementos de pruebas web, orígenes de datos ni reglas de extracción. Tendrá que editar el archivo de prueba web para quitarlos. Esperamos incorporar la compatibilidad con estas funciones en futuras actualizaciones.

#### <a name="q-does-it-support-any-other-web-test-file-formats"></a>P.: ¿Se admiten otros formatos de archivo de prueba web?
**R**: Ahora mismo, solo se admiten archivos de formato de prueba web de Visual Studio.
No dude hacernos saber si necesita que se admitan otros formatos de archivo. Envíenos un correo electrónico a [vsoloadtest@microsoft.com](mailto:vsoloadtest@microsoft.com).

#### <a name="q-what-else-can-i-do-with-a-visual-studio-team-services-account"></a>P: ¿Qué más puedo hacer con una cuenta de Visual Studio Team Services?
**R**: Para buscar una cuenta nueva, vaya a ```https://{accountname}.visualstudio.com```. Comparta su código, compile, pruebe, realice un seguimiento del trabajo y entregue software: todo ello en la nube con cualquier lenguaje o herramienta. Obtenga más información sobre la manera en que las características y los servicios de [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) ayudan al equipo a colaborar con mayor facilidad y a implementar de forma continua.

## <a name="see-also"></a>Consulte también
* [Ejecución de pruebas de rendimiento en la nube simples](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-simple-cloud-load-test)
* [Ejecución de pruebas de rendimiento de Apache Jmeter](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-jmeter-test)
* [Grabación y reproducción de pruebas de carga basadas en la nube](https://www.visualstudio.com/docs/test/performance-testing/getting-started/record-and-replay-cloud-load-tests)
* [Ejecución de pruebas de rendimiento en su aplicación en la nube](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)




<!--HONumber=Nov16_HO3-->


