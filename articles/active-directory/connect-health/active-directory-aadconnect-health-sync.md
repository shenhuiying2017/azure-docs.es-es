---
title: "Uso de Azure AD Connect Health con sincronización | Microsoft Docs"
description: "Esta es la página de Azure AD Connect Health donde se describe cómo supervisar la sincronización de Azure AD Connect."
services: active-directory
documentationcenter: 
author: karavar
manager: femila
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b06338cb62cc458e7b097db36023f0746d4e969
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>Supervisión de Azure AD Connect sync con Azure AD Connect Health
La siguiente documentación es específica de la supervisión de sincronización de Azure AD Connect (Sync) con Azure AD Connect Health.  Para obtener información sobre la supervisión de AD FS con Azure AD Connect Health, consulte [Uso de Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md). Para obtener información adicional sobre la supervisión de los Servicios de dominio de Active Directory con Azure AD Connect Health, consulte [Using Azure AD Connect Health with AD DS](active-directory-aadconnect-health-adds.md)(Uso de Azure AD Connect Health con AD DS).

![Azure AD Connect Health para sincronización](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Alertas de Azure AD Connect Health para sincronización
La sección Alertas de Azure AD Connect Health para sincronización proporciona la lista de alertas activas. Cada alerta incluye información pertinente, pasos de resolución y vínculos a documentación relacionada. Al seleccionar una alerta activa o una alerta resulta, verá una hoja nueva con información adicional, así como los pasos que puede seguir para resolver la alerta y vínculos a documentación adicional. También puede ver datos históricos sobre las alertas resueltas en el pasado.

Al seleccionar una alerta, recibirá información adicional, así como los pasos que puede seguir para resolver la alerta y vínculos a documentación adicional.

![Error de sincronización de Azure AD Connect](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Evaluación limitada de alertas
Si Azure AD Connect no usa la configuración predeterminada (por ejemplo, si el filtrado de atributos se cambia de la configuración predeterminada a una configuración personalizada), el agente de Azure AD Connect Health no cargará los eventos de error relacionados con Azure AD Connect.

Esto limita la evaluación de las alertas por parte del servicio. Verá una pancarta que indica esta condición en el Portal de Azure en su servicio.

![Azure AD Connect Health para sincronización](./media/active-directory-aadconnect-health-sync/banner.png)

Para cambiar esto, haga clic en "Configuración" y permita que el agente de Azure AD Connect Health cargue todos los registros de errores.

![Azure AD Connect Health para sincronización](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>Recomendación de sincronización
Con frecuencia, los administradores desean conocer el tiempo que se tarda en sincronizar cambios en Azure AD y la cantidad de cambios que se están implantando. Esta característica proporciona una manera fácil de visualizar esta información mediante los gráficos siguientes:   

* Latencia de operaciones de sincronización
* Tendencia de cambio de objeto

### <a name="sync-latency"></a>Latencia de sincronización
Esta característica proporciona una tendencia gráfica de la latencia de las operaciones de sincronización (importación, exportación, etc.) para los conectores.  Esto proporciona una forma rápida y fácil de entender no solo la latencia de las operaciones (más grande si se produce un gran conjunto de cambios), sino también una manera de detectar anomalías en la latencia que pueden requerir más investigación.

![Latencia de sincronización](./media/active-directory-aadconnect-health-sync/synclatency02.png)

De manera predeterminada, solo se muestra la latencia de la operación de exportación del conector de Azure AD.  Para ver más operaciones del conector o para ver las operaciones de otros conectores, haga clic con el botón derecho en el gráfico, seleccione Editar gráfico o haga clic en el botón "Editar gráfico de latencia" y elija la operación específica y los conectores.

### <a name="sync-object-changes"></a>Cambios en el objeto de sincronización
Esta característica proporciona una tendencia gráfica del número de cambios que se evalúan y exportan a Azure AD.  Hoy en día, es difícil intentar recopilar esta información de los registros de sincronización.  El gráfico le proporciona, no solo una manera más sencilla de supervisar el número de cambios que se producen en su entorno, sino también una vista de los errores que ocurren.

![Latencia de sincronización](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>Informe de errores de sincronización de nivel de objeto (vista previa)
Esta característica proporciona un informe sobre los errores de sincronización que pueden producirse cuando se sincronizan datos de identidad entre Windows Server AD y Azure AD con Azure AD Connect.

* El informe contiene los errores registrados por el cliente de sincronización (Azure AD Connect versión 1.1.281.0 o superior).
* Incluye los errores producidos en la última operación de sincronización en el motor de sincronización ("Exportación" en Azure AD Connector).
* El agente de Azure AD Connect Health para la sincronización debe tener conectividad saliente a los puntos de conexión necesarios para que el informe incluya los datos más recientes.
* El informe se **actualiza cada 30 minutos** con los datos cargados por el agente de Azure AD Connect Health para la sincronización. Ofrece las siguientes funcionalidades principales:

  * Categorización de errores
  * Lista de objetos con error por categoría
  * Todos los datos sobre los errores en un solo lugar
  * Comparación en paralelo de objetos con error debido a un conflicto
  * Descarga del informe de errores como archivo CVS (próximamente)

### <a name="categorization-of-errors"></a>Categorización de errores
El informe clasifica los errores de sincronización existentes en las siguientes categorías:

| Categoría | Descripción |
| --- | --- |
| Atributo duplicado |Errores que se producen cuando Azure AD Connect intenta crear o actualizar objetos con valores duplicados de uno o más atributos en Azure AD que deben ser únicos en un inquilino como, por ejemplo, proxyAddresses o UserPrincipalName. |
| Error de coincidencia de datos |Errores que se producen cuando la coincidencia parcial no coincide con objetos, lo que da lugar a errores de sincronización. |
| Error de validación de datos |Errores debidos a datos no válidos, como caracteres no admitidos en atributos críticos como UserPrincipalName o errores de formato que no superan la validación antes de escribirse en Azure AD. |
| Atributo grande |Errores que se producen cuando el recuento, el tamaño o la longitud de uno o varios atributos es superior al máximo permitido. |
| Otros |Todos los demás errores que no se ajustan a las categorías anteriores. A raíz de los comentarios recibidos, esta categoría se dividirá en varias subcategorías. |

![Resumen del informe de errores de sincronización](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![Categorías del informe de errores de sincronización](./media/active-directory-aadconnect-health-sync/errorreport02.png)

### <a name="list-of-objects-with-error-per-category"></a>Lista de objetos con error por categoría
Al desglosar cada categoría, se mostrará la lista de objetos que presentan errores en esa categoría.
![Lista de informe de errores de sincronización](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>Detalles del error
Los siguientes datos están disponibles en la vista detallada de cada error.

* Identificadores para el *objeto de AD* implicado
* Identificadores para el *objeto de Azure AD* implicado (si procede)
* Descripción y resolución del error
* Artículos relacionados

![Detalles del informe de errores de sincronización](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>Descarga del informe de errores como archivo CVS
Si selecciona el botón "Exportar" puede descargar un archivo CSV con todos los detalles acerca de todos los errores.

## <a name="related-links"></a>Vínculos relacionados
* [Solución de problemas de errores durante la sincronización](../connect/active-directory-aadconnect-troubleshoot-sync-errors.md)
* [Resistencia de atributos duplicados](../connect/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalación del agente de Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operaciones de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso de Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso de Azure AD Connect Health con AD DS](active-directory-aadconnect-health-adds.md)
* [Preguntas más frecuentes de Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Historial de versiones de Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)