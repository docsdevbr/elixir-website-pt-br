---
# Copyright (c) 2012–2026 The Elixir Team.
# Elixir and the Elixir logo are registered trademarks of The Elixir Team.
#
# Documentation licensed under the Apache License, Version 2.0.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/elixir-lang/elixir/blob/-/LICENSE

source_url: https://github.com/elixir-lang/elixir/blob/main/lib/elixir/pages/getting-started/keywords-and-maps.md
revision: fb8f6900fa2ddcdb5af102057d99c6996dab81ef
status: ready
---

<!--
  SPDX-License-Identifier: Apache-2.0
  SPDX-FileCopyrightText: 2021 The Elixir Team
-->

# Listas de palavras-chave e mapas

Agora vamos falar sobre estruturas de dados associativas.
Estruturas de dados associativas são capazes de associar uma chave a um
determinado valor.
Diferentes linguagens chamam essas estruturas por nomes diferentes, como
dicionários, hashes, arrays associativos, etc.

No Elixir, temos duas estruturas de dados associativas principais: listas de
palavras-chave e mapas.

## Listas de palavras-chave

Listas de palavras-chave são uma estrutura de dados usada para passar opções
para funções.
Vejamos um cenário onde elas podem ser úteis.

Imagine que você queira dividir uma string de números.
Inicialmente, podemos invocar `String.split/2` passando duas strings como
argumentos:

```elixir
iex> String.split("1 2 3 4", " ")
["1", "2", "3", "4"]
```

E se você quiser dividir no máximo 2 vezes?
A função `String.split/3` permite que a opção `parts` seja definida com o número
máximo de entradas no resultado:

```elixir
iex> String.split("1 2 3 4", " ", [parts: 3])
["1", "2", "3 4"]
```

Como você pode notar, obtivemos 3 partes, sendo que a última contém o restante
da entrada sem dividi-la.

Agora imagine que algumas das entradas que você precisa dividir contenham
espaços adicionais entre os números:

```elixir
iex> String.split("1  2  3  4", " ", [parts: 3])
["1", "", "2  3  4"]
```

Como você pode notar, os espaços adicionais resultam em entradas vazias na
saída.
Felizmente, também podemos definir a opção `trim` como `true` para removê-los:

```elixir
iex> String.split("1  2  3  4", " ", [parts: 3, trim: true])
["1", "2", " 3  4"]
```

Mais uma vez recebemos 3 partes, sendo que a última continha as sobras.

`[parts: 3]` e `[parts: 3, trim: true]` são listas de palavras-chave.
Quando uma lista de palavras-chave é o último argumento de uma função, podemos
omitir os colchetes e escrever:

```elixir
iex> String.split("1  2  3  4", " ", parts: 3, trim: true)
["1", "2", " 3  4"]
```

Como mostrado no exemplo acima, as listas de palavras-chave são usadas
principalmente como argumentos opcionais de funções.

Como o nome indica, as listas de palavras-chave são simplesmente listas.
Mais especificamente, são listas compostas por tuplas de dois itens, onde o
primeiro elemento (a chave) é um átomo e o segundo elemento pode ser qualquer
valor.
Ambas as representações são iguais:

```elixir
iex> [{:parts, 3}, {:trim, true}] == [parts: 3, trim: true]
true
```

Listas de palavras-chave são importantes porque possuem três características
especiais:

- As chaves devem ser átomos.
- As chaves são ordenadas, conforme especificado pela pessoa desenvolvedora.
- As chaves podem ser repetidas.

Por exemplo, utilizamos o fato de que as chaves podem ser repetidas ao
[importar funções](../getting-started/alias-require-and-import.md) no Elixir:

```elixir
iex> import String, only: [split: 1, split: 2]
String
iex> split("hello world")
["hello", "world"]
```

No exemplo acima, importamos `split/1` e `split/2` do módulo `String`, o que nos
permite invocá-las sem precisar digitar o nome do módulo.
Usamos uma lista de palavras-chave para listar as funções a serem importadas.

Como as listas de palavras-chave são listas, podemos usar todas as operações
disponíveis para listas.
Por exemplo, podemos usar `++` para adicionar novos valores a uma lista de
palavras-chave:

```elixir
iex> list = [a: 1, b: 2]
[a: 1, b: 2]
iex> list ++ [c: 3]
[a: 1, b: 2, c: 3]
iex> [a: 0] ++ list
[a: 0, a: 1, b: 2]
```

Você pode ler o valor de uma lista de palavras-chave usando a sintaxe de
colchetes, que retornará o valor da primeira chave correspondente.
Isso também é conhecido como sintaxe de acesso, pois é definido pelo módulo
`Access`:

```elixir
iex> list[:a]
1
iex> list[:b]
2
```

Embora seja possível realizar a correspondência de padrões em listas de
palavras-chave, isso não é feito na prática, pois a correspondência de padrões
em listas exige que o número de itens e sua ordem correspondam:

```elixir
iex> [a: a] = [a: 1]
[a: 1]
iex> a
1
iex> [a: a] = [a: 1, b: 2]
** (MatchError) no match of right hand side value: [a: 1, b: 2]
iex> [b: b, a: a] = [a: 1, b: 2]
** (MatchError) no match of right hand side value: [a: 1, b: 2]
```

Além disso, como as listas de palavras-chave são frequentemente usadas como
argumentos opcionais, elas são empregadas em situações onde nem todas as chaves
podem estar presentes, o que impossibilitaria a correspondência com base nelas.
Em resumo, não utilize correspondência de padrões em listas de palavras-chave.

Para manipular listas de palavras-chave, o Elixir oferece o módulo `Keyword`.
Lembre-se, porém, que listas de palavras-chave são simplesmente listas e, como
tal, apresentam as mesmas características de desempenho linear: quanto maior a
lista, mais tempo levará para encontrar uma chave, contar o número de itens e
assim por diante.
Se você precisar armazenar uma grande quantidade de chaves em uma estrutura de
dados chave-valor, o Elixir oferece mapas, que aprenderemos em breve.

### Blocos `do` e palavras-chave

Como vimos, as palavras-chave são usadas principalmente na linguagem para passar
valores opcionais.
De fato, já usamos palavras-chave em capítulos anteriores.
Vejamos a macro `if/2`:

```elixir
iex> if true do
...>   "Isso será visto"
...> else
...>   "Isso não será"
...> end
"Isso será visto"
```

No exemplo acima, os blocos `do` e `else` formam uma lista de palavras-chave.
Eles nada mais são do que uma conveniência sintática adicionada às listas de
palavras-chave.
Podemos reescrever o código acima da seguinte forma:

```elixir
iex> if(true, do: "Isso será visto", else: "Isso não será")
"Isso será visto"
```

Preste muita atenção às duas sintaxes.
O segundo exemplo usa listas de palavras-chave, exatamente como no exemplo
`String.split/3`, então separamos cada par chave-valor com vírgulas e cada chave
é seguida por `:`.
Nos blocos `do`, usamos palavras simples, como `do`, `else` e `end`, e as
separamos por uma nova linha.
Elas são úteis justamente ao escrever blocos de código.
Na maioria das vezes, você usará a sintaxe de bloco, mas é bom saber que elas
são equivalentes.

O fato da sintaxe de bloco ser equivalente à sintaxe de palavras-chave significa
que precisamos de poucas estruturas de dados para representar a linguagem,
mantendo-a simples no geral.
Voltaremos a esse tópico ao discutirmos a [sintaxe opcional](optional-syntax.md)
e a [metaprogramação](../meta-programming/quote-and-unquote.md).

Com isso esclarecido, vamos falar sobre mapas.

## Mapas como pares chave-valor

Sempre que você precisar armazenar pares chave-valor, os mapas são a estrutura
de dados ideal no Elixir.
Um mapa é criado usando a sintaxe `%{}`:

```elixir
iex> map = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> map[:a]
1
iex> map[2]
:b
iex> map[:c]
nil
```

Comparados com listas de palavras-chave, já podemos observar duas diferenças:

- Os mapas permitem qualquer valor como chave.
- Os mapas possuem sua própria ordenação interna, que não é garantida como sendo
  a mesma em mapas diferentes, mesmo que tenham as mesmas chaves.

Ao contrário das listas de palavras-chave, os mapas são muito úteis para
correspondência de padrões.
Quando um mapa é usado em um padrão, ele sempre corresponderá a um subconjunto
dos valores fornecidos:

```elixir
iex> %{} = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> %{:a => a} = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> a
1
iex> %{:c => c} = %{:a => 1, 2 => :b}
** (MatchError) no match of right hand side value: %{2 => :b, :a => 1}
```

Como mostrado acima, um mapa corresponde a todos os mapas desde que as chaves no
padrão existam nele.
Portanto, um mapa vazio corresponde a todos os mapas.

O módulo `Map` fornece uma API muito semelhante à do módulo `Keyword`, com
funções convenientes para adicionar, remover e atualizar chaves de mapas:

```elixir
iex> Map.get(%{:a => 1, 2 => :b}, :a)
1
iex> Map.put(%{:a => 1, 2 => :b}, :c, 3)
%{2 => :b, :a => 1, :c => 3}
iex> Map.to_list(%{:a => 1, 2 => :b})
[{2, :b}, {:a, 1}]
```

## Mapas de chaves predefinidas

Na seção anterior, usamos mapas como uma estrutura de dados chave-valor onde as
chaves podem ser adicionadas ou removidas a qualquer momento.
No entanto, também é comum criar mapas com um conjunto predefinido de chaves.
Seus valores podem ser atualizados, mas novas chaves nunca são adicionadas nem
removidas.
Isso é útil quando conhecemos o formato dos dados com os quais estamos
trabalhando e, se obtivermos uma chave diferente, provavelmente significa que
ocorreu um erro em outro lugar.
Nesses casos, as chaves são, na maioria das vezes, átomos:

```elixir
iex> map = %{:name => "John", :age => 23}
%{name: "John", age: 23}
```

Como você pode ver no resultado impresso acima, o Elixir também permite escrever
mapas de chaves de átomos usando a mesma sintaxe `chave: valor` das listas de
palavras-chave:

```elixir
iex> map = %{name: "John", age: 23}
%{name: "John", age: 23}
```

Quando uma chave é um átomo, também podemos acessá-la usando a sintaxe
`map.key`:

```elixir
iex> map.name
"John"
iex> map.agee
** (KeyError) key :agee not found in: %{name: "John", age: 23}
```

Existe também uma sintaxe para atualizar chaves, que também gera um erro se a
chave ainda não tiver sido definida:

```elixir
iex> %{map | name: "Mary"}
%{name: "Mary", age: 23}
iex> %{map | agee: 27}
** (KeyError) key :agee not found in: %{name: "John", age: 23}
```

Essas operações têm uma grande vantagem: elas geram uma exceção se a chave não
existir no mapa, e o compilador pode até detectar e avisar quando possível.
Isso as torna úteis para obter feedback rápido e identificar bugs e erros de
digitação logo no início.
Essa também é a sintaxe usada para outro recurso do Elixir chamado "Structs",
que aprenderemos mais adiante.

Pessoas desenvolvedoras Elixir geralmente preferem usar a sintaxe `map.key` e
correspondência de padrões em vez das funções do módulo `Map` ao trabalhar com
mapas, pois isso leva a um estilo de programação assertivo.
[Esta postagem no blog de José Valim](https://dashbit.co/blog/writing-assertive-code-with-elixir)
fornece visões e exemplos de como obter um software mais conciso e rápido
escrevendo código assertivo em Elixir.

Em um capítulo posterior, você aprenderá sobre ["Structs"](structs.md), que
reforçam ainda mais a ideia de um mapa com chaves predefinidas.

## Estruturas de dados aninhadas

Frequentemente, teremos mapas dentro de mapas, ou até mesmo listas de
palavras-chave dentro de mapas, e assim por diante.
O Elixir oferece facilidades para manipular estruturas de dados aninhadas por
meio das macros `get_in/1`, `put_in/2`, `update_in/2` e outras, proporcionando
as mesmas conveniências encontradas em linguagens imperativas, ao mesmo tempo
que mantém as propriedades imutáveis da linguagem.

Imagine que você tenha a seguinte estrutura:

```elixir
iex> users = [
  john: %{name: "John", age: 27, languages: ["Erlang", "Ruby", "Elixir"]},
  mary: %{name: "Mary", age: 29, languages: ["Elixir", "F#", "Clojure"]}
]
[
  john: %{age: 27, languages: ["Erlang", "Ruby", "Elixir"], name: "John"},
  mary: %{age: 29, languages: ["Elixir", "F#", "Clojure"], name: "Mary"}
]
```

Temos uma lista de palavras-chave de pessoas usuárias onde cada valor é um mapa
contendo o nome, a idade e uma lista de linguagens de programação que cada
pessoa usuária prefere.
Se quiséssemos acessar a idade de john, poderíamos escrever:

```elixir
iex> users[:john].age
27
```

Acontece que também podemos usar essa mesma sintaxe para atualizar o valor:

```elixir
iex> users = put_in(users[:john].age, 31)
[
  john: %{age: 31, languages: ["Erlang", "Ruby", "Elixir"], name: "John"},
  mary: %{age: 29, languages: ["Elixir", "F#", "Clojure"], name: "Mary"}
]
```

A macro `update_in/2` é semelhante, mas permite passar uma função que controla
como o valor muda.
Por exemplo, vamos remover "Clojure" da lista de linguagens de Mary:

```elixir
iex> users = update_in(users[:mary].languages, fn languages -> List.delete(languages, "Clojure") end)
[
  john: %{age: 31, languages: ["Erlang", "Ruby", "Elixir"], name: "John"},
  mary: %{age: 29, languages: ["Elixir", "F#"], name: "Mary"}
]
```

## Resumo

Existem duas estruturas de dados diferentes para trabalhar com armazenamentos de
chave-valor no Elixir.
Juntamente com o módulo `Access` e a correspondência de padrões, elas fornecem
um conjunto robusto de ferramentas para manipular estruturas de dados complexas
e potencialmente aninhadas.

Ao concluirmos este capítulo, lembre-se de que você deve:

- Usar listas de palavras-chave para passar valores opcionais para funções.

- Usar mapas para estruturas de dados de chave-valor em geral.

- Usar mapas ao trabalhar com dados que possuem um conjunto predefinido de
  chaves.

Agora, vamos falar sobre módulos e funções.
