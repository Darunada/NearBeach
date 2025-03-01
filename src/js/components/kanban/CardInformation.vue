<template>
   <div class="modal fade" id="cardInformationModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
        <div class="modal-dialog modal-lg">
            <div class="modal-content">
                <div class="modal-header">
                    <h2><IconifyIcon v-bind:icon="icons.usersIcon"></IconifyIcon> Card Information</h2>
                    <button type="button"
                            class="btn-close"
                            data-bs-dismiss="modal"
                            aria-label="Close"
                            id="cardInformationModalCloseButton"
                    >
                        <span aria-hidden="true"></span>
                    </button>
                </div>
                <div class="modal-body">
                    <div class="row">
                        <div class="col-md-4">
                            <strong>Card Text</strong>
                            <p class="text-instructions">
                                Write an appropriate name for the kanban card. To update click on the "Update" button.
                            </p>
                        </div>
                        <div class="col-md-8">
                            <label>Card Title</label>
                            <input v-model="cardTitleModel"
                                   class="form-control"
                            >
                            <br/>
                            <button class="btn btn-primary save-changes"
                                    v-on:click="updateCard"
                            >
                                Update Card
                            </button>
                        </div>
                    </div>
                    <hr>

                    <!-- CARD DESCRIPTION -->
                    <div class="row">
                        <div class="col-md-4">
                            <strong>Card Description</strong>
                            <p class="text-instructions">
                                Fill out a detailed description for the card, then click on the "Update Card" button
                                to update the card.
                            </p>
                        </div>
                        <div class="col-md-8">
                            <editor
                               :init="{
                                 height: 300,
                                 menubar: false,
                                 toolbar: 'undo redo | formatselect | ' +
                                  'bold italic backcolor | alignleft aligncenter ' +
                                  'alignright alignjustify | bullist numlist outdent indent | ',
                               }"
                               v-bind:content_css="false"
                               v-bind:skin="false"
                               v-model="cardDescriptionModel"
                            />
                        </div>
                    </div>
                    <hr>

                    <div class="row">
                        <div class="col-md-4">
                            <strong>Notes</strong>
                            <p class="text-instructions">
                                To add a note - type your note in the Note Box and hit the "Submit Note"
                                button.
                            </p>
                        </div>
                        <div class="col-md-8">
                            <label>Note Box</label>
                            <editor
                               :init="{
                                 height: 250,
                                 menubar: false,
                                 toolbar: 'undo redo | formatselect | ' +
                                  'bold italic backcolor | alignleft aligncenter ' +
                                  'alignright alignjustify | bullist numlist outdent indent | ',
                               }"
                               v-bind:content_css="false"
                               v-bind:skin="false"
                               v-model="cardNoteModel"
                            />
                        </div>
                    </div>
                    <hr>
                    <div class="row">
                        <div class="col-md-12">
                            <br/>
                            <button class="btn btn-primary save-changes"
                                    v-on:click="addNote"
                                    v-bind:disabled="cardNoteModel===''"
                            >
                                Add Note
                            </button>
                        </div>
                    </div>
                    <hr>

                    <!-- NOTE HISTORY -->
                    <list-notes v-bind:note-history-results="noteHistoryResults"
                                v-bind:destination="'card'"
                    ></list-notes>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    const axios = require('axios');

    //Mixins
    import iconMixin from "../../mixins/iconMixin";

    export default {
        name: "CardInformation",
        props: {
            cardInformation: Object,
        },
        mixins: [
            iconMixin,
        ],
        data() {
            return {
                cardId: '',
                cardDescriptionModel: '',
                cardNoteModel: '',
                cardTitleModel: '',
                noteHistoryResults: [],
            }
        },
        watch: {
            cardInformation: function() {
                
                //Update the local information
                this.cardId = this.cardInformation['cardId'];
                this.cardTitleModel = this.cardInformation['cardTitle'];

                //Bug fix - needs to blank it out or the value does nto blank out correctly
                if (this.cardInformation['cardDescription'] === null) {
                    //Can not seem to blank out the model correctly?
                    this.cardDescriptionModel = "No Description";
                } else {
                    this.cardDescriptionModel = `${this.cardInformation['cardDescription']}`;
                }


                //Now update the card notes
                this.getCardNotes();
            },
        },
        methods: {
            addNote: function() {
                //Setup data to send
                const data_to_send = new FormData();
                data_to_send.set('note',this.cardNoteModel);

                //Use axios to send the data
                axios.post(
                    `/object_data/kanban_card/${this.cardId}/add_notes/`,
                    data_to_send,
                ).then(response => {
                    //Add the response to the end of the noteHistoryResults
                    this.noteHistoryResults.push(response['data'][0]);

                    //Clear the card note model
                    this.cardNoteModel = '';
                }).catch(error => {
                    
                })
            },
            getCardNotes: function() {
                //Clear the current list of notes
                this.noteHistoryResults = [];

                //Use axios to get the card list
                axios.post(
                    `/object_data/kanban_card/${this.cardId}/note_list/`
                ).then(response => {
                    //Save the data into noteHistoryResults
                    this.noteHistoryResults = response['data'];
                }).catch(error => {
                    
                })
            },
            updateCard: function() {
                //Create the data_to_send
                const data_to_send = new FormData();
                data_to_send.set('kanban_card_text',this.cardTitleModel);
                data_to_send.set('kanban_card_description', this.cardDescriptionModel);
                data_to_send.set('kanban_card_id',this.cardId);

                //Use Axios to send data
                axios.post(
                    `/kanban_information/update_card/`,
                    data_to_send,
                ).then(response => {
                    //Send the new data upstream
                    this.$emit('update_card',{
                        'kanban_card_id': this.cardId,
                        'kanban_card_text': this.cardTitleModel,
                    });

                    //Close the modal
                    document.getElementById("cardInformationModal").click();
                }).catch(error => {
                    
                })
            },
        }
    }
</script>

<style scoped>

</style>
