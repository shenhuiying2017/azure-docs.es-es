---
title: "Guía para el ejemplo de aplicación multiinquilino de SQL Database: SaaS de Wingtip | Microsoft Docs"
description: "Proporciona pasos e instrucciones para instalar y ejecutar la aplicación multiinquilino de ejemplo que utiliza Azure SQL Database, en el ejemplo de SaaS de Wingtip."
keywords: tutorial de SQL Database
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: genemi
ms.openlocfilehash: fbfaea938676991cf6280e5dd8c1e1190aa268a8
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="guidance-and-tips-for-azure-sql-database-multi-tenant-saas-app-example"></a>Guía y sugerencias para el ejemplo de una aplicación SaaS multiinquilino de Azure SQL Database


## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Descargar y desbloquear los scripts SaaS de Wingtip

Es posible que Windows bloquee el contenido ejecutable (scripts, archivos DLL) cuando se descarguen y extraigan archivos ZIP desde un origen externo. Al extraer los scripts de un archivo ZIP, ***siga los pasos siguientes para desbloquear el archivo .zip antes de extraerlo***. Esto garantiza que se puedan ejecutar los scripts.

1. Vaya al [repositorio de SaaS de Wingtip en GitHub](https://github.com/Microsoft/WingtipSaaS).
2. Haga clic en **Clone or download** (Clonar o descargar).
3. Haga clic en **Download ZIP** (Descargar archivos ZIP) y guarde el archivo.
4. Haga clic con el botón derecho en el archivo **WingtipSaaS-master.zip** y seleccione **Propiedades**.
5. En la pestaña **General**, seleccione **Desbloquear**.
6. Haga clic en **Aceptar**.
7. Extraiga los archivos.

Los scripts se encuentran en la carpeta *..\\WingtipSaaS-master\\Learning Modules*.


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>Trabajar con los scripts de PowerShell de SaaS Wingtip

Para sacar el máximo partido del ejemplo debe profundizar en los scripts proporcionados. Use puntos de interrupción y recorra los scripts, examinando los detalles de cómo se implementan los distintos patrones de SaaS. Para recorrer con facilidad los scripts proporcionados y los módulos para entenderlos mejor, se recomienda usar [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Actualizar el archivo de configuración para la implementación

Edite el archivo **UserConfig.psm1** con el valor de usuario y grupo de recursos que se estableció durante la implementación:

1. Abra *PowerShell ISE* y cargue ...\\Learning Modules\\*UserConfig.psm1* 
2. Actualice *ResourceGroupName* y *Name* con los valores específicos para la implementación (solo en las líneas 10 y 11).
3. Guarde los cambios.

Al establecer estos valores aquí simplemente evita tener que actualizar estos valores específicos de la implementación en todos los scripts.

### <a name="execute-scripts-by-pressing-f5"></a>Ejecución de scripts presionando F5

Varios scripts usan *$PSScriptRoot* para la navegación por las carpetas y *$PSScriptRoot* solo se evalúa cuando se ejecutan los scripts presionando **F5**.  Resaltar y ejecutar una selección (**F8**) puede dar lugar a errores, por tanto, presione **F5** al ejecutar los scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Siga paso a paso los scripts para examinar la implementación

La mejor manera de comprender los scripts es recorrerlos para ver lo que hacen. Consulte los scripts **Demo-** incluidos que presentan un flujo de trabajo general fácil de seguir. Los scripts **Demo-** muestran los pasos necesarios para realizar cada tarea, por tanto, establezca puntos de interrupción y profundice en las llamadas individuales para ver los detalles de implementación para los distintos patrones de SaaS.

Sugerencias para explorar y recorrer los scripts de PowerShell:

- Abra los scripts **Demo-** en PowerShell ISE.
- Ejecute o continúe con **F5** (no se recomienda el uso de **F8** porque *$PSScriptRoot* no se evalúa cuando se ejecutan las selecciones de un script).
- Para colocar puntos de interrupción, haga clic o seleccione una línea y presione **F9**.
- Salte una llamada de función o script con **F10**.
- Vaya a una llamada de función o script con **F11**.
- Salga de la llamada de función o script actual con **MAYÚS + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Exploración del esquema de base de datos y ejecución de consultas SQL con SSMS

Use [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para conectarse y examinar las bases de datos y los servidores de la aplicación.

La implementación inicialmente tiene dos servidores de SQL Database para conectarse a los servidores *tenants1-&lt;Usuario&gt;* y *catalog-&lt;Usuario&gt;*. Para garantizar una conexión de demostración correcta, ambos servidores tienen una [regla de firewall](sql-database-firewall-configure.md) que permite todas las direcciones IP.


1. Abra *SSMS* y conéctese al servidor *tenants1-&lt;Usuario&gt;.database.windows.net*.
2. Haga clic en **Conectar** > **Motor de base de datos...**:

   ![Servidor de catálogo](media/saas-dbpertenant-wingtip-app-guidance-tips/connect.png)

3. Las credenciales para la demo son: inicio de sesión = *developer*, contraseña =*P@ssword1*.

   ![connection](media/saas-dbpertenant-wingtip-app-guidance-tips/tenants1-connect.png)

4. Repita los pasos 2-3 y conéctese al servidor *catalog-&lt;Usuario&gt;.database.windows.net*.


Después de conectarse correctamente debería ver ambos servidores. La lista de bases de datos podría ser diferente, dependiendo de los inquilinos que haya aprovisionado.

![Explorador de objetos](media/saas-dbpertenant-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Pasos siguientes

[Implementar la aplicación SaaS de Wingtip](saas-dbpertenant-get-started-deploy.md)

