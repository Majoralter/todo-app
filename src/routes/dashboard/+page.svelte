<script>
import { fade } from "svelte/transition"
import { authHandlers, authStore } from "../../store/store";
import { doc, setDoc } from "firebase/firestore";
import { db } from "$lib/firebase/firebase"

    let todoList = [],
        currTodo = "",
        error = false


    authStore.subscribe(curr =>  todoList = curr.data.todos)    

    const addTodo = () =>{
        error = false
        if(!currTodo) error = true
        todoList = [...todoList, currTodo]

        currTodo = ""
    }
    
    const editTodo = (index) =>{
        let newTodoList = [...todoList].filter((val, i) => {
            return i !== index
        })

        currTodo = todoList[index]

        todoList = newTodoList
    }

    const deleteTodo = (index) =>{
        let newTodoList = [...todoList].filter((val, i) => {
            return i != index
        })

        todoList = newTodoList
    }

    const saveTodos = async () =>{
        try {
            const userRef = doc(db, 'users', $authStore.user.uid)
            await setDoc(userRef, {
                    todos: todoList,
            },
                { merge: true }
            )
        } catch (error) {
            console.log(error.message)
        }
    }
</script>

{#if !$authStore.loading}
<div class="mainContainer">
    <div class="headerContainer">
        <h1>Todo List</h1>
        <div class="headerBtns">
        <button on:click={saveTodos}><span class="material-symbols-outlined">save</span> <p>Save</p></button>
        <button on:click={authHandlers.logout}><span class="material-symbols-outlined">logout</span><p>Logout</p></button>
    </div>
    </div>

    <main>
        {#if todoList.length === 0}
            <p in:fade>You have nothing to do! 🕺🏾💃🏾</p>
        {/if}
        {#each todoList as todo, i}
        <div class="todo" in:fade>
            <p>
            {i + 1}. {todo}
            </p>

            <div class="actions">
                <span class="material-symbols-outlined" on:click={() => editTodo(i)} on:keydown={() =>{}} role="button" tabindex="0">edit</span>
                <span class="material-symbols-outlined" on:click={() => deleteTodo(i)} on:keydown={() =>{}} role="button" tabindex="0">delete</span>
            </div>
        </div>
        {/each}
    </main>
        <div class={"enterTodo " + (error ? "errorBorder" : "")}>
            <input type="text" placeholder="Enter todo" bind:value={currTodo}>
            <button on:click={addTodo}><span class="material-symbols-outlined">add</span> <p>Add</p></button>
        </div>
</div>
{/if}

<style lang="scss">
    .mainContainer{
        display: flex;
        flex-direction: column;
        min-height: 100vh;
        gap: 24px;
        padding: 24px;
        width: 100%;
        max-width: 1200px;
        margin-inline: auto;

        .headerContainer{
            display: flex;
            align-items: center;
            justify-content: space-between;

            .headerBtns{
                display: flex;
                align-items: center;
                gap: 14px;

                button{
                background-color: #003c5b;
                color: white;
                padding: 10px 18px;
                border: none;
                border-radius: 4px;
                font-weight: 600;
                display: flex;
                align-items: center;
                gap: 10px;
                cursor: pointer;

                &:hover{
                    opacity: 0.7;
                }

                span{
                    font-size: 1.1rem;
                }
                }
            }
        }

        main{
        display: flex;
        flex-direction: column;
        gap: 8px;
        flex: 1;

        .todo{
            border-left: 1px solid cyan;
            padding: 8px 14px;
            display: flex;
            align-items: center;
            justify-content: space-between;

            .actions{
                display: flex;
                align-items: center;
                gap: 14px;
                font-size: 1.3rem;

                span{
                    cursor: pointer;
                    &:hover{
                        color: coral;
                    }
                }
            }
        }
    }

        .errorBorder{
            border-color: coral !important;
            }

        .enterTodo{
            display: flex;
            align-items: stretch;
            border: 1px solid #0891b2;
            border-radius: 5px;
            overflow: hidden;

            input{
                background-color: transparent;
                border: none;
                padding: 14px;
                color: white;
                flex: 1;

                &:focus{
                    outline: none;
                }
            }

            button{
                padding-inline: 28px;
                background: #003c5b;
                border: none;
                color: cyan;
                font-weight: 600;
                cursor: pointer;
                display: flex;
                align-items: center;
                gap: 5px;

                &:hover{
                    background: transparent;
                }
            }
        }
    }
</style>