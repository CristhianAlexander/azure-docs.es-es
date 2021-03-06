---
title: Bibliotecas de cliente necesarias para conectarse a Azure Analysis Services | Microsoft Docs
description: Se describen las bibliotecas de cliente necesarias para que las aplicaciones cliente y las herramientas de cliente se conecten a Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/14/2016
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 83f5dc8d28a442a94ffca385ec9f7c5dd9d21f40
ms.contentlocale: es-es
ms.lasthandoff: 06/23/2017


---

# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Bibliotecas de cliente para la conexión a Azure Analysis Services

Se requieren bibliotecas de cliente para que las aplicaciones cliente y las herramientas de cliente se puedan conectar a servidores de Analysis Services. 

Analysis Services usa tres bibliotecas de cliente. ADOMD.NET y los objetos de administración de Analysis Services (AMO), son bibliotecas de cliente administradas. El proveedor OLE DB de Analysis Services (MSOLAP DLL) es una biblioteca de cliente nativa. Normalmente, las tres bibliotecas se instalan al mismo tiempo. Azure Analysis Services requiere la versión más reciente de las bibliotecas. 

Las aplicaciones cliente de Microsoft, como Power BI Desktop y Excel, instalan las tres bibliotecas de cliente. Sin embargo, dependiendo de la versión o la frecuencia de las actualizaciones, las bibliotecas de cliente pueden no ser las versiones más recientes que necesita Azure Analysis Services. Esto mismo se aplica a aplicaciones personalizadas u otras interfaces, como AsCmd, TOM, ADOMD.NET. Estas aplicaciones requieren la instalación manual de las bibliotecas. Las bibliotecas de cliente para la instalación manual se incluyen en los paquetes de características de SQL Server como paquetes de distribución. Sin embargo, estas bibliotecas de cliente están asociadas a la versión de SQL Server y no pueden ser la versión más reciente.  

Las bibliotecas de cliente para conexiones de cliente son diferentes de los proveedores de datos necesarios para conectarse desde un servidor de Azure Analysis Services a un origen de datos. Para más información sobre las conexiones de origen de datos, consulte [Conexiones a origen de datos](analysis-services-datasource.md).

## <a name="download-the-latest-preview-client-libraries"></a>Descargue la **versión preliminar** más reciente de las bibliotecas de cliente.  
Use las siguientes bibliotecas de cliente para obtener las últimas correcciones de errores y actualizaciones. 

[MSOLAP (amd64) (versión preliminar)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/amd64/SQL_AS_OLEDB.msi)</br>
[MSOLAP (x86) (versión preliminar)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/x86/SQL_AS_OLEDB.msi)</br>
[AMO (versión preliminar)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_AMO.msi)</br>
[ADOMD (versión preliminar)](http://download.microsoft.com/download/4/8/2/482E5799-9B8E-4724-8A4C-F301BAE788EE/14.0.500.170/SQL_AS_ADOMD.msi)</br>

## <a name="download-the-latest-rtm-client-libraries"></a>Descargue la versión más reciente de las bibliotecas de cliente de **RTM**.  
Use las siguientes bibliotecas de cliente si se encuentra en un entorno de producción y necesita versiones totalmente finales y que ofrezcan una compatibilidad plena.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Pasos siguientes
[Connect with Excel](analysis-services-connect-excel.md)   (Conexión con Excel)  
[Conexión con Power BI](analysis-services-connect-pbi.md)

