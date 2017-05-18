<template>
    <div class="panel panel-default">
        <div class="panel-heading">
            <h3 class="panel-title">
                Links: <img v-show="isLoading" id="waiting" width="70" height="32" src="/images/waiting/preparing_pleasewait.gif">
            </h3>
        </div>

        <table class="table table-bordered" v-if="leads.length">
            <thead>
                <tr>
                    <th v-for="key in short_columns" v-text="key"></th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(entry, index) in leads">
                    <td v-for="(value, key) in short_columns">
                        <span v-if="!Number.isInteger(key)" v-text="entry[key]"></span>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>

    export default {
        props: {
            contact_id:{
                type: Number,
                required: true
            }
        },
        data: function () {
            return {
                leads: [],
                columns: {},
                isLoading: false,
                modal_lead_show: null,
            };
        },
        methods: {
            // получить список лидов
            fetchLeadList: function () {
                var self = this;
                this.isLoading = true;
                $.getJSON('/contacts/contact/' + this.contact_id + '/leads', function (data) {
                    self.isLoading = !true;
                    self.leads = data.leads;
                    self.columns = data.columns;
                }.bind(this));
            },
        },
        created: function (){
            this.isLoading = true;
            this.fetchLeadList();
        },
        computed: {
            short_columns: function() {
                var data = this.columns;
                if (Object.keys(data).length) {
                    var filtered = {};
                    for (variable in data) {
                        switch (variable) {
                            case 'aon':
                            case 'city':
                            case 'birth_date':
                            case 'vacancy':
                                filtered[variable] = data[variable];
                        }
                    }
                    return filtered;
                }
            }
        }
    }
</script>
