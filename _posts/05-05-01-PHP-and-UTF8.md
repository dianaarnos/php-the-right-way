---
title: Trabalhando com UTF-8
isChild: true
anchor: php_e_utf8
---

## Trabalhando com UTF-8 {#php_e_utf8_title}

_Esta seção foi originalmente escrita por [Alex Cabal](https://alexcabal.com/) como 
[PHP Melhores Práticas](https://phpbestpractices.org/#utf-8) e tem sido usado como base para os nossos próprios conselhos 
sobre UTF-8_.

### Não existe um jeito fácil. Seja cuidadoso, detalhado e consistente.

Atualmente o PHP não possui suporte a Unicode em um nível baixo. Existem maneiras de garantir que strings UTF-8 sejam 
processadas OK, mas não é fácil e isto requer cavar quase todos os níveis da aplicação web desde o HTML passando pelo SQL, até o PHP. 
Vamos abordar para um resumo breve e prático.

### UTF-8 no nível do PHP

As operações básicas com strings, como concatenar duas strings e atribuição de strings a variáveis, não preciso de nada 
especial para o UTF-8. No entanto a maioria das funções de strings, como `strpos()` e `strlen()`, precisam de atenção 
especial. Essas funções têm, frequentemente, uma `mb_*` em contrapartida: por exemplo `mb_strpos()` e `mb_strlen()`. 
Estas funções de string `mb_*` são disponibilizados a você por meio do [Multibyte Extensão String] e são projetadas 
especificamente para operar em strings de caracteres Unicode.

Você deve usar as funções `mb_*` sempre que operar com strings Unicode. Por exemplo, se você usar `substr()` em uma 
string UTF-8, há uma boa chance de que o resultado terá alguns caracteres ilegíveis. A função correta de usar seria a 
contrapartida multibyte, `mb_substr()`.

A parte mais difícil é lembrar de usar as funções `mb_*` em todos os momentos. Se você esquecer mesmo que apenas uma 
vez, sua string Unicode tem uma chance de ser ilegível durante o processamento.

Nem todas as funções de string têm um 'mb_*` contrapartida. Se não houver uma para o que você quer fazer, então você 
pode estar sem sorte.

Você deve usar a função `mb_internal_encoding()` no topo de cada script PHP que você escreve (ou no topo de seu script 
global que seja incluído), e a função `mb_http_output()` logo após ele se seu script está enviando saída para um 
navegador. Definir explicitamente a codificação de suas strings em cada script vai lhe poupar muita dor de cabeça 
futuramente.

Além disso, muitas funções PHP que operam com strings têm um parâmetro opcional que lhe permite especificar 
a codificação. Você deve sempre indicar explicitamente UTF-8 quando for dada a opção. Por exemplo, `htmlentities()` tem 
uma opção para codificação de caracteres, e você deve sempre especificar UTF-8 se lidar com tais strings. Note-se que a 
partir do PHP 5.4.0, UTF-8 é a codificação padrão para `htmlentities()` e `htmlspecialchars()`.

Finalmente, se você estiver criando um aplicativo distribuído e não tiver certeza de que a extensão `mbstring` será 
ativada, então considere o uso do pacote Composer [patchwork/utf8]. Isto irá usar a `mbstring` se estiver disponível, e 
criar fall back para funções UTF-8 que não estiverem.

[Multibyte Extensão String]: https://secure.php.net/book.mbstring
[patchwork/utf8]: https://packagist.org/packages/patchwork/utf8

### UTF-8 no nível de banco de dados

Se o seu script PHP acessa o MySQL, há uma chance de suas strings serem armazenadas como strings não-UTF-8 no banco de 
dados, mesmo que você siga todas as precauções acima.

Para certificar-se de que suas strings irão do PHP para o MySQL como UTF-8, verifique se o banco de dados e as tabelas 
estão todos setados com o character set e o collation como `utf8mb4` e que você use o character set `utf8mb4` na string 
de conexão PDO. Veja o exemplo de código abaixo. Isto é _criticamente importante_.

Observe que você deve usar o character set `utf8mb4` para ter suporte completo de UTF-8 e não o character set `utf8`! 
Veja "Leitura adicional" para o porquê.

### UTF-8 no nível do navegador

Use a função `mb_http_output()` para garantir que o seu script PHP gere strings UTF-8 para o seu browser.

O navegador precisará então ser informado pela resposta HTTP que esta página deve ser considerada como UTF-8. Hoje, é 
comum definir o conjunto de caracteres no cabeçalho de resposta HTTP da seguinte maneira:

{% highlight php %}
<?php
header('Content-Type: text/html; charset=UTF-8')
{% endhighlight %}

A abordagem histórica para fazer isso era incluir a [tag de charset `<meta>`](http://htmlpurifier.org/docs/enduser-utf8.html) 
na tag `<head>` da sua página.

{% highlight php %}
<?php
// Diz para o PHP que estamos usando strings UTF-8 até o final do script
mb_internal_encoding('UTF-8');
$utf_set = ini_set('default_charset', 'utf-8');
if (!$utf_set) {
    throw new Exception('could not set default_charset to utf-8, please ensure it\'s set on your system!');
}

// Diz para o PHP que nós vamos enviar uma saída UTF-8 para o navegador
mb_http_output('UTF-8');

// A nossa string UTF-8 de teste
$string = 'Êl síla erin lû e-govaned vîn.';

// Transformar a string de alguma forma com uma função multibyte
// Observe como cortamos a string em um caractere não-ASCII para fins de demonstração
$string = mb_substr($string, 0, 15);

// Conectar a um banco de dados para armazenar a string transformada
// Veja o exemplo PDO neste documento para obter mais informações
// Observe o `charset=utf8mb4` no Data Source Name (DSN)
$link = new PDO(
    'mysql:host=your-hostname;dbname=your-db;charset=utf8mb4',
    'your-username',
    'your-password',
    array(
        PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
        PDO::ATTR_PERSISTENT => false
    )
);

// Armazena a nossa string transformada como UTF-8 em nosso banco de dados
// Seu DB e tabelas estão com character set e collation utf8mb4, certo?
$handle = $link->prepare('insert into ElvishSentences (Id, Body, Priority) values (default, :body, :priority)');
$handle->bindParam(':body', $string, PDO::PARAM_STR);
$priority = 45;
$handle->bindParam(':priority', $priority, PDO::PARAM_INT); // dizer explicitamente para o PDO esperar um int
$handle->execute();

// Recuperar a string que acabamos de armazenar para provar se foi armazenada corretamente
$handle = $link->prepare('select * from ElvishSentences where Id = :id');
$id = 7;
$handle->bindParam(':id', $id, PDO::PARAM_INT);
$handle->execute();

// Armazenar o resultado em um objeto que emitiremos mais tarde em nossa HTML
// Este objeto não vai matar sua memória porque vai buscar os dados Just-In-Time para
$result = $handle->fetchAll(\PDO::FETCH_OBJ);

// Um wrapper de exemplo para permitir que você escape de dados para html
function escape_to_html($dirty){
    echo htmlspecialchars($dirty, ENT_QUOTES, 'UTF-8');
}

header('Content-Type: text/html; charset=UTF-8'); // Desnecessário se default_charset já estiver configurado para utf-8
?><!doctype html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>Página de teste UTF-8</title>
    </head>
    <body>
        <?php
        foreach($result as $row){
            escape_to_html($row->Body);  // Isso deve gerar a saída correta para o navegador da nossa string UTF-8 transformada
        }
        ?>
    </body>
</html>
{% endhighlight %}

### Leitura adicional

* [Manual do PHP: Operações Strings](http://php.net/language.operators.string)
* [Manual do PHP: Funções para Strings](http://php.net/ref.strings)
  * [`strpos()`](http://php.net/function.strpos)
  * [`strlen()`](http://php.net/function.strlen)
  * [`substr()`](http://php.net/function.substr)
* [Manual do PHP: Funções multibyte de strings](http://php.net/ref.mbstring)
  * [`mb_strpos()`](http://php.net/function.mb-strpos)
  * [`mb_strlen()`](http://php.net/function.mb-strlen)
  * [`mb_substr()`](http://php.net/function.mb-substr)
  * [`mb_internal_encoding()`](http://php.net/function.mb-internal-encoding)
  * [`mb_http_output()`](http://php.net/function.mb-http-output)
  * [`htmlentities()`](http://php.net/function.htmlentities)
  * [`htmlspecialchars()`](http://php.net/function.htmlspecialchars)
* [Stack Overflow: Quais os fatores que fazem o PHP incompatível com Unicode?](http://stackoverflow.com/questions/571694/what-factors-make-php-unicode-incompatible)
* [Stack Overflow: Melhores práticas em PHP e MySQL com strings internacionais](http://stackoverflow.com/questions/140728/best-practices-in-php-and-mysql-with-international-strings)
* [Como ter suporte total a Unicode em bases de dados MySQL](http://mathiasbynens.be/notes/mysql-utf8mb4)
* [Trazendo Unicode para o PHP com `Portable UTF-8`](http://www.sitepoint.com/bringing-unicode-to-php-with-portable-utf8/)
* [Stack Overflow: DOMDocument loadHTML não encoda UTF-8 corretamente](https://stackoverflow.com/questions/8218230/php-domdocument-loadhtml-not-encoding-utf-8-correctly)
