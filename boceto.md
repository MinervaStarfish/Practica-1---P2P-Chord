2**m


m = 5
ids 
0 - 31

1 - 4 - 8 - 14 - 21 - 28


PARTE A
para el nodo 8

n + 2**(i-1), i>=0

i   start-i  successor(start-i)
 
1   9       14
2  10      14
3   12      14
4   16      21
5    24     28



interpretación de los intervalos,
los intervalos van desde el nodo de referencia, y da saltos en potencias de 2 (2**(i-1)), asegurando primero  cobertura en intervalos cercanos y luego lejanos.

¿Por qué es O(logN)?
Verbalmente
Suponiendo que se parte desde un nodo p, hasta un nodo q, se tiene una distancia D, usando closest preceding finger, por cada salto la distancia se reduce a la mitad, o más.
Dando asi que la busqueda en el peor de los casos es O(Log N)

Matemáticamente
Primero, cualquier número entero cumple que esta entre dos potencias seguidas de 2
así,

2**(i-1) <= d < 2**i

donde d es la distancia entre p y q,

Luego de un paso, la distancia se ve reducida 
dn (distancia nueva) = d - s
s siendo el salto que dio en la búsqueda

Usando closest preceding finger, tenemos que, este salto es de al menos 2**(i-1), (por 2**(i-1) <= d, siendo el valor más cercado por la izquierda a la llave a buscar)

dn <= d - 2**(i-1)

también tenemos que d < 2**i
que es d/2 < 2**(i-1)

dn < d - d/2
dn < d/2

o sea que la distancia nueva siempre es, para cada salto, menos de la mitad del total de la distancia entre p y q
esto aplicado a t saltos es

dt <= d /  2**t

esto tiende a 1 mientras t sube

 d /  2**t = 1

2**t = d
log 2**t = log d
t = log d  pasos

d es la distancia inicial desde p hasta q, en el peor de los casos es N, (N siendo el total de nodos activos que cumple además que N <= 2**m), entonces

t = O(log N)

o simplemente que el procedimiento tiene complejidad  O(log N)




PARTE B

OJO: para poder buscar en la finger table de cualquier nodo el closest preceding finger, el successor(start-i) debe cumplir que este dentro de (n ,K)

K = 26

activos

1 - 4 - 8 - 14 - 21 - 28

n1 = 8

finger table de 8
para el nodo 8

i   start-i  successor(start-i)
 
1   9       14
2  10      14
3   12      14
4   16      21
5    24     28


de 8 salta a 21
8  -- > 21

finger table de 21

i   start-i  successor(start-i)

1   22     28
2   23     28
3   25     28
4   29    1
5   5      8     

 no entra ninguno en (n,k)
se pasa al sucesor inmediato 

de 21 salta a 28
21  -- > 28

y K e (predecesor(28),  28] , por lo tanto el nodo 28 es responsable de la clave K = 26
sucesor(26) = 28


8  -- > 21  -- > 28


para K = 2
desde n1 = 4

activos

1 - 4 - 8 - 14 - 21 - 28

K pertenece a 4
sucesor(2) = 4,

pero usando estrictamente closest preceding finger, tenemos que


finger table de 4

i   start-i  successor(start-i)

1  5   8
2  6   8
3  8   8
4  12  14
5  20  21

todos los valores son mayores a K=2, pero nótese que de manera estricta, 21 es un nodo que está antes de K = 2, así que sigue siendo el closest preceding finger de 2, y cumple que pertenece a (4,2)

de 4 salta a 4
4  -- > 21

finger table de 21

i   start-i  successor(start-i)

1   22     28
2   23     28
3   25     28
4   29    1
5   5      8     

mismo caso que arriba. el nodo mas cercano a 2 es 1, 8 ya se lo pasa y 28 esta mas atras del nodo 1, cumple que ( 21, 2)

de 21 salta a 1
21  -- > 1

finger table de 1

i   start-i  successor(start-i)

1  2 4
2  3  4   
3  5   8  
4  9   14
5  17  21 

el rango a cumplir es (1,2) ninguno cumple con ello, entonces pasamos al nodo sucesor de 1, que es 4

de 1 salta a 4
1  -- > 4

K e (predecesor(4),  4] , por lo tanto el nodo 4 es responsable de la clave K = 2
successor(2) = 4








DOC https://dl.acm.org/doi/epdf/10.1145/964723.383071



