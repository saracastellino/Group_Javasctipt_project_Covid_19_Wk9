<template>

  <div class="form-container">
    <form v-on:submit.prevent="HandleSubmitNeed" class="form">

       <h1 class="h1">Post a request</h1>
        <p>Please fill in this form to create a request.</p>
        
          <label for="name">Name:
            <input type="text" placeholder="John Doe" id="name" name="name" v-model="name" required/>
          </label>

          <label for="content">Content:
            <input type="text" placeholder="Milk, Oats & Painkillers... " id="content" name="content" v-model="content"/>
          </label>

          <label for="needDescription">Description:
            <input type="text" placeholder="specific requirements..." id="needDescription" name="needDescription" v-model="needDescription" />
          </label>

          <label for="category">Category:
            <select name="category" v-model="category" id="category" class = "category">
              <option value="" disabled selected>Please choose your request category</option>
              <option v-for="category in this.$GCategorys" :value="category" v-bind:key="category">
                {{ category }}
              </option>
            </select>
          </label>

          <label for="contactnumber">Contact Number:
            <input type="number" placeholder="07711667566" id="contactnumber" name="contactnumber" v-model="contactDetails.contactNumber" required/>
          </label>

          <label for="email">Email:
            <input type="text" placeholder="johndoe@hotmail.com" id="email" name="email" v-model="contactDetails.email" required/>
          </label>

          <label for="address">Address:
            <input type="text" placeholder="10 Featherhall Avenue, Corstorphine" id="address" name="address" v-model="contactDetails.address" required/>
          </label>

          <label for="postcode">Postcode:
            <input type="text" placeholder="EH12 7TQ" id="postcode" name="postcode" v-model="contactDetails.postCode" required/>
          </label>

           <input type="submit" name="submit" value="Save" />
    </form>
  </div>
</template>

<script>
import { eventBus } from "@/main.js";
import NeedService from '@/services/NeedService.js'; 

export default {
  name: "need-form",
  components: {
  },
  props: {
    
  },
  data() {
    return {
      locations: null,
      name: "",
      content: "",
      needDescription: "",
      needStatus: true,
      category: "",
      contactDetails:{
        contactNumber: "",
        email: "",
        address: "",
        postCode: "",
        time: "",
        date: "",
        lat: "",
        lon: ""
      }
    };
  },
    methods: {
      HandleSubmitNeed() {
      event.preventDefault();

      const date = new Date();
      const year = date.getFullYear();
      const month = date.getMonth();
      const day = date.getDate();
      const hour = date.getHours();
      const minute = date.getMinutes();
      const second = date.getSeconds();
      const formateDate = day+":"+month+":"+year;
      const formateTime = hour+":"+minute+":"+second;
      const url = 'https://nominatim.openstreetmap.org/search?format=json&q='+this.contactDetails.address;

      fetch(url)
      .then( (res) => {
        console.log("response converted to json")
        return res.json()
      })
      .then( location => {
        this.contactDetails.lat = location[0].lat;
        this.contactDetails.lon = location[0].lon;
      })
      .then(() => {
        console.log("creating payload")
          return {
          name: this.name,
          content: this.content,
          needDescription: this.needDescription,
          needStatus: this.needStatus,
          category: this.category,
          contactDetails:{
            contactNumber: this.contactDetails.contactNumber,
            email: this.contactDetails.email,
            address: this.contactDetails.address,
            postCode: this.contactDetails.postCode,
            time: formateTime,
            date: formateDate,
            lat: this.contactDetails.lat,
            lon: this.contactDetails.lon
            }
          }
        })
        .then((payload) => {
          NeedService.addNeed(payload)})
        .then(() => {this.$router.push({ name: 'home' })})
      }
    }
}

</script>

<style>
.form-container{
  display: flex;
  font-family: Arial, Helvetica, sans-serif;
  background-color: white;
  justify-content: center;
}

.form{
  padding: 16px;
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  width: 60%;
  align-content: center;
}

.savebtn:hover {
  opacity: 1;
}
.form select {
    width: 100%;
    display: inline-block;
    border: none;
    background: #f1f1f1;
  padding: 16px 20px;
  margin: 8px 0;
}

.form input[type=submit] {
  background-color: #4CAF50;
  color: white;
  padding: 16px 20px;
  margin: 8px 0;
  border: none;
  cursor: pointer;
  width: 100%;
  opacity: 0.9;
}

/* Full-width input fields */
.form input[type=text], input[type=password], input[type=number] {
  width: 100%;
  padding: 15px;
  margin: 5px 0 10px 0;
  display: inline-block;
  border: none;
  background: #f1f1f1;
}

.form input[type=text]:focus, input[type=password]:focus, input[type=number]:focus {
  background-color: #ddd;
  outline: none;
}

</style>