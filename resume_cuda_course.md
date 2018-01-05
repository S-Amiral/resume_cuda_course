# Trois gros chapitres
    1. Lambda Java (orienté CPU) -> java
    2. Omp (orienté CPU) -> c/c++
    3. GPU_Cuda (GPU) -> CUDA

# Qu'est ce qu'une stream
Une *stream* permet d'écrire du code à la volé pour être plus flexible. Car elle nous permet d'utiliser la sortie d'une stream pour l'input d'une autre, même système que les *pipe* en linux. Par exemple on peut très facilement faire en sorte que notre stream se fasse de façon parallel, et ça c'est gratuit ;).


# Lambda Java
Ce sont des raccourci syntaxique pour une implémentation foncionnel.
Java8 support différent types *d'inférence*, c'est à dire ce qui peu-être déduit automatiquement par le compilateur :
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

## Syntaxe :
    Un lambda simple se décompose en trois partie bien distinct :
        1. Paramètre d'entrer | eg.[x1]
        2. opérateur lambda | eg. ->
        3. contenu de la méthode | [x1 * 2]

        Mémo : lambda syntaxe : [paramêtre d'entrer]+[->]+{contenu de la méthode]

    stratégie de codage : ( 1 -> 2 )
        1. qu'est-ce qu'on recoit ?
        2. qu'est-ce qu'on veut faire avec notre input ?

## référence de méthode
Il est possible de reduire la taille de notre lambda en utilisant les références de méthodes ! Par exemple imaginons que notre lambda dois afficher tout les résultats (int) de notre liste, on ferai comme ceci :

```java
    x -> System.println(x);
```
Mais grace en référence de methode nous pouvons tout simplement le réecrire comme ceci :

```java
    System.out.println;
```

# Interface fonctionnelle :
	C'est une interface qui ne contient *qu'une seule méthode !*
	Voici quelque exemple d'interface fonctionnelle :
    1. Runnable -> run()
    2. ActionListener -> actionPerform()
    3. Comparator -> compare()

# Six interfaces fonctionnelles à connaitre :
    1. *predicate<T>* | *filter*
    	- expression boolean qui permet de *filtrer* des données.

    2. *consumer<T>* | s'emploi avec un *foreach*
    	- permet de modifier un objet sans faire de copie (= faire une action sur l'object)

    3. *binaryoperator<T>* | réduction -> répresente une série de nombre par un nombre
    	- remplacer une série de nombre par un nombre (ex. faire la min, max, moy, medianne)

    4. *intBinaryOperator* | Spécifique au Integer!

    5. *function<Input type, Return type>* | *MapTo...*
    	- *d'extraire un attributs d'un objet*
    	- ça permet de faire du *mapping* (relation 1:1)

    6. *bisFunction<Input1 type, Input2 type, return type>*

# Terminal ou non terminal ?
Les lambdas peuvent s'écrire avec la même idée que les pipes en linux. Par exemple la sortie d'une expression pourra être l'entrée de l'expression suivante. Mais attention celà n'est pas vrai pour toutes les interfaces, en effet certaine sont dites terminal , ce qui signifie qu'on ne peut pas réutiliser la sortie pour une autre entrée. Mais certaine sont dite non-terminal ce qui permet de faire des pipe à linux. Voici une liste qui résume ça :

- Skip : non terminal, stream-in -> stream-out
- limi : non terminal, stream-in -> stream-out
- count : terminal, impossible d'employé la stream ensuite
- filter : non terminal, stream-in -> stream-out
- map : non terminal, stream-in -> stream-out
- toArray : termial, impossible d'employé la stream ensuite

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


