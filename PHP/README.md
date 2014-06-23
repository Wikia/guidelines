Wikia will continue to follow the MediaWiki style guidelines for PHP writing code. The MediaWiki
guidelines for PHP can be found [here](http://www.mediawiki.org/wiki/Manual:Coding_conventions/PHP).

Wikia Specific Guidelines
-------------------------

Function length should be limited to one “page” in length where a page is intentionally loosely defined as what fits into your editor window.

Using the MediaWiki PHP Style Helper
------------------------------------

MediaWiki provides a code formatting helper ([stylize.php](https://git.wikimedia.org/blob/mediawiki%2Ftools%2Fcode-utils.git/master/stylize.php))
which is provided via a submodule in this repo under `mediawiki/tools/code-utils`. To use `stylize.php` on your code before you check in:

```sh
git clone git@github.com:Wikia/guidelines.git /usr/wikia/source/guidelines
cd /usr/wikia/source/guidelines; git submodule init && git submodule update
cd /usr/wikia/source/wiki
../guidelines/PHP/bin/php-mediawiki-stylize /path/to/your/file.php
```

If you want to stylize all of the edits that you have before you stage them you can do the following:

```sh
cd /usr/wikia/source/wiki
git diff --name-only | grep php | while read -r i; do ../guidelines/PHP/bin/php-mediawiki-stylize “$i”; done
```
