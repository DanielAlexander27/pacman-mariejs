# Pac-Man en Assembly (Marie.js)
## Lógica de Pac-Man

## Fantasmas
### Lógica
Los fantasmas operan según el siguiente sistema de prioridad:
1. Detectar a Pac-Man y dirigirse hacia este. 
2. En caso de no encontrar a Pac-Man o de haber llegado a la celda en donde fue visto por última vez, aplicar movimiento pseudoaleatorio.

Para mover a un fantasma, se ejecuta el bloque de instrucción [moveGhost()](#moveGhost()). 

El movimiento de pacman se basa en tres comprobaciones:
1. Detectar bolitas o esteroides que esten cerca en un radio de 3 bloques y se mueven en base a un array de direcciones generado al principio de cada partida.
2. Si no hay bolitas cerca en un radio de 3 bloques, Pacman detecta en un mismo radio de 3 si hay fantasmas. Si hay fantasmas y no tiene un esteroide comido, debe ir en dirección contraria a donde esta el fantasma. Si si tiene un esteroide comido, le sigue al fantasma.
3. Si no hay ni fantasmas ni consumibles en un radio de 3, se mueve aleatoriamente.

### Variables Asociadas
Cada fantasma, tiene asociado las siguientes variables:
- **Color original**. Valor constante. Representa el color original del fantasma.
- **Posición inicial**. Valor constante. Indica la posición al iniciar la partida.
- **Array de prioridad**. Constante y de tamaño 4. Contiene las direcciones (arriba, abajo, izquierda y derecha), cuya posición en el array indica su prioridad.
- **Posición**. Indica la posición actual del fantasma.
- **Color actual**. Almacena el color actual del fantasma. Puede ser el color original como el característico color azul cuando Pac-Man consume el *power-up* (esteroide).
- **Dirección del fantasma**. Tiene uno de los siguientes valores: ±16, ±-1 y 0. Indica al fantasma hacia dónde debe dirigirse para atrapar a Pac-Man. Si el valor es 0, indica que Pac-Man no fue detectado por el fantasma.
- **Target del fantasma**. Contiene la dirección de Pac-Man en donde fue visto por última vez por el fantasma. Si no ha sido detectado, el valor almacenado -1.
- **Celda backup**. Antes de que el fantasma se mueva a una celda, guarda el contenido de esta. Empleado para restaurar el valor de celda cuando un fantasma pasa por una bolita o un *porwer-up* (esteroide).

Adicionalmente, existen variables de puntero para la mayoría de variables.

## Bloque de Instrucciones
### moveGhost()
Inicia con la siguiente instrucción:

    moveGhost(ghost_direction;ghost_pos;ghost_color;ghost_target;ptr_array_prioridad;cell_backup), Hex 0

Este bloque de instrución empieza llamando el bloque de instrucción [detectPacman()](#detectPacman()). En caso de detectar a Pac-Man, el fantasma comienza a moverse hacia esta. 

Si Pac-Man no es detectado, verifica si el `target` del fantasma es distinto `-1`. En ese caso, el fantasma se mueve haciendo uso del valor de `direction`.

Si no existe `target`, se calculan las posiciones disponibles (no existe pared) para dirigirse llamando al bloque de instrucción [getPosAvail()](#getPosAvail)
y validadas con [validatePos()](#valdiatePos()).

Una vez generado las posiciones disponibles, se genera un número pseudoaleatorio con [random()](#random()) entre $[0,n)$, en donde $n$ es el número de posiciones disponibles.

Con esto, se procede a realizar el movimiento.

#### Uso de Celda Backup
Antes de realizar el movimiento hacia la próxima celda, se guarda el contenida de esta si es bolita o *power-up*. Caso contrario, el contenido almacenado es `000`.

Cuando un fantasma deja una celda, restaura el valor original de esta con una condición: la celda a reestablecer no debe tener como contenido una bolita o *power-up*. 

Esta lógica se aplica puesto que existen situaciones en donde 2 fantasmas están sobre la misma celda con un ítem consumible; el primero sale y restaura, pero el segundo al salir restaura con el valor vacío. 
Aquel caso se evita con la lógia presentada.

### detectPacman()
Devuelve el valor de dirección que el fantasma debe seguir para alcanzar a fantasma. Devuelve
$16$, $-16$, $1$ o $-1$. Lo que significa arriba, abajo, derecha o izquierda, respectivamente. En caso de no detectar a Pac-Man, devuelve `0`.

La lógica se asemeja a que el fantasmas "vea" a Pac-Man a lo largo de la fila o columna. Por tal motivo, si existen paredes entre las 2 entidades, Pac-Man no será detectado.

El bloque de instrucción empieza verificando si el fantasma y Pac-Man están en la misma fila o columna, aplicando división para 16 o modulo de 16, respectivamente.

En caso de estar en la misma fila o columna, realiza un iteración por todas la fila o columna hasta encontrar a Pac-Man.
Cada celda iterada es validada con [getPosAvail()](getPosAvail()).

### getPosAvail()
Devuelve las posiciones disponibles a las que se puede mover una entidad. Opera con una posición y un puntero, el cual apunta al array de prioridad de una entidad.

### validatePos()
Opera con la posición actual y la posición de interés a dirigirse.  En la mayoría de casos, devuelve la posición de interés misma. No obstante, en los casos del efecto espejo, calcula
y devuelve la posición correspondiente. 

Devuelve -1 si la posición de interés a dirigirse es pared.

### isWall()
Valida si dada una posición existe una pared o no.

### makeGhostsBlue()
Pinta de color azul a todos los fantasmas. Además, registro el inicio del temporizador del *power-up*.

### restoreGhostColors()
Restura los colores originales de los fantasmas si el tiempo del *power-up* finalizo. Adicional, se puede
configurar para que restaura los colores sin validar el tiempo. Este úlitmo caso se usa cuando Pac-Man pierde una vida y 
tiene activado el *power-up*.

### reviewPositions()
Detecta si Pac-Man y un fantasma comparten la misma lógica. En caso de que sí, se realiza la lógica correspondiente.

- Si Pac-Man tiene el *power-up* activado y el fantasma está de color azul, el fantasma regresa a su posición inicial y Pac-Man obtiene 10 puntos.
- Si Pac-Man tiene el *power-up* activada y el fantasma tiene su color original, Pac-Man pierde una vida y regresa a su posición original.
- Si Pac-Man no tiene el *power-up*, este pierde una vida y regresa a su posición original.

Adicional, valida si el fantasma está encima de una bolita o *power-up* para sumar al puntaje y restar de la cantidad total de consumibles.