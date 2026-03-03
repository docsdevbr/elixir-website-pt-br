---
# Copyright (c) 2012–2026 The Elixir Team.
# Elixir and the Elixir logo are registered trademarks of The Elixir Team.
#
# Documentation licensed under the Apache License, Version 2.0.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/elixir-lang/elixir/blob/-/LICENSE

source_url: https://github.com/elixir-lang/elixir/blob/v1.19.5/lib/elixir/pages/getting-started/lists-and-tuples.md
revision: 980500551c67dd08303765764314d6321a7fcf42
status: ready
---

<!--
  SPDX-License-Identifier: Apache-2.0
  SPDX-FileCopyrightText: 2021 The Elixir Team
-->

# Listas e tuplas

Neste capítulo, aprenderemos dois dos tipos de dados de coleção mais usados no
Elixir: listas e tuplas.

## Listas (encadeadas)

O Elixir usa colchetes para especificar uma lista de valores.
Os valores podem ser de qualquer tipo:

```elixir
iex> [1, 2, true, 3]
[1, 2, true, 3]
iex> length([1, 2, 3])
3
```

Duas listas podem ser concatenadas ou subtraídas usando os operadores
[`++`](`++/2`) e [`--`](`--/2`), respectivamente:

```elixir
iex> [1, 2, 3] ++ [4, 5, 6]
[1, 2, 3, 4, 5, 6]
iex> [1, true, 2, false, 3, true] -- [true, false]
[1, 2, 3, true]
```

Os operadores de lista nunca modificam a lista existente.
Concatenar ou remover elementos de uma lista retorna uma nova lista.
Dizemos que as estruturas de dados no Elixir são *imutáveis*.
Uma vantagem da imutabilidade é que ela leva a um código mais claro.
Você pode passar os dados livremente com a garantia de que ninguém os modificará
na memória — apenas os transformará.

Ao longo do tutorial, falaremos bastante sobre a cabeça e a cauda de uma lista.
A cabeça é o primeiro elemento de uma lista e a cauda é o restante da lista.
Elas podem ser obtidas com as funções [`hd`](`hd/1`) e [`tl`](`tl/1`).
Vamos atribuir uma lista a uma variável e obter sua cabeça e cauda:

```elixir
iex> list = [1, 2, 3]
iex> hd(list)
1
iex> tl(list)
[2, 3]
```

Obter o primeiro ou o último elemento de uma lista vazia gera um erro:

```elixir
iex> hd([])
** (ArgumentError) argument error
```

Às vezes, você criará uma lista e ela retornará um valor entre aspas precedido
por `~c`.
Por exemplo:

```elixir
iex> [11, 12, 13]
~c"\v\f\r"
iex> [104, 101, 108, 108, 111]
~c"hello"
```

Quando o Elixir encontra uma lista de números ASCII imprimíveis, ele a imprime
como uma charlist (literalmente uma lista de caracteres).
Charlists são bastante comuns ao interagir com código Erlang existente.
Sempre que você vir um valor em IEx e não tiver certeza do que ele representa,
você pode usar [`i`](`IEx.Helpers.i/1`) para obter informações sobre ele:

```elixir
iex> i ~c"hello"
Term
  i ~c"hello"
Data type
  List
Description
  ...
Raw representation
  [104, 101, 108, 108, 111]
Reference modules
  List
Implemented protocols
  ...
```

Falaremos mais sobre charlists no capítulo
["Binários, strings e charlists"](binaries-strings-and-charlists.md).

> ### Strings entre aspas simples {: .info}
>
> No Elixir, você também pode usar `'hello'` para construir listas de
> caracteres, mas essa notação se tornou obsoleta no Elixir v1.15 e emitirá
> alertas em versões futuras.
> Prefira escrever `~c"hello"` em vez disso.

## Tuplas

O Elixir usa chaves para definir tuplas.
Assim como as listas, as tuplas podem conter qualquer valor:

```elixir
iex> {:ok, "hello"}
{:ok, "hello"}
iex> tuple_size({:ok, "hello"})
2
```

As tuplas armazenam elementos de forma contígua na memória.
Isso significa que acessar um elemento da tupla por índice ou obter o tamanho da
tupla é uma operação rápida.
Os índices começam em zero:

```elixir
iex> tuple = {:ok, "hello"}
{:ok, "hello"}
iex> elem(tuple, 1)
"hello"
iex> tuple_size(tuple)
2
```

Também é possível inserir um elemento em um índice específico em uma tupla com
[`put_elem`](`put_elem/3`):

```elixir
iex> tuple = {:ok, "hello"}
{:ok, "hello"}
iex> put_elem(tuple, 1, "world")
{:ok, "world"}
iex> tuple
{:ok, "hello"}
```

Observe que [`put_elem`](`put_elem/3`) retornou uma nova tupla.
A tupla original armazenada na variável `tuple` não foi modificada.
Assim como as listas, as tuplas também são imutáveis.
Cada operação em uma tupla retorna uma nova tupla; ela nunca altera a tupla
original.

## Listas ou tuplas?

Qual a diferença entre listas e tuplas?

Listas são armazenadas na memória como listas encadeadas, o que significa que
cada elemento em uma lista mantém seu valor e aponta para o elemento seguinte
até que o final da lista seja alcançado.
Isso significa que acessar o comprimento de uma lista é uma operação linear:
precisamos percorrer toda a lista para descobrir seu tamanho.

Da mesma forma, o desempenho da concatenação de listas depende do comprimento da
lista à esquerda:

```elixir
iex> list = [1, 2, 3]
[1, 2, 3]

# Isso é rápido, pois precisamos apenas percorrer `[0]` para adicionar ao início
# de `list`
iex> [0] ++ list
[0, 1, 2, 3]

# Isso é lento, pois precisamos percorrer `list` para adicionar o inteiro 4
iex> list ++ [4]
[1, 2, 3, 4]
```

Por outro lado, as tuplas são armazenadas de forma contígua na memória.
Isso significa que obter o tamanho da tupla ou acessar um elemento por índice é
rápido.
Em contrapartida, atualizar ou adicionar elementos a tuplas é custoso, pois
requer a criação de uma nova tupla na memória.

```elixir
iex> tuple = {:a, :b, :c, :d}
{:a, :b, :c, :d}
iex> put_elem(tuple, 2, :e)
{:a, :b, :e, :d}
```

Observe, no entanto, que os elementos em si não são copiados.
Ao atualizar uma tupla, todas as entradas são compartilhadas entre a tupla
antiga e a nova, exceto a entrada substituída.
Essa regra se aplica à maioria das estruturas de dados no Elixir.
Isso reduz a quantidade de alocação de memória que a linguagem precisa realizar
e só é possível graças à semântica imutável da linguagem.

Essas características de desempenho determinam o uso dessas estruturas de dados.
Em resumo, as listas são usadas quando o número de elementos retornados pode
variar.
As tuplas têm um tamanho fixo.
Vejamos dois exemplos do módulo `String`:

```elixir
iex> String.split("hello world")
["hello", "world"]
iex> String.split("hello beautiful world")
["hello", "beautiful", "world"]
```

A função [`String.split`](`String.split/1`) divide uma string em uma lista de
strings a cada espaço em branco.
Como a quantidade de elementos retornados depende da entrada, usamos uma lista.

Por outro lado, [`String.split_at`](`String.split_at/2`) divide uma string em
duas partes em uma posição específica.
Como ela sempre retorna duas entradas, independentemente do tamanho da entrada,
ela retorna tuplas:

```elixir
iex> String.split_at("hello world", 3)
{"hel", "lo world"}
iex> String.split_at("hello world", -4)
{"hello w", "orld"}
```

Também é muito comum usar tuplas e átomos para criar "tuplas etiquetadas", que
são um valor de retorno útil quando uma operação pode ser bem-sucedida ou
falhar.
Por exemplo, [`File.read`](`File.read/1`) lê o conteúdo de um arquivo em um
determinado caminho, que pode ou não existir.
Ele retorna tuplas etiquetadas:

```elixir
iex> File.read("caminho/para/arquivo/existente")
{:ok, "... contents ..."}
iex> File.read("caminho/para/arquivo/desconhecido")
{:error, :enoent}
```

Se o caminho fornecido para [`File.read`](`File.read/1`) existir, ele retorna
uma tupla com o átomo `:ok` como primeiro elemento e o conteúdo do arquivo como
segundo.
Caso contrário, retorna uma tupla com `:error` e a descrição do erro.
Como veremos em breve, o Elixir nos permite fazer *correspondência de padrões*
em tuplas etiquetadas e lidar facilmente com casos de sucesso e falha.

Como o Elixir segue essas regras consistentemente, a escolha entre listas e
tuplas fica mais clara à medida que você aprende e usa a linguagem.
O Elixir frequentemente o guia para fazer a coisa certa.
Por exemplo, existe uma função [`elem`](`elem/2`) para acessar um item de uma
tupla:

```elixir
iex> tuple = {:ok, "hello"}
{:ok, "hello"}
iex> elem(tuple, 1)
"hello"
```

No entanto, como muitas vezes não se sabe o número de elementos em uma lista,
não existe um equivalente nativo para acessar entradas arbitrárias em listas,
além do seu cabeçalho.

## Tamanho ou comprimento?

Ao contar os elementos em uma estrutura de dados, o Elixir também segue uma
regra simples: a função é chamada de `size` se a operação for de tempo constante
(o valor é pré-calculado) ou `length` se a operação for linear (o cálculo do
comprimento fica mais lento à medida que a entrada aumenta).
Como um mnemônico, tanto "length" quanto "linear" começam com "l".

Por exemplo, até agora usamos quatro funções de contagem:
[`byte_size`](`byte_size/1`) (para o número de bytes em uma string),
[`tuple_size`](`tuple_size/1`) (para o tamanho de uma tupla),
[`length`](`length/1`) (para o comprimento de uma lista) e
[`String.length`](`String.length/1`) (para o número de grafemas em uma string).
Usamos [`byte_size`](`byte_size/1`) para obter o número de bytes em uma string,
que é uma operação barata.
Recuperar o número de grafemas Unicode, por outro lado, usa
[`String.length`](`String.length/1`) e pode ser custoso, pois depende de
percorrer toda a string.

Agora que estamos familiarizados com os tipos de dados básicos da linguagem,
vamos aprender construções importantes para escrever código, antes de
discutirmos estruturas de dados mais complexas.
