<template>
  <div>
    <form @submit.prevent="addUser">
      <h2>Add a new user</h2>
      <label>
        Name:
        <input v-model="newUser.name" />
        <div v-if="validationErrors.name" class="error-message">
          {{ validationErrors.name }}
        </div>
      </label>

      <label>
        Email:
        <input v-model="newUser.email" />
        <div v-if="validationErrors.email" class="error-message">
          {{ validationErrors.email }}
        </div>
      </label>

      <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>
      <p v-if="notification.message" :class="notification.style">
        {{ notification.message }}
      </p>

      <button type="submit">Add User</button>
    </form>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      errorMessage: "",
      newUser: {
        name: "",
        email: "",
      },
      validationErrors: {
        name: "",
        email: "",
      },
      notification: {
        message: "",
        style: "",
      },
    };
  },
  methods: {
    async addUser() {
      if (!this.validate()) {
        return;
      }

      try {
        await axios.post("https://reqres.in/api/users", {
          name: this.newUser.name,
          email: this.newUser.email,
        });

        this.newUser = {
          name: "",
          email: "",
        };

        this.validationErrors = {};
        this.showNotification("User added successfully", "success");
      } catch (error) {
        if (
          error.response &&
          error.response.data &&
          error.response.data.error
        ) {
          this.errorMessage = error.response.data.error;
        } else {
          this.errorMessage = "Error adding a user";
        }
        this.showNotification("Error adding a user", "error-message");
      }
    },

    validate() {
      this.validationErrors = {};

      if (!this.newUser.name) {
        this.validationErrors.name = "Name cannot be empty";
      }

      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      if (!emailRegex.test(this.newUser.email)) {
        this.validationErrors.email = "Invalid email format";
      }

      for (const key in this.validationErrors) {
        if (this.validationErrors[key]) {
          return false;
        }
      }

      return true;
    },

    showNotification(message, style) {
      this.notification.message = message;
      this.notification.style = style;
      setTimeout(() => {
        this.notification.message = "";
        this.notification.style = "";
      }, 3000);
    },
  },
};
</script>

<style scoped>
form {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  max-width: 300px;
  margin: 0 auto;
  padding: 5%;
  border: 2px solid black;
  margin-top: 20px;
  border-radius: 30px;
}

label {
  margin-bottom: 15px;
  padding: 10px;
  text-align: left;
}

input {
  padding: 5px;
  margin-top: 5px;
  width: 100%;
  box-sizing: border-box;
  border-radius: 0.5rem;
}

input,
select {
  border-radius: 0.5rem;
  padding: 8px;
  margin-right: 10px;
  font-size: 16px;
}

@media only screen and (max-width: 370px) {
  h2 {
    font-size: 16px;
  }
  form {
    margin: 40px;
    padding: 2%;
    min-width: 200px;
    min-height: 200px;
  }

  .email {
    font-size: 8px;
  }

  button {
    margin-top: 15px;
    font-size: 10px;
  }
}
</style>
