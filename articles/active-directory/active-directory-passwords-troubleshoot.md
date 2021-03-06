---
title: "Solución de problemas: SSPR de Azure AD | Microsoft Docs"
description: "Solución de problemas de autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 963749bce0a84a97a0938f5531ebf7d694a3ca58
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017

---

# <a name="how-to-troubleshoot-self-service-password-reset"></a>Cómo solucionar problemas de autoservicio de restablecimiento de contraseña

Si tiene problemas con el autoservicio de restablecimiento de contraseña, los elementos siguientes pueden ayudarle a ponerse a trabajar rápidamente.

## <a name="troubleshoot-password-reset-configuration-in-the-azure-portal"></a>Solución de problemas de configuración de restablecimiento de contraseña en el portal de Azure

| **Error** | Solución |
| --- | --- |
| No veo la sección **Restablecimiento de contraseña** en Azure AD en el portal de Azure | Este problema puede ocurrir si no tiene una licencia de Azure AD Básico o Premium asignada al administrador que realiza la operación. <br> Para solucionarlo, puede asignar una licencia a la cuenta del administrador en cuestión siguiendo los pasos del artículo [Asignación, comprobación y solución de problemas de licencias](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses). |
| No veo una opción de configuración determinada | Muchos elementos de la interfaz de usuario están ocultos hasta que se necesitan. Pruebe a habilitar todas las opciones que desee ver. |
| No veo la pestaña **Integración local** | Esta opción solo está visible si ha descargado AD Connect y configurado la escritura diferida de contraseñas. Para más información sobre este tema, consulte el artículo [Introducción a Azure AD Connect mediante la configuración rápida](./connect/active-directory-aadconnect-get-started-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Solución de problemas de informe de restablecimiento de contraseña

| **Error** | Solución |
| --- | --- |
| No veo que aparezca ningún tipo de actividad de administración de contraseñas en la categoría de evento de auditoría **Administración de contraseñas de autoservicio** | Este problema puede ocurrir si no tiene una licencia de Azure AD Básico o Premium asignada al administrador que realiza la operación. <br> Para solucionar este problema, se puede asignar una licencia a la cuenta del administrador en cuestión mediante el artículo [Asignación, comprobación y solución de problemas de licencias]. |
| Los registros de usuario se muestran varias veces | Actualmente, cuando un usuario se registra, anotamos cada pieza de datos individual registrada como un evento independiente. <br> Si desea agregar estos datos, puede descargar el informe y abrir los datos como una tabla dinámica de Excel para disfrutar de mayor flexibilidad.

## <a name="troubleshoot-password-reset-registration-portal"></a>Solución de problemas del portal de registro de restablecimiento de contraseña

| **Error** | Solución |
| --- | --- |
| El directorio no está habilitado para el restablecimiento de contraseña: **Su administrador no le ha permitido usar esta característica** | Cambie el indicador **Se habilitó el restablecimiento de contraseña del autoservicio** a **Un grupo** o **Todos** y haga clic en **Guardar**. |
| El usuario no tiene asignada una licencia de Azure AD Premium o Básico: **Su administrador no le ha permitido usar esta característica** | Este problema puede ocurrir si no tiene una licencia de Azure AD Básico o Premium asignada al administrador que realiza la operación. <br> Para solucionarlo, puede asignar una licencia a la cuenta del administrador en cuestión siguiendo los pasos del artículo [Asignación, comprobación y solución de problemas de licencias](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses). |
| Error al procesar la solicitud | Aunque las causas pueden ser varias, en general este error se debe a una interrupción del servicio o un problema de configuración. Si experimenta este error y afecta a su negocio, póngase en contacto con el soporte técnico de Microsoft para recibir ayuda adicional. |

## <a name="troubleshoot-password-reset-portal"></a>Solución de problemas del portal de restablecimiento de contraseña

| **Error** | Solución |
| --- | --- |
| El directorio no está habilitado para el restablecimiento de contraseñas | Cambie el indicador **Se habilitó el restablecimiento de contraseña del autoservicio** a **Un grupo** o **Todos** y haga clic en **Guardar**. |
| El usuario no tiene asignada una licencia de Azure AD Premium o Básico | Este problema puede ocurrir si no tiene una licencia de Azure AD Básico o Premium asignada al administrador que realiza la operación. <br> Para solucionarlo, puede asignar una licencia a la cuenta del administrador en cuestión siguiendo los pasos del artículo [Asignación, comprobación y solución de problemas de licencias](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses). |
| El directorio está habilitado para restablecer la contraseña, pero falta la información de autenticación del usuario o es incorrecta | Asegúrese de que el usuario ha formado correctamente los datos de contacto en el archivo del directorio antes de continuar. Para más información sobre este tema, consulte el artículo [Datos usados en el autoservicio de restablecimiento de contraseña de Azure AD](active-directory-passwords-data.md). |
| El directorio está habilitado para restablecer la contraseña, pero un usuario tiene sólo una parte de los datos de contacto en el archivo cuando la directiva está configurada para requerir dos pasos de comprobación | Asegúrese de que el usuario tenga al menos dos métodos de contacto configurados correctamente (por ejemplo, teléfono móvil **y** teléfono del trabajo) antes de continuar. |
| El directorio está habilitado para restablecer la contraseña y el usuario está correctamente configurado, pero no es posible ponerse en contacto con él | Puede deberse a un error de servicio temporal o a datos de contacto configurados incorrectamente que no hemos podido detectar como es debido. <br> <br> Si el usuario espera 10 segundos, vuelve a intentarlo y aparece el vínculo "Póngase en contacto con el administrador". Al hacer clic en Intentar de nuevo se reintenta la llamada; sin embargo, si hace clic en "Póngase en contacto con el administrador", se envía a los administradores un correo electrónico de formulario para solicitarles que realicen el restablecimiento de contraseña para esa cuenta de usuario. |
| El usuario nunca recibe la llamada de teléfono o el SMS de restablecimiento de contraseña | Esto puede ser el resultado de un número de teléfono incorrecto en el directorio. Asegúrese de que el número de teléfono tiene el formato "+ccc xxxyyyzzzzXeeee". <br> <br> El restablecimiento de contraseña no admite extensiones, aunque especifique una en el directorio (se quitan antes de enviar la llamada). Pruebe a utilizar un número sin una extensión o a integrar la extensión en el número de teléfono en su PBX. |
| El usuario nunca recibe correo electrónico de restablecimiento de contraseña | La causa más común para este problema es que un filtro de correo no deseado rechaza el mensaje. Compruebe la carpeta de elementos eliminados o correo no deseado para comprobar si ha recibido el correo. <br> Asegúrese también de que comprueba el correo electrónico correcto del mensaje. |
| He configurado una directiva para restablecer la contraseña, pero cuando una cuenta de administrador usa el restablecimiento de contraseña, no se aplica esa directiva | Microsoft administra y controla la directiva de restablecimiento de contraseña de administrador para garantizar el nivel de seguridad más alto. |
| Se impide que el usuario trate de restablecer la contraseña demasiadas veces al día | Implementamos un mecanismo de limitación automático para evitar que los usuarios intenten restablecer sus contraseñas demasiadas veces en un breve período de tiempo. Esto ocurre cuando: <br> 1. Un usuario intenta validar un número de teléfono 5 veces en una hora. <br> 2. Un usuario intenta utilizar la puerta de preguntas de seguridad 5 veces en una hora. <br> 3. Un usuario intenta restablecer la contraseña de la misma cuenta de usuario 5 veces en una hora. <br> Para solucionar este problema, indique al usuario que espere 24 horas después del último intento, y el usuario podrá restablecer su contraseña. |
| El usuario experimenta un error al validar su número de teléfono | Este error se produce cuando el número de teléfono especificado no coincide con el número de teléfono archivado. Asegúrese de que el usuario escribe el número de teléfono completo, incluido el código de área y país, al intentar utilizar un método basado en teléfono para restablecer la contraseña. |
| Error al procesar la solicitud | Aunque las causas pueden ser varias, en general este error se debe a una interrupción del servicio o un problema de configuración. Si experimenta este error y afecta a su negocio, póngase en contacto con el soporte técnico de Microsoft para recibir ayuda adicional. |

## <a name="troubleshoot-password-writeback"></a>Solución de problemas de escritura diferida de contraseñas

| **Error** | Solución |
| --- | --- |
| El servicio de restablecimiento de contraseña no se inicia de forma local con el error 6800 en el registro de eventos de la aplicación de la máquina de Azure AD Connect. <br> <br> Después de la incorporación, los usuarios federados o con sincronización de hash de contraseña no pueden restablecer sus contraseñas. | Cuando está habilitada la escritura diferida de contraseñas, el motor de sincronización llama a la biblioteca de escritura diferida para realizar la configuración (incorporación) hablando con el servicio de incorporación de la nube. Los errores detectados durante la incorporación o al iniciar el punto de conexión de WCF para la escritura diferida de contraseñas producirán errores en el registro de eventos de la máquina de Azure AD Connect. <br> <br> Durante el reinicio del servicio ADSync, si se configuró la escritura diferida, se inicia el punto de conexión de WCF. Sin embargo, si se produce un error en el inicio del punto de conexión, se registra el evento 6800 y se deja que se inicie el servicio de sincronización. La presencia de este evento significa que el extremo de la escritura diferida de contraseñas no se ha iniciado. Los detalles de registro de eventos para este evento (6800) junto con las entradas de registro de eventos generadas por el componente PasswordResetService indicarán porqué el punto de conexión no se ha podido iniciar. Revise estos errores de registro de eventos e intente volver a iniciar Azure AD Connect si la escritura diferida de contraseñas sigue sin funcionar. Si el problema persiste, intente deshabilitar y volver a habilitar la escritura diferida de contraseñas.
| Cuando un usuario intenta restablecer una contraseña o desbloquear una cuenta con la escritura diferida de contraseñas habilitada, se produce un error en la operación. <br> <br> Además, verá un evento en el registro de eventos de Azure AD Connect que contiene: “Synchronization Engine returned an error hr=800700CE, message=The filename or extension is too long” (El motor de sincronización devolvió un error hr=800700CE, mensaje=El nombre de archivo o la extensión es demasiado largo) después de la operación de desbloqueo. | Busque la cuenta de Active Directory para Azure AD Connect y restablezca la contraseña para que no contenga más de 127 caracteres. Después, abra el Servicio de sincronización desde el menú Inicio. Vaya a Conectores y busque el Conector Active Directory. Selecciónelo y haga clic en Propiedades. Vaya a la página Credenciales y escriba la nueva contraseña. Seleccione Aceptar para cerrar la página. |
| En el último paso del proceso de instalación de Azure AD Connect, se generará un error que indica que no se ha podido configurar la escritura diferida de contraseñas. <br> <br> El registro de eventos de la aplicación para Azure AD Connect contiene el error 32009 con el texto "Error al obtener el token de autorización". | Este error se produce en los dos casos siguientes:<br> <br> a. Ha especificado una contraseña incorrecta para la cuenta de administrador global especificada al principio del proceso de instalación de Azure AD Connect.<br> b. Ha tratado de usar un usuario federado para la cuenta de administrador global especificada al principio del proceso de instalación de Azure AD Connect.<br> <br> Para corregir este error, asegúrese de que no esté usando una cuenta federada para el administrador global que especificó al principio del proceso de instalación de Azure AD Connect y que la contraseña especificada sea correcta. |
| El registro de eventos del equipo de Azure AD Connect contiene el error 32002 producido por PasswordResetService. <br> <br> El error dice: "Error al conectarse al Bus de servicio, el proveedor de tokens no pudo proporcionar un token de seguridad..." | El entorno local no es capaz de conectarse al punto de conexión del bus de servicio en la nube. Este error se debe normalmente a una regla de firewall que bloquea una conexión saliente a una dirección de puerto o web determinada. Consulte [Requisitos de red](active-directory-passwords-how-it-works.md#network-requirements) para más información. Una vez que haya actualizado estas reglas, reinicie el equipo de Azure AD Connect y la escritura diferida de contraseñas debería empezar a funcionar de nuevo. |
| Después trabajar durante algún tiempo, los usuarios federados o con sincronización de hash de contraseña no pueden restablecer las contraseñas. | En algunos casos excepcionales, el servicio de escritura diferida de contraseñas puede no volver a iniciarse cuando se reinicia Azure AD Connect. En estos casos, en primer lugar, compruebe si la escritura diferida de contraseñas está habilitada en el entorno local. Esto puede hacerse mediante el Asistente de Azure AD Connect o PowerShell (consulte la sección anterior sobre los procedimientos). Si la característica parece estar habilitada, pruebe a habilitar o deshabilitar la característica de nuevo a través de la interfaz de usuario o de PowerShell. Si esto no funciona, pruebe a desinstalar por completo y volver a instalar Azure AD Connect. |
| Los usuarios federados o con sincronización de hash de contraseña que intenten restablecer su contraseña obtienen un error después de enviar la contraseña que indica que se ha producido un problema de servicio. <br ><br> Además, durante las operaciones de restablecimiento de contraseña, verá un error relacionado con que al agente de administración se le denegó el acceso a los registros de eventos locales. | Si ve estos errores en el registro de eventos, confirme que la cuenta de AD MA (que se especificó en el asistente en el momento de la configuración) tiene los permisos necesarios para la escritura diferida de contraseñas. <br> <br> **Una vez que se concede este permiso, puede tardar hasta una hora en poder usarse a través de la tarea en segundo plano sdprop en el controlador de dominio.** <br> <br> Para que el restablecimiento de contraseña funcione, el permiso debe quedar marcado en el descriptor de seguridad del objeto de usuario cuya contraseña se está restableciendo. Hasta que este permiso se muestra en el objeto de usuario, el restablecimiento de contraseña seguirá generando un error con acceso denegado. |
| Los usuarios federados o con sincronización de hash de contraseña que intenten restablecer su contraseña obtienen un error después de enviar la contraseña que indica que se ha producido un problema de servicio. <br> <br> Además, durante las operaciones de restablecimiento de contraseña, puede obtener un error en los registros de eventos del servicio Azure AD Connect que indique un error “No se encontró el objeto”. | Este error suele indicar que el motor de sincronización no puede encontrar el objeto de usuario en el espacio del conector de AAD o en el objeto de espacio del conector MV o AD vinculado. <br> <br> Para solucionar este problema, asegúrese de que el usuario realmente está sincronizado desde el entorno local a AAD a través de la instancia actual de Azure AD Connect e inspeccione el estado de los objetos de los espacios de conector y MV. Confirme que el objeto de AD CS es conector al objeto MV a través de la regla "Microsoft.InfromADUserAccountEnabled.xxx".|
| Los usuarios federados o con sincronización de hash de contraseña que intenten restablecer su contraseña obtienen un error después de enviar la contraseña que indica que se ha producido un problema de servicio. <br> <br> Además, durante las operaciones de restablecimiento de contraseña, puede obtener un error en los registros de eventos del servicio Azure AD Connect que indique un error "Varias coincidencias detectadas". | Esto indica que el motor de sincronización ha detectado que el objeto de MV está conectado a más de un objeto de AD CS a través de "Microsoft.InfromADUserAccountEnabled.xxx". Esto significa que el usuario tiene una cuenta habilitada en más de un bosque. **Actualmente este escenario no se admite para la escritura diferida de contraseñas.** |
| Error de configuración en las operaciones con contraseñas. El registro de eventos de aplicación contiene: <br> <br> El error 6329 de Azure AD Connect con el texto: 0x8023061f (error en la operación porque no está habilitada la sincronización de contraseñas en este agente de administración). | Esto ocurre si se cambia la configuración de Azure AD Connect para agregar un nuevo bosque de AD (o para quitar y volver a agregar un bosque existente) después de que ya se haya habilitado la escritura diferida de contraseñas. Se producirá un error en las operaciones de contraseñas para los usuarios de estos bosques recién agregados. Para corregir el problema, deshabilite y vuelva a habilitar la característica de escritura diferida de contraseñas después de que se hayan completado los cambios de configuración del bosque. |

## <a name="password-writeback-event-log-error-codes"></a>Códigos de error de registro de eventos de escritura diferida de contraseñas

Una práctica recomendada para solucionar problemas con la escritura diferida de contraseñas consiste en inspeccionar ese registro de eventos de la aplicación en el equipo de Azure AD Connect. Este registro de eventos contiene eventos de dos orígenes de interés para la escritura diferida de contraseñas. El origen PasswordResetService describe las operaciones y los problemas relacionados con el funcionamiento de la escritura diferida de contraseñas. El origen ADSync describe las operaciones y los problemas relacionados con la configuración de contraseñas en el entorno de AD.

### <a name="source-of-event-is-adsync"></a>El origen del evento es ADSync

| Código | Nombre/mensaje | Descripción |
| --- | --- | --- |
| 6329 | BAIL: MMS(4924) 0x80230619 –: "Una restricción impide que la contraseña se modifique por la que ha especificado actualmente". | Este evento se produce cuando el servicio de escritura diferida de contraseñas intenta establecer una contraseña en su directorio local que no cumple la duración de la contraseña, el historial, la complejidad o los requisitos de filtrado del dominio. <br> <br> Si tiene una vigencia mínima de contraseña y ha cambiado recientemente la contraseña desde la ventana de tiempo, no podrá volver a cambiarla hasta que alcance la duración especificada en el dominio. Con fines de prueba, la duración mínima debe establecerse en 0. <br> <br> Si tiene los requisitos del historial de contraseña habilitados, debe seleccionar una contraseña que no se haya utilizado en las últimas N veces, donde N es la configuración de la duración de la contraseña. Si selecciona una contraseña que se ha usado en las últimas N veces, verá un error en este caso. Con fines de prueba, el historial mínimo debe establecerse en 0. <br> <br> Si tiene requisitos de complejidad de contraseña, todos ellos se aplican cuando el usuario intenta cambiar o restablecer una contraseña. <br> <br> Si tiene habilitados filtros de contraseña y un usuario selecciona una contraseña que no cumple los criterios de filtrado, se producirá un error en la operación de restablecimiento o modificación. |
| HR 8023042 | El motor de sincronización devolvió un error hr=80230402, mensaje=Error al intentar obtener un objeto debido a que hay entradas duplicadas con el mismo delimitador | Este evento se produce cuando se habilita el mismo identificador de usuario en varios dominios. Por ejemplo, si se están sincronizando los bosques de cuentas y recursos y tienen el mismo identificador de usuario presente y habilitado en cada uno, es posible que se produzca este error. <br> <br> Este error también puede producirse si usa un atributo delimitador que no sea único (como un alias o UPN) y dos usuarios comparten el mismo atributo delimitador. <br> <br> Para resolver este problema, asegúrese de no tener ningún usuario duplicado dentro de los dominios y de estar utilizando un atributo de delimitador único para cada usuario. |

### <a name="source-of-event-is-passwordresetservice"></a>El origen del evento es PasswordResetService

| Código | Nombre/mensaje | Descripción |
| --- | --- | --- |
| 31001 | PasswordResetStart | Este evento indica que el servicio local detectó una solicitud de restablecimiento de contraseña para un usuario federado o con sincronización de hash de contraseña originada desde la nube. Este evento es que el primer evento en cada operación de escritura diferida de contraseñas. |
| 31002 | PasswordResetSuccess | Este evento indica que un usuario selecciona una nueva contraseña durante una operación de restablecimiento de contraseña, determinamos que esta contraseña cumple los requisitos de contraseñas corporativos y que esa contraseña se ha escrito en diferido correctamente en el entorno de AD local. |
| 31003 | PasswordResetFail | Este evento indica que un usuario selecciona una contraseña y que la contraseña ha llegado correctamente al entorno local, pero al intentar establecer la contraseña en el entorno de AD local, se produjo un error. Esto puede ocurrir por varios motivos: <br> <br> La contraseña el usuario no cumple los requisitos de antigüedad, historial, complejidad o filtro del dominio. Pruebe una contraseña completamente nueva para solucionar el problema. <br> <br> La cuenta de servicio del agente de administración no tiene los permisos adecuados para establecer la nueva contraseña en la cuenta de usuario en cuestión. <br> <br> La cuenta de usuario está en un grupo protegido, como administradores del dominio o de empresa, lo que impide la ejecución de operaciones de conjunto de contraseñas. |
| 31004 | OnboardingEventStart | Este evento se produce si habilita la escritura diferida de contraseñas con Azure AD Connect e indica que se inició la incorporación en su organización del servicio web de escritura diferida de contraseñas. |
| 31005 | OnboardingEventSuccess | Este evento indica que el proceso de incorporación se realizó correctamente y que la capacidad de escritura diferida de contraseñas está lista para usarse. |
| 31006 | ChangePasswordStart | Este evento indica que el servicio local ha detectado una solicitud de cambio de contraseña para un usuario federado o con sincronización de hash de contraseña originada desde la nube. Este evento es el primer evento de cada operación de escritura diferida de cambio de contraseñas. |
| 31007 | ChangePasswordSuccess | Este evento indica que un usuario selecciona una nueva contraseña durante una operación de cambio de contraseña, determinamos que esta contraseña cumple los requisitos de contraseñas corporativos y que esa contraseña se ha escrito en diferido correctamente en el entorno de AD local. |
| 31008 | ChangePasswordFail | Este evento indica que un usuario selecciona una contraseña y que la contraseña ha llegado correctamente al entorno local, pero al intentar establecer la contraseña en el entorno de AD local, se produjo un error. Esto puede ocurrir por varios motivos: <br> <br> La contraseña el usuario no cumple los requisitos de antigüedad, historial, complejidad o filtro del dominio. Pruebe una contraseña completamente nueva para solucionar el problema. <br> <br> La cuenta de servicio del agente de administración no tiene los permisos adecuados para establecer la nueva contraseña en la cuenta de usuario en cuestión. <br> <br> La cuenta de usuario está en un grupo protegido, como administradores del dominio o de empresa, lo que impide la ejecución de operaciones de conjunto de contraseñas. |
| 31009 | ResetUserPasswordByAdminStart | El servicio local ha detectado una solicitud de restablecimiento de contraseña para un usuario federado o con sincronización de hash de contraseña originada por el administrador local en nombre de un usuario. Este evento es el primero en una operación de escritura diferida de restablecimiento de contraseña iniciada por el administrador. |
| 31010 | ResetUserPasswordByAdminSuccess | El administrador ha seleccionado una nueva contraseña durante una operación de restablecimiento de contraseña iniciada por el administrador, determinamos que esta contraseña cumple los requisitos de contraseñas corporativos y que esa contraseña se ha escrito en diferido correctamente en el entorno de AD local. |
| 31011 | ResetUserPasswordByAdminFail | El administrador ha seleccionado una contraseña en nombre de un usuario y que la contraseña ha llegado correctamente al entorno local, pero al intentar establecer la contraseña en el entorno de AD local, se produjo un error. Esto puede ocurrir por varios motivos: <br> <br> La contraseña el usuario no cumple los requisitos de antigüedad, historial, complejidad o filtro del dominio. Pruebe una contraseña completamente nueva para solucionar el problema. <br> <br> La cuenta de servicio del agente de administración no tiene los permisos adecuados para establecer la nueva contraseña en la cuenta de usuario en cuestión. <br> <br> La cuenta de usuario está en un grupo protegido, como administradores del dominio o de empresa, lo que impide la ejecución de operaciones de conjunto de contraseñas. |
| 31012 | OffboardingEventStart | Este evento se produce si habilita la escritura diferida de contraseñas con Azure AD Connect e indica que se inició la externalización en su organización del servicio web de escritura diferida de contraseñas. |
| 31013| OffboardingEventSuccess| Este evento indica que el proceso de externalización se realizó correctamente y que la capacidad de escritura diferida de contraseñas se ha deshabilitado correctamente. |
| 31014| OffboardingEventFail| Este evento indica que el proceso de externalización no se realizó correctamente. Esto podría deberse a un error de permisos en la cuenta de administrador de la nube o local especificada durante la configuración, o porque está intentando utilizar un administrador global de nube federado al deshabilitar la escritura diferida de contraseñas. Para solucionar este problema, compruebe los permisos administrativos y no se utilice ninguna cuenta federada al configurar la capacidad de escritura federada de contraseñas.|
| 31015| WriteBackServiceStarted| Este evento indica que el servicio de escritura diferida de contraseñas se inició correctamente y está listo para aceptar las solicitudes de administración de contraseñas desde la nube.|
| 31016| WriteBackServiceStopped| Este evento indica que el servicio de escritura diferida de contraseñas se ha detenido y que las solicitudes de administración de contraseñas desde la nube no se realizarán correctamente.|
| 31017| AuthTokenSuccess| Este evento indica que se ha recuperado correctamente un token de autorización para el administrador global especificado durante la instalación de Azure AD Connect para iniciar el proceso de incorporación o externalización.|
| 31018| KeyPairCreationSuccess| Este evento indica que se ha creado correctamente la clave de cifrado de contraseña que se usa para cifrar las contraseñas de la nube para enviarlas al entorno local.|
| 32000| UnknownError| Este evento indica un error desconocido durante una operación de administración de contraseñas. Observe el texto de excepción en el evento para obtener más detalles. Si tiene problemas, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas. Si esto no soluciona el problema, incluya una copia del registro de eventos junto con el identificador de seguimiento especificado privilegiado a su ingeniero de soporte técnico.|
| 32001| ServiceError| Este evento indica que se ha producido un error de conexión al servicio de restablecimiento de contraseña en la nube y suele producirse cuando el servicio local no se ha podido conectar con el servicio web de restablecimiento de contraseñas.|
| 32002| ServiceBusError| Este evento indica que se ha producido un error al conectarse a la instancia de Bus de servicio del inquilino. Esto puede deberse a que está bloqueando las conexiones salientes en el entorno local. Compruebe el firewall para garantizar que permite conexiones a través de TCP 443 y a https://ssprsbprodncu-sb.accesscontrol.windows.net/, e inténtelo de nuevo. Si tiene problemas, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas.|
| 32003| InPutValidationError| Este evento indica que la entrada transferida a la API del servicio web no era válida. Vuelva a intentarlo.|
| 32004| DecryptionError| Este evento indica que se ha producido un error al descifrar la contraseña que ha llegado de la nube. Esto puede deberse a una falta de coincidencia de la clave de descifrado entre el servicio en la nube y su entorno local. Para resolver este problema, deshabilite y vuelva a habilitar la escritura diferida de contraseñas en su entorno local.|
| 32005| ConfigurationError| Durante la incorporación, guardamos la información específica del inquilino en un archivo de configuración en su entorno local. Este evento indica que se ha producido un error al guardar este archivo o que cuando se inició el servicio había un error en la lectura del archivo. Para corregir este problema, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas para forzar la escritura diferida de contraseñas de este archivo de configuración.|
| 32007| OnBoardingConfigUpdateError| Durante la incorporación, enviamos datos de la nube al servicio de restablecimiento de contraseñas local. Esos datos, a continuación, se escriben en un archivo en memoria antes de enviarse al servicio de sincronización para almacenar esta información de forma segura en el disco. Este evento indica un problema con escribir o actualizar datos en la memoria. Para corregir este problema, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas para forzar la escritura diferida de contraseñas de esta configuración.|
| 32008| ValidationError| Este evento indica que hemos recibido una respuesta no válida desde el servicio web de restablecimiento de contraseña. Para solucionar este problema, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas.|
| 32009| AuthTokenError| Este evento indica que no se ha podido obtener un token de autorización para la cuenta de administrador global especificada durante la instalación de Azure AD Connect. Este error puede deberse a un nombre de usuario o contraseña especificados para la cuenta de administrador global o porque se federa la cuenta de administrador global especificada. Para corregir este problema, vuelva a ejecutar la configuración con el nombre de usuario correcto y la contraseña y asegúrese de que el administrador es una cuenta administrada (solo en la nube o con la sincronización de contraseñas).|
| 32010| CryptoError| Este evento indica que se produjo un error al generar la clave de cifrado de contraseña o de descifrado de una contraseña que llega desde el servicio en la nube. Este error probablemente indica un problema con su entorno. Consulte los detalles de su registro de eventos para obtener más información y resolver este problema. También puede intentar deshabilitar y volver a habilitar el servicio de escritura diferida de contraseñas para resolver este problema.|
| 32011| OnBoardingServiceError| Este evento indica que el servicio local no pudo comunicarse correctamente con el servicio web de restablecimiento de contraseña para iniciar el proceso de incorporación. Esto puede deberse a una regla de firewall o un problema al obtener un token de autenticación para el inquilino. Para solucionar este problema, asegúrese de que no esté bloqueando las conexiones salientes sobre TCP 443 y TCP 9350 a 9354 o a https://ssprsbprodncu-sb.accesscontrol.windows.net/, y que la cuenta de administrador de AAD usada para la incorporación no esté federada.|
| 32013| OffBoardingError| Este evento indica que el servicio local no pudo comunicarse correctamente con el servicio web de restablecimiento de contraseña para iniciar el proceso de externalización. Esto puede deberse a una regla de firewall o un problema al obtener un token de autenticación para el inquilino. Para solucionar este problema, asegúrese de que no esté bloqueando las conexiones salientes sobre TCP 443 o a https://ssprsbprodncu-sb.accesscontrol.windows.net/, y que la cuenta de administrador de AAD utilizada para la externalización no esté federada.|
| 32014| ServiceBusWarning| Este evento indica que tenemos que tratar de volver a establecer la conexión a la instancia de Bus de servicio del inquilino. En condiciones normales, esto no debe ser un problema, pero si este evento se produce muchas veces, considere la posibilidad de comprobar la conexión de red al bus de servicio, especialmente si se trata de una conexión de ancho de banda bajo o una latencia alta.|
| 32015| ReportServiceHealthError| Para supervisar el estado de su servicio de escritura diferida de contraseñas, enviamos datos de latido a nuestro servicio web de restablecimiento de contraseñas cada cinco minutos. Este evento indica que se ha producido un error al enviar esta información de latido al servicio web en la nube. Esta información de estado no incluye datos OII o PII y se trata únicamente de un latido y estadísticas de servicio básicas para que podamos ofrecer información de estado de servicio en la nube.|
| 33001| ADUnKnownError| Este evento indica que se ha producido un error desconocido devuelto por AD; compruebe el registro de eventos de servidor de Azure AD Connect para eventos del origen de ADSync para obtener más información acerca de este error.|
| 33002| ADUserNotFoundError| Este evento indica que el usuario que está intentando restablecer o cambiar una contraseña no se encontró en el directorio local. Esto puede ocurrir cuando el usuario se ha eliminado en el entorno local pero no en la nube, o si hay un problema con la sincronización. Compruebe los registros de sincronización, así como los últimos detalles de ejecución de la sincronización para obtener más información.|
| 33003| ADMutliMatchError| Si una solicitud de restablecimiento o cambio de contraseña se origina desde la nube, usamos el delimitador de la nube especificado durante el proceso de configuración de Azure AD Connect para determinar cómo vincular tal solicitud de nuevo a un usuario en el entorno local. Este evento indica que hemos encontrado dos usuarios en el directorio local con el mismo atributo delimitador de la nube. Compruebe los registros de sincronización, así como los últimos detalles de ejecución de la sincronización para obtener más información.|
| 33004| ADPermissionsError| Este evento indica que la cuenta de servicio del agente de administración no tiene los permisos adecuados en la cuenta en cuestión para establecer una nueva contraseña. Asegúrese de que la cuenta del agente de administración en el bosque del usuario tiene los permisos de restablecimiento y cambio de contraseña en todos los objetos del bosque. Para más información sobre cómo hacerlo, consulte el Paso 4: configurar los permisos adecuados de Active Directory.|
| 33005| ADUserAccountDisabled| Este evento indica que hemos intentado restablecer o cambiar una contraseña de una cuenta deshabilitada en el entorno local. Habilite la cuenta y vuelva a intentarlo.|
| 33006| ADUserAccountLockedOut| Este evento indica que hemos intentado restablecer o cambiar una contraseña de una cuenta bloqueada en el entorno local. Los bloqueos se aplican cuando un usuario intenta modificar o restablecer la contraseña demasiadas veces en poco tiempo. Desbloquee la cuenta y vuelva a intentarlo.|
| 33007| ADUserIncorrectPassword| Este evento indica que el usuario ha especificado una contraseña incorrecta actual al realizar una operación de cambio de contraseña. Especifique la contraseña actual correcta e inténtelo de nuevo.|
| 33008| ADPasswordPolicyError| Este evento se produce cuando el servicio de escritura diferida de contraseñas intenta establecer una contraseña en su directorio local que no cumple la duración de la contraseña, el historial, la complejidad o los requisitos de filtrado del dominio. <br> <br> Si tiene una vigencia mínima de contraseña y ha cambiado recientemente la contraseña desde la ventana de tiempo, no podrá volver a cambiarla hasta que alcance la duración especificada en el dominio. Con fines de prueba, la duración mínima debe establecerse en 0. <br> <br> Si tiene los requisitos del historial de contraseña habilitados, debe seleccionar una contraseña que no se haya utilizado en las últimas N veces, donde N es la configuración de la duración de la contraseña. Si selecciona una contraseña que se ha usado en las últimas N veces, verá un error en este caso. Con fines de prueba, el historial mínimo debe establecerse en 0. <br> <br> Si tiene requisitos de complejidad de contraseña, todos ellos se aplican cuando el usuario intenta cambiar o restablecer una contraseña. <br> <br> Si tiene habilitados filtros de contraseña y un usuario selecciona una contraseña que no cumple los criterios de filtrado, se producirá un error en la operación de restablecimiento o modificación.|
| 33009| ADConfigurationError| Este evento indica que se ha producido un problema al escribir la contraseña en diferido en el directorio local por un problema de configuración con Active Directory. Compruebe el registro de eventos de la aplicación del equipo de Azure AD Connect para ver los mensajes del servicio ADSync, a fin de obtener más información sobre el error que se ha producido.|


## <a name="troubleshoot-password-writeback-connectivity"></a>Solución de problemas con la conectividad de la escritura diferida de contraseñas

Si se producen interrupciones del servicio con el componente de escritura diferida de contraseñas de Azure AD Connect, aquí tiene algunos pasos que puede seguir para resolver este problema:

* [Reinicio del servicio de sincronización de Azure AD Connect](#restart-the-azure-ad-connect-sync-service)
* [Deshabilitar y volver a habilitar la característica de escritura diferida de contraseña](#disable-and-re-enable-the-password-writeback-feature)
* [Instalación de la última versión de Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Solución de problemas de escritura diferida de contraseñas](#troubleshoot-password-writeback)

En general, se recomienda que ejecute estos pasos en el orden anterior con el fin de recuperar el servicio de la manera más rápida.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Reinicio del servicio de sincronización de Azure AD Connect

Reiniciar el servicio de sincronización de Azure AD Connect puede ayudar a solucionar problemas de conectividad u otros problemas transitorios con el servicio.

1. Como administrador, haga clic en **Iniciar** en el servidor que ejecuta **Azure AD Connect**.
2. Escriba **"services.msc"** en el cuadro de búsqueda y presione **ENTRAR**.
3. Busque la entrada **Microsoft Azure AD Sync**.
4. Haga clic con el botón secundario en la entrada del servicio, haga clic en **Reiniciar**y espere a que se complete la operación.

   ![Reinicio del servicio Azure AD Sync][Service Restart]

Estos pasos restablecen la conexión con el servicio en la nube y resuelven las interrupciones que pueda experimentar. Si reiniciar el servicio de sincronización no resuelve el problema, se recomienda que intente deshabilitar y volver a habilitar la característica de escritura diferida de contraseñas en el siguiente paso.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Deshabilitar y volver a habilitar la característica de escritura diferida de contraseña

Deshabilitar y volver a habilitar la característica de escritura diferida de contraseñas puede ayudar a resolver problemas de conectividad.

1. Como administrador, abra el **Asistente de configuración de Azure AD Connect**.
2. En el cuadro de diálogo **Conectarse a Azure AD**, escriba las **credenciales de administrador global de Azure AD**.
3. En el cuadro de diálogo **Conectarse a AD DS**, escriba las **credenciales de administrador de Servicios de dominio de AD**.
4. En el cuadro de diálogo **Identificación de forma exclusiva de usuarios**, haga clic en el botón **Siguiente**.
5. En el cuadro de diálogo **Características opcionales**, desactive la casilla **Escritura diferida de contraseñas**.
6. Haga clic en **Siguiente** en las páginas de diálogo restantes sin cambiar nada hasta llegar a la página **Listo para configurar**.
7. Asegúrese de que en la página de configuración aparece la **opción de escritura diferida de contraseñas como deshabilitada** y, a continuación, haga clic en el botón verde **Configurar** para confirmar los cambios.
8. En el cuadro de diálogo **Finalizado**, anule la selección de la opción **Sincronizar ahora** y, a continuación, haga clic en **Finalizar** para cerrar el asistente.
9. Vuelva a abrir el **Asistente de configuración de Azure AD Connect**.
10. **Repita los pasos del 2 al 8**, salvo que debe asegurarse de volver a **marcar la opción de escritura diferida de contraseñas** en la pantalla **Características opcionales** para volver a habilitar el servicio.

Estos pasos restablecen la conexión con el servicio en la nube y resuelven las interrupciones que pueda experimentar.

Si el problema no se soluciona después de deshabilitar y volver a habilitar la característica de escritura diferida de contraseñas, le recomendamos que vuelva a instalar Azure AD Connect en el siguiente paso.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instalación de la última versión de Azure AD Connect

Con la reinstalación de Azure AD Connect se pueden resolver los problemas de configuración y de conectividad entre nuestros servicios en la nube y su entorno local de AD.

Se recomienda realizar este paso sólo después de probar los dos primeros pasos descritos anteriormente.

> [!WARNING]
> Si ha personalizado las reglas de sincronización predefinidas, **realice una copia de seguridad de ellas antes de continuar con la actualización y, cuando haya acabado, vuelva a implementarlas manualmente**.

1. Descargue la versión más reciente de AD Connect del [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=615771).
2. Puesto que ya ha instalado Azure AD Connect, solo necesita realizar una actualización in situ para actualizar la instalación de Azure AD Connect a la versión más reciente.
3. Ejecute el paquete descargado y siga las instrucciones en pantalla para actualizar el equipo de Azure AD Connect.

Estos pasos restablecen la conexión con el servicio en la nube y resuelven las interrupciones que pueda experimentar.

Si con la instalación de la última versión del servidor de Azure AD Connect no se resuelve el problema, le recomendamos que intente deshabilitar y volver a habilitar la escritura diferida de contraseñas como último paso después de instalar la versión más reciente.

## <a name="verify-whether-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Compruebe si Azure AD Connect dispone del permiso necesario para la escritura diferida de contraseñas 
Azure AD Connect requiere el permiso de AD **restablecer contraseña** para realizar la escritura diferida de contraseñas. Para averiguar si Azure AD Connect tiene el permiso para una cuenta de usuario de AD local determinada, puede usar la característica Permiso efectivo de Windows:

1. Inicie sesión en el servidor Azure AD Connect e inicie **Synchronization Service Manager** (Inicio → Servicio Synchronization).
2. En la pestaña **Conectores**, seleccione el **conector AD** local y haga clic en **Propiedades**.  
![Permiso efectivo: paso 2](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
3. En el cuadro de diálogo emergente, seleccione la pestaña **Connect to Active Directory Forest** (Conectar con el bosque de Active Directory) y anote el valor de la propiedad **Nombre de usuario**. Se trata de la cuenta de AD DS que Azure AD Connect usa para realizar la sincronización de directorios. Para que Azure AD Connect realice la escritura diferida de contraseñas, la cuenta de AD DS debe tener permiso para restablecer la contraseña.  
![Permiso efectivo: paso 3](./media/active-directory-passwords-troubleshoot/checkpermission02.png)  
4. Inicie sesión en un controlador de dominio local e inicie la aplicación **Usuarios y equipos de Active Directory**.
5. Haga clic en **Vista** y asegúrese de que la opción **Características avanzadas** está habilitada.  
![Permiso efectivo: paso 5](./media/active-directory-passwords-troubleshoot/checkpermission03.png)  
6. Busque la cuenta de usuario de AD que desea comprobar. Haga clic con el botón derecho en la cuenta y seleccione **Propiedades**.  
![Permiso efectivo: paso 6](./media/active-directory-passwords-troubleshoot/checkpermission04.png)  
7. En el cuadro de diálogo emergente, vaya a la pestaña **Seguridad** y haga clic en **Avanzada**.  
![Permiso efectivo: paso 7](./media/active-directory-passwords-troubleshoot/checkpermission05.png)  
8. En el cuadro de diálogo emergente de configuración Seguridad avanzada, vaya a la pestaña **Acceso efectivo**.
9. Haga clic en **Seleccionar un usuario** y seleccione la cuenta de AD DS que usa Azure AD Connect (vea el paso 3). A continuación, haga clic en **Ver acceso efectivo**.  
![Permiso efectivo: paso 9](./media/active-directory-passwords-troubleshoot/checkpermission06.png)  
10. Desplácese hacia abajo y busque **Restablecer contraseña**. Si la entrada está activada, significa que la cuenta de AD DS tiene permiso para restablecer la contraseña de la cuenta de usuario de AD seleccionada.  
![Permiso efectivo: paso 10](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Foros de Azure AD

Si tiene alguna pregunta general sobre Azure AD y el autoservicio de restablecimiento de contraseña, puede pedir ayuda a la comunidad en los [foros de Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Los miembros de la comunidad están formados por ingenieros, jefes de producto, MVP y profesionales de TI.

## <a name="contact-microsoft-support"></a>Consultar al soporte técnico de Microsoft

Si no encuentra la respuesta a un problema, nuestros equipos de soporte técnico están siempre disponibles para ayudarle.

Para que reciba la ayuda correcta, le pedimos que proporcione la mayor cantidad de detalles posible al abrir una incidencia:

* **Descripción general del error**: ¿cuál es el error? ¿Qué comportamiento observó? ¿Cómo podemos reproducir el error? Proporcione tantos detalles como sea posible.
* **Página**: ¿en qué página estaba cuando se detectó el error? Incluya la dirección URL si es posible y una captura de pantalla.
* **Código de soporte técnico**: ¿qué código de soporte técnico se generó cuando el usuario vio el error? 
    * Para encontrarlo, reproduzca el error, haga clic en el vínculo del código de soporte técnico en la parte inferior de la pantalla y envíe al ingeniero de soporte técnico el GUID resultante.
    ![Busque el código de soporte técnico en la parte inferior de la pantalla][Support Code]
    * Si se encuentra en una página sin código de soporte en la parte inferior, presione F12 y busque el SID y CID y envíe estos dos resultados al ingeniero de soporte.
* **Fecha, hora y zona horaria**: incluya la fecha y la hora precisas (incluida la **zona horaria**) en que se produjo el error.
* **Id. de usuario**: ¿quién fue el usuario que vio el error? (user@contoso.com)
    * ¿Es un usuario federado?
    * ¿Es un usuario con sincronización de hash de contraseña?
    * ¿Es un usuario solo de nube?
* **Licencia**: ¿tiene el usuario asignada una licencia de Azure AD Premium o Azure AD Básico?
* **Registro de eventos de aplicación**: si usa la escritura diferida de contraseñas y el error se produce en la infraestructura local, comprima una copia del registro de eventos de la aplicación desde el servidor de Azure AD Connect y envíela cuando se ponga en contacto con el soporte técnico.

    

[Service Restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Reinicio del servicio Azure AD Sync"
[Support Code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "El código de soporte técnico se encuentra en la parte inferior derecha de la ventana"

## <a name="next-steps"></a>Pasos siguientes

Los vínculos siguientes proporcionan información adicional sobre el restablecimiento de contraseñas con Azure AD:

* [**Inicio rápido**](active-directory-passwords-getting-started.md): preparativos para el autoservicio de administración de contraseñas de Azure AD 
* [**Licencias**](active-directory-passwords-licensing.md): configuración de licencias de Azure AD
* [**Datos**](active-directory-passwords-data.md): información sobre los datos necesarios y cómo se usan para administrar contraseñas
* [**Implementación**](active-directory-passwords-best-practices.md): planee e implemente SSPR en sus usuarios mediante las instrucciones que se encuentran aquí.
* [**Personalización**](active-directory-passwords-customize.md): personalice el aspecto de la experiencia SSPR para su empresa.
* [**Directiva**](active-directory-passwords-policy.md): conozca y establezca directivas de contraseñas de Azure AD.
* [**Escritura diferida de contraseñas** ](active-directory-passwords-writeback.md): cómo funciona la escritura diferida de contraseñas con su directorio local.
* [**Informes**](active-directory-passwords-reporting.md): descubra si, cuándo y dónde sus usuarios acceden a la funcionalidad SSPT.
* [**Profundización técnica**](active-directory-passwords-how-it-works.md): conozca lo que hay detrás para comprender cómo funciona.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): ¿Cómo? ¿Por qué? ¿Qué? ¿Dónde? ¿Quién? ¿Cuándo? - Respuestas a preguntas que siempre ha querido hacer

