<properties title="Azure Data Factory - Frequently Asked Questions" pageTitle="Factoría de datos de Azure: preguntas más frecuentes" description="Frequently asked questions about Azure Data Factory." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Factoría de datos de Azure: preguntas más frecuentes

**P: ¿Qué es la Factoría de datos de Azure?**

La factoría de datos es un servicio totalmente administrado destinado a los desarrolladores para la composición de servicios de almacenamiento, movimiento y procesamiento de los datos en canalizaciones de datos tolerantes a errores y de gran disponibilidad. La factoría de datos funciona tanto en el almacenamiento de datos en la nube como local. Una canalización es un conjunto de entradas de datos, actividades de procesamiento y salidas de datos y se define con un scripting sencillo JSON y activado mediante comandos de PowerShell. Una vez activada, la factoría de datos organiza y programa las canalizaciones para que se ejecuten en HDInsight (Hadoop) con opciones para la administración automática del clúster en nombre del usuario. La factoría de datos también proporciona una experiencia visual de administración y supervisión a través del Portal de vista previa de Azure para supervisar todas las canalizaciones con información detallada sobre el funcionamiento y el estado del servicio en un panel.
 
**P: ¿Qué desafío del cliente resuelve la factoría de datos?**

La Factoría de datos de Azure equilibra la agilidad de aprovechar diversos servicios de almacenamiento, procesamiento y movimiento de los datos en el almacenamiento relacional tradicional junto con los datos no estructurados, con las capacidades de control y supervisión de un servicio totalmente administrado.

**P: ¿A quién va destinada la factoría de datos?**


- Desarrolladores de datos: que son responsables de la creación de servicios de integración entre Hadoop y otros sistemas:
	- Debe mantenerse actualizada e integrarse con un panorama de constante cambio y crecimiento de los datos
	- Debe escribir código personalizado para la producción de información, y es costosa, difícil de mantener y no presenta una gran disponibilidad ni es tolerante a errores

- Profesionales de TI: que desean incorporar datos más diversos en su infraestructura de TI:
	- Se requiere para mirar en todos los datos de una organización con el fin de obtener perspectivas empresariales enriquecidas
	- Debe administrar los recursos de proceso y almacenamiento para equilibrar el coste y la escala de manera local y en la nube
	- Debe agregar rápidamente diversos orígenes y procesamiento para abordar las nuevas necesidades del negocio, y mantener al mismo tiempo la visibilidad en todos los recursos de proceso y almacenamiento

**P: ¿Dónde puedo encontrar detalles de precios de la Factoría de datos de Azure?**

Consulte la [página de detalles de precios de la factoría de datos][adf-pricing-details] para obtener información al respecto.  

**P. ¿Cómo puedo comenzar con la Factoría de datos de Azure?**

- Para obtener información general sobre la Factoría de datos de Azure, consulte [Introducción a la Factoría de datos de Azure][adf-introduction].
- Para ver un tutorial rápido, consulte [Introducción a la Factoría de datos de Azure][adfgetstarted].
- Para obtener la documentación completa, consulte [Documentación de la Factoría de datos de Azure][adf-documentation-landingpage].
 
**P: ¿Cuáles orígenes de datos y actividades se admiten?**

- **Orígenes de datos compatibles:** Almacenamiento de Azure (blobs y tablas), SQL Server, Base de datos SQL Azure. 
- **Actividades compatibles:**: actividad de copia (de local a la nube y de la nube a local), actividad de HDInsight (transformaciones Pig y Hive) y actividades C# personalizadas.
  
**P: ¿Cómo los clientes obtienen acceso a la factoría de datos?**

Los clientes pueden obtener acceso a la factoría de datos a través del [Portal de vista previa de Azure][azure-preview-portal].

**P: ¿Cuál es la disponibilidad de regiones de la factoría de datos?**

En la versión de vista previa pública, la factoría de datos solo estará disponible en el oeste de EE. UU.  Los servicios de proceso y almacenamiento utilizados por las factorías de datos pueden estar en otras regiones.
 
**P: ¿Cuáles son los límites en el número de factorías, canalizaciones, actividades y conjuntos de datos?** 


- Número de factorías de datos dentro de una suscripción: 50
- Número de canalizaciones dentro de una factoría de datos: 100
- Número de actividades dentro de una canalización: 10
- Número de conjuntos de datos con una factoría de datos: 100

**P: ¿Qué regiones admite la actividad de copia?**

La actividad de copia es compatible con la copia de datos en las siguientes regiones: Este de Estados Unidos 2, Oeste de Estados Unidos, Norte de Europa, Europa occidental y Sudeste asiático.

También se admite la copia de los datos en otras regiones, mediante el uso de las cinco regiones anteriores para enrutar los datos.  La operación de copia se mide según la región por la que se enrutan los datos.

Región de destino de copia | Región usada para el enrutamiento
-------------------------- | -----------------------
Este de EE. UU. | Este de EE. UU. 2
Centro de EE. UU. | Este de EE. UU. 2
Centro y norte de EE. UU. | Este de EE. UU. 2
Centro y sur de EE. UU. | Oeste de EE. UU.
Asia oriental | Sudeste de Asia
Este de Japón | Oeste de EE. UU.
Oeste de Japón | Oeste de EE. UU.
Sur de Brasil | Este de EE. UU. 2

**P: Al usar un clúster de HDInsight, ¿cuáles son las regiones que admite HDInsight?**

Consulte la sección Disponibilidad geográfica en los siguientes artículos: o [Detalles de precios de HDInsight][hdinsight-supported-regions].

**P: ¿Qué región se usa con un clúster de HDInsight a petición?**

El clúster de HDInsight a petición se crea en la misma región que existe para el almacenamiento que se va a usar con el clúster.    

[adfgetstarted]: ../data-factory-get-started

[adf-introduction]: ../data-factory-introduction
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/es-es/pricing/details/hdinsight/
