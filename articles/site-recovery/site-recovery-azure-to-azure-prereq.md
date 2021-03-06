---
title: "Requisitos previos para replicación en Azure con Azure Site Recovery | Microsoft Docs"
description: "En este artículo se resumen los requisitos previos para replicar máquinas virtuales y máquinas físicas en Azure con el servicio Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/27/2017
ms.author: rajanaki
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 2796a77984fb811b2ea563a45652bb6312b3dd26
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017

---

#  <a name="prerequisites-for-replicating-azure-virtual-machines-to-another-region-by-using-azure-site-recovery"></a>Requisitos previos para la replicación de máquinas virtuales de Azure a otra región mediante Azure Site Recovery

> [!div class="op_single_selector"]
> * [Replicación de Azure a Azure](site-recovery-azure-to-azure-prereq.md)
> * [Replicación del entorno local a Azure](site-recovery-prereq.md)

El servicio de Azure Site Recovery contribuye a su estrategia de recuperación ante desastres y continuidad empresarial (BCDR) al organizar:
* La replicación de máquinas virtuales de Azure a otra región de Azure.
* La replicación de máquinas virtuales y servidores físicos locales a Azure o a un centro de datos secundario. 

Cuando se producen interrupciones en la ubicación principal, podrá realizar la conmutación por error a una ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. Puede realizar la conmutación por recuperación a la ubicación principal cuando vuelva a su funcionamiento normal. Consulte [¿Qué es Site Recovery?](site-recovery-overview.md) para más información sobre Site Recovery.

En este artículo se resumen los requisitos previos necesarios para comenzar la replicación de Site Recovery a Azure.

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="azure-requirements"></a>Requisitos de Azure

**Requisito** | **Detalles**
--- | ---
**Cuenta de Azure** | Una cuenta de [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Servicio Site Recovery** | Para más información sobre los precios de Site Recovery, consulte los [precios de Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). Se recomienda crear un almacén de Recovery Services en la región de Azure de destino que quiera usar como ubicación de recuperación ante desastres. Por ejemplo, si las máquinas virtuales de origen se ejecutan en el este de EE. UU. y quiere replicar en el centro de EE. UU., se recomienda crear el almacén en el centro de EE. UU.|
**Capacidad de Azure** | Para la región de Azure de destino que quiera usar como la ubicación de recuperación ante desastres, debe tener una suscripción con capacidad suficiente para máquinas virtuales, cuentas de almacenamiento y componentes de red. Para agregar más capacidad, puede ponerse en contacto con el soporte técnico.
**Instrucciones sobre almacenamiento** | Asegúrese de seguir las [instrucciones sobre almacenamiento](../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) en las máquinas virtuales de Azure de origen para evitar problemas de rendimiento. Si sigue la configuración predeterminada, Site Recovery crea las cuentas de almacenamiento necesarias en función de la configuración de origen. Si personaliza y selecciona su propia configuración, asegúrese de seguir los pasos que se describen en [Objetivos de escalabilidad para discos de máquinas virtuales](../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
**Instrucciones sobre redes** | Deberá incluir en la lista de permitidos las direcciones URL o los intervalos IP específicos de la conectividad de salida. Para más información, consulte el artículo [networking guidance for replicating Azure virtual machines](site-recovery-azure-to-azure-networking-guidance.md) (Instrucciones sobre redes para replicar máquinas virtuales de Azure).
**MV de Azure** | Asegúrese de que todos los certificados raíz más recientes estén presentes en la máquina virtual Windows o Linux. Si los certificados raíz más recientes no están presentes, la máquina virtual no se puede registrar en Site Recovery debido a restricciones de seguridad.

>[!NOTE]
>Para más información sobre la compatibilidad con configuraciones específicas, lea la [matriz de compatibilidad](site-recovery-support-matrix-azure-to-azure.md).

## <a name="next-steps"></a>Pasos siguientes
- Para más información, consulte el artículo [Networking guidance for replicating Azure virtual machines](site-recovery-azure-to-azure-networking-guidance.md) (Instrucciones de redes para la replicación de máquinas virtuales de Azure).
- Comience a proteger las cargas de trabajo mediante la [replicación de máquinas virtuales de Azure](site-recovery-azure-to-azure.md).

