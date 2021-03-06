---
title: "Integración de Key Vault con SQL Server en máquinas virtuales Windows en Azure (Resource manager) | Microsoft Docs"
description: "Aprenda a automatizar la configuración de cifrado de SQL Server para su uso con Almacén de claves de Azure. Este tema explica cómo usar la integración de Almacén de claves de Azure con máquinas virtuales de SQL Server creadas con el Administrador de recursos."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/23/2017
ms.author: jroth
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 7dd5261960d3f93f01e221c5f5d7a397bc77d590
ms.contentlocale: es-es
ms.lasthandoff: 03/25/2017


---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Configurar la integración de Azure Key Vault para SQL Server en máquinas virtuales de Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Clásico](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Información general
SQL Server tiene varias características de cifrado, como el [cifrado de datos transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), el [cifrado de nivel de columna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) y el [cifrado de copia de seguridad](https://msdn.microsoft.com/library/dn449489.aspx). Estas formas de cifrado requieren administrar y almacenar las claves criptográficas que se usan para el cifrado. El servicio de Almacén de claves de Azure (AKV) está diseñado para mejorar la seguridad y la administración de estas claves en una ubicación segura y con gran disponibilidad. El [conector de SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permite que SQL Server use estas claves de Almacén de claves de Azure.

Si se ejecuta SQL Server con equipos locales, hay una serie de [pasos a seguir para tener acceso a Almacén de claves de Azure desde el equipo de SQL Server local](https://msdn.microsoft.com/library/dn198405.aspx). Pero para SQL Server en las máquinas virtuales de Azure, puede ahorrar tiempo usando la característica *Integración de Almacén de claves de Azure* .

Cuando se habilita esta característica, automáticamente se instala el conector de SQL Server, se configura el proveedor EKM para obtener acceso a Almacén de claves de Azure y se crea la credencial para que pueda tener acceso a su almacén. Si examinamos los pasos descritos en la documentación local que se mencionó anteriormente, puede ver que esta característica automatiza los pasos 2 y 3. Lo único que aún tiene que hacer manualmente es crear el Almacén de claves y las claves. Desde allí, se automatiza toda la configuración de la máquina virtual de SQL. Cuando esta característica haya completado el programa de instalación, puede ejecutar instrucciones de T-SQL para empezar a cifrar sus bases de datos o copias de seguridad como lo haría normalmente.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>Habilitación y configuración de la integración de Almacén de claves de Azure
Puede habilitar la integración de Almacén de claves de Azure durante el aprovisionamiento o configurarlo para las máquinas virtuales existentes.

### <a name="new-vms"></a>Nuevas máquinas virtuales
Si está aprovisionando una nueva máquina virtual de SQL Server con Resource Manager, el Portal de Azure proporciona un paso para habilitar la integración del Almacén de claves de Azure. La característica Azure Key Vault está disponible solo para las ediciones Enterprise, Developer y Evaluation de SQL Server.

![Integración del Almacén de claves de Azure de SQL](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Para obtener un tutorial detallado del aprovisionamiento, consulte [Aprovisionamiento de una máquina virtual de SQL Server en el Portal de Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Máquinas virtuales existentes
Para las máquinas virtuales de SQL Server existentes, seleccione su máquina virtual de SQL Server. Después, seleccione la sección **Configuración de SQL Server** de la hoja **Configuración**.

![Integración de Almacén de claves de Azure de SQL para máquinas virtuales existentes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

En la hoja **Configuración de SQL Server**, haga clic en el botón **Editar** de la sección Integración de Almacén de claves automatizada.

![Configuración de Almacén de claves de Azure de SQL para máquinas virtuales existentes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

Cuando termine, haga clic en el botón **Aceptar** situado en la parte inferior de la hoja **Configuración de SQL Server** para guardar los cambios.

> [!NOTE]
> También puede usar una plantilla para configurar la integración de Almacén de claves de Azure. Para más información, consulte la [plantilla de inicio rápido de Azure para la integración de Almacén de claves de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).
> 
> 

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]


