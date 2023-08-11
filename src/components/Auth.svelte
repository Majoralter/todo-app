<script>
  import { auth } from "../lib/firebase/firebase";
  import { authHandlers } from "../store/store";

    let email = '',
        password = '',
        confirmPassword = '',
        error = false,
        register = false,
        authenticating = false

    const handleAuth = async () =>{
        if(authenticating) return

        if(!email || !password || (register && !confirmPassword)){
            error = true
            return
        }

        try {
            if(!register){
          await authHandlers.login(email, password)
            } else{
                await authHandlers.signUp(email, password)
            }
        } catch (error) {
            console.log(error.message)
            error = true
            authenticating = false
        }
        authenticating = true
    }

    const handleRegister = () =>{
        register = !register
    }

</script>

<div class="authContainer">
    <form action="">
        <h1>{register ? "Register" : "Login"}</h1>
        {#if error}
        <p class="error">The information you have entered is not correct</p>
        {/if}
        <label>
            <p class={email ? 'above' : 'center'}>Email</p>
            <input type="email" placeholder="Email" bind:value={email}>
        </label>
        <label>
            <p class={password ? 'above' : 'center'}>
                {#if password.length < 6}
                    Password must be at least six characters
                    {:else}
                    Password
                {/if}
            </p>
            <input type="password" placeholder="Password" bind:value={password}>
        </label>
        {#if register}
        <label>
            <p class={confirmPassword ? 'above' : 'center'}>Confirm Password</p>
            <input type="password" placeholder="Confirm Password" bind:value={confirmPassword}>
        </label>
        {/if}

        <button type="button" on:click={handleAuth}>
               {#if authenticating}
                <span class="material-symbols-outlined">progress_activity</span>

                {:else if !register && !authenticating}
                    Login
                {:else if register && !authenticating}
                    Register    
               {/if}
        </button>
    </form>

    <div class="options">
        <p>Or</p>
        {#if register}
            <div>
                <p>Already have an account?</p>
                <button on:click={handleRegister} on:keydown={() => {}}>Login</button>
            </div>

            {:else}
            <div>
                <p>Don't have an account?</p>
                <button on:click={handleRegister} on:keydown={() => {}}>Register</button>
            </div>
        {/if}
    </div>
</div>

<style lang="scss">
    .authContainer{
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        flex: 1;
        padding: 24px;

        form,.options{
            width: 400px;
            max-width: 100%;
            margin-inline: auto;
        }

        form{
            display: flex;
            flex-direction: column;
            gap: 14px;

            label{
                position: relative;
                border: 1px solid navy;
                border-radius: 5px;

                input{
                width: 100%;
                border: none;
                background: transparent;
                color: #fff;
                padding: 14px;

                &:focus{
                    border: none;
                    outline: none;
                }
                }

                &:focus-within{
                    border-color: blue;
                }
            }
         

            h1{
                text-align: center;
                font-size: 3rem;
            }

            button{
                padding: 12px 24px;
                border: none;
                background-color: blue;
                color: #fff;
                border-radius: 5px;
                cursor: pointer;
                font-size: 1rem;
                display: grid;
                place-items: center;

                &:hover{
                    background-color: navy;
                }


                span{
                    animation: spin 2s linear infinite;
                }

                @keyframes spin {
                    from{
                        transform: rotate(0deg);
                    }
                    to{
                        transform: rotate(360deg);
                    }
                }
            }

            .above,.center{
                position: absolute;
                transform: translateY(-50%);
                pointer-events: none;
                color: white;
                border-radius: 4px;
                padding-inline: 6px;
                font-size: 0.8rem;
            }

            .above{
                top: 0;
                left: 24px;
                background-color: navy;
                border: 1px solid blue;
                font-size: 0.7rem;
            }

            .center{
                top: 50%;
                left: 6px;
                border: 1px solid transparent;
                opacity: 0;
            }

            .error{
                color: coral;
                font-size: 0.9rem;
                text-align: center;
            }
        }


        .options{
            padding-block: 14px;
            overflow: hidden;
            font-size: .9rem;
            display: flex;
            flex-direction: column;
            gap: 4px;

             > p{
                position: relative;
                text-align: center;
                width: fit-content;
                margin-inline: auto;
                padding-inline: 8px;
                color: #aaa;

                &::before,&::after{
                    content: '';
                    position: absolute;
                    top: 50%;
                    transform: translateY(-50%);
                    width: 100vw;
                    height: 1px;
                    background: #aaa;
                }

                &::after{
                    right: 100%;
                }

                &::before{
                    left: 100%;
                }
            }

            div{
                display: flex;
                align-items: center;
                justify-content: center;
                gap: 8px;

                button{
                    border: none;
                    background: transparent;
                    color: cyan;
                    cursor: pointer;
                }
            }
        }
    }
</style>