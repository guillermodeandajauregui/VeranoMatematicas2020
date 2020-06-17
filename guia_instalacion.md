Guias de instalacion: paqueteria de analisis de redes
================
Guillermo de Anda - Jáuregui

Esta es una guía para instalar programas y paquetes útiles para el análisis de redes. 

[Cytoscape](#cytoscape)

[igraph](#igraph)

[networkx](#networkx)

[Otros](#otros)

Cytoscape
---------

### <https://cytoscape.org/>

Cytoscape es un programa *stand-alone* para analizar y visualizar redes. Algunas de las ventajas que tiene son:

-   Interfaz gráfica.
-   Fácil de usar: *point and click*, *WYSIWYG*
-   Expandible con *plug-ins*

##### instalación

1.  Entrar a <https://cytoscape.org/download.html>

2.  Descargar el instalador (la página detecta el sistema operativo).

-   Cytoscape requiere Java, versión 8. La versión 9 **NO FUNCIONA**
-   En el caso de Windows y Mac, el instalador puede instalar automáticamente la versión correcta de Java
-   En caso de Linux, es necesario tener la máquina Java instalada previamente. Revisar <https://www.java.com/en/download/linux_manual.jsp> y <https://www.java.com/en/download/help/linux_x64_install.xml#install> ... las máquinas virtuales Java libres de sistemas operativos Linux **PUEDEN O NO** funcionar (y fallarán si es una versión distinta a la 8).

1.  En Windows y Mac, se agregará un acceso directo en los lugares usuales (escritorio, menú inicio, Dock, etc...); dependiendo del sabor de Linux, esto puede suceder (por ejemplo, Ubuntu, Debian), o será necesario hacer una liga.

igraph
------

### <https://igraph.org/r/>

En el lenguaje de programación R, existen varias opciones para el análisis de redes. Algunas de ellas son los paquetes *network* y *graph* en Bioconductor. En este curso, daremos soporte para el análisis de redes utilizando el paquete *igraph*.

Algunas ventajas son:

-   Integrable a flujos de trabajo y análisis en R
-   Algoritmos de análisis de redes integrados
-   Expandible complementándose con otros paquetes.

La instalación es sencilla:

-   Tener R instalado en el equipo (<https://cran.r-project.org/>)
-   Dentro de R, correr

``` r
install.packages("igraph")
```

-   En Linux, es necesario contar con un compilador de C y C++; estos pueden obtenerse con el paquete *build-essential* de la distribución.
-   También en Linux, es muy recomendable instalar las bibliotecas para XML *lixml2* y *libxml2-dev* de los repositorios de la distribución.
-   Para cargar el paquete, correr

``` r
library("igraph")
```

-   Se recomienda también instalar dentro de R los paquetes *tcltk*, *rgl* y *ape*.

``` r
install.packages("tcltk")
install.packages("rgl")
install.packages("ape")
```

Es posible integrar igraph a un ambiente tipo *Tidyverse* usando los paquetes *tidygraph* y *ggraph*. Para instalarlos se necesita correr

``` r
install.packages("tidyverse") #instala los paquetes Tidy básicos 
install.packages("tidygraph") #para analizar las redes con sintaxis Tidy
install.packages("ggraph") #para generar visualizaciones tipo ggplot
```

Pueden consultar

networkx
--------

### <https://networkx.github.io/>

En Python es posible analizar redes utilizando el paquete *NetworkX*. Este es un paquete sumamente flexible que permite el análisis de redes complejas.

-   NetworkX requiere Python 3.5, 3.6, or 3.7. Los desarrolladores recomiendan trabajar en el ecosistema de Python Científico (<https://scipy.org/install.html>). \*Para los usuarios de Windows, se recomienda usar una distribución como Anaconda (<https://www.anaconda.com/distribution/>)
-   El mecanismo de instalación requiere el uso de *pip* (si no está instalado, seguir <https://pip.pypa.io/en/stable/installing/>).
-   Ya con pip, la instalación requiere

``` bash
pip install networkx
```

-   Adicionalmente, se recomienda la instalación de paquetes complementarios: *NumPy*, *SciPy*, *pandas*, *Matplotlib*, *PyGraphviz*, *PyYAML*, *gdal*, *lxml*. Esto se puede realizar en un solo comando vía pip con

``` bash
pip install networkx[all]
```

-   *Nota:* Algunos paquetes (como *scipy* y *gdal*) pueden requerir instalar compiladores adicionales en algunos sistemas operativos.

``` python
import networkx as nx
```

Pueden consultar el manual de instalación en <https://networkx.github.io/documentation/latest/install.html>

Otros
-----

Existen otros programas para el análisis de redes:

-   Gephi <https://gephi.org/>
-   Pajek
-   UCINet

Entre otras. No podemos darles soporte en el uso de estas herramientas, pero se encuentran disponibles por si tienen interés.
