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
ms.openlocfilehash: b345283f87c446ff3b583b0c5dd8a4be222303ae
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
Los límites siguientes corresponden a Azure Backup.

| Identificador de límites | Límite predeterminado |
| --- | --- |
| Número de servidores o máquinas que se pueden registrar en cada almacén |50 para Windows Server/Client/SCDPM  <br/> 200 para máquinas virtuales de IaaS |
| Tamaño del origen de datos para los datos almacenados en un almacén de Azure |54 400 GB como máximo<sup>1</sup> |
| Número de almacenes de copia de seguridad que se pueden crear en cada suscripción a Azure |25 almacenes de Recovery Services por región |
| Número de veces por día que se puede programar la copia de seguridad |3 por día para servidor o cliente Windows  <br/> 2 por día para SCDPM <br/> 1 vez al día para máquinas virtuales de IaaS |
| Discos de datos conectados a una máquina virtual de Azure para copia de seguridad |16 |
| Tamaño de un disco de datos individual conectado a una máquina virtual de Azure para copia de seguridad| 4095 GB <sup>2</sup>|

* <sup>1</sup>El límite de 54 400 GB no se aplica a la copia de seguridad de máquinas virtuales IaaS.
 

