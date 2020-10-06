<script>
    import TransitionWrapper from '../components/TransitionWrapper.svelte';
    import BackgroundBlock from '../components/background.svelte';
    import { onMount } from 'svelte';
    import jq from 'jquery';
        
        function sendData(){
        window.jq = jq;
        var userLogin = document.querySelector('#login').value;
        var userPassword = document.querySelector('#password').value;
        console.log(userPassword);
        
        let data = {
                login: userLogin,
                password: userPassword,
                type: 'login'
            };
            console.log(data);
            jq.ajax({type: 'POST', crossDomain: true, url: 'http://localhost:5000', data: JSON.stringify(data)}).done(  function(res) {
            console.log(res); //SUCCESS EVENT
            document.cookie = 'SESSION_ID='+res['SESSION_ID'];
            window.location.href = "http://localhost:3000";
            }).fail(function(){
                document.querySelector('#error-msg-block').innerHTML = "Неверный логин или пароль"; //ERROR MSG
            });
        }

        function checkPassword(regPassword) {
            var repeatedRegPassword = document.querySelector("#reg-password-repeat").value;
            var answer = (regPassword == repeatedRegPassword);
            return answer;
        }

        function registration() {
            let regPassword = document.querySelector("#reg-password").value; 
            if (checkPassword(regPassword)) {
            let regLogin = document.querySelector("#reg-login").value;
            let data = {
                login: regLogin,
                password: regPassword,
                type: 'register'
            }
            var request = jq.ajax({type: 'POST',  url: 'http://localhost:5000', data: JSON.stringify(data)});
            request.done(  function(res) {
            console.log(res); //SUCCESS EVENT
            //пользователь зарегестрирован
            });
            request.fail(function(jqXHR, textStatus, errorThrown){
                document.querySelector('#reg-error-msg-block').innerHTML = "123"; //ERROR MSG
            });
            } else {
                document.querySelector('#reg-error-msg-block').innerHTML = "Пароли не совпадают";
            }
        }

       function changeForm() {
           document.querySelector("#auth-form").style.zIndex = "-1000";
           document.querySelector("#reg-form").style.zIndex = "1000"
       }

       function changeForm2() {
           document.querySelector("#reg-form").style.zIndex = "-1000";
           document.querySelector("#auth-form").style.zIndex = "1000"
       }
</script>

<style>
    #auth-wrapper {
        width: 100%;
        height: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    .forms {
        display: flex;
        justify-content: center;
        align-items: center;
        text-align: center;
        width: 300px;
        height: 250px;
        background-color: rgba(255, 255, 255, 0.3);
        z-index: 1000;
        border-radius: 10px;
        margin-bottom: 45%;
    }

    .form-content {
        width: 100%;
        height: 100%;
        display: flex;
        flex-direction: column;
    }
    input {
        margin: 5px;
        border-radius: 5px;
        border: 1px solid black;
        font-size: 16px;
        height: 24px;
    }

    #auth-btn {
        height: 28px;
        margin-bottom: 10px;
    }

    #reg-btn {
        height: 28px;
        margin-bottom: 10px;
    }

    .direct-btn {
        background-color: rgba(0, 0, 0, 0.1);
        border: 0px;
        cursor: pointer;
    }

    #error-msg-block {
        width: 100%;
        position: absolute;
        left: 0;
        top: 2%;
        color: red;
    }

    #reg-error-msg-block {
        width: 100%;
        position: absolute;
        left: 0;
        top: 2%;
        color: red;
    }

    #reg-form {
        position: absolute;
        top: 21%;
        z-index: -1000;
    }

    #auth-form {
        position: absolute;
        top: 21%;
        z-index: 1000;
    }
</style>

<BackgroundBlock></BackgroundBlock>

<div id="auth-wrapper">
    <form id = "auth-form" class="forms" method="POST">
        <div>
            <div class = "form-content">
                <input id="login" name="login" placeholder="Логин" value/>
                <input id="password" name="password" placeholder="Пароль" value/>
                <input id="auth-btn" type="button" on:click={sendData} value="Войти">
                У Вас нет аккаунта? <input id="btn-to-reg" class="direct-btn" type="button" on:click={changeForm} value="Создать аккаунт">
                <div id="error-msg-block"></div>
            </div>
        </div>
    </form>

    <form id = "reg-form" class="forms" method="POST">
    <div>
        <div class = "form-content">
            <input id="reg-login" name="login" placeholder="Логин" value/>
            <input id="reg-password" minlength="4" maxlength="16" name="password" type="password" placeholder="Пароль" value/>
            <input id="reg-password-repeat" name="password-repeat" type="password" placeholder="Повторите пароль" value/>
            <input id="reg-btn" type="button" on:click={registration} value="Зарегестрироваться">
            <input id="btn-to-log" class="direct-btn" type="button" on:click={changeForm2} value="Назад">
            <div id="reg-error-msg-block"></div>
        </div>
    </div>
    </form>
</div>
