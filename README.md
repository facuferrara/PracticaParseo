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
    
    

2. [4 puntos] Constrúyase el parser ASDP LL(1) y muéstrese el parsing para una entrada de longitud mayor a 4. Palabras válidas del lenguaje {[], [id], [id,id], [id,id,id], ... }

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
    Tambien podes emparejar sacando del tope de la pila y correr el tope a la entrada.
    
    | Pila  |   Entrada     | Regala o Acción       |
    | --    |   --          | --                    |
    | $S    |               |  S-> [ A              |
    |      |                |                       |
    |      |                |                       |
    |      |                |                       |




3. [4 puntos] Constrúyase el parser ASAP SLR y muéstrese el parsing para la entrada [[][]]. Palabras válidas del lenguaje {[], [[]], [][], [[][]], ... }

