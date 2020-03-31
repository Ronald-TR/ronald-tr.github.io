---
title: "Python: anti-patterns que você talvez não conheça"
description: ""
featured: '/snakes.jpg'
image: '/images/trevor-cole-8uJ8w9pL-y8-unsplash.jpg'
date: 2020-03-16T14:05:36-03:00
draft: false
toc: true
tags: ["python", "pep8", "anti-patterns"]
categories: ["python", "article"]
show_reading_time: true
type: "post"
author: "Ronald Rodrigues"
---

Há muitos anti-patterns que podem ser óbvios durante uma revisão de código, outros, podem ser bastante contra intuitivos, por transpaçarem comportamentos internos da linguagem.

Este artigo é fortemente baseado na sessão "[Programming Recomendations](https://www.python.org/dev/peps/pep-0008/#programming-recommendations)" da PEP8 e no e-book gratuito "[The Little Book of Python Anti-Patterns](https://docs.quantifiedcode.com/python-anti-patterns/index.html)", com pequenas adições pessoais na descrição de cada tópico. :smile:
## Anti patterns

### Comparações entre singletons utilizando o sinal de dupla-igualdade (==).

#### Para True ou False
Quando escrevemos condições booleanas como:

```python
if condition == True:
```

Queremos dizer: "se esta condição é verdadeira, faça isto".

Em um contexto de operações booleanas, o Python já interpreta [valores não vazios como `True`](https://docs.python.org/2/reference/expressions.html#boolean-operations), o que torna a comparação acima bastante redundante se `condition` for uma variável.

No caso abaixo:

```python
condition = 'algum valor qualquer'
if condition:
    # esse contexto será executado.
```

A variável `condition` será avaliada como `True` e executará o bloco do `if`, mesmo `condition` sendo uma string e não um booleano.

> Internamente, o Python chama o método mágico `__bool__()` do objeto que efetuará a lógica por trás da avaliação sobre qual valor booleano aquele objeto assumirá, veja todos os métodos mágicos disponíveis [aqui](https://docs.python.org/3/reference/datamodel.html#basic-customization).

Em casos bastante específicos onde há a necessidade de comparação explícita, prefira o pattern proposto na PEP8: `if condition is True`, ambos os formatos são para a redução de redundâncias e incrementos de legibilidade, tudo acima também se aplica para comparações com `False`.

### Comparações com None

Este tópico é bem semelhante ao tópico anterior, com a adição de um detalhe:

Tome cuidado ao usar o formato quando sua intenção é validar se sua variável é diferente de `None`:

```python
if condition:
```

Tipos built-in vazios e `None` em um contexto booleano serão interpretados da mesma forma: Como `False`.

No código abaixo há o exemplo de uma prática comum: A definição de valores *default* para parâmetros de funções. Também é comum utilizarmos estes pârametros como *flags* que definem fluxos internos de execução da função:

```python
def foo(bar=None):
    if bar:
        print("Yay! The bar received something!")
    else:
        print("Oh no, the bar received nothing")


foo({})  # send an empty dict
# output
>>"Oh no, the bar received nothing"
```

No exemplo acima, o dicionário vazio `{}` foi avaliado da mesma forma que `None`, induzindo `foo` a dizer que nada foi passado para `bar`.
Apesar de conceitualmente "nada" realmente tenha sido passado e acima termos um exemplo inofensivo, essa checagem abre brechas para comportamentos inesperados e a retornos imprevisíveis. 

Prefira a notação sugerida na PEP8:

```python
if condition is None:

# ou para negações 

if condition is not None:
```

