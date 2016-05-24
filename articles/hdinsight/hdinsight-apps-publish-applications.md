<properties
   	pageTitle="Publicación de aplicaciones de HDInsight | Microsoft Azure"
   	description="Aprenda a crear y publicar aplicaciones de HDInsight."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="05/16/2016"
   	ms.author="jgao"/>

# Publicación de aplicaciones de HDInsight en Azure Marketplace

Una aplicación de HDInsight es una aplicación que los usuarios pueden instalar en un clúster de HDInsight basado en Linux. Estas aplicaciones puede desarrollarlas Microsoft, fabricantes de software independientes (ISV) o el propio usuario. En este artículo, obtendrá información sobre cómo publicar una aplicación de HDInsight en Azure Marketplace. Para obtener información general acerca de cómo publicar en Azure Marketplace, consulte [Publicación de una oferta en Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

Las aplicaciones de HDInsight usan el modelo *Traiga su propia licencia (BYOL)*, donde el proveedor de la aplicación es el responsable de la concesión de licencias de la aplicación a los usuarios finales y Azure solo cobra a los usuarios finales los recursos que crean, como el clúster de HDInsight y sus máquinas virtuales o nodos. Actualmente, la facturación de la aplicación en sí no se realiza a través de Azure.

Artículo relacionado con otra aplicación de HDInsight:

- [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): obtenga información acerca de cómo instalar y probar aplicaciones personalizadas de HDInsight.

 
## Requisitos previos

Para enviar una aplicación personalizada a Marketplace, es preciso haberla creado y probado. Consulte los artículos siguientes:

- [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): obtenga información acerca de cómo instalar y probar aplicaciones personalizadas de HDInsight.

También es preciso haber registrado una cuenta de desarrollador. Consulte [Publicación de una oferta en Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) y [Crear una cuenta de desarrollador de Microsoft](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## Definición de la aplicación

La publicación de aplicaciones en Azure Marketplace conlleva dos pasos. En primer lugar, es preciso definir un archivo **createUiDef.json** para indicar con que clústeres es compatible la aplicación y, después, publicar la plantilla desde el Portal de Azure. Este es un archivo createUiDef.json de ejemplo.

	{
		"handler": "Microsoft.HDInsight",
		"version": "0.0.1-preview",
		"clusterFilters": {
			"types": ["Hadoop", "HBase", "Storm", "Spark"],
			"tiers": ["Standard", "Premium"],
			"versions": ["3.4"]
		}
	}


|Campo | Descripción | Valores posibles|
|-------|---------------|----------------|
|types |Los tipos de clúster con los que es compatible la aplicación. |Hadoop, HBase, Storm y Spark (o cualquier combinación de éstos)|
|tiers |Los niveles de clúster con los que es compatible la aplicación. |Standard, Premium (o ambos)|
|versions|	Los tipos de clúster de HDInsight con los que es compatible la aplicación. |3\.4|

## Empaquetado de aplicación

Cree un archivo zip que contenga todos los archivos requeridos para instalar aplicaciones de HDInsight. Dicho archivo zip lo necesitará en [Publicación de la aplicación](#publish-application).

- [createUiDefinition.json](#define-application).
- mainTemplate.json. En [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md), puede encontrar un ejemplo.
- Todos los scripts requeridos.

> [AZURE.NOTE] Los archivos de aplicación (incluidos los archivos de aplicación web, en caso de que haya) se pueden ubicar en cualquier punto de conexión con acceso público.

## Publicación de la aplicación

Para publicar una aplicación de HDInsight, siga estos pasos:

1. Inicie sesión en el [Portal de publicación de Azure](https://publish.windowsazure.com/).
2. Haga clic en **Solution templates** (Plantillas de solución) para crear una nueva plantilla de solución.
3. Haga clic en **Create Dev Center account and join the Azure program** (Crear cuenta del Centro de desarrollo y unirse al programa de Azure) para registrar una compañía, en caso de que no lo haya hecho. Consulte [Crear una cuenta de desarrollador de Microsoft](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)
4. Haga clic en **Define some Topologies to get Started** (Definir topologías para empezar). Una plantilla de solución es una "matriz" para todas sus topologías. Puede definir varias topologías en una oferta o plantilla de solución. Cuando se inserta una oferta en un entorno de ensayo, se inserta con todas sus topologías. 
5. Adición de una nueva versión
6. Cargue el archivo zip preparado en [Empaquetado de aplicación](#package-application).  
7. Haga clic en **Request Certification** (Solicitar certificado). El equipo de certificación de Microsoft revisará los archivos y certificará la topología.

## Pasos siguientes

- [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): obtenga información acerca de cómo implementar en HDInsight una aplicación de HDInsight no publicada.
- [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md): obtenga información acerca de cómo usar una acción de script para instalar aplicaciones adicionales.
- [Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): obtenga información general acerca de cómo llamar a plantillas de ARM para crear clústeres de HDInsight.

<!---HONumber=AcomDC_0518_2016-->