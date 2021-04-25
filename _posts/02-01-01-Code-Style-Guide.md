---
title: Guia de estilo de código
anchor: guia_de_estilo_de_codigo
---

# Guia de estilo de código {#guia_de_estilo_de_codigo_title}

A comunidade PHP é grande e diversificada, composta de inúmeras bibliotecas, estruturas e componentes. É comum para
desenvolvedores PHP escolherem vários destes e combiná-los em um único projeto. É importante que o código PHP respeite
(o mais próximo possível) a um estilo de código comum para facilitar aos desenvolvedores a mistura e combinação de
várias bibliotecas para seus projetos.

O [Framework Interop Group][fig] propôs e aprovou uma série de recomendações de estilo. Nem todas elas estão
relacionadas ao estilo de código, mas aquelas que estão são [PSR-1][psr1], [PSR-12][psr12] e [PSR-4][psr4]. Estas
recomendações são apenas um conjunto de regras que muitos projetos como Drupal, Zend, Symfony, Laravel, CakePHP, phpBB,
AWS SDK, FuelPHP, Lithium, etc estão adotando. Você pode usá-los para seus próprios projetos, ou continuar a usar seu
próprio estilo pessoal.

Idealmente, você deveria escrever um código PHP que aderisse a um padrão conhecido. Isto poderia ser qualquer combinação
de PSRs, ou um dos padrões de codificação feitos pela PEAR ou Zend. Isto significa que outros desenvolvedores podem
facilmente ler e trabalhar com seu código, e As aplicações que implementam os componentes podem ter consistência mesmo
quando se trabalha com muitos códigos de terceiros.

* [Leia sobre PSR-1][psr1]
* [Leia sobre PSR-12][psr12]
* [Leia sobre PSR-4][psr4]
* [Leia sobre PEAR Coding Standards][pear-cs]
* [Leia sobre Symfony Coding Standards][symfony-cs]

Você pode usar o [PHP_CodeSniffer][phpcs] para verificar o código em relação a qualquer uma destas recomendações, e
plugins para editores de texto como [Sublime Text][st-cs] para receber feedback em tempo real.

Você pode corrigir o layout do código automaticamente usando uma das seguintes ferramentas:

- Uma delas é o [PHP Coding Standards Fixer][phpcsfixer], que tem uma base de código muito bem testada.
- Além disso, a ferramenta [PHP Code Beautifier and Fixer][phpcbf], incluída no PHP_CodeSniffer, pode ser usada para
  ajustar seu código de acordo.

E você pode executar o phpcs manualmente a partir do terminal:

    phpcs -sw --standard=PSR1 file.php

Ele mostrará os erros e indicará como corrigi-los. Também pode ser útil incluir este comando em um git hook. Dessa
forma, os branches que contêm violações contra o padrão escolhido não podem entrar no repositório até que aquelas
violações tenham sido corrigidas.

Se você tem o PHP_CodeSniffer, então você pode corrigir os problemas de layout de código reportados por ele,
automaticamente, com o
[PHP Code Beautifier and Fixer][phpcbf].

    phpcbf -w --standard=PSR1 file.php

Outra opção é usar o [PHP Coding Standards Fixer][phpcsfixer]. Ele mostrará que tipo de erros a estrutura do código
tinha antes de corrigi-los.

    php-cs-fixer fix -v --rules=@PSR1 file.php

O inglês é o idioma preferido para todos os nomes de símbolos e infra-estrutura de código. Os comentários podem ser
facilmente escritos em qualquer idioma legível por todas as pessoas, atuais e futuras, que possam estar trabalhando na
base de código.

Finalmente, um bom recurso suplementar para escrever código PHP limpo é [Clean Code PHP][cleancode].

[fig]: https://www.php-fig.org/

[psr1]: https://www.php-fig.org/psr/psr-1/

[psr12]: https://www.php-fig.org/psr/psr-12/

[psr4]: https://www.php-fig.org/psr/psr-4/

[pear-cs]: https://pear.php.net/manual/en/standards.php

[symfony-cs]: https://symfony.com/doc/current/contributing/code/standards.html

[phpcs]: https://pear.php.net/package/PHP_CodeSniffer/

[phpcbf]: https://github.com/squizlabs/PHP_CodeSniffer/wiki/Fixing-Errors-Automatically

[st-cs]: https://github.com/benmatselby/sublime-phpcs

[phpcsfixer]: https://cs.symfony.com/

[cleancode]: https://github.com/jupeter/clean-code-php
