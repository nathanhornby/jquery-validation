## jQuery Validation for Meteor

Meteor implementation of [Validation](https://github.com/jzaefferer/jquery-validation) for jQuery.

## Usage

Install via [Atmosphere](http://atmospherejs.com):

` meteor add themeteorchef:jquery-validation`

### Basic Implementation

There are a number of ways to validate your forms. The first and most simplistic is to use the helper classes provided by jQuery Validation in your markup. In the following example, the `.required` and `.email` classes are being used. On the name input, the `class="required"` attribute tells jQuery Validation that this field is required. If a user fails to type anything into this field, an error will be thrown.

On the second field (`name="emailAddress"`), we see two classes in use: `class="required email"`. Similar to the first field, this means that a value is required in this field, but *also* means that the user's input needs to be a valid email address (e.g. `email@website.com` and not `aetoiuetoi90385135`).

Add the "required" class to your markup:

```
<template name="exampleForm">

  <form id="validation-example">

    <label for="name">Name</label> <br>
    <input type="text" name="name" class="required"> <br>

    <label for="emailAddress">Email Address</label> <br>
    <input type="email" name="emailAddress" class="required email"> <br>

    <input type="submit" value="Validate Form" />

  </form>

</template>
```

Back in our Meteor code, we enable validation by using the `.validate()` method provided by jQuery Validation in our template's rendered function:

```
Template.exampleForm.rendered = ->
  $("#validation-example").validate()
```

### Advanced Implementation

If need be, you can make use of all of [jQuery Validation's methods](http://jqueryvalidation.org/validate). To use them in your Meteor app, see the example below for adding validation to the login form in [Base](http://github.com/themeteorchef/base).

First, our markup. Note: instead of adding classes, we're only using bare markup here.

```
<template name="login">
  <div class="row">
    <div class="col-xs-12 col-sm-6 col-md-4">
      <form id="application-login" class="login">
        <div class="form-group">
          <label for="emailAddress">Email Address</label>
          <input type="email" name="emailAddress" class="form-control" placeholder="Email Address">
        </div> <!-- end .form-group -->
        <div class="form-group">
          <label for="password"><span class="pull-left">Password</span> <a class="pull-right" href="{{pathFor 'recover-password'}}">Forgot Password?</a></label>
          <input type="password" name="password" class="form-control" placeholder="Password">
        </div> <!-- end .form-group -->
        <div class="form-group">
          <input type="submit" class="btn btn-success" value="Login">
        </div> <!-- end .form-group -->
      </form>
      <p>Don't have an account? <a href="{{pathFor 'signup'}}">Sign Up</a>.</p>
    </div> <!-- end .col-xs-12 -->
  </div> <!-- end .row -->
</template>
```

Next, in our controller file (where we store our Meteor code), we want to prevent a submission on the `<form>` element:

```
Template.login.events(
  'submit form': (e) ->
    # Prevent form from submitting.
    e.preventDefault()
)
```

Finally, we call the `.validate()` method in our form when our template is rendered:

```
Template.login.rendered = ->
  $('#application-login').validate(
    rules:
      emailAddress:
        required: true
        email: true
      password:
        required: true
    messages:
      emailAddress:
        required: "Please enter your email address to login."
        email: "Please enter a valid email address."
      password:
        required: "Please enter your password to login."
    submitHandler: ->
      # Grab the user's details.
      user =
          email: $('[name="emailAddress"]').val()
          password: $('[name="password"]').val()

      # Log the user in.
      Meteor.loginWithPassword(user.email, user.password, (error)->
          alert error.reason if error
      )
  )
```

In the example above, you can see the required and email methods being used.

For more custom validation options and configuration, check out the jQuery validation documentation: [jQuery Validation Documentation](http://jqueryvalidation.org/documentation)
