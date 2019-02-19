<template>
  <div class="home">
    <h1>New Contact</h1>
    <div>
      First Name: <input v-model="newContactFirst_name">
    </div>
    <div>
      Middle Name: <input v-model="newContactMiddle_name">
    </div>
    <div>
      Last Name: <input v-model="newContactLast_name">
    </div>
    <div>
      Email: <input v-model="newContactEmail">
    </div>
    <div>
      Phone Number: <input v-model="newContactPhone_number">
    </div>
    <div>
      Bio: <input v-model="newContactBio">
    </div>
    <button v-on:click="createContact()">Create</button>
  <h1>All Contacts</h1>
    <div v-for="contact in contacts">
      <h2>{{ contact.first_name }} {{ contact.last_name }}</h2>
      <div>
        <button v-on:click="showContact(contact)">More Info</button>
      </div>
      <div v-if="contact === currentContact">
        <p>Contact: {{ contact.middle_name }}</p>
        <p>Email: {{ contact.email }}</p>
        <p>Phone Number: {{ contact.phone_number }}</p>
        <p>Bio: {{ contact.bio }}</p>

        <div>
          <h4>Edit Recipe</h4>
          <div>
            <div>
              First Name: <input v-model="contact.first_name">
            </div>
            <div>
              Middle Name: <input v-model="contact.middle_name">
            </div>
            <div>
              Last Name: <input v-model="contact.last_name">
            </div>
            <div>
              Email: <input v-model="contact.email">
            </div>
            <div>
              Phone Number: <input v-model="contact.phone_number">
            </div>
            <div>
              Bio: <input v-model="contact.bio">
            </div>
            <button v-on:click="updateContact(contact)">Update</button>
            <button v-on:click="destroyContact(contact)">Delete</button>
          </div>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
var axios = require('axios');

export default {
  data: function() {
    return {
      contacts: [],
      newContactFirst_name: "",
      newContactMiddle_name: "",
      newContactLast_name: "",
      newContactEmail: "",
      newContactPhone_number: "",
      newContactBio: "",
      currentContact: {}
    };
  },
  created: function() {
    axios.get("/api/contacts")
    .then(response => {
      this.contacts = response.data;
    });
  },
  methods: {
    createContact: function() {
      console.log("Create a contact...")
      var params = {
                    first_name: this.newContactFirst_name,
                    middle_name: this.newContactMiddle_name,
                    last_name: this.newContactLast_name,
                    email: this.newContactEmail,
                    phone_number: this.newContactPhone_number,
                    bio: this.newContactBio
                    };
      axios.post("/api/contacts", params)
      .then(response => {
        console.log("Success", response.data);
        this.contacts.push(response.data)
      });
    },
    showContact: function(inputContact) {
      if(this.currentContact === inputContact) {
        this.currentContact = {};
      } else {
          this.currentContact = inputContact;
      }
    },
    updateContact: function(inputContact) {
      var params = {
                    first_name: inputContact.first_name,
                    middle_name: inputContact.middle_name,
                    last_name: inputContact.last_name,
                    email: inputContact.email,
                    phone_number: inputContact.phone_number,
                    bio: inputContact.bio
                    }
      axios.patch("api/contacts/" + inputContact.id, params)
        .then(response => {
          console.log("Success", response.data);
          inputContact = response.data;
        });
    },
    destroyContact: function(inputContact) {
      axios.delete("api/contacts/" + inputContact.id)
        .then(response => {
          console.log("Success", response.data);
          var index = this.contacts.indexOf(inputContact);
          this.contacts.splice(index,1);
        });
    }
  }
};
</script>