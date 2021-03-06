#Angular Validation
`Version: 1.0 Beta` 

Angular Validation made easy! Angular Validation is an angular directive with locales (languages) with a simple approach of defining your validation in 1 line and displaying the errors on another 1 line...that's it! 

The concept is not new, it comes from the easy form input validation approach of Laravel Framework and also from PHP Gump Validation. They both are built in PHP but why not use the same concept over Angular as well? Well now it is available and with some extras.

##  Some Working Examples

Let's start with a simple example and then let's get down to real business.

P.S. For real live example, please download the Github project and run the `index.html` (no server required, except Chrome which doesn't want to run http outside of webserver) while the actual form with validation is inside `templates/testingForm.html` for a better separation.
<a name="examples"></a>
```html
<!-- example 1 -->
<!-- changed the default validation trigger event to (blur), default being (keyup) -->
<label for="input1">Simple Integer -- EVENT(onblur)</label>
<input type="text" name="input1" ng-model="form1.input1" 
	validation="integer|required" 
	validation-event="blur" />
<span class="validation text-danger"></span>

<!-- example 2 -->
<label for="input2">email + min(3) + max(10) + required</label>
<input type="text" name="input2" ng-model="form1.input2" validation="email|min_len:3|max_len:10|required"  />
<span class="validation text-danger"></span>

<!-- example 3 -->
<!-- input type="number", it is easier for the directive to block all non-numeric chars -->
<label for="input3">numeric + required</label>
<input type="number" name="input3" ng-model="form1.input3" validation="numeric|min_len:3|max_len:4|required"  />
<span class="validation text-danger"></span>

<!-- example 4 - with Regular Expression (Date Code of YYWW) -->
<label for="input4">Multiple Validations + Custom Regex of Date Code (YYWW)</label>
<input type="text" name="input4" ng-model="form1.input4" 
		validation="exact_len:4|regex:YYWW:=^(0[9]|1[0-9]|2[0-9]|3[0-9])(5[0-2]|[0-4][0-9])$:regex|required|integer"  />
<span class="validation text-danger"></span>

<!-- example 5 - required select option (dropdown) -->
<!-- Select Option if required, it has to be validated as an onBlur event -->
<!-- even if event is "keyup", the directive will use onBlur anyway for validation -->
<div class="form-group">
    <label for="select1">Select Option Required</label>           
    <select id="stk_type" name="stk_type" class="form-control" ng-model="form1.select1" validation="required" validation-event="blur">
        <option value="">...</option>   
        <option value="1">Option #1</option>
        <option value="2">Option #2</option>
    </select>
    <span class="validation text-danger"></span>            
</div>

<!-- EXPLANATIONS -->
<!-- <input> need the <validation=""> each validators are separated by pipe | -->
<input validation="theValidators" />

<!-- Then we need a <span> or <div> to display the error which you can apply styling -->
<!-- VERY IMPORTANT: The Error HAS to be the following element after the input -->
<span class="myStyleCSS"></span>
<!-- but in some cases we cannot, then see below Example Exceptions -->

```
Example Exceptions
--------------------
Well let's face it, having the `<span>` for error display right after the element to be validated is not always ideal and I encounter the problem myself when using Bootstrap on inputs with `input-group`, it had so much wrapping around the input that the next available element might not be the one we want. In these special occasion, we'll do something that is a little less "Angular way", we will add an `id` to our error display element and then reference it inside our input. We could actually move the error element anywhere we want with this method, just don't forget to name it with an `id` and call the `validation-error-to` attribute. We could even do validation summary with this...just saying hehe.
```html
<div class="form-group" ng-hide="trsn.isDividend">
    <label for="input1">Search Input with BS input-groups</label>
    <div class="input-group">
        <span class="input-group-addon">@</span>
        <input type="text" class="form-control" name="input1" ng-model="form1.input1" validation="min_len:2|max_len:10|alpha_dash_spaces|required" validation-error-to="myErrorId" />
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
    </div>
    <span id="myErrorId" class="validation text-danger"></span>            
</div>
```

<a name="regex"></a>
Regular Expressions (Regex)
--------------------
From the example displayed, I introduce the custom regular expression, there is no limitation on regex itself and you can even use the pipe " | " without being scared of interfering with the other validation filters BUT you have to follow a specific pattern (a writing pattern that is), and if you don't, well it will fail. Let's explain how it works... 

Regex validation is divided in 4 specific parts (Step #1-4). 

Let's use the previous [Examples](#examples) #3 and extract the information out of it to see how it works. 
Step #1-4 are for explanation only, at the end we show the full regex (make sure there is no spaces).

1. Start and End the filter with the following `regex: :regex` that tells the directive to extract it.

2. Custom error message `YYWW` (what do we want to display to user)

3. Followed by a separator which basically says, after this will come the regex `:=`

4. Custom regex pattern `^(0[9]|1[0-9]|2[0-9]|3[0-9])(5[0-2]|[0-4][0-9])$`

Final code (no spaces): `regex:YYWW:=^(0[9]|1[0-9]|2[0-9]|3[0-9])(5[0-2]|[0-4][0-9])$:regex`


Validation Event (default: keyup)
--------------------
We could also specify whichever validation event we want to trigger the validation (keyup, blur, etc...).

Features:

1. All possible events of the element are accepted `keyup`, `blur`, etc...

2. Event could be written with/without the `on` as prefix. `onblur` is equivalent to `blur`.

3. Default event `keyup` can be changed in the directive itself via constant `DEFAULT_EVENT`

Locales (languages)
--------------------
Locales are simply sets of language defined in external JSON files, we can easily add any new language as extra files without affecting the behaviour of the angular directive. You could even change displayed language on the fly, well of course the error message will be reflected only after field value is re-evaluated. You of course have to include the `angular-translate` library and configure it, see section [Include it in your Project](#project)

Note: To be fully localized, I should add the country code at the end of my JSON filename and then change the suffix on the `angular-translate` `loader` method, but then it would add an overhead and I prefer to keep it simple as validation messages often looks the same anyway. If you do want to fully localize, then see the example in [Include it in your Project](#project)

```javascript
// define a key, could be on the fly with a button or a menu link
var key = 'fr'; 

$scope.switchLanguage = function (key) {
  $translate.uses(key);
};
```	  

P.S. If you define new Language set, please make a pull request and I would be happy to add them in current project... It would be nice to have Spanish, German or even Chinese :) Thank you.

Available Validators
--------------------
##### All validators are written as `snake_case` but it's up to the user's taste and could also be written as `camelCase`. So for example `alpha_dash_spaces` and `alphaDashSpaces` are both equivalent.
* `alpha` Only alpha characters (including latin) are present (a-z, A-Z)
* `alpha_spaces` Only alpha characters (including latin) and spaces are present (a-z, A-Z)
* `alpha_num` Only alpha-numeric characters (including latin) are present (a-z, A-Z, 0-9)
* `alpha_num_spaces` Only alpha-numeric characters (with latin) and spaces are present (a-z, A-Z, 0-9)
* `alpha_dash` Only alpha-numeric characters + dashes, underscores are present (a-z, A-Z, 0-9, _-)
* `alpha_dash_spaces` Alpha-numeric chars + dashes, underscores and spaces (a-z, A-Z, 0-9, _-)
* `between_len:min,max` Ensures the length of a string is between a min,max string length.
* `credit_card` Check for valid credit card number (AMEX, VISA, Mastercard, Diner's Club, Discover, JCB)
* `date_iso` Ensure date follows the ISO format (yyyy-mm-dd)
* `date_us_long` Ensure date follows the US long format (mm-dd-yyyy) or (mm/dd/yyyy)
* `date_us_short` Ensure date follows the US short format (mm-dd-yy) or (mm/dd/yy)
* `date_euro_long` Ensure date follows the Europe long format (dd-mm-yyyy) or (dd/mm/yyyy)
* `date_euro_short` Ensure date follows the Europe short format (dd-mm-yy) or (dd/mm/yy)
* `email` Checks for a valid email address
* `exact_len:n` Ensures that field length precisely matches the specified length. n = length parameter.
* `float` Only a positive floating point value (integer are excluded)
* `float_signed` Only a floating point value (integer excluded), could be signed (-/+) positive/negative.
* `iban` Check for a valid IBAN.
* `integer` Only positive integer.
* `integer_signed` Only integer, could be signed (-/+) positive/negative.
* `max_len:n` Checks field length, no longer than specified length where (n) is length parameter.
* `min_len:n` Checks field length, no shorter than specified length where (n) is length parameter.
* `numeric` Only positive numeric value (float, integer).
* `numeric_signed` Only numeric value (float, integer) can also be signed (-/+).
* `regex` Ensure it follows a regular expression pattern... please see [Regex](#regex) section
* `required` Ensures the specified key value exists and is not empty
* `url` Check for valid URL or subdomain

<a name="project"></a>
Include it in your app project:
--------------------
```javascript
// include it your app module ( we need both Translate & Validation)
var myApp = angular.module('myApp', ['ngRoute', 'pascalprecht.translate', 'ghiscoding.validation']);

// include validation languages
// if you want full localization add it in the suffix
// For example on Canadian French/English, we could replace the code by `suffix: '-CA.json'`
myApp.config(function ($translateProvider) {
  $translateProvider.useStaticFilesLoader({
    prefix: 'locales/validation/',
    suffix: '.json'
  });

  // load English ('en') table on startup
  $translateProvider.preferredLanguage('en');
});
```

Dependencies:
------------------

1. Angular-Translate (https://github.com/PascalPrecht/angular-translate)
2. Bootstrap 3 *optional* (http://getbootstrap.com/)

License
-----
[MIT License](http://www.opensource.org/licenses/mit-license.php)  

# TODO 
#### Any kind of help is welcome from the TODO list

* Add `same` and `different` validators (same password)
* Add `ipv4` and `ipv6` validators
* Add `street_address` validator
* Add `time` (12hrs/24hrs) validators
* Add more validators...
* Add more locale languages
* Add online demo