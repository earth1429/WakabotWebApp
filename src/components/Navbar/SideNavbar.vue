<script setup>
import ImageTableCompVue from "../Comps/ImageTableComp.vue";
import ManualDetailsCompVue from "../Comps/ManualDetailsComp.vue";
import { onMounted, ref } from "vue";
import { getAuth, onAuthStateChanged, signOut } from "firebase/auth";
import router from "@/router";
import { getFirestore, collection, addDoc, Timestamp } from "firebase/firestore"; 

const isLoggedIn = ref(false);
const email = ref("");
const auth = getAuth();
const db = getFirestore();

onMounted(() => {
    onAuthStateChanged(auth, async (user) => {
        if (user) {
            isLoggedIn.value = true;
            email.value = auth.currentUser.email

        } else {
            isLoggedIn.value = false;
            email.value = ""
        }
    });
});
const handleSignOut = () => {
    console.log(auth.currentUser)
    signOut(auth).then(() => {
        router.push("/home");
    });
};
const testAdding = async () => {
    if(isLoggedIn.value){
        try {
            const docRef = await addDoc(collection(db, `users/${auth.currentUser.uid}/images`), {
                path:`Image/Human/${Math.floor(Math.random()*5+1)}.jpg`,
                time:Timestamp.fromDate(new Date()),
                case: false,
            });
            console.log("Document written with ID: ", docRef.id);
        } catch (e) {
            console.error("Error adding document: ", e);
        }
    }
};
// window.addEventListener('beforeunload', function(event) {
//     event.returnValue = auth=getAuth()
// })
</script>
<script>
export default {
    data: function() {
        return {
            currentUrl: "",
        };
    },
    created() {
        const arr=(window.location.href).split('/');
        this.currentUrl=arr[arr.length-1];
    },
    methods : {
        setUrl: function(value){
            this.currentUrl=value;
        }
    },
}
</script>
<template>
    <div class="container-fluid">
        <div class="row flex-nowrap">
            <div class="col-auto col-md-3 col-xl-2 px-sm-2 px-0 bg-black">
                <div class="d-flex flex-column align-items-center align-items-sm-start px-4 pt-4 text-white min-vh-100">
                    <a href="/" class="d-flex align-items-center pb-3 mb-md-0 me-md-auto text-white text-decoration-none">
                        <span class="fs-5 d-none d-sm-inline">WakaBot</span>
                    </a>
                    <ul class="nav nav-pills flex-column mb-sm-auto mb-0 align-items-center align-items-sm-start" id="menu">
                        <div class="px-4">
                            <li class="nav-item">
                                <a href="/home" class="nav-link align-middle px-0 tc-grey">
                                    <i class="fs-4 bi-house"></i> <span class="ms-1 d-none d-sm-inline">Home</span>
                                </a>
                                <a href="#Manual" @click="setUrl('feature#Manual')" v-bind:class="currentUrl=='feature#Manual'?'active':''" class="nav-link align-middle px-0 tc-grey">
                                    <i class="fs-4 bi-book"></i> <span class="ms-1 d-none d-sm-inline">Manual</span>
                                </a>
                            </li>
                            <li>
                                <a href="#Image" @click="setUrl('feature#Image')" v-bind:class="currentUrl=='feature#Image'?'active':''" class="nav-link align-middle px-0 tc-grey">
                                    <i class="fs-4 bi-card-image"></i> <span class="ms-1 d-none d-sm-inline">Image</span>
                                </a>
                            </li>
                        </div>
                    </ul>
                    <hr>
                    <div class="dropdown pb-4 miniFooter">
                        <a href="#" class="d-flex align-items-center text-white text-decoration-none dropdown-toggle" id="dropdownUser1" data-bs-toggle="dropdown" aria-expanded="false">
                            <img src="../../assets/img/member1.png" alt="hugenerd" width="30" height="30" class="rounded-circle">
                            <span class="d-none d-sm-inline mx-1">{{ email }}</span>
                        </a>
                        <ul class="dropdown-menu dropdown-menu-dark text-small shadow">
                            <li>
                                <a @click="handleSignOut" v-if="isLoggedIn" class="dropdown-item" href="#">Sign out</a>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
            <div class="col p-3">
                <div id="Image" v-bind:class="currentUrl=='feature#Image'?'':'hidden'">
                    <h1>Image DataTable</h1>
                    <ImageTableCompVue/>
                    <button @click="testAdding()" class="hidden">ADDing</button>
                </div>
                <br>
                <div id="Manual" v-bind:class="currentUrl=='feature#Manual'?'':'hidden'">
                    <ManualDetailsCompVue/>
                </div>
            </div>
        </div>
    </div>
</template>
<style>
@import "../../assets/scss/app.scss";
</style>