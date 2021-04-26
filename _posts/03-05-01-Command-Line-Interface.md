---
title: Interface de Linha de Comando
isChild: true
anchor: interface_de_linha_de_comando
---

## Interface de Linha de Comando {#interface_de_linha_de_comando_title}

O PHP foi criado primariamente para escrever aplicações web, mas ele também é útil para criar scripts de linha de
comando (CLI). Programas PHP de linha de comando podem te ajudar a automatizar tarefas comuns como testes, publicação
e administração de aplicações.

Programas PHP CLI são poderosos pois você pode usar o código de sua aplicação diretamente sem precisar criar e proteger
uma GUI (Interface Gráfica do Usuário) web para isso. Apenas tenha a certeza de **não** colocar seus scripts PHP CLI na
raiz pública do seu servidor web!

Tente executar o PHP a partir da sua linha de comando:

{% highlight console %}
> php -i
{% endhighlight %}

A opção `-i` irá mostrar a sua configuração do PHP da mesma forma que a função [`phpinfo()`][phpinfo].

A opção `-a` fornece um shell interativo, similar ao IRB do ruby e ao shell interativo do python. Também existe um
número de outras [opções de linha comando][cli-options] úteis.

Vamos escrever um programa CLI "Hello, $name" simples. Para testá-lo, crie um arquivo chamado `hello.php`, como
mostrado a seguir.

{% highlight php %}
<?php
if ($argc !== 2) {
    echo "Uso: php hello.php <nome>.\n";
    exit(1);
}
$name = $argv[1];
echo "Hello, $name\n";
{% endhighlight %}

O PHP define duas variáveis especiais baseadas nos argumentos que seu script receber. [`$argc`][argc] é uma variável
integer que contém a *quantidade* de argumentos e [`$argv`][argv] é uma variável array que contém o *valor* de cada
argumento. O primeiro argumento sempre é o nome do arquivo PHP do seu programa, no caso `hello.php`.

A expressão `exit()` é usada com um número diferente de zero para informar ao shell que o comando falhou. Códigos de
saída normalmente usados podem ser encontrados [aqui][exit-codes].

Para executar nosso script acima, a partir da linha de comando:

{% highlight console %}
> php hello.php
Uso: php hello.php [nome]
> php hello.php world
Hello, world
{% endhighlight %}


 * [Aprenda sobre como executar o PHP a partir da linha de comando][php-cli]

[phpinfo]: https://secure.php.net/function.phpinfo
[cli-options]: https://secure.php.net/features.commandline.options
[argc]: https://secure.php.net/reserved.variables.argc
[argv]: https://secure.php.net/reserved.variables.argv
[exit-codes]: https://www.gsp.com/cgi-bin/man.cgi?section=3&amp;topic=sysexits
[php-cli]: https://secure.php.net/features.commandline.options
