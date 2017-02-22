---
title: Conmutaciones por error regionales de Azure DocumentDB | Microsoft Docs
description: "Obtenga información sobre el modo en que funcionan las conmutaciones por error manuales y automáticas con Azure DocumentDB."
services: documentdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 446e2580-ff49-4485-8e53-ae34e08d997f
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 0921464c10d5ca3d426a535d434eab6cf02013e6
ms.openlocfilehash: c234958f5fc1ba0dbcb727e18e733d13ad0c7e71


---
# <a name="regional-failovers-in-azure-documentdb"></a>Conmutaciones por error regionales de Azure DocumentDB
Azure DocumentDB simplifica la distribución global de datos gracias a que ofrece [cuentas de base de datos de varias regiones](documentdb-distribute-data-globally.md) completamente administradas que proporcionan un claro equilibrio entre coherencia, disponibilidad y rendimiento, todo ello con sus garantías correspondientes. Las cuentas de DocumentDB ofrecen alta disponibilidad, latencias de ms de un solo dígito, varios [niveles de coherencia bien definidos](documentdb-consistency-levels.md), conmutación por error regional transparente con API de hospedaje múltiple, y la posibilidad de escalar elásticamente el rendimiento y el almacenamiento en todo el mundo. 

Azure DocumentDB admite tanto las conmutaciones por error definidas por directivas como explícitas que le permiten controlar todo el comportamiento del sistema en caso de errores. En este artículo, nos centramos en los siguientes temas:

* Funcionamiento de las conmutaciones por error manuales en DocumentDB
* Funcionamiento de las conmutaciones por error automáticas en DocumentDB
* Uso de las conmutaciones por error manuales en arquitecturas de aplicación

También obtendrá más información sobre las conmutaciones por error en este vídeo de Azure Friday con Scott Hanselman y el administrador de ingeniería principal Karthik Raman.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

## <a name="a-idconfiguremultiregionapplicationsaconfiguring-multi-region-applications"></a><a id="ConfigureMultiRegionApplications"></a>Configuración de aplicaciones de varias regiones
Antes de profundizar en los modos de conmutación por error, veremos cómo puede configurar una aplicación para que aproveche las ventajas que ofrece la disponibilidad de varias regiones y para que sea resistente en caso de una conmutación por error regional.

* En primer lugar, implemente su aplicación en varias regiones.
* Para garantizar un acceso de baja latencia desde todas las regiones en las que su aplicación se ha implementado, configure la correspondiente [lista de regiones preferidas](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) para cada región a través de uno de los SDK compatibles.

El siguiente fragmento de código muestra cómo inicializar una aplicación de varias regiones. Aquí, la cuenta de DocumentDB `contoso.documents.azure.com` se configura con dos regiones: oeste de EE. UU. y Europa del Norte. 

* La aplicación se implementa en la región oeste de EE. UU. (mediante Azure App Services por ejemplo). 
* Se ha configurado con `West US` como la primera región preferida para lecturas de baja latencia.
* Se ha configurado con `North Europe` como la segunda región preferida (para lograr alta disponibilidad en caso de errores regionales).

En. NET, esta configuración es similar al fragmento de código siguiente:

    ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
    { 
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
    };

    usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
    usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient usClient = new DocumentClient(
        new Uri("https://contosodb.documents.azure.com"),
        "memf7qfF89n6KL9vcb7rIQl6tfgZsRt5gY5dh3BIjesarJanYIcg2Edn9uPOUIVwgkAugOb2zUdCR2h0PTtMrA==",
        usConnectionPolicy);

La aplicación también se implementa en la región de Europa del Norte con el orden de las regiones preferidas invertidas. Es decir, la región de Europa del Norte se especifica primero para lecturas de latencia baja. Después, la región del oeste de EE. UU. se configura como la segunda región preferida para lograr alta disponibilidad en caso de errores regionales.

El siguiente diagrama de arquitectura muestra una implementación de aplicación de varias regiones donde DocumentDB y la aplicación se configuran para que estén disponibles en cuatro regiones geográficas de Azure.  

![Implementación de aplicaciones distribuidas globalmente con Azure DocumentDB](./media/documentdb-regional-failovers/app-deployment.png)

Ahora, veamos cómo el servicio DocumentDB administra los errores regionales mediante conmutaciones por error automáticas. 

## <a name="a-idautomaticfailoversaautomatic-failovers"></a><a id="AutomaticFailovers"></a>Conmutaciones por error automáticas
En el caso excepcional de una interrupción regional de Azure, DocumentDB desencadena automáticamente la conmutación por error de todas las cuentas de DocumentDB presentes en la región afectada. 

**¿Qué ocurre si una región de lectura sufre una interrupción?**

Las cuentas de DocumentDB con una región de lectura que esté entre las regiones afectadas se desconectan automáticamente de su área de escritura y se marcan como desconectadas. Los SDK de DocumentDB implementan un protocolo de detección regional que les permite detectar automáticamente si una región está disponible, y redireccionan las llamadas de lectura a la siguiente región disponible de la lista de regiones preferidas. Si ninguna de las regiones de la lista de las regiones preferidas está disponible, las llamadas se devuelven automáticamente a la región actual de escritura. No es necesario realizar ningún cambio en el código de su aplicación para gestionar las conmutaciones por error regionales. Durante todo este proceso, DocumentDB sigue cumpliendo su garantía de coherencia.

![Errores de la región de lectura en Azure DocumentDB](./media/documentdb-regional-failovers/read-region-failures.png)

Una vez que la región afectada se recupera de la interrupción, el servicio recupera automáticamente todas las cuentas de DocumentDB afectadas de la región. Las cuentas de DocumentDB que tenían una región de lectura en la zona afectada se sincronizan automáticamente con la región de escritura actual y se conecta. Los SDK de DocumentDB detectan la disponibilidad de la nueva región y evalúan si deben seleccionarla como la región de lectura actual de acuerdo con la lista de regiones preferidas configurada para la aplicación. Las siguientes lecturas se redirigen a la región recuperada sin necesidad de realizar cambios en el código de la aplicación.

**¿Qué ocurre si una región de escritura sufre una interrupción?**

Si la región afectada es la región actual de escritura de una determinada cuenta de Azure DocumentDB, entonces la región se marcará automáticamente como desconectadas. Después, se promueve una región alternativa como región de escritura de cada cuenta de DocumentDB afectada. Puede controlar completamente el orden de selección de las regiones para las cuentas de DocumentDB a través de Azure Portal o [mediante programación](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Prioridades de conmutación por error de Azure DocumentDB](./media/documentdb-regional-failovers/failover-priorities.png)

Durante la conmutación por error automática, DocumentDB elige automáticamente la siguiente región de escritura para una cuenta concreta de Azure DocumentDB según el orden de prioridad especificado. 

![Errores de la región de escritura en Azure DocumentDB](./media/documentdb-regional-failovers/write-region-failures.png)

Una vez que la región afectada se recupera de la interrupción, el servicio recupera automáticamente todas las cuentas de DocumentDB afectadas de la región. 

* Las cuentas de DocumentDB cuya región de escritura anterior se encuentre en la zona afectada permanecerán en modo desconectado con disponibilidad de lectura incluso después que se recupere dicha zona. 
* Puede consultar esta región para calcular todas las escrituras no replicadas durante la interrupción realizando una comparación de los datos disponibles en la actual región de escritura. En función de las necesidades de su aplicación, puede realizar una combinación o resolución de conflictos y escribir el conjunto final de cambios en la región de escritura actual. 
* Una vez que haya terminado de combinar los cambios, vuelva a poner la región afectada en línea quitando la región de la cuenta de DocumentDB y volviéndola a agregar. Cuando la región se haya agregado, puede configurarla de nuevo como región de escritura mediante una conmutación por error manual a través de Azure Portal o [mediante programación](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

## <a name="a-idmanualfailoversamanual-failovers"></a><a id="ManualFailovers"></a> Conmutaciones por error manuales

Además de la conmutación por error automática, puede cambiar de forma dinámica y manual la región de escritura actual de una cuenta de DocumentDB a una de las regiones de lectura existentes. Las conmutaciones por error manuales se pueden iniciar a través de Azure Portal o [mediante programación](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

Las conmutaciones por error manuales garantizan que haya **cero pérdida de datos** y **cero pérdida de disponibilidad**, y transfieren sin problemas el estado de escritura de la región de escritura anterior a la nueva para la cuenta de DocumentDB especificada. Al igual que en la conmutación por error automática, el SDK de Azure DocumentDB controla automáticamente los cambios de región de escritura durante las conmutaciones por error manuales y garantiza que las llamadas se redirijan automáticamente a la nueva región de escritura. No se requiere ningún cambio de código o configuración de la aplicación para administrar las conmutaciones por error. 

![Conmutaciones por error manuales de Azure DocumentDB](./media/documentdb-regional-failovers/manual-failovers.png)

Algunos de los escenarios comunes en los que la conmutación por error manual puede ser útil son:

**Modelo de funcionamiento ininterrumpido**: si las aplicaciones tienen patrones de tráfico predecibles en función de la hora del día, puede cambiar periódicamente el estado de escritura a la región geográfica más activa en función de la hora del día.

**Actualización de servicio**: ciertas implementaciones de aplicaciones distribuidas globalmente pueden requerir que se redirija el tráfico a una región diferente mediante Traffic Manager durante actualizaciones de servicio programadas. Dicha implementación de aplicaciones puede usar la conmutación por error manual para mantener el estado de escritura en la región que tendrá tráfico activo durante el período de actualización del servicio.

**Exploraciones de continuidad empresarial y recuperación ante desastres (BCDR)**: la mayoría de las aplicaciones empresariales incluyen pruebas de continuidad empresarial en su proceso de desarrollo y de lanzamiento. Las pruebas de BCDR a menudo son un paso importante de los certificados de cumplimiento, y garantizan la disponibilidad del servicio en caso de una interrupción regional. Puede probar si las aplicaciones que usan DocumentDB para el almacenamiento están preparadas para BCDR activando una conmutación por error manual de su cuenta de DocumentDB o agregando y quitando una región de forma dinámica.

En este artículo, hemos revisado cómo funcionan las conmutaciones por error manuales y automáticas en Azure DocumentDB, y cómo puede configurar sus aplicaciones y cuentas de DocumentDB para que estén disponibles globalmente. Utilice la compatibilidad con replicación global de Azure DocumentDB para mejorar la latencia de extremo a extremo y asegurarse de que estén altamente disponibles incluso en caso de errores de región. 

## <a name="a-idnextstepsanext-steps"></a><a id="NextSteps"></a>Pasos siguientes
* Obtenga información sobre cómo DocumentDB es compatible con la [distribución global](documentdb-distribute-data-globally.md).
* Conozca más sobre la [coherencia global con DocumentDB](documentdb-consistency-levels.md).
* Desarrolle con varias regiones, con el [SDK de Azure DocumentDB](documentdb-developing-with-multiple-regions.md).
* Obtenga información acerca de cómo crear [arquitecturas de sistema de escritura de varias regiones](documentdb-multi-region-writers.md) con Azure DocumentDB.




<!--HONumber=Feb17_HO2-->


