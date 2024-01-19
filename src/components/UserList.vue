<template>
  <div>
    <div class="list-head">
      <h2>User List</h2>

      <label>
        Search:
        <input v-model="searchQuery" />
      </label>

      <label>
        Filter by:
        <select v-model="filterType">
          <option value="name">Name</option>
          <option value="email">Email</option>
        </select>
      </label>

      <label>
        Sort order:
        <select v-model="sortOrder">
          <option value="asc">Ascending</option>
          <option value="desc">Descending</option>
        </select>
      </label>
    </div>

    <ul>
      <li v-for="user in sortedAndFilteredUsers" :key="user.id">
        <img v-if="user.avatar" :src="user.avatar" alt="User Avatar" />
        <button @click="showDetails(user)">
          {{ user.first_name }} {{ user.last_name }}
        </button>
        <div class="email">{{ user.email }}</div>
        <button @click="deleteUser(user.id)">Delete</button>
      </li>
    </ul>

    <p class="notice warning" v-if="!sortedAndFilteredUsers.length">
      No users found
    </p>

    <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>

    <UserForm @userAdded="fetchUsers" />

    <UserDetails
      :user="selectedUser"
      v-if="selectedUser"
      @closeDetails="closeDetails"
    />

    <DeleteNotificationModal ref="deleteNotification" />
  </div>
</template>

<script>
import UserForm from "@/components/UserForm.vue";
import UserDetails from "@/components/UserDetails.vue";
import DeleteNotificationModal from "@/components/DeleteNotificationModal.vue";
import axios from "axios";

export default {
  components: {
    UserForm,
    UserDetails,
    DeleteNotificationModal,
  },
  data() {
    return {
      users: [],
      selectedUser: null,
      errorMessage: "",
      searchQuery: "",
      filterType: "name",
      sortOrder: "asc",
    };
  },
  computed: {
    filteredUsers() {
      return this.users.filter((user) => {
        const searchTerm = this.searchQuery.toLowerCase();
        if (this.filterType === "name") {
          return (
            user.first_name.toLowerCase().includes(searchTerm) ||
            user.last_name.toLowerCase().includes(searchTerm)
          );
        } else if (this.filterType === "email") {
          return user.email.toLowerCase().includes(searchTerm);
        }
        return true;
      });
    },

    sortedAndFilteredUsers() {
      const sortedUsers = [...this.filteredUsers];
      if (this.sortOrder === "asc") {
        sortedUsers.sort((a, b) => a.first_name.localeCompare(b.first_name));
      } else if (this.sortOrder === "desc") {
        sortedUsers.sort((a, b) => b.first_name.localeCompare(a.first_name));
      }
      return sortedUsers;
    },
  },
  mounted() {
    this.fetchUsers();
  },
  methods: {
    async fetchUsers() {
      try {
        const response = await axios.get("https://reqres.in/api/users");
        this.users = response.data.data;
      } catch (error) {
        this.handleApiError(error, "Error fetching users");
      }
    },

    async deleteUser(userId) {
      try {
        await axios.delete(`https://reqres.in/api/users/${userId}`);
        this.fetchUsers();
        this.$refs.deleteNotification.showNotification(true);
      } catch (error) {
        this.handleApiError(error, "Error deleting user");
        this.$refs.deleteNotification.showNotification(false);
      }
    },

    showDetails(user) {
      this.selectedUser = user;
    },

    closeDetails() {
      this.selectedUser = null;
    },

    handleApiError(error, defaultMessage) {
      if (error.response && error.response.data && error.response.data.error) {
        this.errorMessage = error.response.data.error;
      } else {
        this.errorMessage = defaultMessage;
      }
    },
  },
};
</script>

<style scoped>
img {
  display: flex;
  border: 2px solid #ddd;
  border-radius: 5px;
  height: auto;
  max-width: 100%;
}

button {
  margin: 20px;
}

ul {
  list-style: none;
  padding: 0;
  margin: 50px;
}

li {
  margin-bottom: 10px;
  padding: 10px;
  border: 2px solid #ddd;
  border-radius: 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.1);
  background-image: linear-gradient(to right, #36a7bb, #f5f5f5);
}

li:hover {
  background-color: #e5e5e5;
  background-image: linear-gradient(to right, #62a5cc, #f5f5f5);
  box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.15);
}

input,
select {
  padding: 5px;
  margin-right: 10px;
  border-radius: 5px;
}

.email {
  font-size: 20px;
}

.notice {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 400px;
  padding: 0.75rem 1.25rem;
  margin-bottom: 1rem;
  border: 1px solid transparent;
  border-radius: 0.25rem;
  margin: 0 auto;
}

.warning {
  color: #644c05;
  background-color: #fff3cd;
  border-color: #ffeeba;
}

@media only screen and (max-width: 360px) {
  li {
    padding: 5px;
    min-width: 200px;
  }

  .email {
    font-size: 10px;
  }

  .list-head {
    margin: 40px;
    min-width: 200px;
    min-height: 200px;
  }
}
</style>
