<template>
    <meta name="theme-color" content="black">
    <div class="header">
        <img src="src/assets/ZipLogoCropped.png" alt="Fail">

        <div class="profileContainer">
        </div>
    </div>
</template>

<style scoped>
    .header{
        background-color: black;
        padding-left: 0;
        margin-left: 0;
        display: flex;
        justify-content: space-between;
        align-items: center;
        flex-direction: row;
        width: 100vw;
        height: 80px;
        position: -webkit-sticky;
        position: sticky;
        top: 0px;
    }

    .header Img {
        width: 8rem;
        padding: 10px 0vw 10px 28px;
    }

    .profileContainer {
        width: 50px;
        height: 50px;
        background-image: url("src/assets/profile.png");
        background-size: cover;
        background-repeat: no-repeat;
        border-radius: 50%;
        margin: 10px 28px 0vw 10px;
    }

    @media screen and (max-width: 900px) {
        
        .header img {
            width: 10rem;
            padding: 5px 0vw 5px 28px;
        }
    }
</style>