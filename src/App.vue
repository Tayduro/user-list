<template>
  <div id="app">
    <UserList />
  </div>
</template>

<script>
import UserList from "@/components/UserList.vue";

export default {
  components: {
    UserList,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h2 {
  color: #333;
  text-align: center;
}

button {
  background-color: #389adb;
  color: #fff;
  border: none;
  height: auto;
  max-width: 100%;
  padding: 5px;
  margin: 5px;
  cursor: pointer;
  border-radius: 5px;
  font-size: 25px;
  box-shadow: 0px 5px 20px rgba(0, 0, 0, 0.3);
}

button:hover {
  background: rgb(2, 0, 36);
  background: linear-gradient(
    90deg,
    rgba(30, 144, 255, 1) 0%,
    rgba(0, 212, 255, 1) 100%
  );
}

button:active {
  transform: translate(0em, 0.2em);
}

.error-message {
  color: red;
  background-color: #eddcd4;
  border-color: #e6c8c3;
  border-radius: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 15px;
}

.success {
  color: green;
  border-radius: 40px;
  background-color: #d4edda;
  border-color: #c3e6cb;
  margin: 15px;
}

@media only screen and (max-width: 768px) {
  h2 {
    font-size: 2em;
  }
  li {
    flex-direction: column;
    text-align: center;
  }

  .email {
    font-size: 20px;
  }

  button {
    margin-top: 15px;
    font-size: 15px;
  }
}

@media only screen and (max-width: 370px) {
  h2 {
    font-size: 16px;
  }

  button {
    margin-top: 15px;
    font-size: 15px;
  }
}
</style>
