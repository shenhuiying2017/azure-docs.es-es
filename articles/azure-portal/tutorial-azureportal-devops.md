---
title: 'Tutorial: DevOps con Azure Portal | Microsoft Docs'
description: Aprenda acerca de los distintos flujos de trabajo de DevOps en Azure Portal.
services: azure-portal
documentationcenter: 
author: mlearned
manager: douge
editor: mlearned
ms.assetid: 4f1c5bc1-c732-4d35-b5df-0fd68e547d38
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/05/2016
ms.author: mlearned
ms.openlocfilehash: d2c059fe415b743e4b2265d6dda492878307be93
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-devops-with-the-azure-portal"></a>Tutorial: DevOps con Azure Portal
La plataforma Azure está llena de flujos de trabajo de DevOps flexibles. En este tutorial, aprenderá a aprovechar las funcionalidades de Azure Portal para desarrollar, probar, implementar, solucionar problemas, supervisar y administrar las aplicaciones que se están ejecutando. Este tutorial se centra en lo siguiente:

1. Creación de una aplicación web y habilitación de la implementación continua
2. Desarrollo y prueba de una aplicación
3. Supervisión y solución de problemas de una aplicación
4. Tareas generales de administración de aplicaciones

## <a name="creating-a-web-app-and-enabling-continuous-deployment"></a>Creación de una aplicación web y habilitación de la implementación continua
Con el [Azure App Service](https://azure.microsoft.com/services/app-service/), cree una aplicación web que utilizará en el resto de este tutorial. Inicialmente, deberá habilitar la implementación continua desde el repositorio de código fuente en nuestro entorno de ejecución de Azure.

1. Inicie sesión en Azure Portal.
2. Elija **App Services** &gt; **icono Agregar** y escriba un nombre, elija su suscripción y cree un grupo de recursos para que actúe como contenedor del servicio.
   
   Los grupos de recursos permiten administrar diversos aspectos de la solución, como la facturación, las implementaciones y la supervisión, como un solo grupo mediante [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
   
   ![imagen1][image1]
3. Transcurridos unos segundos,el Servicio de aplicaciones se ha creado. Dedique unos minutos a explorar las diversas opciones de menú para el servicio en el portal.
   
   ![imagen2][image2]    
4. Haga clic en la dirección URL. Observe la variedad de opciones disponibles para herramientas y repositorios. También puede usar los lenguajes y plataformas que prefiera, incluidos .NET, Java y Ruby.
   
   ![imagen3][image3]    
5. El Portal de Azure hace que la implementación continua sea un proceso fácil con solo unos pocos pasos sencillos. En el Portal de Azure, elija Configuración en el icono para el Servicio de aplicaciones que acaba de crear.
   
   ![imagen4][image4]
   
   En la hoja que se abre a la derecha, desplácese hasta la sección de publicación.
   
   ![imagen5][image5]
6. A continuación, configure algunas opciones para habilitar la implementación continua para la aplicación. Haga clic en el origen de la implementación y haga clic en Elegir origen. Observe la variedad de opciones disponibles para los orígenes del repositorio.
   
   ![imagen6][image6]
7. En este ejemplo, elija GitHub. También puede elegir el repositorio que prefiera y configurar las credenciales de autorización.
   
   ![imagen7][image7]
8. Después de la autorización en el repositorio, puede elegir un proyecto y una rama que desee implementar. Hay varios ejemplos ficticios que se enumeran a continuación.
   
   ![imagen8][image8]
9. Una vez elegido el proyecto y la rama, haga clic en Aceptar. Debería empezar a ver las notificaciones de una implementación.
   
   ![imagen9][image9]
10. Vuelva a GitHub para ver el webhook que se creó para integrar el repositorio de control de origen con Azure. Azure Portal permite la integración con GitHub con unos pocos pasos sencillos.
    
    ![imagen10][image10]
11. Para demostrar la implementación continua, puede agregar rápidamente algún contenido al repositorio. Para ver un ejemplo simple, agregue un archivo de texto de ejemplo a un repositorio de GitHub. Puede usar .NET, Ruby, Python o algún otro tipo de aplicación con App Service. Puede agregar un archivo de texto o una aplicación de ASP.NET MVC, Java o Ruby en el repositorio que elija.
    
    ![imagen11][image11]
12. Después de confirmar los cambios en el repositorio, verá que una nueva implementación se inicia en el área de notificaciones del Portal. Si no ve los cambios rápidamente después de confirmar en el repositorio, haga clic en Sincronizar.
    
    ![imagen12][image12]
13. En este momento, si intenta cargar la página para el Servicio de aplicaciones, puede recibir un error 403. En este ejemplo, se debe a que no hay ninguna configuración de documentos predeterminada típica para la página, como por ejemplo, un archivo como index.htm o default.html. Puede resolverlo rápidamente con las herramientas de Azure Portal.  En Azure Portal, elija Configuración &gt; Configuración de la aplicación.
    
     ![imagen13][image13]
14. Se abre una hoja de configuración de la aplicación. Escriba el nombre de la página ,"SamplePage.html", y haga clic en Guardar. Dedique unos minutos a explorar los demás valores.
    
    ![imagen14][image14]
15. Si lo desea, actualice la dirección URL del explorador para asegurarse de que ver los cambios previstos. En este caso, ahora hay algún texto simple en la página. Cada cambio adicional en el repositorio dará como resultado una nueva implementación automática.
    
    ![imagen15][image15]
    
    Habilitar la implementación continua con Azure Portal es sencillo. También puede crear canalizaciones de entrega de versiones más complejas y usar muchas otras técnicas con el control de origen existente y sistemas de integración continua para implementar en Azure, como aprovechar la generación automatizada y los sistemas de administración de versiones.

## <a name="develop-and-test-an-app"></a>Desarrollo y prueba de una aplicación
A continuación, realice algunos cambios en el código base e implemente rápidamente los cambios. También configurará algunas pruebas de rendimiento para la aplicación web.

1. En el panel de navegación de Azure Portal, elija Servicios de aplicaciones y busque App Service.
   
   ![imagen16][image16]
2. Haga clic en Herramientas.
   
   ![imagen17][image17]
3. Observe la categoría de desarrollo en Herramientas. Hay varias herramientas útiles aquí que nos permiten trabajar con aplicaciones sin salir de Azure Portal. Haga clic en Consola.
   
   ![imagen18][image18]
4. En la ventana de la consola, puede emitir comandos dinámicos para la aplicación. Escriba el comando dir y presione Entrar. Observe que los comandos que requieren privilegios elevados no funcionan.
   
   ![imagen19][image19]
5. Vuelva a la categoría Desarrollo y elija Visual Studio Online. Nota: Visual Studio Online ahora se llama Visual Studio Team Services.
   
   ![imagen20][image20]
6. Active la experiencia de edición en el explorador para la aplicación.
   
   ![imagen21][image21]
7. Se instala una extensión web para la aplicación. Las extensiones agregan funcionalidades a las aplicaciones de Azure de forma rápida y sencilla. Observe algunos de los otros tipos de extensión disponibles en la siguiente captura de pantalla.
   
   ![imagen22][image22]
8. Una vez instalada la extensión Visual Studio Online, haga clic en Ir.
   
   ![imagen23][image23]
9. Se abre una pestaña del explorador donde verá un IDE de desarrollo directamente en el explorador. Tenga en cuenta que la siguiente experiencia está en Chrome.
   
   ![imagen24][image24]
10. Puede realizar varias actividades, tales como editar archivos, agregar archivos y carpetas, y descargar contenido desde el sitio activo. Realice una operación de edición rápida en el archivo SamplePage.html.
    
    ![imagen25][image25]
11. En unos momentos, los cambios se guardan automáticamente. Si vuelve a la página, puede ver los cambios. Tenga en cuenta que es muy posible que las modificaciones dinámicas como estas no sean adecuadas para entornos de producción. Sin embargo, las herramientas hacen que sea muy fácil realizar cambios rápidos para entornos de prueba y desarrollo.
    
    ![imagen26][image26]
    
    ![imagen27][image27]
12. Vuelva a la hoja de herramientas y, en la categoría Desarrollo, haga clic en Prueba de rendimiento.
    
    ![imagen28][image28]
13. Debe establecer una cuenta de servicios del equipo. Consulte aquí para ver más detalles: [Create a Team Services Account](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
14. Haga clic en Nuevo para crear una prueba de rendimiento.
    
    ![imagen29][image29]
    
    Configure los diversos valores y haga clic en Ejecutar prueba, en la parte inferior del cuadro de diálogo, para iniciar una prueba de rendimiento.
    
    ![imagen30][image30]
    
    ![imagen31][image31]
15. Una vez que la prueba comienza a ejecutarse, puede supervisar el estado.
    
    ![imagen32][image32]
    
    Una vez finalizada la prueba, al hacer clic en el resultado se muestran más detalles.
    
    ![imagen33][image33]
16. En este ejemplo, ha creado la ejecución de una pequeña prueba, por lo que los datos que se analizarán son limitados, pero se pueden ver varias métricas, así como volver a ejecutar la prueba desde esta vista. Gracias a Azure Portal, el proceso de crear, ejecutar y analizar pruebas de rendimiento web es fácil. Las siguientes capturas de pantalla muestran los datos de rendimiento.
    
    ![imagen34][image34]
    
    ![imagen35][image35]
    
    ![imagen36][image36]

## <a name="monitoring-and-troubleshooting-an-app"></a>Supervisión y solución de problemas de una aplicación
Azure proporciona muchas funcionalidades de supervisión y solución de problemas de las aplicaciones que se ejecutan.

1. En Azure Portal para nuestra aplicación web, elija Herramientas.
   
   ![imagen37][image37]
2. En la categoría Solucionar problemas, observe las diversas opciones para utilizar las herramientas con el fin de solucionar posibles problemas con una aplicación en ejecución. Puede hacer cosas como supervisar el tráfico HTTP dinámico, habilitar la recuperación automática, ver registros, etc.
   
   ![imagen38][image38]
3. Elija Métricas del sitio para obtener rápidamente una vista de algunos códigos HTTP.
   
   ![imagen39][image39]
4. Elija Diagnóstico como el Servicio. Elija el tipo de aplicación, a continuación, Ejecutar.
   
   ![imagen40][image40]
   
   Comienza una recopilación.
   
   ![imagen41][image41]
5. Puede elegir el registro adecuado para diagnosticar posibles problemas. Debe habilitar el registro para ver todas las opciones de datos disponibles, como los registros de HTTP.
   
   ![imagen42][image42]
   
   Para descargar y analizar un informe de análisis DebugDiag que ayuda a encontrar posibles problemas, haga clic en el archivo de volcado de memoria.
   
   ![imagen43][image43]
6. Para ver más datos, debe habilitar el registro adicional. En Azure Portal, vaya a la aplicación web y elija Configuración.
   
   ![imagen44][image44]
7. Desplácese a la categoría de características y elija Registros de diagnóstico.
   
      ![imagen45][image45]
8. Observe las diversas opciones para el registro. Active el registro del servidor web y haga clic en Guardar.
   
   ![imagen46][image46]
9. Vuelva al área de herramientas de la aplicación y elija Diagnóstico como un servicio y haga clic en Ejecutar para volver a ejecutar la recopilación de datos.
   
   ![imagen47][image47]
10. Con la opción de registro de HTTP habilitada, ahora verá los datos recopilados para los registros de HTTP.
    
    ![imagen48][image48]
11. Al hacer clic en el registro de archivos HTML se genera un completo informe basado en explorador para una investigación más detallada.
    
    ![imagen49][image49]
12. Vuelva a la sección de herramientas en Azure Portal para la aplicación. Desplácese a la sección Herramientas y elija Explorador de procesos.
    
    ![imagen50][image50]
13. Al elegir Explorador de procesos, puede ver detalles sobre los procesos en ejecución. Observe a continuación que puede profundizar en los procesos e incluso terminarlos todos desde Azure Portal.
    
    ![imagen51][image51]
    
    ![imagen52][image52]
14. Vuelva a la hoja Configuración, situada a la izquierda. Haga clic en Nueva solicitud de soporte técnico.
    
    ![imagen53][image53]
15. En la hoja de la derecha, puede rellenar los detalles acerca de los problemas, escribir la información de contacto e incluso cargar datos de diagnóstico. Gracias a Azure Portal, trabajar con el soporte técnico de Microsoft es una experiencia perfecta.
    
    ![imagen54][image54]
    
    ![imagen55][image55]
    
    Azure Portal ayuda a proporcionar experiencias de herramientas eficaces y familiares para ayudarle a supervisar y solucionar problemas de nuestras aplicaciones en ejecución. También es posible tomar medidas rápidamente mediante la realización de tareas como reciclar procesos, habilitar y deshabilitar diversas colecciones de datos e incluso realizar la integración con el soporte técnico profesional de Microsoft.

## <a name="general-application-management"></a>Administración general de aplicaciones
Al administrar aplicaciones, a menudo necesitará realizar una amplia serie de actividades, como la configuración de estrategias de copia de seguridad, la implementación y administración de proveedores de identidades y la configuración de control de acceso basado en roles. Al igual que con otras experiencias de DevOps, la plataforma Azure integra estas tareas directamente en el portal.

1. Para mantener segura la aplicación web frente a la pérdida de datos debe configurar copias de seguridad. Vaya al área Configuración de la aplicación web.
   
   ![imagen56][image56]
2. En la hoja de la derecha, desplácese a la categoría Características.
   
    ![imagen57][image57]
3. Elija Copias de seguridad; se abre una hoja a la derecha.
   
   ![imagen58][image58]
4. Haga clic en Configurar, elija una cuenta de almacenamiento en la hoja de la derecha.
   
   ![imagen59][image59]
5. Ahora cree y elija un contenedor de almacenamiento para guardar las copias de seguridad. Haga clic en Crear en la parte inferior de la hoja. A continuación, seleccione el contenedor.
   
   ![imagen60][image60]
6. Una vez elegido el contenedor, puede configurar las programaciones y las copias de seguridad de las bases de datos. En este escenario, haga clic en el icono Guardar.
   
    ![imagen61][image61]
7. Después de guardar, desplácese a Copias de seguridad, en la hoja de la izquierda. Haga clic en Realizar copia de seguridad ahora para realizar copias de la aplicación.
   
    ![imagen62][image62]
8. En unos momentos, verá una copia de seguridad creada. Observe la opción Restaurar ahora en la captura de pantalla siguiente.
   
    ![imagen63][image63]
9. Haga clic en Restaurar ahora y examine las opciones de la hoja de la derecha. Puede elegir una copia de seguridad adecuada y restaurar fácilmente a un estado anterior según sea necesario. El Portal de Azure nos ha ayudado a habilitar fácilmente una estrategia de recuperación ante desastres simple para la aplicación.
   
    ![imagen64][image64]
10. Vuelva a la hoja Configuración, situada a la izquierda, y en Características, elija Autenticación/autorización.
    
     ![imagen65][image65]
11. En la hoja de la derecha, elija Autenticación de App Service. Observe la variedad de opciones que puede configurar con proveedores conocidos.
    
     ![imagen66][image66]
12. Elija el proveedor que prefiera y observe las opciones para el ámbito. Puede proporcionar un Id. de la aplicación y un Secreto de la aplicación y habilitar rápidamente la autenticación de Facebook para la aplicación. Azure Portal habilita la autenticación como una solución de llave en mano para las aplicaciones.
    
     ![imagen67][image67]
13. Vuelva a la hoja Configuración y elija Usuarios en la categoría Administración de recursos.
    
     ![imagen68][image68]
14. En la hoja de la derecha, examine las diversas opciones para agregar roles y usuarios. Azure Portal permite un control fácil de la aplicación mediante RBAC (control de acceso basado en rol).
    
     ![imagen69][image69]

## <a name="summary"></a>Resumen
En este tutorial se muestra parte de la capacidad de la plataforma Azure al habilitar rápidamente la implementación continua para una aplicación web, efectuar diversas actividades de desarrollo y prueba, supervisar y solucionar problemas de una aplicación activa y, por último, administrar estrategias clave como la recuperación ante desastres, identidades y control de acceso basado en roles. La plataforma Azure proporciona una experiencia integrada para estos flujos de trabajo de DevOps y permite trabajar eficazmente al permanecer en el contexto para la tarea en cuestión.

## <a name="next-steps"></a>Pasos siguientes
* Azure Resource Manager es importante para habilitar DevOps en la plataforma Azure.  Para más información, visite [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Para más información acerca de la implementación de Azure App Service, visite [Documentación de implementación de Azure App Service](../app-service/app-service-deploy-local-git.md)

[image1]: ./media/tutorial-azureportal-devops/image1.png
[image2]: ./media/tutorial-azureportal-devops/image2.png
[image3]: ./media/tutorial-azureportal-devops/image3.png
[image4]: ./media/tutorial-azureportal-devops/image4.png
[image5]: ./media/tutorial-azureportal-devops/image5.png
[image6]: ./media/tutorial-azureportal-devops/image6.png
[image7]: ./media/tutorial-azureportal-devops/image7.png
[image8]: ./media/tutorial-azureportal-devops/image8.png
[image9]: ./media/tutorial-azureportal-devops/image9.png
[image10]: ./media/tutorial-azureportal-devops/image10.png
[image11]: ./media/tutorial-azureportal-devops/image11.png
[image12]: ./media/tutorial-azureportal-devops/image12.png
[image13]: ./media/tutorial-azureportal-devops/image13.png
[image14]: ./media/tutorial-azureportal-devops/image14.png
[image15]: ./media/tutorial-azureportal-devops/image15.png
[image16]: ./media/tutorial-azureportal-devops/image16.png
[image17]: ./media/tutorial-azureportal-devops/image17.png
[image18]: ./media/tutorial-azureportal-devops/image18.png
[image19]: ./media/tutorial-azureportal-devops/image19.png
[image20]: ./media/tutorial-azureportal-devops/image20.png
[image21]: ./media/tutorial-azureportal-devops/image21.png
[image22]: ./media/tutorial-azureportal-devops/image22.png
[image23]: ./media/tutorial-azureportal-devops/image23.png
[image24]: ./media/tutorial-azureportal-devops/image24.png
[image25]: ./media/tutorial-azureportal-devops/image25.png
[image26]: ./media/tutorial-azureportal-devops/image26.png
[image27]: ./media/tutorial-azureportal-devops/image27.png
[image28]: ./media/tutorial-azureportal-devops/image28.png
[image29]: ./media/tutorial-azureportal-devops/image29.png
[image30]: ./media/tutorial-azureportal-devops/image30.png
[image31]: ./media/tutorial-azureportal-devops/image31.png
[image32]: ./media/tutorial-azureportal-devops/image32.png
[image33]: ./media/tutorial-azureportal-devops/image33.png
[image34]: ./media/tutorial-azureportal-devops/image34.png
[image35]: ./media/tutorial-azureportal-devops/image35.png
[image36]: ./media/tutorial-azureportal-devops/image36.png
[image37]: ./media/tutorial-azureportal-devops/image37.png
[image38]: ./media/tutorial-azureportal-devops/image38.png
[image39]: ./media/tutorial-azureportal-devops/image39.png
[image40]: ./media/tutorial-azureportal-devops/image40.png
[image41]: ./media/tutorial-azureportal-devops/image41.png
[image42]: ./media/tutorial-azureportal-devops/image42.png
[image43]: ./media/tutorial-azureportal-devops/image43.png
[image44]: ./media/tutorial-azureportal-devops/image44.png
[image45]: ./media/tutorial-azureportal-devops/image45.png
[image46]: ./media/tutorial-azureportal-devops/image46.png
[image47]: ./media/tutorial-azureportal-devops/image47.png
[image48]: ./media/tutorial-azureportal-devops/image48.png
[image49]: ./media/tutorial-azureportal-devops/image49.png
[image50]: ./media/tutorial-azureportal-devops/image50.png
[image51]: ./media/tutorial-azureportal-devops/image51.png
[image52]: ./media/tutorial-azureportal-devops/image52.png
[image53]: ./media/tutorial-azureportal-devops/image53.png
[image54]: ./media/tutorial-azureportal-devops/image54.png
[image55]: ./media/tutorial-azureportal-devops/image55.png
[image56]: ./media/tutorial-azureportal-devops/image56.png
[image57]: ./media/tutorial-azureportal-devops/image57.png
[image58]: ./media/tutorial-azureportal-devops/image58.png
[image59]: ./media/tutorial-azureportal-devops/image59.png
[image60]: ./media/tutorial-azureportal-devops/image60.png
[image61]: ./media/tutorial-azureportal-devops/image61.png
[image62]: ./media/tutorial-azureportal-devops/image62.png
[image63]: ./media/tutorial-azureportal-devops/image63.png
[image64]: ./media/tutorial-azureportal-devops/image64.png
[image65]: ./media/tutorial-azureportal-devops/image65.png
[image66]: ./media/tutorial-azureportal-devops/image66.png
[image67]: ./media/tutorial-azureportal-devops/image67.png
[image68]: ./media/tutorial-azureportal-devops/image68.png
[image69]: ./media/tutorial-azureportal-devops/image69.png
