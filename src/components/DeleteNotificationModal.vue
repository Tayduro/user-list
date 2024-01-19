<template>
  <div v-if="isVisible" class="delete-notification-modal">
    <div class="notification-content" :class="notificationStyle">
      {{ notificationMessage }}
    </div>
    <button @click="closeModal">Close</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isVisible: false,
      isSuccess: false,
      isError: false,
      notificationMessage: "",
      notificationStyle: "",
    };
  },
  methods: {
    showNotification(isSuccess) {
      this.isVisible = true;
      this.isSuccess = isSuccess;
      this.isError = !isSuccess;

      if (isSuccess) {
        this.notificationMessage = "User deleted successfully.";
        this.notificationStyle = "success";
      } else {
        this.notificationMessage = "Error deleting user.";
        this.notificationStyle = "error";
      }

      setTimeout(() => {
        this.isVisible = false;
      }, 3000);
    },

    closeModal() {
      this.isVisible = false;
    },
  },
};
</script>

<style scoped>
.delete-notification-modal {
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

.notification-content {
  padding: 10px;
  border-radius: 5px;
}

.error {
  color: red;
  background-color: #f8d7da;
  border-color: #f5c6cb;
}

button {
  margin-top: 10px;
  padding: 5px 10px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
</style>
