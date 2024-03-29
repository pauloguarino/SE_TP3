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

![GitHub Logo](imagenes/imagen1.png)
Figura 1.
## Example 2 - Using the task parameter”

Este programa crea dos tareas (task1 y task2), ambas de igual prioridad y que llaman a la misma función (vTaskFunction). Difieren únicamente en el 'task name for the task', el cual se utiliza para imprimirlo una vez dentro de la tarea.
El ejemplo pretende mostrar que a igual prioridad, las tareas se reparten el tiempo de CPU. Además, muestra que se puede usar la misma función para diferentes tareas.
La imagen 2 muestra el reparto de la CPU en el tiempo.

![GitHub Logo](imagenes/imagen2.png)
Figura 2.
## Example 3 - Experimenting with priorities”
En este ejemplo se usaron 2 tareas de distinta prioridad. En la gráfica 3 se muestra que la task2 que es la tarea de mayor prioridad, es la única tarea que se ejecuta.

![GitHub Logo](imagenes/imagen3.png)
Figura 3.
## Example 4 - Using the Blocked state to create delay”
Igual que el ejemplo anterior se crearon 2 tareas (task1 y task2), con task2 de mayor prioridad que task1. Y se agregó un tiempo de delay en ambas, mediante la función:
```
vTaskDelay(100 / portTICK_RATE_MS);
```
Esta función bloquea la tarea por un tiempo determinado, lo que libera a la CPU para que ejecute otras tareas. Es por eso que ambas tareas tienen su tiempo de CPU a pesar de que la task1 tiene menor prioridad que la task2. Ver figura 4.

![GitHub Logo](imagenes/imagen4.png)
Figura 4.
## Example 5 - Converting the example tasks to use vTaskDelayUntil”
Este ejemplo funciona de forma similar al anterior pero asegura que el tiempo que la tarea está bloqueada es exactamente el explicitado. Ver figura 5. Esto ocurre por que se cambia 
```
vTaskDelay(100 / portTICK_RATE_MS);
```
Por:
```
xLastWakeTime = xTaskGetTickCount();
```
Sincroniza el momento inicial con el número de ticks del contador del programa.

```
vTaskDelayUntil(&xLastWakeTime, (250 / portTICK_RATE_MS));
```
Mide el tiempo en el número de ticks con respecto al momento en que se sincroniza.
El ejemplo tiene la siguiente respuesta temporal. 

![GitHub Logo](imagenes/imagen5.png)
Figura 5.
## Example 6 - Combining blocking and non-blocking tasks”
En este ejemplo hay 3 tareas, 2 de prioridad 1 y una periódica de prioridad 2. Las tareas de baja prioridad se encargan de prender y apagar el LED, mientras que la tarea de alta prioridad está bloqueada.Ver figura 6.

![GitHub Logo](imagenes/imagen6.png)
Figura 6.
## Example 7 - Defining an idle task hook function”
En este ejemplo se define un tarea Idle que aumenta la cuenta de un contador y después llama a una interrupción que espera a que las demás tareas se habiliten. También se definen 2 tareas 'task1' con prioridad 1 y 'task2' con prioridad 2, que togglean el LED3, imprimen el contador del Idle y se bloquean. A continuación se muestra su diagrama temporal. 

![GitHub Logo](imagenes/imagen7.png)
Figura 7.
## Example 8 - Changing task priorities”
En este ejemplo se utiliza un una función para cambiar la prioridad a la tarea 2 de forma tal de que su prioridad sea un cierto tiempo mayor a la prioridad de 'task1' y en otro momento de menor prioridad.
La función usada es:
```
vTaskPrioritySet(xTask2Handle, (uxPriority + 1));
```
En la figura 8 se muestra su diagrama temporal.

![GitHub Logo](imagenes/imagen8.png)
Figura 8.
## Example 9 - Deleting tasks”
En este ejemplo se muestra como dentro de tareas se pueden crear otras tareas y cómo deben destruirse. 
```
vTaskDelete(xTask2Handle);
```
El diagrama temporal se muestra en la figura 9.

![GitHub Logo](imagenes/imagen9.png)
Figura 9.
## Example 10 - Blocking when receiving from a queue”
En este ejemplo hay 3 tareas (vSender1, vSender1 y vReceiver) con prioridades 1, 1 y 2. Las tareas vSender envían el valor 100 o 200 a una cola de tamaño 5. Después la tarea vReceiver lee los datos de la cola y los imprime. Cabe aclarar, que la cola nunca se llenará porque vReceiver  es de mayor prioridad que la otra tarea que envía datos.
El diagrama temporal se muestra en la figura 10.

![GitHub Logo](imagenes/imagen10.png)
Figura 10.
## Example 11 - Blocking when sending to a queue or sending structures on a queue”
El diagrama temporal se muestra en la figura 11.

![GitHub Logo](imagenes/imagen11.png)
Figura 11.
## Example 12- Using a binary semaphore to synchronize a task with an interrupt”
El diagrama temporal se muestra en la figura 12.

![GitHub Logo](imagenes/imagen12.png)
Figura 12.
## Example 13 - Using a counting semaphore to synchronize a task with an interrupt”
El diagrama temporal se muestra en la figura 13.

![GitHub Logo](imagenes/imagen13.png)
Figura 13.
## Example 14 - Sending and receiving on a queue from within an interrupt”
El diagrama temporal se muestra en la figura 14.

![GitHub Logo](imagenes/imagen14.png)
Figura 14.
## Example 15- Re-writing vPrintString() to use a semaphore”
El diagrama temporal se muestra en la figura 15.

![GitHub Logo](imagenes/imagen15.png)
Figura 15.

## Implementación 1
Se implementaron 3 tareas. Task1 tarea periódica de periodo 500ms, con prioridad 1. Esta tarea genera una interrupción, que le entrega un semáforo a la tarea 2, de prioridad 2. Esta última le envía a la tarea 3 un dato mediante una cola, para que la tarea 3 la imprima.

![GitHub Logo](imagenes/implementacion1.png)

## Implementación 2
Se implementaron 3 tareas. Task1 tarea periódica de periodo 500ms, con prioridad 1. Esta tarea genera una interrupción, que habilita por medio de una cola a la tarea 2. Esta tarea a su vez habilita a la tarea 3 mediante un semáforo.

![GitHub Logo](imagenes/implementacion2.png)

## Implementación 3
Se crean tres tareas de igual prioridad. Donde cada tarea hace titilar el mismo LED.
Para que cada tarea complete una pasada se utiliza un mutex, que se toma al iniciar la tarea y se libera al finalizar el delay luego de apagar el LED.  A continuación, se muestra la ejecución del programa. 

![GitHub Logo](imagenes/ejer_3.png)

# Hoja de ruta

![GitHub Logo](rtos.jpg)
