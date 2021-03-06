# EUCookieLaw

>  EUROPA websites must follow the Commission's guidelines on [privacy and data protection](http://ec.europa.eu/ipg/basics/legal/data_protection/index_en.htm) and inform 
  users that cookies are not being used to gather information unnecessarily.
   
>  The [ePrivacy directive](http://eur-lex.europa.eu/LexUriServ/LexUriServ.do?uri=CELEX:32002L0058:EN:HTML) – more specifically Article 5(3) – requires prior informed consent for storage for access to information stored on a user's terminal equipment. 
  In other words, you must ask users if they agree to most cookies and similar technologies (e.g. web beacons, Flash cookies, etc.) before the site starts to use them.

>  For consent to be valid, it must be informed, specific, freely given and must constitute a real indication of the individual's wishes.

In this context this solution lives.
It simply alters the default `document.cookie` behavior to disallow cookies to be written on the client side, until the user accept the agreement.
At the same time it blocks all third-party domains you have configured as cookie generators.

# Donations

If you find this script useful, and since I've noticed that nobody did this script before of me,
I'd like to receive [a donation](https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=me%40diegolamonica%2einfo&lc=IT&item_name=EU%20Cookie%20Law&no_note=0&currency_code=EUR&bn=PP%2dDonationsBF%3abtn_donateCC_LG%2egif%3aNonHostedGuest).   :)

# How to use

If you want to use it as wordpress plugin then skip the **Client side** and the **Server side** sections

## Client side

Download the script file `EUCookieLaw.js` 

Add this code in your HTML `head` section (better if before all others JavaScripts)
```html
<script src="EUCookieLaw.js"></script>
<script>
    new EUCookieLaw({
        languages: {
            Italiano: {
                title: 'Informativa sull\'uso dei cookie',
                message: 'In base ad una direttiva europea sulla privacy e la protezione dei dati personali, è necessario il tuo consenso prima di conservare i cookie nel tuo browser. Me lo consenti?',
                agreeLabel: 'Sono d\'accordo',
                disagreeLabel: 'Non sono d\'accordo'
            },
            English: {
                title: 'Cookie Policy',
                message: 'According to european directive about privacy you need to agree this policy before surfing this site.',
                agreeLabel: 'I agree',
                disagreeLabel: 'I disagree'
            }
        } 
    });
</script>
```

If the user accepts the agreement then EUCookieLaw will store a cookie for itself (to remember that the user accepted the agreement) named `__eucookielaw` with `true` as value,
that lives during the current session.  

### Customize the behavior
the `EUCookieLaw` initialization expect an Object with the following properties:

* `showBanner` (`boolean` default `false`) if you want to show a banner at the top of your page you need to set this 
  option to `true`. 

* `reload` (`boolean` default `false`)  if `true` the page will be refreshed after the user accepts the agreement. This is useful is used 
  in conjunction with the server side part.

* `message` is the message used by the default confirmation dialog. In the case of `showBanner`, the `message` can be an 
  HTML content.  
  > **NOTE: Since version 2.7.0 this property is deprecated in favor of `languages.<language>` object**

* `debug` (`boolean` default `false`)  if `true` will show in browser console some useful informations about script execution.

* `cookieEnabled` (`boolean` default `false`)  set to `true` to not show the banner. However this setting will change 
  once the user take a choice.

* `cookieRejected` (`boolean` default `false`)  set to `true` to not show the banner. However this setting will change 
  once the user take a choice.

* `duration` (`integer` default `0`) the number of days you want the cookie will expire. If `0`, it will produce a 
  session cookie. 

* `remember` (`boolean` default `true`) if setted to `true`, the user rejection will be remember through the current session 
  else the choice will be valid only for the current page.   

* `path` (`string` defualt `/`) defines the path where the consent cookie will be valid.

* `domain` (`string` default setted to `window.location.host` value) defines the domain which to apply the cookie.    
  > **Note:** Define it to `false` if the URL is defined by IP instead of domain.
  
* `cookieList` (`array` default `[]`) the list of techincal cookies the user cannot choose to reject. If some script try 
  to write one of the listed cookie it will be accepted.  
  > **TIP:** You can use the `*` wildchar as suffix to intend all the cookies that starts with a specific value (eg. `__umt*` will mean `__umta`, `__umtc` and so on).
   
* `blacklist` (`array` default `[]`) if some script try to inject HTML into the page trhough the `document.write` it will be allowed only if
  in the code is not present something that points to one of the `blacklist`ed domain.

* `reload` (`boolean` default `false`) if set to true, the page will be reloaded on user agreement.

* `raiseLoadEvent` (`boolean` default `true`) if set to `true` the page will raise the `load` event on user consent. 
  > **Note:** some javascript analisys tools requires the event load to be fired, at the same time some scripts would
   consider the `load` event to be fired just one time in the life of the page so you should check if all the script on
   your page is compliant with the same behavior to determine which is the best setting for you and your page.

#### Options available only if `showBanner` is `true` 

* `id` (`string` default `boolean` `false`) if not `false` the banner box will not be created and the script will assume
  that the banner is the one referred by the `id`.    
  > **NOTE:** do not set the hash (`#`) before the id (eg. **OK** `id: 'my-box'` **NO** `id: '#my-banner'`)
  
* `tag` if not empty, the script will use it as predefined tag for title content of the banner. The default value is **`h1`**. 
   If the value is an empty string the title is not shown. 
   
* `bannerTitle` will be the banner title, it will be used only if the `tag` value is set.  
  > **NOTE: Since version 2.7.0 this property is deprecated in favor of `languages.<language>` object**
  
* `agreeLabel` the agree button label. Default is `I agree`  
  > **NOTE: Since version 2.7.0 this property is deprecated in favor of `languages.<language>` object**

* `disagreeLabel` the disagreement button label. Default is an empty string. If not given the disagree button will not be shown.  
  > **NOTE: Since version 2.7.0 this property is deprecated in favor of `languages.<language>` object**

* `fixOn` it defines if the banner is fixed on top or bottom, default value, if not defined or empty, is `top`. Allowed values are `top`, `bottom` or `static`.

* `showAgreement` is the callback method that will show the dialog with the user agreement about the cookie usage. If you 
  use a synchronous mode to show a dialog (eg. `confirm` method) the `showAgreement` must return `true` if the user have 
  accepted the agreement, in all other cases (user rejected the agreement or in asynchronous mode) it must return `false`.

* `agreeOnScroll` if `true`, when the user will scroll the page, then the agreement is implicitly accepted. The default value is `false`.

* `minScroll` a number (strictly depending on `agreeOnScroll`=`true`) which defines the number of pixels the user need to scroll to apply consent. The default value if not defined is `100`. 

* `agreeOnClick` if `true`, the user express its conesnt by clicking wherever on the page (but outside the banner).

* `languages` allows the banner translation, this property keep a set of languages which ones contains the relative informations about the title, message and button labels.
```javascript
var settings = {
    // ...
    languages: {
        Italiano: {
            title: 'titolo',
            message: 'messaggio del banner',
            agreeLabel: "sono d'accordo",
            disagreeLabel: "non sono d'accordo"
        },
        English: {
            title: '...',
            message: '...',
            agreeLabel: "...",
            disagreeLabel: "..."
        },
        // ... and so on...
    }
};
```

Once `EUCookieLaw` is initialized, you can access some useful methods in your JavaScript:

* `enableCookies` enables the site to store cookies

* `reject` reject the cookies agreement

* `isRejected` if the user have rejected the request to store cookie

* `isCookieEnabled` if the site can store cookies

* `reconsider` allows the user to review again the banner and take a new choice.
  To invoke this function from everywhere in your policy page you can create a link or a button with the following code:  
```html
<a href="#" 
   onclick="(new EUCookieLaw()).reconsider(); return false;">
      Reconsider my choice
</a>
```

#### Custom agreement example

Synchronous mode ([see demo](http://diegolamonica.info/demo/cookielaw/demo1.html)):

```html
<script src="EUCookieLaw.js"></script>
<script>
    function myCustomAgreement(){
        if(!eu.isRejected()) {
            if (confirm('do you agree?')) {
                return true;
            }
            eu.reject();
        }
        return false;
    }

    new EUCookieLaw({
        showAgreement: myCustomAgreement
    });
</script>
```

Asynchronous mode ([see demo](http://diegolamonica.info/demo/cookielaw/demo2.html)): 

```html
<script src="EUCookieLaw.js"></script>
<script>

    function showDialog(){
        /*
         * Your custom dialog activator goes here
         */
     }
    function myCustomAgreement(){
        /* show some HTML-made dialog box */
        showDialog();
        return false;
    }

    new EUCookieLaw({
        showAgreement: myCustomAgreement
    });
</script>
```

With agreement banner ([see demo](http://diegolamonica.info/demo/cookielaw/demo3.html)): 
```html
<script src="EUCookieLaw.js"></script>
<script>

    new EUCookieLaw({
        message: "La legge prevede l'autorizzazione all'utilizzo dei cookie. Me la vuoi dare per favore?",
        showBanner: true,
        bannerTitle: 'Autorizzazione alla conservazione dei cookie',
        agreeLabel: 'Do il mio consenso',
        disagreeLabel: 'Nego il consenso',
        tag: 'h1'
    });
</script>
```

## Server Side

> **NOTE:** The server side usage is available only for servers which has PHP installed.

The server-side script intercept the output buffer and will remove the sent cookies when user has not yet approved the
agreement.

Then you should include the file `eucookielaw-header.php` as the first operation on your server.

This will ensure you that any of your script or CMS like Drupal, Joomla or whatever you are using, is able to 
write a cookie if the user doesn't given his consensus.

```php
// This must be the first line of code of your main, always called, file.
require_once 'eucookielaw-header.php'; 
```

However if the server already detected that the user agreed the cookie law agreement the 
script does not override the built-in function.

> **NOTE:** Some servers does not have enabled by default the PHP zlib extension, then you should upload the file `gzcompat.php` too to your server. The file is included on demand by `eucookielaw-header.php` if needed.
To ensure the right inclusion, both files (`eucookielaw-header.php` and `gzcompat.php` must reside in the same folder).

Further if you want to block some javascript elements you can do it by adding a `data-eucookielaw="block"` attribute to the `script` elements.
 
### Server side constants
If you want to block specific domains you can define in your script (before including `eucookielaw-header.php`) two constants:

* `EUCOOKIELAW_USE_DOM` if `true` and the class `DOMDocument` is available, then the default parser will use `DOMDocument`. In all other cases the `Regular Expressions` will be used to parse page contents.  
  > **Note:** It's suggested to enable this option as default. Disable it only if the page results is a damaged content.

* `EUCOOKIELAW_DISALLOWED_DOMAINS` a semicolon (`;`) separated list of URLs disallowed since the user does not accept the agreement.  
  Each space before and/or after each URL will be removed.  
  > **Note:** if the domain start by a dot (eg. `.google.com`) then all the related subdomains will be included in the temporary blacklist.
  
* `EUCOOKIELAW_LOOK_IN_TAGS` a pipe (`|`) separated list of tags where to search for the domains to block.   
  If not specified, the deafault tags are `iframe`, `script`, `link`, `img`, `embed` and `param`.
  
* `EUCOOKIELAW_LOOK_IN_SCRIPTS` a boolean value, if `true` the URLs defined in `EUCOOKIELAW_DISALLOWED_DOMAINS` will be searched in the `<script>...</script>` tags too.

* `EUCOOKIELAW_SEARCHBOT_AS_HUMAN` if `true` the search engines will be threated as humans (same contents, to avoid accidental [cloacking](https://en.wikipedia.org/wiki/Cloaking) contents).

* `EUCOOKIELAW_ALLOWED_COOKIES` the list (**must be a comma separated value**) of techincal cookies that the server is allowed to generate and that will not removed from headers.  
  > **TIP \#1:** You can use the `*` wildchar as suffix to intend all the cookies that starts with a specific value (eg. `__umt*` will mean `__umta`, `__umtc` and so on).  
  > **TIP \#2:** If you use just `*` all cookies generated by your Web Site are allowed.

* `EUCOOKIELAW_AUTOSTART` if you want to invoke late the `ob_start` then you should define this constant to `true`.  
  > **NOTE:** If you set this option to `true` you need to invoke lately by your own the `buffering` class method. 

* `EUCOOKIELAW_DISABLED` a boolean value, if `true` the class `EUCookieLawHeader` will not be instantiated when you include
  the `eucookielaw-cache.php` in your PHP scripts.

* `EUCOOKIELAW_DEBUG` a boolean value, if `true` the HTML output will report before each replacement the rule applied and at the beginning of the file it will show all the applied rules.  
  > **Important** do not keep it enabled on production environment.  
  > **Note:** in the beginning of your HTML file you can see `<!-- (EUCookieLaw Debug Enabled) -->` message followed by some other details. 
  Those messages are useful to understand what exactly is happening in your site.

* `EUCOOKIELAW_DEBUG_VERBOSITY` (optional, default value is `99`) if `EUCOOKIELAW_DEBUG` is set to `true` this constants sets the
  verbosity of the log. It can assume one of the following values:
    * `0`: Silent
    * `10`: Several debug log messages
    * `20`: Most of debug log messages
    * `99`: All the log messages

* `EUCOOKIELAW_LOG_FILE` (optional) if defined and `EUCOOKIELAW_DEBUG` is `true` the output log will be written in the file defined in this constant.

* `EUCOOKIELAW_BANNER_ADDITIONAL_CLASS` a string where to define the custom classes applied to the banner. 

* `EUCOOKIELAW_BANNER_TITLE` the title to show on the banner.

* `EUCOOKIELAW_BANNER_DESCRIPTION` the description to show into the banner.

* `EUCOOKIELAW_BANNER_AGREE_BUTTON` the label on the agree button

* `EUCOOKIELAW_BANNER_DISAGREE_BUTTON` the label on the disagree button. If not defined or defined as empty string then the disagree button will not be shown on the page.

* `EUCOOKIELAW_BANNER_AGREE_LINK` the link to apply the consent. To let this script to manage by its own the consent, the querystring on this link must contain also the argument **`__eucookielaw=agree`**.  
  this mean that if the link is `http://example.com/my-page.html?arg1=a&arg2=b` then you should append the suggested argument as follows: `http://example.com/my-page.html?arg1=a&arg2=b&__eucookielaw=agree`.
   
* `EUCOOKIELAW_BANNER_DISAGREE_LINK` the link to reject the consent. To let this script to manage by its own the rejection, the querystring on this link must contain also the argument **`__eucookielaw=disagree`**.  
  this mean that if the link is `http://example.com/my-page.html?arg1=a&arg2=b` then you should append the suggested argument as follows: `http://example.com/my-page.html?arg1=a&arg2=b&__eucookielaw=disagree`.

* `EUCOOKIELAW_IGNORED_URLS` the list of site urls where not to apply the cookie policy. The list of urls must be separated by new line character (`\n`).  
  In the URL is allowed the `*` wildchar that means everything.  
  > **Few Examples:**  
  > If you type `/sitemap.xml` as one of the ignored URL then, the exact match will be ignored.    
  > If you type `*/sitemap.xml` everything that ends with `/sitemap.xml` will be ignored.  
  > If you type `/sitemaps/*` everything in the site directory `/sitemaps/` will be ignored.  
  > If you type `/folder/*.xml` everything in the `/folder/` directory that has `.xml` extension will be ignored.

* `EUCOOKIELAW_BANNER_LANGUAGES` a string containing json encoded data relative to languages.  
   To produce this definition constant is worth to use the following script:
   
   ```php
   
   $languages = array(
       'English' => array(
            'title' => 'English title of the banner',
            'message' => 'English message of the banner',
            'agreeLabel' => 'Agree',
            'disagreeLabel' => 'Disagree'
       ),
       'Italiano' => array(
           'title' => 'Titolo del banner in italiano',
           'message' => 'Messaggio del banner in italiano',
           'agreeLabel' => 'Consento',
           'disagreeLabel' => 'Non consento'
       )
   );
   
   define('EUCOOKIELAW_BANNER_LANGUAGES', json_encode($languages) );
   
   ```
   
### How to manage by your own the reconsider link

While WordPress has its own shortcode to manage the reconsider button, in the standalone version you should produce a link with a specific argument into the querystring: `__eucookielaw=reconsider`.


## Using EUCookieLaw into WordPress

The plugin is available on [WordPress plugin directory](http://wordpress.org/plugins/eucookielaw/).

If you want install from this repository, just download the zip and install it in your WordPress.
The plugin actually supports translation in both Italian (by translation file) and English (default). 

The plugin is compliant (also read as **has been tested**) with **WP Super Cache**, **W3 Total Cache** and **Zen Cache** plugins to serve the right contents when the user has not yet approved the agreement.

### Shortcodes

Actually EUCookieLaw supports two shortcodes 
#### `EUCookieLawReconsider`
 
The purpose of this shortcode is to produce a button that allow user to choose again whether to consent or not the cookie policy.

It will show a link with `btn` and `btn-warning` classes and text defined in the `label` attribute.
If you don't define the `label` attribute the default value is `Reconsider`.

**Example:** `[EUCookieLawReconsider label="I want take another choice"]` 

#### `EUCookieLawBlock`
The purpose of this shortcode is to wrap contents into a post and make it available once the user agreed the policy.

**Example:** 
```html
[EUCookieLawBlock]
    <p>
        This content is blocked until user consent
    </p>
[EUCookieLawBlock]
```
### How to make the banner title and message translate in the proper language.

> **NOTE:** This section is no more useful since the new settings for multiple languages.

I've implemented the custom text-domain files ( `EUCookieLawCustom-it_IT.po` / `EUCookieLawCustom-it_IT.po` ).  
Remember that to get custom translations properly work, **you need to move the `EUCookieLawCustom` directory at the `plugins` directory level**.

To be more clear the custom directory will be: **`wp-content/plugins/EUCookieLawCustom`**
 
Then take the file default and you have to put 4 strings in your translation file:

* `Banner title`
* `Banner description`
* `I agree`
* `I disagree`
* `Reconsider`

Remember to put the above text in the plugin settings page (default behavior) and to produce the translation files 
(starting from the `default.po` located in the `EUCookieLawCustom` directory).

You can see a production example on my [personal WebSite](http://diegolamonica.info).

### Create a detailed policy privacy page

To ensure your site is law compliant, you should have a page where you describe to your user which are the third-party cookies, 
which is their purpose and how to disable them. And yes! Don't forget to put the link in the banner!

## CSS Cookie Banner Customization
The structure of generated banner (with the default heading tag settings) is the following:

```html
<div class="eucookielaw-banner fixedon-top" id="eucookielaw-135">
	<div class="well">
		<h1 class="banner-title">
			The banner title
		</h1>
		<div class="banner-message">
			The banner message
		</div>
		<ul id="eucookielaw-language-switcher">
		    <li onclick="(new EUCookieLaw()).switchLanguage('English'); return false;">
		        <a href="/?__eucookielaw=switch:English">English</a>
		    </li>
		    <li onclick="(new EUCookieLaw()).switchLanguage('Italiano'); return false;">
		        <a href="/?__eucookielaw=switch:Italiano">
		            Italiano
		        </a>
		    </li>
		</ul>
		<p class="banner-agreement-buttons text-right">
			<a class="disagree-button btn btn-danger" onclick="(new EUCookieLaw()).reject();">Disagree</a> 
			<a class="agree-button btn btn-primary" onclick="(new EUCookieLaw()).enableCookies();">Agree</a>
		</p>
	</div>
</div>
```

* `.eucookielaw-banner` is the banner container it will have a random `id` attribute name that 
starts always with `eucookielaw-` and then followed by a number between `0` and `200`.
* `.well` is the inner container
* `h1.banner-title` is the banner title
* `p.banner-message` is the banner html message
* `#eucookielaw-language-switcher` is the list of languages which the banner is available. 
  It is not visible if just one lanuages is available.
* `p.banner-agreement-buttons.text-right` is the buttons container for the agree/disagree buttons
* `a.disagree-button` is the disagree button it implements the CSS classes `btn` and `btn-danger`
* `a.disagree-button` is the agree button it implements the CSS classes `btn` and `btn-primary`

You can make your own CSS to build a custom aspect for the banner. 
However, if you prefer, you can start from the bundled CSS.  

> **NOTE:** If you are using the script as WordPress plugin, the custom CSS must be located in the directory `wp-content/plugins/EUCookieLawCustom/` 
and must be named `eucookielaw.css`. Then it will be read in conjunction with the default plugin CSS.

# Contribute

I'd like to translate this plugin in all european languages, but I'm limited to the Italian and English too.

If you want to get involved in this plugin development, then fork the repository, translate in your language and make a pull request!

# Changelog

## 2.7.2

* **BUGFIX**: If some contents to be blocked are present before the `EUCookieLaw.js` file inclusion the page will become broken.
* **BUGFIX**: \[WP\] `Warning: Constants may only evaluate to scalar values` bugfix (issue #89)
* Updated the version number

## 2.7.1
* **IMPROVEMENTS**: \[WP\] a better way to recognize if there is a cache plugin installed
* **BUGFIX**: \[WP\] `Warning: Constants may only evaluate to scalar values` bugfix (issue #87)
* **BUGFIX**: If the only language configured is **Default** a javascript error occours (issue #88)
* **BUGFIX**: \[WP\] INIReader.php file was missing (solves issue #86 and part of issue #87)
* **BUGFIX**: \[WP\] Resolved an issue that has broken the Customizer.


## 2.7.0
* **NEW**: In the JavaScript file `EUCookieLaw.js` now is available the variable `EUCOOKIELAW_VERSION` with the number of current version.
* **NEW**: Now you can set the cookie policy's banner with multiple languages
* **NEW**: \[WP\] Improved WordPress admin interface to a better management of the multiple languages.
* **NEW**: \[WP\] Multilingual no requires any multilingual plugins
* **NEW**: Now you can choose to raise the load event on user agreement.
* **IMPROVEMENTS**: The regexp eingine now takes care about Internet Explorer Conditional Comments (solves issue #84)
* **IMPROVEMENTS**: \[WP\] every minute the cron checks if the configuration files into cache are available to solve definitively the issues against WP Super Cache and W3 Total Cache plugins. 
* **IMPROVEMENTS**: \[WP\] When `wp-config.php` is not available in the site root, the plugin notify what to manually wrtite into it.
* **IMPROVEMENTS**: Now the banner message is nested into a `div` to better fit the most sites/users requirements.
* **BUGFIX**: \[WP\] Google Maps and Google Fonts were switched in *fast service selection* group
* **NOTICE**: Some definitions were marked as deprecated since this version  
* Minor bugfixes and general improvements
* Updated documentation
* Updated translation files
* Updated the version number

## 2.6.3
* **IMPROVEMENTS**: The regenerated contents via javascript (without page reload) are correctly parsed evenif there is a `document.write` call
* **IMPROVEMENTS**: if in the query string is present the `__eucookielaw` argument it will be redirected (with `301: Moved Permanently`) to the same resource without the argument to avoid the Google duplicated tags warning.
* **IMPROVEMENTS**: if not defined `EUCOOKIELAW_BANNER_ADDITIONAL_CLASS` will be automatically defined as empty.
* updated the version number

## 2.6.2
* **BUGFIX**: Removed an accidentally leaved `utf8_decode` method that broke the output in several servers.
* updated the version number

## 2.6.1
* **IMPROVEMENTS**: After consent the script raises the `window`'s `load` event to be compliant with some scripts
* **IMPROVEMENTS**: Setted DOMDocument Engine to keep the original spacing to avoid some strange behavior
* **BUGFIX**: In some circumstances the regexp engine turns in infinite loop
* updated the version number

## 2.6.0
* **NEW**: Now you can configure the URL where the banner must not be shown (Issue #69, #66, #61.
* **NEW**: Now you can set the debug level
* **IMPROVEMENTS**: Improved javascript to avoid full page reload
* **IMPROVEMENTS**: Improved Regular Expression parsing Engine
* **IMPROVEMENTS**: Improved DOMDocument parsing Engine
* **IMPROVEMENTS**: \[WP\] Minor admin panel reorganization
* **IMPROVEMENTS**: Better code readability in `eucookielaw-header.php`
* **BUGFIX**: W3TC Page Cache flush causes EUCookieLaw to not work properly (Issue #65).
* **BUGFIX**: Cache clear after saving not works properly causing a warning in error log file
* Minor bugfixes and general improvements
* updated documentation
* updated the version number

## 2.5.0
* **NEW**: Now you can define the domain where the cookie will be applied
* **IMPROVEMENTS**: Javascript page reload forces contents from server (ignoring browser cache)
* **BUGFIX**: `WP_CONTENT_DIR` defined instead of `EUCL_CONTENT_DIR` causes some problems if site is without cache.
* Minor bugfixes and general improvements
* updated documentation
* updated the version number

## 2.4.0
* **NEW**: Now you can set the number of pixels for consent on scroll
* **NEW**: If not configured (as constants) the agree and disagree links will be auto-generated by the server.
* **NEW**: \[WP\] If you type twice a blocked URL or if a rule is already covered from another one then it will be visually noticed. 
* **NEW**: \[WP\] You can now analyze your home page to which external URL are called and which ones is producing cookeis.
* **NEW**: \[WP\] If user agent contains the information `EUCookieLaw:<VERSION_NUMBER>` then it will bypass the cookielaw block (used by the site analyzer).
* **IMPROVEMENTS**: If you define an empty rule in disallowed URLS it will be ignored
* **IMPROVEMENTS**: \[WP\] Several improvements on admin page and admin JavaScript
* **IMPROVEMENTS**: On localhost (`127.0.0.1`) the domain defined for the technical cookie must be empty to grand compatibility with some browsers.
* **BUGFIX**: `header_remove` method in PHP prior 5.3 does not exists.
* **BUGFIX**: The non JavaScript version of banner was containing wrong consent/rejection URL
* **BUGFIX**: \[WP\] With some W3 Total Cache configuration, EUCookieLaw was producing invalid output
* **BUGFIX**: \[WP\] Path definition confilcts with NextGenGallery
* **BUGFIX**: \[WP\] On settings page the *Replaced scripts source* assumes the value of *Replaced iframe source* also if the value is correctly saved.
* updated translation files
* updated documentation
* updated the version number


## 2.3.2
* **BUGFIX**: \[WP\] JavaScript for the admin interface was corrupted.
* **BUGFIX**: \[WP\] Unable to save settings due to a `1` accidentally placed in the wrong place.
* **IMPROVEMENTS**: \[WP\] Admin interface minor improvements

## 2.3.0
* **NEW**: Now there are two parsing engine, one based on regular expressions and one based on DOMDocument.
* **NEW**: \[WP\] Now you can import and export settings to apply the same contents on multiple sites easly.
* **NEW**: Now you can write debug informations on file.
* **NEW**: \[WP\] When the plugin's debug is enabled you will see an alert on every admin page.
* **NEW**: New theme `floating` available.
* **IMPROVEMENTS**: \[WP\] Admin interface improved
* **IMPROVEMENTS**: Improved documentation
* updated translation files
* updated documentation
* updated the version number

## 2.2.2
* **IMPROVEMENTS**: Some JavaScript were not detected by the server if formatted in certain formats.
* **BUGFIX**: \[WP\] When W3 Total Cache is enabled and you do not have right permissions on file the message as quite cryptical.

## 2.2.0
* **NEW**: \[WP\] On tinyMCE (visual editor) you have the EUCookieLaw helpers
* **NEW**: \[WP\] Visual shortcodes in the visual editor
* **NEW**: Now you can define which is the default file replacement for `iframe`s and `script`s.
* **IMPROVEMENT**: Some improvements in admin area page
* **IMPROVEMENT**: Some improvements in content identification
* **IMPROVEMENT**: \[WP\] Now only administrators can access the settings page
* **BUGFIX**: Due to a typos the client side cookies (generated by JavaScript) are always written
* updated the version number

## 2.1.4
* **IMPROVEMENT**: If not defined the `EUCOOKIELAW_BANNER_DISAGREE_BUTTON` the disagree button will not be shown on the page.
* **IMPROVEMENT**: Removed the session/local storage in favor of technical session cookie for storing the user rejection
* **IMPROVEMENT**: Improved the way to detect if the cookie is approved or rejected
* **IMPROVEMENT**: Uniformed the way to write the technical cookie `__eucookielaw`
* **IMPROVEMENT**: Improved the way how the banner is removed
* **IMPROVEMENT**: Updated missing pieces in documentation.
* **IMPROVEMENT**: Optimized behavior when asked reload of contents after consent.
* **BUGFIX**: Resolved an [anicient related firefox issue](https://bugzilla.mozilla.org/show_bug.cgi?id=356558)
* **BUGFIX**: \[WP\] if the disabled option is set to yes, neither the JavaScript and CSS must be loaded on the page.
* **BUGFIX**: Minor bugfixes in JavaScript
* updated the minor version number
* updated documentation

## 2.1.0
* **BUGFIX**: when PHP does not have gzdecode the method is implemented on needs.
* **BUGFIX**: Internet Explorer and some mobile Browser does not recognize the `instance` variable as `EUCookieLaw` object causing a bad banner behavior.
* **BUGFIX**: \[WP\] NextGenGallery has some weird behavior sometimes (skipped to load the locker if it is a NGG URL.
* **IMPROVEMENT**: \[WP\] The plugin now tries to write into `wp-config.php` only if there is another cache plugin enabled on the site.
* **IMPROVEMENT**: EUCookieLaw related PHP Warnings threated as required

## 2.0.2
* **CRITICAL**: 
Most of WordPress sites uses a FTP settings for writing files. Used native `file_get_contents` and `file_put_contents` 
to write data into some files for a better user experience.

## 2.0
* **NEW:** [\WP\] Full compliant with any cache plugin (actually successfully tested with **WP Super Cache**, **W3 Total Cache**, **Zen Cache**)
* **NEW:** The banner is now visible either with and without javascript enabled.
* **NEW:** User consent whenever he clicks on an element of the page (Issue [#12](https://github.com/diegolamonica/EUCookieLaw/issues/12))
* **NEW:** You can list the allowed cookies before consent (aka *Technical Cookies*). This solves the issue [#15](https://github.com/diegolamonica/EUCookieLaw/issues/15)
* **NEW:** Now Google Analytics is able to write cookies via JavaScript (if configured) (Issue [#15](https://github.com/diegolamonica/EUCookieLaw/issues/15))
* **NEW:** \[WP\] You can enable/disable the banner on frontend (Issue [#20](https://github.com/diegolamonica/EUCookieLaw/issues/20))
* **NEW:** \[WP\] You can enable/disable the banner on the login page (Issue [#21](https://github.com/diegolamonica/EUCookieLaw/issues/21))
* **NEW:** You can set the "reload on scroll" (Issue [#26](https://github.com/diegolamonica/EUCookieLaw/issues/26))
* **NEW:** \[WP\] Added the WPML XML Configuration File for a better WPML compatibility.
* **IMPROVEMENT:** \[WP\] Lack of documentation on certain admin fields (Issue [#27](https://github.com/diegolamonica/EUCookieLaw/issues/27))
* **IMPROVEMENT:** Most of PHP Code was completely refactored from the ground to improve performance and readability.
* **BUGFIX:** \[WP\] NextGenGallery conflict resolved (Issue [#31](https://github.com/diegolamonica/EUCookieLaw/issues/31))
* **BUGFIX:** \[WP\] QuickAdsense conflict resolved (Issue [#36](https://github.com/diegolamonica/EUCookieLaw/issues/36) and  [#32](https://github.com/diegolamonica/EUCookieLaw/issues/32) )
* **BUGFIX:** \[WP\] Revolution Slider conflict resolved (Issue [#37](https://github.com/diegolamonica/EUCookieLaw/issues/37))
* **BUGFIX:** Page URL changes after reload (Issue [#38](https://github.com/diegolamonica/EUCookieLaw/issues/38))
* **BUGFIX:** Scroll on tablet does not work  (Issue [#40](https://github.com/diegolamonica/EUCookieLaw/issues/40))
* **BUGFIX:** Invalid Calling Object in Internet Explorer 9 and Safari was resolved  (Issue [#41](https://github.com/diegolamonica/EUCookieLaw/issues/41))
* updated translation files
* updated documentation
* updated the version number

## 1.5
* **NEW:** Now the plugin is able to detect if the user agent and does not block contents if it is search engine
* **NEW:** All the external contents are loaded after the user consent without page reloading ( Issues [#4](https://github.com/diegolamonica/EUCookieLaw/issues/4) and [#10](https://github.com/diegolamonica/EUCookieLaw/issues/10))
* **NEW:** The script allows to define the consent duration in days (Issue [#7](https://github.com/diegolamonica/EUCookieLaw/issues/7), [#17](https://github.com/diegolamonica/EUCookieLaw/issues/17) and [#23](https://github.com/diegolamonica/EUCookieLaw/issues/23))
* **NEW:** Now is possible to check almost in every HTML element ( Implicitly resolved issue [#6](https://github.com/diegolamonica/EUCookieLaw/issues/6))
* **NEW:** The script remembers the user rejection.
* **NEW:** New JavaScript public method `reconsider` to revoke the consent (and the rejection) showing the banner again (Issue [#7](https://github.com/diegolamonica/EUCookieLaw/issues/7))
* **NEW:** \[WP\] Added shortcode for reconsider button (see documentation for further details) (Issue [#7](https://github.com/diegolamonica/EUCookieLaw/issues/7))
* **NEW:** \[WP\] Added shortcode for wrapping contents (see documentation for further details) 
* **NEW:** Now the consent on scroll is fired at least after 100px scroll (up or down)
* **IMPROVEMENT:** \[WP\] Made compliant with **WP Super Cache**, **W3 Total Cache**, **Zen Cache** (Issue [#23](https://github.com/diegolamonica/EUCookieLaw/issues/23))
* **IMPROVEMENT:** Javascript has been refactored to improve performance and maintenability
* **IMPROVEMENT:** \[WP\] Admin interface improved
* **IMPROVEMENT:** Some CSS improvements (Issue (Issue [#8](https://github.com/diegolamonica/EUCookieLaw/issues/8))
* **BUGFIX:** Consent on scroll doesn't work propery
* **BUGFIX:** \[WP\] Custom content path not recognized correctly ( Issue [#9](https://github.com/diegolamonica/EUCookieLaw/issues/9))
* **BUGFIX:** Typos where `script` was written as `srcript` on server script (Issue [#16](https://github.com/diegolamonica/EUCookieLaw/issues/16))
* **BUGFIX:** Only first occourrence of the same/similar URL is blocked (Issue [#19](https://github.com/diegolamonica/EUCookieLaw/issues/19))
* **BUGFIX:** Corrected some IE8 weird behavior
* updated translation files
* updated documentation
* updated the version number

## 1.4.1
* **BUGFIX:** fixed the javascript that has wrong characters in the script

## 1.4
* **NEW:** when you specify a domain starting with a dot (eg. `.google.com`) all the subdomains are valid (eg. `www.google.com` and `sub.domain.google.com`)
* **NEW:** Improved the banner loading (loaded before the DOM Event `load`)
* **NEW:** Optional implicit user agree on page scrolling ([Issue #4](https://github.com/diegolamonica/EUCookieLaw/issues/4)).
* **NEW:** Debugging options
* **NEW:** You can fix the banner on top or bottom of the page.
* **NEW:** The custom CSS (from `EUCookieLawCustom`) will be loaded in conjunction with the default CSS.
* **BUGFIX:** removed the `<![CDATA[ ... ]]>` envelop on script replacement due to some browser incompatibility.
* **BUGFIX:** Custom translations was never read
* updated translation files
* updated documentation
* updated the version number

## 1.3.1
* **BUGFIX:** the default text for disagree button when not given was `Disagree` instead it should be empty.
* **BUGFIX:** whatever is the name of the plugin directory the directory for the customizations (translations and CSS) must be `/wp-content/plugins/EUCookieLawCustom/`.
* updated documentation
* updated the version number

## 1.3
* Updated the eucookielaw-header.php,
  * **NEW:** now the disallowed domains trims the spaces on each domain. It means that is allowed to write `domain1.com ; domain2.com` and they will be correctly interpreted as `domain1.com` and `domain2.com`
* **NEW:** If not defined the disagee label text then the button is not shown. Useful for informative non-restrictive cookie policy.
* **BUGFIX:** the cookie `__eucookielaw` setted by javascript is defined at root domain level.
* updated documentation
* updated the version number

## 1.2
* Updated the eucookielaw-header.php,
  * **NEW:** now the search of url is performed in `<script>...</script>` tags too.
  * **BUGFIX:** some translations strings were broken.
* updated translation files
* updated documentation
* updated the version number

## 1.1
This update introduces several improvements, features and bugfixes. For a detailed information about the new release see:
[Issue #1](https://github.com/diegolamonica/EUCookieLaw/issues/1)

* updated the eucookielaw-header.php,
  * **NEW:** now it blocks script tags with `data-eucookielaw="block"` attribute
  * **NEW:** now is possible to define a blacklist of domains to block before the user consent the agreement
  * **NEW:** the blacklist is related to a set of tags (by default the plugin will scan `iframe`, `link` and `script` tags
* **NEW::** managed title tag, blocked domains and tags to scan
* **NEW:** if the plugin WP Super Cache is installed then the plugin will clear the cache until the user has not approved the agreeement to ensure to show always the right contents
* **NEW::** if there is a CSS file named `eucookielaw.css` in the custom directory `wp-content/plugins/EUCookieLawCustom/` the it will be appliead in place of the default one.
* **BUGFIX:** unescaped post data before saving the admin settings
* updated the version number
* updated translation strings
* updated documentation

## 1.0
* First release

# Donations

If you find this script useful, and since I've noticed that nobody did this script before of me,
I'd like to receive [a donation](https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=me%40diegolamonica%2einfo&lc=IT&item_name=EU%20Cookie%20Law&no_note=0&currency_code=EUR&bn=PP%2dDonationsBF%3abtn_donateCC_LG%2egif%3aNonHostedGuest).   :)
