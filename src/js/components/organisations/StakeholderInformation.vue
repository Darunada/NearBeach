<template>
    <div class="row">
        <!-- Description -->
        <div class="col-md-4">
            <h2>Stakeholder</h2>
        </div>
        <div class="col-md-8 organisation-details">
            <img v-bind:src="getStakeholderImage" alt="Stakeholder Logo" class="organisation-image">
            <div class="organisation-name">
                <a v-bind:href="`/organisation_information/${organisationResults[0]['pk']}/`">
                    {{stakeholderModel['organisation_name']}}
                </a>
            </div>
            <div class="organisation-link">
                <IconifyIcon v-bind:icon="icons.linkOut"></IconifyIcon> Website:
                <a v-bind:href="stakeholderModel['organisation_website']" 
                   target="_blank"
                   rel="noopener noreferrer"
                >
                    {{ stakeholderModel['organisation_website'] }}
                </a>
            </div>
            <div class="organisation-email">
                <IconifyIcon v-bind:icon="icons.mailIcon"></IconifyIcon> Email:
                <a v-bind:href="`mailto:${stakeholderModel['organisation_email']}`">
                    {{stakeholderModel['organisation_email']}}
                </a>
            </div>
        </div>
    </div>
</template>

<script>
    //Mixins
    import iconMixin from "../../mixins/iconMixin";

    export default {
        name: "StakeholderInformation",
        props: {
            defaultStakeholderImage: String,
            organisationResults: Array
        },
        data() {
            return {
                stakeholderModel: this.organisationResults[0]['fields'],
            }
        },
        mixins: [
            iconMixin,
        ],
        computed: {
            getStakeholderImage: function() {
                if (this.stakeholderModel['organisation_profile_picture'] === '') {
                    //There is no image - return the default image
                    return this.defaultStakeholderImage;
                }
                return this.stakeholderModel['organisation_profile_picture']
            }
        },
        methods: {

        },
    }
</script>

<style scoped>

</style>