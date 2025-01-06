# php-mime-mail-parser

A fully tested email parser for PHP 8.0+ (mailparse extension wrapper).

It's the most effective PHP email parser around in terms of performance, foreign character encoding, attachment handling, and ease of use.
Internet Message Format RFC [822](https://tools.ietf.org/html/rfc822), [2822](https://tools.ietf.org/html/rfc2822), [5322](https://tools.ietf.org/html/rfc5322).

[![Latest Version](https://img.shields.io/packagist/v/php-mime-mail-parser/php-mime-mail-parser.svg?style=flat-square)](https://github.com/php-mime-mail-parser/php-mime-mail-parser/releases)
[![Total Downloads](https://img.shields.io/packagist/dt/php-mime-mail-parser/php-mime-mail-parser.svg?style=flat-square)](https://packagist.org/packages/php-mime-mail-parser/php-mime-mail-parser)
[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE)

## Why?

This extension can be used to...
 * Parse and read email from Postfix
 * Read messages (Filename extension: `.eml`)
 * Create webmail 
 * Store email information such a subject, HTML body, attachments, etc. into a database

## Is it reliable?

Yes. All known issues have been reproduced, fixed and tested.

We use GitHub Actions, Codecov, Codacy to help ensure code quality. You can see real-time statistics below:

[![CI](https://github.com/php-mime-mail-parser/php-mime-mail-parser/actions/workflows/main.yml/badge.svg?style=flat-square)](https://github.com/php-mime-mail-parser/php-mime-mail-parser/actions/workflows/main.yml)
[![Coverage](https://codecov.io/gh/php-mime-mail-parser/php-mime-mail-parser/branch/main/graph/badge.svg?token=wTSIbXJDL0)](https://codecov.io/gh/php-mime-mail-parser/php-mime-mail-parser)
[![Code Quality](https://app.codacy.com/project/badge/Grade/8cbfe0fcd84c4b2b9282b9a0b4467607)](https://www.codacy.com/gh/php-mime-mail-parser/php-mime-mail-parser/dashboard?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=php-mime-mail-parser/php-mime-mail-parser&amp;utm_campaign=Badge_Grade)

## How do I install it?

The easiest way is via [Composer](https://getcomposer.org/).

To install the latest version of PHP MIME Mail Parser, run the command below:

	composer require php-mime-mail-parser/php-mime-mail-parser

## Requirements

The following versions of PHP are supported:

* PHP 8.0
* PHP 8.1
* PHP 8.2
* PHP 8.3
* PHP 8.4

Previous Versions:

| PHP Compatibility | Version                     |
|-------------------|-----------------------------|
| HHVM              | [php-mime-mail-parser 2.11.1](https://github.com/php-mime-mail-parser/php-mime-mail-parser/releases/tag/2.11.1) |
| PHP 5.4           | [php-mime-mail-parser 2.11.1](https://github.com/php-mime-mail-parser/php-mime-mail-parser/releases/tag/2.11.1) |
| PHP 5.5           | [php-mime-mail-parser 2.11.1](https://github.com/php-mime-mail-parser/php-mime-mail-parser/releases/tag/2.11.1) |
| PHP 5.6           | [php-mime-mail-parser 3.0.4](https://github.com/php-mime-mail-parser/php-mime-mail-parser/releases/tag/3.0.4)  |
| PHP 7.0           | [php-mime-mail-parser 3.0.4](https://github.com/php-mime-mail-parser/php-mime-mail-parser/releases/tag/3.0.4)  |
| PHP 7.1           | [php-mime-mail-parser 5.0.5](https://github.com/php-mime-mail-parser/php-mime-mail-parser/releases/tag/5.0.5)  |
| PHP 7.2           | [php-mime-mail-parser 7.1.2](https://github.com/php-mime-mail-parser/php-mime-mail-parser/releases/tag/7.1.2)  |
| PHP 7.3           | [php-mime-mail-parser 7.1.2](https://github.com/php-mime-mail-parser/php-mime-mail-parser/releases/tag/7.1.2)  |
| PHP 7.4           | [php-mime-mail-parser 7.1.2](https://github.com/php-mime-mail-parser/php-mime-mail-parser/releases/tag/7.1.2)  |

Make sure you have the mailparse extension (http://php.net/manual/en/book.mailparse.php) properly installed. The command line `php -m | grep mailparse` needs to return "mailparse".


### Install mailparse extension

#### Debian, Ubuntu & derivatives
```
sudo apt install php-cli php-mailparse
```

#### MacOS
```
brew install php
pecl install mailparse
```

#### Other platforms
```
sudo apt install php-cli php-pear php-dev php-mbstring
pecl install mailparse
```

#### From source

AAAAMMDD should be `php-config --extension-dir`
```
git clone https://github.com/php/pecl-mail-mailparse.git
cd pecl-mail-mailparse
phpize
./configure
sed -i 's/#if\s!HAVE_MBSTRING/#ifndef MBFL_MBFILTER_H/' ./mailparse.c
make
sudo mv modules/mailparse.so /usr/lib/php/AAAAMMDD/
echo "extension=mailparse.so" | sudo tee /etc/php/8.4/mods-available/mailparse.ini
sudo phpenmod mailparse
```

#### Windows
You need to download mailparse DLL from http://pecl.php.net/package/mailparse and add the line `extension=php_mailparse.dll` to `php.ini` accordingly.

## How do I use it?

### Loading an email

You can load an email in 4 differents ways:

```php
require_once __DIR__.'/vendor/autoload.php';

$path = 'path/to/email.eml';
$parser = new PhpMimeMailParser\Parser();

// 1. Either specify a file path (string)
$parser->setPath($path); 

// 2. or specify the raw mime mail text (string)
$parser->setText(file_get_contents($path));

// 3. or specify a php file resource (stream)
$parser->setStream(fopen($path, "r"));

// 4. or specify a stream to work with a mail server (stream)
$parser->setStream(fopen("php://stdin", "r"));
```

### Get the metadata of the message

Get the sender and the receiver:

```php
$rawHeaderTo = $parser->getHeader('to');
// return "test" <test@example.com>, "test2" <test2@example.com>

$arrayHeaderTo = $parser->getAddresses('to');
// return [["display"=>"test", "address"=>"test@example.com", false]]

$rawHeaderFrom = $parser->getHeader('from');
// return "test" <test@example.com>

$arrayHeaderFrom = $parser->getAddresses('from');
// return [["display"=>"test", "address"=>"test@example.com", "is_group"=>false]]
```

Get the subject:

```php
$subject = $parser->getHeader('subject');
```

Get other headers:

```php
$stringHeaders = $parser->getHeadersRaw();
// return all headers as a string, no charset conversion

$arrayHeaders = $parser->getHeaders();
// return all headers as an array, with charset conversion
```

### Get the body of the message

```php
$text = $parser->getMessageBody('text');
// return the text version

$html = $parser->getMessageBody('html');
// return the html version

$htmlEmbedded = $parser->getMessageBody('htmlEmbedded');
// return the html version with the embedded contents like images

```

### Get attachments

Save all attachments in a directory

```php
$parser->saveAttachments('/path/to/save/attachments/');
// return all attachments saved in the directory (include inline attachments)

$parser->saveAttachments('/path/to/save/attachments/', false);
// return all attachments saved in the directory (exclude inline attachments)

// Save all attachments with the strategy ATTACHMENT_DUPLICATE_SUFFIX (default)
$parser->saveAttachments('/path/to/save/attachments/', false, PhpMimeMailParser\Parser::ATTACHMENT_DUPLICATE_SUFFIX);
// return all attachments saved in the directory: logo.jpg, logo_1.jpg, ..., logo_100.jpg, YY34UFHBJ.jpg

// Save all attachments with the strategy ATTACHMENT_RANDOM_FILENAME
$parser->saveAttachments('/path/to/save/attachments/', false, PhpMimeMailParser\Parser::ATTACHMENT_RANDOM_FILENAME);
// return all attachments saved in the directory: YY34UFHBJ.jpg and F98DBZ9FZF.jpg

// Save all attachments with the strategy ATTACHMENT_DUPLICATE_THROW
$parser->saveAttachments('/path/to/save/attachments/', false, PhpMimeMailParser\Parser::ATTACHMENT_DUPLICATE_THROW);
// return an exception when there is attachments duplicate.

```

Get all attachments

```php
$attachments = $parser->getAttachments();
// return an array of all attachments (include inline attachments)

$attachments = $parser->getAttachments(false);
// return an array of all attachments (exclude inline attachments)
```


Loop through all attachments
```php
foreach ($attachments as $attachment) {
    echo 'Filename : '.$attachment->getFilename().'<br>';
    // return logo.jpg
    
    echo 'Filesize : '.filesize($attach_dir.$attachment->getFilename()).'<br>';
    // return 1000
    
    echo 'Filetype : '.$attachment->getContentType().'<br>';
    // return image/jpeg
    
    echo 'MIME part string : '.$attachment->getMimePartStr().'<br>';
    // return the whole MIME part of the attachment
    
    $stream = $attachment->getStream();
    // get the stream of the attachment file

    $attachment->save('/path/to/save/myattachment/', PhpMimeMailParser\Parser::ATTACHMENT_DUPLICATE_SUFFIX);
    // return the path and the filename saved (same strategy available than saveAttachments)
}
```

## Postfix configuration to manage email from a mail server

To forward mails from [Postfix](http://www.postfix.org/) to the PHP script above, add this line at the end of your `/etc/postfix/master.cf`
(to specify myhook to send all emails to the script `test.php`):

```
myhook unix - n n - - pipe
  				flags=F user=www-data argv=php -c /etc/php5/apache2/php.ini -f /var/www/test.php ${sender} ${size} ${recipient}
```

Edit this line (register myhook)
```
smtp      inet  n       -       -       -       -       smtpd
        			-o content_filter=myhook:dummy
```

The PHP script must use the fourth method (see above) to work with this configuration.

And finally the easiest way is to use my SaaS https://mailcare.io


## Can I contribute?

Feel free to contribute!

	git clone https://github.com/php-mime-mail-parser/php-mime-mail-parser
	cd php-mime-mail-parser
	composer install
	./vendor/bin/phpunit

If you report an issue, please provide the raw email that triggered it. This helps us reproduce the issue and fix it more quickly.

## License

The php-mime-mail-parser/php-mime-mail-parser is open-sourced software licensed under the [MIT license](http://opensource.org/licenses/MIT)
