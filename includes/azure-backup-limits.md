---
title: archivo de inclusión
description: archivo de inclusión
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: 1f41567f7d5dcc9b6536b47ef639ba53abff8ec4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
ms.locfileid: "31613643"
---
Los límites siguientes corresponden a Azure Backup.

| Identificador de límites | Límite predeterminado |
| --- | --- |
| Número de servidores o máquinas que se pueden registrar en cada almacén |50 para Windows Server/Client/SCDPM  <br/> 1000 para máquinas virtuales de IaaS |
| Tamaño del origen de datos para los datos almacenados en un almacén de Azure |54 400 GB como máximo<sup>1</sup> |
| Número de almacenes de copia de seguridad que se pueden crear en cada suscripción a Azure |500 almacenes de Recovery Services por región |
| Número de veces por día que se puede programar la copia de seguridad |3 por día para servidor o cliente Windows  <br/> 2 por día para SCDPM <br/> 1 vez al día para máquinas virtuales de IaaS |
| Discos de datos conectados a una máquina virtual de Azure para copia de seguridad |16 |
| Tamaño de un disco de datos individual conectado a una máquina virtual de Azure para copia de seguridad| 4095 GB <sup>2</sup>|

* <sup>1</sup>El límite de 54 400 GB no se aplica a la copia de seguridad de máquinas virtuales IaaS.
 

