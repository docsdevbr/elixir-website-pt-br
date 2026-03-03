---
# Copyright (c) 2012–2026 The Elixir Team.
# Elixir and the Elixir logo are registered trademarks of The Elixir Team.
#
# Documentation licensed under the Apache License, Version 2.0.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/elixir-lang/elixir/blob/-/LICENSE

source_url: https://github.com/elixir-lang/elixir/blob/v1.19.5/lib/elixir/pages/getting-started/anonymous-functions.md
revision: f9966230ee2aa1735847a115d62192b4fcd9d796
status: ready
---

<!--
  SPDX-License-Identifier: Apache-2.0
  SPDX-FileCopyrightText: 2021 The Elixir Team
-->

# Funções anônimas

Funções anônimas permitem armazenar e passar código executável como se fosse um
inteiro ou uma string.
Vamos aprender mais.

## Identificando funções e documentação

Antes de discutirmos funções anônimas, vamos falar sobre como o Elixir
identifica funções nomeadas – as funções definidas em
[modules](modules-and-functions.md).

No Elixir, as funções são identificadas tanto pelo nome quanto pela aridade.
A aridade de uma função descreve o número de argumentos que ela recebe.
A partir deste ponto, usaremos tanto o nome da função quanto sua aridade para
descrevê-la na documentação. `trunc/1` identifica a função chamada `trunc` que
recebe `1` argumento, enquanto `trunc/2` identifica uma função diferente
(inexistente) com o mesmo nome, mas com aridade `2`.

Também podemos usar essa sintaxe para acessar a documentação.
O shell Elixir define a função [`h`](`IEx.Helpers.h/1`), que você pode usar para
acessar a documentação de qualquer função.
Por exemplo, digitar `h trunc/1` irá imprimir a documentação da função
`trunc/1`:

```elixir
iex> h trunc/1
                             def trunc(number)

Returns the integer part of number.
```

`h trunc/1` funciona porque está definida no módulo `Kernel`.
Todas as funções do módulo `Kernel` são importadas automaticamente para o nosso
namespace.
Na maioria das vezes, você também incluirá o nome do módulo ao consultar a
documentação de uma determinada função:

```elixir
iex> h Kernel.trunc/1
                             def trunc(number)

Returns the integer part of number.
```

Você pode usar os identificadores de módulo + função para consultar a
documentação de qualquer coisa, incluindo operadores (experimente
`h Kernel.+/2`).
Invocar [`h`](`IEx.Helpers.h/1`) sem argumentos exibe a documentação de
`IEx.Helpers`, onde `h` e outras funcionalidades são definidas.

## Definindo funções anônimas

Funções anônimas no Elixir são delimitadas pelas palavras-chave `fn` e `end`:

```elixir
iex> add = fn a, b -> a + b end
#Function<12.71889879/2 in :erl_eval.expr/5>
```

No exemplo acima, definimos uma função anônima que recebe dois argumentos, `a` e
`b`, e retorna o resultado de `a + b`.
Os argumentos estão sempre à esquerda de `->` e o código a ser executado à
direita.
A função anônima é armazenada na variável `add`.
Você pode ver que ela retorna um valor representado por `#Function<...>`.
Embora sua representação seja opaca, o bit `:erl_eval.expr` nos informa que a
função foi definida no shell (durante a avaliação).

Podemos invocar funções anônimas passando argumentos para elas, usando um ponto
(`.`) entre a variável e o parêntese de abertura:

```elixir
iex> add.(1, 2)
3
```

O ponto deixa claro quando você está chamando uma função anônima, armazenada na
variável `add`, em oposição a uma função chamada `add/2`.
Por exemplo, se você tiver uma função anônima armazenada na variável `is_atom`,
não há ambiguidade entre `is_atom.(:foo)` e `is_atom(:foo)`.
Se ambas usassem a mesma sintaxe `is_atom(:foo)`, a única maneira de saber o
comportamento real de `is_atom(:foo)` seria examinando todo o código até então
em busca de uma possível definição da variável `is_atom`.
Essa busca prejudica a manutenção, pois exige que as pessoas desenvolvedoras
acompanhem o contexto adicional mentalmente ao ler e escrever o código.

No Elixir, funções anônimas também são identificadas pelo número de argumentos
que recebem.
Podemos verificar se um valor é uma função usando `is_function/1` e também
verificar sua aridade usando `is_function/2`.

```elixir
iex> is_function(add)
true
# verifica se add é uma função que espera exatamente 2 argumentos
iex> is_function(add, 2)
true
# verifica se add é uma função que espera exatamente 1 argumento
iex> is_function(add, 1)
false
```

## Closures

Funções anônimas também podem acessar variáveis que estão no escopo quando a
função é definida.
Isso é normalmente chamado de closure, pois elas "fecham" seu escopo.
Vamos definir uma nova função anônima que usa a função anônima `add` que
definimos anteriormente:

```elixir
iex> double = fn a -> add.(a, a) end
#Function<6.71889879/1 in :erl_eval.expr/5>
iex> double.(2)
4
```

Uma variável atribuída em uma função não afeta o ambiente ao seu redor:

```elixir
iex> x = 42
42
iex> (fn -> x = 0 end).()
0
iex> x
42
```

## Cláusulas e guardas

Semelhante ao `case/2`, podemos usar a correspondência de padrões nos argumentos
de funções anônimas, bem como definir múltiplas cláusulas e guardas:

```elixir
iex> f = fn
...>   x, y when x > 0 -> x + y
...>   x, y -> x * y
...> end
#Function<12.71889879/2 in :erl_eval.expr/5>
iex> f.(1, 3)
4
iex> f.(-1, 3)
-3
```

O número de argumentos em cada cláusula de função anônima precisa ser o mesmo;
caso contrário, ocorrerá um erro.

```elixir
iex> f2 = fn
...>   x, y when x > 0 -> x + y
...>   x, y, z -> x * y + z
...> end
** (CompileError) iex:1: cannot mix clauses with different arities in anonymous functions
```

## O operador de captura

Ao longo deste guia, temos usado a notação `nome/aridade` para nos referirmos a
funções.
Acontece que essa notação pode ser usada para capturar uma função existente em
um tipo de dados que podemos passar adiante, de forma semelhante ao
comportamento das funções anônimas.

```elixir
iex> fun = &is_atom/1
&:erlang.is_atom/1
iex> is_function(fun)
true
iex> fun.(:hello)
true
iex> fun.(123)
false
```

Como você pode ver, uma vez que uma função é capturada, podemos passá-la como
argumento ou invocá-la usando a notação de função anônima.
O valor retornado acima também indica que podemos capturar funções definidas em
módulos:

```elixir
iex> fun = &String.length/1
&String.length/1
iex> fun.("hello")
5
```

Como os operadores são funções no Elixir, você também pode capturar operadores:

```elixir
iex> add = &+/2
&:erlang.+/2
iex> add.(1, 2)
3
```

A sintaxe de captura também pode ser usada como um atalho para criar funções que
encapsulam funções existentes.
Por exemplo, imagine que você queira criar uma função anônima que verifique se
uma determinada função tem aridade 2.
Você poderia escrevê-la como:

```elixir
iex> is_arity_2 = fn fun -> is_function(fun, 2) end
#Function<8.71889879/1 in :erl_eval.expr/5>
iex> is_arity_2.(add)
true
```

Mas, usando a sintaxe de captura, você pode escrevê-la como:

```elixir
iex> is_arity_2 = &is_function(&1, 2)
#Function<8.71889879/1 in :erl_eval.expr/5>
iex> is_arity_2.(add)
true
```

O operador `&1` representa o primeiro argumento passado para a função.
Portanto, ambas as funções anônimas `is_arity_2` definidas acima são
equivalentes.

Mais uma vez, considerando que os operadores são chamadas de função, a sintaxe
abreviada de captura também funciona com operadores, ou mesmo com interpolação
de strings:

```elixir
iex> fun = &(&1 + 1)
#Function<6.71889879/1 in :erl_eval.expr/5>
iex> fun.(1)
2

iex> fun2 = &"Good #{&1}"
#Function<6.127694169/1 in :erl_eval.expr/5>
iex> fun2.("morning")
"Good morning"
```

O operador `&(&1 + 1)` acima é o mesmo que `fn x -> x + 1 end`.
Você pode ler mais sobre o operador de captura `&` em [sua documentação](`&/1`).

A seguir, vamos revisitar alguns dos tipos de dados que aprendemos no passado e
explorar mais a fundo como eles funcionam.
