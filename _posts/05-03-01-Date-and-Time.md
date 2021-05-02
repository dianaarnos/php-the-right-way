---
title: Data e Horário
isChild: true
anchor: data_e_horario
---

## Data e Horário {#data_e_horario_title}

O PHP tem uma classe chamada DateTime para ajudar você com leitura, escrita, comparações e cálculos com datas e
horários. Existem muitas funções no PHP relacionadas a datas e horários além da DateTime, mas ela fornece uma boa
interface orientada a objetos para a maioria dos usos comuns. Ela pode tratar de fusos horários, mas isso vai além
do escopo dessa breve introdução.

Para começar a trabalhar com a DateTime, converta uma string bruta de data e hora para um objeto com o método _factory_
`createFromFormat()`, ou use `new DateTime` para obter a data e a hora atual. Use o método `format()` para converter
um objeto DateTime de volta para uma string para saída.

{% highlight php %}
<?php
$raw = '22. 11. 1968';
$start = DateTime::createFromFormat('d. m. Y', $raw);

echo "Start date: " . $start->format('Y-m-d') . "\n";
{% endhighlight %}

Cálculos com a DateTime são possíveis com a classe DateInterval. A DateTime tem métodos como o `add()` e o `sub()` que
recebem um DateInterval como argumento. Não escreva código que espere o mesmo número de segundos para todos os dias,
pois tanto as alterações de horário de verão quanto as de fuso horário irão quebrar essa suposição. Em vez disso use
intervalos de data. Para calcular diferenças entre datas use o método `diff()`. Ele retornará um novo DateInterval,
que é bem fácil de exibir.

{% highlight php %}
<?php
// cria uma cópia de $start e adiciona um mês e 6 dias
$end = clone $start;
$end->add(new DateInterval('P1M6D'));

$diff = $end->diff($start);
echo "Diferença: " . $diff->format('%m mês, %d dias (total: %a dias)') . "\n";
// Diferença: 1 mês, 6 dias (total: 37 dias)
{% endhighlight %}

Com objetos DateTime, você pode usar a comparação padrão:

{% highlight php %}
<?php
if($start < $end) {
    echo "Start is before end!\n";
}
{% endhighlight %}
    
Um último exemplo para demonstrar a classe DatePeriod. Ela é usada para iterar por eventos recorrentes. Ela pode
receber dois objetos DateTime, um início e um fim, e o intervalo para o qual ele retornará todos os eventos 
intermediários.

{% highlight php %}
<?php
// mostra todas as quintas-feiras entre $start e $end
$periodInterval = DateInterval::createFromDateString('first thursday');
$periodIterator = new DatePeriod($start, $periodInterval, $end, DatePeriod::EXCLUDE_START_DATE);
foreach($periodIterator as $date) {
    //mostra cada data no período
    echo $date->format('Y-m-d') . " ";
}
{% endhighlight %}

Uma extensão da PHP API popular é a [Carbon](https://carbon.nesbot.com/). Ela herda tudo na classe DateTime, portanto
implica em alterações mínimas de código, mas as funcionalidades extras incluem suporte à localização, outras formas de
adicionar, subtrair e formatar um objeto DateTime, mais meios para testar seu código simulando uma data e hora de sua
escolha.

* [Leia sobre a classe DateTime][datetime]
* [Leia sobre formatação de datas][dateformat] (opções aceitas para formatos de strings de data)

[datetime]: http://php.net/book.datetime
[dateformat]: http://php.net/function.date
