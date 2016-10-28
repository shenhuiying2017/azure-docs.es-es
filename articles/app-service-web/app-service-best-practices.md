<properties
	pageTitle="Procedimientos recomendados para el Servicio de aplicaciones de Azure"
	description="Información acerca de los procedimientos recomendados y la solución de problemas del Servicio de aplicaciones de Azure."
	services="app-service"
	documentationCenter=""
	authors="dariagrigoriu"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2016"
	ms.author="dariagrigoriu"/>
    
# Procedimientos recomendados para el Servicio de aplicaciones de Azure

En este artículo se resumen los procedimientos recomendados para usar el [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

## <a name="colocation"></a>Ubicación compartida
Cuando los recursos de Azure que componen una solución como una aplicación web y una base de datos se encuentran en regiones diferentes los efectos pueden incluir:

*  Mayor latencia en la comunicación entre recursos
*  Cargos monetarios de transferencia de datos entre regiones como se indica en el [página de precios de Azure](https://azure.microsoft.com/pricing/details/data-transfers)

Para los recursos de Azure que componen una solución, como una aplicación web y una cuenta de almacenamiento o base de datos usada para mantener el contenido o los datos, se recomienda que la ubicación compartida se encuentre en la misma región. Al crear recursos es preciso asegurarse de que estén de la misma región de Azure, salvo que haya motivos comerciales o de diseño concretos para que no lo estén. Para mover una aplicación del Servicio de aplicaciones a la región de la base de datos, utilice la [característica de clonación del Servicio de aplicaciones](app-service-web-app-cloning-portal.md), que actualmente está disponible para las aplicaciones del servicio de aplicaciones Premium.

## <a name="memoryresources"></a>Cuando las aplicaciones consumen más memoria de lo esperado
Si observa que una aplicación consume más memoria de lo esperado, lo que se indica a través de la supervisión o de recomendaciones de servicio, considere la posibilidad de usar la [característica de recuperación automática del Servicio de aplicaciones](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Una de las opciones de esta característica es que realiza acciones personalizadas en función de un umbral de memoria. Las acciones abarcan todo el espectro, desde las notificaciones por correo electrónico a la investigación a través de volcado de memoria a la mitigación inmediata mediante el reciclado del proceso de trabajo. La recuperación automática se puede configurar tanto a través de web.config como a través de una interfaz de usuario, tal y como se describe en esta entrada del blog [App Service Support Site Extension](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps) (Extensión del sitio de soporte del Servicio de aplicaciones).

## <a name="CPUresources"></a>Cuando las aplicaciones consumen más CPU de lo esperado
Si observa que una aplicación consume más CPU de lo esperado o experimenta repetidas puntas de actividad de la CPU como indican la supervisión o las recomendaciones de servicio, considere la posibilidad de escalar el plan del servicio de aplicaciones vertical u horizontalmente. Si una aplicación es con estado, el escalado vertical es la única opción, mientras que si es sin estado, el escalado horizontal le proporcionará mayor flexibilidad y mayor potencial de escalado.

Para obtener más información sobre las aplicaciones "con estado" y "sin estado", puede ver este vídeo: [Planning a Scalable End-to-End Multi-Tier Application on Microsoft Azure Web App](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid) (Planeamiento de una aplicación completa de niveles múltiples en una aplicación web de Microsoft Azure). Para obtener más información sobre las opciones de escalado y escalado automático del Servicio de aplicaciones, consulte [Escalación de una aplicación web en el Servicio de aplicaciones de Azure](web-sites-scale.md).

## <a name="socketresources"></a>Cuando se agotan los recursos del socket
Una razón habitual para agotar las conexiones TCP salientes es el uso de bibliotecas de cliente que no se han implementado para reutilizar las conexiones TCP o en el caso de un protocolo de nivel superior como HTTP, no saca provecho de las conexiones persistentes. Revise la documentación de las bibliotecas a las que hacen referencia las aplicaciones del plan del servicio de aplicaciones para asegurarse de que se configuran o se tiene acceso a ellas en el código para una reutilización eficiente de las conexiones salientes. Siga también la guía de la documentación de la biblioteca para que la creación y liberación, o la limpieza sean correctas para evitar las conexiones con fugas. Aunque las investigaciones de estas bibliotecas de cliente están en curso, el impacto se pueden mitiga mediante el escalado horizontal a varias instancias.

## <a name="appbackup"></a>Cuando la copia de seguridad de la aplicación comienza a fallar
Los dos principales motivos por los que comienza a fallar la copia de seguridad de una aplicación son una configuración de almacenamiento no válida y una configuración de base de datos no válida. Estos errores suelen ocurrir cuando se producen cambios en los recursos de almacenamiento o de base de datos o en el acceso a estos recursos (por ejemplo, la actualización de las credenciales de la base de datos seleccionada en la configuración de copia de seguridad). Las copias de seguridad suelen ejecutarse según una programación y requieren acceso al almacenamiento (para generar los archivos de copia de seguridad) y a las bases de datos (para copiar y leer el contenido que se incluirá en la copia de seguridad). Si no se tiene acceso a cualquiera de estos recursos, se produciría un error de copia de seguridad.

Cuando aparecen errores de copia de seguridad, revise los resultados más recientes para saber qué tipo de error se está produciendo. En el caso de errores de acceso de almacenamiento, revise y actualice la configuración de almacenamiento utilizada en la configuración de copia de seguridad. En el caso de errores de acceso de la base de datos, revise y actualice las cadenas de conexiones como parte de la configuración de la aplicación. Después, continúe para actualizar la configuración de copia de seguridad con el fin de incluir correctamente las bases de datos necesarias. Para obtener más información sobre la copia de seguridad de aplicaciones, consulte la documentación [Hacer copia de seguridad de una aplicación web en el servicio de aplicaciones de Azure](web-sites-backup.md).

## <a name="nodejs"></a>Cuando se implementan nuevas aplicaciones de Node.js en el Servicio de aplicaciones de Azure
La configuración predeterminada del Servicio de aplicaciones de Azure para las aplicaciones Node.js tiene que como objetivo satisfacer mejor los requisitos de las aplicaciones más comunes. Si considera que la configuración de la aplicación Node.js va a beneficiarse del ajuste personalizado para mejorar el rendimiento o para optimizar el uso de recursos para los recursos de la CPU, memoria o red, podría consultar nuestras prácticas recomendadas y pasos para solucionar problemas. En este artículo de documentación se describe la configuración de IISNode que necesita para ajustar las opciones de la aplicación Node.js, así como los distintos escenarios o problemas de dicha aplicación. También se muestra cómo solucionar estos problemas: [Procedimientos recomendados para el Servicio de aplicaciones de Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md).

<!---HONumber=AcomDC_0713_2016-->