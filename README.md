# stata-tutorial

# Tutorial: Construindo um Painel Municipal no Stata (PIB × Homicídios -- Ceará)

Este tutorial apresenta, passo a passo, como importar, limpar, juntar e
transformar bases de dados no Stata para formar um painel municipal.

------------------------------------------------------------------------

## 📁 Definir diretório

**Comando:**

``` cpp
cd "C:\Users\user_exemplo\Desktop\atividade1"
```

**Explicação:**\
Define a pasta onde o Stata deve procurar e salvar arquivos.

------------------------------------------------------------------------

## 📥 Importar PIB

**Comando:**

``` cpp
import excel "pib.xls", sheet("pib") firstrow
```

**Explicação:**\
Importa a planilha de PIB usando a aba "pib" e a primeira linha como
nomes das variáveis.

**Saída esperada:**

    (8 vars, 30 obs)
    .

------------------------------------------------------------------------

**Comando:**

``` cpp
save pib.dta
```

**Explicação:**\
Salva a base importada no formato `.dta`.

**Saída esperada:**

    file pib.dta saved
    .

------------------------------------------------------------------------

**Comando:**

``` cpp
clear
```

**Explicação:**\
Limpa a memória do Stata.

**Saída esperada:**

    .

------------------------------------------------------------------------

## 📥 Importar homicídios

**Comando:**

``` cpp
import excel "homicidios.xls", sheet("homic") firstrow
```

**Explicação:**\
Importa a planilha de homicídios usando a aba "homic".

**Saída esperada:**

    (5 vars, 30 obs)
    .

------------------------------------------------------------------------

**Comando:**

``` cpp
save homicidios.dta
```

**Explicação:**\
Salva a base de homicídios no formato `.dta`.

**Saída esperada:**

    file homicidios.dta saved
    .

------------------------------------------------------------------------

**Comando:**

``` cpp
clear
```

**Explicação:**\
Limpa a memória.

**Saída esperada:**

    .

------------------------------------------------------------------------

## 🔗 Juntar as bases

**Comando:**

``` cpp
use pib.dta, clear
```

**Explicação:**\
Carrega a base de PIB.

**Saída esperada:**

    .

------------------------------------------------------------------------

**Comando:**

``` cpp
merge 1:1 Codigo using homicidios.dta, generate(merge1)
```

**Explicação:**\
Junta as bases pelo identificador `Codigo`.\
`generate(merge1)` cria uma variável indicando o status da junção.

**Saída esperada:**

        Result                           # of obs.
        -----------------------------------------
        not matched                             0
        matched                                30   (_merge==3)
        -----------------------------------------
    .

------------------------------------------------------------------------

**Comando:**

``` cpp
save pib_violencia.dta
```

**Explicação:**\
Salva a base combinada.

**Saída esperada:**

    file pib_violencia.dta saved
    .

------------------------------------------------------------------------

## 📚 Reestruturar para painel (reshape)

**Comando:**

``` cpp
reshape long pib homic, i(Codigo) j(ano)
```

**Explicação:**\
Converte a base do formato wide para long.

**Saída esperada:**

    (note: j = 1990 1991 1992 1993 1994 1995 ... )

    Data                               Wide   ->   Long
    -----------------------------------------------------------------------------
    Number of obs.                       30   ->    600
    Number of variables                  10   ->      4
    j variable (year)                         ->   ano
    xij variables:
       pib1990 pib1991 ...                   ->   pib
       homic1990 homic1991 ...               ->   homic
    .

------------------------------------------------------------------------

**Comando:**

``` cpp
save painel_ceara.dta
```

**Explicação:**\
Salva a base final em formato painel.

**Saída esperada:**

    file painel_ceara.dta saved
    .

------------------------------------------------------------------------

## 🧭 Declarar o painel

**Comando:**

``` cpp
xtset Codigo ano, yearly
```

**Explicação:**\
Define a estrutura de painel:\
- `Codigo` = identificação do município\
- `ano` = variável temporal\
- `yearly` = frequência anual

**Saída esperada:**

    panel variable:  Codigo (unbalanced)
     time variable:  ano, 1990 to 2019
             delta:  1 year
