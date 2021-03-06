---
title: "Transmisión de registros de diagnóstico de Azure a Event Hubs | Microsoft Docs"
description: "Aprenda a transmitir registros de diagnóstico de Azure a Centros de eventos."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 42bc4845-c564-4568-b72d-0614591ebd80
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: c9063fcc59d83cb2a6a159cf3a69234417138a5c
ms.openlocfilehash: 5cadc3ea77ba13d40876c7bc11d2aa1d6abe6b87


---
# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>Transmisión de registros de diagnóstico de Azure a Centros de eventos
Los **[registros de diagnóstico de Azure](monitoring-overview-of-diagnostic-logs.md)** se pueden transmitir casi en tiempo real a cualquier aplicación mediante la opción "Exportar a Event Hubs" integrada en el Portal o habilitando el identificador de regla de Service Bus en una configuración de diagnóstico por medio de los cmdlets de Azure PowerShell o la CLI de Azure.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Qué se puede hacer con registros de diagnóstico y Centros de eventos
Estas son solo algunas formas de usar la funcionalidad de streaming para registros de diagnóstico:

* **Transmisión de registros a sistemas de registro y telemetría de terceros** : con el tiempo, el streaming de Centros de eventos se convertirá en el mecanismo para canalizar los registros de diagnóstico a sistemas de información de seguridad y administración de eventos (SIEM) y soluciones de análisis de registro de terceros.
* **Visualización del estado del servicio mediante streaming de datos de "ruta de acceso activa" a PowerBI** : utilizando Centros de eventos, Análisis de transmisiones y Power BI, puede transformar fácilmente los datos de diagnóstico en información casi en tiempo real sobre los servicios de Azure. [En este artículo de documentación se ofrece una excelente introducción sobre cómo configurar Centros de eventos, procesar datos con Análisis de transmisiones y usar Power BI como salida](../stream-analytics/stream-analytics-power-bi-dashboard.md). A continuación presentamos algunas recomendaciones para la configuración con registros de diagnóstico:
  
  * Los Centros de eventos para una categoría de registros de diagnóstico se crean automáticamente al seleccionar la opción en el portal o habilitarla mediante PowerShell, por lo que debería seleccionar los Centros de eventos en el espacio de nombres del Bus de servicio con el nombre que empieza por “insights-”
  * Esta es una consulta de Análisis de transmisiones de ejemplo que puede utilizar para analizar simplemente todos los datos de registro en una tabla de PowerBI:

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

* **Creación de una plataforma personalizada de registro y telemetría** : si ya tiene una plataforma de telemetría personalizada o está pensando en crear una, la gran escalabilidad en cuanto a la suscripción y la publicación de los Centros de eventos permite introducir registros de diagnóstico de manera flexible. [Consulte la guía de Dan Rosanova para usar Centros de eventos en una plataforma de telemetría de escala global aquí](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Habilitación de la transmisión de registros de diagnóstico
Puede habilitar el streaming de registros de diagnóstico mediante programación, a través del portal o mediante la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx). En cualquier caso, si elige un espacio de nombres del Bus de servicio, se crea un Centro de eventos en el espacio de nombres para cada categoría de registro que habilita. Un **categoría de registro** de diagnóstico es un tipo de registro que un recurso puede recopilar. Puede seleccionar qué categorías de registro desea recopilar para un recurso determinado en la hoja Diagnósticos del Portal de Azure.

![Categorías de registro en el Portal](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [!WARNING]
> La habilitación y streaming de registros de diagnóstico desde recursos de proceso (por ejemplo, máquinas virtuales o Service Fabric) [requiere ejecutar una serie de pasos distinta](../event-hubs/event-hubs-streaming-azure-diags-data.md).
> 
> 

El espacio de nombres del centro de eventos o el bus de servicio no tiene que estar en la misma suscripción que la que del recurso que emite los registros, siempre que el usuario que configura la configuración tenga acceso RBAC adecuado a ambas suscripciones.

### <a name="via-powershell-cmdlets"></a>Mediante cmdlets de PowerShell
Para habilitar el streaming mediante [cmdlets de Azure PowerShell](insights-powershell-samples.md), puede utilizar el cmdlet `Set-AzureRmDiagnosticSetting` con estos parámetros:

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

El identificador de regla del Bus de servicio es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`, por ejemplo, `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`.

### <a name="via-azure-cli"></a>Mediante la CLI de Azure
Para habilitar el streaming mediante la [CLI de Azure](insights-cli-samples.md), puede utilizar el comando `insights diagnostic set` del modo siguiente:

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Utilice el mismo formato para el identificador de regla del Bus de servicio, como se explicó para el cmdlet de PowerShell.

### <a name="via-azure-portal"></a>Mediante el Portal de Azure
Para habilitar el streaming a través del Portal de Azure, vaya a la configuración de diagnósticos de un recurso y seleccione 'Exportar a Centro de eventos'.

![Exportar a Centros de eventos en el Portal](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

Para configurarlo, seleccione un espacio de nombres del Bus de servicio existente. En el espacio de nombres seleccionado será donde se creen Centros de eventos (si es la primera vez que transmite registros de diagnóstico) o a donde se transmitan (si ya hay recursos que estén transmitiendo esa categoría de registro a este espacio de nombres); la directiva define los permisos que tiene el mecanismo de streaming. En la actualidad, para transmitir a un Centro de eventos, se necesitan permisos de administración, lectura y envío. Puede crear o modificar las directivas de acceso compartido del espacio de nombres del Bus de servicio en el Portal clásico en la pestaña "Configurar" para el espacio de nombres del Bus de servicio. Para actualizar una de estas opciones de diagnóstico, el cliente debe tener el permiso ListKey en la regla de autorización del Bus de servicio.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>¿Cómo se consumen los datos de registro procedentes de Centros de eventos?
Estos son datos de salida de ejemplo de los Centros de eventos:

```
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Nombre del elemento | Description |
| --- | --- |
| records |Matriz de todos los eventos de registro de esta carga. |
| Twitter en tiempo |Hora a la que se produjo el error. |
| categoría |Categoría de registro para este evento. |
| resourceId |Id. de recurso del recurso que generó este evento. |
| operationName |Nombre de la operación. |
| level |Opcional. Indica el nivel de registro de eventos. |
| propiedades |Propiedades del evento. |

Puede ver una lista de todos los proveedores de recursos que admiten el streaming al Centro de eventos [aquí](monitoring-overview-of-diagnostic-logs.md).

## <a name="stream-data-from-compute-resources"></a>Transmisión de datos de Recursos de proceso
También puede transmitir los registros de diagnóstico de Recursos de proceso mediante el agente de Windows Azure Diagnositcs. [Consulte este artículo](../event-hubs/event-hubs-streaming-azure-diags-data.md) para ver cómo configurarlo.

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre los registros de Diagnósticos de Azure](monitoring-overview-of-diagnostic-logs.md)
* [Introducción a los Centros de eventos](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)




<!--HONumber=Dec16_HO2-->


