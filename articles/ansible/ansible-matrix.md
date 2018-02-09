---
title: "Matriz de módulo y versión de Ansible para Azure"
description: "Matriz de módulo y versión de Ansible para Azure"
ms.service: ansible
keywords: "ansible, roles, matriz, versión, azure, devops"
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/19/2018
ms.topic: article
ms.openlocfilehash: f62cc2df9e4ce815c4427b80e271ddc672748e4f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="ansible-module-and-version-matrix"></a>Matriz de módulo y versión de Ansible

## <a name="ansible-modules-for-azure"></a>Módulos de Ansible para Azure
Ansible se suministra con una serie de módulos que se pueden ejecutar directamente en los hosts remotos o mediante guiones de procedimientos.
En este artículo se enumeran los módulos de Ansible para Azure que pueden aprovisionar recursos en la nube de Azure, como máquinas virtuales, redes y servicios de contenedor. Puede obtener estos módulos desde la versión oficial de Ansible o desde los siguientes roles de guion de procedimientos publicados por Microsoft.

| Módulo Ansible para Azure                   |  Ansible 2.4 |  Rol de guion de procedimientos [azure_module](#introduction-to-azuremodule) |  Rol de guion de procedimientos [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|-----------------------------|-------------------------------------| 
| **Proceso**                    |           |                          |                                  | 
| azure_rm_availabilityset                    | Sí          | Sí                         | Sí                                 | 
| azure_rm_availabilityset_facts              | Sí          | Sí                         | Sí                                 | 
| azure_rm_deployment                         | Sí          | Sí                         | Sí                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Sí          | Sí                         | Sí                                 | 
| azure_rm_virtualmachineimage_facts          | Sí          | Sí                         | Sí                                 | 
| azure_rm_resourcegroup                      | Sí          | Sí                         | Sí                                 | 
| azure_rm_resourcegroup_facts                | Sí          | Sí                         | Sí                                 | 
| azure_rm_virtualmachine                     | Sí          | Sí                         | Sí                                 | 
| azure_rm_virtualmachine_extension           | Sí          | Sí                         | Sí                                 | 
| azure_rm_virtualmachine_scaleset            | Sí          | Sí                         | Sí                                 | 
| azure_rm_image                              |              | Sí                         | Sí                                 | 
| **Redes**                    |           |                          |                                  | 
| azure_rm_virtualnetwork                     | Sí          | Sí                         | Sí                                 | 
| azure_rm_virtualnetwork_facts               | Sí          | Sí                         | Sí                                 | 
| azure_rm_subnet                             | Sí          | Sí                         | Sí                                 | 
| azure_rm_networkinterface                   | Sí          | Sí                         | Sí                                 | 
| azure_rm_networkinterface_facts             | Sí          | Sí                         | Sí                                 | 
| azure_rm_publicipaddress                    | Sí          | Sí                         | Sí                                 | 
| azure_rm_publicipaddress_facts              | Sí          | Sí                         | Sí                                 | 
| azure_rm_dnsrecordset                       | Sí          | Sí                         | Sí                                 | 
| azure_rm_dnsrecordset_facts                 | Sí          | Sí                         | Sí                                 | 
| azure_rm_dnszone                            | Sí          | Sí                         | Sí                                 | 
| azure_rm_dnszone_facts                      | Sí          | Sí                         | Sí                                 | 
| azure_rm_loadbalancer                       | Sí          | Sí                         | Sí                                 | 
| azure_rm_loadbalancer_facts                 | Sí          | Sí                         | Sí                                 | 
| azure_rm_appgw                              | -            | -                           | Sí                                 | 
| azure_rm_appgwroute                         | -            | -                           | Sí                                 | 
| azure_rm_appgwroute                         | -            | -                           | Sí                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | Sí                                 |
| azure_rm_appgwroutetable                    | -            | -                           | Sí                                 |
| azure_rm_securitygroup                      | Sí          | Sí                         | Sí                                 | 
| azure_rm_appgwroutetable_facts              | Sí          | Sí                         | Sí                                 | 
| **Storage**                    |           |                          |                                  | 
| azure_rm_storageaccount                     | Sí          | Sí                         | Sí                                 | 
| azure_rm_storageaccount_facts               | Sí          | Sí                         | Sí                                 | 
| azure_rm_storageblob                        | Sí          | Sí                         | Sí                                 | 
| azure_rm_managed_disk                       | Sí          | Sí                         | Sí                                 | 
| azure_rm_managed_disk_facts                 | Sí          | Sí                         | Sí                                 | 
| **Contenedores**                    |           |                          |                                  | 
| azure_rm_acs                                | Sí          | Sí                         | Sí                                 | 
| azure_rm_containerinstance                  | -            | Sí                         | Sí                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | Sí                                 | 
| azure_rm_containerregistry                  | -            | Sí                         | Sí                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | Sí                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | Sí                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | Sí                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | Sí                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | Sí                                 | 
| **Azure Functions**                    |           |                          |                                  | 
| azure_rm_functionapp                        | Sí          | Sí                         | Sí                                 | 
| azure_rm_functionapp_facts                  | Sí          | Sí                         | Sí                                 | 
| **Bases de datos**                    |           |                          |                                  | 
| azure_rm_sqlserver                          | -            | Sí                         | Sí                                 | 
| azure_rm_sqlserver_facts                    | -            | -                           | Sí                                 | 
| azure_rm_sqldatabase                        | -            | Sí                         | Sí                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | Sí                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | Sí                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | Sí                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | Sí                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | Sí                                 | 
| azure_rm_mysqlserver                        | -            | Sí                         | Sí                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | Sí                                 | 
| azure_rm_mysqldatabase                      | -            | Sí                         | Sí                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | Sí                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | Sí                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | Sí                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | Sí                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | Sí                                 | 
| azure_rm_postgresqlserver                   | -            | Sí                         | Sí                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | Sí                                 | 
| azure_rm_postgresqldatabase                 | -            | Sí                         | Sí                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | Sí                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | Sí                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | Sí                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | Sí                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | Sí                                 | 
| **Key Vault**                    |           |                          |                                  | 
| azure_rm_keyvault                           | -            | -                           | Sí                                 |
| azure_rm_keyvault_facts                     | -            | -                           | Sí                                 |
| azure_rm_keyvaultkey                        | -            | -                           | Sí                                 |
| azure_rm_keyvaultsecret                     | -            | -                           | Sí                                 |

## <a name="introduction-to-azuremodule"></a>Introducción a azure_module
El [rol de guion de procedimientos azure_module](https://galaxy.ansible.com/Azure/azure_modules/) incluye los cambios y correcciones de errores más recientes para los módulos de Azure de la [rama de desarrollo del repositorio de Ansible](https://github.com/ansible/ansible/tree/devel). Si no puede esperar a la próxima versión de Ansible, instalar el rol azure_module es una buena opción.

El rol de guion de procedimientos azure_module se publica cada tres semanas.

## <a name="introduction-to-azurepreviewmodule"></a>Introducción a azure_preview_module
El [rol de guion de procedimientos azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) es el rol más completo e incluye todos los módulos más recientes de Azure. Las actualizaciones y correcciones de errores se realizan de manera más oportuna que en la versión oficial de Ansible. Si usa Ansible para el aprovisionamiento de recursos de Azure, se recomienda instalar el rol azure_preview_module.

El rol de guion de procedimientos azure_preview_module se publica cada tres semanas.

## <a name="next-steps"></a>pasos siguientes
Para más información relacionada con los roles de guion de procedimientos, consulte [Creating Reusable Playbooks](http://docs.ansible.com/ansible/latest/playbooks_reuse.html) (Creación de guiones de procedimientos reutilizables). 
