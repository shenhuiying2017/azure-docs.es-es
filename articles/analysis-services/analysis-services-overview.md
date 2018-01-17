---
title: "Introducción a Azure Analysis Services | Microsoft Docs"
description: "Obtenga una visión general de Analysis Services en Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/04/2017
ms.author: owend
ms.openlocfilehash: fe46b3c099c4fd4e04a39e2b2f127c1206711e60
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="azure-analysis-services-overview"></a>Introducción a Azure Analysis Services
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services proporciona modelado de datos de nivel empresarial en la nube. Es una plataforma totalmente administrada como servicio (PaaS), que se integra con los servicios de la plataforma de datos de Azure. 

Con Analysis Services, puede combinar datos de diversos orígenes, definir métricas y proteger los datos en un modelo de datos semántico único y de confianza. El modelo de datos proporciona a los usuarios una manera más fácil y rápida de examinar grandes cantidades de datos con aplicaciones cliente como Power BI, Excel, Reporting Services y aplicaciones de terceros o personalizadas.

![Orígenes de datos](./media/analysis-services-overview/aas-overview-data-sources.png)

Vea [este vídeo](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4) para averiguar cómo Azure Analysis Services se integra en las funcionalidades globales de BI de Microsoft y cómo puede beneficiarse de tener sus modelos de datos en la nube.

## <a name="built-on-sql-server-analysis-services"></a>Basado en SQL Server Analysis Services
Azure Analysis Services es compatible con muchas de las estupendas características que ya se encuentran en SQL Server Analysis Services Enterprise Edition. Azure Analysis Services admite modelos tabulares en los [niveles de compatibilidad](analysis-services-compat-level.md) 1200 y 1400. Se admiten todas las traducciones, particiones, seguridad de nivel de fila y relaciones bidireccionales. Los modos In-memory y DirectQuery permiten consultas increíblemente rápidas en conjuntos de datos enormes y complejos.

Los modelos tabulares ofrecen un desarrollo rápido y son altamente personalizables. Para los desarrolladores, los modelos tabulares incluyen el modelo de objetos tabulares (TOM) para describir los objetos del modelo. El modelo de objetos tabulares se expone en JSON por medio del [lenguaje de scripting de modelos tabulares (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) y el lenguaje de definición de datos AMO mediante el espacio de nombres [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx).

## <a name="better-with-azure"></a>Mejor con Azure
Azure Analysis Services se integra con muchos servicios de Azure lo que le permite compilar soluciones de análisis sofisticadas. La integración con [Azure Active Directory](../active-directory/active-directory-whatis.md) proporciona acceso seguro, basado en roles, a los datos críticos. Se integra en las canalizaciones de [Azure Data Factory](../data-factory/introduction.md) mediante la inclusión de una actividad que carga los datos en el modelo. [Azure Automation](../automation/automation-intro.md) y [Azure Functions](../azure-functions/functions-overview.md) se pueden usar para realizar una orquestación ligera de modelos mediante código personalizado.

## <a name="get-up-and-running-quickly"></a>Póngase rápidamente a pleno funcionamiento
Con Azure Portal, puede [crear un servidor](analysis-services-create-server.md) en minutos. Además, con las [plantillas](../azure-resource-manager/resource-manager-create-first-template.md) de Azure Resource Manager y PowerShell, puede aprovisionar servidores mediante una plantilla declarativa. Con una única plantilla, puede implementar varios servicios junto con otros componentes de Azure como las cuentas de almacenamiento y Azure Functions. 

Una vez que haya creado un servidor, puede crear un modelo tabular directamente en Azure Portal. Con la nueva [característica Diseñador web](analysis-services-create-model-portal.md) (versión preliminar), se puede conectar a orígenes de datos de Azure SQL Database y Azure SQL Data Warehouse, o importar un archivo .pbix de Power BI Desktop. Las relaciones entre las tablas se crean automáticamente, y puede crear medidas o editar el archivo model.bim en formato json directamente desde el explorador.

## <a name="scale-to-your-needs"></a>Adaptación a sus necesidades

### <a name="the-right-tier-when-you-need-it"></a>El nivel correcto cuando lo necesite

Azure Analysis Services está disponible en los niveles Desarrollador, Básico y Estándar. Dentro de cada nivel, los costos del plan varían según la potencia de procesamiento, las QPU y el tamaño de la memoria. Cuando cree un servidor, seleccione un plan dentro de un nivel. Puede mejorar o disminuir el plan dentro del mismo nivel o cambiar a un nivel superior, pero no se puede cambiar de un nivel superior a uno inferior.

Suba o baje de nivel o realice una pausa en el servidor. Use Azure Portal o tenga un control total al instante mediante PowerShell. Pague solo por lo que usa. Para aprender más sobre los distintos planes y niveles y usar la calculadora de precios para determinar el plan adecuado para usted, consulte [precios de Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

### <a name="scale-out-resources-for-fast-query-responses"></a>Escalado horizontal de los recursos para acelerar las respuestas a las consultas

Con el escalado horizontal de Azure Analysis Services, las consultas de cliente se distribuyen entre varias *réplicas de consulta* de un grupo de consulta. Las réplicas de consulta tienen copias sincronizadas de los modelos tabulares. Al repartir la carga de trabajo de consulta, se pueden reducir los tiempos de respuesta durante las cargas de trabajo de consulta elevadas. Las operaciones de procesamiento de modelos se pueden separar del grupo de consultas, lo que garantiza que las consultas de cliente no resultan afectadas negativamente por las operaciones de procesamiento. Puede crear un grupo de consultas con hasta siete réplicas de consulta adicionales (ocho en total, incluido el servidor). 

Al igual que con el cambio de nivel, puede escalar horizontalmente las réplicas de consulta según sus necesidades. Configure el escalado horizontal en el portal o mediante las API de REST. Para más información, consulte [Azure Analysis Services scale-out](analysis-services-scale-out.md) (Escalado horizontal de Azure Analysis Services).

## <a name="keep-your-data-close"></a>Mantener los datos a mano
Los servidores de Azure Analysis Services se pueden crear en las siguientes [regiones de Azure](https://azure.microsoft.com/regions/):

| América | Europa | Asia Pacífico |
|----------|--------|--------------|
|  Sur de Brasil<br> Centro de Canadá<br> Este de EE. UU. 2<br> Centro-Norte de EE. UU<br> Centro-Sur de EE. UU<br> Centro occidental de EE.UU.<br> Oeste de EE. UU | Europa del Norte<br> Sur del Reino Unido 2<br> Europa occidental |   Sudeste de Australia<br> Este de Japón<br> Sudeste asiático<br> Oeste de la India  |

Constantemente se agregan nuevas regiones, por lo que esta lista puede estar incompleta. Elija una ubicación cuando cree el servidor en Azure Portal o mediante las plantillas de Azure Resource Manager. Para obtener el mejor rendimiento, elija la ubicación más cercana a su base de usuarios más grande. Garantice una [alta disponibilidad](analysis-services-bcdr.md) mediante la implementación de los modelos en servidores redundantes de varias regiones.

## <a name="migrate-your-existing-tabular-models"></a>Migración de los modelos tabulares existentes
Si ya tiene soluciones locales de modelos de SQL Server Analysis Services existentes, puede migrar a Azure Analysis Services sin realizar cambios significativos. Para la migración, puede usar SSDT para implementar el modelo en el servidor. O bien, en SSMS, puede usar un proceso de copia de seguridad y restauración o TMSL.

Si tiene orígenes de datos locales, deberá instalar y configurar una [puerta de enlace de datos local](analysis-services-gateway.md). Si tiene roles y miembros de rol ya configurados, estos migrarán, pero tendrá que volver a agregar los miembros del rol con SSMS o PowerShell.

## <a name="connect-to-popular-data-sources"></a>Conexión a orígenes de datos conocidos
Azure Analysis Services admite la [conexión a orígenes de datos](analysis-services-datasource.md) locales de su organización y a orígenes de datos en la nube. Combine datos de orígenes de datos tanto locales como en la nube para crear una solución híbrida. 

Los nuevos modelos tabulares 1400 usan la moderna característica Obtención de datos en SSDT, basada en el lenguaje de consulta de fórmulas M. Con Obtención de datos, dispondrá de más características de transformación de datos y mashup, y la posibilidad de crear y editar sus propias consultas avanzadas con el lenguaje de fórmulas M. Por ejemplo, con los modelos tabulares 1400, puede modelar en archivos de datos de Azure Blob Storage.

## <a name="use-the-tools-you-already-know"></a>Use las herramientas que ya conoce

![Herramientas para desarrolladores de BI](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>SQL Server Data Tools (SSDT) para Visual Studio
Desarrolle e implemente modelos con [SQL Server Data Tools (SSDT) para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) de forma gratuita. SSDT incluye plantillas de proyecto de Analysis Services que le permiten ponerse rápidamente a pleno funcionamiento. SSDT ya incluye la moderna funcionalidad de consultas de orígenes de datos y de mashup denominada Obtención de datos para los modelos tabulares 1400. Si está familiarizado con esta funcionalidad en Power BI Desktop y Excel 2016, ya sabe lo fácil que es crear consultas de orígenes de datos altamente personalizadas.

#### <a name="sql-server-management-studio"></a>SQL Server Management Studio
Administrar los servidores y las bases de datos modelo mediante el uso de [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Conéctese a los servidores en la nube. Ejecute scripts de TMSL directamente desde la ventana de consultas XMLA y automatice las tareas mediante los scripts de TMSL. Surgen nuevas características y funcionalidades con gran rapidez: SSMS se actualiza mensualmente.

#### <a name="powershell"></a>PowerShell
Las tareas de administración de los recursos del servidor como crear servidores, suspender o reanudar las operaciones del servidor o cambiar el nivel de servicio, usan cmdlets de Azure Resource Manager (AzureRM). Las demás tareas de administración de bases de datos, como agregar o quitar miembros de rol, procesar o ejecutar scripts de TMSL, usan los cmdlets del módulo SqlServer. Los módulos AzureRM y SQLServer están disponibles en la [galería de PowerShell](https://www.powershellgallery.com/).


## <a name="your-data-is-secure"></a>Los datos están seguros
![Visualizaciones de datos](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>Autenticación
La autenticación de usuarios para Azure Analysis Services se realiza mediante [Azure Active Directory (AD)](../active-directory/active-directory-whatis.md). Al intentar iniciar sesión en una base de datos de Azure Analysis Services, los usuarios utilizan una identidad de cuenta de la organización con acceso a la base de datos a la que se están intentando acceder. Estas identidades de usuario deben ser miembros del directorio predeterminado de Azure Active Directory para la suscripción donde se encuentra el servidor de Azure Analysis Services. Para más información, consulte [Permisos de usuario y autenticación](analysis-services-manage-users.md).

#### <a name="data-security"></a>Seguridad de los datos
Azure Analysis Services usa Azure Blob Storage para conservar el almacenamiento y los metadatos de las bases de datos de Analysis Services. Los archivos de datos en Blob se cifran mediante cifrado de lado servidor (SSE) de Azure Blob. Cuando se usa el modo de consulta directa, se almacenan solo metadatos. Se accede a los datos en sí desde el origen de datos en el momento de la consulta.

#### <a name="firewall"></a>Firewall

El Firewall de Azure Analysis Services bloquea todas las conexiones de cliente a excepción de las especificadas en las reglas. Configure reglas mediante la especificación de las direcciones IP permitidas por direcciones IP de cliente individuales o por intervalo. También se pueden permitir las conexiones de Power BI (servicio) o bloquear. 

#### <a name="on-premises-data-sources"></a>Orígenes de datos locales
Proteja el acceso a los datos que residen localmente en su organización mediante la instalación y configuración de una [puerta de enlace de datos local](analysis-services-gateway.md). Las puertas de enlace de proporcionan acceso a datos tanto para el modo DirectQuery como para el modo en memoria. Cuando un modelo Azure Analysis Services se conecta a un origen de datos local, se crea una consulta junto con las credenciales cifradas para el origen de datos local. El servicio en la nube de puerta de enlace analiza la consulta e inserta la solicitud en una instancia de Azure Service Bus. La puerta de enlace local sondea Azure Service Bus en busca de solicitudes pendientes. La puerta de enlace obtiene la consulta, descifra las credenciales y se conecta a los orígenes de datos para la ejecución. Los resultados se devuelven desde el origen de datos a la puerta de enlace, y luego se envían a la base de datos Azure Analysis Services.

Azure Analysis Services se rige por los [términos de Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) y la [declaración de privacidad de Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Para más información sobre la seguridad de Azure, consulte [Microsoft Trust Center](https://www.microsoft.com/trustcenter/Security/AzureSecurity).



## <a name="supports-the-latest-client-tools"></a>Compatibilidad con las herramientas de cliente más recientes
![Visualizaciones de datos](./media/analysis-services-overview/aas-overview-clients.png)

Las herramientas modernas de exploración y visualización de datos como Power BI, Excel, SQL Server 2017 Reporting Services y otras de terceros, proporcionan a los usuarios un punto de vista interactivo y enriquecido visualmente sobre los datos del modelo. 

Los clientes usan las [bibliotecas de cliente](analysis-services-data-providers.md) de MSOLAP, AMO o ADOMD para conectarse a los servidores de Analysis Services. Las aplicaciones cliente de Microsoft, como Power BI Desktop y Excel, instalan las tres bibliotecas de cliente. Sin embargo, tenga en cuenta que, dependiendo de la versión o la frecuencia de las actualizaciones, las bibliotecas de cliente pueden no ser las versiones más recientes que necesita Azure Analysis Services. Esto mismo se aplica a aplicaciones personalizadas u otras interfaces, como AsCmd, TOM, ADOMD.NET. Estas aplicaciones normalmente requieren la instalación manual de las bibliotecas como parte de un paquete.


## <a name="get-help"></a>Obtención de ayuda

#### <a name="documentation"></a>Documentación
Azure Analysis Services es fácil de configurar y administrar. Puede encontrar toda la información que necesita para crear y administrar los servicios del servidor aquí. Crear un modelo de datos para implementar en el servidor es similar a crear un modelo de datos que se implementa en un servidor local. Hay una amplia biblioteca de artículos de referencia, sobre conceptos, procedimientos y tutoriales en la [ayuda de SQL Server Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services).

#### <a name="videos"></a>Vídeos
Consulte algunos vídeos útiles en [Azure Analysis Services en Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

#### <a name="blogs"></a>Blogs
Las cosas cambian rápidamente. Siempre puede obtener la información más reciente en el [blog del equipo de Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/) y el [blog de Azure](https://azure.microsoft.com/blog/).

#### <a name="community"></a>Comunidad
Analysis Services cuenta con una dinámica comunidad de usuarios. Únase a la conversación en el [foro de Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Comentarios
¿Tiene sugerencias o solicitudes de características? No olvide dejar sus comentarios en la página de [comentarios de Azure Analysis Services](https://aka.ms/azureanalysisservicesfeedback).

¿Tiene sugerencias sobre la documentación? Puede agregar comentarios mediante Livefyre al final de cada artículo.

## <a name="next-steps"></a>pasos siguientes
Ahora que sabe más acerca de Azure Analysis Services, es momento de empezar a trabajar. Aprenda a [crear un servidor](analysis-services-create-server.md) en Azure. Cuando el servidor esté listo, vaya al [tutorial de Adventure Works](tutorials/aas-adventure-works-tutorial.md) para aprender a crear un modelo tabular totalmente funcional y a implementarlo en el servidor.
