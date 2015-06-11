<properties 	
	pageTitle="Generador de datos de Azure - ejemplos" 
	description="Proporciona detalles acerca de los ejemplos que se incluyen con el servicio de generador de datos de Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="spelluru"/>

# Generador de datos de Azure - ejemplos

## Ejemplos de Portal de Azure
Rápidamente implementar, revisar y probar un ejemplo de generador de datos de Azure utilizando el **ejemplo canalizaciones** blade en el Portal de Azure.

1. Cree un nuevo generador de datos o abra un generador de datos existente. Consulte [Introducción a la factoría de datos de Azure][data-factory-get-started] para conocer los pasos crear un generador de datos.
2. En el **factoría de datos** blade para el generador de datos, haga clic en el **canalizaciones de ejemplo** en mosaico.

	![Mosaico de canalizaciones de ejemplo](./media/data-factory-samples/SamplePipelinesTile.png)

2. En el **ejemplo canalizaciones** blade, haga clic en el **ejemplo** que desea implementar.
	
	![Módulo de canalizaciones de ejemplo](./media/data-factory-samples/SampleTile.png)

3. Especificar opciones de configuración para el ejemplo. Por ejemplo, la clave de nombre y la cuenta de la cuenta de almacenamiento de Azure, el nombre del servidor de SQL Azure, base de datos, Id. de usuario y contraseña, etc....

	![Módulo de ejemplo](./media/data-factory-samples/SampleBlade.png)

4. Cuando haya terminado con la especificación de las opciones de configuración, haga clic en **crear** que crea e implementa las canalizaciones de ejemplo y los servicios y las tablas vinculadas utilizadas las canalizaciones.
5. Verá el estado de implementación en el mosaico de ejemplo hizo anteriormente en el **ejemplo canalizaciones** blade.

	![Estado de implementación](./media/data-factory-samples/DeploymentStatus.png)

6. Cuando vea el **se implementó correctamente** mensajes en el mosaico del ejemplo, cierre el **ejemplo canalizaciones** blade.
5. En **factoría de datos** blade, verá que las canalizaciones, conjuntos de datos y servicios vinculados se agregan a su generador de datos.  

	![Módulo de generador de datos](./media/data-factory-samples/DataFactoryBladeAfter.png)
   

En la tabla siguiente proporciona una descripción breve de los ejemplos disponibles en la **canalizaciones de ejemplo** en mosaico.

Nombre de ejemplo | description
----------- | -----------
Generación de perfiles de cliente de juegos | Contoso es una empresa de juegos que cree juegos para múltiples plataformas: equipos personales (PC), dispositivos de mano y consolas de juegos. Cada uno de estos juegos produce miles de registros. Objetivo de Contoso es recopilar y analizar los registros generados por estos juegos para obtener información de uso, identificar las oportunidades de ventas y ventas cruzadas, desarrollar nuevas características atractivas etc.... para mejorar el negocio y ofrecer la mejor experiencia a los clientes. Este ejemplo recopila registros de ejemplo, los procesos y enriquece a ellos con datos de referencia y transforma los datos para evaluar la eficacia de una campaña de marketing que Contoso ha lanzado recientemente.
 
## Ejemplos en GitHub
El [repositorio de GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) contiene varios ejemplos que le ayudarán a rápidamente despegue con servicio de generador de datos de Azure (o) modificar las secuencias de comandos y usarla en la propia aplicación. La carpeta Samples\JSON contiene fragmentos de JSON para escenarios comunes.

[data-factory-get-started]: data-factory-get-started.md#CreateDataFactory

<!---HONumber=GIT-SubDir--> 