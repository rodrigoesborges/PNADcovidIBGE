-   [O Pacote PNADcIBGE](#o-pacote-pnadcibge)
    -   [Instalação do Pacote](#instalacao-do-pacote)
    -   [Importação *Online*](#importacao-online)
        -   [Microdados trimestrais](#microdados-trimestrais)
        -   [Microdados anuais](#microdados-anuais)
    -   [Importação *offline*](#importacao-offline)
        -   [Estimando Totais](#Totais)
        -   [Estimando Médias](#Media)

O Pacote PNADcIBGE
==================

O Pacote **PNADcIBGE** foi desenvolvido para facilitar o *download*, importação e análise dos dados amostrais da Pesquisa Nacional por Amostra de Domicílios Contínua realizada pelo Instituto Brasileiro de Geografia e Estatística - IBGE.

A PNAD Contínua possui dois tipos de microdados:

-   Trimestral, que contém a parte básica investigada pela pesquisa, contendo variáveis conjunturais de mercado de trabalho referentes a um trimestre civil;
-   Anual, que contém temas estruturais específicos investigados na pesquisa para um ano civil.

Maiores informações sobre a pesquisa e os temas investigados podem ser encontrados no [site oficial do IBGE.](https://www.ibge.gov.br/estatisticas-novoportal/sociais/trabalho/17270-pnad-continua.html)

Através do objeto criado com este pacote, é possível utilizar o pacote *survey* \[@Survey1\] para realizar análises considerando o efeito do esquema de seleção utilizado no plano amostral complexo da pesquisa e calcular corretamente as medidas de erro das estimativas, considerando o estimador de pós-estratificação utilizado na pesquisa.

### Instalação do Pacote

O Pacote está diponível no CRAN do R, onde pode ser acessada sua [documentação](https://CRAN.R-project.org/package=PNADcIBGE).
É importante sempre conferir se a versão instalada em seu computador é a mais recente disponível no CRAN.
A instalação do pacote pode ser feita pelo seguinte comando:

`r   install.packages("PNADcIBGE")` Antes de utilizar o pacote é necessário carregá-lo no R através do comando:

`r   library(PNADcIBGE)`

------------------------------------------------------------------------

\# Importação de Microdados

Para a importação dos microdados da PNAD Contínua, há duas opções. A primeira é uma opção *online*, que exige que o computador esteja conectado à Internet. A segunda opção não necessita de conexão a Internet, e é utilizada para a leitura de microdados que estejam no disco rígido.

### Importação *Online*

A função `get_pnadc` permite o download, leitura, rotulação e criação do objeto do plano amostral da pesquisa.
Esta função pode ser usada para microdados trimestrais e anuais.

``` r
help("get_pnadc")
```

#### Microdados trimestrais

A importação dos microdados anuais através da função `get_pnadc` é muito simples. Basta indicar o ano e o trimestre dos dados desejados nos argumentos da função. Abaixo um exemplo de como importar os microdados do 2º trimestre de 2017:

`r   dadosPNADc = get_pnadc(year = 2017, quarter = 2)`

Apenas com esse comando, o microdado da pesquisa já está disponível no objeto `pnadc022017` para análise.

Além dos argumentos `year` e `quarter`, que indicam, respectivamente, o ano e o trimestre dos microdados a serem baixados, a função `get_pnadc` ainda possui outros quatro argumentos que podem ser modificados no download:

-   `design`: Um argumento lógico que indica se a função deve retornar um objeto do plano amostral para análise com o pacote *survey*. Caso `design=FALSE`, a função retorna apenas um data-frame com os microdados. É **altamente recomendado** que mantenha essa opção como `TRUE`, caso contrário suas análises poderão ser feitas de forma incorreta;

-   `vars`: Este argumento recebe um vetor de caracteres com o nome das variáveis a serem baixadas. Caso nenhuma variável seja passada, todas as variáveis disponíveis na pesquisa são baixadas. É útil caso deseje trabalhar com poucas variáveis, pois assim o objeto ocupará um espaço menor na memória do computador;

-   `labels`: Um argumento lógico que indica se os níveis das variáveis categóricas devam ser rotuladas de acordo com o dicionário da pesquisa. O `default` é rotulá-los;

-   `savedir`: O endereço onde devem ser salvos os arquivos baixados. Padrão é utilizar uma pasta temporária.

Por exemplo, um usuário que deseje trabalhar apenas com as variáveis de **Condição em relação à força de trabalho** (VD4001) e **Condição de ocupação** (VD4002) do 2º trimestre de 2017, pode utilizar o argumento `vars` parece seleiconar apenas estas variáveis:

`r   dadosPNADc <- get_pnadc(year = 2017, quarter = 2, vars=c("VD4001","VD4002"))`

Através do código abaixo, podemos ver que o objeto retornado pela função é um `survey.design`, objeto utilizado para análises de dados amostrais complexos através do pacote *survey*.

``` r
dadosPNADc
```

    ## Stratified 1 - level Cluster Sampling design (with replacement)
    ## With (15085) clusters.
    ## survey::postStratify(design = data_pre, strata = ~posest, population = popc.types)

``` r
class(dadosPNADc)
```

    ## [1] "survey.design2" "survey.design"

Caso o usuário não queira trabalhar com este objeto, ele pode escolher a opção `design = FALSE` para baixar os microdados brutos:

`r   dadosPNADc_brutos <- get_pnadc(year = 2017, quarter = 2, vars = c("VD4001", "VD4002"), design = FALSE)` O Objeto resultante é um data-frame com as variáveis selecionadas e as variáveis relacionadas ao processo de amostragem:

``` r
dadosPNADc_brutos
```

<script data-pagedtable-source type="application/json">
  </script>

``` r
class(dadosPNADc_brutos)
```

    ## [1] "tbl_df"     "tbl"        "data.frame"

Também é possível baixar os dados sem incluir os rótulos dos níveis, através do argumento `labels`.

``` r
dadosPNADc_brutos_sem <- get_pnadc(year = 2017, quarter = 2, vars = c("VD4001", "VD4002"), design = FALSE, labels = FALSE)
```

Perceba que agora os níveis das categorias são representados por números:

``` r
dadosPNADc_brutos_sem
```

<script data-pagedtable-source type="application/json">
  </script>

#### Microdados anuais

Além dos microdados trimestrais, a função `get_pnadc` também permite a importação dos microdados anuais.
A relação dos temas investigados anualmente, juntamente com a entrevista referente aaos microdados, pode ser acessada na [página da pesquisa](https://www.ibge.gov.br/estatisticas-novoportal/sociais/trabalho/17270-pnad-continua.html).

Neste exemplo, mostraremos como importar os dados da 1ª visita de 2016, que compreende os temas:

-   Características Adicionais do Mercado de Trabalho;
-   Características Gerais dos Moradores;
-   Características Gerais dos Domicílios.
-   Rendimentos de Outras Fontes

Para a importação do microdados de uma entrevista basta informar o número da entrevista desejada no parâmetro `interview`:

`r   dadosPNADc_anual <- get_pnadc(year = 2016, interview = 1)   dadosPNADc_anual`

O objeto retornado é um `survey.design` e os argumentos `vars`, `labels`, `design` e `savedir` podem ser utilizados da mesma forma que para os microdados trimestral.

### Importação *offline*

Caso não seja possível utilizar a importação *online* através da função `get_pnadc`, é possível criar o mesmo objeto para análise com arquivos que estejam em disco. Para isto, são utilzadas três funções:

-   `read_pnadc`: Para a leitura do arquivo .txt dos microdados;
-   `pnadc_labeller`: Opcional. Coloca os rótulos dos níveis nas variáveis categóricas;
-   `pnadc_design`: Cria o objeto do plano amostral para a análise com o pacote `survey`.

Para utilizar estas funções, primeiramente é necessário ter os microdados e sua documentação no disco e extraídos dos arquivos compactados, se for o caso.
Estes arquivos podem ser baixados diretamente do [FTP do IBGE](ftp://ftp.ibge.gov.br/Trabalho_e_Rendimento/Pesquisa_Nacional_por_Amostra_de_Domicilios_continua/).

Para a função `read_pnadc` são utilizados os arquivos de texto contendo os microdados e o input para sas.

**Exemplo de leitura** para o 2º trimestre de 2017:

`r   dados_pnadc <- read_pnadc("PNADC_022017.txt", "Input_PNADC_trimestral.txt")` Assim como na importação *online*, pode ser utilizado o argumento `vars` para definir quais variáveis serão lidas.

A função `pnadc_labeller` utiliza o objeto criado pela função `read_pnadc` e o arquivo de dicionário das variáveis presente na documentação da pesquisa.

``` r
dados_pnadc <- pnadc_labeller(dados_pnadc, "dicionário_das_variáveis_PNAD_Continua_microdados.xls")
```

A utilização de `pnadc_labeller` é opcional e não interfere nos resultados obtidos.

A criação do **objeto do plano amostral** é feita diretamente pela função `pnadc_design` aplicada no objeto que contém os microdados:

`r   dados_pnadc <- pnadc_design(dados_pnadc)`

O objeto resultante é o mesmo retornado pela função `get_pnadc`e pode ser utilizado para análises com o `survey`.

A importação *offline* é feita da mesma forma tanto para microdados mensais quanto anuais.

------------------------------------------------------------------------

\# Análise com pacote `survey`

Devido ao plano amostral utilziado na PNAD Contínua, é necessário que sejam utilizadas ferramentas específicas para a análise de dados amostrais complexos. O pacote [*survey*](https://cran.r-project.org/web/packages/survey/index.html) é um pacote criado especificamente para análise e modelagem de dados provenientes de pesquisas com estes tipos de planos amostrais. Maiores detalhes sobre o pacote podem ser encontrados no [site do autor](http://faculty.washington.edu/tlumley/old-survey/index.html).

Para os exemplos, utilizaremos microdados da PNAD Contínua do 2º trimestre de 2017, com algumas variáveis selecionadas de acordo com a tabela abaixo:

<table>
<colgroup>
<col width="7%" />
<col width="92%" />
</colgroup>
<thead>
<tr class="header">
<th>Variável</th>
<th>Descrição</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>UF</td>
<td>Unidade da Federação</td>
</tr>
<tr class="even">
<td>V2007</td>
<td>Sexo</td>
</tr>
<tr class="odd">
<td>V2009</td>
<td>Idade do morador na data de referência</td>
</tr>
<tr class="even">
<td>V2010</td>
<td>Cor ou raça</td>
</tr>
<tr class="odd">
<td>V3007</td>
<td>Já concluiu algum outro curso de graduação?</td>
</tr>
<tr class="even">
<td>VD3001</td>
<td>Nível de instrução mais elevado alcançado (pessoas de 5 anos ou mais de idade)</td>
</tr>
<tr class="odd">
<td>VD4001</td>
<td>Condição em relação à força de trabalho na semana de referência para pessoas de 14 anos ou mais de idade</td>
</tr>
<tr class="even">
<td>VD4002</td>
<td>Condição de ocupação na semana de referência para pessoas de 14 anos ou mais de idade</td>
</tr>
<tr class="odd">
<td>VD4019</td>
<td>Rendimento mensal habitual de todos os trabalhos para pessoas de 14 anos ou mais de idade (apenas para pessoas que receberam em dinheiro, produtos ou mercadorias em qualquer trabalho)</td>
</tr>
<tr class="even">
<td>VD4031</td>
<td>Horas habitualmente trabalhadas por semana em todos os trabalhos para pessoas de 14 anos ou mais de idade</td>
</tr>
</tbody>
</table>

Importando os dados

``` r
variaveis_selecionadas <- c("UF", "V2007", "V2009", "V2010", "V3007", "VD3001", "VD4001", "VD4002", "VD4019", "VD4031")
dadosPNADc <- get_pnadc(year = 2017, quarter = 2, vars = variaveis_selecionadas)
```

O objeto `dadosPNADC` vai ser utilizado com o pacote `survey` para a análise dos microdados.

Para iniciar a análise dos dados é necessário carregar o pacote `survey`

``` r
library(survey)
```

    ## Warning: package 'survey' was built under R version 3.4.4

    ## Warning: package 'Matrix' was built under R version 3.4.3

#### Estimando Totais

A função do pacote para a estimação de totais populacionais é a `svytotal`. Sua sintaxe precisa de três parâmetros principais

-   O nome da variável que se deseja calcular o total, precedido por um `~`;
-   O nome do objeto do plano amostral;
-   A opção `na.rm = T`, que remove as observações onde a variável é não-aplicável.

##### Variáveis Numéricas

Ela pode ser utilizada para a estimação do **total de uma variável numérica**, como a renda mensal habitual.

``` r
totalrenda <- svytotal(~VD4019, dadosPNADc, na.rm = T)
totalrenda
```

    ##               total         SE
    ## VD4019 185096435765 3215376947

Além da estimativa do total da renda habitual, o comando também retorna o **erro padrão (SE)** dessa estimativa.
A partir desse resultado, também é possível calcular **coeficientes de variação** e **intervalos de confiança** para a estimativa:

`r   cv(totalrenda)`

`##          VD4019   ## VD4019 0,017371`

`r   confint(totalrenda) #intervalo de confiança de 95% (padrão)`

`##               2,5 %       97,5 %   ## VD4019 178794412753 191398458777`

`r   confint(totalrenda, level= .99) #intervalo de confiança de 99%`

`##               0,5 %       99,5 %   ## VD4019 176814173604 193378697926` \#\#\#\#\# Variáveis Categóricas {\#Categ}

Também é possível estimar **totais populacionais de categorias**, utilizando variáveis categóricas, como o sexo:

`r   totalsexo <- svytotal(~V2007, dadosPNADc, na.rm = T)   totalsexo`

`##                 total     SE   ## V2007Homem  100034274 145990   ## V2007Mulher 106848455 145990`

E estimar o total de mais de uma variável categórica no mesmo código, separando com o operador `+`:

`r   totalsexoraca <- svytotal(~V2007 + V2010, dadosPNADc, na.rm = T)   totalsexoraca`

`##                   total     SE   ## V2007Homem    100034274 145990   ## V2007Mulher   106848455 145990   ## V2010Branca    90333286 420783   ## V2010Preta     16850706 203617   ## V2010Amarela    1145693  65273   ## V2010Parda     98034400 390188   ## V2010Indígena    496364  30298   ## V2010Ignorado     22281   7068`

Também é possível estimar o total resultante do **cruzamento de duas ou mais variáveis** categóricas, com a função `interaction`:

`r   totalsexoEraca <- svytotal(~ interaction(V2007, V2010), dadosPNADc, na.rm = T)   ftable(totalsexoEraca)`

    ##                V2007      Homem     Mulher
    ## V2010                                     
    ## Branca   total       42738909,8 47594376,2
    ##          SE            224955,0   247335,4
    ## Preta    total        8381150,0  8469555,8
    ##          SE            107647,6   120905,6
    ## Amarela  total         527450,7   618242,0
    ##          SE             33191,8    38383,8
    ## Parda    total       48134169,2 49900230,5
    ##          SE            216322,9   224877,5
    ## Indígena total         241584,1   254779,7
    ##          SE             17169,6    16562,3
    ## Ignorado total          11010,4    11270,7
    ##          SE              4452,5     3550,4

A função `ftable` possibilita uma saída mais organizada da tabela no caso de cruzamento de variáveis. Para todos esses objetos também podem ser utilizadas as funções `cv` e `confint`.

#### Estimando Médias

A média de uma variável numérica é estimada através da função `svymean`, que possui uma sintaxe idêntica à [`svytotal`](#Totais). O [exemplo do total da renda habitual](#Numer) pode ser facilmente reescrito para médias:

    ```r
    mediarenda <- svymean(~VD4019, dadosPNADc, na.rm = T)
    mediarenda
    ```

    ```
    ##        mean   SE
    ## VD4019 2104 35,6
    ```

    E podemos calcular intervalos de confiança e coeficientes de variação da mesma forma:
      
      ```r
          cv(mediarenda)
      ```
      
      ```
      ##          VD4019
      ## VD4019 0,016947
      ```
      
      ```r
          confint(mediarenda)
      ```
      
      ```
      ##        2,5 % 97,5 %
      ## VD4019  2034 2173,7
      ```

    #### Estimando Proporções

    Utilizando variáveis categóricas, é possível estimar a **frequência relativa** de cada categoria.
    Isso pode ser feito também através da função `svymean`, com uma sintaxe análoga à utilizada para estimar os [totais das categorias.](#Categ)
      
      Para estimar a proporção de cada sexo na população:
        
        ```r
              propsexo <- svymean(~V2007, dadosPNADc, na.rm = T)
              propsexo
        ```
        
        ```
        ##              mean SE
        ## V2007Homem  0,484  0
        ## V2007Mulher 0,516  0
        ```
      
      Da mesma forma, podemos estimar a proporção de mais de uma variável:
        
        ```r
              propsexoraca <- svymean(~V2007 + V2010, dadosPNADc, na.rm = T)
              propsexoraca
        ```
        
        ```
        ##                   mean SE
        ## V2007Homem    0,483531  0
        ## V2007Mulher   0,516469  0
        ## V2010Branca   0,436640  0
        ## V2010Preta    0,081451  0
        ## V2010Amarela  0,005538  0
        ## V2010Parda    0,473865  0
        ## V2010Indígena 0,002399  0
        ## V2010Ignorado 0,000108  0
        ```
      
      E estimando a proporção de um cruzamento de duas ou mais variáveis com a função `interaction`
      
      ```r
      propsexoEraca <- svymean(~ interaction(V2007, V2010), dadosPNADc, na.rm = T)
      ftable(propsexoEraca)
      ```
      
      
      ```
      ##               V2007       Homem      Mulher
      ## V2010                                      
      ## Branca   mean       0,206585199 0,230054855
      ##          SE         0,001087355 0,001195534
      ## Preta    mean       0,040511598 0,040938922
      ##          SE         0,000520332 0,000584416
      ## Amarela  mean       0,002549516 0,002988369
      ##          SE         0,000160438 0,000185534
      ## Parda    mean       0,232664029 0,241200562
      ##          SE         0,001045631 0,001086981
      ## Indígena mean       0,001167735 0,001231518
      ##          SE         0,000082992 0,000080056
      ## Ignorado mean       0,000053220 0,000054479
      ##          SE         0,000021522 0,000017161
      ```
      Neste caso, as frequências relativas calculadas são em relação ao total, não a uma marginal.
      Na seção de [Análise por domínios](#Domin) são apresentados como calcular frequências relativas em relações às marginais.
        
        #### Estimando Razões {#razoes}
        
        Além das proporções, também é possível estimar razões entre duas variáveis.
        Um exemplo de razão é a **taxa de desocupação** divulgada pelo IBGE: ela é a razão entre o total de pessoas desocupadas pelo total de pessoas na força de trabalho.
        
        A função para estimar razões é a `svyratio`. Sua sintaxe utiliza quatro argumentos: a variável cujo total estará no numerador, a variável cujo total estará no denominador, o objeto do plano amostral e a opção de remover valores não aplicáveis.
        
        Um exemplo para a estimativa dessa taxa é
        
        
        ```r
        txdesocup <- svyratio(~VD4002 == "Pessoas desocupadas",~VD4001 == "Pessoas na força de trabalho", dadosPNADc, na.rm = T)
        txdesocup
        ```
        
        ```
        ## Ratio estimator: svyratio.survey.design2(~VD4002 == "Pessoas desocupadas", ~VD4001 == 
        ##     "Pessoas na força de trabalho", dadosPNADc, na.rm = T)
        ## Ratios=
        ##                                 VD4001 == "Pessoas na força de trabalho"
        ## VD4002 == "Pessoas desocupadas"                                  0,13002
        ## SEs=
        ##                                 VD4001 == "Pessoas na força de trabalho"
        ## VD4002 == "Pessoas desocupadas"                                0,0011407
        ```
        Cálculos de coeficiente de variação e intervalos de confiança para essa taxa:
          
          ```r
                  cv(txdesocup)
          ```
          
          ```
          ##                                 VD4001 == "Pessoas na força de trabalho"
          ## VD4002 == "Pessoas desocupadas"                                0,0087733
          ```
          
          ```r
                  confint(txdesocup)
          ```
          
          ```
          ##                                                                            2,5 %
          ## VD4002 == "Pessoas desocupadas"/VD4001 == "Pessoas na força de trabalho" 0,12778
          ##                                                                           97,5 %
          ## VD4002 == "Pessoas desocupadas"/VD4001 == "Pessoas na força de trabalho" 0,13225
          ```
        
        #### Estimando Medianas e Quantis
        
        Medianas e quantis de variáveis numéricas são estimados através da função `svyquantile`. Além dos argumentos utilizados para [estimar a média](#Media), é necessário definir os quantis a serem calculados no argumento `quantiles`.
          
          Para calcular a mediana, basta utilizar `quantiles = .5`:
            
            ```r
                      medianarenda <- svyquantile(~VD4019, dadosPNADc, quantiles = .5, na.rm = T)
                      medianarenda
            ```
            
            ```
            ##         0,5
            ## VD4019 1200
            ```
          Diferentemente das anteriores, a função svyquantile não retorna o erro como `default` o erro padrão das estimativas.
          Para obtê-los, é necessário utilizar o parâmetro `ci = TRUE`, que estima intervalos de confiança e a partir dele, utilziar as funções `SE` e `cv` para estimar o erro padrão e coeficiente de variação, respectivamente.
          
          
          ```r
          medianarenda <- svyquantile(~VD4019, dadosPNADc, quantiles = .5, na.rm = T, ci = TRUE)
          medianarenda
          ```
          
          ```
          ## $quantiles
          ##         0,5
          ## VD4019 1200
          ## 
          ## $CIs
          ## , , VD4019
          ## 
          ##           0,5
          ## (lower 1200,0
          ## upper) 1230,8
          ```
          
          ```r
          SE(medianarenda)
          ```
          
          ```
          ## [1] 7,858
          ```
          
          ```r
          cv(medianarenda)
          ```
          
          ```
          ##    VD4019 
          ## 0,0065484
          ```
          
          
          Além disso, é possível calcular vários quantis simultaneamente, colocando um vetor na opção quantiles.  
          No código abaixo, estimamos, além da mediana, o primeiro e nono decis e primeiro e terceiro quartis:
            
            ```r
                      quantisrenda <- svyquantile(~VD4019, dadosPNADc, quantiles = c(.1,.25,.5,.75,.9), na.rm = T)
                      quantisrenda
            ```
            
            ```
            ##        0,1 0,25  0,5 0,75  0,9
            ## VD4019 460  937 1200 2000 4000
            ```
          
          #### Estimação para um Domínio
          
          Muitas vezes queremos fazer uma análise para um domínio específico da população. Com o survey, esse domínio pode ser selecionado utilizando a função `subset` no objeto do plano amostral, aplicando a condição que define aquele domínio. É **necessário** que a seleção desse domínio nos dados seja feita somente **após** a criação do objeto que define o plano amostral, caso contrário podem ser obtidos resultados incorretos. 
          
          Para a seleção dos domínios podem ser utilizadas **condicionais com igualdade**, como por exemplo para estimar a renda habitual média de mulheres:
            
            ```r
                      mediarendaM <- svymean(~VD4019, subset(dadosPNADc, V2007 == "Mulher")  , na.rm = T)
                      mediarendaM
            ```
            
            ```
            ##        mean   SE
            ## VD4019 1787 19,6
            ```
          Ou **condicionais com desigualdade**, como por exemplo estimar a [taxa de desocupação](#razoes) para pessoas com idade igual ou superior a 25 anos:
            
            ```r
            txdesocup25 <- svyratio(~VD4002 == "Pessoas desocupadas",~VD4001 == "Pessoas na força de trabalho", subset(dadosPNADc, V2009>=25) , na.rm = T)
            txdesocup25
            ```
            
            ```
            ## Ratio estimator: svyratio.survey.design2(~VD4002 == "Pessoas desocupadas", ~VD4001 == 
            ##     "Pessoas na força de trabalho", subset(dadosPNADc, V2009 >= 
            ##     25), na.rm = T)
            ## Ratios=
            ##                                 VD4001 == "Pessoas na força de trabalho"
            ## VD4002 == "Pessoas desocupadas"                                 0,094094
            ## SEs=
            ##                                 VD4001 == "Pessoas na força de trabalho"
            ## VD4002 == "Pessoas desocupadas"                               0,00099285
            ```
            Além disso, é possível utilizar **múltiplas condições** com os operados lógicos `&` ("e") e `|`("ou"), para estimar a frequência relativa de cada nível de instrução para homens pardos com mais de 30 anos, por exemplo.
            
            
            ```r
            nivelinstrHP30 <- svymean(~VD3001, subset(dadosPNADc, V2007 == "Homem" & V2010 == "Parda" & V2009 > 30), na.rm = T)
            nivelinstrHP30
            ```
            
            ```
            ##                                                  mean SE
            ## VD3001Sem instrução e menos de 1 ano de estudo 0,1492  0
            ## VD3001Fundamental incompleto ou equivalente    0,4004  0
            ## VD3001Fundamental completo ou equivalente      0,0870  0
            ## VD3001Médio incompleto ou equivalente          0,0421  0
            ## VD3001Médio completo ou equivalente            0,2297  0
            ## VD3001Superior incompleto ou equivalente       0,0209  0
            ## VD3001Superior completo                        0,0709  0
            ```
            
            Além disso, caso deseje realizar diversas análises para um mesmo domínio, é possível criar um objeto que contenha apenas os dados daquele domínio a partir de um `subset` do objeto original do plano amostral.
            
            
            ```r
            dadosPNADc_mulheres <- subset(dadosPNADc, V2007 == "Mulher")
            dadosPNADc_mulheres
            ```
            
            ```
            ## Stratified 1 - level Cluster Sampling design (with replacement)
            ## With (15085) clusters.
            ## subset(dadosPNADc, V2007 == "Mulher")
            ```
            
            As análises então podem ser feitas utilizando esse novo objeto ao invés do objeto que contém todos os dados.
            
            #### Estimação para Vários Domínios.
            
            Em outros casos há interesse em estimar quantidades de interesse para diversos domínios mutuamente exclusivos, a fim de possibilitar comparações. Para isto, podemos utilizar a função `svyby`.
            
            Para utilizá-la são necessários os seguinte argumentos
            
            * A variável da qual se deseja calcular a quantidade
            * A variável que define os domínios
            * O objeto do plano amostral
            * A função utilizada para calcular a quantidade de interesse (`svytotal`,`svymean`,`svyratio`,`svyquantile`,...)
            
            Se desejamos estimar a frequência relativa de homens e mulheres em cada nível de instrução, usamos o seguinte código:
              
              ```r
                          freqSexoInstr <- svyby(~V2007, ~VD3001, dadosPNADc, svymean, na.rm = T)
                          freqSexoInstr
              ```
            <div data-pagedtable="false">
              <script data-pagedtable-source type="application/json">
            {"columns":[{"label":[""],"name":["_rn_"],"type":[""],"align":["left"]},{"label":["V2007Homem"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["V2007Mulher"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["se.V2007Homem"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["se.V2007Mulher"],"name":[4],"type":["dbl"],"align":["right"]}],"data":[{"1":"0,49544","2":"0,50456","3":"0,0023635","4":"0,0023635","_rn_":"Sem instrução e menos de 1 ano de estudo"},{"1":"0,50607","2":"0,49393","3":"0,0012966","4":"0,0012966","_rn_":"Fundamental incompleto ou equivalente"},{"1":"0,50044","2":"0,49956","3":"0,0033118","4":"0,0033118","_rn_":"Fundamental completo ou equivalente"},{"1":"0,50667","2":"0,49333","3":"0,0037091","4":"0,0037091","_rn_":"Médio incompleto ou equivalente"},{"1":"0,46461","2":"0,53539","3":"0,0017642","4":"0,0017642","_rn_":"Médio completo ou equivalente"},{"1":"0,46255","2":"0,53745","3":"0,0045460","4":"0,0045460","_rn_":"Superior incompleto ou equivalente"},{"1":"0,40587","2":"0,59413","3":"0,0025878","4":"0,0025878","_rn_":"Superior completo"}],"options":{"columns":{"min":{},"max":[5]},"rows":{"min":[10],"max":[10]},"pages":{}}}
              </script>
            </div>
            
            Caso desejemos estimar o contrário, a frequência relativa de cada nível de instrução para cada sexo, basta inverter as variáveis correspondetes na sintaxe
            
            ```r
            freqInstrSexo <- svyby(~VD3001, ~V2007, dadosPNADc, svymean, na.rm = T)
            freqInstrSexo
            ```
            <div data-pagedtable="false">
              <script data-pagedtable-source type="application/json">
            {"columns":[{"label":[""],"name":["_rn_"],"type":[""],"align":["left"]},{"label":["VD3001Sem instrução e menos de 1 ano de estudo"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["VD3001Fundamental incompleto ou equivalente"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["VD3001Fundamental completo ou equivalente"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["VD3001Médio incompleto ou equivalente"],"name":[4],"type":["dbl"],"align":["right"]},{"label":["VD3001Médio completo ou equivalente"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["VD3001Superior incompleto ou equivalente"],"name":[6],"type":["dbl"],"align":["right"]},{"label":["VD3001Superior completo"],"name":[7],"type":["dbl"],"align":["right"]},{"label":["se.VD3001Sem instrução e menos de 1 ano de estudo"],"name":[8],"type":["dbl"],"align":["right"]},{"label":["se.VD3001Fundamental incompleto ou equivalente"],"name":[9],"type":["dbl"],"align":["right"]},{"label":["se.VD3001Fundamental completo ou equivalente"],"name":[10],"type":["dbl"],"align":["right"]},{"label":["se.VD3001Médio incompleto ou equivalente"],"name":[11],"type":["dbl"],"align":["right"]},{"label":["se.VD3001Médio completo ou equivalente"],"name":[12],"type":["dbl"],"align":["right"]},{"label":["se.VD3001Superior incompleto ou equivalente"],"name":[13],"type":["dbl"],"align":["right"]},{"label":["se.VD3001Superior completo"],"name":[14],"type":["dbl"],"align":["right"]}],"data":[{"1":"0,11274","2":"0,36383","3":"0,087581","4":"0,073769","5":"0,22715","6":"0,040725","7":"0,094208","8":"0,00086263","9":"0,0016377","10":"0,00082315","11":"0,00081633","12":"0,0014069","13":"0,00064182","14":"0,0016268","_rn_":"Homem"},{"1":"0,10669","2":"0,32998","3":"0,081240","4":"0,066744","5":"0,24323","6":"0,043971","7":"0,128148","8":"0,00083002","9":"0,0015366","10":"0,00081711","11":"0,00072719","12":"0,0013240","13":"0,00061161","14":"0,0017207","_rn_":"Mulher"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
              </script>
            </div>
            
            Também pode ser utilizado para estimar a renda média habitual por unidade da federação
            
            ```r
            mediaRendaUF <- svyby(~VD4019, ~UF, dadosPNADc, svymean, na.rm = T)
            mediaRendaUF
            ```
            <div data-pagedtable="false">
              <script data-pagedtable-source type="application/json">
            {"columns":[{"label":[""],"name":["_rn_"],"type":[""],"align":["left"]},{"label":["VD4019"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["se"],"name":[2],"type":["dbl"],"align":["right"]}],"data":[{"1":"1732,2","2":"70,651","_rn_":"Rondônia"},{"1":"1732,4","2":"81,271","_rn_":"Acre"},{"1":"1751,7","2":"118,846","_rn_":"Amazonas"},{"1":"2040,2","2":"132,552","_rn_":"Roraima"},{"1":"1390,6","2":"41,405","_rn_":"Pará"},{"1":"2376,0","2":"281,977","_rn_":"Amapá"},{"1":"1793,4","2":"78,702","_rn_":"Tocantins"},{"1":"1257,8","2":"83,477","_rn_":"Maranhão"},{"1":"1384,2","2":"82,958","_rn_":"Piauí"},{"1":"1361,4","2":"54,407","_rn_":"Ceará"},{"1":"1591,4","2":"118,991","_rn_":"Rio Grande do Norte"},{"1":"1515,9","2":"80,638","_rn_":"Paraíba"},{"1":"1666,7","2":"96,913","_rn_":"Pernambuco"},{"1":"1321,3","2":"48,243","_rn_":"Alagoas"},{"1":"1623,2","2":"107,601","_rn_":"Sergipe"},{"1":"1438,5","2":"67,478","_rn_":"Bahia"},{"1":"1820,2","2":"40,932","_rn_":"Minas Gerais"},{"1":"1991,7","2":"58,234","_rn_":"Espírito Santo"},{"1":"2268,0","2":"54,852","_rn_":"Rio de Janeiro"},{"1":"2743,3","2":"138,285","_rn_":"São Paulo"},{"1":"2228,5","2":"50,512","_rn_":"Paraná"},{"1":"2271,8","2":"34,369","_rn_":"Santa Catarina"},{"1":"2329,3","2":"54,224","_rn_":"Rio Grande do Sul"},{"1":"2134,3","2":"73,053","_rn_":"Mato Grosso do Sul"},{"1":"2068,5","2":"60,298","_rn_":"Mato Grosso"},{"1":"1978,5","2":"59,977","_rn_":"Goiás"},{"1":"3726,0","2":"195,487","_rn_":"Distrito Federal"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[9],"max":[9]},"pages":{}}}
              </script>
            </div>
            
            É possível também calcular o intervalo de confiança para cada uma dessas estimativas:
              
              
              ```r
                          confint(mediaRendaUF)
              ```
            <div data-pagedtable="false">
              <script data-pagedtable-source type="application/json">
            {"columns":[{"label":[".rownames"],"name":[1],"type":["chr"],"align":["left"]},{"label":["X2.5.."],"name":[2],"type":["dbl"],"align":["right"]},{"label":["X97.5.."],"name":[3],"type":["dbl"],"align":["right"]}],"data":[{"1":"Rondônia","2":"1593,7","3":"1870,6"},{"1":"Acre","2":"1573,1","3":"1891,7"},{"1":"Amazonas","2":"1518,8","3":"1984,6"},{"1":"Roraima","2":"1780,4","3":"2300,0"},{"1":"Pará","2":"1309,5","3":"1471,8"},{"1":"Amapá","2":"1823,4","3":"2928,7"},{"1":"Tocantins","2":"1639,1","3":"1947,6"},{"1":"Maranhão","2":"1094,1","3":"1421,4"},{"1":"Piauí","2":"1221,6","3":"1546,8"},{"1":"Ceará","2":"1254,8","3":"1468,0"},{"1":"Rio Grande do Norte","2":"1358,2","3":"1824,6"},{"1":"Paraíba","2":"1357,9","3":"1674,0"},{"1":"Pernambuco","2":"1476,8","3":"1856,7"},{"1":"Alagoas","2":"1226,7","3":"1415,8"},{"1":"Sergipe","2":"1412,3","3":"1834,1"},{"1":"Bahia","2":"1306,3","3":"1570,8"},{"1":"Minas Gerais","2":"1740,0","3":"1900,4"},{"1":"Espírito Santo","2":"1877,5","3":"2105,8"},{"1":"Rio de Janeiro","2":"2160,5","3":"2375,5"},{"1":"São Paulo","2":"2472,3","3":"3014,3"},{"1":"Paraná","2":"2129,5","3":"2327,5"},{"1":"Santa Catarina","2":"2204,5","3":"2339,2"},{"1":"Rio Grande do Sul","2":"2223,0","3":"2435,5"},{"1":"Mato Grosso do Sul","2":"1991,1","3":"2277,4"},{"1":"Mato Grosso","2":"1950,3","3":"2186,7"},{"1":"Goiás","2":"1860,9","3":"2096,1"},{"1":"Distrito Federal","2":"3342,8","3":"4109,1"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[9],"max":[9]},"pages":{}}}
              </script>
            </div>
            
            É possível definir domínios que sejam **cruzamentos de variáveis categóricas** com a função `interaction`.  
            Na `svyby` também é possível utilizar `vartype="cv"` caso desejemos que no output apareça o coeficiente de variação ao invés do erro padrão da estimativa.
            Para estimar a taxa de desocupação por sexo e cor/raça utilizando `svyratio`e `svyby` e o respectivo cv:
              
              ```r
                          txdesocupSexoRaca <- svyby(~VD4002 == "Pessoas desocupadas", ~interaction(V2007,V2010), dadosPNADc, svyratio, denominator = ~VD4001 == "Pessoas na força de trabalho", na.rm = T, vartype = "cv")
                          txdesocupSexoRaca
              ```
            <div data-pagedtable="false">
              <script data-pagedtable-source type="application/json">
            {"columns":[{"label":[""],"name":["_rn_"],"type":[""],"align":["left"]},{"label":["ratio"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["cv"],"name":[2],"type":["dbl"],"align":["right"]}],"data":[{"1":"0,089957","2":"0,019172","_rn_":"Homem.Branca"},{"1":"0,118998","2":"0,018625","_rn_":"Mulher.Branca"},{"1":"0,139803","2":"0,029574","_rn_":"Homem.Preta"},{"1":"0,181698","2":"0,030774","_rn_":"Mulher.Preta"},{"1":"0,086812","2":"0,160894","_rn_":"Homem.Amarela"},{"1":"0,120706","2":"0,153786","_rn_":"Mulher.Amarela"},{"1":"0,133725","2":"0,013935","_rn_":"Homem.Parda"},{"1":"0,174048","2":"0,014007","_rn_":"Mulher.Parda"},{"1":"0,131563","2":"0,173548","_rn_":"Homem.Indígena"},{"1":"0,153449","2":"0,177374","_rn_":"Mulher.Indígena"},{"1":"0,000000","2":"NaN","_rn_":"Homem.Ignorado"},{"1":"0,136575","2":"0,603237","_rn_":"Mulher.Ignorado"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[8],"max":[8]},"pages":{}}}
              </script>
            </div>
            #### Gráficos para Dados Amostrais
            
            O Pacote `survey` possui funções específicas para gerar gráficos que incorporem os pesos amostrais das observações. Para a criação dos gráficos, é utilizado o mesmo objeto de plano amostral utilizado para estimar as quantidades populacionais.
            
            Entre os gráficos disponíveis no pacote estão o histograma, boxplot e gráficos de dispersão.
            
            ##### Histograma
            
            A função `svyhist` permite a criação de histogramas para variáveis numéricas que consideram os pesos amostrais para computar as frequências ou densidades das barras.  
            A sintaxe básica é semelhante as de estimação apresentadas anteriormente. Além disto, possui os mesmos parâmetros que a função `hist`: `breaks`, `xlab`, `main`, etc... .  
            Para estimativas das frequências absolutas, deve-se utilizar `freq = TRUE`. Abaixo, um exemplo do histograma do número de horas trabalhadas para cada um dos casos:
              
              
              ```r
                          svyhist(~ as.numeric(VD4031), dadosPNADc, main = "Histograma", xlab = "Número de Horas Trabalhadas")
              ```
              
              ![](readme_files/figure-markdown_github/unnamed-chunk-52-1.png)
              
              ```r
                          svyhist(~ as.numeric(VD4031), dadosPNADc, freq = TRUE, main = "Histograma", xlab = "Número de Horas Trabalhadas")
              ```
              
              ![](readme_files/figure-markdown_github/unnamed-chunk-52-2.png)
            
            ##### Boxplot
            
            Para a construção de _boxplots_ que considerem os pesos amostrais, a função é `svyboxplot`.
            
            A sintaxe dela difere um pouco das demais. É necessário declarar a variável numérica, sucedida por um `~` e a variável de agrupamento do boxplot. Caso não deseje usar grupos, basta colocar o número `1`. Além disto, há a opção de plotar apenas os _outliers_ mais extremos ou todos, através da opção `all.outliers`. Exemplos:
              
              
              ```r
                          svyboxplot(VD4031 ~ 1, dadosPNADc, main = "Boxplot do Número de Horas Trabalhadas")
              ```
              
              ![](readme_files/figure-markdown_github/unnamed-chunk-53-1.png)
              
              ```r
                          svyboxplot(VD4031 ~ V2007, dadosPNADc, main = "Boxplot do Número de Horas Trabalhadas por Sexo")
              ```
              
              ![](readme_files/figure-markdown_github/unnamed-chunk-53-2.png)
              
              ```r
                          svyboxplot(VD4031 ~ V2007, dadosPNADc, main = "Boxplot do Número de Horas Trabalhadas por Sexo", all.outliers = TRUE)
              ```
              
              ![](readme_files/figure-markdown_github/unnamed-chunk-53-3.png)
            
            ##### Gráficos de Dispersão
            
            Os gráficos de dispersão usuais não são muito úteis para dados provindo de amostras complexas, pois não conseguem diferenciar o peso de cada ponto representado no gráfico. Para isso, a função `svyplot` tem algumas opções de construção de gráficos de dispersão que representam esses pesos através do argumento `style`.
            
            Uma opção é a utilização de **`style = bubble`**, que o tamanho do ponto no gráfico representa o peso das obervações naquele ponto.
            
            ```r
            svyplot(VD4019 ~ VD4031, dadosPNADc, style = "bubble", xlab = "Horas habitualmente trabalhadas", ylab = "Rendimento habitual")
            ```
            ![](readme_files/figure-markdown_github/unnamed-chunk-55-1.png)
            
            Outra opção é a **`style = transparent`**, onde regiões com menor peso são mais transparentes e com maior peso, mais sólidas:
              
              
              ```r
                          svyplot(VD4019 ~ VD4031, dadosPNADc, style = "transparent", xlab = "Horas habitualmente trabalhadas", ylab = "Rendimento habitual")
              ```
            ![](readme_files/figure-markdown_github/unnamed-chunk-57-1.png)
            
            ***
              
              # Modelagem com pacote `survey` 
              
              No pacote `survey` ainda há a possibilidadede realizar testes de hipóteses e estimar parâmetros de modelos, considerando o plano amostral complexo.
            
            #### Testes de Hipóteses
            
            Os testes de hipóteses incluídos no pacote `survey` incluem teste-t para médias, teste qui-quadrado e teste de postos.
            
            Abaixo um exemplo do teste t para diferenças de médias de rendimentos habituais entre sexos:
              
              ```r
                          svyttest(VD4019 ~ V2007, dadosPNADc)
              ```
              
              ```
              ## 
              ##    Design-based t-test
              ## 
              ## data:  VD4019 ~ V2007
              ## t = -10,8, df = 14500, p-value <2e-16
              ## alternative hypothesis: true difference in mean is not equal to 0
              ## 95 percent confidence interval:
              ##  -654,09 -452,85
              ## sample estimates:
              ## difference in mean 
              ##            -553,47
              ```
            
            Outro exemplo para testar diferenças entre médias de horas trabalhadas, entre concluintes e não concluintes de graduação:
              
              ```r
                          svyttest(as.numeric(VD4031) ~ V3007, dadosPNADc)
              ```
              
              ```
              ## 
              ##    Design-based t-test
              ## 
              ## data:  as.numeric(VD4031) ~ V3007
              ## t = -2,18, df = 6410, p-value = 0,029
              ## alternative hypothesis: true difference in mean is not equal to 0
              ## 95 percent confidence interval:
              ##  -1,877630 -0,098908
              ## sample estimates:
              ## difference in mean 
              ##           -0,98827
              ```
            
            #### Modelos lineares
            
            Além de testes de hipóteses, é possível estimar modelos lineares generalizados para dados amostrais complexos através da função `svyglm`.
            
            ##### Regressão Linear
            
            Para regressão linear simples ou múltipla, basta descrever a fórmula do modelo desejado na função `svyglm`.
            
            No exemplo abaixo, utilizamos um modelo de regressão com rendimento habitual como variável dependente e escolaridade, cor/raça e idade como variáveis explicativas. Para obter as estatísticas do modelo, pode ser utilizada as função `summary`, assim como feito para a regressão linear convencional.
            
            ```r
            modeloLin <- svyglm(VD4019 ~ VD3001 + V2010 + V2009, dadosPNADc)
            summary(modeloLin)
            ```

            Além disso, é possível computar intervalos de confiança para os parâmetros:
              
              ```r
                          confint(modeloLin)
              ```
            
            ##### Regressão Logística
            
            Outro modelo pertencente a classe de modelos generalizados é a Regressão Logística. Para utilizar este modelo, basta colocar o argumento `family = "binomial"`.
            
            No exemplo abaixo modelamos a não-conclusão de graduação pelo sexo, raça e idade:
              
              ```r
                          modeloLog <- svyglm(V3007 ~ V2007 + V2010 + V2009, dadosPNADc, family = "binomial")
                          summary(modeloLog)
              ```


            ```r
            confint(modeloLog)
            ```
            
            #Análise de concentração de renda com Pacote `convey`
            
            O Pacote [convey](https://cran.r-project.org/package=convey) permite estimar diversas medidas de concentração de renda para dados provenientes de pesquisas com planos amostrais complexos. 
            
            Este pacote segue uma sintaxe bem próxima à sintaxe do `survey`, sendo possível utilizar funções do `survey` em objetos do `convey`. Uma extensa documentação sobre este pacote e a sobre o processo de estimação dos índices pode ser encontrada no github dos autores: [https://guilhermejacob.github.io/context/index.html](https://guilhermejacob.github.io/context/index.html).
            
            Para a utilização do pacote convey, primeiramente é necessário utilizar a função `convey_prep`, que transforma o objeto do plano amostral do `survey` no objeto que o `convey` utiliza para as estimações:
              
              ```r
                          library(convey)
                          dadosPNADc <- convey_prep(dadosPNADc)
              ```
            
            Aqui, apresentaremos apenas exemplo de duas das diversas medidas disponíveis no pacote: O índice de Gini e a Curva de Lorenz.
            
            #### Índice de Gini
            
            Para estimamos o índice de Gini, basta utilizar a função `svygini` com a variável de renda desejada. 
            Neste exemplo, estimamos o índice de Gini do Brasil para a renda habitual:
              
              ```r
                          giniHab <- svygini(~VD4019, dadosPNADc, na.rm  =  TRUE)
                          giniHab
              ```
              
              ```
              ##         gini   SE
              ## VD4019 0,499 0,01
              ```
            Assim como nas funções do pacote `survey`, é possível, por exemplo, estimar o coeficiente de variação desta estimativa:
              
              ```r
                          cv(giniHab)
              ```
              
              ```
              ##         VD4019
              ## VD4019 0,01488
              ```
            
            Também é possível utilizar a função `svyby` para estimar o Gini da renda habitual por Unidade da Federação:
              
              ```r
                          giniUF <- svyby(~VD4019, by = ~UF, dadosPNADc, svygini, na.rm  =  TRUE)
                          giniUF
              ```
            
            <div data-pagedtable="false">
              <script data-pagedtable-source type="application/json">
            {"columns":[{"label":[""],"name":["_rn_"],"type":[""],"align":["left"]},{"label":["VD4019"],"name":[1],"type":["dbl"],"align":["right"]},{"label":["se"],"name":[2],"type":["dbl"],"align":["right"]}],"data":[{"1":"0,43985","2":"0,0170041","_rn_":"Rondônia"},{"1":"0,51858","2":"0,0155454","_rn_":"Acre"},{"1":"0,54264","2":"0,0218424","_rn_":"Amazonas"},{"1":"0,48461","2":"0,0172277","_rn_":"Roraima"},{"1":"0,48792","2":"0,0102127","_rn_":"Pará"},{"1":"0,55644","2":"0,0444482","_rn_":"Amapá"},{"1":"0,46975","2":"0,0142295","_rn_":"Tocantins"},{"1":"0,52088","2":"0,0267823","_rn_":"Maranhão"},{"1":"0,55074","2":"0,0177948","_rn_":"Piauí"},{"1":"0,50144","2":"0,0150547","_rn_":"Ceará"},{"1":"0,50402","2":"0,0284635","_rn_":"Rio Grande do Norte"},{"1":"0,53146","2":"0,0168834","_rn_":"Paraíba"},{"1":"0,52442","2":"0,0213443","_rn_":"Pernambuco"},{"1":"0,44381","2":"0,0142772","_rn_":"Alagoas"},{"1":"0,53758","2":"0,0212047","_rn_":"Sergipe"},{"1":"0,53229","2":"0,0160241","_rn_":"Bahia"},{"1":"0,45434","2":"0,0085308","_rn_":"Minas Gerais"},{"1":"0,45752","2":"0,0100574","_rn_":"Espírito Santo"},{"1":"0,45815","2":"0,0084110","_rn_":"Rio de Janeiro"},{"1":"0,50721","2":"0,0216454","_rn_":"São Paulo"},{"1":"0,44338","2":"0,0086321","_rn_":"Paraná"},{"1":"0,39156","2":"0,0057614","_rn_":"Santa Catarina"},{"1":"0,46139","2":"0,0079878","_rn_":"Rio Grande do Sul"},{"1":"0,44275","2":"0,0129135","_rn_":"Mato Grosso do Sul"},{"1":"0,42826","2":"0,0111961","_rn_":"Mato Grosso"},{"1":"0,43567","2":"0,0109822","_rn_":"Goiás"},{"1":"0,54805","2":"0,0094932","_rn_":"Distrito Federal"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[9],"max":[9]},"pages":{}}}
              </script>
            </div>
            
            E estimar intervalos de confiança para cada UF:
              
              ```r
                          confint(giniUF)
              ```
              
              ```
              ##                       2,5 %  97,5 %
              ## Rondônia            0,40652 0,47318
              ## Acre                0,48811 0,54905
              ## Amazonas            0,49983 0,58545
              ## Roraima             0,45084 0,51837
              ## Pará                0,46790 0,50794
              ## Amapá               0,46932 0,64355
              ## Tocantins           0,44186 0,49763
              ## Maranhão            0,46838 0,57337
              ## Piauí               0,51587 0,58562
              ## Ceará               0,47193 0,53094
              ## Rio Grande do Norte 0,44823 0,55980
              ## Paraíba             0,49837 0,56455
              ## Pernambuco          0,48259 0,56626
              ## Alagoas             0,41583 0,47180
              ## Sergipe             0,49601 0,57914
              ## Bahia               0,50088 0,56370
              ## Minas Gerais        0,43762 0,47106
              ## Espírito Santo      0,43781 0,47724
              ## Rio de Janeiro      0,44167 0,47464
              ## São Paulo           0,46478 0,54963
              ## Paraná              0,42647 0,46030
              ## Santa Catarina      0,38027 0,40285
              ## Rio Grande do Sul   0,44573 0,47704
              ## Mato Grosso do Sul  0,41744 0,46806
              ## Mato Grosso         0,40632 0,45020
              ## Goiás               0,41415 0,45720
              ## Distrito Federal    0,52944 0,56666
              ```
            
            #### Curva de Lorenz
            
            A Curva de Lorenz é um gráfico utilizado para relacionar a distribuição relativa de renda pelas pessoas. A área entre essa curva e a reta identidade, é uma das formas de definir o coeficiente de Gini. No pacote `convey`, é possível fazer o gráfico desta curva com a função `svylorenz`. Os quantis da população para os quais a renda acumulada serão plotados no gráfico são definidos no argumento `quantiles`.
            
            Exemplo para a Curva de Lorenz da renda habitual:
              
              ```r
                          curvaLorenz <- svylorenz(~VD4019, dadosPNADc, quantiles = seq(0, 1, .05), na.rm  =  TRUE)
              ```
              
              ![](readme_files/figure-markdown_github/unnamed-chunk-70-1.png)
            
            

[1] Instituto Brasileiro de Geografia e Estatística - <douglas.braga@ibge.gov.br>