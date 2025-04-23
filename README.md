# Pac-Man en Assembly (Marie.js)

---
## Lógica de Pac-Man

## Fantasmas
### Lógica
Los fantasmas operan según el siguiente sistema de prioridad:
1. Detectar a Pac-Man y dirigirse hacia este. 
2. En caso de no encontrar a Pac-Man o de haber llegado a la celda en donde fue visto por última vez, aplicar movimiento pseudoaleatorio.

Para mover a un fantasma, se ejecuta el bloque de instrucción [moveGhost()](#moveGhost()). 

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

Si no existe `target`, se calculan las posiciones disponibles (no existe pared) para dirigirse llamando al bloque de instrucción [getPosAvail()](#getPosAvail). 

Una vez generado las posiciones disponibles, se genera un número pseudoaleatorio con [random()](#random()) entre $[0,n)$, en donde $n$ es el número de posiciones disponibles.

#### Uso de Celda Backup
Antes de realizar el movimiento,

### detectPacman()

### getPosAvail()