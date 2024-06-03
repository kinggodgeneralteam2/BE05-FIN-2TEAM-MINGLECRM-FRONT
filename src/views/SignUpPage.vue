<template>
  <div class="signup-text-div">
    회원가입
  </div>

  <div class="vertical-spacer"> </div>

  <div class="signup-page">
    <SignUpForm />
  </div>
</template>

<script>
import SignUpForm from "@/components/SignUpForm.vue";

export default {
  components: {
    SignUpForm,
  },
};
</script>

<style scoped>

.signup-text-div {
  font-size: 40px;
}

.vertical-spacer {
  margin-bottom: 2%;  
}

.signup-page {
  max-width: 800px;
  max-height: 800px;
  margin: auto auto 100px auto;
  padding: 70px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f9f9f9;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

h2 {
  margin-bottom: 20px;
  color: #333;
  text-align: center;
}
</style>
