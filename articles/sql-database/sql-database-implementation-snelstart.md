---
title: "Caso práctico de Azure SQL Database: Snelstart | Microsoft Docs"
description: "Sepa cómo SnelStart usa SQL Database para expandir rápidamente sus servicios empresariales con una cifra de 1000 nuevas bases de datos SQL de Azure cada mes."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: fab506b2-439d-4f1a-bdc5-d1d25c80d267
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 3861814974b95f0c65879158cb02ec727a25c99f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Con Azure, SnelStart ha expandido rápidamente sus servicios empresariales con una cifra de 1000 nuevas bases de datos SQL de Azure cada mes
![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart desarrolla conocidos software de administración financiera y empresarial para pequeñas y medianas empresas (pymes) de los Países Bajos. Un personal compuesto por 110 empleados —entre ellos, un equipo de TI de 35 miembros— atiende a 55 000 clientes. Al migrar su infraestructura a la oferta de software como servicio (SaaS) de Azure, SnelStart pudo integrar la mayoría de los servicios, con lo que automatizó la administración mediante un entorno familiar en C#, y optimizó el rendimiento y la escalabilidad sin tener que provocar el exceso o la falta de aprovisionamiento de negocios gracias a los grupos elásticos. Azure proporciona a SnelStart fluidez para migrar clientes entre entornos locales y en la nube.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-SnelStart/player]
> 
> 

## <a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>¿Por qué SnelStart expandió sus servicios del entorno de escritorio a la nube?
> "Gracias a Azure, podemos entregar software más rápidamente, responder a las exigencias del cliente de manera más ágil y escalar soluciones cuando aumenta la demanda".
> 
> Henry Been, arquitecto de software
> 
> 

SnelStart utilizó, durante años y con éxito, un modelo de desarrollo de software tradicional: codificación, empaquetado, envío y repetición. Con el tiempo, el ritmo de los cambios aumentó cada vez más rápido. Las normativas cambiaban con frecuencia y los clientes necesitaban maneras más fáciles de procesar los registros financieros y de colaborar con sus contables y representantes gubernamentales para adaptarse al ritmo de dichos cambios.

"Enviar software a los clientes era costoso y tenía limitaciones", explica henry Been, arquitecto de software. "Los gastos de producción, empaquetado y envío limitaban la frecuencia con la que podíamos publicar software. Teníamos que empaquetar actualizaciones para realizar envíos periódicos, y esto nos complicaba cumplir las necesidades cambiantes de nuestros clientes. Nunca podíamos asegurar que nuestros clientes actualizaran a la versión más reciente del producto. Por lo tanto, teníamos que prestar servicios de asistencia para varias versiones, así que era muy difícil realizar las tareas de soporte de manera eficaz".

Been explica: "Queríamos una manera de programar y lanzar las actualizaciones a una velocidad alta, de modo que pudiésemos innovar y crear nuevos servicios para nuestros clientes de forma más rápida. También buscábamos una forma de automatizar más los procesos con el fin de simplificar las necesidades de administración empresarial de nuestros clientes".

Para SnelStart, la solución fue ampliar sus servicios convirtiéndose en un proveedor de SaaS en la nube. La plataforma Azure SQL Database ayudó a SnelStart a cumplir este objetivo sin tener que realizar la importante sobrecarga de recursos de TI que habría requerido una solución de infraestructura como servicio (IaaS).

El modelo de nube también permite a SnelStart corregir los errores y ofrecer nuevas características rápidamente, sin necesidad de que los clientes tengan que descargar y actualizar el software. Según Been: "Los servicios en la nube de Azure nos permite responder más rápido a los requisitos de terceros en constante evolución. En lugar de tener que enviar una nueva versión a miles de clientes, podemos adaptar la información transmitida desde nuestra aplicación de escritorio a los nuevos formatos que exigen los terceros".

## <a name="a-modern-company-with-traditional-roots"></a>Una empresa moderna con raíces tradicionales
SnelStart es una empresa moderna, ágil y de alta tecnología de raíces humilde que nació en el año 1964, cuando los fundadores crearon la empresa como fabricante de piezas de instrumentos musicales. SnelStart empezó realmente a dedicarse al desarrollo de software a finales de la década de los 80, durante la proliferación de los equipos personales. La empresa necesitaba una mejor alternativa al software de contabilidad que tenían en esos momentos, así que tomó cartas en el asunto. En 1982, la compañía construyó los cimientos para convertir finalmente SnelStart en un software de contabilidad. Desde el principio, el software destacaba por su simplicidad y velocidad, tanto que la empresa cambió su modelo de negocio y se convirtió en una compañía de software.

En 1995, SnelStart publicó su primera aplicación de contabilidad para Windows. La aplicación, que se basa en Microsoft Visual Basic 1.0 y las bases de datos de Microsoft Access, ayudó a ampliar la base de clientes a más de 5000 usuarios.

En la actualidad, SnelStart es un importante proveedor de aplicaciones de línea de negocio (LOB) y administración empresarial destinadas a pymes neerlandesas y emprendedores autónomos. Tal y como explica Carlo Kuip, arquitecto de TI: "Nuestro objetivo es proporcionar a nuestros clientes una automatización completa de los servicios de administración empresarial".

## <a name="optimizing-performance-and-cost-with-elastic-pools"></a>Optimización del rendimiento y los costos con los grupos elásticos
SnelStart fue pionera a gran escala en utilizar los grupos elásticos. Gracias a ellos, la empresa puede limitar los costos y administrar los requisitos de rendimiento de manera más eficaz. Según Been: "Al utilizar grupos elásticos, podemos optimizar el rendimiento según las necesidades de nuestros clientes sin provocar el exceso de aprovisionamiento. Sería bastante costoso aprovisionar según la carga pico. En su lugar, la opción de compartir recursos entre varias bases de datos de escaso uso nos permite crear una solución que funciona bien y es rentable".

## <a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Azure SQL Databases ayuda a agregar los datos en contenedores para disfrutar de aislamiento y seguridad
Azure SQL Database permite a SnelStart trasladar a Azure, de manera sencilla y transparente, los datos de administración empresarial locales de los clientes. Azure SQL Database es un contenedor cómodo que proporciona aislamiento, un límite de autenticación, autorización y funcionalidades de copia de seguridad y restauración sencillas. Las bases de datos proporcionan un modelo conceptual adecuado para la administración empresarial. Según Carlo Kuip, arquitecto de TI: "Los elementos dentro de este límite de contenedor incluyen datos confidenciales fundamentales para una empresa, y como se almacenan en una base de datos aislada, pueden mantenerse bien protegidos. Podemos administrar la autorización en el nivel de las bases de datos, e incluso automatizar su administración y escalado horizontal sin necesidad de tener en plantilla administradores de bases de datos (DBA)".

Azure SQL Data Warehouse también desempeña un papel importante en la seguridad y administración de SnelStart al ayudar a la empresa a recopilar datos de telemetría, como la detección de intrusiones, el registro de actividad de los usuarios y la conectividad.

## <a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure elimina la sobrecarga para que los desarrolladores pueden dedicar más tiempo a proporcionar valor
El modelo de la plataforma de Azure quitó la sobrecarga de infraestructura y permitió a SnelStart automatizar las implementaciones con las bibliotecas de administración en C#. Como indica Kuip: "Pudimos aumentar nuestras operaciones con muy poco personal y mejorar al mismo tiempo las opciones de recuperación ante desastres, la velocidad y la escalabilidad para nuestros clientes. La transformación en el desarrollo de servicios liberó recursos para centrarse en nuevos servicios y características, en lugar de simplemente actualizar el código existente para adaptarlo a los nuevos códigos impositivos o normativas". "Automatizando la administración y el uso de la oferta de SaaS podemos ofrecer más valor a nuestros clientes sin tener que realizar grandes inversiones en personal de operaciones SaaS", afirma. Por ejemplo, mediante el uso de los grupos elásticos de Azure, SnelStart pudo agregar diferentes características nuevas, incluidas una integración de datos del cliente más sólida con los bancos, la facturación de nuevos servicios, las comprobaciones de antecedentes de pequeñas empresas y los servicios de correo electrónico.

> "En tan solo los primeros meses del 2016, hemos ampliado nuestras implementaciones de Azure SQL Database de 5500 aproximadamente a más de 12 000. En la actualidad, estamos agregando casi 1000 bases de datos cada mes".
> 
> Henry Been, arquitecto de software
> 
> 

La administración de bases de datos se automatiza aún más mediante la característica de trabajos elásticos. Tal y como explica Kuip: "Valoramos sumamente la detección automática de bases de datos en una instancia (servidor) de SQL Database". Gracias a esto, SnelStart puede ejecutar operaciones de administración en su base de clientes en constante crecimiento sin asumir una sobrecarga extra.

La empresa también está desarrollando una API que actúa como agente entre los datos del cliente y las aplicaciones creadas por los asociados de software terceros. Como explica Kuip: "Esta API permitirá a otros proveedores agregar funcionalidad a nuestro software, como la eliminación de la entrada de datos para las facturas y otros documentos". Los clientes ya no tendrán que escribir datos manualmente en las facturas dentro del software de contabilidad para pequeñas empresas; SnelStart intercambiará directamente la información necesaria. De este modo, los clientes asocian sus tareas de administración empresarial con el ecosistema de la información que está emergiendo de la transformación digital del sector.  

## <a name="how-azure-services-enable-saas-for-snelstart"></a>Cómo los servicios de Azure permiten que SnelStart use el modelo SaaS
Gracias a Azure, SnelStart puede prestar servicios a sus clientes y contables de forma más sencilla en la nube. Por ejemplo, los clientes y contables pueden compartir información accediendo directamente a las API cliente de SnelStart, que está hospedada en Azure. Kuip explica: "Estos servicios reutilizables están disponibles en nuestras aplicaciones web orientadas al cliente. Además, proporcionan la infraestructura y las funciones para permitir que los clientes y el software de terceros que usan puedan acceder a las funcionalidades de administración empresarial. Por ejemplo, características de configuración del producto, administración de reglas de firewall y gestión de procesos de larga ejecución, como las copias de seguridad".

> Nuestro objetivo es proporcionar a nuestros clientes una automatización completa de los servicios de administración empresarial". 
> 
> Carlo Kuip, arquitecto de TI
> 
> 

Además, los servicios web SnelStart permiten a los clientes y contables acceder fácilmente a los datos en grupos de bases de datos elásticas de Azure SQL Database. Este modelo de SaaS, junto con la elasticidad de las bases de datos y Azure Resource Manager, brinda a SnelStart las características de escalabilidad que complementan cada implementación de Azure. La implementación se automatiza por completo mediante el uso de bibliotecas de administración en C#.

![Arquitectura de SnelStart](./media/sql-database-implementation-snelstart/figure1.png)

Figura 1. Desde junio de 2016, SnelStart tiene más de 11 000 bases de datos y más de 50 grupos elásticos

## <a name="simplicity-from-the-cloud"></a>La simplicidad de la nube
Desde que pasaran a utilizar una solución basada en la nube de Azure, SnelStart pudo responder al rápido aumento de clientes, y ofrecer servicios y características innovadoras. Según Been: "Gracias a Azure, podemos distribuir a nuestros clientes actualizaciones de forma casi continua sin aumentar nuestro personal de operaciones. Además, obtenemos integradas el resto de las características importantes de Azure, como la escalabilidad y la recuperación ante desastres".

> "Cuando salimos de Redmond, recibí una llamada de un desarrollador de los Países Bajos sobre un problema que se había producido. Pudimos ponernos en contacto con Microsoft para cambiar su entorno de producción en 48 horas con el fin de resolver nuestro problema".
> 
> Henry Been, arquitecto de software
> 
> 

SnelStart también valora el sólido vínculo que han establecido con el equipo de Microsoft Azure SQL Database. Como explica Kuip: "Tenemos debates sobre características y cómo usar la tecnología, algo que apreciamos ambas partes".
El objetivo inmediato de SnelStart es seguir aumentando la base de clientes satisfechos. Como afirma Been: "Sin las limitaciones técnicas y de recursos que tuvimos como ISV, ya no tenemos horizonte de crecimiento".

## <a name="more-information"></a>Más información
* Para obtener más información sobre los grupos elásticos de Azure, consulte [este artículo](sql-database-elastic-pool.md).
* Para obtener más detalles sobre los roles web y de trabajo, consulte [este artículo](../fundamentals-introduction-to-azure.md#compute).    
* Si quiere conocer más información sobre Azure este artículo, lea [este artículo](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)
* Para obtener más información sobre SnelStart, vea [este artículo](http://www.snelstart.nl).

