---
title: "Proyecto de análisis de datos médicos en Azure"
description: "Guía para implementar un proyecto de análisis de datos médicos HIPAA/HITRUST"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: simorjay
ms.openlocfilehash: 41b36a25eab9c1564dc59a40acd7daefde888b7f
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Proyecto de seguridad y cumplimiento de Azure: IA y datos médicos HIPAA/HITRUST

## <a name="overview"></a>Información general

**El proyecto de seguridad y cumplimiento de Azure: IA y datos médicos HIPAA/HITRUST ofrece una implementación llave en mano de una solución de PaaS de Azure para mostrar cómo recopilar, almacenar, analizar e interactuar de forma segura con los datos médicos y alcanzar los requisitos de cumplimiento del sector. El proyecto le ayuda a acelerar la adopción en la nube y el uso de los clientes con datos regulados.**

El proyecto de seguridad y cumplimiento de Azure: IA y datos médicos HIPAA/HITRUST proporciona herramientas y orientación para ayudar a implementar un entorno seguro de plataforma como servicio (PaaS) conforme a Health Insurance Portability and Accountability Act (HIPAA) y Health Information Trust Alliance (HITRUST) para introducir, almacenar, analizar e interactuar con los historiales médicos personales y de carácter no personal en un entorno seguro y niveles múltiples en la nube, implementado como una solución de un extremo a otro. Muestra una arquitectura de referencia común y está diseñado para simplificar la adopción de Microsoft Azure. La arquitectura proporcionada muestra una solución completa para satisfacer las necesidades de organizaciones que buscan un enfoque basado en la nube para reducir la carga y los costos de implementación.

![](images/components.png)

La solución está diseñada para consumir un conjunto de datos de ejemplo en formato Fast Healthcare Interoperability Resources (FHIR), un estándar internacional para el intercambio electrónico de información de datos médicos y su almacenamiento de forma segura. Los clientes pueden usar Azure Machine Learning para aprovechar sus eficaces herramientas de inteligencia empresarial y análisis para revisar las predicciones realizadas sobre los datos de ejemplo. Como ejemplo del tipo de experimento que Azure Machine Learning puede facilitar, el proyecto incluye un conjunto de datos de ejemplo, scripts y herramientas para predecir la duración de la permanencia del paciente en un centro hospitalario. 

Este proyecto está diseñado para servir como base modular para que los clientes lo ajusten a sus requisitos específicos y desarrollen nuevos experimentos de Azure Machine Learning para resolver escenarios de casos de uso tanto clínicos como operativos. Está diseñado para ser seguro y conforme una vez implementado; sin embargo, los clientes son responsables de la configuración correcta de los roles y de implementar cualquier modificación. Tenga en cuenta lo siguiente:

-   Este proyecto proporciona una base de referencia para que los clientes puedan usar Microsoft Azure en un entorno HITRUST y HIPAA.

-   Aunque el proyecto está diseñado para alinearse con HIPAA y HITRUST (a través de la estructura de seguridad común, CSF), no debería considerarse conforme hasta ser certificado por un auditor externo según los requisitos de certificación de HIPAA y HITRUST.

-   Los clientes son responsables de llevar a cabo las revisiones de seguridad y cumplimiento adecuadas de todas las soluciones creadas con esta arquitectura base.

Para obtener información general de cómo funciona esta solución, vea este [vídeo](https://aka.ms/healthblueprintvideo) en el que se explica y demuestra cómo realizar la implementación.

## <a name="deploying-the-automation"></a>Implementación de la automatización

- Para implementar la solución, siga las instrucciones proporcionadas en la guía de implementación. 

[![](./images/deploy.png)](https://aka.ms/healthblueprintdeploy)

- Las preguntas más frecuentes se pueden encontrar en la guía de [preguntas más frecuentes](https://aka.ms/healthblueprintfaq).


## <a name="solution-components"></a>Componentes de soluciones


La arquitectura fundamental consta de los siguientes componentes:

-   **Diagrama de arquitectura.** El diagrama muestra la arquitectura de referencia utilizada en el proyecto y el escenario del caso de uso de ejemplo.

-   **Plantillas de implementación**. En esta implementación, las [plantillas de Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) se usan para implementar automáticamente los componentes de la arquitectura en Microsoft Azure; para ello, se especifican los parámetros de configuración durante la instalación.

-   **[Scripts de implementación automatizados](https://aka.ms/healthblueprintdeploy)**. Estos scripts ayudan a implementar la solución. Asimismo, estos scripts constan de los siguientes elementos:


-   Una instalación de módulo y un script de configuración del [administrador global](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) se usan para instalar y comprobar que los roles de administrador global y los módulos de PowerShell necesarios están configurados correctamente. 
-   Se usa un script de instalación de PowerShell para implementar la solución, que se proporciona en un archivo .zip que contiene funciones de demostración integradas.


-   **[Modelo de amenazas](https://aka.ms/healththreatmodel)** se proporciona un modelo de amenazas completo en formato tm7 para su uso con [Microsoft Threat Modeling Tool](https://www.microsoft.com/en-us/download/details.aspx?id=49168) que muestra los componentes de la solución, los datos que fluyen entre ellos y los límites de confianza. El modelo puede ayudar a los clientes a comprender los puntos de riesgo potencial de la infraestructura del sistema al desarrollar componentes de aprendizaje automático u otras modificaciones.

-   **[Matriz de responsabilidad del cliente](https://aka.ms/healthcrmblueprint)** un libro de Microsoft Excel enumera los requisitos de HITRUST pertinentes y explica cómo Microsoft y el cliente son responsables del cumplimiento de cada uno de ellos.

-   **[Revisión de datos médicos. La solución ha sido revisada por Coalfire Systems, Inc. El documento de revisión del cumplimiento en datos médicos (HIPAA y HITRUST) y las instrucciones para la implementación proporcionan la opinión del auditor \' sobre la solución y sus consideraciones para transformar el proyecto en una implementación lista para producción.

# <a name="architectural-diagram"></a>Diagrama de arquitectura


![](images/refarch.png)

## <a name="roles"></a>Roles


El proyecto define dos roles para los usuarios administrativos (operadores) y tres roles para los usuarios de administración del hospital y atención al paciente. Se define un sexto rol para que un auditor pueda evaluar el cumplimiento con HIPAA y otras normativas. El control de acceso basado en rol (RBAC) de Azure permite la administración de acceso con precisión para cada usuario de la solución a través de roles integrados y personalizados. Consulte [Introducción al control de acceso basado en rol en Azure Portal](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) y [Roles integrados para el control de acceso basado en rol de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) para obtener información detallada acerca de RBAC, los roles y los permisos.

### <a name="site-administrator"></a>Administrador del sitio


El administrador del sitio es responsable de la suscripción de Azure del cliente. Controla la implementación completa, pero no tiene acceso a los historiales de los pacientes.

-   Asignaciones de roles predeterminadas: [Propietario](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#owner)

-   Asignaciones de roles personalizados: N/D

-   Ámbito: suscripción

### <a name="database-analyst"></a>Analista de base de datos

El analista de base de datos administra la instancia de SQL Server y la base de datos.
No tiene acceso a los historiales de los pacientes.

-   Asignaciones de roles integrados: [Colaborador de base de datos SQL](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#sql-db-contributor), [Colaborador de SQL Server](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#sql-server-contributor)

-   Asignaciones de roles personalizados: N/D

-   Ámbito: grupo de recursos

 ### <a name="data-scientist"></a>Científico de datos


Los científicos de datos operan el servicio Azure Machine Learning. Pueden importar, exportar y administrar los datos y ejecutar informes. Los científicos de datos tienen acceso a los datos del paciente, pero no tienen privilegios administrativos.

-   Asignaciones de roles integrados: [Colaborador de la cuenta de almacenamiento](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#storage-account-contributor)

-   Asignaciones de roles personalizados: N/D

-   Ámbito: grupo de recursos

### <a name="chief-medical-information-officer-cmio"></a>Director de información médica (CMIO)


El director de información médica (CMIO) se encuentra en la línea divisoria entre los profesionales informáticos o técnicos y los profesionales sanitarios en un centro de atención médica. Sus tareas suelen incluir el uso de análisis para determinar si se están asignando correctamente los recursos en la organización.

-   Asignaciones de roles integrados: ninguno

### <a name="care-line-manager"></a>Administrador de la línea de atención al paciente


El administrador de la línea de atención al paciente está directamente implicado en la atención a los pacientes.
Este rol requiere la supervisión del estado de cada paciente y asegurarse de que hay personal disponible para atender las necesidades de atención específicas de los pacientes. El administrador de la línea de atención al paciente es responsable de agregar y actualizar los historiales de los pacientes.

-   Asignaciones de roles integrados: ninguno

-   Asignaciones de roles personalizados: tiene privilegios para ejecutar HealthcareDemo.ps1 para realizar tanto la admisión del paciente como el alta.

-   Ámbito: grupo de recursos

### <a name="auditor"></a>Auditor


El auditor evalúa el cumplimiento de la solución. No tienen ningún acceso directo a la red.

-   Asignaciones de roles integrados: [Lector](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#reader)

-   Asignaciones de roles personalizados: N/D

-   Ámbito: suscripción

## <a name="example-use-case"></a>Ejemplo de caso de uso


El caso de uso de ejemplo incluido con este proyecto muestra cómo se puede utilizar para habilitar el aprendizaje automático y el análisis de datos médicos en la nube. Contosoclinic es un hospital pequeño que se encuentra en Estados Unidos. Los administradores de red del hospital desean usar Azure Machine Learning para predecir mejor la duración de la estancia del paciente en el momento de la admisión, con el fin de aumentar la eficiencia operativa de la carga de trabajo y mejorar la calidad de la atención que esto puede proporcionar.

### <a name="predicting-length-of-stay"></a>Predicción de la duración de la estancia


El escenario del caso de uso de ejemplo utiliza Azure Machine Learning para predecir la duración de la estancia del paciente recién admitido comparando los detalles médicos tomados en la admisión de pacientes con datos históricos agregados de pacientes anteriores.
El proyecto incluye un conjunto grande de historiales médicos anonimizados para demostrar las funcionalidades de predicción y de entrenamiento de la solución. En una implementación de producción, los clientes utilizarían sus propios historiales para entrenar la solución con el fin de obtener predicciones más precisas que reflejen los detalles únicos del entorno, las instalaciones y los pacientes.

### <a name="users-and-roles"></a>Usuarios y roles


**Administrador del sitio: Alex**

*Correo electrónico: Alex\_SiteAdmin*

El trabajo de Alex consiste en evaluar las tecnologías que puedan reducir la carga de la administración de una red local y reducir los costos de administración. Alex ha estado evaluando Azure durante algún tiempo, pero se encontró con problemas al configurar los servicios que necesita para cumplir los requisitos de cumplimiento de las normas HiTrust para almacenar datos de pacientes en la nube. Alex ha seleccionado la tecnología de inteligencia artificial sobre datos médicos de Azure para implementar una solución de datos médicos preparada en materia de cumplimiento y que proporciona los requisitos necesarios para el cumplimiento de clientes en HiTrust.

**Científica de datos: Debra**

*Correo electrónico: Debra\_DataScientist*

Debra está a cargo del uso y la creación de modelos que analizan los historiales médicos para brindar información detallada sobre la atención al paciente. Debra utiliza SQL y el lenguaje de programación estadístico R para crear sus modelos.

**Analista de base de datos: Danny**

*Correo electrónico: Danny\_DBAnalyst*

Danny es el contacto principal para cualquier asunto relacionado con Microsoft SQL Server, que almacena todos los datos de los pacientes de Contosoclinic. Danny es un administrador experimentado de SQL Server que recientemente se ha familiarizado con Azure SQL Database.

**Directora de información médica: Caroline**

Caroline trabaja con Chris, el administrador de la línea de atención al paciente y con Debra, la científica de datos, para determinar qué factores afectan a la duración de la estancia de los pacientes.
Caroline usa las predicciones de la solución de duración de estancia (LOS) para determinar si se están asignando correctamente los recursos en la red del hospital. Por ejemplo, mediante el panel que proporciona esta solución.

**Administrador de la línea de atención al paciente: Chris**

*Correo electrónico: Chris\_CareLineManager*

Como la persona directamente responsable de administrar la admisión y alta de pacientes en Contosoclinic, Chris usa las predicciones generadas por la solución de duración de estancia para asegurarse de que está disponible el personal adecuado para proporcionar la atención necesaria a los pacientes durante su estancia en las instalaciones.

**Auditor: Han**

*Correo electrónico: Han\_Auditor*

Han es un auditor certificado con experiencia en auditorías ISO, SOC y HiTrust. Han ha sido contratado para revisar la red de Contosoclinic. Han puede revisar la matriz de responsabilidades del cliente proporcionada por la solución para asegurarse de que el proyecto y la solución de duración de estancia se pueden utilizar para almacenar, procesar y mostrar datos personales confidenciales.


# <a name="design-configuration"></a>Configuración de diseño


En esta sección se describen las configuraciones predeterminadas y las medidas de seguridad integradas en el proyecto, que se desglosan en:

- **INGESTA** de orígenes de datos sin formato incluido el origen de datos FHIR
- **ALMACENAMIENTO** de información confidencial
- Salidas de **ANÁLISIS** y predicciones
- **INTERACCIÓN** con los resultados y las predicciones
- Administración de la **IDENTIDAD** en la solución
- Características habilitadas para la **SEGURIDAD**


## <a name="identity"></a>IDENTIDAD 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Azure Active Directory y control de acceso basado en rol (RBAC)


**Autenticación:**

-   [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) es el servicio de directorio y de administración de identidades multiinquilino basado en la nube de Microsoft\'. Todos los usuarios de la solución se crearon en Azure Active Directory, incluidos los usuarios que acceden a SQL Database.



-   La autenticación para acceder a la aplicación se realiza con Azure AD. Para obtener más información, consulte [Integración de aplicaciones con Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

-   [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) detecta posibles puntos vulnerables que afectan a las identidades de la organización, configura respuestas automatizadas si surgen acciones sospechosas relacionadas con esas identidades, investiga incidentes sospechosos y toma las medidas oportunas para resolverlos.

-   El [control de acceso basado en rol de Azure (RBAC)](/azure/active-directory/role-based-access-control-configure) permite administrar al detalle el control de acceso de Azure. El acceso a la suscripción está limitado al administrador de dicha suscripción y el acceso a Azure Key Vault está limitado al administrador del sitio. Se requieren contraseñas seguras (12 caracteres como mínimo con al menos una letra en mayúsculas y minúsculas, un número y un carácter especial).

-   Se admite la autenticación multifactor cuando el modificador -enableMFA está habilitado durante la implementación.

-   Las contraseñas expiran transcurridos 60 días cuando el modificador -enableADDomainPasswordPolicy está habilitado durante la implementación.

**Roles:**

-   La solución hace uso de los [roles integrados](/azure/active-directory/role-based-access-built-in-roles) para administrar el acceso a los recursos.

-   Se asignan roles integrados específicos de forma predeterminada a todos los usuarios.

### <a name="azure-key-vault"></a>Azure Key Vault

-   Los datos almacenados en el almacén de claves incluyen:

    -   Clave de Application Insights
    -   Clave de acceso del almacenamiento de los datos de los pacientes
    -   Cadena de conexión de pacientes
    -   Nombre de la tabla de datos de los pacientes
    -   Punto de conexión de servicio Web de Azure ML
    -   Clave de la API del servicio Azure ML

-   Se configuran directivas de acceso avanzadas según las necesidades
-   Se definen directivas de acceso a Key Vault con los permisos mínimos requeridos para las claves y los secretos
-   Todas las claves y los secretos en Key Vault tienen fechas de expiración
-   Todas las claves en Key Vault están protegidas con HSM \[Tipo de clave = clave RSA de 2048 bits protegida con HSM\]
-   Se otorgan los permisos necesarios mínimos a todos los usuarios e identidades mediante el control de acceso basado en rol (RBAC)
-   Las aplicaciones no comparten un almacén de claves a menos que confíen entre sí y que necesiten tener acceso a los mismos secretos en tiempo de ejecución
-   Los registros de diagnóstico de Key Vault están habilitados con un período de retención de al menos 365 días.
-   Las operaciones criptográficas permitidas para las claves están restringidas únicamente a las requeridas

## <a name="ingest"></a>INGESTA 

### <a name="azure-functions"></a>Azure Functions
La solución ha sido diseñada para el uso de [Azure Functions](/azure/azure-functions/) para procesar la datos de duración de estancia de ejemplo que se usan en la demostración de análisis. Se han creado tres funcionalidades en las funciones.

**1. Importación masiva de los datos de información sanitaria protegida del cliente**

Cuando se utiliza el script de demostración. .\\HealthcareDemo.ps1 con el modificador **BulkPatientAdmission** tal como se describe en **Implementación y ejecución de la demostración** se ejecuta la canalización de procesamiento siguiente:
1. **Azure Blob Storage**: Se carga en el almacenamiento el archivo .csv de ejemplo con los datos de los pacientes
2. **Event Grid**: un evento publica los datos en Azure Functions (Importación masiva: evento de blob)
3. **Azure Functions**: realiza el procesamiento y almacena los datos en el almacenamiento SQL utilizando la función segura: event(tipo; URL del blob)
4. **Base de datos SQL**: el almacén de base de datos de pacientes utiliza etiquetas para la clasificación y el proceso de aprendizaje automático se inicia para entrenar el experimento.

![](images/dataflow.png)

Además, la función de Azure se diseñó para leer y proteger los datos confidenciales designados en el conjunto de datos de ejemplo con las siguientes etiquetas:
- dataProfile => "ePHI"
- owner = > \<UPN de administración del sitio\>
- environment => "Pilot"
- department = > "Global Ecosystem" el etiquetado se aplicó al conjunto de datos de ejemplo donde los nombres de los pacientes se identifican como texto sin cifrar.

**2. Admisión de nuevos pacientes**

Cuando se utiliza el script de demostración. .\\HealthcareDemo.ps1 con el modificador **BulkPatientadmission** tal como se describe en **Implementación y ejecución de la demostración** se ejecuta la canalización de procesamiento siguiente: ![](images/securetransact.png)
**1. Azure Functions** se desencadena y la función solicita un [token al portador](/rest/api/) de Azure Active Directory.

**2. Key Vault** Se solicita un secreto que está asociado al token solicitado.

**3. Los roles de Azure validan la solicitud y autorizan la solicitud de acceso a Key Vault.

**4. Key Vault** devuelve el secreto, en este caso, la cadena de conexión de la base de datos SQL.

**5. Azure Functions** usa la cadena de conexión para conectarse de forma segura a SQL Database y continúa el procesamiento adicional para almacenar los datos ePHI.

Para lograr el almacenamiento de los datos, se implementa un esquema de API común según la norma Fast Healthcare Interoperability Resources (FHIR). Se proporcionan a la función los siguientes elementos de intercambio FHIR:

-   [Esquema de pacientes](https://www.hl7.org/fhir/patient.html) incluye la información de "quién" es un paciente.

-   [Esquema de observación](https://www.hl7.org/fhir/observation.html) incluye el elemento central en la atención médica, se usa para admitir el diagnóstico, supervisar el progreso, determinar las líneas de base y los patrones e incluso para capturar características demográficas. 

-   [Esquema de encuentros](https://www.hl7.org/fhir/encounter.html) incluye los tipos de encuentro; por ejemplo ambulatorio, emergencia, atención domiciliaria, paciente interno y encuentro virtual.

-   [Esquema de condiciones](https://www.hl7.org/fhir/condition.html) incluye información detallada sobre una condición, problema, diagnóstico o cualquier otro evento, situación, problema o concepto clínico que ha llegado a un nivel de atención.  



### <a name="event-grid"></a>Event Grid


La solución es compatible con Azure Event Grid, un único servicio para administrar el enrutamiento de todos los eventos desde cualquier origen hasta cualquier destino que proporciona:

-   [Seguridad y autenticación](/azure/event-grid/security-authentication)

-   [Control de acceso basado en rol](/azure/event-grid/security-authentication#management-access-control) para realizar diversas operaciones de administración como enumerar suscripciones a eventos, crear nuevos y generar claves

-   Auditoría

## <a name="store"></a>ALMACENAMIENTO 

### <a name="sql-database-and-server"></a>SQL Database y SQL Server 


-   [Cifrado de datos transparente (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) proporciona cifrado y descifrado en tiempo real de los datos almacenados en Azure SQL Database, mediante una clave almacenada en Azure Key Vault.

-   [Evaluación de puntos vulnerables de SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) es una herramienta de sencilla configuración que puede detectar, realizar un seguimiento y corregir posibles puntos vulnerables en la base de datos.

-   [Detección de amenazas de SQL Database](/azure/sql-database/sql-database-threat-detection) habilitada.

-   [Auditoría de SQL Database](/azure/sql-database/sql-database-auditing) habilitada.

-   [Métricas y registros de diagnóstico de SQL Database](/azure/sql-database/sql-database-metrics-diag-logging) habilitados.

-   [Las reglas de firewall de nivel de servidor y base de datos](/azure/sql-database/sql-database-firewall-configure) se han mejorado.

-   [Columnas siempre cifradas](/azure/sql-database/sql-database-always-encrypted-azure-key-vault) para proteger los nombres y apellidos de los pacientes.
    Además, el cifrado de columnas de la base de datos también usa Azure Active Directory (AD) para autenticar la aplicación en Azure SQL Database.

-   El acceso a SQL Database y SQL Server está configurado según el principio de privilegios mínimos.

-   Solo se permite el acceso a través del firewall de SQL a las direcciones IP necesarias.

### <a name="storage-accounts"></a>Cuentas de almacenamiento


-   [Los datos en movimiento se transfieren utilizando únicamente TLS/SSL](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

-   No se permite el acceso anónimo a los contenedores.

-   Se configuran reglas de alertas para el seguimiento de actividades anónimas.

-   Se requiere HTTPS para tener acceso a los recursos de la cuenta de almacenamiento.

-   Los datos de la solicitud de autenticación se registran y supervisan.

-   Los datos de almacenamiento de blobs se cifran en reposo.

## <a name="analyze"></a>ANÁLISIS

### <a name="machine-learning"></a>Machine Learning


-   [El registro está habilitado](/azure/machine-learning/studio/web-services-logging) para los servicios web de Machine Learning.
- El uso de [Machine Learning](/azure/machine-learning/preview/experimentation-service-configuration) Workbench requiere el desarrollo de experimentos, que proporciona la capacidad de predecir un conjunto de soluciones. [La integración con Workbench](/azure/machine-learning/preview/using-git-ml-project) puede ayudar a simplificar la administración de experimentos.

## <a name="security"></a>SEGURIDAD

### <a name="azure-security-center"></a>Azure Security Center
- [Azure Security Center](https://azure.microsoft.com/services/security-center/) proporciona una visión centralizada del estado de la seguridad de todos los recursos de Azure. Podrá comprobar de un solo vistazo si los controles de seguridad adecuados se han implementado y configurado correctamente, y podrá identificar rápidamente cualquier recurso que requiera su atención. 

- [Azure Advisor](/azure/advisor/advisor-overview) es un consultor en la nube personalizado que le ayudará a seguir los procedimientos recomendados para optimizar las implementaciones de Azure. Analiza la configuración de recursos y la telemetría de uso, y recomienda soluciones que pueden ayudar a mejoran la rentabilidad, el rendimiento, la alta disponibilidad y la seguridad de los recursos de Azure.

### <a name="application-insights"></a>Application Insights
- [Application Insights](/azure/application-insights/app-insights-overview) es un servicio de Application Performance Management (APM) extensible para desarrolladores web en varias plataformas. Úselo para supervisar la aplicación web en directo. Detecta las anomalías de rendimiento. Incluye herramientas de análisis eficaces que le ayudan a diagnosticar problemas y comprender lo que hacen realmente los usuarios con la aplicación. Está diseñado para ayudarle a mejorar continuamente el rendimiento y la facilidad de uso.

### <a name="azure-alerts"></a>Alertas de Azure
- Las alertas ofrecen un método de supervisar los servicios de Azure y le permiten configurar condiciones en los datos. Las alertas también proporcionan notificaciones cuando una condición de alerta coincide con los datos supervisados.

### <a name="operations-management-suite-oms"></a>Operations Management Suite (OMS)
[Operations Management Suite (también conocido como OMS)](/azure/operations-management-suite/operations-management-suite-overview) es una colección de servicios de administración.

-   El área de trabajo está habilitada para Security Center

-   El área de trabajo está habilitada para la supervisión de cargas de trabajo

-   La supervisión de cargas de trabajo está habilitada para:

    -   Identidad y acceso

    -   Seguridad y auditoría

    -   Azure SQL DB Analytics

    -   La solución [Azure WebApp Analytics](/azure/log-analytics/log-analytics-azure-web-apps-analytics)

    -   Análisis de Key Vault

    -   Seguimiento de cambios

-   [Application Insights Connector (versión preliminar)](/azure/log-analytics/log-analytics-app-insights-connector) está habilitado

-   El [Análisis del registro de actividad](/azure/log-analytics/log-analytics-activity) está habilitado