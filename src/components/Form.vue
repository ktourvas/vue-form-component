<template>
    <div>
        <slot v-bind:dataset="dataset" v-bind:onSubmit="onSubmit"></slot>
    </div>
</template>

<script>

    import { DataSet } from 'form-data-manager';

    export default {

        components: {},

        props: [ 'slug', 'method', 'action' ],

        /**
         * The form data variable is being initialized with the items and passthrough props being passed to it.
         * The items prop is a collection of the user visible form elements that will be coupled with the dom.
         * The passthrough prop is a collection of defined value attributes that will be passed to every request.
         */
        data() {
            return {

                success: false,

                dataset: null
            }
        },

        created() {
            let set = {};
            if( this.$parent.$data.hasOwnProperty('forms') && this.$parent.$data.forms.hasOwnProperty(this.slug) ) {
                set = this.$parent.$data.forms[this.slug];
            } else {
                if( this.$root.$data.hasOwnProperty('forms') && this.$root.$data.forms.hasOwnProperty(this.slug) ) {
                    set = this.$root.$data.forms[this.slug];
                }
            }
            this.dataset = new DataSet(set);
        },

        methods: {

            /**
             *
             * To be used as the submit handler of the form.
             * Uses the form data as the payload, sends a request to the prop: action.
             * As well as emitting success and failure events, also sets the success data attribute to true
             * after a successful submission and sets it back to false after 5s so that it can be used for
             * showing relevant messages and similar functionality.
             *
             * @returns void
             * @emits success
             * @emits failure
             *
             */
            onSubmit() {

                this.dataset.validate();

                if(!this.dataset.errors.any()) {

                    this.dataset[this.method](this.action)
                        .then(response => {
                            this.$emit('success', response);
                            this.success = true;

                            setInterval(() => {
                                this.success = false;
                            }, 5000);

                        })
                        .catch(error => {
                            this.$emit('failure', error);
                        });

                }

            },
        }

    }

</script>
