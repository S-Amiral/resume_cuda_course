# Trois gros chapitres
    1. Lambda Java (orienté CPU) -> java
    2. Omp (orienté CPU) -> c/c++
    3. GPU_Cuda (GPU) -> CUDA

# Petit rappel sur les intances

``` java
int[]::new // on donne une référence vers un constructeur qui va créer un tableau dans le cas échéant
b = new int[] // c'est une function
```

**Cb d'objets instancier ?**
Java :
```java
java : A[] tab = new A[n] // 1 objets
```

c/c++ :
```c
A* tab = new A[n] // n+1 objets
A** tab = new A[n] // 1 objets
```


# Qu'est ce qu'une stream
Une **stream** permet d'écrire du code à la volé pour être plus flexible. Car elle nous permet d'utiliser la sortie d'une stream pour l'input d'une autre, même système que les **pipe** en linux. Par exemple on peut très facilement faire en sorte que notre stream se fasse de façon **parallel**, et ça c'est gratuit ;).


# Lambda Java
Ce sont des raccourcis syntaxique pour une **implémentation foncionnel**. (x -> return x\*x)

Un lambda simple se décompose en **trois partie* bien distincte** :
    1. Paramètre d'entrer | eg.[x1]
    2. opérateur lambda | eg. ->
    3. contenu de la méthode | [x1 * 2]

Mémo : lambda syntaxe : **[paramêtre d'entrer]+[->]+{contenu de la méthode]**

stratégie de codage : ( 1 -> 2 )
    1. qu'est-ce qu'on recoit ?
    2. qu'est-ce qu'on veut faire avec notre input ?

Java8 support différent **Types d'inférence**, c'est à dire ce qui peu-être déduit automatiquement par le compilateur :
        - type
        - {}
        - return
        - nom de la méthode
        - nom de la classe
        - variable

par exemple on peut réécrire notre lambda de façon plus courte :
```java
    (x) -> return x*x;
    x -> x*x
```

Attention dans certain cas on est obligé de laisser les parathèses quand on a deux input par exemple :
```java
    (x,y) -> return x*y;
    (x,y) -> x*y
```


## référence de méthode
Il est possible de reduire la taille de notre lambda en utilisant les références de méthodes ! Par exemple imaginons que notre lambda doit afficher tous les résultats (int) de notre liste, on ferai comme ceci :

```java
    x -> System.println(x);
```
Mais grace au référence de methode nous pouvons tout simplement le réecrire comme ceci :

```java
    System.out.println;
```
Voici un tableau résumant les différents type de référence de méthode avec chacun un exemple :

| Type   | Référence de méthode | Expression lambda |
| ------ | -------------------- | ----------------- |
| Référence à une méthode statique | System.out::println <br> Math::Pow| x -> System.out.println(x) <br> (x,y)->Math.pow(x,y) |
| Référence à une méthode sur une instance | monObject::maMethode | x -> monObject.maMethode(x) |
| Référence à une méthode d'un objet arbitraire d'un type donné | String::compareToIgnoreCase | (x,y) -> x.compareToIgnoreCase(y) |
| Référence à un constructeur | MaClasse::new | () -> new MaClasse() |


# Interface fonctionnelle :
C'est une interface qui ne contient *qu'une seule méthode !*. Voici quelque exemple d'interface fonctionnelle :

| Type | Methode |
| ---- | ------- |
| Runnable | run() |
| ActionListener | actionPerform() |
| Comparator | compare() |


## Six interfaces fonctionnelles à connaitre :

| Type | Methode | Explication |
| ---- | ------- | ----------- |
| predicate<T> | filter | expression boolean qui permet de **filtrer** des données.
| consumer<T> | foreach | permet de modifier un objet sans faire de copie (= faire une action sur l'object)
| binaryoperator<T> <br> intBinaryOperator[méthode utile])| reduce | répresente une série de nombre par un nombrplacer une série de nombre par un nombre (ex. faire la min, max, moy, medianne)
| function<InputType, ReturnType> <br> bisFunction<Input1 type, Input2 type, return type>| MapTo... | d'extraire un attributs d'un objet <br> ça permet de faire du **mapping** (relation 1:1)



# Terminal ou non terminal ?
Les lambdas peuvent s'écrire avec la même idée que les pipes en linux. Par exemple la sortie d'une expression pourra être l'entrée de l'expression suivante. Mais attention celà n'est pas vrai pour toutes les interfaces, en effet certaine sont dites terminal , ce qui signifie qu'on ne peut pas réutiliser la sortie pour une autre entrée. Mais certaine sont dite non-terminal ce qui permet de faire des pipe à linux. Voici une liste qui résume ça :

| methode | action |
| ------- | ------ |
| Skip | **non terminal**, stream-in -> stream-out |
| limit | **non terminal**, stream-in -> stream-out |
| filter | **non terminal**, stream-in -> stream-out |
| map | **non terminal**, stream-in -> stream-out |
| count | **terminal**, impossible d'employé la stream ensuite |
| collect | **terminal**, impossible d'employé la stream ensuite |
| reduce | **terminal**, impossible d'employé la stream ensuite |
| toArray | **terminal**, impossible d'employé la stream ensuite |

# Frabrication d'un stream et d'un count
Il y a plusieur façon de créer une stream... Voici quelque cas généraux pour les containers

## Créer une stream sur une liste
```java
	//stream sur une liste/set
	List<Integer> list = Arrays.asList(1, 2, 3);
	Stream<Integer> stream = list.stream();

	Set<Integer> set = new TreeSet<Integer>();
	set.add(7);
	set.add(8);
	set.add(9);
	Stream<Integer> stream = set.stream();

	//stream sur un tableau
    Integer[] tab = { 1, 2, 3 };
    Stream<Integer> stream = Arrays.stream(tab);
```


## Fabrication d'un Filter
Permet de filtrer dans une collection/array.
Par exemple compter le nombre personnes > 50 ans. Ici nous devons filtrer !
```java
        List<Personne> list = PersonneTools.create(n);
         long count = 0;
        { //v1 avec variable
        Predicate<Personne> ageBigger50 = personne -> personne.getAge() > 50;
            count = list.stream().filter(ageBigger50).count();
        }

        {//v2 sans variable
            count = list.stream().filter(personne -> personne.getAge()).count();
        }
        // count : terminal, interdit employer stream apres
```


# OpenMP
C'est une interface de programmation pour le calcul paralèlle. Il se présente sous la forme d'un ensemble de directives.

Il existe plusieur pattern pour gérer les indices, mais ici nous allons en voir deux :
- Moitié/Moitié
- entrelacement

## Pattern Moitié/moitié
Ce partern est facile à expliquer, mais compliquer à mettre en place.... Si le nombre de case du tableau n'est pas divisible par le nombre de thread, cela peut vite devenir un problème.


## Pattern Entrelacement
Ce pattern est très facile à mettre en place mais peut être moins évident à comprendre. Pour faire simple imaginons trois threads qui doivent nettoyer les vitres d'un bus. Le thread n°1 prendra la fenêtre n°1 et quand le thread n°1 aura fini sa tâche il prendra la fenêtre ID_FENETRE + NB_THREADS. Et celà va s'appliquer pour les autres threads. Comme ça on évite tout problème de concurrence vu que chaque thread fera seuelement les fenêtres qui lui seront attribué.


Voici un code séquentiel :

```c
    //code séquentiel
    `for(int i = 0; i<n; ++i)
    {
        work(i)
    }
```

Voici ça version parallélisé avec openMP :

```c
#pragma omp parallel
{ // tout le bloc sera exécuté en parallèl

    int s = TID; //id du thread courrant

    while(s<n) // n = le nombre de fenêtre dans le bus
    {
        work(s); //le thread néttoie la vitre qui lui est attribué
        s+=NB_THREAD; //on met à jour l'indice
    }
}
```


