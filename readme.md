# kss-node [![Build Status](https://secure.travis-ci.org/hughsk/kss-node.png?branch=master)](http://travis-ci.org/hughsk/kss-node)

This is a NodeJS implementation of [Knyle Style Sheets](https://github.com/kneath/kss) (KSS), "a documentation syntax for CSS". Beyond that, it's intended to have syntax readable by humans *and* machines - hence, this module can be used to create a "living styleguide". The methodology and ideas behind Knyle Style Sheets are contained in [the specification](https://github.com/kneath/kss/blob/master/SPEC.md).

kss-nodeはCSSスタイルガイドジェネレーターである[Knyle Style Sheets](https://github.com/kneath/kss)をNodeJSで実装したものです。
KSSの手法や考え方は[the specification](https://github.com/kneath/kss/blob/master/SPEC.md)の仕様書に記述されています。

There's an example project in the [demo directory](https://github.com/hughsk/kss-node/tree/master/demo) of this repo.

このリポジトリの[demo directory](https://github.com/hughsk/kss-node/tree/master/demo) の中にサンプルプロジェクトを用意しています。

## Installation
Just one line: `npm install kss`. If you want to use the command line interface, make sure the installation is global: `npm install -g kss`

`npm install kss`　の　ワンラインだけでインストールできます。もし、コマンドラインを使用したいのであれば、念のためグローバルにインストールします。`npm install -g kss`

## Using the CLI
To get you up and running quickly, a styleguide generator is included that can be used from the command line. It parses a directory of stylesheets and spits out a set of static HTML files like the ones used on this site.

コマンドラインからさくっと使えるように、スタイルガイドジェネレータが付属されています。スタイルガイドジェネレータは、スタイルシートのディレクトリをパースし、このサイトで使用されているような静的htmlを吐き出してくれます。


```
Usage:
 kss-node sourcedir [destdir] --init [directory] --{style,less,sass,stylus} [file]

Options:
  -t, --template  Use a custom template to build your styleguide [string]
  -s, --style     Compile and include a stylesheet               [string]
  -l, --less      Compile and include a LESS stylesheet          [string]
  -y, --stylus    Compile and include a Stylus stylesheet        [string]
  -S, --sass      Compile and include a SASS stylesheet          [string]
  -c, --css       Compile and include a CSS stylesheet           [string]
  -m, --mask      Use a custom mask for detecting stylesheets    [string]
  -i, --init      Create a new styleguide template to work from
```

You'll need to specify a directory containing all of your CSS files to be parsed for documentation as the first argument. Optionally, the second argument can be used to specify a target directory. Your CSS won't be included by default, hence you should use the `--less`, `--css`, etc. flags to point to a stylesheet to compile and include.

最初の引数には、解析したい全てのCSSファイルが含まれているディレクトリを指定してください。

2番目の引数には、ターゲットディレクトリを指定してください。

デフォルトではCSSは含まれませんので、あなたが使用しているもの`--less`, `--css`, などを指定して下さい。スタイルシートにコンパイルし、インクルードするためのフラグになっています。


You can generate a copy of the demo styleguide like so:

下記をコマンドラインで叩けばデモのスタイルガイドを生成できます。
    $ kss-node demo styleguide --less demo/styles.less

You can create your own templates too, either by editing the contents of the `lib/template` directory or using the `--template` flag to point to your own.

`lib/template`の中を直接編集するか、もしくは`--template`フラグで新たに作成したテンプレートを指定することで、オリジナルのテンプレートを作ることもできます。


The default template should look something like this:　![CLI Template Preview](https://raw.github.com/hughsk/kss-node/develop/demo/preview.png)

デフォルトのテンプレートはこのようになるはずです。　![CLI Template Preview](https://raw.github.com/hughsk/kss-node/develop/demo/preview.png)

## Using kss-node from Node
Check out the [Module API](https://github.com/hughsk/kss-node/wiki/Module-API) a full explanation. Here's an example:

[Module API](https://github.com/hughsk/kss-node/wiki/Module-API)の詳述を確認してください。下記に例を書きます。

``` javascript
var kss = require('kss'),
    options = {
        markdown: false
    };

kss.traverse('public/stylesheets/', options, function(err, styleguide) {
    if (err) throw err;

    styleguide.section('2.1.1')                                   // <KssSection>
    styleguide.section('2.1.1').description()                     // A button suitable for giving stars to someone
    styleguide.section('2.1.1').modifiers(0)                      // <KssModifier>
    styleguide.section('2.1.1').modifiers(0).name                 // ':hover'
    styleguide.section('2.1.1').modifiers(0).description          // 'Subtle hover highlight'
    styleguide.section('2.1.1').modifiers(':hover').description() // 'Subtle hover highlight'
    styleguide.section('2.1.1').modifiers(0).className()          // 'pseudo-class-hover'
    styleguide.section('2.x.x')                                   // [<KssSection>, ...]
    styleguide.section('2.1.1').modifiers()                       // [<KssModifier>, ...]
});
```

## Differences

Included are a few additional (optional) features to allow for completely automated styleguide generation.

差分で付属しているものは、スタイルガイド生成を完全に自動化することを可能にするための追加（オプション）機能です。


Take a look at the [demo project](http://github.com/hughsk/kss-node/tree/master/demo) for some examples.

[demo project](http://github.com/hughsk/kss-node/tree/master/demo)からいくつか例として見ていきましょう。


*Overview Document**. 
This "overview" page is generated from a Markdown file, which you should place in the directory you're generating from, just name it `styleguide.md` and it will be included in the final styleguide automatically.

この"overview"ページは、Markdownファイルから生成されるもので、Markdownファイルを生成しているディレクトリ内に、`styleguide.md`という名前で保存してください。自動的に最終的なスタイルガイド内に含まれます。


**HTML Markup**. In `kss-node` you can include sample markup in your styleguide entries. This is not only helpful for newcomers to a project, but is also used by the generator to include samples in your styleguide - just start a paragraph in your description section with `Markup:` like so:

`kss-node`はスタイルガイド内にサンプルマークアップを含めることができます。これはプロジェクトに新たに加わってくるメンバーだけに有用なだけではなく、ジェネレーターがあなたのスタイルガイドにhtmlサンプルを含める用途としても用いられます。
下記のように、説明の箇所に `Markup:`　と書くだけでスタートできます。

``` javascript
// Buttons
//
// Buttons can and should be clicked.
//
// Markup: <button class="button {$modifiers}">
//
// :hover - Highlight the button when hovered.
//
// Styleguide 1.1
```

**Multi-line descriptions**.
You can run your descriptions over multiple lines and paragraphs, and if you don't want to include the "modifiers" section you don't have to.

複数行で説明を記述することも可能です。そしてもしあなたが、"modifiers" sectionを含めたくなければ、含める必要はありません。

## Development
Forking, hacking, tearing apart of this module welcome - it still needs some cleaning up.

If you've got [mocha](https://github.com/visionmedia/mocha) installed, you can run the module's tests with `npm test` or `make test`.

To generate a new version of the demo styleguide, use `make gh-pages`. After committing your changes to master you can use the `gh-pages.sh` script to move this over to the `gh-pages` branch real quick.

## Contributors

* [Warin](http://github.com/Warin)
* [Manuel Goerlich](http://github.com/MaThGo)
* [Kevin Lamping](http://github.com/klamping)
