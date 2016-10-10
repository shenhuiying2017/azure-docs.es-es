<properties
  pageTitle="Preguntas más frecuentes sobre el conjunto de aplicaciones Azure IoT | Microsoft Azure"
  description="Preguntas más frecuentes sobre el Conjunto de aplicaciones de IoT"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="09/26/2016"
  ms.author="araguila"/>
   
# Preguntas más frecuentes sobre el Conjunto de aplicaciones de IoT

### ¿Cuál es la diferencia entre eliminar un grupo de recursos en el Portal de Azure y hacer clic en Eliminar en una solución preconfigurada en azureiotsuite.com?

- Si elimina la solución preconfigurada en [azureiotsuite.com][lnk-azureiotsuite], eliminará todos los recursos aprovisionados cuando se creó la solución preconfigurada; si agregó recursos adicionales al grupo de recursos, estos también se eliminan.

- Si elimina el grupo de recursos en el [Portal de Azure][lnk-azure-portal], solo eliminará los recursos de dicho grupo de recursos; también tendrá que eliminar la aplicación de Azure Active Directory asociada a la solución preconfigurada en el [Portal de Azure clásico][lnk-classic-portal].

### ¿Cuántas instancias del Centro de IoT se pueden aprovisionar en una suscripción? 

Diez. Para aumentar este límite se puede crear una [incidencia de soporte técnico de Azure][link-azuresupportticket]; pero, de forma predeterminada, solo se pueden aprovisionar diez Centros de IoT por suscripción, como se describe en [Límites de suscripciones de Azure][link-azuresublimits]. Como consecuencia, dado que todas las soluciones preconfiguradas aprovisionan un Centro de IoT nuevo, solo se pueden aprovisionar diez soluciones preconfiguradas en una suscripción dada.

### ¿Cuántas instancias de DocumentDB se pueden aprovisionar en una suscripción?

Cincuenta. Para aumentar este límite se puede crear una [incidencia de soporte técnico de Azure][link-azuresupportticket]; pero, de forma predeterminada, solo se pueden aprovisionar cincuenta instancias de DocumentDB por suscripción.

### ¿Cuántas API de Mapas de Bing gratis se pueden aprovisionar en una suscripción?

Dos. Solo puede crear dos planes de Mapas de Bing para empresa de nivel 1 de transacciones internas en una suscripción de Azure. La solución de supervisión se aprovisiona de forma predeterminada con el plan de nivel 1 de transacciones internas. Como consecuencia, solo se pueden aprovisionar un máximo de dos soluciones de supervisión remota en una suscripción sin modificaciones.

### Tengo una implementación de soluciones de supervisión remota con un mapa estático, ¿cómo se puede agregar un mapa Bing interactivo? 
1. Obtenga la QueryKey de la API de Mapas de Bing para empresa en el [Portal de Azure][lnk-azure-portal]\:
 1. Navegue al grupo de recursos donde está la API de Mapas de Bing para empresa en el [Portal de Azure][lnk-azure-portal].
 2. Haga clic en Toda la configuración y después en Administración de claves.
 3. Verá dos claves: MasterKey y QueryKey. Copie el valor de QueryKey.

     > [AZURE.NOTE] ¿No tiene una cuenta de la API de Mapas de Bing para empresa? Para crear una cuenta en el [Portal de Azure][lnk-azure-portal] haga clic en + Nuevo, busque la API de Mapas de Bing para empresa y siga las indicaciones para crearla.

2. Descargue el código más reciente de [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].

3. Ejecute una implementación local o en la nube siguiendo las instrucciones de implementación de línea de comandos en la carpeta /docs/ del repositorio.

4. Cuando haya ejecutado la implementación local o en la nube, busque en la carpeta raíz el archivo *.user.config creado durante la implementación. Abra este archivo en un editor de texto.

5. Cambie la línea siguiente para incluir el valor copiado desde la clave QueryKey:
   
  `<setting name="MapApiQueryKey" value="" />`

### ¿Puedo crear una solución preconfigurada si tengo Microsoft Azure para DreamSpark?
En este momento, no se puede crear una solución preconfigurada con una cuenta de [Microsoft Azure para DreamSpark][lnk-dreamspark]. Sin embargo, puede crear una [cuenta de evaluación gratuita de Azure][lnk-30daytrial] en un par de minutos, lo que le permitirá crear una solución preconfigurada.

### ¿Cómo se eliminan inquilinos de AAD?

Consulte la entrada del blog de Eric Golpe [Walkthrough of Deleting an Azure AD Tenant][lnk-delete-aad-tennant] (Tutorial para la eliminación de inquilinos de Azure AD).

## Pasos siguientes

También puede explorar algunas de las demás características y funcionalidades de las soluciones preconfiguradas del conjunto de aplicaciones de IoT:

- [Información general de la solución preconfigurada de mantenimiento predictivo][lnk-predictive-overview]
- [Seguridad total de IoT][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_0928_2016-->