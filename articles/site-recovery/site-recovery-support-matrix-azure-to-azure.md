---
title: "Matriz de compatibilidad de Azure Site Recovery para la replicación de Azure a Azure | Microsoft Docs"
description: "Se resumen los sistemas operativos compatibles y las configuraciones para la replicación de Azure Site Recovery de máquinas virtuales (VM) de Azure de una región a otra en caso de que sea necesario realizar una recuperación ante desastres."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/10/2017
ms.author: sujayt
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 7c30f5164b9fe7ff6044bbf23767a5db9a0f7c30
ms.contentlocale: es-es
ms.lasthandoff: 06/15/2017


---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-azure-to-azure"></a>Matriz de compatibilidad de Azure Site Recovery para la replicación de Azure a Azure


>[!NOTE]
>
> La replicación de Site Recovery en máquinas virtuales de Azure se encuentra actualmente en versión preliminar.

En este artículo se resumen las configuraciones y los componentes admitidos en Azure Site Recovery al replicar y recuperar máquinas virtuales de Azure de una región a otra.

## <a name="user-interface-options"></a>Opciones de la interfaz de usuario

**Interfaz de usuario** |  **Se admite/no se admite**
--- | ---
**Portal de Azure** | Compatible
**Portal clásico** | No compatible
**PowerShell** | No se admite actualmente.
**API de REST** | No se admite actualmente.
**CLI** | No se admite actualmente.


## <a name="resource-move-support"></a>Compatibilidad con el movimiento de recursos.

**Tipo de movimiento de recursos** | **Se admite/no se admite** | **Comentarios**  
--- | --- | ---
**Mover el almacén entre grupos de recursos** | No compatible |No se puede mover el almacén de servicios de recuperación entre grupos de recursos.
**Mover los servicios Compute, Storage y Network entre grupos de recursos** | No compatible |Si mueve una máquina virtual (o sus componentes asociados, como Storage y Network) después de habilitar la replicación, deberá deshabilitar la replicación y habilitarla de nuevo en la máquina virtual.


## <a name="support-for-deployment-models"></a>Compatibilidad con modelos de implementación

**Modelo de implementación** | **Se admite/no se admite** | **Comentarios**  
--- | --- | ---
**Clásico** | Compatible | Solo puede replicar una máquina virtual clásica y recuperarla como máquina virtual clásica. No puede recuperarla como una máquina virtual de Resource Manager. Tampoco se admite la implementación de una máquina virtual clásica sin una red virtual y directamente en una región de Azure.
**Resource Manager** | Compatible |

## <a name="support-for-replicated-machine-os-versions"></a>Compatibilidad con las versiones de SO de las máquinas replicadas

Esta compatibilidad es aplicable a cualquier carga de trabajo que se ejecute en el SO mencionado.

#### <a name="windows"></a>Windows

- Windows Server 2012 R2 de 64 bits
- Windows Server 2012
- Windows Server 2008 R2 con al menos SP1

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 7.1, 7.2, 7.3
- CentOS 6.5, 6.6, 6.7, 6.8, 7.0, 7.1, 7.2, 7.3
- Oracle Enterprise Linux 6.4 y 6.5, en ejecución en el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Sistemas de archivos y configuraciones de almacenamiento de invitado admitidos en máquinas virtuales de Azure que ejecutan el sistema operativo Linux

* Sistemas de archivos: ext3, ext4, ReiserFS (solo Suse Linux Enterprise Server), XFS
* Administrador de volúmenes: LVM2
* Software de múltiples rutas: asignador de dispositivos

## <a name="region-support"></a>Regiones admitidas

Puede replicar y recuperar máquinas virtuales entre dos regiones cualesquiera dentro del mismo clúster geográfico.

**Clúster geográfico** | **Regiones de Azure**
-- | --
América | Centro de Canadá y este de Canadá, centro-sur de EE. UU., centro-oeste de EE. UU., este de EE. UU., este de EE. UU. 2, oeste de EE. UU., oeste de EE. UU. 2 centro de EE. UU., centro-norte de EE. UU.
Europa | Oeste de Reino Unido, Sur de Reino Unido, Europa del Norte, Europa Occidental
Asia | India del Sur, India Central, Sudeste Asiático, Asia Oriental, Japón Oriental, Japón Occidental
Australia   | Este de Australia, Sudeste de Australia

>[!NOTE]
>
> En la región sur de Brasil, solo puede replicar y conmutar por error a las regiones de centro-sur de EE. UU., centro-oeste de EE. UU., este de EE. UU., este de EE. UU. 2, oeste de EE. UU., oeste de EE. UU. 2 y centro-norte de EE. UU. y conmutar por recuperación.


## <a name="support-for-compute-configuration"></a>Compatibilidad con la configuración de Compute

**Configuración** | **No admite/no se admite** | **Comentarios**
--- | --- | ---
Tamaño | Cualquier tamaño de máquina virtual de Azure con 2 núcleos de CPU y 1 GB de RAM | Consulte los [tamaños de máquina virtual de Azure](../virtual-machines/windows/sizes.md)
Conjuntos de disponibilidad | Compatible | Si usa la opción predeterminada durante el paso para habilitar la replicación en el portal, el conjunto de disponibilidad se crea automáticamente en función de la configuración de la región de origen. Puede cambiar el conjunto de disponibilidad de destino en "Elemento replicado > Configuración > Compute and Network (Proceso y red) > Conjunto de disponibilidad" en cualquier momento.
Máquinas virtuales con ventaja de uso híbrido (HUB) | Compatible | Si la máquina virtual de origen tiene habilitada una licencia HUB, en la conmutación por error de prueba o la máquina virtual de conmutación por error también se usa la licencia HUB.
Conjuntos de escalado de máquinas virtuales | No compatible |
Imágenes de la galería de Azure (publicadas por Microsoft) | Compatible | Se admiten siempre y cuando la máquina virtual se ejecute en un sistema operativo compatible con Site Recovery
Imágenes de la Galería de Azure (publicadas por terceros) | Compatible | Se admiten siempre y cuando la máquina virtual se ejecute en un sistema operativo compatible con Site Recovery.
Imágenes personalizadas (publicados de terceros) | Compatible | Se admiten siempre y cuando la máquina virtual se ejecute en un sistema operativo compatible con Site Recovery.
Máquinas virtuales migradas con Site Recovery | Compatible | Si es una máquina de VMware o física que se migra a Azure mediante Site Recovery, deberá desinstalar la versión antigua del servicio de movilidad y reiniciar la máquina antes de replicarla en otra región de Azure.

## <a name="support-for-storage-configuration"></a>Compatibilidad con la configuración de Storage

**Configuración** | **No admite/no se admite** | **Comentarios**
--- | --- | ---
Tamaño de disco máximo del sistema operativo | Tamaño de disco máximo del sistema operativo que se admite en Azure.| Consulte [Discos usados por las máquinas virtuales](../storage/storage-about-disks-and-vhds-windows.md#disks-used-by-vms).
Tamaño máximo del disco de datos | Tamaño máximo del disco de datos que se admite en Azure.| Consulte [Discos usados por las máquinas virtuales](../storage/storage-about-disks-and-vhds-windows.md#disks-used-by-vms).
Número de discos de datos | Hasta 64, que es el admitido por un tamaño de máquina virtual específico de Azure. | Consulte los [tamaños de máquina virtual de Azure](../virtual-machines/windows/sizes.md)
Disco temporal | Siempre se excluyen de la replicación | El disco temporal se excluye de la replicación siempre. Como recomienda Azure, no se deben colocar los datos persistentes en los discos temporales. Consulte [Discos temporales en máquinas virtuales de Azure](../storage/storage-about-disks-and-vhds-windows.md#temporary-disk) para más información.
Velocidad de cambio de datos en el disco | 6 Mbps como máximo por disco. | Si la velocidad media de cambio de los datos en el disco sobrepasa los 6 Mbps continuamente, la replicación no mantendrá el ritmo. Sin embargo, si es una ráfaga de datos ocasional y la velocidad de cambio de los datos es superior a 6 Mbps durante algún tiempo y desciende, la replicación mantendrá el ritmo. En este caso, podría ver puntos de recuperación ligeramente retrasados.
Discos en cuentas de almacenamiento estándar | Compatible |
Discos en cuentas de almacenamiento premium | Compatible | Si una máquina virtual tiene discos repartidas entre cuentas de almacenamiento estándar y premium, puede seleccionar una cuenta de almacenamiento de destino diferente para cada disco a fin de garantizar que tenga la misma configuración de almacenamiento en la región de destino.
Discos administrados estándar | No compatible |  
Discos administrados premium | No compatible |
Espacios de almacenamiento | No compatible |         
Cifrado en reposo (SSE) | Compatible | Para las cuentas de almacenamiento de destino y de almacenamiento en caché, puede seleccionar una cuenta de almacenamiento habilitada para SSE.     
Azure Disk Encryption (ADE) | No compatible |
Agregar/quitar disco en caliente | No compatible | Si agrega o quita un disco de datos en la máquina virtual, deberá deshabilitar la replicación y habilitarla de nuevo para la máquina virtual.
Excluir el disco | No compatible|   El disco temporal se excluye de forma predeterminada.
LRS | Compatible |
GRS | Compatible |
RA-GRS | Compatible |
ZRS | Compatible |  
Almacenamiento en frío y en caliente | No compatible | Los discos de máquina virtual no admiten el almacenamiento temporal y permanente.

>[!IMPORTANT]
> Asegúrese de seguir las [instrucciones sobre almacenamiento](../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) en las máquinas virtuales de Azure de origen para evitar problemas de rendimiento. Si sigue la configuración predeterminada, Site Recovery creará las cuentas de almacenamiento necesarias en función de la configuración de origen. Si personaliza y selecciona su propia configuración, asegúrese de seguir las instrucciones que se describen en (../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) en sus máquinas virtuales de origen.

## <a name="support-for-network-configuration"></a>Compatibilidad con la configuración de red
**Configuración** | **No admite/no se admite** | **Comentarios**
--- | --- | ---
Tarjeta de interfaz de red (NIC) | Hasta el número máximo de NIC admitidas por un tamaño específico de máquina virtual de Azure. | Las NIC se crean cuando la máquina virtual se crea como parte de la operación de conmutación por error o conmutación por error de prueba. El número de tarjetas NIC en la máquina virtual de conmutación por error viene determinado por el número de tarjetas NIC que haya en la máquina virtual de origen en el momento de habilitar la replicación. El hecho de agregar o quitar tarjetas NIC después de habilitar la replicación, no influye sobre el número de tarjetas NIC en la máquina virtual de conmutación por error.
Equilibrador de carga de Internet | Compatible | Deberá asociar el equilibrador de carga configurado previamente con un script de automatización de Azure de un plan de recuperación.
Equilibrador de carga interno | Compatible | Deberá asociar el equilibrador de carga configurado previamente con un script de automatización de Azure de un plan de recuperación.
Dirección IP pública| Compatible | Deberá asociar a la NIC una IP pública ya existente o una nueva mediante un script de automatización de Azure de un plan de recuperación.
NSG en NIC (Resource Manager)| Compatible | Deberá asociar el NSG a la NIC con un script de automatización de Azure de un plan de recuperación.  
NSG en una subred (Resource Manager y modelo clásico)| Compatible | Deberá asociar el NSG a la NIC con un script de automatización de Azure de un plan de recuperación.
NSG en una máquina virtual (modelo clásico)| Compatible | Deberá asociar el NSG a la NIC con un script de automatización de Azure de un plan de recuperación.
IP reservada (IP estática)/retener la IP de origen | Compatible | Si la NIC de la máquina virtual de origen tiene una configuración de IP estática y la subred de destino tiene la misma IP disponible, se asigna a la máquina virtual de conmutación por error. Si la subred de destino no tiene la misma IP disponible, una de las direcciones IP disponibles de la subred se reserva para esta máquina virtual. Puede especificar una IP fija de su elección en "Elemento replicado > Configuración > Compute and Network (Proceso y red) > Interfaces de red". Puede seleccionar la NIC y especificar la subred y la dirección IP de su elección.
IP dinámica| Compatible | Si la NIC de la máquina virtual de origen tiene una configuración de IP dinámica, la NIC de la máquina virtual de conmutación por error es también dinámica de forma predeterminada. Puede especificar una IP fija de su elección en "Elemento replicado > Configuración > Compute and Network (Proceso y red) > Interfaces de red". Puede seleccionar la NIC y especificar la subred y la dirección IP de su elección.
Integración de Traffic Manager | Compatible | Puede configurar previamente Traffic Manager de forma que el tráfico se dirija regularmente al punto de conexión de la región de origen y al punto de conexión de la región de destino en caso de conmutación por error.
DNS administrado por Azure | Compatible |
DNS personalizado  | Compatible |    
Proxy no autenticado | Compatible | Consulte el [documento de instrucciones sobre redes](site-recovery-azure-to-azure-networking-guidance.md).    
Proxy autenticado | No compatible | Si la máquina virtual usa un proxy autenticado para la conectividad saliente, no se puede replicar mediante Azure Site Recovery.    
VPN de sitio a sitio local (con o sin ExpressRoute)| Compatible | Asegúrese de que los UDR y NSG estén configurados de manera que el tráfico de Site Recovery no se dirija al entorno local. Consulte el [documento de instrucciones sobre redes](site-recovery-azure-to-azure-networking-guidance.md).  
Conexión de red virtual a red virtual | Compatible | Consulte el [documento de instrucciones sobre redes](site-recovery-azure-to-azure-networking-guidance.md).  


## <a name="next-steps"></a>Pasos siguientes
- Aprenda más en las [instrucciones sobre redes para replicar máquinas virtuales de Azure](site-recovery-azure-to-azure-networking-guidance.md).
- Comience a proteger las cargas de trabajo mediante la [replicación de máquinas virtuales de Azure](site-recovery-azure-to-azure.md).

