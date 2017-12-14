---
title: "Herramienta de evaluación de soluciones Cortana Intelligence | Microsoft Docs"
description: "Como partner de Microsoft, estos son los pasos que debe seguir para publicar su solución Cortana Intelligence en AppSource."
services: machine-learning
documentationcenter: 
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: anupams;v-bruham;garye
ms.openlocfilehash: c9ea8164d5866662a7ed81672ee1ba776603b193
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="cortana-intelligence-solution-evaluation-tool"></a>Herramienta de evaluación de soluciones Cortana Intelligence
## <a name="overview"></a>Información general
Puede usar la herramienta de evaluación de la soluciones Cortana Intelligence para evaluar el cumplimiento de los procedimientos recomendados de Microsoft por parte de las soluciones de análisis avanzado. Microsoft se complace en trabajar con los partners (ISV o SI) para proporcionar soluciones de alta calidad para los clientes, los distribuidores y la implementación. Este documento le guiará durante el proceso de uso de la herramienta de evaluación de soluciones con su solución y además especifica procedimientos recomendados concretos.

## <a name="getting-started"></a>Introducción
[Descargue](https://aka.ms/aa-evaluation-tool-download) e instale la herramienta de evaluación de soluciones Cortana Intelligence.

Requisitos previos:
- Windows 10: [Sitio oficial de Windows 10](https://www.microsoft.com/en-us/windows)
- Azure PowerShell: [Instalación y configuración de Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).

## <a name="identifying-your-app"></a>Identificación de la aplicación
Una vez finalizada la instalación, abra la herramienta y empiece con la primera evaluación.

![Herramienta de evaluación abierta](./media/cortana-intelligence-appsource-evaluation-tool/1-open-evaluation-tool.png)

Especifique la información de identificación de la solución.

![Conexión a suscripción de Azure](./media/cortana-intelligence-appsource-evaluation-tool/2-connect-azure-subscription.png)

Conéctese a la suscripción de Azure y especifique el grupo de recursos que contiene la aplicación.

![Selección de recursos](./media/cortana-intelligence-appsource-evaluation-tool/3-select-resources.png)

Una vez cargado el grupo de recursos, seleccione los recursos incluidos en la solución e identifique la accesibilidad de los recursos de datos como:
- Ingesta de datos
- Consumo
- Interno

Esta información se usa para una mejor comprensión de cómo usan la solución distintos componentes y para asegurarse de que los componentes para el usuario son coherentes con los procedimientos recomendados.

### <a name="ingestion"></a>Ingesta de datos
En este caso, ingesta hace referencia a cualquier origen de datos que se use para extraer datos de fuera de la solución o que cualquier servicio de fuera de la solución use para insertar datos en ella.

### <a name="consumption"></a>Consumo
Consumo en este caso hace referencia a cualquier conjunto de datos que se use para insertar datos en los usuarios finales, ya sea directa o indirectamente. Por ejemplo:
- Los conjuntos de datos usados en consultas directas de PowerBI.
- Los conjuntos de datos consultados en una aplicación web.

>[!NOTE]
Si se usa un recurso concreto para la ingesta y el consumo, elija **Consumo**.

### <a name="internal"></a>Interno
Use interno para los recursos de datos que solo se usan en el procesamiento interno de la aplicación.

Luego se le pedirá que proporcione credenciales válidas para las bases de datos especificadas en el paso anterior:

![Establecimiento de requisitos previos](./media/cortana-intelligence-appsource-evaluation-tool/4-set-test-prerequisites.png)

## <a name="solution-test-cases"></a>Casos de prueba de la solución
La herramienta de soluciones realizará una serie de pruebas automatizadas en la solución.

![Establecimiento de ejecución de pruebas](./media/cortana-intelligence-appsource-evaluation-tool/5-set-test-execution.png)

Después de terminar las pruebas se le pedirá que proporcione una explicación o justificación de por qué la solución no satisface el requisito.

![Justificación comercial](./media/cortana-intelligence-appsource-evaluation-tool/6-provide-business-justification.png)

Por ejemplo, si la solución se publica en Azure SQL DW, las pruebas de evaluación exigen que también lo haga en Azure Analysis Services. 

La solución podría usar máquinas virtuales de IaaS con SQL Server Analysis Services en lugar de Azure Analysis Services. Esta sería una razón aceptable para el error de la prueba.
## <a name="packaging-your-evaluation-results"></a>Empaquetado de los resultados de evaluación
Después de terminar los casos de prueba, el paquete de evaluación se exporta a un archivo zip y se le pide que proporcione comentarios sobre la herramienta de evaluación. 

Tiene que compartir este archivo zip con los resultados de la prueba con Microsoft para que se pueda evaluar la solución antes de obtener la aprobación con el fin de agregarla a AppSource

![Puntuación de la herramienta de evaluación](./media/cortana-intelligence-appsource-evaluation-tool/7-grade-evaluation-tool.png)

La sección anterior de este artículo trata diversas características de la herramienta; ahora vamos a revisar los tipos de procedimientos recomendados que evalúa esta herramienta.

## <a name="security-evaluation-considerations"></a>Consideraciones sobre la evaluación de seguridad
### <a name="databases-should-use-azure-active-directory-authentication"></a>Las bases de datos deberían usar la autenticación de Azure Active Directory
Los recursos de Azure SQL o Azure SQL DW de la solución deberían tener habilitada la autenticación de Azure Active Directory (AAD). AAD proporciona una única ubicación para administrar todas las identidades y todos los roles.

| Para más información sobre | Vea este artículo |
| --- | --- |
| AAD con SQL Database y SQL Data Warehouse | [Use Azure Active Directory Authentication for authentication with SQL Database or SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) (Usar la autenticación de Azure Active Directory con SQL Database o SQL Data Warehouse) |
| Configuración y administración de AAD | [Configuración y administración de la autenticación de Azure Active Directory con SQL Database o SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) |
| Autenticación de aplicaciones web de Azure | [Autenticación y autorización en Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) |
| Configuración de aplicaciones web con AAD | [Configuración de la aplicación de App Service para usar el inicio de sesión de Azure Active Directory](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)|

### <a name="datasets-accessible-to-end-users-should-support-role-based-access-control"></a>Los conjuntos de datos accesibles a los usuarios finales deberían admitir el control de acceso basado en roles
Al ejecutar la herramienta de evaluación, se le pedirá que especifique cualquier recurso de elaboración de informes o publicación. Se supone que estos recursos están diseñados para el acceso de los usuarios finales, no de los desarrolladores. Estos recursos deberían proporcionar control de acceso basado en roles (RBAC) con el fin de garantizar que los usuarios finales solo puedan acceder a los datos autorizados.

En concreto, cualquiera de los siguientes recursos de Azure se puede configurar con RBAC y se considera aceptable:
- Secure HDInsight, vea [Introducción a la seguridad de Hadoop con clústeres de HDInsight unidos a dominio (versión preliminar)](https://docs.microsoft.com/azure/hdinsight/hdinsight-domain-joined-introduction)
- Azure SQL, vea [AAD authentication with Azure SQL]( https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) (Autenticación de SSD con Azure SQL)
- Azure Analysis Services, vea [Manage database roles and users for Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-database-users) (Administrar usuarios y roles de base de datos de Azure Analysis Services)
- Azure SQL Data Warehouse (Tenga en cuenta que, dado que SQL DW admite RBAC, no se recomienda para el acceso directo de usuarios finales).

Si usa otro tipo de recurso que admita RBAC, especifíquelo así en la justificación del caso de prueba.

### <a name="azure-data-lake-store-should-use-at-rest-encryption"></a>Azure Data Lake Store debería usar cifrado en reposo
Azure Data Lake Store (ADLS) admite el cifrado en reposo de forma predeterminada mediante claves de cifrado administradas por ADLS. También puede configurar el cifrado con Azure Key Vault.

Para más información sobre cómo especificar la configuración de cifrado de ADLS, vea [Creación de una cuenta de Almacén de Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal#create-an-azure-data-lake-store-account).

### <a name="azure-sql-and-azure-sql-data-warehouse-should-use-encryption"></a>Azure SQL y Azure SQL Data Warehouse deberían usar cifrado
Azure SQL y Azure SQL DW admiten el cifrado de datos transparente (TDE), que proporciona cifrado y descifrado en tiempo real de datos y archivos de registro.

| Para más información sobre | Vea este artículo |
| --- | --- |
| Cifrado de datos transparente (TDE)  | [Cifrado de datos transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Azure SQL Data Warehouse con TDE | [Introducción al cifrado de datos transparente (TDE)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-encryption-tde-tsql) |
| Configuración de Azure SQL con TDE | [Cifrado de datos transparente con Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) |
| Configuración de Azure SQL con Always Encrypted | [Always Encrypted: protección de datos confidenciales en SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)|

Además de TDE, Azure SQL también admite Always Encrypted, una nueva tecnología de cifrado de datos que garantiza que los datos se cifren no solo en reposo y durante el movimiento entre cliente y servidor, sino también durante su uso al ejecutar comandos en el servidor.

### <a name="any-virtual-machines-must-be-deployed-from-the-azure-marketplace"></a>Todas las máquinas virtuales deben implementarse desde Azure Marketplace
Para proporcionar un nivel de seguridad coherente en AppSource, es necesario que las máquinas virtuales implementadas como parte de una solución de Cortana Intelligence se certifiquen y se publiquen en Azure Marketplace.

Para buscar en la lista actual de imágenes de Azure Marketplace, vea [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute).

Para más información sobre cómo publicar una imagen de máquina virtual para Azure Marketplace, vea [Guide to create a virtual machine image for the Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation) (Guía para crear una imagen de máquina virtual para Azure Marketplace).

## <a name="scalability-evaluation-considerations"></a>Consideraciones sobre la evaluación de escalabilidad
### <a name="cortana-intelligence-solutions-should-include-a-scalable-big-data-platform"></a>Las soluciones de Cortana Intelligence deberían incluir una plataforma escalable de macrodatos
Las soluciones de Cortana Intelligence deberían escalarse a tamaños de datos muy grandes. En Azure, esto significa que deberían incluir una de las dos plataformas de datos a escala de petabytes:
- Almacén de Azure Data Lake
- Azure SQL Data Warehouse

Si la solución no necesita compatibilidad con estos tamaños de datos o si usa una plataforma de datos alternativa, explíquelo en la justificación del caso de prueba.
### <a name="cortana-intelligence-solutions-should-include-dedicated-ingestion-data-environments"></a>Las soluciones de Cortana Intelligence deberían incluir entornos dedicados de datos de ingesta
Las soluciones de Cortana Intelligence en general deberían evitar la inserción directa de datos en orígenes de datos relacionales. En lugar de eso, los datos sin procesar deberían almacenarse en un entorno no estructurado, con inserciones o actualizaciones independientes en cualquier almacén relacional con Azure Data Factory.

Para más información sobre cómo copiar datos con Azure Data Factory, vea [Tutorial: Crear una canalización con la actividad de copia mediante Visual Studio](https://docs.microsoft.com/azure/data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio).

### <a name="azure-sql-data-warehouse-should-use-polybase-for-data-ingestion"></a>Azure SQL Data Warehouse debería usar PolyBase para la ingesta de datos
Azure SQL DW admite PolyBase para la ingesta de datos paralela altamente escalable. PolyBase permite usar Azure SQL DW para emitir consultas en conjuntos de datos externos almacenados en Azure Blob Storage o Azure Data Lake Store. Esto proporciona un rendimiento superior al de los métodos alternativos de actualizaciones masivas.

Para obtener instrucciones sobre cómo empezar a usar PolyBase y Azure SQL DW, vea [Carga de datos con PolyBase en SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-load-with-polybase).

Para más información sobre los procedimientos recomendados con PolyBase y Azure SQL DW, vea [Guía para el uso de PolyBase en SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide).

## <a name="availability-evaluation-considerations"></a>Consideraciones sobre la evaluación de disponibilidad

### <a name="datasets-accessible-to-end-users-should-support-a-large-volume-of-concurrent-users"></a>Los conjuntos de datos accesibles a los usuarios finales deberían admitir un gran volumen de usuarios simultáneos
Al ejecutar la herramienta de evaluación, se le pedirá que especifique cualquier recurso de elaboración de informes o publicación. Se supone que estos recursos están diseñados para el acceso de los usuarios finales, no de los desarrolladores. Estos recursos deberían admitir un número mediano o grande de usuarios simultáneos.

En concreto, Azure SQL Data Warehouse NO debería ser el único origen de datos disponible para los usuarios finales. Si Azure SQL DW se proporciona como un recurso para usuarios avanzados, Azure Analysis Services debería estar disponible para los usuarios normales.

Para más información sobre los límites de simultaneidad de Azure SQL DW, vea [Simultaneidad y administración de cargas de trabajo en SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency).

Para más información sobre Azure Analysis Services, vea [Analysis Services Overview](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) (Información general sobre Analysis Services).

### <a name="azure-sql-resources-should-have-a-read-only-replica-for-failover"></a>Los recursos de Azure SQL deberían tener una réplica de solo lectura para la conmutación por error
Las bases de datos de Azure SQL admiten la replicación geográfica en una instancia secundaria. Esta instancia se puede usar como instancia de conmutación por error para proporcionar aplicaciones de alta disponibilidad.

Para más información sobre la replicación geográfica para bases de datos de Azure SQL, vea [SQL Database GEO Replication overview](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) (Información general sobre la replicación geográfica de SQL Database).

Para obtener instrucciones sobre cómo configurar la replicación geográfica para Azure SQL, vea [Configuración de la replicación geográfica activa para Azure SQL Database con Transact-SQL](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-transact-sql).

### <a name="azure-sql-data-warehouse-should-have-geo-redundant-backups-enabled"></a>Azure SQL Data Warehouse debería tener habilitadas las copias de seguridad con redundancia geográfica
Azure SQL DW admite copias de seguridad diarias en almacenamiento con redundancia geográfica. Esta replicación geográfica garantiza que se pueda restaurar el almacenamiento de datos incluso en situaciones en que no se pueda acceder a las instantáneas almacenadas en la región principal. Esta característica está activada de forma predeterminada y no se debe deshabilitar para las soluciones de Cortana Intelligence.

Para más información sobre las copias de seguridad y la restauración de Azure SQL DW, vea [Copias de seguridad de SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-backups).

### <a name="virtual-machines-should-be-configured-with-availability-sets"></a>Las máquinas virtuales deberían configurarse con conjuntos de disponibilidad
Las máquinas virtuales de Azure deberían configurarse en conjuntos de disponibilidad para minimizar el impacto de los eventos de mantenimiento planeados y no planeados.

Para más información sobre la disponibilidad de las máquinas virtuales de Azure, vea [Administración de la disponibilidad de las máquinas virtuales Windows en Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="other-evaluation-considerations"></a>Otras consideraciones sobre la evaluación
### <a name="cortana-intelligence-apps-should-use-a-centralized-tool-for-data-orchestration"></a>Las aplicaciones de Cortana Intelligence deberían usar una herramienta centralizada de orquestación de datos
Con una única herramienta para administrar y programar la transformación y el movimiento de los datos se garantiza la coherencia de los datos críticos. Proporciona una lógica clara en torno a la lógica de reintento, la administración de dependencias, las alertas y los registros, etc. Se recomienda usar [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-introduction) para la orquestación de datos en Azure.

Si está usando una herramienta que no sea Azure Data Factory para la orquestación de datos, describa la herramienta o herramientas que use.
### <a name="azure-machine-learning-models-should-be-retrained-using-azure-data-factory"></a>Los modelos de Azure Machine Learning deberían volver a aprender con Azure Data Factory
Azure Machine Learning (AzureML) proporciona herramientas fáciles de usar para la creación e implementación del modelado de predicción y las canalizaciones de aprendizaje automático. Pero es importante que las implementaciones de producción de estos modelos de AzureML no se basen en un único conjunto de datos fijo, sino que se adapten a la dinámica cambiante de fenómenos del mundo real.

Para más información sobre la creación de servicios web de reaprendizaje en AzureML, vea [Volver a entrenar modelos de Machine Learning mediante programación](https://docs.microsoft.com/azure/machine-learning/machine-learning-retrain-models-programmatically).

Para más información sobre la automatización del proceso de aprendizaje de modelos mediante Azure Data Factory, vea [Updating Azure Machine Learning models using Update Resource Activity](https://docs.microsoft.com/azure//data-factory/v1/data-factory-azure-ml-update-resource-activity) (Actualización de los modelos de Azure Machine Learning con la actividad de actualización de recurso).

## <a name="existing-documentation"></a>Documentación existente
[Microsoft Azure Certified para hacer crecer su negocio en la nube](https://azure.microsoft.com/en-us/marketplace/programs/certified/)

[Microsoft Azure Certified: Cortana Intelligence](https://azure.microsoft.com/en-us/marketplace/programs/certified/cortana/)

