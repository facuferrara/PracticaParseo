1. [2 puntos] Diséñese el DT para un scanner que reconoce identificadores que inician con una letra, le pueden o no seguir letras o dígitos o guiones bajos. No pueden ocurrir dos guiones bajos seguidos, tampoco pueden aparecer al final de un identificador.

    ```plain
    L ::= [a-zA-Z]
    D ::= [0-9]
    G ::= [_]
    ID ::= L (L | D | G L | G D)*
    ```

     ![AF Aritmética](img/P1UNAHUR_PARSEO_REC_2023.png)

    | Q   | Letra | Dígito | Guión | Otro     | Token | Retroceso |
    | --  | --    | --     | --    | --       | --    | --        |
    | >q0 | q1    | Error  | Error | Error    | -     | -         |
    | q1  | q1    | q1     | q3    | Error    | -     | -         |
    | *q2 | -     | -      |  -    | -        | ID    | 1         |
    | q3  | q1    | q1     | Error | Error    | -     | -         |
    
    

2. [4 puntos] Constrúyase el parser ASDP LL(1) y 
   muéstrese el parsing para una entrada de longitud mayor a 4.
    Palabras válidas del lenguaje {[], [id], [id,id], [id,id,id], ... }

    ```plain
    L = {[], [id], [id,id], [id,id,id], ... }

    S -> [ A 
    A -> ] | B ]
    B ->  idC 
    C -> λ | ,idC  

    PRIM(S) = {[}
    PRIM(A) = {], id}
    PRIM(B) = {id}
    PRIM(C) = {λ, ','}

    SIG(S) = {$}
    SIG(A) = {$}
    SIG(B) = {]}
    SIG(C) = {]}
    
    PRED(S-> [ A) = {[}
    PRED(A -> ]) = {]}
    PRED(A -> B ]) = {id}
    PRED(B ->  idC) = {id}
    PRED(C -> λ  ) = (PRIM(λ)-{λ}) U SIG(C) = {]}
    PRED(C -> ,idC  ) = { ','}
    ```
    Tabla de ASDP:
    
    Todos los simbolos terminales y $ como columnas y como fila van los no Terminales
    
    | VN | [            | id            |    ]      |   ','     |   $    |
    | -- |   --         | --            | --        | --        | --     |
    | S  |   S-> [ A    | Error         | Error     | Error     | error  |
    | A  |   Error      | A -> B ]      | A -> ]    | Error     | error  |
    | B  |   Error      | B ->  idC     |  Error    | Error     | error  |
    | C  |   Error      | Error         | C -> λ    | C -> ,idC |error   |

    Tabla de parsing:
    
    Se hace $S siempre $ seguido del axioma en este caso S
    Chequeo en el puntero de la pila si tengo esa entrada en la producción.
    Tambien podes emparejar sacando del tope de la pila y
    correr el tope a la entrada.
    
    | Pila   |   Cadena        | Regala o Acción       |
    | --     |   --            | --                    |
    | $S     |   [id,id]$      |  S-> [ A              |
    | $[A    |   [id,id]$      |  Emparejar([)         |
    | $A     |   id,id]$       |  A -> B ]             |
    | $]B    |   id,id]$       |  B ->  idC            |
    | $]Cid  |   id,id]$       |  Emparejar(id)        |
    | $]C    |   ,id]$         |  Emparejar(,)         |
    | $]C    |   id]$          |   C -> λ              |
    | $]     |   id]$          |   Emparejar(id)       |
    | $]     |   ]$            |   Emparejar(])        |
    | $      |   $             |   Accept              |



3. [4 puntos] Constrúyase el parser ASAP SLR y muéstrese 
   el parsing para la entrada [[][]]. 

   Palabras válidas del lenguaje {[], [[]], [][], [[][]], ... }

    Gramatica:

    S -> [] | [S] | SS
    
    S' -> S

    R1 S-> []
    R2 S -> [S]
    R3 S -> SS

   ### Tabla de Parsing para la Entrada `[[][]]`

| Pila               | Entrada   | Acción                         |
|--------------------|-----------|--------------------------------|
| `[0]`              | `[[][]]$` | **Shift `[`** (desplazar a 1) |
| `[0, [1]`          | `[][]]$`  | **Shift `[`** (desplazar a 1) |
| `[0, [1, [1]`      | `][]]$`   | **Shift `]`** (desplazar a 2) |
| `[0, [1, [1, ]2]`  | `[]]$`    | **Reduce `S → []`** <br> (pop `[1, ]2`, push `S3`) |
| `[0, [1, S3]`      | `[]]$`    | **Shift `]`** (desplazar a 4) |
| `[0, S5]`          | `[]$`     | **Reduce `S → [S]`** <br> (pop `[1, S3, ]4`, push `S3`) |
| `[0, S3]`          | `[]$`     | **Shift `[`** (desplazar a 1) |
| `[0, S3, [1]`      | `]$`      | **Shift `]`** (desplazar a 2) |
| `[0, S3, [1, ]2]`  | `$`       | **Reduce `S → []`** <br> (pop `[1, ]2`, push `S3`) |
| `[0, S3, S3]`      | `$`       | **Reduce `S → SS`** <br> (pop `S3, S3`, push `S5`) |
| `[0, S5]`          | `$`       | **Aceptar** (accept)          |

### Explicación Paso a Paso

1. En el primer paso, vamos desplazando corchetes abiertos `[` y cerrados `]`, aplicando **shift** en cada símbolo hasta poder aplicar una reducción.
   
2. Las reducciones se aplican cada vez que encontramos una subcadena completa que coincide con una de las producciones:
   - **`S → []`**: cuando encontramos un par `[]`.
   - **`S → [S]`**: cuando encontramos una expresión `S` dentro de corchetes `[` y `]`.
   - **`S → SS`**: cuando tenemos dos expresiones `S` una tras otra.

3. Finalmente, después de aplicar todas las reducciones, llegamos al estado de **aceptación** (`accept`), lo cual indica que la cadena de entrada `[[][]]` es válida.

Esta tabla completa muestra el proceso del parser SLR para analizar la entrada `[[][]]` hasta su aceptación final.
     