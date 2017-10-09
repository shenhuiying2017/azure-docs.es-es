---
title: "Creación de una instancia de Azure Data Factory con Azure Portal | Microsoft Docs"
description: "Cree una instancia de Azure Data Factory para copiar datos de un almacén de datos en la nube (Azure Blob Storage) a otro (Azure SQL Database)."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: b884d7f08311cc60dc3af500f552d525d23b91e6
ms.contentlocale: es-es
ms.lasthandoff: 09/26/2017

---
# <a name="create-a-data-factory-using-the-azure-portal"></a>Crear una factoría de datos mediante Azure Portal
Azure Data Factory es un servicio de integración de datos basado en la nube que le permite crear flujos de trabajo basados en datos en la nube a fin de coordinar y automatizar el movimiento y la transformación de datos. Mediante Azure Data Factory, puede crear y programar flujos de trabajo orientados a datos (llamados canalizaciones) que pueden ingerir datos de almacenes de datos dispares, procesar o transformar los datos mediante servicios de proceso, como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics y Azure Machine Learning, y publicar datos de salida en almacenes de datos, como Azure SQL Data Warehouse para que los consuman las aplicaciones de inteligencia empresarial (BI). 

En esta guía de inicio rápido, se usa Azure Portal para crear una factoría de datos. Después de crear la factoría de datos, debe usar PowerShell, SDK de .NET, SDK de Python o API de REST como en otras guías de inicio rápido como canalización de datos que copia datos de un almacén de datos de origen a uno de destino. Actualmente, no se pueden crear canalizaciones en una factoría de datos con Azure Portal.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal
Inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-a-data-factory"></a>Crear una factoría de datos
Estos son los pasos que se realizan en esta guía de inicio rápido:
1. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. En la hoja **Nueva factoría de datos**, escriba el nombre **ADFTutorialDataFactory******. 
      
     ![Hoja Nueva Factoría de datos](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser **único de forma global**. Si recibe el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory) e intente crearlo de nuevo. Consulte el tema [Factoría de datos: reglas de nomenclatura](naming-rules.md) para las reglas de nomenclatura para los artefactos de Factoría de datos.
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos. 
4. Para el **grupo de recursos**, realice uno de los siguientes pasos:
     
      - Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable. 
      - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
      En algunos de los pasos de esta guía de inicio rápido se supone que se usa el nombre: **ADFTutorialResourceGroup** para el grupo de recursos. Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
4. Seleccione **V2 (versión preliminar)** como **versión**.
5. Seleccione la **ubicación** de Data Factory. Actualmente, puede crear factorías de datos V2 únicamente en la región **oriental de EE. UU**. Sin embargo, los almacenes de datos y procesos que se usan en las factorías de datos pueden estar en otras regiones. 
6. Seleccione **Anclar al panel**.     
7. Haga clic en **Crear**.
      
      > [!IMPORTANT]
      > Para crear instancias de Data Factory, es preciso ser miembro del rol [Colaborador de Data Factory](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) en el nivel de grupo de recursos o suscripción.
      > 
      > El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, hacerse públicamente visible.             
3. En el panel, verá el icono siguiente con el estado: **Implementando factoría de datos**. 

    ![icono implementando factoría de datos](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
1. Una vez completada la creación, puede ver la hoja **Data Factory** , tal como se muestra en la imagen.
   
   ![Página principal de Factoría de datos](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)


## <a name="next-steps"></a>Pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Consulte los [tutoriales](tutorial-copy-data-dot-net.md) para obtener información acerca del uso de Data Factory en otros escenarios. 
