<template>
  <div>
    <form @submit.prevent="signUp" class="signup-form">

      <div class="form-group-name">
        <input id="name" v-model="name" type="text" required placeholder="이름">
      </div>

      <div class=signup-form-vertical-spacer> </div>

      <div class="form-group-email">
        <input id="email" v-model="email" type="email" required @input="validateEmail" placeholder="이메일">
        <div class="email-form-horizon-spacer"> </div>
        <button class="email-auth-button" type="button" @click="toggleAuthCodeInput"> 인증 </button>
        
      </div>

      <div class=signup-form-vertical-spacer> </div>

      <div class="form-group-email-auth" v-if="showAuthCodeInput">
        <input id="auth-code" v-model="authCode" type="text" placeholder="인증번호" >
        <div class="email-form-horizon-spacer"> </div>
        <button class="email-auth-check-button" type ="button" @click="emailAuthCheck"> 확인 </button>
      </div>
      

      <p class="email-message" :style="{ color: isEmailValid ? 'blue' : 'red' }">{{ emailMessage }}</p>
       
      <div class=signup-form-vertical-spacer> </div>

      <div class="form-group">
        <input id="phone" v-model="phone" type="tel" required @input="validatePhone" placeholder="전화번호">
        <p class="phone-message" :style="{ color: isPhoneValid ? 'blue' : 'red' }">{{ phoneMessage }}</p>
      </div>

      <div class=signup-form-vertical-spacer> </div>

      <div class="form-group">
        <input id="password" v-model="password" type="password" required @input="validatePassword" placeholder="비밀번호" >
        <p class="password-message" :style="{ color: isPasswordValid ? 'blue' : 'red' }">{{ passwordMessage }}</p>
      </div>

      <div class=signup-form-vertical-spacer> </div>

      <div class="form-group">
        <input id="confirmPassword" v-model="confirmPassword" type="password" required @input="validateConfirmPassword" placeholder="비밀번호 확인" >
        <p class="confirm-password-message" :style="{ color: doPasswordsMatch ? 'blue' : 'red' }">{{ confirmPasswordMessage }}</p>
      </div>

      <!-- 유효성 메시지 표시 -->
      <button type="submit" class="btn-submit" :disabled="!isPasswordValid || !doPasswordsMatch">회원가입</button>



    </form>
    <p v-if="message" class="message">{{ message }}</p>
  </div>
</template>

<script>
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from 'vue-router';

export default {
  setup() {
    const email = ref('');
    const name = ref('');
    const phone = ref('');
    const password = ref('');


    const message = ref('');
    const passwordMessage = ref('');
    const confirmPassword = ref('');
    const confirmPasswordMessage = ref('');
    
    const emailMessage = ref('');
    const phoneMessage = ref('');
    const isPasswordValid = ref(false);
    const doPasswordsMatch = ref(true);
    const isEmailValid = ref(false);
    const isPhoneValid = ref(false);

    const isEmailAuthValid = ref(false);

    const showAuthCodeInput = ref(false);
    const authCode = ref('');

    const router = useRouter();

    console.log(authCode);

    const signUp = async () => {
      if (password.value !== confirmPassword.value) {
        confirmPasswordMessage.value = '비밀번호가 일치하지 않습니다.';
        doPasswordsMatch.value = false;
        return;
      }

      validatePassword();
      if (!isPasswordValid.value) {
        window.alert('비밀번호를 정확히 입력해주세요.');
        return;
      }

      if (!isEmailAuthValid.value) {
        window.alert('이메일 인증을 해주세요.');
        return;
      }

      if (!isEmailValid.value) {
        window.alert('이메일 주소를 정확히 입력해주세요.');
        return;
      }

      if (!isPhoneValid.value) {
        window.alert('전화번호를 정확히 입력해주세요.');
        return;
      }

      try {
        const response = await axios.post('http://localhost:8080/api/v1/auth/signup', {
          email: email.value,
          name: name.value,
          phone: phone.value,
          password: password.value
        });

        alert(`${name.value}님, 환영합니다! 회원가입이 성공적으로 완료되었습니다.`);
        message.value = response.data.message;
        await router.push('/login');
      } catch (error) {
        if (error.response) {
          message.value = error.response.data.message;
        } else {
          message.value = '네트워크 오류: 서버에 연결할 수 없습니다.';
        }
      }
    };

    const emailAuthCheck = async () => {
      try {
        const response = await axios.post("http://localhost:8080/api/v1/auth/authcheck", {
          email: email.value,
          authCode: authCode.value
        });
  
        if (response.data.status === "success") {
          console.log("인증 성공");
          isEmailAuthValid.value = !isEmailAuthValid.value;
          window.alert("이메일 인증에 성공하였습니다!");
        } else {
          console.log("인증 실패");
          window.alert("잘못된 인증코드입니다. 다시 입력해주세요.");
          isEmailAuthValid.value = !isEmailAuthValid.value;
        }

      } catch (error) {
        if (error.response) {
            message.value = error.response.data.message;
          } else {
            message.value = '네트워크 오류: 서버에 연결할 수 없습니다.';
          }
      }
    } 

    const toggleAuthCodeInput = async () => {
      if (!isEmailValid.value) {
      window.alert('이메일 주소를 정확히 입력해주세요.');
      return;
      } else {
        
        try {
        window.alert('잠시만 기다려주세요');
        showAuthCodeInput.value = true;
        const response = await axios.post('http://localhost:8080/api/v1/auth/signup/emailauth', {
          email: email.value
        });
        
        if (response.data.data === false) {
          window.alert('이미 존재하는 이메일입니다. 다른 이메일을 사용해주세요.');
        } else {
          window.alert('이메일 코드가 발송되었습니다.');
        }


        message.value = response.data.message;
        
        } catch (error) {
          if (error.response) {
            message.value = error.response.data.message;
          } else {
            message.value = '네트워크 오류: 서버에 연결할 수 없습니다.';
          }
        }
      }
    };



    const validatePassword = () => {
      const passwordPattern = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,16}$/;
      if (!passwordPattern.test(password.value)) {
        passwordMessage.value = '영문, 숫자, 대문자, 특수문자를 조합하여 8-16자로 입력해주세요.';
        isPasswordValid.value = false;
      } else {
        passwordMessage.value = '올바른 비밀번호입니다.';
        isPasswordValid.value = true;
      }
    };

    const validateConfirmPassword = () => {
      if (password.value === confirmPassword.value ) {
        confirmPasswordMessage.value = '두 비밀번호가 일치합니다.'
        doPasswordsMatch.value = true;
      } else {
        confirmPasswordMessage.value = '두 비밀번호가 일치하지 않습니다.'
        doPasswordsMatch.value = false;
      }
    }

    const validateEmail = () => {
      const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      if (!emailPattern.test(email.value)) {
        emailMessage.value = '올바른 이메일 주소를 입력해주세요.';
        isEmailValid.value = false;
      } else {
        emailMessage.value = '올바른 이메일 주소입니다.';
        isEmailValid.value = true;
      }
    };

    const validatePhone = () => {
      const phonePattern = /^\d{3}\d{4}\d{4}$/;
      if (!phonePattern.test(phone.value)) {
        phoneMessage.value = '올바른 전화번호 형식을 입력해주세요. (000-0000-0000)';
        isPhoneValid.value = false;
      } else {
        phoneMessage.value = '올바른 전화번호 형식입니다.';
        isPhoneValid.value = true;
      }
    };

    return {
      email,
      name,
      phone,
      password,
      message,
      passwordMessage,
      emailMessage,
      phoneMessage,
      isPasswordValid,
      isEmailValid,
      isPhoneValid,
      signUp,
      validatePassword,
      validateEmail,
      validatePhone,
      showAuthCodeInput,
      toggleAuthCodeInput,
      doPasswordsMatch,
      confirmPasswordMessage,
      validateConfirmPassword,
      confirmPassword,
      authCode,
      emailAuthCheck,
      isEmailAuthValid
    };
  }
};
</script>


<style scoped>


.signup-form {
  max-width: 400px;
  margin: 0 auto;
}

.form-group-name {
  max-width: 200px;

}

.form-group-email {
  display: flex;
  max-width: 500px;
}

.form-group-email-auth {
  display: flex;
  max-width: 500px;
  text-align: center;
}

.email-form-horizon-spacer {
  margin-right: 30px;
}

.email-auth-button {
  
  min-width: 70px;
  background-color: #007bff;
  color: #fff;
  border: 1px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;

  font-family: "Do Hyeon", sans-serif;
}

.email-auth-check-button {
  min-width: 70px;
  background-color: #007bff;
  color: #fff;
  border: 1px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;

  font-family: "Do Hyeon", sans-serif;
}

.signup-form {
  max-width: 600px;
  margin: 0 auto;
}

.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  font-weight: bold;
}

input[type="email"],
input[type="text"],
input[type="tel"],
input[type="password"] {
  width: 100%;
  padding: 10px;
  font-size: 18px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-family: "Do Hyeon", sans-serif;
}

.btn-submit {
  background-color: #007bff;
  color: #fff;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
}

.btn-submit:hover {
  background-color: #0056b3;
}

.message {
  color: red;
  margin-top: 10px;
}

.password-message {
  margin-top: 5px;
}

::placeholder {
  font-family: "Do Hyeon", sans-serif;
}

.signup-form-vertical-spacer {
  margin-bottom: 30px;
}
</style>
