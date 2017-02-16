---
title: "¿Qué es el soporte de protocolo de DocumentDB para MongoDB? | Microsoft Docs"
description: "¿Qué es el soporte de protocolo de DocumentDB para MongoDB? Permite usar Azure DocumentDB, un servicio administrado basado en la nube, como almacén de datos para aplicaciones escritas para MongoDB."
keywords: "Qué es MongoDB"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 0703fa4f4d3eb9f23aa9b3c0b90267fac9557f13
ms.openlocfilehash: 876fff6a63ccf1a3c10a6852927c60b454436954


---
# <a name="what-is-documentdb-protocol-support-for-mongodb"></a>¿Qué es el soporte de protocolo de DocumentDB para MongoDB?

Las bases de datos de DocumentDB se pueden utilizar ahora como almacenes de datos para aplicaciones escritas para MongoDB. Esto significa que mediante [controladores](https://docs.mongodb.org/ecosystem/drivers/) existentes para bases de datos de MongoDB, la aplicación escrita para MongoDB se puede comunicar ahora con DocumentDB y usar bases de datos de DocumentDB en lugar de bases de datos de MongoDB. En muchos casos, puede cambiar del uso de MongoDB a DocumentDB con solo cambiar una cadena de conexión. Mediante esta funcionalidad, los clientes pueden crear y ejecutar fácilmente aplicaciones de base de datos de MongoDB en la nube de Azure, y aprovechar las bases de datos NoSQL totalmente escalables y administradas de DocumentDB, al tiempo que se siguen usando conocimientos y herramientas conocidos para MongoDB.

## <a name="what-is-the-benefit-of-using-documentdb-protocol-support-for-mongodb"></a>¿Cuál es la ventaja de usar la compatibilidad con el protocolo de DocumentDB para MongoDB?
**Sin administración del servidor**: DocumentDB es un servicio completamente administrado, lo que significa que no tiene que administrar ninguna infraestructura o máquina virtual por su cuenta. DocumentDB está disponible en más de 20 [regiones de Azure](https://azure.microsoft.com/regions/services/).

**Escala sin límites**: puede escalar la capacidad de proceso y el almacenamiento de manera independiente y elástica. Puede agregar capacidad para atender millones de solicitudes por segundo con facilidad.

**Nivel empresarial**: DocumentDB admite varias réplicas locales para ofrecer protección de datos y disponibilidad del 99,99 % frente a errores locales y regionales. DocumentDB presenta [certificaciones de conformidad](https://www.microsoft.com/trustcenter) y características de seguridad de nivel empresarial. 

**Compatibilidad con MongoDB**: compatibilidad diseñada del protocolo de DocumentDB con MongoDB. Puede usar su código, sus aplicaciones, sus controladores y sus herramientas existentes para trabajar con DocumentDB. 

## <a name="how-to-get-started"></a>Primeros pasos
Cree una cuenta de DocumentDB con compatibilidad del protocolo para MongoDB en [Azure Portal](https://portal.azure.com) y cambie la conexión a la nueva cuenta. 

*Y ya está.*

Para obtener instrucciones más detalladas, siga los pasos para [crear una cuenta](documentdb-create-mongodb-account.md) y [conectar con su cuenta](documentdb-connect-mongodb-account.md).

## <a name="next-steps"></a>Pasos siguientes
* Para crear una cuenta de DocumentDB, siga los pasos que se describen en el tutorial [Creación de una cuenta de DocumentDB con soporte de protocolo para MongoDB mediante Azure Portal](documentdb-create-mongodb-account.md).
* Para saber cómo obtener la información de la cadena de conexión de su cuenta, siga los pasos que se indican en [Conexión de una cuenta de DocumentDB con compatibilidad de protocolo para MongoDB](documentdb-connect-mongodb-account.md).
* Para aprender a crear una conexión entre su bases de datos de DocumentDB y una aplicación de MongoDB en MongoChef, siga los pasos que se indican en [Uso de MongoChef con una cuenta de DocumentDB con soporte de protocolo para MongoDB](documentdb-mongodb-mongochef.md).
* Explore DocumentDB con soporte de protocolo para buscar [ejemplos](documentdb-mongodb-samples.md)de MongoDB.




<!--HONumber=Nov16_HO4-->


