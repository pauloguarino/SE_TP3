# SE_TP3
TP3 de Sistemas Embebidos

## Example 1 - Creating tasks”

Las funciones usadas en el main del ejemplo son:

| Función | Descripción |
| ----- | ---- |
| prvSetupHardware() | inicializa la placa y el clock |
|DEBUGOUT(pcTextForMain)| envía un mensaje de debug|
|xTaskCreate(..)| Crea las tareas, les asigna prioridades, nombres y handlers|
|vTaskStartScheduler()| Inicializar el scheduler y crea la tarea IDLE|

### Cómo funciona función xTaskCreate(..) 
Esta función recibe por parámetros:
- Un puntero a la tarea
- El nombre de la tarea
- La prioridad de la tarea
- El tamaño del Stack
- El handle de la tarea

Dentro de la función
1. Pido memoria para el TCB y el stack de la tarea.
```
pxNewTCB = prvAllocateTCBAndStack( usStackDepth, puxStackBuffer );
```
2. Calcular el valor del último elemento del stack y lo apuntó con pxTopOfStack.
```
			pxTopOfStack = pxNewTCB->pxStack + ( usStackDepth - ( uint16_t ) 1 );
			pxTopOfStack = ( StackType_t * ) ( ( ( portPOINTER_SIZE_TYPE ) pxTopOfStack ) & ( ( portPOINTER_SIZE_TYPE ) ~portBYTE_ALIGNMENT_MASK  ) );
```
3. Inicializo los estados del TCB (Prioridad, ..).
```
prvInitialiseTCBVariables( pxNewTCB, pcName, uxPriority, xRegions, usStackDepth );
```
4. Me aseguro que el sistema no entre en alguna interrupción.
```
taskENTER_CRITICAL();
```
5. Aumentó el número de tareas.
```
uxCurrentNumberOfTasks++;
```
6. Agregó la tarea creada a la siguiente en la lista y asignó el número de tareas al TCB.
```
pxCurrentTCB = pxNewTCB;
```
7. Paso la tarea creada a estado READY.
```
prvAddTaskToReadyList( pxNewTCB );
```



