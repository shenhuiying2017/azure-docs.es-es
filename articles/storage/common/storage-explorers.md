---
title: Herramientas para trabajar con Azure Storage | Microsoft Docs
description: Lista de herramientas que le permiten ver datos de Almacenamiento de Azure o interactuar con ellos.
services: storage
documentationcenter: 
author: dineshmurthy
manager: jahogg
editor: tysonn
ms.assetid: e4748642-98c4-437e-b0ed-4f9641c2e894
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: dineshmurthy
ms.openlocfilehash: 5c2add48b128a3e5a632c048f0feb4413fcb26cc
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2017
---
# <a name="azure-storage-client-tools"></a>Herramientas de cliente de Almacenamiento de Azure
Los usuarios de Almacenamiento de Azure con frecuencia desean poder ver o interactuar con sus datos mediante una herramienta de cliente de Almacenamiento de Azure. En las tablas siguientes, mostramos un número de herramientas que le permiten hacer esto. Escribimos una "X" en cada bloque si permite enumerar la abstracción de los datos o tener acceso a ella. La tabla también muestra si las herramientas están disponibles. "Versión de prueba" indica que cuenta con una evaluación gratuita, pero que el producto completo no es gratuito. "S/N" indica que hay disponible una versión gratuita, mientras que la versión disponible para la compra es diferente.

Únicamente proporcionamos una instantánea de las herramientas de cliente de Almacenamiento de Azure. Estas herramientas pueden seguir evolucionando y ampliando su funcionalidad. Si existen correcciones o actualizaciones, le agradeceremos que nos lo haga saber a través de un comentario. De igual manera, si conoce otras herramientas que deberíamos mostrar, estaremos encantados de agregarlas.

**Herramientas de cliente de Almacenamiento de Microsoft Azure**

<table>
  <tr>
    <th rowspan="2">Herramienta de cliente de Almacenamiento de Azure</th>
    <th rowspan="2">Blob en bloques</th>
    <th rowspan="2">Blob en páginas</th>
    <th rowspan="2">Append Blob</th>
    <th rowspan="2">Tablas</th>
    <th rowspan="2">Colas</th>
    <th rowspan="2">Archivos</th>
    <th rowspan="2">Gratuito</th>
    <th colspan="4">Plataforma</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Microsoft Azure Portal</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://storageexplorer.com/">Explorador de Microsoft Azure Storage</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Explorador de servidores de Microsoft Visual Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**Herramientas de cliente de Almacenamiento de Azure de terceros**

No hemos comprobado la funcionalidad ni la calidad proporcionadas por las siguientes herramientas de terceros; el hecho de que figuren en esta lista no implica su aprobación por parte de Microsoft.

<table>
  <tr>
    <th rowspan="2">Herramienta de cliente de Almacenamiento de Azure</th>
    <th rowspan="2">Blob en bloques</th>
    <th rowspan="2">Blob en páginas</th>
    <th rowspan="2">Append Blob</th>
    <th rowspan="2">Tablas</th>
    <th rowspan="2">Colas</th>
    <th rowspan="2">Archivos</th>
    <th rowspan="2">Gratuito</th>
    <th colspan="4">Plataforma</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="http://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Versión de prueba</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgate: Azure Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Azure Web Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.cloudberrylab.com/explorer/microsoft-azure.aspx">CloudBerry Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>S/N</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gapotchenko.com/cloudcombine">Cloud Combine</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Versión de prueba</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gladinet.com/Azure-Storage/index.htm">Gladinet Cloud</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>Versión de prueba</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>
