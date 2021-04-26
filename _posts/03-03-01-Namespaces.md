---
title: Namespaces
isChild: true
anchor: namespaces
---

## Namespaces {#namespaces_title}

Como mencionado anteriormente, a comunidade PHP tem muitos desenvolvedores criando muito código. Isso significa que o
código de uma biblioteca PHP pode usar o mesmo nome de classe que uma outra biblioteca. Quando ambas bibliotecas são
usadas no mesmo namespace, elas colidem e causam problemas.

Os _Namespaces_ resolvem esse problema. Como descrito no manual de referência do PHP, os namespaces podem ser comparados
com os diretórios dos sistemas operacionais, que fazem _namespace_ dos arquivos; dois arquivos com o mesmo nome podem
coexistir em diretórios separados. Da mesma forma, duas classes PHP com o mesmo nome podem coexistir em namespaces PHP
separados. Simples assim.

É importante que você use namespace no seu código para que ele possa ser usado por outros desenvolvedores sem risco de
colisão com outras bibliotecas.

Um modo recomendado de usar namespaces está descrito na [PSR-4][psr4], que tem como objetivo fornecer uma convenção
padrão para arquivos, classes e namespaces para parmitir um código plug-and-play.

Em outubro de 2014 o PHP-FIG (Framework Interop Group) depreciou o padrão para autoloading anterior: a [PSR-0][psr0].
Tanto a PSR-0 e a PSR-4 ainda são perfeitamente utilizáveis. A última requer o PHP 5.3, então muitos projetos que rodam
apenas em PHP 5.2 implementam a PSR-0.

Se você estiver planejando usar um padrão para auto-carregamento para uma nova aplicação ou pacote, olhe na PSR-4.

* [Leia sobre os Namespaces][namespaces]
* [Leia sobre a PSR-0][psr0]
* [Leia sobre a PSR-4][psr4]

[namespaces]: https://secure.php.net/language.namespaces
[psr0]: https://www.php-fig.org/psr/psr-0/
[psr4]: https://www.php-fig.org/psr/psr-4/
