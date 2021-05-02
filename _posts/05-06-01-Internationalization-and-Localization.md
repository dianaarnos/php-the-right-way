---
title: Internacionalização e Localização
isChild: true
anchor: i18n_l10n
---

## Internacionalização (i18n) e Localização (l10n) {#i18n_l10n_title}

_Aviso aos recém-chegados: i18n e l10n são numerônimos, uma espécie de abreviação onde os números são usados para encurtar
palavras - em nosso caso, internacionalização torna-se i18n e localização, l10n._

Antes de tudo, precisamos definir esses dois conceitos semelhantes e outras coisas relacionadas:

- **Internacionalização*** é quando você organiza seu código para que ele possa ser adaptado a diferentes idiomas ou 
  regiões sem refatorações. Esta ação geralmente é feita uma única vez - de preferência, no início do projeto, 
  caso contrário, você provavelmente precisará de algumas mudanças enormes no código fonte!
- **Localização*** acontece quando você adapta a interface (principalmente) através da tradução do conteúdo, com base no 
  trabalho i18n feito antes. Geralmente é feito sempre que uma nova língua ou região precisa de suporte e é atualizado 
  quando novas peças de interface são adicionadas, pois elas precisam estar disponíveis em todos os idiomas suportados.
- **Pluralização** define as regras necessárias entre idiomas distintos para interoperar strings contendo números e
  contadores. Por exemplo, em inglês, quando você tem apenas um item, ele é singular, e qualquer coisa diferente disso é
  chamado plural; o plural neste idioma é indicado pela adição de um S após algumas palavras, e às vezes muda partes 
  delas. Em outros idiomas, como o russo ou sérvio, há duas formas plurais além do singular - você pode até encontrar 
  idiomas com um total de quatro, cinco ou seis formas, tais como esloveno, irlandês ou árabe.

## Formas comuns de implementar

A maneira mais fácil de internacionalizar software PHP é usando arquivos de array e usando essas strings em templates, 
tal como`<h1><?=$TRANS[’title_about_page']?><</h1>``. No entanto, esta forma é dificilmente recomendada para projetos 
sérios, já que gera alguns problemas de manutenção ao longo da estrada - alguns podem aparecer logo no início, tais 
como a pluralização. Portanto, por favor, não tente isto se seu projeto contiver mais do que algumas páginas.

A maneira mais clássica e freqüentemente considerada como referência para i18n e l10n é uma 
[ferramenta Unix chamada `gettext`][gettext]. Ela data de 1995 e ainda é uma implementação completa para a tradução de 
software. É bastante fácil de ser utilizado, mas ainda contando com poderosas ferramentas de apoio. É sobre o Gettext 
que falaremos aqui. Além disso, para ajudar você a não ficar atordoado com a linha de comando, apresentaremos uma ótima 
aplicação GUI que pode ser usada para atualizar facilmente sua fonte l10n.

### Outras ferramentas

Há bibliotecas comumente usadas que suportam o Gettext e outras implementações da i18n. Algumas delas podem parecer mais 
fáceis de instalar ou oferecem suporte a recursos adicionais ou formatos de arquivo i18n. Neste documento, nos focamos 
nas ferramentas fornecidas com o core do PHP, mas aqui listamos outros para complementação:

- [aura/intl][aura-intl]: Fornece ferramentas de internacionalização (I18N), tradução de mensagem orientada a pacotes 
  por localização . Ela usa formatos de arrays para mensagem. Ela usa formatos de matriz para mensagem. Não fornece um 
  extrator de mensagens, mas fornece formatação avançada de mensagens através da extensão `intl' (incluindo mensagens 
  pluralizadas).
- [oscarotero/Gettext][oscarotero]: Suporte ao Gettext com uma interface OO; inclui funções de ajuda melhoradas, 
  extratores poderosos para vários formatos de arquivo (alguns deles não suportados nativamente pelo comando `gettext`), 
  e também pode exportar para outros formatos além dos arquivos `.mo/.po`. Pode ser útil se você precisar integrar seus 
  arquivos de tradução em outras partes do sistema, como uma interface JavaScript.  
- [symfony/translation] [symfony]: suporta muitos formatos diferentes, mas recomenda o uso de verbose do XLIFF. Não 
  inclui funções de auxílio nem um extrator embutido, mas suporta placeholders utilizando `strtr()` internamente.
- [zend/i18n][zend]: suporta arquivos array e INI, ou formatos Gettext. Implementa uma camada de caching para poupá-lo 
  de ler o sistema de arquivos todas as vezes. Também inclui ajudantes de visualização e filtros e validadores de 
  entrada que levam em conta a localização. Entretanto, não tem extrator de mensagens.

Outros frameworks também incluem módulos i18n, mas estes não estão disponíveis fora de suas bases de código:

- [Laravel] suporta arquivos básicos de array, não tem extrator automático, mas inclui um ajudante `@lang` para arquivos 
  de template.
- [Yii] suporta array, Gettext, e tradução baseada em banco de dados, e inclui um extrator de mensagens. Ele é 
  sustentado pela extensão [`Intl`][intl], disponível desde o PHP 5.3, e baseada no [projeto ICU][ICU project]; 
  isto permite que o Yii execute poderosas substituições, como a ortografia de números, formatação de datas, horários, 
  intervalos, moedas e 
  ordinais.

Se você decidir optar por uma das bibliotecas que não fornecem extratores, você pode querer usar os formatos gettext, 
então você pode usar a cadeia de ferramentas gettext original (incluindo Poedit), conforme descrito no resto do capítulo.

## Gettext

### Instalação

Você pode precisar instalar o Gettext e a biblioteca PHP relacionada utilizando seu gerenciador de pacotes, como `apt-get` 
ou `yum`. Após instalado, ative-o adicionando `extension=gettext.so` (Linux/Unix) ou `extension=php_gettext.dll` 
(Windows) em seu `php.ini`.

Aqui também usaremos [Poedit] para criar arquivos de tradução. Você provavelmente o encontrará no gerenciador de pacotes 
do seu sistema; está disponível para Unix, Mac e Windows, e também pode ser [baixado gratuitamente em seu site][poedit_download].

### Estrutura

#### Tipos de arquivos

Há três arquivos com os quais você normalmente lida enquanto trabalha com o gettext. Os principais são os arquivos PO 
(Portable Object) e MO (Machine Object), sendo o primeiro uma lista de "objetos traduzidos" legíveis e o segundo, o 
correspondente binário a ser interpretado pelo gettext ao fazer a localização. Há também um arquivo POT (Template), 
que simplesmente contém todas as chaves existentes em seus arquivos de código fonte, e pode ser usado como um guia para 
gerar e atualizar todos os arquivos PO. Esses templates não são obrigatórios: dependendo da ferramenta que você está 
usando para fazer l10n, você pode fazer tudo com apenas arquivos PO/MO.
Você sempre terá um par de arquivos PO/MO por idioma e região, mas apenas um POT por domínio.

### Domínios

Há alguns casos, em grandes projetos, em que você pode precisar separar as traduções quando as mesmas palavras
transmitem significado diferente, dado um contexto. Nesses casos, você os divide em diferentes _domínios_. Eles são,
basicamente, nomes de grupos de arquivos POT/PO/MO, onde o nome do arquivo é o referido _domínio da tradução_. Projetos
de pequeno e médio porte geralmente, para simplificar, usam apenas um domínio; seu nome é arbitrário, mas usaremos
"main" para nossos exemplos de código. Em projetos [Symfony], por exemplo, os domínios são usados para separar a
tradução para mensagens de validação.

#### Código de localidade
A localidade é simplesmente um código que identifica uma versão de um idioma. Ele é definido seguindo as especificações [ISO 639-1][639-1] e
[ISO 3166-1 alfa-2][3166-1]: duas letras minúsculas para o idioma, opcionalmente seguidas de um sublinhado e duas
letras maiúsculas que identificam o código do país ou da região. Para [idiomas raros][rare], são usadas três letras.

Para alguns falantes, a parte do país pode parecer redundante. Na verdade, alguns idiomas têm dialetos em diferentes
países, como o alemão austríaco (`de_AT`) ou o português brasileiro (`pt_BR`). A segunda parte é utilizada para distinguir
entre esses dialetos - quando não está presente, é tomado como uma versão "genérica" ou "híbrida" do idioma.

### Estrutura de Diretórios
Para utilizar o Gettext, precisaremos aderir a uma estrutura específica de pastas. Primeiro, você precisará selecionar
uma raiz para seus arquivos l10n em seu repositório de origem. Dentro dela, você terá uma pasta para cada localidade
necessária, e uma pastta fixa `LC_MESSAGES' que conterá todos os seus pares PO/MO. 
Exemplo:

{% highlight console %}
<raiz do projeto>
├─ src/
├─ templates/
└─ locales/
├─ forum.pot
├─ site.pot
├─ de/
│  └─ LC_MESSAGES/
│     ├─ forum.mo
│     ├─ forum.po
│     ├─ site.mo
│     └─ site.po
├─ es_ES/
│  └─ LC_MESSAGES/
│     └─ ...
├─ fr/
│  └─ ...
├─ pt_BR/
│  └─ ...
└─ pt_PT/
└─ ...
{% endhighlight %}

### Formas plurais
Como dissemos na introdução, diferentes idiomas podem ter regras plurais diferentes. No entanto, o gettext nos salva
deste problema mais uma vez. Ao criar um novo arquivo `.po', você terá que declarar as [regras plurais][plural] para isso
e as peças traduzidas que são suscetíveis ao plural terão uma forma diferente para cada uma dessas regras. Quando
chamar o Gettext em código, você terá que especificar o número relacionado à sentença, e ele irá determinar a forma a
ser usada - mesmo usando substituição de strings, se necessário.

As regras plurais incluem o número de plurals disponíveis e um teste booleano com `n` que definiria em qual regra o
número dado cai (começando a contagem com 0). Por exemplo:

- Japonês: `nplurals=1; plural=0` - apenas uma regra
- Inglês: `nplurals=2; plural=(n != 1);` - duas regras, a primeira se N é 1, segunda regra caso contrário
- Português brasileiro: `nplurals=2; plural=(n > 1);` - duas regras, a segunda se N for maior que 1, a primeira caso contrário

Now that you understood the basis of how plural rules works - and if you didn't, please look at a deeper explanation
on the [LingoHub tutorial][lingohub_plurals] -, you might want to copy the ones you need from a [list][plural] instead
of writing them by hand.

When calling out Gettext to do localization on sentences with counters, you will have to provide it the
related number as well. Gettext will work out what rule should be in effect and use the correct localized version.
You will need to include in the `.po` file a different sentence for each plural rule defined.

### Sample implementation
After all that theory, let's get a little practical. Here's an excerpt of a `.po` file - don't mind with its format,
but with the overall content instead; you will learn how to edit it easily later:

{% highlight po %}
msgid ""
msgstr ""
"Language: pt_BR\n"
"Content-Type: text/plain; charset=UTF-8\n"
"Plural-Forms: nplurals=2; plural=(n > 1);\n"

msgid "We are now translating some strings"
msgstr "Nós estamos traduzindo algumas strings agora"

msgid "Hello %1$s! Your last visit was on %2$s"
msgstr "Olá %1$s! Sua última visita foi em %2$s"

msgid "Only one unread message"
msgid_plural "%d unread messages"
msgstr[0] "Só uma mensagem não lida"
msgstr[1] "%d mensagens não lidas"
{% endhighlight %}

The first section works like a header, having the `msgid` and `msgstr` especially empty. It describes the file encoding,
plural forms and other things that are less relevant.
The second section translates a simple string from English to
Brazilian Portuguese, and the third does the same, but leveraging string replacement from [`sprintf`][sprintf] so the
translation may contain the user name and visit date.
The last section is a sample of pluralization forms, displaying
the singular and plural version as `msgid` in English and their corresponding translations as `msgstr` 0 and 1
(following the number given by the plural rule). There, string replacement is used as well so the number can be seen
directly in the sentence, by using `%d`. The plural forms always have two `msgid` (singular and plural), so it is
advised not to use a complex language as the source of translation.

### Discussion on l10n keys
As you might have noticed, we are using as source ID the actual sentence in English. That `msgid` is the same used
throughout all your `.po` files, meaning other languages will have the same format and the same `msgid` fields but
translated `msgstr` lines.

Talking about translation keys, there are two main "schools" here:

1. _`msgid` as a real sentence_.
   The main advantages are:
  - if there are pieces of the software untranslated in any given language, the key displayed will still maintain some
    meaning. Example: if you happen to translate by heart from English to Spanish but need help to translate to French,
    you might publish the new page with missing French sentences, and parts of the website would be displayed in English
    instead;
  - it is much easier for the translator to understand what's going on and do a proper translation based on the
    `msgid`;
  - it gives you "free" l10n for one language - the source one;
  - The only disadvantage: if you need to change the actual text, you would need to replace the same `msgid`
    across several language files.

2. _`msgid` as a unique, structured key_.
   It would describe the sentence role in the application in a structured way, including the template or part where the
   string is located instead of its content.
  - it is a great way to have the code organized, separating the text content from the template logic.
  - however, that could bring problems to the translator that would miss the context. A source language file would be
    needed as a basis for other translations. Example: the developer would ideally have an `en.po` file, that
    translators would read to understand what to write in `fr.po` for instance.
  - missing translations would display meaningless keys on screen (`top_menu.welcome` instead of `Hello there, User!`
    on the said untranslated French page). That is good it as would force translation to be complete before publishing -
    however, bad as translation issues would be remarkably awful in the interface. Some libraries, though, include an
    option to specify a given language as "fallback", having a similar behavior as the other approach.

The [Gettext manual][manual] favors the first approach as, in general, it is easier for translators and users in
case of trouble. That is how we will be working here as well. However, the [Symfony documentation][symfony-keys] favors
keyword-based translation, to allow for independent changes of all translations without affecting templates as well.

### Everyday usage
In a typical application, you would use some Gettext functions while writing static text in your pages. Those sentences
would then appear in `.po` files, get translated, compiled into `.mo` files and then, used by Gettext when rendering
the actual interface. Given that, let's tie together what we have discussed so far in a step-by-step example:

#### 1. A sample template file, including some different gettext calls
{% highlight php %}
<?php include 'i18n_setup.php' ?>
<div id="header">
    <h1><?=sprintf(gettext('Welcome, %s!'), $name)?></h1>
    <!-- code indented this way only for legibility -->
    <?php if ($unread): ?>
        <h2><?=sprintf(
            ngettext('Only one unread message',
                     '%d unread messages',
                     $unread),
            $unread)?>
        </h2>
    <?php endif ?>
</div>

<h1><?=gettext('Introduction')?></h1>
<p><?=gettext('We\'re now translating some strings')?></p>
{% endhighlight %}

- [`gettext()`][func] simply translates a `msgid` into its corresponding `msgstr` for a given language. There's also
  the shorthand function `_()` that works the same way;
- [`ngettext()`][n_func] does the same but with plural rules;
- there's also [`dgettext()`][d_func] and [`dngettext()`][dn_func], that allows you to override the domain for a single
  call. More on domain configuration in the next example.

#### 2. A sample setup file (`i18n_setup.php` as used above), selecting the correct locale and configuring Gettext
{% highlight php %}
<?php
/**
 * Verifies if the given $locale is supported in the project
 * @param string $locale
 * @return bool
 */
function valid($locale) {
   return in_array($locale, ['en_US', 'en', 'pt_BR', 'pt', 'es_ES', 'es']);
}

//setting the source/default locale, for informational purposes
$lang = 'en_US';

if (isset($_GET['lang']) && valid($_GET['lang'])) {
    // the locale can be changed through the query-string
    $lang = $_GET['lang'];    //you should sanitize this!
    setcookie('lang', $lang); //it's stored in a cookie so it can be reused
} elseif (isset($_COOKIE['lang']) && valid($_COOKIE['lang'])) {
    // if the cookie is present instead, let's just keep it
    $lang = $_COOKIE['lang']; //you should sanitize this!
} elseif (isset($_SERVER['HTTP_ACCEPT_LANGUAGE'])) {
    // default: look for the languages the browser says the user accepts
    $langs = explode(',', $_SERVER['HTTP_ACCEPT_LANGUAGE']);
    array_walk($langs, function (&$lang) { $lang = strtr(strtok($lang, ';'), ['-' => '_']); });
    foreach ($langs as $browser_lang) {
        if (valid($browser_lang)) {
            $lang = $browser_lang;
            break;
        }
    }
}

// here we define the global system locale given the found language
putenv("LANG=$lang");

// this might be useful for date functions (LC_TIME) or money formatting (LC_MONETARY), for instance
setlocale(LC_ALL, $lang);

// this will make Gettext look for ../locales/<lang>/LC_MESSAGES/main.mo
bindtextdomain('main', '../locales');

// indicates in what encoding the file should be read
bind_textdomain_codeset('main', 'UTF-8');

// if your application has additional domains, as cited before, you should bind them here as well
bindtextdomain('forum', '../locales');
bind_textdomain_codeset('forum', 'UTF-8');

// here we indicate the default domain the gettext() calls will respond to
textdomain('main');

// this would look for the string in forum.mo instead of main.mo
// echo dgettext('forum', 'Welcome back!');
?>
{% endhighlight %}

#### 3. Preparing translation for the first run
One of the great advantages Gettext has over custom framework i18n packages is its extensive and powerful file format.
"Oh man, that’s quite hard to understand and edit by hand, a simple array would be easier!" Make no mistake,
applications like [Poedit] are here to help - _a lot_. You can get the program from [their website][poedit_download],
it’s free and available for all platforms. It’s a pretty easy tool to get used to, and a very powerful one at the same
time - using all features Gettext has available. This guide is based on PoEdit 1.8.

In the first run, you should select “File > New...” from the menu. You’ll be asked straight ahead for the language:
here you can select/filter the language you want to translate to, or use that format we mentioned before, such as
`en_US` or `pt_BR`.

Now, save the file - using that directory structure we mentioned as well. Then you should click “Extract from sources”,
and here you’ll configure various settings for the extraction and translation tasks. You’ll be able to find all those
later through “Catalog > Properties”:

- Source paths: here you must include all folders from the project where `gettext()` (and siblings) are called - this
  is usually your templates/views folder(s). This is the only mandatory setting;
- Translation properties:
  - Project name and version, Team and Team’s email address: useful information that goes in the .po file header;
  - Plural forms: here go those rules we mentioned before - there’s a link in there with samples as well. You can
    leave it with the default option most of the time, as PoEdit already includes a handy database of plural rules for
    many languages.
  - Charsets: UTF-8, preferably;
  - Source code charset: set here the charset used by your codebase - probably UTF-8 as well, right?
- Source keywords: The underlying software knows how `gettext()` and similar function calls look like in several
  programming languages, but you might as well create your own translation functions. It will be here you’ll add those
  other methods. This will be discussed later in the “Tips” section.

After setting those points it will run a scan through your source files to find all the localization calls. After every
scan PoEdit will display a summary of what was found and what was removed from the source files. New entries will fed
empty into the translation table, and you’ll start typing in the localized versions of those strings. Save it and a .mo
file will be (re)compiled into the same folder and ta-dah: your project is internationalized.

#### 4. Translating strings
As you may have noticed before, there are two main types of localized strings: simple ones and those with plural
forms. The first ones have simply two boxes: source and localized string. The source string cannot be modified as
Gettext/Poedit do not include the powers to alter your source files - you should change the source itself and rescan
the files. Tip: you may right-click a translation line and it will hint you with the source files and lines where that
string is being used.
On the other hand, plural form strings include two boxes to show the two source strings, and tabs so you can configure
the different final forms.

Whenever you change your sources and need to update the translations, just hit Refresh and Poedit will rescan the code,
removing non-existent entries, merging the ones that changed and adding new ones. It may also try to guess some
translations, based on other ones you did. Those guesses and the changed entries will receive a "Fuzzy" marker,
indicating it needs review, appearing golden in the list. It is also useful if you have a translation team and someone
tries to write something they are not sure about: just mark Fuzzy, and someone else will review later.

Finally, it is advised to leave "View > Untranslated entries first" marked, as it will help you _a lot_ to not forget
any entry. From that menu, you can also open parts of the UI that allow you to leave contextual information for
translators if needed.

### Tips & Tricks

#### Possible caching issues
If you are running PHP as a module on Apache (`mod_php`), you might face issues with the `.mo` file being cached. It
happens the first time it is read, and then, to update it, you might need to restart the server. On Nginx and PHP5 it
usually takes only a couple of page refreshes to refresh the translation cache, and on PHP7 it is rarely needed.

#### Additional helper functions
As preferred by many people, it is easier to use `_()` instead of `gettext()`. Many custom i18n libraries from
frameworks use something similar to `t()` as well, to make translated code shorter. However, that is the only function
that sports a shortcut. You might want to add in your project some others, such as `__()` or `_n()` for `ngettext()`,
or maybe a fancy `_r()` that would join `gettext()` and `sprintf()` calls. Other libraries, such as
[oscarotero's Gettext][oscarotero] also provide helper functions like these.

In those cases, you'll need to instruct the Gettext utility on how to extract the strings from those new functions.
Don't be afraid; it is very easy. It is just a field in the `.po` file, or a Settings screen on Poedit. In the editor,
that option is inside "Catalog > Properties > Source keywords". Remember: Gettext already knows the default functions
for many languages, so don’t be afraid if that list seems empty. You need to include there the specifications of those
new functions, following [a specific format][func_format]:

- if you create something like `t()` that simply returns the translation for a string, you can specify it as `t`.
  Gettext will know the only function argument is the string to be translated;
- if the function has more than one argument, you can specify in which one the first string is - and if needed, the
  plural form as well. For instance, if we call our function like this: `__('one user', '%d users', $number)`, the
  specification would be `__:1,2`, meaning the first form is the first argument, and the second form is the second
  argument. If your number comes as the first argument instead, the spec would be `__:2,3`, indicating the first form is
  the second argument, and so on.

After including those new rules in the `.po` file, a new scan will bring in your new strings just as easy as before.

### References

* [Wikipedia: i18n and l10n](https://en.wikipedia.org/wiki/Internationalization_and_localization)
* [Wikipedia: Gettext](https://en.wikipedia.org/wiki/Gettext)
* [LingoHub: PHP internationalization with gettext tutorial][lingohub]
* [PHP Manual: Gettext](https://secure.php.net/manual/book.gettext.php)
* [Gettext Manual][manual]

[Poedit]: https://poedit.net
[poedit_download]: https://poedit.net/download
[lingohub]: https://lingohub.com/blog/2013/07/php-internationalization-with-gettext-tutorial/
[lingohub_plurals]: https://lingohub.com/blog/2013/07/php-internationalization-with-gettext-tutorial/#Plurals
[plural]: http://docs.translatehouse.org/projects/localization-guide/en/latest/l10n/pluralforms.html
[gettext]: https://en.wikipedia.org/wiki/Gettext
[manual]: https://www.gnu.org/software/gettext/manual/gettext.html
[639-1]: https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes
[3166-1]: https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2
[rare]: https://www.gnu.org/software/gettext/manual/gettext.html#Rare-Language-Codes
[func_format]: https://www.gnu.org/software/gettext/manual/gettext.html#Language-specific-options
[aura-intl]: https://github.com/auraphp/Aura.Intl
[oscarotero]: https://github.com/oscarotero/Gettext
[symfony]: https://symfony.com/doc/current/components/translation.html
[zend]: https://docs.zendframework.com/zend-i18n/translation
[laravel]: https://laravel.com/docs/master/localization
[yii]: https://www.yiiframework.com/doc/guide/2.0/en/tutorial-i18n
[intl]: https://secure.php.net/manual/intro.intl.php
[ICU project]: http://www.icu-project.org
[symfony-keys]: https://symfony.com/doc/current/components/translation/usage.html#creating-translations

[sprintf]: https://secure.php.net/manual/function.sprintf.php
[func]: https://secure.php.net/manual/function.gettext.php
[n_func]: https://secure.php.net/manual/function.ngettext.php
[d_func]: https://secure.php.net/manual/function.dgettext.php
[dn_func]: https://secure.php.net/manual/function.dngettext.php
