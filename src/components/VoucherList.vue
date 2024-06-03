코드 복사
<template>
  <div class="voucher-list-container">
    <div class="voucher-list">
      <h2>바우처 목록</h2>
      <div v-if="vouchers.length > 0">
        <div class="voucher-header">
          <span class="voucher-column">회원 ID</span>
          <span class="voucher-column">회원명</span>
          <span class="voucher-column">발급 직원 ID</span>
          <span class="voucher-column">발급 직원명</span>
          <span class="voucher-column">금액</span>
          <span class="voucher-column">바우처 코드</span>
        </div>
        <ul class="voucher-items">
          <li
            v-for="voucher in vouchers"
            :key="voucher.id"
            class="voucher-item"
          >
            <div class="voucher-info">
              <span class="voucher-customerId">{{ voucher.customerId }}</span>
              <span class="voucher-customerName">{{
                voucher.customerName
              }}</span>
              <span class="voucher-employeeId">{{ voucher.employeeId }}</span>
              <span class="voucher-employeeName">{{
                voucher.employeeName
              }}</span>
              <span class="voucher-amount">{{ voucher.amount }}</span>
              <span class="voucher-voucherCode">{{ voucher.voucherCode }}</span>
            </div>
          </li>
        </ul>
      </div>
      <p v-else class="empty-message">바우처가 없습니다.</p>
      <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      vouchers: [],
      errorMessage: "",
    };
  },
  created() {
    this.fetchVouchers();
  },
  methods: {
    async fetchVouchers() {
      try {
        const response = await axios.get(
          "http://localhost:8080/api/v1/vouchers"
        );
        this.vouchers = response.data.data; // response.data.data로 접근
        this.errorMessage = "";
      } catch (error) {
        console.error("바우처 목록을 불러오는 중 에러 발생:", error);
        this.errorMessage = "바우처 목록을 불러오는 중 에러가 발생했습니다.";
      }
    },
  },
};
</script>

<style scoped>
.voucher-list-container {
  max-width: 800px;
  margin: 0 auto;
}

.voucher-list {
  background-color: #f5f5f5;
  border-radius: 10px;
  padding: 20px;
}

.voucher-header {
  display: flex;
  justify-content: space-between;
  font-weight: bold;
  margin-bottom: 10px;
}

.voucher-column {
  flex: 1;
  text-align: center;
}

.voucher-items {
  list-style-type: none;
  padding: 0;
}

.voucher-item {
  background-color: #ffffff;
  padding: 15px;
  border-radius: 5px;
  margin-bottom: 15px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.voucher-info {
  display: flex;
  justify-content: space-between;
}

.voucher-info span {
  flex: 1;
  text-align: center;
}

.empty-message,
.error-message {
  margin-top: 20px;
  text-align: center;
  color: #555555;
}

.error-message {
  color: red;
}
</style>
