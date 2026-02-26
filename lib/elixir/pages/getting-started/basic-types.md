---
# Copyright (c) 2012–2026 The Elixir Team.
# Elixir and the Elixir logo are registered trademarks of The Elixir Team.
#
# Documentation licensed under the Apache License, Version 2.0.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/elixir-lang/elixir/blob/-/LICENSE

source_url: https://github.com/elixir-lang/elixir/blob/main/lib/elixir/pages/getting-started/basic-types.md
revision: 980500551c67dd08303765764314d6321a7fcf42
status: ready
---

<!--
  SPDX-License-Identifier: Apache-2.0
  SPDX-FileCopyrightText: 2021 The Elixir Team
-->

# Tipos básicos

Neste capítulo, aprenderemos mais sobre os tipos básicos do Elixir: inteiros,
números de ponto flutuante, booleanos, átomos e strings.
Outros tipos de dados, como listas e tuplas, serão explorados no próximo
capítulo.

```elixir
iex> 1          # inteiro
iex> 0x1F       # inteiro
iex> 1.0        # número de ponto flutuante
iex> true       # booleano
iex> :atom      # átomo / símbolo
iex> "elixir"   # string
iex> [1, 2, 3]  # lista
iex> {1, 2, 3}  # tupla
```

## Aritmética básica

Abra o `iex` e digite as seguintes expressões:

```elixir
iex> 1 + 2
3
iex> 5 * 5
25
iex> 10 / 2
5.0
```

Observe que `10 / 2` retornou um número de ponto flutuante `5.0` em vez de um
inteiro `5`.
Isso é esperado.
No Elixir, o operador [`/`](`//2`) sempre retorna um número de ponto flutuante.
Se você quiser realizar uma divisão inteira ou obter o resto da divisão, pode
invocar as funções [`div`](`div/2`) e [`rem`](`rem/2`):

```elixir
iex> div(10, 2)
5
iex> div 10, 2
5
iex> rem 10, 3
1
```

Note que o Elixir permite omitir os parênteses ao invocar funções que esperam um
ou mais argumentos.
Esse recurso proporciona uma sintaxe mais limpa ao escrever declarações e
estruturas de controle de fluxo.
No entanto, as pessoas desenvolvedoras Elixir geralmente preferem usar
parênteses.

O Elixir também oferece suporte a notações abreviadas para inserir números
binários, octais e hexadecimais:

```elixir
iex> 0b1010
10
iex> 0o777
511
iex> 0x1F
31
```

Os números de ponto flutuante exigem um ponto seguido de pelo menos um dígito e
também suportam o caractere `e` para notação científica:

```elixir
iex> 1.0
1.0
iex> 1.0e-10
1.0e-10
```

No Elixir, os números de ponto flutuante têm precisão de 64 bits.

Você pode invocar a função [`round`](`round/1`) para obter o inteiro mais próximo
de um determinado número de ponto flutuante, ou a função [`trunc`](`trunc/1`)
para obter a parte inteira de um número de ponto flutuante.

```elixir
iex> round(3.58)
4
iex> trunc(3.58)
3
```

Finalmente, ao trabalharmos com diferentes tipos de dados, aprenderemos que o
Elixir fornece diversas funções predicativas para verificar o tipo de um valor.
Por exemplo, [`is_integer`](`is_integer/1`) pode ser usado para verificar se um
valor é um inteiro ou não.

```elixir
iex> is_integer(1)
true
iex> is_integer(2.0)
false
```

Você também pode usar [`is_float`](`is_float/1`) ou [`is_number`](`is_number/1`)
para verificar, respectivamente, se um argumento é um número de ponto flutuante,
ou um inteiro ou números de ponto flutuante.

## Valores booleanos e `nil`

Elixir suporta `true` e `false` como valores booleanos:

```elixir
iex> true
true
iex> true == false
false
```

O Elixir também fornece três operadores booleanos: [`or`](`or/2`),
[`and`](`and/2`) e [`not`](`not/1`).
Esses operadores são estritos no sentido de que esperam algo que avalie como um
valor booleano (`true` ou `false`) como primeiro argumento:

```elixir
iex> true and true
true
iex> false or is_boolean(true)
true
```

Fornecer um valor que não seja booleano resultará em uma exceção:

```elixir
iex> 1 and true
** (BadBooleanError) expected a boolean on left-side of "and", got: 1
```

`or` e `and` são operadores de curto-circuito.
Eles executam o lado direito apenas se o lado esquerdo não for suficiente para
determinar o resultado.

```elixir
iex> false and raise("Este erro nunca será lançado")
false
iex> true or raise("Este erro nunca será lançado")
true
```

Elixir também oferece o conceito de `nil`, para indicar a ausência de um valor,
e um conjunto de operadores lógicos que também manipulam `nil`: `||/2`, `&&/2` e
`!/1`.
Para esses operadores, `false` e `nil` são considerados "falsos", enquanto todos
os outros valores são considerados "verdadeiros".

```elixir
# or
iex> 1 || true
1
iex> false || 11
11

# and
iex> nil && 13
nil
iex> true && 17
17

# not
iex> !true
false
iex> !1
false
iex> !nil
true
```

Da mesma forma, valores como `0` e `""`, que algumas outras linguagens de
programação consideram "falsos", também são "verdadeiros" no Elixir.

Como regra geral, use `and`, `or` e `not` quando você espera valores booleanos.
Se algum dos argumentos não for booleano, use `&&`, `||` e `!`.

## Átomos

Um átomo é uma constante cujo valor é o seu próprio nome.
Algumas outras linguagens chamam esses átomos de símbolos.
Eles são frequentemente úteis para enumerar valores distintos, como:

```elixir
iex> :maçã
:maçã
iex> :laranja
:laranja
iex> :melão
:melão
```

Os átomos são iguais se seus nomes forem iguais.

```elixir
iex> :maçã == :maçã
true
iex> :maçã == :laranja
false
```

Frequentemente, eles são usados para expressar o estado de uma operação,
utilizando valores como `:ok` e `:erro`.

Os valores booleanos `true` e `false` também são átomos:

```elixir
iex> true == :true
true
iex> is_atom(false)
true
iex> is_boolean(:false)
true
```

O Elixir permite omitir os dois pontos (`:`) iniciais para os átomos `false`,
`true` e `nil`.

## Strings

No Elixir, as strings são delimitadas por aspas duplas e codificadas em UTF-8:

```elixir
iex> "hellö"
"hellö"
```

> Nota: se você estiver usando o Windows, é possível que seu terminal não
> utilize UTF-8 por padrão.
> Você pode alterar a codificação da sua sessão atual executando `chcp 65001`
> antes de entrar no IEx.

Você pode concatenar duas strings com o operador [`<>`](`<>/2`):

```elixir
iex> "olá " <> "mundo!"
"olá mundo!"
```

O Elixir também suporta interpolação de strings:

```elixir
iex> string = "mundo"
iex> "olá #{string}!"
"olá mundo!"
```

A concatenação de strings exige que ambos os lados sejam strings, mas a
interpolação suporta qualquer tipo de dado que possa ser convertido em uma
string:

```elixir
iex> número = 42
iex> "eu tenho #{número} anos!"
"eu tenho 42 anos!"
```

As strings podem conter quebras de linha.
Você pode introduzi-las usando sequências de escape:

```elixir
iex> "olá
...> mundo"
"olá\nmundo"
iex> "olá\nmundo"
"olá\nmundo"
```

Você pode imprimir uma string usando a função [`IO.puts`](`IO.puts/1`) do módulo
`IO`:

```elixir
iex> IO.puts("olá\nmundo")
olá
mundo
:ok
```

Observe que a função [`IO.puts`](`IO.puts/1`) retorna o átomo `:ok` após a
impressão.

No Elixir, as strings são representadas internamente por sequências contíguas de
bytes conhecidas como binários.

```elixir
iex> is_binary("hellö")
true
```

Também podemos obter o número de bytes em uma string:

```elixir
iex> byte_size("hellö")
6
```

Observe que o número de bytes nessa string é 6, mesmo que ela tenha 5 grafemas.
Isso ocorre porque o grafema "ö" ocupa 2 bytes para ser representado em UTF-8.
Podemos obter o comprimento real da string, com base no número de grafemas,
usando a função [`String.length`](`String.length/1`):

```elixir
iex> String.length("hellö")
5
```

O módulo `String` contém um conjunto de funções que operam em strings conforme
definido no padrão Unicode:

```elixir
iex> String.upcase("hellö")
"HELLÖ"
```

## Comparação estrutural

O Elixir também fornece [`==`](`==/2`), [`!=`](`!=/2`), [`<=`](`<=/2`),
[`>=`](`>=/2`), [`<`](`</2`) e [`>`](`>/2`) como operadores de comparação.
Podemos comparar números:

```elixir
iex> 1 == 1
true
iex> 1 != 2
true
iex> 1 < 2
true
```

Mas também átomos, strings, booleanos, etc.:

```elixir
iex> "foo" == "foo"
true
iex> "foo" == "bar"
false
```

Inteiros e números de ponto flutuante são considerados iguais se tiverem o mesmo
valor:

```elixir
iex> 1 == 1.0
true
iex> 1 == 2.0
false
```

No entanto, você pode usar o operador de comparação estrita [`===`](`===/2`) e
[`!==`](`!==/2`) se quiser distinguir entre inteiros e números de ponto
flutuante:

```elixir
iex> 1 === 1.0
false
```

Os operadores de comparação no Elixir podem comparar qualquer tipo de dado.
Dizemos que esses operadores realizam _comparação estrutural_.
Para mais informações, você pode ler nossa documentação sobre
[Comparações estruturais vs. semânticas](`Kernel#module-structural-comparison`).

O Elixir também fornece tipos de dados para expressar coleções, como listas e
tuplas, que aprenderemos a seguir.
Quando falarmos sobre concorrência e tolerância a falhas por meio de processos,
também discutiremos portas, PIDs e referências, mas isso ficará para capítulos
posteriores.
Vamos em frente.
