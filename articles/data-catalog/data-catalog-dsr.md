---
title: "Orígenes de datos compatibles en Azure Data Catalog | Microsoft Docs"
description: "En este artículo se enumeran las especificaciones de los orígenes de datos compatibles actualmente."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstevens
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: d6867c73bc6ea3c238cceef8a68466d451f3365c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="supported-data-sources-in-azure-data-catalog"></a>Orígenes de datos compatibles en Azure Data Catalog

Pueden publicarse metadatos mediante una API pública o una herramienta de registro con un solo clic, o mediante la especificación manual de información directamente en el portal web de Azure Data Catalog. En la siguiente tabla se resumen todos los orígenes que admite el catálogo actualmente y las funcionalidades de publicación de cada uno de ellos. También se enumeran las herramientas de datos externas que cada origen puede iniciar desde nuestra experiencia "abierta" del portal. La segunda tabla contiene una especificación más técnica de las propiedades de conexión de cada propiedad de la conexión del origen de datos.


## <a name="list-of-supported-data-sources"></a>Lista de orígenes de datos que se admiten

<table>
    <tr>
       <td><b>Objeto de origen de datos</b></td>
       <td><b>API</b></td>
       <td><b>Entrada manual</b></td>
       <td><b>Herramienta de registro</b></td>
       <td><b>Herramientas abiertas</b></td>
       <td><b>Notas</b></td>
    </tr>
    <tr>
      <td>Directorio de Azure Data Lake Store</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Archivo de Azure Data Lake Store</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Almacenamiento de blobs de Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Directorio de Azure Storage</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabla de Azure Storage</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>
    <tr>
      <td>Directorio de HDFS</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Archivo de HDFS</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabla de Hive</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de Hive</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabla de MySQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de MySQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabla de Oracle Database</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de Oracle Database</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Otro (recurso genérico)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabla de Azure SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Dimensión de SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>KPI de SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Medida de SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabla de SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Informe de SQL Server Reporting Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Browser</font></td>
      <td><font size=2>Solo servidores de modo nativo. No se admite el modo de SharePoint.</font></td>
    </tr>
    <tr>
      <td>Tabla de SQL Server</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de SQL Server</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabla de Teradata</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de Teradata</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de SAP HANA</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabla de DB2</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de DB2</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Archivo de sistema de archivos</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Directorio FTP</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Archivo FTP</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Informe HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Punto de conexión HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Archivo HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Conjunto de entidades de OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Función de OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabla de PostgreSQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de PostgreSQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de SAP HANA</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td> Objeto de Salesforce</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Lista de SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Colección de Azure Cosmos DB</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabla ODBC genérica</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de ODBC genérica</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabla de Cassandra</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publicar como un recurso ODBC genérico</font></td>
    </tr>
    <tr>
      <td>Vista de Cassandra</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publicar como un recurso ODBC genérico</font></td>
    </tr>
    <tr>
      <td>Tabla de Sybase</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Vista de Sybase</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabla de MongoDB</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publicar como un recurso ODBC genérico</font></td>
    </tr>
    <tr>
      <td>Vista de MongoDB</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publicar como un recurso ODBC genérico</font></td>
    </tr>
</table>

Si necesita compatibilidad con más fuentes, envíe una solicitud de característica al [foro de Azure Data Catalog](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


## <a name="data-source-reference-specification"></a>Especificación de referencia de origen de datos
> [!NOTE]
> La columna **Estructura de DSL** de la tabla siguiente solo enumera las propiedades de conexión del contenedor de propiedades "dirección" que usa Azure Data Catalog. Es decir, el contenedor de propiedades "dirección" puede contener otras propiedades de conexión del origen de datos que Azure Data Catalog conserva, pero no utiliza.

<table>
    <tr>
       <td><b>Tipo de origen</b></td>
       <td><b>Tipo de recurso</b></td>
       <td><b>Tipos de objeto</b></td>
       <td><b>Estructura de DSL<b></td>
    </tr>
    <tr>
      <td>Almacén de Azure Data Lake</td>
      <td>Contenedor</td>
      <td>Data Lake</td>
      <td>
        <font size=2> Protocolo: webhdfs <br>Autenticación: {básica, oauth} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Almacén de Azure Data Lake</td>
      <td>Tabla</td>
      <td>Directorio, archivo</td>
      <td>
        <font size=2> Protocolo: webhdfs <br>Autenticación: {básica, oauth} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de Azure</td>
      <td>Contenedor</td>
      <td>Contenedor</td>
      <td>
        <font size=2> Protocolo: azure-blobs <br>Autenticación: {azure-access-key} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; cuenta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; contenedor</font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de Azure</td>
      <td>Tabla</td>
      <td>Blob, directorio</td>
      <td>
        <font size=2> Protocolo: azure-blobs <br>Autenticación: {azure-access-key} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; cuenta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; contenedor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; nombre </font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de Azure</td>
      <td>Contenedor</td>
      <td>Contenedor</td>
      <td>
        <font size=2> Protocolo: azure-tables <br>Autenticación: {azure-access-key} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; cuenta</font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de Azure</td>
      <td>Tabla</td>
      <td>Tabla</td>
      <td>
        <font size=2> Protocolo: azure-tables <br>Autenticación: {azure-access-key} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; cuenta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; nombre </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Contenedor</td>
      <td>Clúster virtual</td>
      <td>
        <font size=2> Protocolo: cosmos <br>Autenticación: {básica, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Tabla</td>
      <td>Transmisión, conjunto de transmisiones, vista</td>
      <td>
        <font size=2> Protocolo: cosmos <br>Autenticación: {básica, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Contenedor</td>
      <td>Sitio</td>
      <td>
        <font size=2> Protocolo: http <br>Autenticación: {no, básica, windows, oauth} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Informe</td>
      <td>Informe, panel</td>
      <td>
        <font size=2> Protocolo: http <br>Autenticación: {no, básica, windows, oauth} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> Protocolo: db2 <br>Autenticación: {básica, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> Protocolo: db2 <br>Autenticación: {básica, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema </font>
      </td>
    </tr>
    <tr>
      <td>Sistema de archivos</td>
      <td>Tabla</td>
      <td>Archivo</td>
      <td>
        <font size=2> Protocolo: archivo <br>Autenticación: {no, básica, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ruta de acceso </font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Tabla</td>
      <td>Directorio, archivo</td>
      <td>
        <font size=2> Protocolo: ftp <br>Autenticación: {no, básica, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>sistema de archivos distribuido de Hadoop</td>
      <td>Contenedor</td>
      <td>Clúster</td>
      <td>
        <font size=2> Protocolo: webhdfs <br>Autenticación: {básica, oauth} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>sistema de archivos distribuido de Hadoop</td>
      <td>Tabla</td>
      <td>Directorio, archivo</td>
      <td>
        <font size=2> Protocolo: webhdfs <br>Autenticación: {básica, oauth} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> Protocolo: hive <br>Autenticación: {hdinsight, básica, nombre de usuario, no} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> Protocolo: hive <br>Autenticación: {hdinsight, básica, nombre de usuario, no} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>http</td>
      <td>Contenedor</td>
      <td>Sitio</td>
      <td>
        <font size=2> Protocolo: http <br>Autenticación: {no, básica, windows, oauth} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>http</td>
      <td>Informe</td>
      <td>Informe, panel</td>
      <td>
        <font size=2> Protocolo: http <br>Autenticación: {no, básica, windows, oauth} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>http</td>
      <td>Tabla</td>
      <td>Punto de conexión, archivo</td>
      <td>
        <font size=2> Protocolo: http <br>Autenticación: {no, básica, windows, oauth} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> Protocolo: mysql <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> Protocolo: mysql <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Contenedor</td>
      <td>Contenedor de entidades</td>
      <td>
        <font size=2> Protocolo: odata <br>Autenticación: {no, básica, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Tabla</td>
      <td>Conjunto de entidades, función</td>
      <td>
        <font size=2> Protocolo: odata <br>Autenticación: {no, básica, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; recurso </font>
      </td>
    </tr>
    <tr>
      <td>Base de datos de Oracle</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> Protocolo: oracle <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>Base de datos de Oracle</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> Protocolo: oracle <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> Protocolo: postgresql <br>Autenticación: {básica, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> Protocolo: postgresql <br>Autenticación: {básica, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Contenedor</td>
      <td>Sitio</td>
      <td>
        <font size=2> Protocolo: http <br>Autenticación: {no, básica, windows, oauth} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Informe</td>
      <td>Informe, panel</td>
      <td>
        <font size=2> Protocolo: http <br>Autenticación: {no, básica, windows, oauth} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Tabla</td>
      <td>Mashup de datos</td>
      <td>
        <font size=2>Protocolo: power-query <br>Autenticación: {oauth} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Tabla</td>
      <td>Objeto</td>
      <td>
        <font size=2> Protocolo: salesforce-com <br>Autenticación: {básica, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; class <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; itemName </font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Contenedor</td>
      <td>Server</td>
      <td>
        <font size=2> Protocolo: sap-hana-sql <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor </font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Tabla</td>
      <td>Ver</td>
      <td>
        <font size=2> Protocolo: sap-hana-sql <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Tabla</td>
      <td>Enumerar</td>
      <td>
        <font size=2> Protocolo: sharepoint-list <br>Autenticación: {básica, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de datos SQL</td>
      <td>Comando</td>
      <td>Procedimiento almacenado</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de datos SQL</td>
      <td>TableValuedFunction</td>
      <td>Función con valores de tabla</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de datos SQL</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de datos SQL</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Comando</td>
      <td>Procedimiento almacenado</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Función con valores de tabla</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> Protocolo: tds <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>Contenedor</td>
      <td>Modelo</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticación: {windows, básica, anónima, no} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticación: {windows, básica, anónima, no} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticación: {windows, básica, anónima, no} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services multidimensional</td>
      <td>Tabla</td>
      <td>Dimension Data</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticación: {windows, básica, anónima, no} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Dimension} </font>
      </td>
    </tr>
    <tr>
      <td>Tabular de SQL Server Analysis Services</td>
      <td>Contenedor</td>
      <td>Modelo</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticación: {windows, básica, anónima, no} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo </font>
      </td>
    </tr>
    <tr>
      <td>Tabular de SQL Server Analysis Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticación: {windows, básica, anónima, no} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>Tabular de SQL Server Analysis Services</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticación: {windows, básica, anónima, no} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>Tabular de SQL Server Analysis Services</td>
      <td>Tabla</td>
      <td>Tabla</td>
      <td>
        <font size=2> Protocolo: analysis-services <br>Autenticación: {windows, básica, anónima, no} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Table} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Contenedor</td>
      <td>Server</td>
      <td>
        <font size=2> Protocolo: reporting-services <br>Autenticación: {windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Informe</td>
      <td>Informe</td>
      <td>
        <font size=2> Protocolo: reporting-services <br>Autenticación: {windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ruta de acceso <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> Protocolo: teradata <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> Protocolo: teradata <br>Autenticación: {protocolo, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Contenedor</td>
      <td>Modelo</td>
      <td>
        <font size="2"> Protocolo: mssql-mds <br>Autenticación: {windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; versión </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Tabla</td>
      <td>Entidad</td>
      <td>
        <font size="2"> Protocolo: mssql-mds <br>Autenticación: {windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; versión <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; entidad </font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> Protocolo: document-db <br>Autenticación: {azure-access-key} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Colección</td>
      <td>Colección</td>
      <td>
        <font size=2> Protocolo: document-db <br>Autenticación: {azure-access-key} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; colección </font>
      </td>
    </tr>
    <tr>
      <td>ODBC genérico</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> Protocolo: odbc <br>Autenticación: {básica, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; opciones <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>ODBC genérico</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> Protocolo: odbc <br>Autenticación: {básica, windows} <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; opciones <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema </font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> protocolo: sybase <br>autenticación: {básica, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> protocolo: sybase <br>autenticación: {básica, windows} <br>dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; base de datos <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Otro (ninguno de los anteriores)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2> Protocolo: generic-asset <br>Dirección: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId </font>
      </td>
    </tr>
</table>
