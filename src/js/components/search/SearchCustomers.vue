<template>
    <div class="card">
        <div class="card-body">
            <!-- HEADING -->
            <h1>Search Customers</h1>
            <br/>

            <!-- SEARCH FIELD -->
            <div class="form-group">
                <label>Search:</label>
                <input type="text"
                       class="form-control search-organisation"
                       v-model="searchModel"
                >
            </div>

            <!-- LIST OUT RESULTS -->
            <hr>
            <list-customers v-bind:customer-results="localCustomerResults"
                            v-bind:root-url="rootUrl"
                            v-bind:static-url="staticUrl"
            ></list-customers>

            <!-- SHOW IF NO RESULTS -->
            <div class="alert alert-warning"
                 v-if="localCustomerResults.length == 0"
            >There are no customers with the search parameters used. Please try again.</div>
        </div>
    </div>
</template>

<script>
    const axios = require('axios');

    //Import mixins
    import searchMixin from "../../mixins/searchMixin";

    export default {
        name: "SearchCustomers",
        props: {
            customerResults: Array,
            staticUrl: {
                type: String,
                default: "/",
            },
            rootUrl: {
                type: String,
                root: "/",
            },
        },
        mixins: [
            searchMixin,
        ],
        data() {
            return {
                localCustomerResults: this.customerResults,
                searchModel: '',
                searchTimeout: '',
            }
        },
        methods: {
            getSearchResults: function() {
                //Create the data_to_send
                const data_to_send = new FormData();
                data_to_send.set('search',this.searchModel);

                //Use axios to obtain the data we require
                axios.post(
                    `${this.rootUrl}search/customer/data/`,
                    data_to_send
                ).then(response => {
                    this.localCustomerResults = response['data'];
                }).catch(error => {
                    
                });
            },
        },
        watch: {
            searchModel: function() {
                this.searchTrigger({
                   'return_function': this.getSearchResults,
                   'searchTimeout': this.searchTimeout,
                });
            },
        }
    }
</script>

<style scoped>

</style>
