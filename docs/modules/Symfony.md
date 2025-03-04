---
layout: doc
title: Symfony - Codeception - Documentation
---



<div class="btn-group" role="group" style="float: right" aria-label="..."><a class="btn btn-default" href="https://github.com/Codeception/Codeception/blob/2.5/src/Codeception/Module/Symfony.php">source</a><a class="btn btn-default" href="https://github.com/Codeception/Codeception/blob/master/docs/modules/Symfony.md">master</a><a class="btn btn-default" href="https://github.com/Codeception/Codeception/blob/2.3/docs/modules/Symfony.md">2.3</a><a class="btn btn-default" href="https://github.com/Codeception/Codeception/blob/2.2/docs/modules/Symfony.md">2.2</a><a class="btn btn-default" href="https://github.com/Codeception/Codeception/blob/2.1/docs/modules/Symfony.md">2.1</a><a class="btn btn-default" href="https://github.com/Codeception/Codeception/blob/2.0/docs/modules/Symfony.md">2.0</a><a class="btn btn-default" href="https://github.com/Codeception/Codeception/blob/1.8/docs/modules/Symfony.md">1.8</a></div>

# Symfony


This module uses Symfony Crawler and HttpKernel to emulate requests and test response.

### Demo Project

<https://github.com/Codeception/symfony-demo>

### Config

#### Symfony 4.x

* app_path: 'src' - in Symfony 4 Kernel is located inside `src`
* environment: 'local' - environment used for load kernel
* kernel_class: 'App\Kernel' - kernel class name
* em_service: 'doctrine.orm.entity_manager' - use the stated EntityManager to pair with Doctrine Module.
* debug: true - turn on/off debug mode
* cache_router: 'false' - enable router caching between tests in order to [increase performance](http://lakion.com/blog/how-did-we-speed-up-sylius-behat-suite-with-blackfire)
* rebootable_client: 'true' - reboot client's kernel before each request

##### Example (`functional.suite.yml`) - Symfony 4 Directory Structure

    modules:
       enabled:
          - Symfony:
              app_path: 'src'
              environment: 'test'


#### Symfony 3.x

* app_path: 'app' - specify custom path to your app dir, where the kernel interface is located.
* var_path: 'var' - specify custom path to your var dir, where bootstrap cache is located.
* environment: 'local' - environment used for load kernel
* kernel_class: 'AppKernel' - kernel class name
* em_service: 'doctrine.orm.entity_manager' - use the stated EntityManager to pair with Doctrine Module.
* debug: true - turn on/off debug mode
* cache_router: 'false' - enable router caching between tests in order to [increase performance](http://lakion.com/blog/how-did-we-speed-up-sylius-behat-suite-with-blackfire)
* rebootable_client: 'true' - reboot client's kernel before each request

##### Example (`functional.suite.yml`) - Symfony 3 Directory Structure

    modules:
       enabled:
          - Symfony:
              app_path: 'app/front'
              var_path: 'var'
              environment: 'local_test'


#### Symfony 2.x

* app_path: 'app' - specify custom path to your app dir, where bootstrap cache and kernel interface is located.
* environment: 'local' - environment used for load kernel
* kernel_class: 'AppKernel' - kernel class name
* debug: true - turn on/off debug mode
* em_service: 'doctrine.orm.entity_manager' - use the stated EntityManager to pair with Doctrine Module.
* cache_router: 'false' - enable router caching between tests in order to [increase performance](http://lakion.com/blog/how-did-we-speed-up-sylius-behat-suite-with-blackfire)
* rebootable_client: 'true' - reboot client's kernel before each request

#### Example (`functional.suite.yml`) - Symfony 2.x Directory Structure

{% highlight yaml %}
   modules:
       - Symfony:
           app_path: 'app/front'
           environment: 'local_test'

{% endhighlight %}

### Public Properties

* kernel - HttpKernel instance
* client - current Crawler instance

### Parts

* services - allows to use Symfony DIC only with WebDriver or PhpBrowser modules.

Usage example:

{% highlight yaml %}

actor: AcceptanceTester
modules:
    enabled:
        - Symfony:
            part: SERVICES
        - Doctrine2:
            depends: Symfony
        - WebDriver:
            url: http://your-url.com
            browser: phantomjs

{% endhighlight %}


### Actions

#### _findElements

*hidden API method, expected to be used from Helper classes*
 
Locates element using available Codeception locator types:

* XPath
* CSS
* Strict Locator

Use it in Helpers or GroupObject or Extension classes:

{% highlight php %}

<?php
$els = $this->getModule('Symfony')->_findElements('.items');
$els = $this->getModule('Symfony')->_findElements(['name' => 'username']);

$editLinks = $this->getModule('Symfony')->_findElements(['link' => 'Edit']);
// now you can iterate over $editLinks and check that all them have valid hrefs

{% endhighlight %}

WebDriver module returns `Facebook\WebDriver\Remote\RemoteWebElement` instances
PhpBrowser and Framework modules return `Symfony\Component\DomCrawler\Crawler` instances

 * `param` $locator
 * `return` array of interactive elements


#### _getResponseContent

*hidden API method, expected to be used from Helper classes*
 
Returns content of the last response
Use it in Helpers when you want to retrieve response of request performed by another module.

{% highlight php %}

<?php
// in Helper class
public function seeResponseContains($text)
{
   $this->assertContains($text, $this->getModule('Symfony')->_getResponseContent(), "response contains");
}
?>

{% endhighlight %}

 * `return` string
@throws ModuleException


#### _loadPage

*hidden API method, expected to be used from Helper classes*
 
Opens a page with arbitrary request parameters.
Useful for testing multi-step forms on a specific step.

{% highlight php %}

<?php
// in Helper class
public function openCheckoutFormStep2($orderId) {
    $this->getModule('Symfony')->_loadPage('POST', '/checkout/step2', ['order' => $orderId]);
}
?>

{% endhighlight %}

 * `param` $method
 * `param` $uri
 * `param array` $parameters
 * `param array` $files
 * `param array` $server
 * `param null` $content


#### _request

*hidden API method, expected to be used from Helper classes*
 
Send custom request to a backend using method, uri, parameters, etc.
Use it in Helpers to create special request actions, like accessing API
Returns a string with response body.

{% highlight php %}

<?php
// in Helper class
public function createUserByApi($name) {
    $userData = $this->getModule('Symfony')->_request('POST', '/api/v1/users', ['name' => $name]);
    $user = json_decode($userData);
    return $user->id;
}
?>

{% endhighlight %}
Does not load the response into the module so you can't interact with response page (click, fill forms).
To load arbitrary page for interaction, use `_loadPage` method.

 * `param` $method
 * `param` $uri
 * `param array` $parameters
 * `param array` $files
 * `param array` $server
 * `param null` $content
 * `return` mixed|Crawler
@throws ExternalUrlException
@see `_loadPage`


#### _savePageSource

*hidden API method, expected to be used from Helper classes*
 
Saves page source of to a file

{% highlight php %}

$this->getModule('Symfony')->_savePageSource(codecept_output_dir().'page.html');

{% endhighlight %}
 * `param` $filename


#### amHttpAuthenticated
 
Authenticates user for HTTP_AUTH

 * `param` $username
 * `param` $password


#### amOnPage
 
Opens the page for the given relative URI.

{% highlight php %}

<?php
// opens front page
$I->amOnPage('/');
// opens /register page
$I->amOnPage('/register');

{% endhighlight %}

 * `param string` $page


#### amOnRoute
 
Opens web page using route name and parameters.

{% highlight php %}

<?php
$I->amOnRoute('posts.create');
$I->amOnRoute('posts.show', array('id' => 34));
?>

{% endhighlight %}

 * `param` $routeName
 * `param array` $params


#### attachFile
 
Attaches a file relative to the Codeception `_data` directory to the given file upload field.

{% highlight php %}

<?php
// file is stored in 'tests/_data/prices.xls'
$I->attachFile('input[@type="file"]', 'prices.xls');
?>

{% endhighlight %}

 * `param` $field
 * `param` $filename


#### checkOption
 
Ticks a checkbox. For radio buttons, use the `selectOption` method instead.

{% highlight php %}

<?php
$I->checkOption('#agree');
?>

{% endhighlight %}

 * `param` $option


#### click
 
Perform a click on a link or a button, given by a locator.
If a fuzzy locator is given, the page will be searched for a button, link, or image matching the locator string.
For buttons, the "value" attribute, "name" attribute, and inner text are searched.
For links, the link text is searched.
For images, the "alt" attribute and inner text of any parent links are searched.

The second parameter is a context (CSS or XPath locator) to narrow the search.

Note that if the locator matches a button of type `submit`, the form will be submitted.

{% highlight php %}

<?php
// simple link
$I->click('Logout');
// button of form
$I->click('Submit');
// CSS button
$I->click('#form input[type=submit]');
// XPath
$I->click('//form/*[@type="submit"]');
// link in context
$I->click('Logout', '#nav');
// using strict locator
$I->click(['link' => 'Login']);
?>

{% endhighlight %}

 * `param` $link
 * `param` $context


#### deleteHeader
 
Deletes the header with the passed name.  Subsequent requests
will not have the deleted header in its request.

Example:
{% highlight php %}

<?php
$I->haveHttpHeader('X-Requested-With', 'Codeception');
$I->amOnPage('test-headers.php');
// ...
$I->deleteHeader('X-Requested-With');
$I->amOnPage('some-other-page.php');
?>

{% endhighlight %}

 * `param string` $name the name of the header to delete.


#### dontSee
 
Checks that the current page doesn't contain the text specified (case insensitive).
Give a locator as the second parameter to match a specific region.

{% highlight php %}

<?php
$I->dontSee('Login');                         // I can suppose user is already logged in
$I->dontSee('Sign Up','h1');                  // I can suppose it's not a signup page
$I->dontSee('Sign Up','//body/h1');           // with XPath
$I->dontSee('Sign Up', ['css' => 'body h1']); // with strict CSS locator

{% endhighlight %}

Note that the search is done after stripping all HTML tags from the body,
so `$I->dontSee('strong')` will fail on strings like:

  - `<p>I am Stronger than thou</p>`
  - `<script>document.createElement('strong');</script>`

But will ignore strings like:

  - `<strong>Home</strong>`
  - `<div class="strong">Home</strong>`
  - `<!-- strong -->`

For checking the raw source code, use `seeInSource()`.

 * `param string` $text
 * `param array|string` $selector optional


#### dontSeeCheckboxIsChecked
 
Check that the specified checkbox is unchecked.

{% highlight php %}

<?php
$I->dontSeeCheckboxIsChecked('#agree'); // I suppose user didn't agree to terms
$I->seeCheckboxIsChecked('#signup_form input[type=checkbox]'); // I suppose user didn't check the first checkbox in form.
?>

{% endhighlight %}

 * `param` $checkbox


#### dontSeeCookie
 
Checks that there isn't a cookie with the given name.
You can set additional cookie params like `domain`, `path` as array passed in last argument.

 * `param` $cookie

 * `param array` $params


#### dontSeeCurrentUrlEquals
 
Checks that the current URL doesn't equal the given string.
Unlike `dontSeeInCurrentUrl`, this only matches the full URL.

{% highlight php %}

<?php
// current url is not root
$I->dontSeeCurrentUrlEquals('/');
?>

{% endhighlight %}

 * `param string` $uri


#### dontSeeCurrentUrlMatches
 
Checks that current url doesn't match the given regular expression.

{% highlight php %}

<?php
// to match root url
$I->dontSeeCurrentUrlMatches('~^/users/(\d+)~');
?>

{% endhighlight %}

 * `param string` $uri


#### dontSeeElement
 
Checks that the given element is invisible or not present on the page.
You can also specify expected attributes of this element.

{% highlight php %}

<?php
$I->dontSeeElement('.error');
$I->dontSeeElement('//form/input[1]');
$I->dontSeeElement('input', ['name' => 'login']);
$I->dontSeeElement('input', ['value' => '123456']);
?>

{% endhighlight %}

 * `param` $selector
 * `param array` $attributes


#### dontSeeEmailIsSent
 
Checks that no email was sent. This is an alias for seeEmailIsSent(0).

 * `[Part]` email


#### dontSeeInCurrentUrl
 
Checks that the current URI doesn't contain the given string.

{% highlight php %}

<?php
$I->dontSeeInCurrentUrl('/users/');
?>

{% endhighlight %}

 * `param string` $uri


#### dontSeeInField
 
Checks that an input field or textarea doesn't contain the given value.
For fuzzy locators, the field is matched by label text, CSS and XPath.

{% highlight php %}

<?php
$I->dontSeeInField('Body','Type your comment here');
$I->dontSeeInField('form textarea[name=body]','Type your comment here');
$I->dontSeeInField('form input[type=hidden]','hidden_value');
$I->dontSeeInField('#searchform input','Search');
$I->dontSeeInField('//form/*[@name=search]','Search');
$I->dontSeeInField(['name' => 'search'], 'Search');
?>

{% endhighlight %}

 * `param` $field
 * `param` $value


#### dontSeeInFormFields
 
Checks if the array of form parameters (name => value) are not set on the form matched with
the passed selector.

{% highlight php %}

<?php
$I->dontSeeInFormFields('form[name=myform]', [
     'input1' => 'non-existent value',
     'input2' => 'other non-existent value',
]);
?>

{% endhighlight %}

To check that an element hasn't been assigned any one of many values, an array can be passed
as the value:

{% highlight php %}

<?php
$I->dontSeeInFormFields('.form-class', [
     'fieldName' => [
         'This value shouldn\'t be set',
         'And this value shouldn\'t be set',
     ],
]);
?>

{% endhighlight %}

Additionally, checkbox values can be checked with a boolean.

{% highlight php %}

<?php
$I->dontSeeInFormFields('#form-id', [
     'checkbox1' => true,        // fails if checked
     'checkbox2' => false,       // fails if unchecked
]);
?>

{% endhighlight %}

 * `param` $formSelector
 * `param` $params


#### dontSeeInSource
 
Checks that the current page contains the given string in its
raw source code.

{% highlight php %}

<?php
$I->dontSeeInSource('<h1>Green eggs &amp; ham</h1>');

{% endhighlight %}

 * `param`      $raw


#### dontSeeInTitle
 
Checks that the page title does not contain the given string.

 * `param` $title



#### dontSeeLink
 
Checks that the page doesn't contain a link with the given string.
If the second parameter is given, only links with a matching "href" attribute will be checked.

{% highlight php %}

<?php
$I->dontSeeLink('Logout'); // I suppose user is not logged in
$I->dontSeeLink('Checkout now', '/store/cart.php');
?>

{% endhighlight %}

 * `param string` $text
 * `param string` $url optional


#### dontSeeOptionIsSelected
 
Checks that the given option is not selected.

{% highlight php %}

<?php
$I->dontSeeOptionIsSelected('#form input[name=payment]', 'Visa');
?>

{% endhighlight %}

 * `param` $selector
 * `param` $optionText



#### dontSeeResponseCodeIs
 
Checks that response code is equal to value provided.

{% highlight php %}

<?php
$I->dontSeeResponseCodeIs(200);

// recommended \Codeception\Util\HttpCode
$I->dontSeeResponseCodeIs(\Codeception\Util\HttpCode::OK);

{% endhighlight %}
 * `param` $code


#### fillField
 
Fills a text field or textarea with the given string.

{% highlight php %}

<?php
$I->fillField("//input[@type='text']", "Hello World!");
$I->fillField(['name' => 'email'], 'jon@mail.com');
?>

{% endhighlight %}

 * `param` $field
 * `param` $value


#### grabAttributeFrom
 
Grabs the value of the given attribute value from the given element.
Fails if element is not found.

{% highlight php %}

<?php
$I->grabAttributeFrom('#tooltip', 'title');
?>

{% endhighlight %}

 * `param` $cssOrXpath
 * `param` $attribute



#### grabCookie
 
Grabs a cookie value.
You can set additional cookie params like `domain`, `path` in array passed as last argument.

 * `param` $cookie

 * `param array` $params


#### grabFromCurrentUrl
 
Executes the given regular expression against the current URI and returns the first capturing group.
If no parameters are provided, the full URI is returned.

{% highlight php %}

<?php
$user_id = $I->grabFromCurrentUrl('~^/user/(\d+)/~');
$uri = $I->grabFromCurrentUrl();
?>

{% endhighlight %}

 * `param string` $uri optional



#### grabMultiple
 
Grabs either the text content, or attribute values, of nodes
matched by $cssOrXpath and returns them as an array.

{% highlight html %}

<a href="#first">First</a>
<a href="#second">Second</a>
<a href="#third">Third</a>

{% endhighlight %}

{% highlight php %}

<?php
// would return ['First', 'Second', 'Third']
$aLinkText = $I->grabMultiple('a');

// would return ['#first', '#second', '#third']
$aLinks = $I->grabMultiple('a', 'href');
?>

{% endhighlight %}

 * `param` $cssOrXpath
 * `param` $attribute
 * `return` string[]


#### grabPageSource
 
Grabs current page source code.

@throws ModuleException if no page was opened.

 * `return` string Current page source code.


#### grabService
 
Grabs a service from Symfony DIC container.
Recommended to use for unit testing.

{% highlight php %}

<?php
$em = $I->grabService('doctrine');
?>

{% endhighlight %}

 * `param` $service
 * `[Part]` services


#### grabServiceFromContainer
 
Grabs a service from Symfony DIC container.
Recommended to use for unit testing.

{% highlight php %}

<?php
$em = $I->grabServiceFromContainer('doctrine');
?>

{% endhighlight %}

 * `param` $service
 * `[Part]` services
@deprecated Use grabService instead


#### grabTextFrom
 
Finds and returns the text contents of the given element.
If a fuzzy locator is used, the element is found using CSS, XPath,
and by matching the full page source by regular expression.

{% highlight php %}

<?php
$heading = $I->grabTextFrom('h1');
$heading = $I->grabTextFrom('descendant-or-self::h1');
$value = $I->grabTextFrom('~<input value=(.*?)]~sgi'); // match with a regex
?>

{% endhighlight %}

 * `param` $cssOrXPathOrRegex



#### grabValueFrom
 
 * `param` $field

 * `return` array|mixed|null|string


#### haveHttpHeader
 
Sets the HTTP header to the passed value - which is used on
subsequent HTTP requests through PhpBrowser.

Example:
{% highlight php %}

<?php
$I->haveHttpHeader('X-Requested-With', 'Codeception');
$I->amOnPage('test-headers.php');
?>

{% endhighlight %}

To use special chars in Header Key use HTML Character Entities:
Example:
Header with underscore - 'Client_Id'
should be represented as - 'Client&#x0005F;Id' or 'Client&#95;Id'

{% highlight php %}

<?php
$I->haveHttpHeader('Client&#95;Id', 'Codeception');
?>

{% endhighlight %}

 * `param string` $name the name of the request header
 * `param string` $value the value to set it to for subsequent
       requests


#### invalidateCachedRouter
 
Invalidate previously cached routes.


#### moveBack
 
Moves back in history.

 * `param int` $numberOfSteps (default value 1)


#### onReconfigure
 
HOOK to be executed when config changes with `_reconfigure`.


#### persistService
 
Get service $serviceName and add it to the lists of persistent services.
If $isPermanent then service becomes persistent between tests

 * `param string`  $serviceName
 * `param boolean` $isPermanent


#### rebootClientKernel
 
Reboot client's kernel.
Can be used to manually reboot kernel when 'rebootable_client' => false

{% highlight php %}

<?php
...
perform some requests
...
$I->rebootClientKernel();
...
perform other requests
...

?>

{% endhighlight %}



#### resetCookie
 
Unsets cookie with the given name.
You can set additional cookie params like `domain`, `path` in array passed as last argument.

 * `param` $cookie

 * `param array` $params


#### see
 
Checks that the current page contains the given string (case insensitive).

You can specify a specific HTML element (via CSS or XPath) as the second
parameter to only search within that element.

{% highlight php %}

<?php
$I->see('Logout');                        // I can suppose user is logged in
$I->see('Sign Up', 'h1');                 // I can suppose it's a signup page
$I->see('Sign Up', '//body/h1');          // with XPath
$I->see('Sign Up', ['css' => 'body h1']); // with strict CSS locator

{% endhighlight %}

Note that the search is done after stripping all HTML tags from the body,
so `$I->see('strong')` will return true for strings like:

  - `<p>I am Stronger than thou</p>`
  - `<script>document.createElement('strong');</script>`

But will *not* be true for strings like:

  - `<strong>Home</strong>`
  - `<div class="strong">Home</strong>`
  - `<!-- strong -->`

For checking the raw source code, use `seeInSource()`.

 * `param string` $text
 * `param array|string` $selector optional


#### seeCheckboxIsChecked
 
Checks that the specified checkbox is checked.

{% highlight php %}

<?php
$I->seeCheckboxIsChecked('#agree'); // I suppose user agreed to terms
$I->seeCheckboxIsChecked('#signup_form input[type=checkbox]'); // I suppose user agreed to terms, If there is only one checkbox in form.
$I->seeCheckboxIsChecked('//form/input[@type=checkbox and @name=agree]');
?>

{% endhighlight %}

 * `param` $checkbox


#### seeCookie
 
Checks that a cookie with the given name is set.
You can set additional cookie params like `domain`, `path` as array passed in last argument.

{% highlight php %}

<?php
$I->seeCookie('PHPSESSID');
?>

{% endhighlight %}

 * `param` $cookie
 * `param array` $params


#### seeCurrentRouteIs
 
Checks that current url matches route.

{% highlight php %}

<?php
$I->seeCurrentRouteIs('posts.index');
$I->seeCurrentRouteIs('posts.show', array('id' => 8));
?>

{% endhighlight %}

 * `param` $routeName
 * `param array` $params


#### seeCurrentUrlEquals
 
Checks that the current URL is equal to the given string.
Unlike `seeInCurrentUrl`, this only matches the full URL.

{% highlight php %}

<?php
// to match root url
$I->seeCurrentUrlEquals('/');
?>

{% endhighlight %}

 * `param string` $uri


#### seeCurrentUrlMatches
 
Checks that the current URL matches the given regular expression.

{% highlight php %}

<?php
// to match root url
$I->seeCurrentUrlMatches('~^/users/(\d+)~');
?>

{% endhighlight %}

 * `param string` $uri


#### seeElement
 
Checks that the given element exists on the page and is visible.
You can also specify expected attributes of this element.

{% highlight php %}

<?php
$I->seeElement('.error');
$I->seeElement('//form/input[1]');
$I->seeElement('input', ['name' => 'login']);
$I->seeElement('input', ['value' => '123456']);

// strict locator in first arg, attributes in second
$I->seeElement(['css' => 'form input'], ['name' => 'login']);
?>

{% endhighlight %}

 * `param` $selector
 * `param array` $attributes
@return


#### seeEmailIsSent
 
Checks if the desired number of emails was sent.
If no argument is provided then at least one email must be sent to satisfy the check.

{% highlight php %}

<?php
$I->seeEmailIsSent(2);
?>

{% endhighlight %}

 * `param null|int` $expectedCount


#### seeInCurrentRoute
 
Checks that current url matches route.
Unlike seeCurrentRouteIs, this can matches without exact route parameters

{% highlight php %}

<?php
$I->seeCurrentRouteMatches('my_blog_pages');
?>

{% endhighlight %}

 * `param` $routeName


#### seeInCurrentUrl
 
Checks that current URI contains the given string.

{% highlight php %}

<?php
// to match: /home/dashboard
$I->seeInCurrentUrl('home');
// to match: /users/1
$I->seeInCurrentUrl('/users/');
?>

{% endhighlight %}

 * `param string` $uri


#### seeInField
 
Checks that the given input field or textarea *equals* (i.e. not just contains) the given value.
Fields are matched by label text, the "name" attribute, CSS, or XPath.

{% highlight php %}

<?php
$I->seeInField('Body','Type your comment here');
$I->seeInField('form textarea[name=body]','Type your comment here');
$I->seeInField('form input[type=hidden]','hidden_value');
$I->seeInField('#searchform input','Search');
$I->seeInField('//form/*[@name=search]','Search');
$I->seeInField(['name' => 'search'], 'Search');
?>

{% endhighlight %}

 * `param` $field
 * `param` $value


#### seeInFormFields
 
Checks if the array of form parameters (name => value) are set on the form matched with the
passed selector.

{% highlight php %}

<?php
$I->seeInFormFields('form[name=myform]', [
     'input1' => 'value',
     'input2' => 'other value',
]);
?>

{% endhighlight %}

For multi-select elements, or to check values of multiple elements with the same name, an
array may be passed:

{% highlight php %}

<?php
$I->seeInFormFields('.form-class', [
     'multiselect' => [
         'value1',
         'value2',
     ],
     'checkbox[]' => [
         'a checked value',
         'another checked value',
     ],
]);
?>

{% endhighlight %}

Additionally, checkbox values can be checked with a boolean.

{% highlight php %}

<?php
$I->seeInFormFields('#form-id', [
     'checkbox1' => true,        // passes if checked
     'checkbox2' => false,       // passes if unchecked
]);
?>

{% endhighlight %}

Pair this with submitForm for quick testing magic.

{% highlight php %}

<?php
$form = [
     'field1' => 'value',
     'field2' => 'another value',
     'checkbox1' => true,
     // ...
];
$I->submitForm('//form[@id=my-form]', $form, 'submitButton');
// $I->amOnPage('/path/to/form-page') may be needed
$I->seeInFormFields('//form[@id=my-form]', $form);
?>

{% endhighlight %}

 * `param` $formSelector
 * `param` $params


#### seeInSource
 
Checks that the current page contains the given string in its
raw source code.

{% highlight php %}

<?php
$I->seeInSource('<h1>Green eggs &amp; ham</h1>');

{% endhighlight %}

 * `param`      $raw


#### seeInTitle
 
Checks that the page title contains the given string.

{% highlight php %}

<?php
$I->seeInTitle('Blog - Post #1');
?>

{% endhighlight %}

 * `param` $title



#### seeLink
 
Checks that there's a link with the specified text.
Give a full URL as the second parameter to match links with that exact URL.

{% highlight php %}

<?php
$I->seeLink('Logout'); // matches <a href="#">Logout</a>
$I->seeLink('Logout','/logout'); // matches <a href="/logout">Logout</a>
?>

{% endhighlight %}

 * `param string` $text
 * `param string` $url optional


#### seeNumberOfElements
 
Checks that there are a certain number of elements matched by the given locator on the page.

{% highlight php %}

<?php
$I->seeNumberOfElements('tr', 10);
$I->seeNumberOfElements('tr', [0,10]); // between 0 and 10 elements
?>

{% endhighlight %}
 * `param` $selector
 * `param mixed` $expected int or int[]


#### seeOptionIsSelected
 
Checks that the given option is selected.

{% highlight php %}

<?php
$I->seeOptionIsSelected('#form input[name=payment]', 'Visa');
?>

{% endhighlight %}

 * `param` $selector
 * `param` $optionText



#### seePageNotFound
 
Asserts that current page has 404 response status code.


#### seeResponseCodeIs
 
Checks that response code is equal to value provided.

{% highlight php %}

<?php
$I->seeResponseCodeIs(200);

// recommended \Codeception\Util\HttpCode
$I->seeResponseCodeIs(\Codeception\Util\HttpCode::OK);

{% endhighlight %}

 * `param` $code


#### seeResponseCodeIsBetween
 
Checks that response code is between a certain range. Between actually means [from <= CODE <= to]

 * `param` $from
 * `param` $to


#### seeResponseCodeIsClientError
 
Checks that the response code is 4xx


#### seeResponseCodeIsRedirection
 
Checks that the response code 3xx


#### seeResponseCodeIsServerError
 
Checks that the response code is 5xx


#### seeResponseCodeIsSuccessful
 
Checks that the response code 2xx


#### selectOption
 
Selects an option in a select tag or in radio button group.

{% highlight php %}

<?php
$I->selectOption('form select[name=account]', 'Premium');
$I->selectOption('form input[name=payment]', 'Monthly');
$I->selectOption('//form/select[@name=account]', 'Monthly');
?>

{% endhighlight %}

Provide an array for the second argument to select multiple options:

{% highlight php %}

<?php
$I->selectOption('Which OS do you use?', array('Windows','Linux'));
?>

{% endhighlight %}

Or provide an associative array for the second argument to specifically define which selection method should be used:

{% highlight php %}

<?php
$I->selectOption('Which OS do you use?', array('text' => 'Windows')); // Only search by text 'Windows'
$I->selectOption('Which OS do you use?', array('value' => 'windows')); // Only search by value 'windows'
?>

{% endhighlight %}

 * `param` $select
 * `param` $option


#### sendAjaxGetRequest
 
If your page triggers an ajax request, you can perform it manually.
This action sends a GET ajax request with specified params.

See ->sendAjaxPostRequest for examples.

 * `param` $uri
 * `param` $params


#### sendAjaxPostRequest
 
If your page triggers an ajax request, you can perform it manually.
This action sends a POST ajax request with specified params.
Additional params can be passed as array.

Example:

Imagine that by clicking checkbox you trigger ajax request which updates user settings.
We emulate that click by running this ajax request manually.

{% highlight php %}

<?php
$I->sendAjaxPostRequest('/updateSettings', array('notifications' => true)); // POST
$I->sendAjaxGetRequest('/updateSettings', array('notifications' => true)); // GET


{% endhighlight %}

 * `param` $uri
 * `param` $params


#### sendAjaxRequest
 
If your page triggers an ajax request, you can perform it manually.
This action sends an ajax request with specified method and params.

Example:

You need to perform an ajax request specifying the HTTP method.

{% highlight php %}

<?php
$I->sendAjaxRequest('PUT', '/posts/7', array('title' => 'new title'));


{% endhighlight %}

 * `param` $method
 * `param` $uri
 * `param` $params


#### setCookie
 
Sets a cookie with the given name and value.
You can set additional cookie params like `domain`, `path`, `expires`, `secure` in array passed as last argument.

{% highlight php %}

<?php
$I->setCookie('PHPSESSID', 'el4ukv0kqbvoirg7nkp4dncpk3');
?>

{% endhighlight %}

 * `param` $name
 * `param` $val
 * `param array` $params



#### submitForm
 
Submits the given form on the page, with the given form
values.  Pass the form field's values as an array in the second
parameter.

Although this function can be used as a short-hand version of
`fillField()`, `selectOption()`, `click()` etc. it has some important
differences:

 * Only field *names* may be used, not CSS/XPath selectors nor field labels
 * If a field is sent to this function that does *not* exist on the page,
   it will silently be added to the HTTP request.  This is helpful for testing
   some types of forms, but be aware that you will *not* get an exception
   like you would if you called `fillField()` or `selectOption()` with
   a missing field.

Fields that are not provided will be filled by their values from the page,
or from any previous calls to `fillField()`, `selectOption()` etc.
You don't need to click the 'Submit' button afterwards.
This command itself triggers the request to form's action.

You can optionally specify which button's value to include
in the request with the last parameter (as an alternative to
explicitly setting its value in the second parameter), as
button values are not otherwise included in the request.

Examples:

{% highlight php %}

<?php
$I->submitForm('#login', [
    'login' => 'davert',
    'password' => '123456'
]);
// or
$I->submitForm('#login', [
    'login' => 'davert',
    'password' => '123456'
], 'submitButtonName');


{% endhighlight %}

For example, given this sample "Sign Up" form:

{% highlight html %}

<form action="/sign_up">
    Login:
    <input type="text" name="user[login]" /><br/>
    Password:
    <input type="password" name="user[password]" /><br/>
    Do you agree to our terms?
    <input type="checkbox" name="user[agree]" /><br/>
    Select pricing plan:
    <select name="plan">
        <option value="1">Free</option>
        <option value="2" selected="selected">Paid</option>
    </select>
    <input type="submit" name="submitButton" value="Submit" />
</form>

{% endhighlight %}

You could write the following to submit it:

{% highlight php %}

<?php
$I->submitForm(
    '#userForm',
    [
        'user' => [
            'login' => 'Davert',
            'password' => '123456',
            'agree' => true
        ]
    ],
    'submitButton'
);

{% endhighlight %}
Note that "2" will be the submitted value for the "plan" field, as it is
the selected option.

You can also emulate a JavaScript submission by not specifying any
buttons in the third parameter to submitForm.

{% highlight php %}

<?php
$I->submitForm(
    '#userForm',
    [
        'user' => [
            'login' => 'Davert',
            'password' => '123456',
            'agree' => true
        ]
    ]
);

{% endhighlight %}

This function works well when paired with `seeInFormFields()`
for quickly testing CRUD interfaces and form validation logic.

{% highlight php %}

<?php
$form = [
     'field1' => 'value',
     'field2' => 'another value',
     'checkbox1' => true,
     // ...
];
$I->submitForm('#my-form', $form, 'submitButton');
// $I->amOnPage('/path/to/form-page') may be needed
$I->seeInFormFields('#my-form', $form);

{% endhighlight %}

Parameter values can be set to arrays for multiple input fields
of the same name, or multi-select combo boxes.  For checkboxes,
you can use either the string value or boolean `true`/`false` which will
be replaced by the checkbox's value in the DOM.

{% highlight php %}

<?php
$I->submitForm('#my-form', [
     'field1' => 'value',
     'checkbox' => [
         'value of first checkbox',
         'value of second checkbox',
     ],
     'otherCheckboxes' => [
         true,
         false,
         false
     ],
     'multiselect' => [
         'first option value',
         'second option value'
     ]
]);

{% endhighlight %}

Mixing string and boolean values for a checkbox's value is not supported
and may produce unexpected results.

Field names ending in `[]` must be passed without the trailing square
bracket characters, and must contain an array for its value.  This allows
submitting multiple values with the same name, consider:

{% highlight php %}

<?php
// This will NOT work correctly
$I->submitForm('#my-form', [
    'field[]' => 'value',
    'field[]' => 'another value',  // 'field[]' is already a defined key
]);

{% endhighlight %}

The solution is to pass an array value:

{% highlight php %}

<?php
// This way both values are submitted
$I->submitForm('#my-form', [
    'field' => [
        'value',
        'another value',
    ]
]);

{% endhighlight %}

 * `param` $selector
 * `param` $params
 * `param` $button


#### switchToIframe
 
Switch to iframe or frame on the page.

Example:
{% highlight html %}

<iframe name="another_frame" src="http://example.com">

{% endhighlight %}

{% highlight php %}

<?php
# switch to iframe
$I->switchToIframe("another_frame");

{% endhighlight %}

 * `param string` $name


#### uncheckOption
 
Unticks a checkbox.

{% highlight php %}

<?php
$I->uncheckOption('#notify');
?>

{% endhighlight %}

 * `param` $option


#### unpersistService
 
Remove service $serviceName from the lists of persistent services.

 * `param string` $serviceName

<p>&nbsp;</p><div class="alert alert-warning">Module reference is taken from the source code. <a href="https://github.com/Codeception/Codeception/tree/2.5/src/Codeception/Module/Symfony.php">Help us to improve documentation. Edit module reference</a></div>
