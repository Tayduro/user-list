<template>
  <div class="user-details-modal">
    <h2>{{ user.first_name }} {{ user.last_name }}</h2>
    <p>Email:</p>
    <p>{{ user.email }}</p>

    <div>
      <label>
        Phone number:
        <input v-model="editedPhone" :disabled="!editing" />
        <div v-if="validationErrors.phone" class="error-message">
          {{ validationErrors.phone }}
        </div>
      </label>
    </div>

    <div>
      <label>
        Address:
        <input v-model="editedAddress" :disabled="!editing" />
        <div v-if="validationErrors.address" class="error-message">
          {{ validationErrors.address }}
        </div>
      </label>
    </div>

    <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>
    <p v-if="notification.message" :class="notification.style">
      {{ notification.message }}
    </p>

    <button v-if="!editing" @click="toggleEditing">Edit</button>
    <button v-if="editing" @click="saveChanges">Save Changes</button>
    <button @click="closeDetails">Close</button>
  </div>
</template>

<script>
import axios from "axios";

export default {
  props: {
    user: Object,
  },
  data() {
    return {
      editing: false,
      editedPhone: "",
      editedAddress: "",
      originalPhone: "", // store the original phone number
      originalAddress: "", // store the original address
      errorMessage: "",
      validationErrors: {
        phone: "",
        address: "",
      },
      notification: {
        message: "",
        style: "",
      },
    };
  },
  methods: {
    closeDetails() {
      this.$emit("closeDetails");
      this.errorMessage = "";
    },

    toggleEditing() {
      this.editing = !this.editing;

      if (this.editing) {
        this.originalPhone = this.user.phone; // store the original phone number
        this.originalAddress = this.user.address; // store the original address
        this.editedPhone = this.user.phone || this.editedPhone;
        this.editedAddress = this.user.address
          ? `${this.user.address.city}, ${this.user.address.street}`
          : this.editedAddress;
      }
    },

    async saveChanges() {
      if (!this.validate()) {
        return;
      }

      try {
        const updatedUser = {
          ...this.user,
          phone: this.editedPhone,
          address: this.editedAddress,
        };

        await axios.put(
          `https://reqres.in/api/users/${this.user.userId}`,
          updatedUser,
        );

        this.editing = false;
        this.showNotification("Changes saved successfully", "success");
      } catch (error) {
        if (
          error.response &&
          error.response.data &&
          error.response.data.error
        ) {
          this.errorMessage = error.response.data.error;
        } else {
          this.errorMessage = "Error saving changes.";
        }
        this.showNotification("Error saving changes", "error-message");
      }
    },

    validate() {
      this.validationErrors = {};

      if (this.editing) {
        if (this.editedPhone.length < 1 && this.originalPhone === "") {
          this.validationErrors.phone = "Phone number cannot be empty";
        }

        if (this.editedAddress.length < 1 && this.originalAddress === "") {
          this.validationErrors.address = "Address cannot be empty";
        }
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
.user-details-modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #fff;
  padding: 20px;
  border: 1px solid #0f0f0f;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  z-index: 1000;
}

.validationErrors {
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 25px;
  padding: 10px;
}

input {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin: 10px;
  width: 250px;
}

.editable-field {
  cursor: pointer;
  color: #3498db;
  text-decoration: underline;
}

@media only screen and (max-width: 768px) {
  .user-details-modal {
    margin: 40px;
    position: fixed;
    padding: 10px;
  }
}

@media only screen and (max-width: 370px) {
  .user-details-modal {
    margin: 20px;
    position: fixed;
    padding: 10px;
  }
}
</style>
