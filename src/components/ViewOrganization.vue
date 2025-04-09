<template>
  <div class="view-container">
    <h2>View Organization: {{ organization.name }}</h2>
    <table v-if="organization && organization.references && organization.references.length">
      <thead>
        <tr>
          <th>Date</th>
          <th>Reference Number</th>
          <th>Subject</th>
          <th>Recipient/Recipient Name</th>
         
          <th>Sender/Department</th>
          
        </tr>
      </thead>
      <tbody>
        <tr v-for="(ref, index) in organization.references" :key="index">
          <td>{{ ref.date }}</td>
          <td>{{ ref.referenceNumber }}</td>
          <td>{{ ref.subject }}</td>
          <td>{{ ref.recipientTitle }} - {{ ref.recipientName }}</td>
         
          <td>{{ ref.senderDepartment }}  -  {{ ref.SenderName }}</td>
          
        </tr>
      </tbody>
    </table>
    <p v-else>No reference entries found for this organization.</p>
    
    <button @click="goBack"><span>Back </span> </button>
  </div>
</template>

<script>
import { store } from '@/store';

export default {
  name: "ViewOrganization",
  computed: {
    // Get the organization name from the route params
    orgName() {
      return this.$route.params.orgName;
    },
    organization() {
      // Find the organization in the store by matching the name from the route params
      return store.testOrganizations.find(
        (org) => org.name.toLowerCase() === this.orgName.toLowerCase()
      );
    }
  },
  methods: {
    goBack() {
      this.$router.push({ name: 'ReferenceGenerator' });
    }
  }
};
</script>

<style scoped>
.view-container {
  padding: 20px;
  font-family: Arial, sans-serif;
  background-image: url('@/assets/peaks-pic.jpg');
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center; 
  margin: 8px;
}

button {
 outline: none;
 cursor: pointer;
 border: none;
 padding: 0.4rem 1rem;
 margin: 0;
 margin-top: 30px;
 font-family: inherit;
 font-size: inherit;
 position: relative;
 display: inline-block;
 letter-spacing: 0.05rem;
 font-weight: 700;
 font-size: 0.9em;
 border-radius: 500px;
 overflow: hidden;
 background: #32fa0a;
 color: ghostwhite;
}

button span {
 position: relative;
 z-index: 10;
 transition: color 0.4s;
}

button:hover span {
 color: black;
}

button::before,
button::after {
 position: absolute;
 top: 0;
 left: 0;
 width: 100%;
 height: 100%;
 z-index: 0;
}

button::before {
 content: "";
 background: #000000;
 width: 120%;
 left: -10%;
 transform: skew(30deg);
 transition: transform 0.4s cubic-bezier(0.3, 1, 0.8, 1);
}

button:hover::before {
 transform: translate3d(100%, 0, 0);
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}
th, td {
  padding: 10px;
  border: 1px solid #ddd;
  text-align: center;
}
th {
  background-color: #ffffff;
}

button:hover {
  background-color: #0004ff;
}
</style>