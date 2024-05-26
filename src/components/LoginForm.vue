<template>
  <div>
    <div class="login-form">
      <!-- 이메일 입력 -->
      <div class="form-group">
        <input id="email" v-model="email" type="email" required placeholder="이메일">
      </div>

      <div class=signup-form-vertical-spacer> </div>

      <!-- 비밀번호 입력 -->
      <div class="form-group">
        <input id="password" v-model="password" type="password" required placeholder="비밀번호">
      </div>
      <!-- 로그인 버튼 -->
      <button v-if="!isValidEmailPassword" type="submit" class="btn-submit" @click="signInValid">로그인</button>
      
    </div>

    <!-- 로그인 성공 시 회원 정보 수정 버튼 -->
    <div class="form-email-auth" v-if="isValidEmailPassword">
      <input id="authCode" v-model="authCode" type="text" required placeholder="인증코드"> 
      <div class="email-form-horizon-spacer"> </div>
      <button class="email-auth-check-button" @click="signIn">인증</button>
    </div>
    
  </div>
</template>

<script>
import { useAuthStore } from '../storage/auth';
import { ref, watch } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const authStore = useAuthStore(); // Pinia Store 불러오기

    const email = ref(''); // 이메일
    const password = ref(''); // 비밀번호
    const message = ref(''); // 로그인 결과 메시지
    const isSuccess = ref(false); // 로그인 성공 여부
    const isValidEmailPassword = ref(false); // 로그인 요청한 이메일과 패스워드 유효성 여부
    const authCode = ref('');
    // const userId = ref(null); // 로그인한 사용자의 ID


    const signInValid = async () => {
      try {
        // 이메일, 패스워드 유효성 체크
        const response = await axios.post('http://localhost:8080/api/v1/auth/signin/valid', {
          email: email.value,
          password: password.value
        });

        if (response.data.data === true) {
          isValidEmailPassword.value = true;
          window.alert("인증 메일이 발송되었습니다!");
        } else {
          window.alert("로그인에 실패하였습니다. 이메일과 비밀번호를 확인해주세요.");
        }
        console.log(response.data.status);


      } catch (error) {
        // 네트워크 오류 메시지 표시
        console.error('로그인 오류:', error);
        message.value = '로그인에 실패했습니다.';
        isSuccess.value = false;
      }
    }

    
    const emailRequest = async () => {
      try {
        // 서버에서 사용자 정보 가져오기
        const response = await axios.post('http://localhost:8080/api/v1/auth/signin/email', {
          email: email.value,
          password: password.value
        });

        console.log(response.data.status);
        authStore;

      } catch (error) {
        // 네트워크 오류 메시지 표시
        console.error('로그인 오류:', error);
        message.value = '로그인에 실패했습니다.';
        isSuccess.value = false;
      }
    };

    const signIn = async () => {
      console.log("signIn함수 실행");
      try {
        console.log(email.value);
        console.log(authCode.value);
        // 인증 코드 확인하기
        const response = await axios.post('http://localhost:8080/api/v1/auth/signin', {
          email: email.value,
          password: password.value,
          authCode: authCode.value
        })
        console.log(response.data.data);
        const tokens = response.data.data;

        // Pinia Store에 토큰 설정
        authStore.setTokens(tokens.atk, tokens.rtk);

        // AccessToken 저장
        localStorage.setItem('accessToken', tokens.atk);

        // RefreshToken 저장 (HTTP Only 쿠키)
        document.cookie = `refreshToken=${tokens.rtk}; Secure; SameSite=Strict;`;

        // 로그인 성공 메시지 표시
        message.value = '로그인에 성공했습니다.';
        isSuccess.value = true;

        console.log("tokens", tokens);
      } catch (error) {
        // 네트워크 오류 메시지 표시
        console.error('로그인 오류:', error);
        message.value = '로그인에 실패했습니다.';
        isSuccess.value = false;
      }
    }

    // 회원 정보 수정 페이지로 이동 함수
    const goToUpdatePage = () => {
      // 로그인한 사용자의 ID를 사용하여 회원 정보 수정 페이지로 이동
      // 여기서는 this를 사용하지 않아야 합니다.
      // 대신에 router를 직접 import하여 사용해야 합니다.
      // 아래의 코드는 예시이며, 사용 중인 라우터에 따라 수정해야 합니다.
      // import { useRouter } from 'vue-router';
      // const router = useRouter();
      // router.push({ name: 'UserEdit', params: { id: userId.value } });
    };

    watch(isValidEmailPassword, (newVal) => {
      if (newVal) {
        emailRequest();
      }
    });

    return { 
      email, 
      password, 
      message, 
      isSuccess, 
      emailRequest, 
      goToUpdatePage,
      isValidEmailPassword,
      authCode,
      signInValid,
      signIn };
  }

};
</script>
<style scoped>


.login-page {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f9f9f9;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.login-form {
  max-width: 400px;
  margin: 0 auto;
}

.form-group {
  margin-bottom: 20px;
}
.form-email-auth {
  display:flex;
  max-width: 300px;
  margin: 0 auto;
}

label {
  display: block;
  font-weight: bold;
}

input[type="email"],
input[type="password"],
input[type="text"] {
  width: 100%;
  padding: 10px;
  font-size: 18px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-family: "Do Hyeon", sans-serif;
}

.btn-submit {
  background-color: #CAD4CE;
  color: black;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
  font-family: "Do Hyeon", sans-serif;;
}

.btn-submit:hover {
  background-color: #0056b3;
}

.email-auth-check-button {
  min-width: 70px;
  background-color: #CAD4CE;
  color: black;
  border: 1px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;

  font-family: "Do Hyeon", sans-serif;
}

.message {
  color: red;
  margin-top: 10px;
}

.message-success {
  color: blue;
}

::placeholder {
  font-family: "Do Hyeon", sans-serif;
}

.signup-form-vertical-spacer {
  margin-bottom: 30px;
}

.email-form-horizon-spacer {
  margin-right: 30px;
}
</style>

