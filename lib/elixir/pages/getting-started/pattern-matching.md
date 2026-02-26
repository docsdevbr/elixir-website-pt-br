---
# Copyright (c) 2012–2026 The Elixir Team.
# Elixir and the Elixir logo are registered trademarks of The Elixir Team.
#
# Documentation licensed under the Apache License, Version 2.0.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/elixir-lang/elixir/blob/-/LICENSE

source_url: https://github.com/elixir-lang/elixir/blob/main/lib/elixir/pages/getting-started/pattern-matching.md
revision: dd45d9666fe10a05b9e917692a16e774a50c0a6e
status: ready
---

<!--
  SPDX-License-Identifier: Apache-2.0
  SPDX-FileCopyrightText: 2021 The Elixir Team
-->

# Correspondência de padrões

Neste capítulo, aprenderemos por que o operador [`=`](`=/2`) no Elixir é chamado
de operador de correspondência e como usá-lo para realizar correspondência de
padrões em estruturas de dados.
Aprenderemos também sobre o operador de fixação [`^`](`^/1`) usado para acessar
valores previamente atribuídos.

## O operador de correspondência

Já usamos o operador [`=`](`=/2`) algumas vezes para atribuir valores a
variáveis no Elixir:

```elixir
iex> x = 1
1
iex> x
1
```

No Elixir, o operador [`=`](`=/2`) é na verdade chamado de *operador de
correspondência*.
Vejamos porquê:

```elixir
iex> x = 1
1
iex> 1 = x
1
iex> 2 = x
** (MatchError) no match of right hand side value: 1
```

Observe que `1 = x` é uma expressão válida e correspondeu porque ambos os lados,
esquerdo e direito, são iguais a `1`.
Quando os lados não coincidem, um erro `MatchError` é gerado.

Uma variável só pode ser atribuída ao lado esquerdo de [`=`](`=/2`):

```elixir
iex> 1 = unknown
** (CompileError) iex:1: undefined variable "unknown"
```

## Correspondência de padrões

O operador de correspondência não é usado apenas para comparar valores simples,
mas também é útil para desestruturar tipos de dados mais complexos.
Por exemplo, podemos usar a correspondência de padrões em tuplas:

```elixir
iex> {a, b, c} = {:hello, "world", 42}
{:hello, "world", 42}
iex> a
:hello
iex> b
"world"
```

Ocorrerá um erro de correspondência de padrão se os lados não puderem ser
correspondidos, por exemplo, se as tuplas tiverem tamanhos diferentes:

```elixir
iex> {a, b, c} = {:hello, "world"}
** (MatchError) no match of right hand side value: {:hello, "world"}
```

E também ao comparar tipos diferentes, por exemplo, ao comparar uma tupla do
lado esquerdo com uma lista do lado direito:

```elixir
iex> {a, b, c} = [:hello, "world", 42]
** (MatchError) no match of right hand side value: [:hello, "world", 42]
```

Mais interessante ainda, podemos fazer correspondências com base em valores
específicos.
O exemplo abaixo afirma que o lado esquerdo só corresponderá ao lado direito
quando o lado direito for uma tupla que começa com o átomo `:ok`:

```elixir
iex> {:ok, result} = {:ok, 13}
{:ok, 13}
iex> result
13

iex> {:ok, result} = {:error, :oops}
** (MatchError) no match of right hand side value: {:error, :oops}
```

Podemos realizar reconhecimento de padrões em listas:

```elixir
iex> [a, b, c] = [1, 2, 3]
[1, 2, 3]
iex> a
1
```

Uma lista também permite a correspondência em sua própria cabeça e cauda:

```elixir
iex> [head | tail] = [1, 2, 3]
[1, 2, 3]
iex> head
1
iex> tail
[2, 3]
```

Semelhante às funções [`hd`](`hd/1`) e [`tl`](`tl/1`), não podemos encontrar uma
correspondência entre uma lista vazia e um padrão de cabeça e cauda:

```elixir
iex> [head | tail] = []
** (MatchError) no match of right hand side value: []
```

O formato `[head | tail]` não é usado apenas na correspondência de padrões, mas
também para adicionar itens ao início de uma lista:

```elixir
iex> list = [1, 2, 3]
[1, 2, 3]
iex> [0 | list]
[0, 1, 2, 3]
```

Em alguns casos, você não se importa com um valor específico em um padrão.
É uma prática comum associar esses valores ao sublinhado, `_`.
Por exemplo, se apenas o primeiro elemento da lista nos interessa, podemos
atribuir a cauda ao sublinhado:

```elixir
iex> [head | _] = [1, 2, 3]
[1, 2, 3]
iex> head
1
```

A variável `_` é especial porque nunca pode ser lida.
Tentar ler algo dela resulta em um erro de compilação:

```elixir
iex> _
** (CompileError) iex:1: invalid use of _. "_" represents a value to be ignored in a pattern and cannot be used in expressions
```

Se uma variável for mencionada mais de uma vez em um padrão, todas as
referências devem se vincular ao mesmo valor:

```elixir
iex> {x, x} = {1, 1}
{1, 1}
iex> {x, x} = {1, 2}
** (MatchError) no match of right hand side value: {1, 2}
```

Embora a correspondência de padrões nos permita construir estruturas poderosas,
seu uso é limitado.
Por exemplo, não é possível fazer chamadas de função no lado esquerdo de uma
correspondência.
O exemplo a seguir é inválido:

```elixir
iex> length([1, [2], 3]) = 3
** (CompileError) iex:1: cannot invoke remote function :erlang.length/1 inside match
```

A correspondência de padrões permite que as pessoas desenvolvedoras
desestruturem facilmente tipos de dados como tuplas e listas.
Como veremos nos próximos capítulos, ela é um dos fundamentos da recursão no
Elixir e também se aplica a outros tipos, como mapas e binários.

## O operador de fixação

Variáveis no Elixir podem ser reassociadas:

```elixir
iex> x = 1
1
iex> x = 2
2
```

No entanto, há momentos em que não queremos que as variáveis sejam redefinidas.

Use o operador de fixação [`^`](`^/1`) quando quiser comparar padrões com o
*valor existente* de uma variável, em vez de redefini-la.

```elixir
iex> x = 1
1
iex> ^x = 2
** (MatchError) no match of right hand side value: 2
```

Como fixamos `x` quando ele estava vinculado ao valor `1`, isso é equivalente ao
seguinte:

```elixir
iex> 1 = 2
** (MatchError) no match of right hand side value: 2
```

Observe que vemos a mesma mensagem de erro.

Podemos usar o operador de fixação em outras correspondências de padrões, como
tuplas ou listas:

```elixir
iex> x = 1
1
iex> [^x, 2, 3] = [1, 2, 3]
[1, 2, 3]
iex> {y, ^x} = {2, 1}
{2, 1}
iex> y
2
iex> {y, ^x} = {2, 2}
** (MatchError) no match of right hand side value: {2, 2}
```

Como `x` estava vinculado ao valor `1` quando foi fixado, este último exemplo
poderia ter sido escrito como:

```elixir
iex> {y, 1} = {2, 2}
** (MatchError) no match of right hand side value: {2, 2}
```

Isso conclui nossa introdução à correspondência de padrões.
Como veremos no próximo capítulo, a correspondência de padrões é muito comum em
diversas construções de linguagem e pode ser ainda mais aprimorada com guardas.
