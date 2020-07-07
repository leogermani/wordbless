# WorDBless
WorDBless allows you to use WordPress core functions in your PHPUnit tests without having to set up a database and the whole WordPress environment

## Usage

### Require WorDBless

```
composer require automattic/wordbless --dev
```

### Make sure to copy db.php

Add this script to your `composer.json`:

```
    "scripts": {
        "post-update-cmd": "php -r \"copy('vendor/automattic/wordbless/src/dbless-wpdb.php', 'wordpress/wp-content/db.php');\""
    },
```

Alternatively, you can manually copy the file.

### Initialize it in your bootstrap file

In your PHP Unit bootstrap file add:

```
require_once __DIR__ . '/../vendor/autoload.php'; // adjust the path as needed

\WorDBless\Load::load();

```

That's it! You can now use WordPress core functions in your tests!

### Writing tests

Extend the `BaseTestCase` in order to have all the `setUp` and `tearDown`in place.

```PHP
class My_Tests extends \WorDBless\BaseTestCase {

	public function test_add() {
		add_option( 'test', 123 );
		$this->assertEquals( 123, get_option( 'test' ) );
	}

}
```

If you choose not to extend this base class, no problem, just make sure to call its `setUp` and `tearDown` methods.

## What will work and what will not work?

Disclaimer: This is still experimental, so all testing is very welcome.

Basically any WordPress core function will work. Things like `wp_parse_args`, `add_query_arg`, etc.

Hooks and filters will work.

Anything that uses the database WILL NOT work, unless you believe in magic.

EXCEPT that, magically, OPTIONS WILL WORK. `get_option`, `update_option`, `add_option` and `delete_option` should work fine!

### Populating default options

By default, only `site_url` and `home` options are populated with `http://example.org`.

If you want, you can add more options to be loaded by default. 

Just declare a `dbless_default_options()` function in your bootstrap and make it return an array where the keys are option names and values, options values.

## Example

See a live example here -> https://github.com/Automattic/jetpack/pull/16163
