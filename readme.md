# vue form component

A simple vue component that can wrap forms with validation and submit functionality. 

## installation 

Using npm:

```shell
$ npm i -save-dev vue-form-component
```

## usage

### set up

import and use as a component in your javascript: 

```js
import VueForm from 'vue-form-component';
import { required } from 'vue-form-component/src/validators';

let vm = new Vue({

    el: '#app',

    data: {

        forms: {

            formslug: {

                fieldname: {
                    rules: {
                        required
                    },
                    messages: {
                        [required.fieldname]: 'error message ... blah blah blah'
                    }
                },
            },
        }
    },

    components: {
        VueForm
    },

    methods: {

        onFailure(error) {
            
            // handle an unsuccessful form submission here
            
        },

        onSuccess(response) {
            
            // handle the successful form submission here
            
        }
        
    }

});
```
use custom component in your html:
```html
<vue-form inline-template
          method="post"
          action="/the/form/action"
          slug="formslug"
          v-on:success="onSuccess"
          v-on:failure="onFailure"
          ref="formref">

    <form action="/the/form/action"
          method="post"
          @submit.prevent="onSubmit"
          @keydown="dataset.errors.clear($event.target.name)">

        <div class="form-group">
            <input type="text" class="form-control required" id="fieldnameInput" placeholder="" name="fieldname"
                   v-model="dataset.fieldname"
                   v-bind:class="{ 'is-invalid': dataset.errors.has('fieldname') }">
            <span class="invalid-feedback" role="alert" v-if="dataset.errors.has('fieldname')">
                <strong v-text="dataset.errors.get('fieldname')"></strong>
            </span>
        </div>

        <div class="form-group row align-items-center">
            <div class="col-12 col-lg-5 text-center text-lg-right mt-3 mt-lg-0">
                <button type="submit" class="btn btn-orange mt-5 mt-md-0 mb-0">submit</button>
            </div>
        </div>

    </form>

</vue-form>
```

the form component uses the form-data-manager package, in order to handle form validation and submission. 
Upon initialization, it will look for a set of rules at the root vue component using it's slug property as reference.

```html
<vue-form inline-template
          ...
          slug="formslug"
          ...
          >
```
means the component will be looking for the root component this.$root.$data.forms.formslug as below 

```js
let vm = new Vue({

    data: {

        forms: {

            formslug: {

                //...
            },
        }
    },

    //...

});
``` 

### form validation
vue-form-component will handle validation of fields found in a form, by order of the rules assigned to it. 
Available rules can be imported by the component's validators directory.

```js
import { 
    required, 
    integer, 
    //...
} from 'vue-form-component/src/validators';
```   
more rules wil be added in time :). A property called "dataset" is exposed by the component containing the form's current state ie. 

* field values 
* errors 

the dataset object can be then used to dictate how different html elements will be behaving.  

```html
<div class="form-group">
    <input type="text" class="form-control required" id="fieldnameInput" placeholder="" name="fieldname"
           v-model="dataset.fieldname"
           v-bind:class="{ 'is-invalid': dataset.errors.has('fieldname') }">
    <span class="invalid-feedback" role="alert" v-if="dataset.errors.has('fieldname')">
        <strong v-text="dataset.errors.get('fieldname')"></strong>
    </span>
</div>
``` 
In the example above 
* the value of the input is set to the dataset equivalent object key
* a class named "is-invalid" will be added to the input if the dataset's errors object contains the specific field
* a span will be visible under the same condition, with it's text set to the dataset's specific error


### form submission
the form submission is handled by the component which will submit a request with all the form data to the url set by the component's action property and with the method set by the component's method property. 

```html
<vue-form inline-template
          method="post"
          action="/the/form/action"
          //...
          >
```
in the example above a post request will be sent to /the/form/action 

### response 
the component will emit a success or failure event depending on the outcome of the submission request. Both events contain the relevant server response so that further handling can be implemented. The two event handlers have to simply be set on the component.

```html
<vue-form inline-template
          // ...
          v-on:success="onSuccess"
          v-on:failure="onFailure"
          >
```

```js
let vm = new Vue({

    //...

    methods: {

        onFailure(error) {
            
            // handle an unsuccessful form submission here
            
        },

        onSuccess(response) {
            
            // handle the successful form submission here
            
        }
        
    }

});
```