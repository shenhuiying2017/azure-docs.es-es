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
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="04/22/2016"
  ms.author="araguila"/>
   
# Preguntas más frecuentes sobre el Conjunto de aplicaciones de IoT

### ¿Cuál es la diferencia entre eliminar un grupo de recursos en el Portal de Azure y hacer clic en Eliminar en una solución preconfigurada en azureiotsuite.com?

- Si elimina la solución preconfigurada en [azureiotsuite.com][lnk-azureiotsuite], eliminará todos los recursos aprovisionados cuando se creó la solución preconfigurada; si agregó recursos adicionales al grupo de recursos, estos también se eliminan. 

- Si elimina el grupo de recursos en el [Portal de Azure][lnk-azure-portal], solo eliminará los recursos de dicho grupo de recursos; también tendrá que eliminar la aplicación de Azure Active Directory asociada a la solución preconfigurada en el [Portal de Azure clásico][lnk-classic-portal].

### ¿Cuántas instancias de DocumentDB se pueden aprovisionar en una suscripción?

Cinco. Para aumentar este límite se puede crear una [incidencia de soporte técnico de Azure][link-azuresupportticket]; pero, de forma predeterminada, solo se pueden aprovisionar cinco instancias de DocumentDB por suscripción. Como consecuencia, solo se pueden aprovisionar un máximo de cinco soluciones preconfiguradas de supervisión remota en una suscripción determinada.

### ¿Cuántas API de Mapas de Bing gratis se pueden aprovisionar en una suscripción?

Dos. Solo se pueden crear dos API de Mapas de Bing gratis en una suscripción. La solución de supervisión remota se aprovisiona de forma predeterminada con una API de Mapas de Bing gratis. Como consecuencia, solo se pueden aprovisionar un máximo de dos soluciones de supervisión remota en una suscripción sin modificaciones.

### Tengo una implementación de soluciones de supervisión remota con un mapa estático, ¿cómo se puede agregar un mapa Bing interactivo? 
1. Obtenga la clave QueryKey de API de Mapas de Bing para empresa en el [Portal de Azure][lnk-azure-portal]\: 
 1. Desplácese al grupo de recursos donde está la API de Mapas de Bing para empresa en el [Portal de Azure][lnk-azure-portal].
 2. Haga clic en Toda la configuración y después en Administración de claves. 
 3. Verá dos claves: MasterKey y QueryKey. Copie el valor de QueryKey.

     > [AZURE.NOTE] ¿No tiene una cuenta de la API de Mpas Mapas de Bing para empresa? Cree una cuenta en el [Portal de Azure][lnk-azure-portal] haciendo clic en + Nuevo, busque la API de Mapas de Bing para empresa y siga las indicaciones para crearla.

2. Descargue el código más reciente en la página [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].

3. Ejecute una implementación local o en la nube siguiendo las instrucciones de implementación de línea de comandos en la carpeta /docs/ del repositorio.

4. Cuando haya ejecutado la implementación local o en la nube, busque en la carpeta raíz el archivo *.user.config creado durante la implementación. Abra este archivo en un editor de texto.

5. Cambie la línea siguiente para incluir el valor copiado desde la clave QueryKey:
   
  `<setting name="MapApiQueryKey" value="" />`


### ¿Cómo se eliminan inquilinos de AAD?

Consulte la entrada del blog de Eric Golpe [Walkthrough of Deleting an Azure AD Tenant][lnk-delete-aad-tennant] (Tutorial para la eliminación de inquilinos de Azure AD).

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_0427_2016-->