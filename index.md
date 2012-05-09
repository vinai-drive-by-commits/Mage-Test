---
layout: default
title: Mage-Test. Making Magento testable since 2010
---

This module provides a patched version of Mage\_Core enabling you to inject testing dependencies at run time. Due to the functionality of the Varien\_Autoloader the local code pool is prioritised over the core. Meaning that any code duplicated from the Mage vendor namespace into the local code pool will be used over the core.

This allows you to build and run functional controller tests in the same way you would with a standard Zend Framework Application using [Zend Test](http://framework.zend.com/manual/en/zend.test.phpunit.html). This mocks the Request and Response objects to that you can query the Response within a suite of tests.

<!--- Note that the space in the <a> tag is necessary for Jekyll MD parser to work correctly. It gets a bit muddled with self-closing tags -->
## <a id='requirements'> </a>Requirements  ##

* PHPUnit 3.5+

## <a id='quickstart' > </a> Quickstart
{% include video.html %}

Installing Mage-Test is really rather easy. For this guide we assume the you have installed:

* A working [Magento](http://www.magentocommerce.com/download) installation
* [Modman](https://github.com/colinmollenhour/modman)
* [Git](http://git-scm.com/)
* [PHPUnit](http://www.phpunit.de/manual/current/en/installation.html)

### Folder Structure

For this guide we are going to use a simple folder structure.

    /extensions
This is where all your third party extensions will go, including Mage-Test

    /public
This is your Magento root folder and Magento is installed there

    /tests
This is where your unit, functional and integration tests will go.

### Get Mage-Test

Using git we clone the GitHub repo into our extensions directory and it will download Mage-Test and put it in a `Mage-Test` directory

    cd extensions
    git clone https://github.com/alistairstead/Mage-Test.git

### Install Mage-Test using modman

With Mage-Test downloaded, we can use Modman to install it for us. Modman will create a bunch of symlinks to the correct places in our Magento installation.
From the Magento root directory, we can intialise Modman and then tell it create links to the Mage-Test module:

    cd ../public
    modman init
    modman link /absolute/path/to/extensions/Mage-Test

Alternately, you can use Modman to both download AND install Mage-Test:

	cd ../public
	modman init
	modman clone git://github.com/alistairstead/Mage-Test.git

That will download Mage-Test to `public/.modman/Mage-Test` and then create the necessary symlinks in Magento to install it.

### Setup PHPUnit

Mage-Test comes with the `bootstrap.php` and `phpunit.xml` files needed to configure PHPUnit. These will go in our `tests/` folder.
Mage-Test also comes with an autolader that will deal with loading the necessary Magento classes for us that you just need to `require_once` at the end of the `bootstrap.php`
You may need to edit the paths in the `bootstrap.php` to match your setup, in our case we need to change the paths to look like this:

{% highlight php %}
<?php
$includePath = array(
    __DIR__,
    __DIR__ . '/../public/app/code/community/',
    __DIR__ . '/../public/lib/',
    __DIR__ . '/../public/app',
    get_include_path()
);
set_include_path(implode(PATH_SEPARATOR, $includePath));
require_once 'MageTest/autoload.php';
{% endhighlight %}

For our purposes we can copy the `phpunit.xml` verbatim
{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<phpunit bootstrap="bootstrap.php" stopOnFailure="false" colors="false" syntaxCheck="false" verbose="false" processIsolation="false" stderr="true">
    <testsuite name="Mage Test Tests">
        <directory suffix="Test.php">tests</directory>
    </testsuite>
    <testsuite name="Magento Tests">
        <directory suffix="Test.php">src/tests</directory>
    </testsuite>

    <!-- <logging>
        <log type="coverage-html" target="build/coverage" title="Mage Test Coverage"
            charset="UTF-8" yui="true" highlight="true"
            lowUpperBound="35" highLowerBound="70"/>
        <log type="coverage-clover" target="build/logs/clover.xml"/>
        <log type="junit" target="build/logs/junit.xml" logIncompleteSkipped="false"/>
    </logging> -->
    <listeners>
        <listener class="MageTest_PhpUnit_Framework_TestListener"/>
    </listeners>
</phpunit>

{% endhighlight %}

### Write your first test

Since we are dealing with a vanilla Magento installation, we don't have any of our own modules to test, so this example test is rather contrived. We are going to test that `Mage::getModel()` returns an instance of the class we expect, in this case `Mage_Catalog_Model_Product`.
In `tests/unit/app/code/local/MagetestDemo/Demo/Model/DemoTest.php`
{% highlight php %}
<?php
class Magetestdemo_Demo_Model_DemoTest extends MageTest_PHPUnit_Framework_TestCase {

    public function test_getModel_returns_expected_object()
    {
        $this->assertInstanceOf('Mage_Catalog_Model_Product', Mage::getModel('catalog/product'));
    }
}
{% endhighlight %}

Once you have a test written just run PHPUnit from your `tests/` directory

    $ phpunit .


Happy testing!

## <a id='feature-request'> </a> Feature Request

If you have an idea of how to make this a better project or add functionality that would be of use the community then please [submit a feature request](https://github.com/alistairstead/Mage-Test/issues). Create a new ticket and add the label of 'Feature'.

## <a id='contributing'> </a> Contributing

<p>Developer IRC channel for MageTest is <a href="irc://irc.freenode.net/magetest">#magetest</a> on Freenode.</p>


1. [Fork it](https://github.com/alistairstead/Mage-Test/fork_select)
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Add tests for your new feature of bug fix.
4. Add your Feature or Fix to satisfy the tests.
5. Commit your changes (`git commit -am 'Added some feature'`)
6. Push to the branch (`git push origin my-new-feature`)
7. Create new Pull Request
