---
layout: post
title:  "El code challenge (trabajo en proceso)"
date:   2022-11-06 00:00:00 -0600
categories: java
tags: java
comments: true
published: false
---

Coleccion de consejos autobiograficos para comenzar a prepararte y sobrevivir el temido `code challenge`.

#### Aprende y practica tu Inglés

Por mucho que me encanta mi lengua materna, lo cierto es que el idioma Inglés es la `lingua franca` en la programación y en todo lo que tiene que ver con las tecnologias de la información, todos los recursos que voy a ligar a este post estan en Inglés.  

#### La Gran O

*Lo que no se mide no se puede mejorar*. `Big O notation` es una herramienta que nos permite describir como cambia la eficiencia de nuestros algoritmos de acuerdo a los elementos sobre los cuales trabaja dicho algoritmo.

Se usa para describir `space complexity`: es decir la memoria que usara y `time complexity`: el numero de pasos que vamos a usar.

Dependiendo de la particularidad de los datos a almacenar podemos tener `best case`: el mejor escenario, `average case`: escenario promedio o `worst case`: el peor de los casos.

Dado el siguiente metodo que nos dice si un elemento se encuentra o no en un arreglo, determinaremos sus big o.

{% highlight java %}
public static boolean existsOnArray(int []arr, int target) {
    for(int i = 0; i < arr.length; i++) {
        if(arr[i] == target) {
            return true;
        }
    }
    return false;
}
{% endhighlight java %}

Para su time complexity tenemos

|best case| Ω(1) | El elemento buscado esta en arr[0]|
|average case|  Θ(n) |Tenemos que recorrer el array completo|
|worst case|  O(n) |El elemento no existe|

Para el space complexity, no importa que tan grande sea el arreglo que tomamos como argumento, no estamos gastando mas espacio, es decir es constante `O(1)`.

Dos cosas a notar es que, lo que mas se busca son los valores del `worst case` es decir los representados con una O, esto por que asi sabremos como se comportan nuestros algoritmos en el peor de los casos, si tenemos dos algoritmos que tienen el mismo big o para el worst case, entonces podemos voltear a ver el average case para elegir uno de los dos.

La otra cosa a notar es que en el codigo de arriba, si nos ponemos a contar las operaciones, enrealidad son mas de O(n), por que estamos inicializando a  `i`, y en cada ciclo se incremena `i` y se revisa `i < arr.length`, por lo tanto podriamos querer decir que su big o en el peor de los casos es `O((n * 3) + 1)` (inicializar i y en cada vuelta incrementar i, revisar si es menor a la longitud del array y luego revisar si el arreglo en ese indice es igual a nuestro elemento a buscar `arr[i] == target`). Pero en big O notation este tipo de constantes generalmente no se toman en cuenta.

De aqui en adelante nos concentraremos en big o para `time complexity` y en su worst case.

#### O(1)

{% highlight java %}
public static void printFirst(String names[]) {
    System.out.println(names[0]);
}
{% endhighlight java %}

#### O(log(n))
{% highlight java %}
public static int binarySearch(int[] nums, int target) {
    return search(nums, 0, nums.length-1, target);
}

private static int binarySearch(int[] nums, int start, int end, int target){
    if(start > end){
        return -1;
    }
    int mid = start + ((end-start) / 2 );
    if(nums[mid] == target) {
        return mid;
    }
    if(nums[mid]<target){
        return search(nums, mid+1, end, target);    
    } else {
        return search(nums, start, mid-1, target);
    }
}
{% endhighlight java %}

#### O(n)

{% highlight java %}
public static void printAll(String names[]) {
    for(String name : names) {
        System.out.println(name);
    }
}
{% endhighlight java %}

#### O(n * m)

{% highlight java %}
public static void nTimesMsum(int n[], int m[]) {
    for(int i = 0; i < n.lenght; i++){
        int sum = 0;
        for(int j = 0; j < m.lenght; j++){
            sum += m[j];
        }
        System.out.println(i * sum);
    }
}
{% endhighlight java %}

#### O(n²) El cuadratico

{% highlight java %}
public static void bubbleSort(int arr[]) {
    int n = arr.length;
    for(int i = 0; i < n - 1; i++){
        for (int j = 0; j < n - i - 1; j++)
            if (arr[j] > arr[j + 1]) {
                int temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = temp;
            }
    }
}
{% endhighlight java %}



#### O(n log(n))

{% highlight java %}
public static void mergeSort(int array[], int start, int end){
    if(start<end){
        int mid = (start + end) / 2;
        mergeSort(array,start,mid);
        mergeSort(array,mid+1,end);
        merge(array,start,mid,end);
    }

}

private static void merge(int array[], int start, int mid, int end){
    int left[] = new int[(mid - start) + 2];
    System.arraycopy(array, start, left, 0, left.length - 1);
    left[left.length - 1] = Integer.MAX_VALUE;
    mid++;

    int right[] = new int[(end-mid) +2];
    System.arraycopy(array, mid, right, 0, right.length - 1);
    right[right.length - 1] = Integer.MAX_VALUE;


    int rightIdx = 0;
    int leftIdx = 0;

    for(int i= start; i<=end; i++){
        if(left[leftIdx] < right[rightIdx]){
            array[i] = left[leftIdx];
            leftIdx++;
        }else{
            array[i] = right[rightIdx];
            rightIdx++;
        }
    }

}
{% endhighlight java %}

#### Las data structures

Es muy importante conocer las estructuras de datos ya que usar la estructura de datos correcta simplifica y optimiza nuestros algoritmos.

#### Stacks
#### Queues
#### Dynamic Arrays
|operacion|complejidad|
#### Linked Lists
#### Hash Tables
#### Set
#### Trees
#### Graphs
#### Tries

#### Practica practica practica

Debes de sentirte comodo resolviendo problemas algoritmicos mientras te observan ya sea en vivo o en una camara, por lo tanto practicar resolviendo problemas es muy importante.

Aca te dejo recursos para que practiques.

#### Que se espera de ti en un code challenge

Tan importante como llegar a la solucion (si no es que mas) lo que quieren saber de ti es como piensas, asi que no te quedes en silencio y di lo que va pasando por tu mente mientras programas.

Asegurate de que entiendes totalmente el problema, haz todas las preguntas que necesites, pregunta si los datos vienen ordenados, si el input es numerico puedes preguntar si hay datos negativos, si es una cadena de texto puedes preguntar si todo viene en minusculas o mayusculas etc.

No desesperes tratando de encontrar la solucion optima (a menos que la hallas encontrado), puedes buscar la solucion `naive` por fuerza bruta y luego ir aproximandote a una mejor solucion.

No tengas miedo de hacer preguntas mientras resuelves el problema, la persona que esta entrevistandote tiene tantas ganas como tu de que te vaya bien en la entrevista, asi que pregunta todo lo que necesites.

Code Challenge de ejemplo: [https://github.com/jsedano/examples/blob/main/Solution.java][https://github.com/jsedano/examples/blob/main/Solution.java]


https://www.bigocheatsheet.com
https://www.bigocheatsheet.com/img/big-o-complexity-chart.png



[https://github.com/jsedano/examples/blob/main/Solution.java]: https://github.com/jsedano/examples/blob/main/Solution.java
