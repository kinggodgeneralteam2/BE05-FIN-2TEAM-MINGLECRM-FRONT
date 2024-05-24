<template>
<div class="top-bar">

    <div class="top-bar-left">
        <div class="logo">로고</div>
        <a href="/" class="home-button">MINGLE</a>
    </div>

    <div class="top-bar-right">
        <a href="/signup" class="top-bar-signup-button">회원 가입</a>
        <span class="spacer"></span>
        <a href="/login" class="top-bar-login-button">로그인</a>
    </div>

</div>

</template>

<style>

.top-bar {
    display:flex;
    background-color: #3C5B6E;
    justify-content: space-between;
    color: white;
}

.top-bar-right{
    display: flex;
    align-items: center; /* 세로 중앙 정렬 */
    font-size: 25px;
    margin-right: 30px;

}

.top-bar-left {
    display: flex;
    align-items: center; /* 세로 중앙 정렬 */
}

.home-button {
    font-size: 40px;
}

.spacer {
    margin-left: 20px; /* 원하는 간격 값으로 조정 */
}

a {
    text-decoration: None;
    color:white;
}
</style>