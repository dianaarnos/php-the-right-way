---
title: Estrutura Padrão de Diretório
isChild: true
anchor: estrutura_padrao_diretorio
---

## Estrutura Padrão de Diretório {#estrutura_padrao_diretorio_title}

Uma pergunta comum entre aqueles começando com programação para a web é: "onde coloco minhas coisas?". Ao longo dos
anos, esta resposta tem sido sempre "onde está o `DocumentRoot’ (a raiz do diretório)". Embora esta resposta não seja
completa, é um ótimo lugar para começar.

Por razões de segurança, os arquivos de configuração não devem ser acessíveis pelos visitantes de um site; portanto, os
scripts públicos são mantidos em um diretório público e as configurações e dados privados são mantidos fora desse
diretório.

Para cada time, CMS ou estrutura em que se trabalha, uma estrutura de diretório padrão é usada por cada uma dessas
entidades. Entretanto, se alguém está iniciando um projeto sozinho, saber qual estrutura de diretórios usar pode ser
assustador.

[Paul M. Jones] tem feito algumas pesquisas fantásticas sobre práticas comuns de dezenas de milhares de projetos github
no reino do PHP. Ele compilou uma estrutura padrão de arquivos e diretórios, o [Standard PHP Package Skeleton], com base
nesta pesquisa. Nesta estrutura de diretórios, o `DocumentRoot' deve apontar para `public/', os testes unitários devem
estar no diretório `tests/', e bibliotecas de terceiros, como as instaladas pelo [composer], pertencem ao diretório `
vendor/'. Para outros arquivos e diretórios, obedecer ao [Standard PHP Package Skeleton] fará mais sentido para os
contribuidores de um projeto.

[Paul M. Jones]: http://paul-m-jones.com/

[Standard PHP Package Skeleton]: https://github.com/php-pds/skeleton

[Composer]: /#composer_and_packagist
