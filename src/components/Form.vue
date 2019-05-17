<script>

    import FormData from './Form';

    import DataSet from '../../../vue-form/src/DataSet';

    export default {

        components: {},

        props: [ 'slug', 'action', 'items', 'passthrough' ],

        /**
         * The form data variable is being initialized with the items and passthrough props being passed to it.
         * The items prop is a collection of the user visible form elements that will be coupled with the dom.
         * The passthrough prop is a collection of defined value attributes that will be passed to every request.
         */
        data() {
            return {

                success: false,

                form: new FormData(
                    this.items,
                    this.passthrough
                ),
                dataset: null
            }
        },

        created() {
            this.dataset = new DataSet(this.$root.$data.forms[this.slug]);
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

                // console.log(
                this.dataset.validate();
                if(!this.dataset.errors.any()) {

                    this.form.post(this.action)
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
                // );

            },
        }

    }

</script>
