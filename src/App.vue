<template>

  <!-- import login component -->
  <login :showMain="this.showMain" @showEvent="showMain=true" />


  <section v-if="showMain">

    <!-- header declaration start -->
    <headerComp />
    <!-- header end  -->


    <!-- navbar start -->
    <div id="create-section">
      <div id="nav">
        <button @click="createNewActive = true, updateFieldActive = false, mainContentPosts=false, removeInputs()"> <img
            class="icons" src="./assets/plusIcon.svg" alt=""> </button>
        <div id="nav-right">


          <button id="zip-padding" class="bold"> <a href="https://zip.org.nz/"> &nbsp; ZIP website &nbsp; </a></button>
          <button> <img src="./assets/refreshIcon.svg" alt="" @click="getAll()" class="icons"></button>
        </div>
      </div>
    </div>
    <!-- navbar end -->


    <!-- Ed's add a post section -->
    <div v-if="createNewActive" class="flexCenter">
      <div class="mainFormStyling">
        <h3>Create new post</h3>
        <input type="text" placeholder="Title" maxlength="17" v-model="formValues.title">
        <input type="text" placeholder="ImageUrl" v-model="formValues.imageUrl">
        <input type="text" placeholder="Location" v-model="formValues.location">
        <div class="formBtnFlex">
          <button @click="insertDoc(), mainContentPosts=true" class="formBtn blue "> Post </button>
          <button @click="createNewActive = false, mainContentPosts=true" class="formBtn black"> Cancel </button>
        </div>
      </div>
    </div>
    <!--add a post section end-->


    <!-- update docutment section start -Ed -->
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

    <!-- Main post items section loop start -->
    <ul v-if="mainContentPosts">
      <li v-for="profile in profiles" :key=index class="grid-list">
        <div class="postersName">
          <h3 class="proxima">Guest</h3>
          <button class="pencil"
            @click="getDoc(profile._id), updateFieldActive = true, createNewActive = false, mainContentPosts=false ">
            <img class="pencil" src="./assets/pencil-edit-button-svgrepo-com.svg" /> </button>
        </div>
        <p class="proxima"> {{profile.location}} </p>
        <h2 class="proxima heavy-font"> {{profile.title}} </h2>
        <img :src="profile.imageUrl" alt="">
        <br>

        <br>


        <!-- comments section loop start -->
        <ul class="reply-parent">

          <li v-if="!postMessages[profile._id]" id="greyedOut"> Be the first to comment. </li>

          <li v-for="(pstMsg, i) in postMessages[profile._id]" :key="i">
            <p class="replies"> <span class="bold"> {{replyPostersName}} </span> <br>
              {{pstMsg.comment}} </p>
          </li>
        </ul>
        <!-- comments section loop end -->

        <div id="replyToPost">
          <input v-bind:value="replyValues.comment" v-on:input="msgBoxInput = $event.target.value" class="repliesInput"
            id="replyCommentBox" type="text" @keydown="(isreplybtnenable = msgBoxInput = !''  ? false : true)">
          <button :disabled="isreplybtnenable" @click="insertReply(profile._id)" class="icons">
            <img class="icons" src="./assets/send-svgrepo-com(1).svg" alt="">
          </button>
          <br>
        </div>
        <!-- comments section end -->

      </li>
    </ul>
    <!-- Main post items section loop end -->
  </section>
</template>

<!-- Apis, top one is the main posts, second one is replies -->
<script>
const api = "https://ratbash-api.netlify.app/.netlify/functions/api/"
const replyApi = "https://ratbashreply.netlify.app/.netlify/functions/api/"

// importing of components
import headerComp from './components/headerComp.vue'
import login from './components/login.vue'

// Data
export default {
  data() {
    return {
      mainContentPosts: true,
      createNewActive: false,
      updateFieldActive: false,
      loginClicked: false,
      showMain: false,
      isreplybtnenable: true,
      replyPostersName: "Guest",
      profiles: [],
      postMessages: [],
      id: "",
      msgBoxInput: "",
      formValues: {
        username: "",
        title: "",
        imageUrl: "",
        location: "",
        comments: "",
      },
      replies: [],
      replyValues: {
        comment: "",
        post_id: "",
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

    //Fetch aka find the item details
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

    // Gets all the main posts - used so new or updates posts show up straigt away
    getAll() {
      fetch(api)
        .then((response) => response.json())
        .then((data) => {
          this.profiles = data.reverse(),
            this.getReplies()
          this.mainContentPosts = true
        })
        .catch((err) => {
          if (err) throw err;
        })
    },

    // Removes inouts after user has entered or updated a post
    removeInputs() {
      this.formValues.title = ""
      this.formValues.imageUrl = ""
      this.formValues.location = ""
    },


    // Reply section methods start

    // Inserts the reply to the correct post
    insertReply(post_id) {
      console.log(this.isreplybtnenable)
      this.replyValues.post_id = post_id;
      this.replyValues.comment = this.msgBoxInput;
      console.log(this.replyValues)
      fetch(replyApi, {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify(this.replyValues)
      })
        .then((response) => response.text())
        .then((data) => {
          this.getReplies();
        })
        .catch((err) => {
          if (err) throw err;
        })
      this.replyValues.comment = ""
      this.msgBoxInput = "",
        this.isreplybtnenable = true
    },

// Gets the replies, used to display them, is also called on load via mounting.
    getReplies() {
      fetch(replyApi)
        .then((response) => response.json())
        .then((data) => {
          this.replies = data.reverse()
          this.postMessages = this.replies.reduce((results, msg) => {
            results[msg.post_id] = results[msg.post_id] || [];
            results[msg.post_id].push(msg);
            return results;
          }, {}
          );
          console.log(data)
        })
        .catch((err) => {
          if (err) throw err;
        })
    },

    //Get fetch aka find the replies
    getReplyDocs(id) {
      this.id = id
      fetch(replyApi + id, {
        method: 'GET'
      })
        .then((response) => response.json())
        .then((data) => {
          this.replyValues.comment = data.comment
          this.replyValues.post_id = data.post_id
        })
        .catch((err) => {
          if (err) throw err;
        })
    },

  },
  // Reply section methods end

// Getting data from database on first load
  mounted() {
    fetch(api)
      .then((response) => response.json())
      .then((data) => {
        this.profiles = data.reverse()
      })
      .catch((err) => {
        if (err) throw err;
      });

    fetch(replyApi)
      .then((response) => response.json())
      .then((data) => {
        this.replies = data
      })
      .catch((err) => {
        if (err) throw err;
      });
    this.getReplies()
  },

  components: { headerComp, login }


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
  padding-left: 30px;
  text-decoration: none;
}

/* Link styling */
a {
  text-decoration: none;
  color: #2CB6B4;
}

a :visted {
  text-decoration: none;

}

/* black div styling */
div {
  margin: 0;
  padding: 0;
}

/* Font stylings */
.proxima {
  font-family: proxima-nova, sans-serif;
  font-weight: 400;
  font-style: normal;
  padding-left: 1.75rem;
  margin: 0;
}

.bold {
  font-weight: 1000;
}

.heavy-font {
  background-color: #b3b6bf;
  color: white;
}

/* Styling for the name of post creater */
.postersName {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  padding: 10px 0 10px 0;
  padding-right: 1.75rem;

}

/* Reply parent div styling */
.reply-parent {
  padding-left: 1.75rem;
  padding-right: 1.75rem;
  display: flex;
  flex-direction: column;
  grid-gap: 0px;
  overflow: scroll;
  height: 60px;
}

/* post reply styling */
.replies {
  padding: 20px;
  background-color: #f5f5f5;
  border-radius: 20px;
  padding: 5px 15px 5px 15px;
  border: none;
  margin: 0px 0px 10px 0px;
}

.repliesInput {
  padding: 20px;
  background-color: #f5f5f5;
  border-radius: 20px;
  padding: 5px 15px 5px 15px;
  border: none;
  margin: 0;
}

/* reply to posts styling */
#replyToPost {
  display: flex;
  padding-left: 1.75rem;
  padding-right: 1.75rem;
  padding-bottom: 15px;
  justify-content: space-between;
  padding-top: 15px;
}

#replyCommentBox {
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
  grid-gap: 1.5em;
  padding: 20px;
  padding-top: 0;
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
  position: sticky;
  position: -webkit-sticky;
  top: 80px;
  width: 100vw;

}

/* Create post section button*/
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
.mainFormStyling {
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

.mainFormStyling input {
  border: rgba(25, 134, 163, 1) solid 2px;
  border-radius: 5px;
  margin: 20px 0 20px 0px;
  padding: 8px 20px 8px 20px;
  width: 80%;
}

/* form button styling */
.red {
  background-color: rgba(255, 90, 90, 1);
}

/* button styling */
.black {
  background-color: rgb(35, 35, 35);
}

.blue {
  background-color: rgba(25, 134, 163, 1)
}

.formBtn {
  color: white;
  border-radius: 5px;
  margin: 20px 0 20px 0px;
  padding: 8px 10px 8px 10px;
  width: 30%;
  border: none;
  font-size: 0.8rem;
}

.formBtnFlex {
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  width: 100%;
  margin: 0;
  padding: 0;
}

/* button styling end*/

#greyedOut {
  color: rgb(182, 182, 182);
}

/* align items to center class*/
.flexCenter {
  display: flex;
  flex-direction: row;
  justify-content: center;
  margin: 0;
  padding: 0;
  padding-top: 25px;
}

/* Mobil view only changes start here */
@media screen and (max-width: 900px) {

  ul {
    display: grid;
    grid-template-columns: 1fr;
    padding: 0%;
  }

  li {
    border-radius: 0px;
  }

  li img {
    width: 100%;
    height: 48vw;
    object-fit: cover;
    padding-top: 0;
  }

  .formBtn {
    width: 40%;
  }
/* Mobil view only changes start here */

}
</style>