Introudcción al Análisis de redes
================
Guilermo de Anda-Jáuregui

**X Escuela de Verano de Matemáticas de la Unidad Juriquilla**

*Aprendizaje de máquina en biología de sistemas*

# Análisis de redes

# ¿Qué es una red?

Una red es un objeto matemático que tiene dos conjuntos:

  - Un conjunto de elementos, representados por *nodos*
  - Un conjunto de relaciones entre los elementos, representados por
    *enlaces*

# ![](redes_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

# ¿Qué haremos hoy?

1 Leer una red (y escribirla, de una vez)

2 Describir la red: tamaño, caminos mas cortos, número de componentes,
etc.

3 Estudiar propiedades de los nodos: centralidades, coeficientes de
agrupamiento locales, etc.

4 Estudiar propiedades de los enlaces: intermediacion

5 Buscar grupos (comunidades)

6 Graficar



# Iniciemos

carguemos los paquetes


**R** 
``` r
library(igraph)
library(igraph)
```

**Python** 
``` python
import networkx as nx
```

# Leer y escribir redes

Para el ejemplo, voy a usar la red de interacciones de los personajes en la novela de *Los
Miserables* (crédito: Donald Knuth, Mark Newman). 

Pueden seguir los ejercicios con cualquiera de las redes biológicas que les compartimos.

(tambien está la red de les Mis, si quieren replicar este código)

*Hay que descargar la red guardando el archivo haciendo click en "raw" y guardando la página. Nota: en algunos sistemas operativos / navegadores hay problema para descargar el raw file. Si no pueden descargarlo, intenten copiar y pegar el texto en un editor de textos y guardar el archivo.*



**Colocar el archivo en el directorio de trabajo**

## Leamos la red usando read.graph

**R** 
``` r
g <- read.graph(file = "les_miserables.gml", format = "gml")
```

**Python** 
``` python
g = nx.read_graphml("les_miserables.graphml")

```

Considerar que existen varios tipos de formatos de redes.

Examinemos nuestra red

**R**
``` r
g
```

    ## IGRAPH e2a34c4 U--- 77 254 -- 
    ## + attr: id (v/n), label (v/c)
    ## + edges from e2a34c4:
    ##  [1]  1-- 2  1-- 3  1-- 4  3-- 4  1-- 5  1-- 6  1-- 7  1-- 8  1-- 9  1--10
    ## [11] 11--12  4--12  3--12  1--12 12--13 12--14 12--15 12--16 17--18 17--19
    ## [21] 18--19 17--20 18--20 19--20 17--21 18--21 19--21 20--21 17--22 18--22
    ## [31] 19--22 20--22 21--22 17--23 18--23 19--23 20--23 21--23 22--23 17--24
    ## [41] 18--24 19--24 20--24 21--24 22--24 23--24 13--24 12--24 24--25 12--25
    ## [51] 25--26 24--26 12--26 25--27 12--27 17--27 26--27 12--28 24--28 26--28
    ## [61] 25--28 27--28 12--29 28--29 24--30 28--30 12--30 24--31 31--32 12--32
    ## [71] 24--32 28--32 12--33 12--34 28--34 12--35 30--35 12--36 35--36 30--36
    ## + ... omitted several edges

**Python** 
``` python
g
len(g.nodes()) # num. nodos
len(g.edges()) # num. aristas
```

Nuestra red tiene 77 nodos, con 254 enlaces entre ellos. Es una red *no
dirigida* y *no pesada*.


¿Cómo es *tu* red?


## Hagamos una primera visualización

``` r
plot(g)
```

**Python** 
``` python
nx.draw(g)
```

![](redes_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

Esta visualización no es muy estética… ya la mejoraremos

## Exportemos la red

Existen varios formatos para representar redes: matriz de adyacencia, lista de enlaces, etc. 

Escribamos nuestra red en graphml, un formato basado en xml que permite portar información de nodos y aristas.

**R**
``` r
#escribir
write.graph(graph = g, 
            file = "red_mis.graphml", 
            format = "graphml"
            )
```

**Python**
``` python
#escribir
nx.write_graphml(file="red_mis.graphml")
```

### Para explorar: 

- Revisen el archivo de salida; ¿cómo se ve? 
- Experimenten otro tipo de archivos de salida: gml, edge list, json... ¿cómo difieren? 
- Para los usuarios de R y Python: ¿qué pasa si intento leer una red escrita en Python en R o viceversa? 


### Interludio: ¿Hay una red en mis datos? 

Respuesta: **es más probable de lo que te imaginas** 


Ejercicio opcional: 

- Descargar estos datos:

<https://raw.githubusercontent.com/guillermodeandajauregui/useR2019INMEGEN/master/movies.csv>


Se ven más o menos así: 

``` r
    ##               person      movie billing  role
    ## 1    Viggo Mortensen green book       1 actor
    ## 2     Mahershala Ali green book       2 actor
    ## 3   Linda Cardellini green book       3 actor
    ## 4 Dimiter D. Marinov green book       4 actor
    ## 5        Mike Hatton green book       5 actor
    ## 6        Iqbal Theba green book       6 actor
``` 

Preguntas:

- ¿Puedo leer esto como una red? (Pista: con Python, *read_edgelist()*, con R, leer la tabla y *graph_from_dataframe()*)
- ¿Qué tipo de red sería esta? 
- ¿Tienes algún conjunto de datos que se parezca a esta tabla?


Dejemos esta red para otro momento…

Recordar: si tenemos un data set con dos columnas que puedan
representarse como factores, en principio tenemos alguna estructura de
relaciones que podemos representar como una red. ¿Es una red
interesante? *No lo sabemos*.

### Fin del interludio 

Volvamos a nuestra red biológica (o literaria)...

## Analicemos propiedades globales de la red

  - Componentes

**R**
``` r
mis_componentes <- components(g)
```

**Python**

``` python
nx.number_connected_components(g)
nx.connected_components(g)
#para redes dirigidas hay que usar weak or strong connected components
nx.strongly_connected_components(g)
nx.weakly_connected_components(g)
```


- Longitud promedio de caminos más cortos

**R**
``` r
mis_caminos <- average.path.length(g)
```

**Python**
``` python
mis_caminos = nx.average_shortest_path_length(g)
``` 

- clustering coefficient global

**R**
``` r
mi_clustering <- transitivity(g, type = "global")
```

**Python**
``` python
average_clustering(g)
``` 

- Diámetro 

**R**
``` r
mi_diametro <- diameter(g)
mi_diametro
```

**Python**
``` python
nx.diameter(g)
``` 


## analizar propiedades de los nodos

Accedamos a los nodos


**R**
``` r
V(g)
```

**python**
``` python
g.nodes()
g.nodes(data=True)
```

Podemos tener la información completa de los nodos

**R**
``` r
mi_df_nodos <- get.data.frame(x = g, 
                              what = "vertices")
```

**Python**
``` python
#usando pandas
import pandas as pd
pd.DataFrame.from_dict(dict(g.nodes(data=True)), orient='index')
```


Podemos asignar nuevas propiedades a los nodos

**R**
``` r
#hagamos una nueva propiedad, name, que sea una copia de label
V(g)$name <- V(g)$label

mi_df_nodos <- get.data.frame(x = g, 
                              what = "vertices")

```

**Python**
``` python
my_labels = nx.get_node_attributes(g, "label")
nx.set_node_attributes(g, values=my_labels, name="name")
pd.DataFrame.from_dict(dict(g.nodes(data=True)), orient='index')
```

### Calculemos algunas medidas de centralidad

  - grado

**R**
``` r
V(g)$grado <- degree(g)
```

  - intermediación (*betweenness*)


**R**
``` r
V(g)$intermediacion <- betweenness(g)
```

### Podemos tener también el coeficiente de agrupamiento local

**R**
``` r
V(g)$agrupamiento <- transitivity(g, type = "local", isolates = "zero")
```

**R**
``` r
mi_df_nodos <- get.data.frame(x = g, 
                              what = "vertices")
```

### Ejercicio opcional: 

¿Con lo que hemos aprendido, cómo obtendríamos la distribución de grado de nuestra red? ¿Qué nos dice sobre nuestra red?



## analizar propiedades de los enlaces

Accedamos a los enlaces

``` r
E(g)
```

    ## + 254/254 edges from e2a34c4 (vertex names):
    ##  [1] Myriel        --Napoleon       Myriel        --MlleBaptistine
    ##  [3] Myriel        --MmeMagloire    MlleBaptistine--MmeMagloire   
    ##  [5] Myriel        --CountessDeLo   Myriel        --Geborand      
    ##  [7] Myriel        --Champtercier   Myriel        --Cravatte      
    ##  [9] Myriel        --Count          Myriel        --OldMan        
    ## [11] Labarre       --Valjean        MmeMagloire   --Valjean       
    ## [13] MlleBaptistine--Valjean        Myriel        --Valjean       
    ## [15] Valjean       --Marguerite     Valjean       --MmeDeR        
    ## [17] Valjean       --Isabeau        Valjean       --Gervais       
    ## [19] Tholomyes     --Listolier      Tholomyes     --Fameuil       
    ## + ... omitted several edges

``` r
#podemos tener un data frame 
mi_df_links <- get.data.frame(g, what = "edges")
mi_df_links
```

    ##                 from               to
    ## 1             Myriel         Napoleon
    ## 2             Myriel   MlleBaptistine
    ## 3             Myriel      MmeMagloire
    ## 4     MlleBaptistine      MmeMagloire
    ## 5             Myriel     CountessDeLo
    ## 6             Myriel         Geborand
    ## 7             Myriel     Champtercier
    ## 8             Myriel         Cravatte
    ## 9             Myriel            Count
    ## 10            Myriel           OldMan
    ## 11           Labarre          Valjean
    ## 12       MmeMagloire          Valjean
    ## 13    MlleBaptistine          Valjean
    ## 14            Myriel          Valjean
    ## 15           Valjean       Marguerite
    ## 16           Valjean           MmeDeR
    ## 17           Valjean          Isabeau
    ## 18           Valjean          Gervais
    ## 19         Tholomyes        Listolier
    ## 20         Tholomyes          Fameuil
    ## 21         Listolier          Fameuil
    ## 22         Tholomyes      Blacheville
    ## 23         Listolier      Blacheville
    ## 24           Fameuil      Blacheville
    ## 25         Tholomyes        Favourite
    ## 26         Listolier        Favourite
    ## 27           Fameuil        Favourite
    ## 28       Blacheville        Favourite
    ## 29         Tholomyes           Dahlia
    ## 30         Listolier           Dahlia
    ## 31           Fameuil           Dahlia
    ## 32       Blacheville           Dahlia
    ## 33         Favourite           Dahlia
    ## 34         Tholomyes          Zephine
    ## 35         Listolier          Zephine
    ## 36           Fameuil          Zephine
    ## 37       Blacheville          Zephine
    ## 38         Favourite          Zephine
    ## 39            Dahlia          Zephine
    ## 40         Tholomyes          Fantine
    ## 41         Listolier          Fantine
    ## 42           Fameuil          Fantine
    ## 43       Blacheville          Fantine
    ## 44         Favourite          Fantine
    ## 45            Dahlia          Fantine
    ## 46           Zephine          Fantine
    ## 47        Marguerite          Fantine
    ## 48           Valjean          Fantine
    ## 49           Fantine    MmeThenardier
    ## 50           Valjean    MmeThenardier
    ## 51     MmeThenardier       Thenardier
    ## 52           Fantine       Thenardier
    ## 53           Valjean       Thenardier
    ## 54     MmeThenardier          Cosette
    ## 55           Valjean          Cosette
    ## 56         Tholomyes          Cosette
    ## 57        Thenardier          Cosette
    ## 58           Valjean           Javert
    ## 59           Fantine           Javert
    ## 60        Thenardier           Javert
    ## 61     MmeThenardier           Javert
    ## 62           Cosette           Javert
    ## 63           Valjean     Fauchelevent
    ## 64            Javert     Fauchelevent
    ## 65           Fantine       Bamatabois
    ## 66            Javert       Bamatabois
    ## 67           Valjean       Bamatabois
    ## 68           Fantine         Perpetue
    ## 69          Perpetue         Simplice
    ## 70           Valjean         Simplice
    ## 71           Fantine         Simplice
    ## 72            Javert         Simplice
    ## 73           Valjean      Scaufflaire
    ## 74           Valjean           Woman1
    ## 75            Javert           Woman1
    ## 76           Valjean            Judge
    ## 77        Bamatabois            Judge
    ## 78           Valjean     Champmathieu
    ## 79             Judge     Champmathieu
    ## 80        Bamatabois     Champmathieu
    ## 81             Judge           Brevet
    ## 82      Champmathieu           Brevet
    ## 83           Valjean           Brevet
    ## 84        Bamatabois           Brevet
    ## 85             Judge       Chenildieu
    ## 86      Champmathieu       Chenildieu
    ## 87            Brevet       Chenildieu
    ## 88           Valjean       Chenildieu
    ## 89        Bamatabois       Chenildieu
    ## 90             Judge      Cochepaille
    ## 91      Champmathieu      Cochepaille
    ## 92            Brevet      Cochepaille
    ## 93        Chenildieu      Cochepaille
    ## 94           Valjean      Cochepaille
    ## 95        Bamatabois      Cochepaille
    ## 96        Thenardier        Pontmercy
    ## 97        Thenardier     Boulatruelle
    ## 98     MmeThenardier          Eponine
    ## 99        Thenardier          Eponine
    ## 100          Eponine          Anzelma
    ## 101       Thenardier          Anzelma
    ## 102    MmeThenardier          Anzelma
    ## 103          Valjean           Woman2
    ## 104          Cosette           Woman2
    ## 105           Javert           Woman2
    ## 106     Fauchelevent   MotherInnocent
    ## 107          Valjean   MotherInnocent
    ## 108     Fauchelevent          Gribier
    ## 109        Jondrette        MmeBurgon
    ## 110        MmeBurgon         Gavroche
    ## 111       Thenardier         Gavroche
    ## 112           Javert         Gavroche
    ## 113          Valjean         Gavroche
    ## 114          Cosette     Gillenormand
    ## 115          Valjean     Gillenormand
    ## 116     Gillenormand           Magnon
    ## 117    MmeThenardier           Magnon
    ## 118     Gillenormand MlleGillenormand
    ## 119          Cosette MlleGillenormand
    ## 120          Valjean MlleGillenormand
    ## 121 MlleGillenormand     MmePontmercy
    ## 122        Pontmercy     MmePontmercy
    ## 123 MlleGillenormand      MlleVaubois
    ## 124 MlleGillenormand   LtGillenormand
    ## 125     Gillenormand   LtGillenormand
    ## 126          Cosette   LtGillenormand
    ## 127 MlleGillenormand           Marius
    ## 128     Gillenormand           Marius
    ## 129        Pontmercy           Marius
    ## 130   LtGillenormand           Marius
    ## 131          Cosette           Marius
    ## 132          Valjean           Marius
    ## 133        Tholomyes           Marius
    ## 134       Thenardier           Marius
    ## 135          Eponine           Marius
    ## 136         Gavroche           Marius
    ## 137     Gillenormand        BaronessT
    ## 138           Marius        BaronessT
    ## 139           Marius           Mabeuf
    ## 140          Eponine           Mabeuf
    ## 141         Gavroche           Mabeuf
    ## 142           Marius         Enjolras
    ## 143         Gavroche         Enjolras
    ## 144           Javert         Enjolras
    ## 145           Mabeuf         Enjolras
    ## 146          Valjean         Enjolras
    ## 147         Enjolras       Combeferre
    ## 148           Marius       Combeferre
    ## 149         Gavroche       Combeferre
    ## 150           Mabeuf       Combeferre
    ## 151         Gavroche        Prouvaire
    ## 152         Enjolras        Prouvaire
    ## 153       Combeferre        Prouvaire
    ## 154         Gavroche          Feuilly
    ## 155         Enjolras          Feuilly
    ## 156        Prouvaire          Feuilly
    ## 157       Combeferre          Feuilly
    ## 158           Mabeuf          Feuilly
    ## 159           Marius          Feuilly
    ## 160           Marius       Courfeyrac
    ## 161         Enjolras       Courfeyrac
    ## 162       Combeferre       Courfeyrac
    ## 163         Gavroche       Courfeyrac
    ## 164           Mabeuf       Courfeyrac
    ## 165          Eponine       Courfeyrac
    ## 166          Feuilly       Courfeyrac
    ## 167        Prouvaire       Courfeyrac
    ## 168       Combeferre          Bahorel
    ## 169         Gavroche          Bahorel
    ## 170       Courfeyrac          Bahorel
    ## 171           Mabeuf          Bahorel
    ## 172         Enjolras          Bahorel
    ## 173          Feuilly          Bahorel
    ## 174        Prouvaire          Bahorel
    ## 175           Marius          Bahorel
    ## 176           Marius          Bossuet
    ## 177       Courfeyrac          Bossuet
    ## 178         Gavroche          Bossuet
    ## 179          Bahorel          Bossuet
    ## 180         Enjolras          Bossuet
    ## 181          Feuilly          Bossuet
    ## 182        Prouvaire          Bossuet
    ## 183       Combeferre          Bossuet
    ## 184           Mabeuf          Bossuet
    ## 185          Valjean          Bossuet
    ## 186          Bahorel             Joly
    ## 187          Bossuet             Joly
    ## 188         Gavroche             Joly
    ## 189       Courfeyrac             Joly
    ## 190         Enjolras             Joly
    ## 191          Feuilly             Joly
    ## 192        Prouvaire             Joly
    ## 193       Combeferre             Joly
    ## 194           Mabeuf             Joly
    ## 195           Marius             Joly
    ## 196          Bossuet        Grantaire
    ## 197         Enjolras        Grantaire
    ## 198       Combeferre        Grantaire
    ## 199       Courfeyrac        Grantaire
    ## 200             Joly        Grantaire
    ## 201         Gavroche        Grantaire
    ## 202          Bahorel        Grantaire
    ## 203          Feuilly        Grantaire
    ## 204        Prouvaire        Grantaire
    ## 205           Mabeuf   MotherPlutarch
    ## 206       Thenardier        Gueulemer
    ## 207          Valjean        Gueulemer
    ## 208    MmeThenardier        Gueulemer
    ## 209           Javert        Gueulemer
    ## 210         Gavroche        Gueulemer
    ## 211          Eponine        Gueulemer
    ## 212       Thenardier            Babet
    ## 213        Gueulemer            Babet
    ## 214          Valjean            Babet
    ## 215    MmeThenardier            Babet
    ## 216           Javert            Babet
    ## 217         Gavroche            Babet
    ## 218          Eponine            Babet
    ## 219       Thenardier       Claquesous
    ## 220            Babet       Claquesous
    ## 221        Gueulemer       Claquesous
    ## 222          Valjean       Claquesous
    ## 223    MmeThenardier       Claquesous
    ## 224           Javert       Claquesous
    ## 225          Eponine       Claquesous
    ## 226         Enjolras       Claquesous
    ## 227           Javert     Montparnasse
    ## 228            Babet     Montparnasse
    ## 229        Gueulemer     Montparnasse
    ## 230       Claquesous     Montparnasse
    ## 231          Valjean     Montparnasse
    ## 232         Gavroche     Montparnasse
    ## 233          Eponine     Montparnasse
    ## 234       Thenardier     Montparnasse
    ## 235          Cosette        Toussaint
    ## 236           Javert        Toussaint
    ## 237          Valjean        Toussaint
    ## 238         Gavroche           Child1
    ## 239         Gavroche           Child2
    ## 240           Child1           Child2
    ## 241            Babet           Brujon
    ## 242        Gueulemer           Brujon
    ## 243       Thenardier           Brujon
    ## 244         Gavroche           Brujon
    ## 245          Eponine           Brujon
    ## 246       Claquesous           Brujon
    ## 247     Montparnasse           Brujon
    ## 248          Bossuet     MmeHucheloup
    ## 249             Joly     MmeHucheloup
    ## 250        Grantaire     MmeHucheloup
    ## 251          Bahorel     MmeHucheloup
    ## 252       Courfeyrac     MmeHucheloup
    ## 253         Gavroche     MmeHucheloup
    ## 254         Enjolras     MmeHucheloup

Definamos una propiedad de enlaces

``` r
E(g)$intermediacion <- edge.betweenness(g)
```

Y obtenemos el data.frame

``` r
mi_df_links <- get.data.frame(g, what = "edges")
mi_df_links %>% head()
```

    ##             from             to intermediacion
    ## 1         Myriel       Napoleon             76
    ## 2         Myriel MlleBaptistine              8
    ## 3         Myriel    MmeMagloire              8
    ## 4 MlleBaptistine    MmeMagloire              1
    ## 5         Myriel   CountessDeLo             76
    ## 6         Myriel       Geborand             76

## Clustering –\> Comunidades

Podemos usar la estructura de las redes para buscar un tipo especial de
clusters llamados *comunidades*. Estas son, intuitivamente, conjuntos de
nodos que comparten más enlaces entre ellos, que con nodos fuera del
conjunto.

Podemos usar diferentes algoritmos para detectar comunidades.

``` r
comm.louvain <- cluster_louvain(g)
comm.louvain
```

    ## IGRAPH clustering multi level, groups: 6, mod: 0.56
    ## + groups:
    ##   $`1`
    ##   [1] "Myriel"       "Napoleon"     "CountessDeLo" "Geborand"    
    ##   [5] "Champtercier" "Cravatte"     "Count"        "OldMan"      
    ##   
    ##   $`2`
    ##    [1] "Tholomyes"   "Listolier"   "Fameuil"     "Blacheville"
    ##    [5] "Favourite"   "Dahlia"      "Zephine"     "Fantine"    
    ##    [9] "Perpetue"    "Simplice"   
    ##   
    ##   $`3`
    ##   + ... omitted several groups/vertices

``` r
#podemos agregar la comunidad a la que pertenecen a cada nodo 
V(g)$comm.louvain <- membership(comm.louvain)
```

Tenemos varios algoritmos implementados

``` r
comm.infomap <- cluster_infomap(g)
comm.walktrp <- cluster_walktrap(g)
comm.fstgred <- cluster_fast_greedy(g)

V(g)$comm.infomap <- membership(comm.infomap)
V(g)$comm.walktrp <- membership(comm.walktrp)
V(g)$comm.fstgred <- membership(comm.fstgred)
```

Extraigamos una vez más la tabla de nodos

``` r
mi_df_nodos <- get.data.frame(x = g, 
                              what = "vertices")
mi_df_nodos %>% head()
```

    ##                id          label           name grado intermediacion
    ## Myriel          0         Myriel         Myriel    10            504
    ## Napoleon        1       Napoleon       Napoleon     1              0
    ## MlleBaptistine  2 MlleBaptistine MlleBaptistine     3              0
    ## MmeMagloire     3    MmeMagloire    MmeMagloire     3              0
    ## CountessDeLo    4   CountessDeLo   CountessDeLo     1              0
    ## Geborand        5       Geborand       Geborand     1              0
    ##                agrupamiento comm.louvain comm.infomap comm.walktrp
    ## Myriel           0.06666667            1            6            2
    ## Napoleon         0.00000000            1            6            2
    ## MlleBaptistine   1.00000000            3            6            2
    ## MmeMagloire      1.00000000            3            6            2
    ## CountessDeLo     0.00000000            1            6            2
    ## Geborand         0.00000000            1            6            2
    ##                comm.fstgred
    ## Myriel                    3
    ## Napoleon                  3
    ## MlleBaptistine            3
    ## MmeMagloire               3
    ## CountessDeLo              3
    ## Geborand                  3

## Hagamos unos plots

``` r
plot(g, 
     vertex.label.cex = 0.75,
     vertex.size = degree(g), 
     layout = layout_nicely
     )
```

![](redes_files/figure-gfm/unnamed-chunk-30-1.png)<!-- -->

Genera una visualización con el tamaño de los nodos proporcional al
grado, escogiendo heurísticamente el “mejor acomodo”, y con el tamaño de
las etiquetas más chico

``` r
plot(g, 
     vertex.label.cex = 0.75, 
     edge.curved = TRUE, 
     layout = layout_in_circle
     )
```

![](redes_files/figure-gfm/unnamed-chunk-31-1.png)<!-- -->

## Esta visualización acomoda los nodos en un círculo, y pone los enlaces curveados

También podemos plotear objetos de comunidades

``` r
plot(comm.walktrp, 
     g,  
     vertex.label.cex = 0.75, 
     layout = layout_nicely
     )
```

![](redes_files/figure-gfm/unnamed-chunk-32-1.png)<!-- -->

## ¿Podemos hacer esto más Tidy?

**Si podemos.** Usemos *tidygraph* para trabajar con las redes, y
*ggraph* para nuestros plots

``` r
library(tidygraph)
library(ggraph)
```

## Los datos de redes no son Tidy… pero los datos de nodos Y los datos de enlaces pueden ser Tidy por separado.

Convertimos nuestra red de igraph en una red de tidygraph

``` r
gt <- as_tbl_graph(g)
gt
```

    ## # A tbl_graph: 77 nodes and 254 edges
    ## #
    ## # An undirected simple graph with 1 component
    ## #
    ## # Node Data: 77 x 10 (active)
    ##      id label name  grado intermediacion agrupamiento comm.louvain
    ##   <dbl> <chr> <chr> <dbl>          <dbl>        <dbl>        <dbl>
    ## 1     0 Myri… Myri…    10            504       0.0667            1
    ## 2     1 Napo… Napo…     1              0       0                 1
    ## 3     2 Mlle… Mlle…     3              0       1                 3
    ## 4     3 MmeM… MmeM…     3              0       1                 3
    ## 5     4 Coun… Coun…     1              0       0                 1
    ## 6     5 Gebo… Gebo…     1              0       0                 1
    ## # … with 71 more rows, and 3 more variables: comm.infomap <dbl>,
    ## #   comm.walktrp <dbl>, comm.fstgred <dbl>
    ## #
    ## # Edge Data: 254 x 3
    ##    from    to intermediacion
    ##   <int> <int>          <dbl>
    ## 1     1     2             76
    ## 2     1     3              8
    ## 3     1     4              8
    ## # … with 251 more rows

## Ahora tenemos el verbo activate para acceder a los datos de nodos o de aristas, y trabajar con ellos

Agreguemos los agrupamientos de otro algoritmo de comunidades, *label
propagation*

``` r
gt <- gt %>% 
        activate(what = "nodes") %>% 
        mutate(comm.label_prop = group_label_prop())
gt
```

    ## # A tbl_graph: 77 nodes and 254 edges
    ## #
    ## # An undirected simple graph with 1 component
    ## #
    ## # Node Data: 77 x 11 (active)
    ##      id label name  grado intermediacion agrupamiento comm.louvain
    ##   <dbl> <chr> <chr> <dbl>          <dbl>        <dbl>        <dbl>
    ## 1     0 Myri… Myri…    10            504       0.0667            1
    ## 2     1 Napo… Napo…     1              0       0                 1
    ## 3     2 Mlle… Mlle…     3              0       1                 3
    ## 4     3 MmeM… MmeM…     3              0       1                 3
    ## 5     4 Coun… Coun…     1              0       0                 1
    ## 6     5 Gebo… Gebo…     1              0       0                 1
    ## # … with 71 more rows, and 4 more variables: comm.infomap <dbl>,
    ## #   comm.walktrp <dbl>, comm.fstgred <dbl>, comm.label_prop <int>
    ## #
    ## # Edge Data: 254 x 3
    ##    from    to intermediacion
    ##   <int> <int>          <dbl>
    ## 1     1     2             76
    ## 2     1     3              8
    ## 3     1     4              8
    ## # … with 251 more rows

O filtremos por la red para quedarnos con nodos que tengan al menos
cinco vecinos

``` r
gt_5 <- gt %>% 
        activate(what = "nodes") %>% 
        filter(grado > 5)
gt_5
```

    ## # A tbl_graph: 41 nodes and 194 edges
    ## #
    ## # An undirected simple graph with 1 component
    ## #
    ## # Node Data: 41 x 11 (active)
    ##      id label name  grado intermediacion agrupamiento comm.louvain
    ##   <dbl> <chr> <chr> <dbl>          <dbl>        <dbl>        <dbl>
    ## 1     0 Myri… Myri…    10           504        0.0667            1
    ## 2    11 Valj… Valj…    36          1624.       0.121             3
    ## 3    16 Thol… Thol…     9           116.       0.611             2
    ## 4    17 List… List…     7             0        1                 2
    ## 5    18 Fame… Fame…     7             0        1                 2
    ## 6    19 Blac… Blac…     7             0        1                 2
    ## # … with 35 more rows, and 4 more variables: comm.infomap <dbl>,
    ## #   comm.walktrp <dbl>, comm.fstgred <dbl>, comm.label_prop <int>
    ## #
    ## # Edge Data: 194 x 3
    ##    from    to intermediacion
    ##   <int> <int>          <dbl>
    ## 1     1     2          536  
    ## 2     3     4           19.8
    ## 3     3     5           19.8
    ## # … with 191 more rows

Ahora hagamos plots con *ggraph*

``` r
gt %>% 
  ggraph(layout = 'kk') + 
  geom_edge_link(aes(), show.legend = FALSE) + 
  geom_node_point(aes(colour = as.factor(comm.louvain)), size = 7) + 
  guides(colour=guide_legend(title="Louvain community")) +
  theme_graph()
```

![](redes_files/figure-gfm/unnamed-chunk-37-1.png)<!-- -->

``` r
gt %>% 
  ggraph(layout = 'kk') + 
  geom_edge_link(aes(), show.legend = FALSE) + 
  geom_node_point(aes(colour = as.factor(comm.infomap)), size = 7) + 
  guides(colour=guide_legend(title="Infomap community")) +
  theme_graph()
```

![](redes_files/figure-gfm/unnamed-chunk-37-2.png)<!-- -->

``` r
gt_5%>% 
  ggraph(layout = 'kk') + 
  geom_edge_link(aes(), show.legend = FALSE) + 
  geom_node_point(aes(colour = as.factor(comm.walktrp)), size = 7) + 
  guides(colour=guide_legend(title="Walktrap community")) +
  theme_graph()
```

![](redes_files/figure-gfm/unnamed-chunk-38-1.png)<!-- -->