<template>
    <div class="navBar">
       <div class="searchContainer">
        <!-- <v-autocomplete
              :items="searchHistory"
              density="comfortable"
              menu-icon=""
              placeholder="Search..."
              prepend-inner-icon="mdi-magnify"
              :style="{height: '34px'}"
              theme="light"
              variant="solo-filled"
              auto-select-first
              item-props
          ></v-autocomplete> -->
       </div>
       <div class="taskNotificationContainer">
            <button @click="handleCreateBoard()">       
            <span class="material-symbols-outlined">add</span>
            New Board</button>
       </div>      
       <div class="themeToggler" @click="handleThemeToggle">
        <span class="material-symbols-sharp active" id="lightMode">light_mode</span>
        <span class="material-symbols-sharp" id="darmMode">dark_mode</span>
       </div>
       <img src="@/assets/notification.png" class="notificationIcon">
       <img src="@/assets/totalCustomers.png" class="profileImage">
       <v-overlay  v-model="isCreateBoard" class="align-center justify-center" contained>
           <CreateNewBoardView @closeOverlay="handleCloseOverlay"/>
        </v-overlay>
    </div>
</template>
<script>
import CreateNewBoardView from '@/components/CreateNewBoardView.vue'
import { ref } from 'vue'
export default {
    components: {
        CreateNewBoardView
    },
    props: ["isExpanded"],
    setup() {
        var searchHistory = ref(["Groceries", "Fruits", "Meat", "Meatlo", "Something nice here"])
        var isSideBarExpanded = ref(false)
        var isCreateBoard = ref(false)
        var isDarkMode = ref(false)
        return { searchHistory, isSideBarExpanded, isCreateBoard, isDarkMode } 
    }, 
    methods: {
        handleCloseOverlay() {
            console.log("is overlay closed")
            this.isCreateBoard = false 
        },
        handleCreateBoard() {
            // create a new board
            this.isCreateBoard = true 
        }, 
        handleThemeToggle() {
         this.handleToggle(true)
        }, 
        handleToggle(isInvertable) {
            document.body.classList.toggle("dark-theme-variables")
            let lightMode = document.getElementById("lightMode")
            let darkMode = document.getElementById("darmMode")

            if (this.isDarkMode) {
                lightMode.classList.add("active")
                darkMode.classList.remove("active")
            } else {
                lightMode.classList.remove("active")
                darkMode.classList.add("active")
            }
            if (isInvertable === true) {
                this.isDarkMode = !this.isDarkMode
                let cacheValue = this.isDarkMode ? "1" : "0"
                localStorage.setItem("isDarkMode", cacheValue)
            }
        }
    },
    watch: { 
        isExpanded: function(newVal, oldVal) {
            console.log('Prop changed isSideBarExpanwded: ', newVal)
            this.isSideBarExpanded = newVal
        }
    }, 
    mounted() {
    //    let isDark = localStorage.getItem("isDarkMode")
    //    if (isDark != null) {
    //       this.isDarkMode = isDark == "1"
    //       this.handleToggle(false)
    //    }
    },
}
</script>
<style scoped>
.profileImage {    
    height: 40px;
    width: 40px;
    border-radius: 50%;
    margin-top: auto;
    margin-bottom: auto;
    margin-right: 15px;
}
.themeToggler span.active {
    background-color: var(--color-bar-dark);
    color: white;
    border-radius: var(--border-radius-1);
}

.themeToggler span {
    height: 100%;
    width: 50%;
    padding-top: 8px;
}

.themeToggler {
    display: flex;
    margin-top: auto;
    margin-bottom: auto;
    margin-right: 20px;
    width: 100px;
    height: 38px;
    border-radius: var(--border-radius-1);
    background-color: var(--color-light);
    justify-content: space-between;
    align-items: center;
    justify-content: center;
}
.taskNotificationContainer button {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    color: white;
    padding-left: 15px;
    margin-top: auto;
    margin-bottom: auto;
    font-weight: 500;
}
.notificationIcon {
    width: 40px;
    height: 40px;
    border-radius: 4px;
    margin-top: auto;
    margin-bottom: auto;
    margin-right: 15px;
}
.searchContainer {
    width: calc(100% - 340px);
    height: 50px;
    padding-right: 400px;
    padding-left: 20px;

}
.taskNotificationContainer {
    width: 150px;
    height: 44px;
    display: flex;
    flex-direction: row;
    background-color: var(--color-bar-dark);
    border-radius: var(--border-radius-2);
    margin-right: 30px;
}

.searchContainer, .taskNotificationContainer {
    margin-top: auto;
    margin-bottom: auto;
}

.navBar {
  height: 58px;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  margin-right: auto;
  margin-left: auto;
  background-color: white;
}
</style>