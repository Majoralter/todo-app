<script>
import "../app.scss";
import { base } from "$app/paths"

import { onMount } from "svelte";
import {auth, db} from "$lib/firebase/firebase"
import { getDoc, doc, setDoc } from "firebase/firestore";
import { authStore } from '../store/store.js'

const nonAuthRoutes = ["/"]

onMount(() =>{
    const unSubscribe = auth.onAuthStateChanged(async user =>{
        const currentPath = window.location.pathname

        if(!user && !nonAuthRoutes.includes(currentPath)){
            window.location.href = "/"
            return
        }

        if(user && currentPath === "/"){
            window.location.href = "{base}/dashboard"
            return
        }

        if(!user){
            return
        }

        let dataToSetToStore

        const docRef = doc(db, "users", user.uid),
              docSnap = await getDoc(docRef)  

        if(!docSnap.exists()){
            const userRef = doc(db, "users", user.uid)

            dataToSetToStore = {
                    email: user.email,
                    todos: []
                }

            await setDoc(
                userRef,
                dataToSetToStore,
                { merge: true }
            )
        }  else {
            const userData = docSnap.data()
            dataToSetToStore = userData
        }    

        authStore.update((curr) =>{
            return {
                ...curr,
                user,
                data: dataToSetToStore,
                loading: false
            }
        })
    })
})

</script>

<main>
    <slot />
</main>

<style lang="scss">
    main{
        min-height: 100vh;
        background: linear-gradient(to right, #000428, #000046);
        color: #fff;
        position: relative;
        display: flex;
        flex-direction: column;
    }
</style>