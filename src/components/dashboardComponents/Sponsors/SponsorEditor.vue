<template>
<div class="sponsor-editview">
    <div class="header"  v-if="sponsor.company">
        <label>Company name:</label><br>
        <input class="company-name" v-model="sponsor.company">
        <div class="lighter-gray" style="cursor:pointer;" @click="toggleExpand">
            Toggle Details
        </div>
    </div>
    <form @submit.prevent="saveSponsor" v-if="isExpanded">
        <h3>Contacts</h3>
        <div v-for="contact in sponsor.contacts" 
                class="contact-card"
                v-bind:key="contact.email">
            <div class="contact-input">
                <label for="contactName" >Name:</label>
                <input v-model="contact.name" id="contactName" placeholder="Joe Schmoe" />
            </div>
            <div class="contact-input">
                <label for="contactEmail" >Email:</label>
                <input v-model="contact.email" id="contactEmail" placeholder="joe@schmoe.com"/>
            </div>
            <div class="contact-input">
                <label for="contactNotes" >Notes:</label>
                <input v-model="contact.notes" id="contactNotes" placeholder="Likes to be called 'Lil Joey'"/>
            </div>
        </div>
        <input type="button" @click="newContact" value="Add Contact"/><br>
        Interactions
        <div v-for="(interaction, index) in sponsor.interactions"
                :key="index">
            <input type="date" v-model="interaction.occurred" /><br>
            Notes<textarea v-model="interaction.notes" /><br>
        </div><br>
        <input type="button" @click="newInteraction" value="Add Interaction"/><br>
        <label for="sponsAmt" >Amount</label><input type="number" v-model="sponsor.sponsored.amount" id="sponsAmt"/><br>
        <label for="sponsComitted" >Date Committed</label><input type="date" v-model="sponsor.sponsored.dateCommitted" id="sponsCommitted"/><br>
        <label for="sponsPaid" >Date Paid</label><input type="date" v-model="sponsor.sponsored.datePaid" id="sponsPaid"/><br>
        <input type="button" @click="watchThisSponsor" value="Watch" />
        <input type="submit" value="Save"/>
        <contact-tracker :sponsor="sponsor"></contact-tracker>
    </form>
</div>
</template>

<script>
import { Sponsor, Contact, Interaction } from './Sponsor'
import ContactTracker from  '@/components/dashboardComponents/Sponsors/ContactTracker.vue'

export default {
    components: {
        ContactTracker
    },
    data() {
        return {
            isExpanded: false,
            contact: {
                name: '',
                email: "",
                notes: "",
            }
        }
    },
    props: {
        sponsor: {
            type: Object,
            required: true,
            default: function() {
                return Sponsor();
            }
        }
    },
    methods: {
        async saveSponsor() {
            await this.$store.dispatch('sponsors/set', this.sponsor);
        },
        newContact() {
            this.sponsor.contacts.push(Contact())
        },
        newInteraction() {
            this.sponsor.interactions.push(Interaction())
        },
        watchThisSponsor() {
            this.sponsor.watchers.push(
                this.$parent.$parent.$parent.userId
            );
        },
        toggleExpand() {
            this.isExpanded = !this.isExpanded;
        }
    }
}
</script>

<style scoped lang="scss">
@import '@/GlobalVars.scss';

.header {
    width: 100%;
}
.sponsor-editview {
    width: 80%;
    padding: 5%;
    margin: 0 auto;
}
.company-name {
    background: none;
    font-size: 25px;
    padding: 5px 10px;
    border: none;
    border-bottom: 3px solid $lighter-gray;
    color: $lighter-gray;
}

.contact-input {
    display: flex;
    justify-content: space-around;
}

.contact-card {
    background: $lighter-gray;
    box-shadow: $box-shading;
    color: $gray;
    padding: 10px;
    margin-bottom: 10px;
    input {
        background: none;
        border: none;
        border-bottom: solid 3px $gray;
    }
}
</style>

