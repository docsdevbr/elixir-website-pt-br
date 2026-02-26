---
# Copyright (c) 2012–2026 The Elixir Team.
# Elixir and the Elixir logo are registered trademarks of The Elixir Team.
#
# Documentation licensed under the Apache License, Version 2.0.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/elixir-lang/elixir/blob/-/LICENSE

source_url: https://github.com/elixir-lang/elixir/blob/main/lib/elixir/pages/getting-started/case-cond-and-if.md
revision: fb8f6900fa2ddcdb5af102057d99c6996dab81ef
status: ready
---

<!--
  SPDX-License-Identifier: Apache-2.0
  SPDX-FileCopyrightText: 2021 The Elixir Team
-->

# case, cond e if

Neste capítulo, aprenderemos sobre as estruturas de controle de fluxo
[`case`](`case/2`), [`cond`](`cond/1`) e [`if`](`if/2`).

## case

[`case`](`case/2`) permite comparar um valor com vários padrões até encontrarmos
um correspondente:

```elixir
iex> case {1, 2, 3} do
...>   {4, 5, 6} ->
...>     "Esta cláusula não corresponderá"
...>   {1, x, 3} ->
...>     "Esta cláusula corresponderá e vinculará x a 2 nesta cláusula"
...>   _ ->
...>     "Essa cláusula corresponderia a qualquer valor"
...> end
"Esta cláusula corresponderá e vinculará x a 2 nesta cláusula"
```

Se você quiser realizar uma correspondência de padrões com uma variável
existente, você precisa usar o operador [`^`](`^/1`):

```elixir
iex> x = 1
1
iex> case 10 do
...>   ^x -> "Não irá corresponder"
...>   _ -> "Irá corresponder"
...> end
"Irá corresponder"
```

As cláusulas também permitem que condições adicionais sejam especificadas por
meio de guardas:

```elixir
iex> case {1, 2, 3} do
...>   {1, x, 3} when x > 0 ->
...>     "Irá corresponder"
...>   _ ->
...>     "A correspondência seria possível se a condição de guarda não fosse satisfeita"
...> end
"Irá corresponder"
```

A primeira cláusula acima só será verdadeira quando `x` for positivo.

Lembre-se de que erros em guardas não vazam, mas simplesmente fazem com que a
guarda falhe:

```elixir
iex> hd(1)
** (ArgumentError) argument error
iex> case 1 do
...>   x when hd(x) -> "Não irá corresponder"
...>   x -> "Obteve #{x}"
...> end
"Obteve 1"
```

Se nenhuma das cláusulas corresponder, será gerado um erro:

```elixir
iex> case :ok do
...>   :error -> "Não irá corresponder"
...> end
** (CaseClauseError) no case clause matching: :ok
```

A documentação do módulo `Kernel` lista todas as proteções disponíveis em sua
barra lateral.
Você também pode consultar a referência completa
[Padrões e guardas](../references/patterns-and-guards.md#guards) para obter uma
documentação detalhada.

## if

[`case`](`case/2`) utiliza correspondência de padrões e guardas para
desestruturar e comparar condições específicas.
No entanto, padrões e guardas são limitados a certas expressões otimizadas pelo
compilador.
Em muitas situações, é necessário escrever condições que vão além do que pode
ser expresso com [`case`](`case/2`).
Para esses casos, [`if`](`if/2`) é uma alternativa útil:

```elixir
iex> if true do
...>   "Isso funciona!"
...> end
"Isso funciona!"
iex> if false do
...>   "Isso jamais será visto"
...> end
nil
```

Se a condição fornecida para [`if`](`if/2`) retornar `false` ou `nil`, o corpo
fornecido entre `do` e `end` não será executado e, em vez disso, retornará
`nil`.

[`if`](`if/2`) também suporta blocos `else`:

```elixir
iex> if nil do
...>   "Isso não será visto"
...> else
...>   "Isso será visto"
...> end
"Isso será visto"
```

### Expressões

Algumas linguagens de programação fazem distinção entre expressões (código que
retorna um valor) e instruções (código que não retorna nenhum valor).
Em Elixir, existem apenas expressões, não instruções.
Tudo o que você escreve em Elixir retorna algum valor.

Essa propriedade permite que as variáveis sejam delimitadas a blocos de código
individuais, como [`if`](`if/2`) e [`case`](`case/2`), onde declarações ou
alterações são visíveis apenas dentro do bloco.
Uma alteração não pode vazar para blocos externos, o que torna o código mais
fácil de acompanhar e entender.
Por exemplo:

```elixir
iex> x = 1
1
iex> if true do
...>   x = x + 1
...> end
2
iex> x
1
```

Você vê o valor de retorno da expressão [`if`](`if/2`) como o resultado `2`
aqui.
Para manter as alterações feitas dentro da expressão [`if`](`if/2`) no bloco
externo, você precisa atribuir o valor retornado a uma variável no bloco
externo.

```elixir
iex> x = 1
1
iex> x =
...>   if true do
...>     x + 1
...>   else
...>     x
...>   end
2
```

Com todas as expressões retornando um valor, também não há necessidade de
construções alternativas, como operadores ternários que se apresentam como uma
alternativa a [`if`](`if/2`).
O Elixir inclui uma notação em linha para [`if`](`if/2`) e, como
[aprenderemos mais tarde](keywords-and-maps.md#do-blocks-and-keywords), trata-se
de uma variação sintática dos argumentos de `if`.

> #### `if` é uma macro {: .info}
>
> Uma observação interessante sobre [`if`](`if/2`) é que ele é implementado como
> uma macro na linguagem: não é uma construção especial da linguagem como seria
> em muitas outras.
> Você pode consultar a documentação e o código-fonte para obter mais
> informações.

Se você se deparar com vários blocos [`if`](`if/2`), talvez queira considerar o
uso de [`cond`](`cond/1`) em vez disso.
Vamos dar uma olhada.

## cond

Usamos `case` para encontrar uma cláusula correspondente a partir de vários
padrões.
Usamos `if` para verificar uma única condição.
Se você precisar verificar várias condições e encontrar a primeira que não seja
avaliada como `nil` ou `false`, [`cond`](`cond/1`) é uma construção útil:

```elixir
iex> cond do
...>   2 + 2 == 5 ->
...>     "Isso não será verdadeiro"
...>   2 * 2 == 3 ->
...>     "Nem isto"
...>   1 + 1 == 2 ->
...>     "Mas isto será"
...> end
"Mas isto será"
```

Isso é equivalente às cláusulas `else if` em muitas linguagens imperativas,
embora sejam menos usadas no Elixir.

Se todas as condições retornarem `nil` ou `false`, um erro (`CondClauseError`)
será gerado.
Por esse motivo, pode ser necessário adicionar uma condição final, igual a
`true`, que sempre corresponderá:

```elixir
iex> cond do
...>   2 + 2 == 5 ->
...>     "Isso nunca é verdadeiro"
...>   2 * 2 == 3 ->
...>     "Nem isto"
...>   true ->
...>     "Isso é sempre verdadeiro (equivalente a else)"
...> end
"Isso é sempre verdadeiro (equivalente a else)"
```

Semelhante a [`if`](`if/2`), [`cond`](`cond/1`) considera qualquer valor
diferente de `nil` e `false` como verdadeiro:

```elixir
iex> cond do
...>   hd([1, 2, 3]) ->
...>     "1 é considerado verdadeiro"
...> end
"1 é considerado verdadeiro"
```

## Resumindo

Concluímos a introdução às construções de controle de fluxo mais fundamentais no
Elixir.
De modo geral, as pessoas desenvolvedoras Elixir preferem correspondência de
padrões e guardas, usando [`case`](`case/2`) e definições de função (que
exploraremos em capítulos futuros), pois são sucintos e precisos.
Quando sua lógica não puder ser definida por padrões e guardas, você pode
considerar usar [`if`](`if/2`), recorrendo a [`cond`](`cond/1`) quando houver
várias condições a serem verificadas.
