---
title: "archivo de inclusión"
description: "archivo de inclusión"
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk
ms.custom: include file
ms.openlocfilehash: 7ca5b34961b4d0e3d4fcecb8175e3e0901d7049d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
Los límites siguientes corresponden a Azure Backup.

| Identificador de límites | Límite predeterminado |
| --- | --- |
| Número de servidores o máquinas que se pueden registrar en cada almacén |50 para Windows Server/Client/SCDPM  <br/> 200 para máquinas virtuales de IaaS |
| Tamaño del origen de datos para los datos almacenados en un almacén de Azure |54 400 GB como máximo<sup>1</sup> |
| Número de almacenes de copia de seguridad que se pueden crear en cada suscripción a Azure |25 almacenes de Recovery Services por región |
| Número de veces por día que se puede programar la copia de seguridad |3 por día para servidor o cliente Windows  <br/> 2 por día para SCDPM <br/> 1 vez al día para máquinas virtuales de IaaS |
| Discos de datos conectados a una máquina virtual de Azure para copia de seguridad |16 |
| Tamaño de un disco de datos individual conectado a una máquina virtual de Azure para copia de seguridad| 1024 GB <sup>2</sup>|

* <sup>1</sup>El límite de 54 400 GB no se aplica a la copia de seguridad de máquinas virtuales IaaS.
* <sup>2</sup> Disponemos de una [versión preliminar privada](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a?redir=0) para admitir discos de hasta 4 TB. 

