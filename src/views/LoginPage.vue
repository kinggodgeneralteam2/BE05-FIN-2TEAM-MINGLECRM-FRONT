<template>
  <div>
    <div style="font-size: 40px;">
    로그인
    </div>

    <div class="vertical-spacer"> </div>
    <div class="signin-page">
      <LoginForm />
    </div>
    
  </div>
</template>

<script>


import LoginForm from "@/components/LoginForm.vue";

export default {
  components: {
    LoginForm
  }
};
</script>

<style scoped>
/* 필요한 스타일 작성 */

.vertical-spacer {
  margin-bottom: 2%;  
}

.signin-page {
  max-width: 800px;
  max-height: 800px;
  margin: auto auto 100px auto;
  padding: 70px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f9f9f9;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}
</style>
