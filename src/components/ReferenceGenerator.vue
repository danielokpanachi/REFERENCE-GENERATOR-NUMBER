<template>
  <div class="container">
    <div>
      <h2 class="title">Reference Number Generator</h2>
      <!-- Flex container for sidebar and main content -->
      <div class="content">
        <!-- Left Sidebar: Always visible -->
        <div class="left-sidebar translucent-panel">
          <div class="left-sidebar">
            <h3>Search Organisation / add organizations</h3>
            <input
              v-model="sidebarSearch"
              type="text"
              placeholder="Search organization"
              class="sidebar-search-input"
            />
            <!-- Search Popup: appears when user enters text -->
            <!-- Organization List: Always visible below the search bar -->
<div class="organization-list-container">
  <ul class="organization-list">
    <li v-for="org in filteredTestOrgs" :key="org.name">
  <span>{{ org.name }}</span>
  <button class="view-btn" @click="selectOrganization(org.name)"><span>View</span></button>
</li>
  </ul>
</div>
            <button @click="addOrganization" class="add-btn"><span>Add</span></button>
          </div>
        </div>
  
        <!-- Main Content moved inside the same flex container -->
        <div class="main-content translucent-panel">
          <div class="form-container">
            <div class="form-box">
              <div class="input-group">
                <label>Name of Organisation</label>
                <input :value="firstMatchingOrg" type="text" class="org-input" readonly />
              </div>
              <div class="input-group">
                <label>Recipient Title</label>
                <input v-model="recipientTitle" type="text" placeholder="Enter recipient title" />
              </div>
              <div class="input-group">
                <label>Subject</label>
                <input v-model="subject" type="text" placeholder="Enter subject" />
              </div>
              <div class="input-group">
                <label>Recipient Name</label>
                <input v-model="recipientName" type="text" placeholder="Enter recipient name" />
              </div>
              <div class="input-group">
                <label>Sender Name</label>
                <input v-model="SenderName" type="text" placeholder="Enter Sender Name" />
              </div>
              <div class="input-group">
                <label>Sender Department</label>
                <select v-model="senderDepartment"> 
                  <option>Company Secretariat(legal)</option>
                  <option>Corporate Services</option>
                  <option>Corporate Services(IT)</option>
                  <option>Finance(Finance and Accounts)</option>
                  <option>Finance(Budget and Performance Mgt)</option>
                  <option>Finance(Treasury and Investments)</option>
                  <option>Human resources</option>
                  <option>Internal Audit</option>
                  <option>Office of the MD</option>
                  <option>Office of the Chief Economist</option>
                  <option>Operations(BDRM)</option>
                  <option>Operations(Credit Ops)</option>
                  <option>Operations(Product Dev)</option>
                  <option>Risk Mgt(ERM)</option>
                  <option>Risk Mgt(Internal Control)</option>
                  <option>Risk Mgt(Risk Assesment)</option>
                </select>
              </div>
              <div class="input-group">
                <label>Reference Date</label>
                <input v-model="date" type="date" />
              </div>
               <button @click="generateReference" class="generate-btn"><span>Generate Reference</span> </button>
            </div>
          </div>
        </div>
      </div>
  
       <!-- Popup Overlay: Add Organization Form -->
       <div v-if="showAddPopup" class="popup-overlay">
        <div class="popup-container">
          <h2>Add New Organization</h2>
          <div class="input-group">
            <label>Name of Organization</label>
            <input v-model="newOrgName" type="text" placeholder="Enter organization name" />
          </div>
          <div class="input-group">
            <label>Organization Category</label>
            <select v-model="newOrgCategory">
              <option>Regulatory Agency</option>
              <option>PFI</option>
              <option>Govt/state Government Agency</option>
              <option>Govt/Federal Government Agency</option>
              <option>Embassy/Consular office</option>
              <option>Development partner</option>
              <option>Individual</option>
              <option>Vendor/service provider</option>
            </select>
          </div>
          <div class="input-group">
            <label>Organization Code</label>
            <input v-model="newOrgCode" type="text" placeholder="Enter organization code" />
          </div>
          <button @click="addNewOrganization" class="generate-btn"><span>Add Organization</span></button>
          <button @click="cancelAddPopup" class="close-btn"><span>Close</span></button>
        </div>
      </div>
  
        <!-- Display Generated Reference if exists -->
        <div class="generated-result" v-if="referenceNumber">
  <h4>Generated Reference</h4>
  <div v-if="!isEditing">
    <p>{{ referenceNumber }}</p>
    <button @click="editReference" class="edit-btn"><span>Edit</span></button>
  </div>
  <div v-else>
    <input v-model="editedReference" class="edit-input"/>
    <button @click="updateReference" class="update-btn"><span>Update</span></button>  // span addition
    <button @click="cancelEdit" class="cancel-btn"><span>Cancel</span></button>       // span addition
  </div>
</div>



     
    
    </div>
  </div>
</template>
  
<script>
import { store } from '@/store';
  
export default {
  name: 'ReferenceGenerator',
  data() {
    return {
      selectedOrg: '', 
      sidebarSearch: '',
      orgName: '',
      recipientTitle: '',
      subject: '',
      recipientName: '',
      senderDepartment: '',
      SenderName: '',
      date: '',
      organizationCategory: '',
      orgCode: '',
      referenceNumber: '',
      isEditing: false,
      editedReference: '',
     // testOrganizations: Array.from({ length: 20 }, (_, i) => ({ name: `Test Org ${i + 1}` })), // Changed to objects
      organizations: ["Org A", "Org B", "Org C", "Org D"],
      showAddPopup: false,
      newOrgCode: '',
      newOrgCategory: '',
      dropdown1: '1',
      dropdown2: '1',
      dropdown3: '1'
    };
  },
  computed: {
    filteredTestOrgs() {
    return store.testOrganizations.filter(org =>
      org.name.toLowerCase().includes(this.sidebarSearch.toLowerCase())
    );
  },
  firstMatchingOrg() {
    return this.filteredTestOrgs.length > 0 ? this.filteredTestOrgs[0].name : '';
  }
},
  methods: {
    // Select Organization and Navigate to View Page
    selectOrganization(orgName) {
      this.sidebarSearch = '';
      this.$router.push({ name: 'ViewOrganization', params: { orgName } });
    },
    
    clearSearch() {
      this.sidebarSearch = '';
    },

    
    generateReference() {
  if (!this.recipientTitle || !this.subject || !this.recipientName || !this.senderDepartment || !this.date  || !this.SenderName) {
    alert('Please fill in all required fields.');
    return;
  }

  const formattedDate = this.date.replace(/-/g, '');
  const newReference = {
    referenceNumber: `DBN/${this.firstMatchingOrg.replace(/\s+/g, '').toUpperCase()}/${formattedDate}/${this.NewOrgCode}`,     // `DBN/${formattedDate}/${Math.floor(1000 + Math.random() * 9000)}`,
    date: this.date,
    subject: this.subject,
    recipientTitle: this.recipientTitle,
    recipientName: this.recipientName,
    senderDepartment: this.senderDepartment,
    SenderName:this.SenderName,
    NewOrgCode:this.NewOrgCode,
    firstMatchingOrg:this.firstMatchingOrg,
    
  };

  //  FIX: Use firstMatchingOrg instead of this.orgName
  const selectedOrgName = this.firstMatchingOrg; 

      // Find the organization from the store
      const org = store.testOrganizations.find(
        (o) => o.name.toLowerCase() === selectedOrgName.toLowerCase()
      );
      if (org) {
        // Push the new reference entry into the organization's references array
        org.references.push(newReference);
        // Force reactivity update by reassigning the references array
        org.references = [...org.references];
        // Optionally, update a local property to display the generated reference
        this.referenceNumber = newReference.referenceNumber;
        this.referenceIndex = org.references.length - 1;

        alert(`Reference Number Generated - ${this.referenceNumber}`);
      } 
      else {
        alert('Organization not found in Test Organizations');
      }
      if (org) {
    // Check if an exact reference already exists
    const duplicateReference = org.references.some(ref => 
      ref.date === newReference.date &&
      ref.subject.toLowerCase() === newReference.subject.toLowerCase() &&
      ref.recipientTitle.toLowerCase() === newReference.recipientTitle.toLowerCase() &&
      ref.recipientName.toLowerCase() === newReference.recipientName.toLowerCase() &&
      ref.senderDepartment.toLowerCase() === newReference.senderDepartment.toLowerCase()
    );

    if (duplicateReference) {
      alert('Reference code already generated');
      return;
    }
  }


    },
    goToTestOrganizations() {
      this.$router.push({ name: 'TestOrganizations' });
    },
    addOrganization() {
      this.showAddPopup = true;
    },
    confirmAddPopup() {
      const newOrg = `${this.newOrgCode} (Drop1:${this.dropdown1}, Drop2:${this.dropdown2}, Drop3:${this.dropdown3})`;
      this.testOrganizations.push(newOrg);
      this.showAddPopup = false;
      // Reset fields
      this.newOrgCode = '';
      this.dropdown1 = '1';
      this.dropdown2 = '1';
      this.dropdown3 = '1';
    },
    
    deleteOrganization() {
      this.testOrganizations.pop();
    },
    viewOrganization(org) {
      // Navigate to the view page using Vue Router
      this.$router.push({ name: 'ViewOrganization', params: { orgName: org } });
    },
    


    // ... existing methods such as generateReference, etc.
    editReference() {
      // Set the editedReference value to the current referenceNumber and enable edit mode
      this.editedReference = this.referenceNumber;
      this.isEditing = true;
    },
    updateReference() {
      // Update local referenceNumber
      this.referenceNumber = this.editedReference;
      // Update the corresponding reference in the store
      const org = store.testOrganizations.find(
        (o) => o.name.toLowerCase() === this.firstMatchingOrg.toLowerCase()
      );
      if (org && this.referenceIndex !== null) {
        org.references[this.referenceIndex].referenceNumber = this.editedReference;
        // Force reactivity update
        org.references = [...org.references];
      }
      // Update the referenceNumber with the edited value and disable edit mode
      this.referenceNumber = this.editedReference;
      this.isEditing = false;
    },
    cancelEdit() {
      // Cancel editing and revert to display mode without changes
      this.isEditing = false;
    
  },
// Add New Organization Function
addNewOrganization() {
      if (!this.newOrgName || !this.newOrgCategory || !this.newOrgCode) {
        alert('Please fill all fields before adding the organization.');
        return;
      }

// Check if organization already exists
const orgExists = store.testOrganizations.some(org => 
    org.name.toLowerCase() === this.newOrgName.toLowerCase()
  );

  if (orgExists) {
    alert('Organization already added');
    return;
  }

      // Create new organization object
      const newOrg = {
        name: this.newOrgName,
        category: this.newOrgCategory,
        code: this.newOrgCode,
        references: []  // initialize references if needed
      };

       // Push new organization into the global store
  store.testOrganizations.push(newOrg);

      // Add to the organization list
      // this.testOrganizations.push(newOrg);

      // Reset input fields
      this.newOrgName = '';
      this.newOrgCategory = '';
      this.newOrgCode = '';

      // Close the popup
      this.showAddPopup = false;
    },
    // Cancel and close add popup
    cancelAddPopup() {
      this.showAddPopup = false;
      this.newOrgName = '';
      this.newOrgCategory = '';
      this.newOrgCode = '';
    }
  }






  

};
</script>
  
<style scoped>
.sidebar-search-input {
  width: 79%; /* Match the width of input fields in the main body */;
  padding: 8px; /* Match padding */
  border:  1px solid #e0e0e0;
  border-radius: 5px  #1512f0;
  font-size: 0.9em; /* Match the font size */
  text-align: center;
  margin-top: -10px;
  margin-bottom: 10px;
  transition: all 0.3s cubic-bezier(0.19, 1, 0.22, 1);

}
   .sidebar-search-input:hover {
  border: 2px solid lightgrey;
  box-shadow: 0px 0px 20px -17px;
}

.sidebar-search-input:active {
  transform: scale(0.95);
}

.sidebar-search-input:focus {
  border: 2px solid grey;
}

body, html {
  display: flex;
  justify-content: center;
  align-items: center;
}
.container {
  min-height: 100vh;
  width: 100vw;
  background-image: url('@/assets/peaks-pic.jpg');
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center;
  background-attachment: fixed;
}
.title {
  text-align: center;
  font-size: 2em;
  margin-bottom: 20px;
  padding: 10px;
  background: darkblue;
  color: white;
  padding:10px;
  border-radius: 8px;
}
.translucent-panel {
  background: rgba(255,255,255,0.6);
  backdrop-filter: blur(10px);
  border-radius:10px;
  padding: 20px;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
  transition: transform 0.3s ease;
}
.translucent-panel:hover {
  transform: translateY(-10px);
}
.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}
.popup-container {
  background: #ffffff;
  padding: 20px;
  border-radius: 10px;
  width: 90%;
  max-width: 500px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}
.content {
  display: flex;
  gap: 20px;
}
.organization-list {
  width: 100%;
  max-height: 500px;
  overflow-y: auto;
  background: rgba(255, 255, 255, 0.8);
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 10px;
  list-style: none;
  margin-top: 5px;
}
.organization-list-container {
  width: 110%;
  max-height:325px;
  overflow-y: auto;
  height: 2000px;
  background: rgba(255, 255, 255, 0.8);
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 4px;
  margin-bottom: 15px;
  margin-top:15px; /* Adds space between the search bar and the list */
}

.organization-list li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 5px;
  border-bottom: 1px solid #ddd;
}
.organization-list li:last-child {
  border-bottom: none;
}

.left-sidebar,
.main-content {
  flex: 1;
  height: auto;
  width: auto;
}
.left-sidebar {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.left-sidebar.translucent-panel > .left-sidebar {
  transform: translateY(40px) !important;
}

.form-container {
  display: flex;
  justify-content: center;
  width: 100%;
}
.form-box {
  width: 70%;
  background: rgba(255,255,255,0.8);
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.15);
  margin-right: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.input-group {
  width: 100%;
  margin: 1px auto 10px auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
}
.input-group label {
  font-weight:  bold;
  margin-bottom: 5px;
}
.input-group input ,.input-group select {
  width: 50%;
  padding: 8px;
  border: 1px solid #adabab;
  border-radius: 5px;
  font-size: 0.9em;
  text-align: center;
}

.input-group:hover {
  border:none; /*2px solid lightgrey;*/
  box-shadow: 0px 0px 10px -17px;
}

.input-group:active {
  transform: scale(0.95);
}

.input-group:focus {
  border: none;/*2px solid grey;*/
}



.generate-btn {
  width: auto;
  margin: 40px;
  padding: 10px 20px;
  background: rgb(87, 231, 82);
  color: white;
  border: none;
  cursor: pointer;
  margin-top: 15px;
  border-radius: 20px;
  text-align: center;
  transition: background 0.3s;


  position: relative;
  z-index: 10;
  transition: color 0.4s;
  overflow: hidden;
  
}
.generate-btn span {
  position: relative;
  z-index: 10;
  transition: color 0.4s;
}

.generate-btn:hover span { 
  background: rgb(49, 93, 175);
}

.generated-result {
  margin-top: 15px;
  background: #ffffff;
  padding: 10px;
  border-radius: 5px;
}
 .generate-btn  {
  position: relative;
 z-index: 10;
 transition: color 0.4s;
}


.generate-btn::before,
.generate-btn::after {
 position: absolute;
 top: 0;
 left: 0;
 width: 100%;
 height: 100%;
 z-index: 0;
}

.generate-btn::before {
 content: "";
 background: #000000;
 width: 120%;
 left: -10%;
 transform: skew(30deg);
 transition: transform 0.4s cubic-bezier(0.3, 1, 0.8, 1);
}

.generate-btn:hover::before {
 transform: translate3d(100%, 0, 0);
}

button {
 outline: none;
 cursor: pointer;
 border: none;
 padding: 0.1rem 1rem;
 margin: 0;
 font-family: inherit;
 font-size: inherit;
 position: relative;
 display: inline-block;
 letter-spacing: 0.05rem;
 font-weight: 500;
 font-size: 13px;
 border-radius: 500px;
 overflow: hidden;
 background: #66ff66;
 color: ghostwhite;
}
button:hover {
  background-color: #0056b3;
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
 background: #000;
 width: 120%;
 left: -10%;
 transform: skew(30deg);
 transition: transform 0.4s cubic-bezier(0.3, 1, 0.8, 1);
}

button:hover::before {
 transform: translate3d(100%, 0, 0);
}











.add-btn {
  padding:  0.2rem 2rem;
  background-color: blue;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background 0.3s;
  float: right; /* Move the button to the right of the input */
  margin-right: 20px; /* Add some space between the input and the button */
  margin-top: 20px; /* Adds space between the search bar and the list */

}

.close-btn {
  padding: 9px 16px;
  background-color: rgb(21, 255, 0);
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background 0.3s;
  margin-left: 80px;
}
.add-btn:hover {
  background-color: rgb(247, 43, 7);
}
</style>
