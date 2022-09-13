<template>

  <!-- header start -->
  <headerComp />
  <!-- header end  -->


  <!-- navbar start -->
  <div id="create-section">
      <div id="nav">
        <button @click="createNewActive = true, updateFieldActive = false, mainContentPosts=false, removeInputs()"> <img class="icons" src="./assets/plusIcon.svg" alt=""> </button>
        <div id="nav-right">
          <button id="zip-padding" class="bold">&nbsp; ZIP website &nbsp; </button>
          <button> <img src="./assets/refreshIcon.svg" alt="" class="icons"></button>
        </div>
      </div>
    </div>
 <!-- navbar end -->


 <!-- Vic's add a post section -->
<!-- <post :createNewActive="this.createNewActive" :editState="editState" :formValues="this.formValues" @call-insertDoc="insertDoc"/> -->

<div v-if="createNewActive" class="flexCenter">
  <div class="mainFormStyling">
        <h3>Create new post</h3>

        <input type="text" placeholder="Title" v-model="formValues.title">
        <input type="text" placeholder="ImageUrl" v-model="formValues.imageUrl">
        <input type="text" placeholder="ImageUrl" v-model="formValues.location">
        <div class="formBtnFlex">
        <button @click="insertDoc(), mainContentPosts=true" class="formBtn blue "> Post </button>
        <button @click="createNewActive = false, mainContentPosts=true" class="formBtn black"> Cancel </button>
        </div>
</div>
      </div>   
 <!--add a post section end-->


  <!-- update docutment section start -->
  <div class="flexCenter" v-if="updateFieldActive">
    <div class="mainFormStyling">
      <h3>Edit post</h3>
    <br>

    <input type="text" placeholder="Title" v-model="formValues.title">
    <input type="text" placeholder="ImageUrl" v-model="formValues.imageUrl">
    <input type="text" placeholder="Location" v-model="formValues.location">

    <div class="formBtnFlex">

    <button class="formBtn black" @click="updateFieldActive = false, mainContentPosts=true"> Cancel</button>

    <button @click="updateDoc(), updateFieldActive=false" class="formBtn blue"> Save changes </button>
    </div>

    <button @click="deleteDoc(id), updateFieldActive=false" class="formBtn red"> Delete </button>

    <br>
  </div> 
</div> 
<!-- update docutment section end -->







<!-- list items section loop start -->
  <ul v-if="mainContentPosts">
    <li v-for="profile, index in profiles" :key=index class="grid-list">
      <div class="postersName">
        <h3 class="proxima">Guy Nameson</h3>
        <button class="pencil" @click="getDoc(profile._id), updateFieldActive = true, createNewActive = false, mainContentPosts=false ">
           <img class="pencil"
            src="./assets/pencil-edit-button-svgrepo-com.svg" /> </button>
      </div>
      <p class="proxima"> {{profile.location}} </p>
      <h2 class="proxima heavy-font"> {{profile.title}} </h2>
     

      <!-- <button @click="deleteDoc(profile._id)"> Delete </button> -->
      <img :src="profile.imageUrl" alt="">
      <br>
      
        <br>


<!-- comments section start -->
        <div class="reply-parent">
          <h5> </h5>
          <p class="replies"> <span class="bold"> Kate Marshall </span>
            <br> {{profile.comments}} </p>
        </div>


        <div id="replyToComment">
          <input class="replies" v-model="formValues.comments" id="replyCommentBox"
          type="text" placeholder="Reply" >

          <button  @click="getDoc(profile._id), updateDoc()"   class="icons"> <img class="icons" src="./assets/send-svgrepo-com(1).svg" alt=""></button>
          <br>
        </div>
        <!-- comments section end -->
      
    </li>
  </ul>
  <!-- list items loop end -->

</template>




<script>
const api = "https://ratbash-api.netlify.app/.netlify/functions/api/"

// import post from './components/post.vue'
import headerComp from './components/headerComp.vue'

export default {
  data() {
    return {
      mainContentPosts: true,
      createNewActive: false,
      updateFieldActive: false, 
      profiles: [],
      id: "",
      formValues: {
        username: "",
        title: "",
        imageUrl: "",
        location: "",
        comments: "",
      }
    }
  },
  methods: {
    //Post aka insert the item
    insertDoc() {
      fetch(api, {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify(this.formValues)
      })
        .then((response) => response.text())
        .then((data) => {
          console.log(data),
            this.getAll();
          this.removeInputs();
          this.createNewActive = false
        })
        .catch((err) => {
          if (err) throw err;
        })
    },


    //Put aka update the item
    updateDoc() {
      fetch(api + this.id, {
        method: "PUT",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify(this.formValues)
      })
        .then((response) => response.text())
        .then((data) => {
          console.log(data),
            this.getAll(),
            this.removeInputs();
        })
        .catch((err) => {
          if (err) throw err;
        })
    },

    //Get fetch aka find the item
    getDoc(id) {
      this.id = id
      fetch(api + id, {
        method: 'GET'
      })
        .then((response) => response.json())
        .then((data) => {
          console.log(data)
          this.formValues.title = data.title
          this.formValues.imageUrl = data.imageUrl
          this.formValues.location = data.location
          
        })

        .catch((err) => {
          if (err) throw err;
        })
    },

    //Delete the item
    deleteDoc(id) {
      fetch(api + id, {
        method: "DELETE",
      })

        .then((response) => response.text())
        .then((data) => {
          console.log(data),
            this.getAll();
          this.removeInputs();

        })
        .catch((err) => {
          if (err) throw err;
        })

    },

    getAll() {
      fetch(api)
        .then((response) => response.json())
        .then((data) => {
          this.profiles = data,
            // this.getAll()
            this.mainContentPosts = true
        })
        .catch((err) => {
          if (err) throw err;
        })
    },

    removeInputs() {
      this.formValues.title = ""
      this.formValues.imageUrl = ""
      this.formValues.location = ""
      this.formValues.replies = ""
    },
  },

  mounted() {
    fetch(api)
      .then((response) => response.json())
      .then((data) => {
        this.profiles = data
      })
      .catch((err) => {
        if (err) throw err;
      });
  }, components: { headerComp }


}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto&display=swap');
@import url("https://use.typekit.net/kjl5yqv.css");


/* header section */
#header {
  background-color: black;
  padding-left: 0;
  margin-left: 0;
}
#header img {
  max-height: 5vw;
  padding: 1vw 5vw 1vw 5vw;
}

/* Generic and specific icon sizing */
.icons {
  height: 30px;
  width: auto;
  border: none;
  background-color: white
}
.pencil {
  height: 18px;
  width: auto;
  border: none;
  background-color: white
}
#zip-padding {
  margin-right: 3vw;
padding-left: 30px;}


/* black div styling */
div {
  margin: 0;
  padding: 0;
}



.postersName {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  padding: 10px 0 10px 0;
  padding-right: 1.75rem;

}

/* Font stylings */
.proxima {
  font-family: proxima-nova, sans-serif;
  font-weight: 400;
  font-style: normal;
  padding-left: 1.75rem;
  margin: 0
}

.bold {
  font-weight: 1000;
}

.heavy-font {
  background-color: #b3b6bf;
  color: white;
}


/* Reply parent div styling */
.reply-parent {
  padding-left: 1.75rem;
  padding-right: 1.75rem;
}

/* post reply styling */
.replies {
  padding: 20px;
  background-color: #f5f5f5;
  border-radius: 20px;
  padding: 5px 15px 5px 15px;
  border: none;
}
/* reply to posts styling */
#replyToComment {
  display: flex;
  padding-left: 1.75rem;
  padding-right: 1.75rem;
  padding-bottom: 15px;
  justify-content: space-between;
}

#replyCommentBox{
  width: 80%;
}




/* Navigation section */

#nav {
  display: flex;
  flex-direction: row;
  padding: 0 28px 0 28px;

}

#nav-right {
  display: flex;
  flex-direction: row;
  position: sticky;
  left: 1000%;
}


/* List items/loop styling */
ul {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  /* grid-template-rows: 400px 400px; */
  grid-gap: 1.5em;
  padding: 20px;
}
li {
  list-style: none;
  background-color: rgb(255, 255, 255);
  border-radius: 5px;
}
li img {
  width: 100%;
  height: 18vw;
  object-fit: cover;
  padding-top: 0;
}


/* Create post section */
#create-section {
  background-color: rgb(12, 194, 194);
  padding: 8px 0 8px 0;

}

#create-section button {
  color: rgb(12, 194, 194);
  border: none;
  background-color: rgb(255, 255, 255);
  border-radius: 10px;
  display: flex;
  align-items: center;
  padding: 5px 8px 5px 8px
}

#create-section img {
  border-radius: 10px;
}


/* update information section */
.mainFormStyling{
  background-color: #ffffff;
  border: rgba(25, 134, 163, 1) solid 4px;
  border-radius: 15px;
  display: flex;
  flex-direction: column;
  margin: 0 15px 0 15px;
  padding: 0 20px 0 20px;
  width: 75vw;
  max-width: 450px;
  display: flex;
  align-items: center;
}

.mainFormStyling input{
  border: rgba(25, 134, 163, 1) solid 2px;
  border-radius: 5px;
  margin: 20px 0 20px 0px;
 padding: 8px 20px 8px 20px;
 width: 80%;
}

/* form button styling */
.red{
  background-color: rgba(255, 90, 90, 1);
}

/* button styling */
.black{
  background-color: rgb(35, 35, 35);
}
.blue{
  background-color: rgba(25, 134, 163, 1)
}

.formBtn{
  color: white;
  border-radius: 5px;
  margin: 20px 0 20px 0px;
 padding: 8px 10px 8px 10px;
 width: 30%;
 border: none;
 font-size: 0.8rem;
}

.formBtnFlex{
  display: flex;
flex-direction: row;
justify-content: space-around;
width: 100%;
margin: 0;
padding: 0;}
/* button styling end*/


/* align items to center class*/
.flexCenter{
  display: flex;
  flex-direction: row;
  justify-content: center;
  margin: 0;
  padding: 0;
  padding-top: 25px;
}

@media screen and (max-width: 900px) {

  /* #header img {
    max-height: 10vw;
    padding: 2vw 10vw 2vw 5vw;
  } */

  ul {
    display: grid;
    grid-template-columns: 1fr;
    /* grid-template-rows: 400px 400px; */
    grid-gap: 1.5em;
    padding: 0%;
  }

  li{border-radius: 0px;}

  li img {
    width: 100%;
    height: 48vw;
    object-fit: cover;
    padding-top: 0;
  }
  .formBtn{
 width: 40%;
}


}
</style>
