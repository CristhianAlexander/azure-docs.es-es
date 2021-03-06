---
title: "Sincronización de datos (versión preliminar) | Microsoft Docs"
description: "Se trata de una introducción a Azure SQL Data Sync (versión preliminar)."
services: sql-database
documentationcenter: 
author: douglaslms
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: douglasl
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 075b5563688158289d51f2f0b5da4a3441ddd13a
ms.contentlocale: es-es
ms.lasthandoff: 06/29/2017


---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Sincronización de datos entre varias bases de datos locales y de la nube con SQL Data Sync

SQL Data Sync es un servicio basado en Azure SQL Database que permite sincronizar los datos seleccionados de manera bidireccional entre varias bases de datos SQL e instancias de SQL Server.

Data Sync se basa en el concepto de un grupo de sincronización. Un grupo de sincronización es un grupo de bases de datos que desea sincronizar.

Un grupo de sincronización tiene varias propiedades, incluidas las siguientes:

-   En el **esquema de sincronización** se describen qué datos se están sincronizando.

-   La **dirección de sincronización** puede ser bidireccional o puede fluir solo en una dirección. Es decir, la dirección de sincronización puede ser de la *base de datos central al cliente* o del *cliente a la base de datos central*, o ambas opciones.

-   El **intervalo de sincronización** se refiere a la frecuencia con que se produce la sincronización.

-   El **directiva de resolución de conflictos** es una directiva de nivel de grupo, que puede ser *Prevalece la base de datos central* o *Prevalece el cliente*.

Data Sync usa una topología de concentrador y radio para sincronizar los datos. Debe definir una de las bases de datos del grupo como la base de datos central. El resto de las bases de datos son bases de datos miembro. La sincronización solo se produce entre la base de datos central y los clientes individuales.
-   La **base de datos central** debe ser una instancia de Azure SQL Database.
-   Las **bases de datos miembro** pueden ser bases de datos SQL, bases de datos de SQL Server locales o instancias de SQL Server en Azure Virtual Machines.
-   La **base de datos de sincronización** contiene los metadatos y el registro para Data Sync. La base de datos de sincronización tiene que ser una instancia de Azure SQL Database ubicada en la misma región que la base de datos central. La base de datos la crea el propio cliente y es de su propiedad.

> [!NOTE]
> Si usa una base de datos local, debe [configurar un agente local](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-sql-data-sync).

![Sincronización de datos entre bases de datos](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>Cuándo usar Data Sync

Data Sync es útil en los casos en que es necesario mantener los datos actualizados entre varias bases de datos SQL de Azure o bases de datos de SQL Server. Estos son los casos de uso principales de Data Sync:

-   **Sincronización de datos híbridos:** con la sincronización de datos, puede mantener los datos sincronizados entre bases de datos locales y bases de datos SQL de Azure para habilitar aplicaciones híbridas con su capa de datos de SQL. Esta funcionalidad puede interesar a los clientes que se plantean realizar la migración a la nube y les gustaría colocar algunas de sus aplicaciones en Azure.

-   **Aplicaciones distribuidas:** en muchos casos, es conveniente separar diferentes cargas de trabajo entre diferentes bases de datos. Por ejemplo, si tiene una base de datos de producción de grande, pero también debe ejecutar una carga de trabajo de informes o análisis en estos datos, resulta útil tener una segunda base de datos para esta carga de trabajo adicional. Este enfoque minimiza el impacto de rendimiento en la carga de trabajo de producción. Puede usar Data Sync para mantener estas dos bases de datos sincronizadas.

-   **Aplicaciones globalmente distribuidas:** muchas empresas abarcan varias regiones e incluso varios países. Para minimizar la latencia de red, es preferible disponer de los datos en una región más cercana. Con Data Sync, puede mantener sincronizadas con facilidad las bases de datos de regiones de todo el mundo.

No se recomienda Data Sync en los siguientes escenarios:

-   Recuperación ante desastres

-   Escalado de lectura

-   ETL (OLTP a OLAP)

-   Migración de SQL Server local a Azure SQL Database

## <a name="how-does-data-sync-work"></a>¿Cómo funciona Data Sync? 

-   **Seguimiento de cambios de datos:** Data Sync realiza un seguimiento de cambios mediante los desencadenadores de inserción, actualización y eliminación. Los cambios se registran en una tabla en la base de datos de usuario.

-   **Sincronización de datos:** Data Sync está diseñado en un modelo de concentrador y radio. La base de datos central se sincronizada con cada cliente individualmente. Los cambios de la base de datos central se descargan en el cliente y, después, los cambios del cliente se cargan en la base de datos central.

-   **Resolución de conflictos:** Data Sync proporciona dos opciones para la resolución de conflictos, *Prevalece la base de datos central* o *Prevalece el cliente*.
    -   Si selecciona *Prevalece la base de datos central*, los cambios de la base de datos central siempre sobrescriben los cambios del cliente.
    -   Si selecciona *Prevalece el cliente*, los cambios del cliente sobrescriben los cambios de la base de datos central. Si hay más de un cliente, el valor final depende del cliente que primero se sincronice.

## <a name="limitations-and-considerations"></a>Limitaciones y consideraciones

### <a name="performance-impact"></a>Impacto en el rendimiento
Data Sync usa desencadenadores de inserción, actualización y eliminación para realizar un seguimiento de los cambios. Crea tablas en la base de datos de usuario. Estas actividades repercuten en la carga de trabajo de la base de datos, por lo que debe evaluar el servicio y actualizarlo, si procede.

### <a name="eventual-consistency"></a>Coherencia final
Como Data Sync se basa en desencadenadores, la coherencia transaccional no está garantizada. Microsoft garantiza que todos los cambios se realicen finalmente y que Data Sync no ocasione pérdida de datos.

### <a name="unsupported-data-types"></a>Tipos de datos no admitidos

-   Secuencia de archivos

-   UDT SQL/CLR

-   XMLSchemaCollection (XML admitido)

-   Cursor, Timestamp, Hierarchyid

### <a name="requirements"></a>Requisitos

-   Cada tabla debe tener una clave principal.

-   Una tabla no puede tener columnas de identidad que no sean la clave principal.

-   El nombre de la base de datos no puede contener caracteres especiales.

### <a name="limitations-on-service-and-database-dimensions"></a>Limitaciones de las dimensiones de la base de datos y del servicio

|                                                                 |                        |                             |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| **Dimensiones**                                                      | **Límite**              | **Solución alternativa**              |
| Número máximo de grupos de sincronización a los que una base de datos puede pertenecer       | 5                      |                             |
| Número máximo de puntos de conexión en un único grupo de sincronización              | 30                     | Crear varios grupos de sincronización |
| Número máximo de puntos de conexión locales en un único grupo de sincronización | 5                      | Crear varios grupos de sincronización |
| Nombres de base de datos, tabla, esquema y columna                       | 50 caracteres por nombre |                             |
| Tablas de un grupo de sincronización                                          | 500                    | Crear varios grupos de sincronización |
| Columnas de una tabla de un grupo de sincronización                              | 1000                   |                             |
| Tamaño de la fila de datos en una tabla                                        | 24 Mb                  |                             |
| Intervalo de sincronización mínimo                                           | 5 minutos              |                             |

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre SQL Database y SQL Data Sync, vea:

-   [Introducción a SQL Data Sync](sql-database-get-started-sql-data-sync.md)

-   [Descarga de la documentación técnica completa de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)

-   [Descarga de la documentación de la API de REST de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

-   [Información general de Base de datos SQL](sql-database-technical-overview.md)

-   [Administración del ciclo de vida de las aplicaciones](https://msdn.microsoft.com/library/jj907294.aspx)



