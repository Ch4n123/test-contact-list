<template>
  <div>
    <h1 class="mb-4">Contact List</h1>
    <div class="d-flex justify-content-middle">
      <input
        type="text"
        v-model="searchQuery"
        class="form-control mb-4 search-bar"
        placeholder="Search Contacts"
      />
    </div>
    <div class="row">
      <div v-for="contact in filteredContacts" :key="contact.id" class="col-md-5 mb-4">
        <div
          class="card"
          @mouseenter="hoveredCard = contact.id"
          @mouseleave="hoveredCard = null"
          :class="{ 'zoomed': hoveredCard === contact.id }"
        >
          <div class="card-body">
            <div class="name-button-container">
              <h5 class="card-title mb-4">
                <font-awesome-icon
                  icon="fa-solid fa-user"
                  class="icons users"
                  style="margin-right: 10px"
                />
                {{ contact.name }}
              </h5>
              <button
                v-show="hoveredCard === contact.id"
                @click="openEmailModal(contact)"
                class="btn btn-primary"
              >
                Email Me
              </button>
            </div>
            <h6 class="card-subtitle mb-2">
              <font-awesome-icon icon="fa-solid fa-envelope" class="icons" />
              {{ contact.email }}
            </h6>
            <p class="card-text mb-2">
              <font-awesome-icon icon="fa-solid fa-phone" class="icons" />
              {{ contact.phone }}
            </p>
            <p class="card-text mb-2">
              <font-awesome-icon icon="fa-solid fa-house" class="icons" />
              {{ contact.address.street }}, {{ contact.address.suite }},
              {{ contact.address.city }}, {{ contact.address.zipcode }}
            </p>
            <p class="card-text mb-2 ml-5">
              <font-awesome-icon icon="fa-solid fa-building" class="icons" />
              {{ contact.company.name }}
            </p>
            <p class="card-text mb-2">
              <font-awesome-icon icon="fa-solid fa-globe" class="icons" />
              <a :href="contact.website" target="_blank">{{ contact.website }}</a>
            </p>
          </div>
        </div>
      </div>
      <div v-if="filteredContacts.length === 0" class="mt-4 text-danger no-contact">
        Sorry. There is no Contact named {{ searchQuery }}.
      </div>
    </div>
    <!--email modal -->
    <div v-if="isEmailModalOpen" class="custom-modal">
      <div class="modal-content">
        <span @click="closeEmailModal" class="close">&times;</span>
        <h2>Email Form</h2>
        <form>
          <div class="form-group">
            <label for="exampleInputEmail1" class="mb-3">Email address</label>
            <input
              type="email"
              class="form-control"
              id="exampleInputEmail1"
              aria-describedby="emailHelp"
              placeholder="Enter email"
            />
            <small id="emailHelp" class="form-text text-muted mb-3">
              We'll never share your email with anyone else.
            </small>
          </div>
          <div class="form-group mb-3">
            <label for="exampleFormControlTextarea1" class="">Message</label>
            <textarea class="form-control mb-3" id="exampleFormControlTextarea1" rows="3"></textarea>
          </div>
          <div class="mt-3 d-flex justify-content-between">
            <button @click="closeEmailModal" class="btn btn-primary">Submit</button>
            <button @click="closeEmailModal" class="btn btn-outline-secondary">Close</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      contacts: [],
      searchQuery: '',
      hoveredCard: null,
      isEmailModalOpen: false,
    };
  },
  computed: {
    filteredContacts() {
      return this.contacts.filter(contact =>
        contact.name.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },
  },
  mounted() {
    this.fetchContacts();
  },
  methods: {
    async fetchContacts() {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/users');
        if (response.ok) {
          this.contacts = await response.json();
        } else {
          console.error('Failed to fetch contacts');
        }
      } catch (error) {
        console.error('Error: ', error);
      }
    },
    openEmailModal(contact) {
      this.isEmailModalOpen = true;
    },
    closeEmailModal() {
      this.isEmailModalOpen = false;
    },
  },
};
</script>

<style scoped lang="scss">

.search-bar {
  border-radius: 30px;
  width: 100%;
  max-width: 730px;
  height: 50px;
  padding-left: 30px;
}
.name-button-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.zoomed {
  transform: scale(1.1);
  overflow: visible;
}
.icons {
  margin-right: 30px;
  font-size: 20px;
}
.users {
  font-size: 40px;
}
.card-title {
  font-size: 30px;
  text-decoration: underline;
}
.no-contact {
  font-size: 30px;
}
.custom-modal {
  display: block;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.4);
  z-index: 9999;
}
.modal-content {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #fff;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
  max-width: 800px;
}
.close {
  position: absolute;
  top: 10px;
  right: 10px;
  cursor: pointer;
}
</style>