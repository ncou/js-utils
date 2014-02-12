[js-utils][js-utils]
========

These are a suite of utilities for javascript and jquery, including tools for validating, text formatting, and misc.

Getting Started
---------------
The tools has a dependency on [jQuery 1.10+][jQuery.js], which must be loaded before [js-utils][jherax.js].<br>
Also require some [CSS][jherax.css] for tooltip, loading, and other methods.<br>
Some functions have a dependency on [jQuery.UI 1.9+][jQuery.ui]

The library has the following structure:
- `jsu:` main namespace
  - `author:` me :^)
  - `version:` release number
  - `dependencies:` array with name of dependencies
  - `createNS:` utility to create safe namespaces
  - `wrapper:` selector where dynamic HTML elements are placed
  - `regional:` namespace to set the language setting

Initialization
--------------
```javascript
  (function() {
    // We set the container of the views
    jsu.wrapper = "#main-section";

    // We set the language setting
    jsu.regional.set(jsu.regional.english);
    
    // Add your code for plugins init, event handlers, etc...
  })();
```
  
Namespacing
-----------
In many programming languages, namespacing is a technique employed to avoid collisions with other objects or variables in the global context. They're also extremely useful for helping organize blocks of functionality in your application into easily manageable groups that can be uniquely identified.

Global variables should be reserved for objects that have system-wide relevance and they should be named to avoid ambiguity and minimize the risk of naming collisions. In practice this means you should avoid creating global objects unless they are absolutely necessary.

Is critical as it's important to safeguard your code from breaking in the event of another script on the page using the same variable or method names as you are. To overcome some of these issues, we take advantage of the [Module Pattern](http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html) through namespace injection. The logic is shielded from the global scope by a function wrapper (usually self-invoking) which exposes an object representing the module’s public interface.

I recommend this excellent book: [Learning JavaScript Design Patterns](http://www.addyosmani.com/resources/essentialjsdesignpatterns/book/).<br>
Also worth reading these articles:
* [Namespacing in JavaScript](http://msdn.microsoft.com/en-us/magazine/gg578608.aspx)
* [Essential JavaScript Namespacing Patterns](http://addyosmani.com/blog/essential-js-namespacing/)
* [JavaScript Namespacing Common Practices](http://michaux.ca/articles/javascript-namespacing)
* [Immediately-Invoked Function Expression (IIFE)](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)
* [JavaScript Coding Standards and Best Practices](http://github.com/stevekwan/best-practices/blob/master/javascript/best-practices.md)

### jsu.createNS
This utility makes life easier when you require create nested namespaces.<br>
For example, you need to create the following object structure:
- animation
  - g2D
    - slide
  - g3D
    - cubic
  - tools

```javascript
  // using closures, modules, IIFE, are good practices
  (function() {
    jsu.createNS("animation.g2D.slide");
    jsu.createNS("animation.g3D.cubic");
    // you can get the reference of created namespace
    var tools = jsu.createNS("animation.tools");
    // If you use local/cached references is recommended declare them
    // within a function or module at the top of your function scope
    // (this is a dependancy declaration pattern)
  })();
```

### jsu.regional
This namespace exposes objects and methods to setup your language preferences.<br>
As we are using [jQuery.UI][jQuery.ui], we can provide a [language](http://github.com/jquery/jquery-ui/tree/master/ui/i18n) to configure [datepicker](http://api.jqueryui.com/datepicker/) widget.<br>
Available predefined languages are `jsu.regional.english` and `jsu.regional.spanish`<br>
By default spanish language is set, although you can specify language using method `set()`<br>
e.g. `jsu.regional.set(jsu.regional.english);`<br>
You can define your own language settings:
```javascript
(function() {
  // Create the locale language object
  // (the text should be in Italian)
  jsu.regional.italian = {
    culture: "it", //locale codes: http://www.science.co.il/Language/Locale-codes.asp
    wordPattern: null, //regular expression pattern for text capitalization in fnCapitalize
    timeFormat: "HH:mm", //pattern for time. HH: 0-23 hour, hh: 1-12 hour, mm: minutes, ss: seconds
    dateFormat: "MM/dd/yyyy", //pattern for date. dd: 2-digit day, MM: 2-digit month, yyyy: 4-digit year
    dateFormatError: "The date format is incorrect", //text for fnIsValidDate when date format is wrong
    dateIsGreater: "The date can't be greater than today", //text of date validation in fnIsValidDate
    dateIsLesser: "The date can't be lesser than today", //text of date validation in fnIsValidDate
    validateRequired: "This field is required", //text for $.fnEasyValidate required fields
    dialogTitle: "Information", //default jQuery.ui.dialog title
    dialogCancel: "Cancel", //default $.fnConfirm cancel text
    dialogOK: "Agree" //default $.fnConfirm approve text
  };
  // We set the created language setting
  jsu.regional.set(jsu.regional.italian);
})();
```
If you want to provide additional languages to other plugins, you can pass a function as second parameter in method `set();` Keep in mind that some plugins can be configured only previous to its initialization.
```javascript
(function() {
  // We will create italian language for datepicker plugin
  // Additional languages for datepicker can be found at:
  // [http://github.com/jquery/jquery-ui/tree/master/ui/i18n]
  $.datepicker.regional['it'] = {
    closeText: 'Chiudi',
    prevText: '&#x3C;Prec',
    nextText: 'Succ&#x3E;',
    currentText: 'Oggi',
    monthNames: ['Gennaio','Febbraio','Marzo','Aprile','Maggio','Giugno',
          'Luglio','Agosto','Settembre','Ottobre','Novembre','Dicembre'],
    monthNamesShort: ['Gen','Feb','Mar','Apr','Mag','Giu',
          'Lug','Ago','Set','Ott','Nov','Dic'],
    dayNames: ['Domenica','Lunedì','Martedì','Mercoledì','Giovedì','Venerdì','Sabato'],
    dayNamesShort: ['Dom','Lun','Mar','Mer','Gio','Ven','Sab'],
    dayNamesMin: ['Do','Lu','Ma','Me','Gi','Ve','Sa'],
    weekHeader: 'Sm',
    dateFormat: 'dd/mm/yy',
    firstDay: 1,
    isRTL: false,
    showMonthAfterYear: false,
    yearSuffix: ''
  };
  // We set the created language setting
  jsu.regional.set(jsu.regional.italian, function() {
    $.datepicker.setDefaults($.datepicker.regional['it']);
  });
})();
```
---

List of methods
---------------
* [browser](#browser)
* [inputType](#inputtype)
* [isDOM](#isdom-object)
* [isFunction](#isfunction-object)
* [fnStringify](#fnstringify-json)
* [fnGetDate](#fngetdate-)
* [fnGetHtmlText](#fngethtmltext-index-value)
* [fnGetSelectedText](#fngetselectedtext-)
* [fnGetCaretPosition](#fngetcaretposition-dom)
* [fnSetCaretPosition](#fnsetcaretposition-dom-position)
* [fnEscapeRegExp](#fnescaperegexp-text)
* [fnCapitalize](#fncapitalize-object-type)
* [fnNumericFormat](#fnnumericformat-object)
* [fnIsValidFormat](#fnisvalidformat-object-type)
* [fnIsValidDate](#fnisvaliddate-dom-options)
* [fnShowTooltip](#fnshowtooltip-dom-message)
* [fnShowDialog](#fnshowdialog-options)
* [fnSetFocus](#fnsetfocus-)
* [fnLoading](#fnloading-options)

jQuery extensions
-----------------
* [$.fnCenter](#jqueryfncenter-)
* [$.fnMaxLength](#jqueryfnmaxlength-length)
* [$.fnCapitalize](#jqueryfncapitalize-type)
* [$.fnShowTooltip](#jqueryfnshowtooltip-message)
* [$.fnIsValidFormat](#jqueryfnisvalidformat-type)
* [$.fnIsValidDate](#jqueryfnisvaliddate-options)
* [$.fnNumericFormat](#jqueryfnnumericformat-)
* [$.fnNumericInput](#jqueryfnnumericinput-)
* [$.fnCustomInput](#jqueryfncustominput-mask)
* [$.fnDisableKey](#jqueryfndisablekey-keys)
* [$.fnEasyValidate](#jqueryfneasyvalidate-options)
* [$.fnConfirm](#jqueryfnconfirm-options)

Usage
-----

### browser
Adds support for browser detect, because jquery 1.9+ deprecates the *[browser](http://api.jquery.com/category/deprecated/#post-301)* property.<br>
For detecting capabilities, is better to use [Modernizr](http://modernizr.com/docs/).<br>
**Returns** `Object`
```javascript
  // see browser and version
  console.log(jsu.browser);
  // check for a specific browser
  if (jsu.browser.msie) { ... }
  if (jsu.browser.chrome) { ... }
  if (jsu.browser.mozilla) { ... }
  if (jsu.browser.opera) { ... }
```

### inputType
This object has two methods to determine the type of the `<input>` element.
* **`isText (dom)`**: This function returns `true` when the ***dom*** parameter is a writable  `<input>`<br>Elements classified in this category are: [Category:text](#categorytext)
* **`isCheck (dom)`**: This function returns `true` when the ***dom*** parameter is a checkable `<input>`<br>Elements classified in this category are: `"checkbox"` and `"radio"` input elements.

```html
  <input type="radio" id="radio" />
  <input type="date" id="date" />
  <textarea id="area"></textarea>
```
```javascript
  (function() {
    //we use jquery.get() to get first DOM element
    var area = $("#area").get(0);
    var date = $("#date").get(0);
    var radio = $("#radio").get(0);
    if (jsu.inputType.isText(area)) area.value = "I am category:text";
    if (jsu.inputType.isText(date)) date.value = new Date().toISOString().substring(0, 10);
    if (jsu.inputType.isCheck(radio)) radio.checked = true;
  })();
```

### isDOM *(object)*
Determines if the entry parameter is a [DOM Element](http://api.jquery.com/Types/#Element).<br>
**Returns** `Boolean`
* **object:** `Object` to validate

```javascript
  var obj = { name: "Mordecai" };
  if (jsu.isDOM(obj)) { ... } //false
  obj = document.getElementById("txtName");
  if (jsu.isDOM(obj)) { ... } //true
  obj = $(":button");
  if (jsu.isDOM(obj)) { ... } //false
  obj = $(":button").get(0);
  if (jsu.isDOM(obj)) { ... } //true
```

### isFunction *(object)*
Determines if the entry parameter is a function.<br>
**Returns** `Boolean`
* **object:** `Object` to validate

```javascript
  var fn = {};
  if (jsu.isFunction(fn)) { fn(); } //false
  fn = null;
  if (jsu.isFunction(fn)) { fn(); } //false
  fn = "function";
  if (jsu.isFunction(fn)) { fn(); } //false
  fn = function() { alert("is function"); };
  if (jsu.isFunction(fn)) { fn(); } //true
```

### fnStringify *(json)*
This is a reference to [`JSON.stringify`](http://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) and provides support in old browsers.<br>
fnStringify serializes a *JSON* object and returns its string representation.<br>
**Returns** `String`
* **json:** `JSON` object to be serialized

```javascript
  var jsonPerson = {
      name: "David",
      sex: "male",
      age: 30
  };
  console.log(jsu.fnStringify(jsonPerson));
  // '{"name":"David","age":30,"sex":"male"}'

  // We use jQuery.extend to merge the contents of
  // two or more objects together into the first object.
  var jsonNew = $.extend({ alias: 'jherax' }, jsonPerson);
  console.log(jsu.fnStringify(jsonNew));
```

### fnGetDate ()
Gets the text of current date according to [regional setting](#jsuregional).<br>
**Returns** `Object` with the following properties:
```javascript
{
  date: String //gets the date according to [dateFormat]
  time: String //gets the time according to [timeFormat]
  dateTime: String //gets the date with [dateFormat] + [timeFormat]
}
```
```javascript
  (function() {
    // We set the language setting
    jsu.regional.set(jsu.regional.english);
    var d = jsu.fnGetDate();
    $("#date").html(d.date +" <b>"+ d.time +"</b>");
  })();
```

### fnGetHtmlText *(index, value)*
Gets the text as encoded html, so if you have the text `<p>hello</p>`,<br>
fnGetHtmlText will encoded it to `&lt;p&gt;hello&lt;/p&gt;`<br>
This function is a delegate for [jQuery.val()](http://api.jquery.com/val/#val2) or [jQuery.text()](http://api.jquery.com/text/#text2)<br>
**Returns** `String` with encoded html
```html
  <textarea rows="4"></textarea>
  <textarea rows="6" id="target"></textarea>
  <div id="demo-wrapper">
    <h3>fnGetHtmlText</h3>
    <p><u>Delegated function</u></p>
  </div>
```
```javascript
  var html = $("#demo-wrapper").html();
  $("textarea").val($.trim(html));
  $("#target").val(jsu.fnGetHtmlText);
  // invoke the function programmatically
  console.log(jsu.fnGetHtmlText(html));
```

### fnGetSelectedText ()
Gets the selected text in the document.<br>
**Returns** `Object` with the following properties:
```javascript
{
  text: String //selected text (whitespace removed from the beginning and end of text)
  slice: String //complement of selected text (if active element is category:text)
  start: Number //initial cursor position (if active element is category:text)
  end: Number //final cursor position (if active element is category:text)
}
```
<div align="left">Take a look at <a href="#categorytext">category:text</a>&nbsp;</div>
```javascript
  var sel = jsu.fnGetSelectedText();
  if (sel.text !== "") alert(sel.text);
  console.log(sel);
```

### fnGetCaretPosition *(dom)*
Gets the cursor position of ***dom*** element.<br>
**Returns** `Number`
* **dom:** `DOM` element [category:text][category.text]

```javascript
  var text = $("#txtName").val("Hello!").get(0);
  var pos = jsu.fnGetCaretPosition(text);
  console.log(pos);
```

### fnSetCaretPosition *(dom, position)*
Sets the cursor ***position*** in the ***dom*** element.<br>
* **dom:** `DOM` element [category:text][category.text]
* **position:** `Number` that indicates where the cursor is set

```javascript
  var text = $("#txtName").get(0);
  text.value = "Hello World!";
  text.focus();
  jsu.fnSetCaretPosition(text, 5);
  //cursor is positioned after "Hello"
```

### fnEscapeRegExp *(text)*
Escapes user input to be treated as a literal string in a regular expression.<br>
This mean that special characters will be treated as literals.<br>
e.g. The expression `"(\\w+)"` will turn into `"\(\\w\+\)"`<br>
**Returns** `String`, or `null` if ***text*** parameter is not a string
* **text:** `String` to literalize

```javascript
  var re1 = new RegExp("[abc]+\\d"); //treats the string as a regular expression pattern
  var re2 = new RegExp(jsu.fnEscapeRegExp("[abc]+\\d")); //treats the string as a literal
  console.log("re1: " + re1.test("ac1") + ", regexp: " + re1.source); //regexp: /[abc]+\d/
  console.log("re2: " + re2.test("ac1") + ", regexp: " + re2.source); //regexp: /\[abc\]\+\\d/
```

### fnCapitalize *(object, type)*
Applies a transformation to the text, removing all line-breaks, spaces, and tabs from the beginning and end of the supplied string. If the whitespace characters occur in middle of the string, also they are removed.<br>
**Note:** The object defined in [`jsu.regional.<language>.wordPattern`](#jsuregional) is a regular expression used to lowercasing some words after text capitalization. Only works when `type = "word"`<br>
**Returns** `String`
* **object:** `String` or `DOM` element [category:text][category.text]
* **type:** `String` specifying the text transformation. Can be one of the following values:
  * `"word"` transform to lowercase and then turns the first letter of each word into uppercase
  * `"title"` turns the first letter of each word into uppercase
  * `"first"` only the first letter is uppercase
  * `"lower"` transform to lowercase
  * `"upper"` transform to uppercase

```javascript
  var text = "\n  make  your\t  code DRY  \n ";
  console.log('original text: "' + text + '"');
  console.log('word : "' + jsu.fnCapitalize(text, "word" ) + '"');
  console.log('title: "' + jsu.fnCapitalize(text, "title") + '"');
  console.log('lower: "' + jsu.fnCapitalize(text, "lower") + '"');
  console.log('upper: "' + jsu.fnCapitalize(text, "upper") + '"');
```
If you want to lowercase specific words, you can do it this way:
```javascript
  (function() {
    // We configure the global language setting
    // The following words will always be lowercase
    jsu.regional.english.wordPattern = /\s(?:And|Are|At|A|O[nrf]|By|In|The)\b/g;
    jsu.regional.set(jsu.regional.english);
    var text = " pc AND KEYBOARD\t ARE on the table ";
    console.log("Before: ", '"' + text + '"');
    // Transforms the text after 2 seconds
    setTimeout(function() {
      text = jsu.fnCapitalize(text, "word");
      console.log("After: ", '"' + text + '"');
    }, 2000);
  })();
```

### fnNumericFormat *(object)*
Sets the numeric format according to **es-CO** culture.<br>
Places the decimal`.` and thousand`,` separator.<br>
**Returns** `String` with the formatted number
* **object:** `String` or `DOM` element [category:text][category.text]

```javascript
  var num = "123456789,47.15";
  console.log(jsu.fnNumericFormat(num)); //sends string
  var dom = $("#txtName").val(num).get(0);
  jsu.fnNumericFormat(dom) //sends DOM
  console.log(dom.value);
```

### fnIsValidFormat *(object, type)*
Validates the text format, depending on the ***type*** supplied.<br>
**Note:** Date validations are run according to [regional setting](#jsuregional).<br>
**Returns** `Boolean`
* **object:** `String` or `DOM` element [category:text][category.text]
* **type:** `String` specifying the type of validation:
  * `"t"` validates the time format (Default HH:mm)
  * `"d"` validates the date format (Default dd/MM/yyyy)
  * `"dt"` validates full date format (Default dd/MM/yyyy HH:mm)
  * `"email"` validates an email address
  * `"pass"` validates the password strength (must have 8-20 characters, 1+ uppercase, 1+ number)
  * `"lat"` validates the latitude
  * `"lon"` validates the longitude

```javascript
  (function() {
    //We configure the global language setting
    jsu.regional.set(jsu.regional.english);
    var _dateTime = "10/31/2013 16:10";
    var _email = "some-mail.gmail.com";
    var _pass = "insufficient";
    console.log(jsu.fnIsValidFormat(_dateTime, "dt"));
    console.log(jsu.fnIsValidFormat(_email, "email"));
    console.log(jsu.fnIsValidFormat(_pass, "pass"));
  })();
```

### fnIsValidDate *(dom, options)*
Evaluates whether the input value is a date or not.<br>
The validation result will be shown in a tooltip.<br>
The tooltip is positioned via [jQuery.ui.position()](http://api.jqueryui.com/position/)<br>
**Note:** Date validations are run according to [regional setting](#jsuregional).<br>
**Important:** You can customize the messages defined in [`jsu.regional`](#jsuregional) namespace:<br>
`jsu.regional.<language>.dateIsGreater`<br>
`jsu.regional.<language>.dateIsLesser`<br>
`jsu.regional.<language>.dateFormatError`

**Returns** `Boolean`
* **dom:** `DOM` element [category:text][category.text]
* **options:** `Object` that provides the following settings:

```javascript
{
  compareTo: Date|String //date against which to compare the dom value (Default: new Date())
  isFuture: Boolean //determines whether entry date must be greater than [compareTo] (Default:false)
  warning: String //message indicating that entry date did not meet the requirements
}
```
```javascript
  (function() {
    //We configure the global language setting
    jsu.regional.set(jsu.regional.english);
  })();

  //Validates some form fields
  $("#btnSendForm").on("click", function() {
  	var dBirthday = $("#txtBirthday").get(0);
  	dBirthday.value = jsu.fnGetDate().date;
  	var dDriverLic = $("#txtDriverLic").val("02/28/2010").get(0);
  
  	if (!jsu.fnIsValidDate(dDriverLic, {
  	  //uncomment the following line to set a custom message
  	  //warning: "The driver's license expedition can't be greater than today"
  	})) return false;
  
  	if (!jsu.fnIsValidDate(dBirthday, {
  		compareTo: dDriverLic.value,
  		warning: "Your birthday can't be greater than driver's license expedition"
  	})) return false;
  });
```

### fnShowTooltip *(dom, message)*
Shows a tooltip ***message*** at the right side of the ***dom*** element and focuses that element.<br>
This feature is very useful when you need to display a validation message.<br>
It has a dependency on [jQuery.UI][jQuery.ui] for positioning.<br>
Also has a [css class][jherax.css] named `.vld-tooltip`<br>
**Returns** `Boolean`, always returns `false`
* **message:** `String` to display
* **dom:** `DOM` element

```javascript
  var _email = document.getElementById("txtEmail");
  if (!jsu.fnIsValidFormat(_email, "email")) {
    return jsu.fnShowTooltip(_email, "The email address is not valid");
  }
```

### fnShowDialog *(options)*
This is a facade for [`jQuery.ui.dialog`](http://api.jqueryui.com/dialog/) which is a modal window useful for displaying text, [DOM](http://api.jquery.com/Types/#Element) or [jQuery](http://api.jquery.com/Types/#jQuery) elements. 
You can display text as html by passing the string to `content` property. Generated HTML is appended by default 
to where [`jsu.wrapper`](#getting-started) selector indicate, but if you want to place it into a specific element,
then you can provide the wrapper selector to `appendTo` property.<br>
There are some [images]() used to display an image to the left side of text, only if `content` parameter is a string.<br>
Also you can display existing HTML elements by passing the *DOM* or *jQuery* object to the `content` property.<br>
The `closeOnPageUnload` option determines whether dialog should be closed on `window.beforeunload` event.<br>
**Note:** It has a dependency on [jQuery.UI][jQuery.ui] and also has some [css rules][jherax.css].<br>
**Returns** `jQuery` dialog element
* options: Object that provides the following properties: //TODO

### fnSetFocus ()
Sets the focus on all `input:text` and `textarea` elements, except those that have `.no-auto-focus` class.<br>
This function is useful when you need validate form fields using any of the below [jQuery plugins](#jquery-plugins).
```javascript
  $(document).on("ready", function() {
	  $("#txtDate").datepicker().addClass("no-auto-focus");
	  $("#txtName").fnCapitalize("word");
	  $("#txtID").fnNumericInput();
  });
  $("#btnSendForm").on("click", function() {
	  jsu.fnSetFocus();
  });
```

### fnLoading *(options)*
Shows a overlay screen with the "loading" indicator at the center.<br>
The progress animation is done via CSS3, so you must add the following [css][jherax.css]:<br>
`#floatingBarsG` `.blockG` `@keyframes fadeG` `.bg-fixed` `.bg-opacity`<br>
**Returns** `Boolean`, always returns `true`
* **options:** `Object` that provides the following settings:

```javascript
{
  show: Boolean //shows the loading screen (Default: true)
  hide: Boolean //hides the loading screen (Default: false)
  delay: Number //miliseconds of fadeIn animation (Default: 2600)
}
```
```javascript
  $("#btnTest").on("click", function() {
    jsu.fnLoading();
    setTimeout(function() {
      jsu.fnLoading({ hide:true });
    }, 8000);
  });
```

jQuery plugins
--------------
This is a set of utilities for [jQuery](http://jquery.com/).<br>
jQuery is a fast, small, and feature-rich JavaScript library. It makes things like HTML document traversal and manipulation, event handling, animation, and Ajax much simpler with an easy API that works cross-browser.<br>
If you want to learn more about jQuery, here is a full guide: [How jQuery Works](http://learn.jquery.com/about-jquery/how-jquery-works/).

### jQuery.fnCenter ()
Sets the collection of jquery objects in the center of screen.<br>
Elements are centered using fixed position.<br>
**Returns** `jQuery`
```javascript
  // with existing element
  $(".warning").fnCenter();
  // or create a new one
  var div = $('<div id="divHello" />').css({
    'padding': '20px',
    'background': '#ccc',
    'borderRadius': '5px'
  }).appendTo("body").html("<h4>Hello jQuery</h4>");
  div.fnCenter();
```

### jQuery.fnMaxLength *(length)*
Limits the maximum length of characters allowed in the elements [category:text][category.text]<br>
A tooltip will be placed on the right side of input element showing the characters remaining.<br>
It has a dependency on [jQuery.UI][jQuery.ui] for positioning, and also has a [css class][jherax.css] `.vld-tooltip`<br>
**Returns** `jQuery`
* **length:** `Number` specifying the max length of characters

```javascript
  $("#txtName").fnMaxLength(20);
  $(".numbers").fnMaxLength(10);
```

### jQuery.fnCapitalize *(type)*
This is the jQuery version of [fnCapitalize](#fncapitalize-object-type).<br>
Applies a transformation to the text, removing all line-breaks, spaces, and tabs from the beginning and end of the supplied string. If the whitespace characters occur in middle of the string, also they are removed.<br>
**Note:** The object defined in [`jsu.regional.<language>.wordPattern`](#jsuregional) is a regular expression used to lowercasing some words after text capitalization. Only works when `type = "word"`<br>
**Note:** The text is transformed when the `blur` event occurs.<br>
**Returns** `jQuery`
* **type:** `String` specifying the text transformation. Can be one of the following values:
  * `"word"` transform to lowercase and then turns the first letter of each word into uppercase
  * `"title"` turns the first letter of each word into uppercase
  * `"first"` only the first letter is uppercase
  * `"lower"` transform to lowercase
  * `"upper"` transform to uppercase

```javascript
  var text = "\n do\t it  with\t  jQuery\t\n ";
  var $input = $("#txtName").val(text);
  $input.fnCapitalize("title").focus();
  //raise blur event to transform text
  setTimeout(function() {
    alert("capitalize");
    $input.blur();
  }, 2000);
```
If you want to lowercase specific words, you can do it this way:
```javascript
  (function() {
    // We configure the global language setting
    // The following words will always be lowercase
    jsu.regional.english.wordPattern = /\s(?:And|Are|At|A|O[nrf]|By|In|The)\b/g;
    jsu.regional.set(jsu.regional.english);
    // You can bind the event handler at beginning
    var $input = $("#txtName").fnCapitalize("word");
    var text = " pc AND KEYBOARD\t ARE on the table ";
    // Raise blur event to transform text
    $input.val(text).focus();
    setTimeout(function() {
      alert("capitalize");
      $input.blur();
    }, 2000);
  })();
```

### jQuery.fnNumericFormat ()
This is the jQuery version of [fnNumericFormat](#fnnumericformat-object).<br>
Sets the numeric format according to **es-CO** culture.<br>
Places the decimal`.` and thousand`,` separator.<br>
**Note:** Text is formatted when the `keyup, blur` events occurs.<br>
**Returns** `jQuery`
```javascript
  var num = "123456789,47.15";
  console.log(num); //number to be formatted
  $("#txtName").val(num).fnNumericFormat().focus();
```

### jQuery.fnNumericInput ()
This function allows that only numeric keys can be pressed at the input.<br>
**Returns** `jQuery`
```javascript
  $(".vld-numeric").fnNumericInput();
```

### jQuery.fnCustomInput *(mask)*
This function applies a mask for the allowed characters.<br>
**Returns** `jQuery`
* **mask:** It can be one of these types:<br>`String`: a literal with the allowed characters.<br>`RegExp`: a regular expression for the allowed characters.

```javascript
  $("#txtGrade").fnCustomInput("abc1-6");
  $("#txtEmail").fnCustomInput(/[@ñ;.\-\w]/);
```

### jQuery.fnDisableKey *(keys)*
Disables the specified keyboard keys.<br>
To allow a set of characters, better use [$.fnCustomInput](#jqueryfncustominput-mask)<br>
**Returns** `jQuery`
* **keys:** `String` with character(s) that will be blocked

```javascript
  // prevents the spacebar be pressed in the document
  $(document).fnDisableKey(" ");
  // avoids pressing the keys q,w,e,r,t on the input
  $("#txtName").fnDisableKey("qwert");
```

### jQuery.fnEasyValidate *(options)*
Validates the form fields with [css class][jherax.css] `.vld-required` through a submit button.<br>
It has a dependency on [jQuery.UI][jQuery.ui] for the validation notification.<br>
If you wish to validate only a set of elements in the form, you can specify a **validation group** by adding the `data-validation` attribute to the elements to validate and also to the submit button.<br>
**Returns** `jQuery`
* **options:** `Object` that provides the following settings:

```javascript
{
  fnvalidator: Function //performs a custom validation and the function must return true or false
  firstItemInvalid: Boolean //validates first item of <select> as an invalid option (Default: true)
}
```
```html
<select id="ddlType" class="vld-required" data-validation="group.a">
  <option value="0">Select...</option>
  <option value="1">Magnetic</option>
  <option value="2">Electric</option>
</select>
<input type="number" id="txtValue" class="vld-numeric vld-required" data-validation="group.a" />
<input type="date" id="txtDate" class="vld-required" /><!--This one is not in validation group-->
<button type="submit" id="btnAdd" data-validation="group.a">Add new item</button>
```
```javascript
$(document).on("ready", function () {
  $(".vld-numeric").fnNumericInput();
  $("#txtDate").datepicker().addClass("no-auto-focus");
  //$("#btnAdd").fnEasyValidate(); //only validates required fields
  $("#btnAdd").fnEasyValidate({
    firstItemInvalid: true,
    fnValidator: function (btn) {
      var num = $('#txtValue').get(0);
      if (parseFloat(num.value) < 1000) {
        return jsu.fnShowTooltip(num, "Price must be greater than $999"); //cancel submit
      }
      return true; //submit the form
  	}
  });
});
```

### jQuery.fnConfirm *(options)*
TODO

Appendix
--------
### Category:text
We speak about *DOM element category:text* when that object belongs to one of the following html elements:
```html
  <textarea></textarea>
  <input type="text" />
  <input type="password" />
  <input type="number" />
  <input type="search" />
  <input type="tel" />
  <input type="url" />
  <input type="email" />
  <input type="datetime" />
  <input type="datetime-local" />
  <input type="date" />
  <input type="time" />
  <input type="month" />
  <input type="week" />
  <input type="file" />
```

Issues
------
If you discover a bug, please let me know here on GitHub!<br>
https://github.com/jherax/js-utils/issues

Versioning
----------
The releases will be numbered with the follow format:<br>
`<major>.<minor>.<patch>`

And constructed with the following guidelines:

* Breaking backwards compatibility bumps the major
* New additions without breaking backwards compatibility bumps the minor
* Bug fixes and misc changes bump the patch

For more information on semantic versioning, please visit http://semver.org/

Author
------
Developed by [David Rivera](http://careers.stackoverflow.com/jheraxcorp) ([stackoverflow](http://stackoverflow.com/users/2247494/jherax-corp) / [GitHub](https://github.com/jherax)) as *jherax*, from Bogota-Colombia.


License
-------
Licensed under the MIT License

<!-- links -->
[js-utils]: https://github.com/jherax/js-utils.git
[jQuery.js]: http://code.jquery.com/jquery-1.10.2.min.js
[jQuery.ui]: http://ajax.googleapis.com/ajax/libs/jqueryui/1.9.2/jquery-ui.min.js
[category.text]: #categorytext
[jherax.css]: https://github.com/jherax/js-utils/tree/master/assets/css/jherax.css
[jherax.js]: https://github.com/jherax/js-utils/tree/master/assets/js/jherax.js
