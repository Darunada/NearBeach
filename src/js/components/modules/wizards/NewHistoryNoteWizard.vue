<template>
    <!-- NEW HISTORY NOTE -->
    <div class="modal fade"
         id="newNoteModal"
         tabindex="-1"
         role="dialog"
         aria-labelledby="exampleModalLabel"
         aria-hidden="true"
    >
        <div class="modal-dialog modal-lg"
             role="document"
        >
            <div class="modal-content">
                <div class="modal-header">
                    <h2><IconifyIcon v-bind:icon="icons.noteAdd"></IconifyIcon> New Note</h2>
                    <button type="button"
                            class="btn-close"
                            data-bs-dismiss="modal"
                            aria-label="Close"
                            id="newNoteCloseButton"
                    >
                        <span aria-hidden="true"></span>
                    </button>
                </div>
                <div class="modal-body">
                    <p class="text-instructions">
                        Use the text editor to type out your note. Click on the submit button to submit the note.
                    </p>
                    <editor
                       :init="{
                         height: 300,
                         menubar: false,
                         toolbar: 'undo redo | ' +
                          'bold italic backcolor | alignleft aligncenter ' +
                          'alignright alignjustify | bullist numlist outdent indent | ',
                       }"
                       v-bind:content_css="false"
                       v-bind:skin="false"
                       v-model="newNoteModel"
                    />
                </div>
                <div class="modal-footer">
                    <button type="button"
                            class="btn btn-primary"
                            v-bind:disabled="newNoteModel == ''"
                            v-on:click="submitNote"
                    >
                        Submit Note
                    </button>
                    <button type="button"
                            class="btn btn-secondary"
                            data-bs-dismiss="modal"
                    >
                        Close
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    //JavaScript components
    import errorModalMixin from "../../../mixins/errorModalMixin";
    import iconMixin from "../../../mixins/iconMixin";

    const axios = require('axios');

    export default {
        name: "NewHistoryNoteWizard",
        props: [
            'locationId',
            'destination',
        ],
        mixins: [
            errorModalMixin,
            iconMixin,
        ],
        data() {
            return {
                newNoteModel: '',
            }
        },
        methods: {
            submitNote: function() {
                //Construct the form data to send
                const data_to_send = new FormData();
                data_to_send.set('destination',this.destination);
                data_to_send.set('location_id',this.locationId);
                data_to_send.set('note',this.newNoteModel);

                //Add the data to data_to_send
                axios.post(
                    `/object_data/${this.destination}/${this.locationId}/add_notes/`,
                    data_to_send,
                ).then((response) => {
                    //Submit the note up
                    this.$emit('update_note_history_results',response['data']);

                    //Close the modal
                    document.getElementById("newNoteCloseButton").click();
                }).catch(error => {
                    this.showErrorModal(error, this.destination);
                })
            }
        }
    }
</script>

<style scoped>

</style>
