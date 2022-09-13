<!--post component-->


<!-- <template> -->
 <!-- create new post start -->
 <!-- <div v-if="formClicked" id="updateInfo">
        <h3>Create new post</h3>

        <input type="text" placeholder="Title" v-model="formValues.title">
        <input type="text" placeholder="ImageUrl" v-model="formValues.imageUrl">
        <input type="text" placeholder="Location" v-model="formValues.location">
        <div class="formBtnFlex">
        <button @click="insertDoc" class="formBtn blue "> Post </button>
        <button @click="formClicked = false" class="formBtn black"> Cancel </button>
        </div>

      </div>    -->
  <!-- end of create new post ection -->

<!-- </template>

<script >
    export default{
        props: {
            editState: Boolean,
            formClicked: Boolean,
            formValues: Object
        },
        methods: {
        insertDoc(){
            this.$emit("call-insertDoc")
           
        }
    }
    } 

   
</script>