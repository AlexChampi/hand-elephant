<script>
import {defineComponent} from 'vue'

export default {
    data: function () {
        return {
            login: "",
            password: "",
            error: "",
            showPassword: false
        }
    },
    methods: {

        toggleShow() {
            this.showPassword = !this.showPassword;
        },
        enter() {
            if (!this.login.trim() || !this.password.trim()) {
                this.error = "Заполните все поля";
            } else {
                this.error = ""
            }
            if (this.error === "") {
                fetch("http://5.23.49.8:8080/api/v1/user/enter", {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                        login: this.login,
                        password: this.password
                    })
                }).then(response => response.json())
                    .then(data => {
                        if (data["user"]) {
                            // this.$store.commit('login', JSON.stringify(data.user));
                            this.emitter.emit('onLogin', data.user)
                            this.$router.push('index');
                        } else if (data["message"]) {
                            this.error = data["message"];
                        }
                    })
                    .catch(error => this.error = error);
            }
        }
    },
    beforeCreate() {
        this.emitter.emit('clearActive');
    }
}
</script>

<template>
    <div class="enter">
        <div class="form">
            <div class="header">
                Вход
            </div>
            <form @submit.prevent="enter">
                <div class="data">
                    <div class="field">
                        <div class="name">Email</div>
                        <div class="place-holder">
                            <input type="text" id="login" name="login" v-model="login"/>
                        </div>
                    </div>
                    <div class="field">
                        <div class="name">Пароль</div>
                        <div class="place-holder">
                            <input v-if="showPassword" type="text" id="pass" name="pass" v-model="password"/>
                            <input v-else type="password" id="pass" name="pass" v-model="password"/>
                            <svg @click.prevent="toggleShow" fill="#000000" height="64px" width="64px" version="1.1"
                                 xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512"
                                 xmlns:xlink="http://www.w3.org/1999/xlink" enable-background="new 0 0 512 512">
                                <g>
                                    <path d="m494.8,241.4l-50.6-49.4c-50.1-48.9-116.9-75.8-188.2-75.8s-138.1,26.9-188.2,75.8l-50.6,49.4c-11.3,12.3-4.3,25.4 0,29.2l50.6,49.4c50.1,48.9 116.9,75.8 188.2,75.8s138.1-26.9 188.2-75.8l50.6-49.4c4-3.8 11.7-16.4 0-29.2zm-238.8,84.4c-38.5,0-69.8-31.3-69.8-69.8 0-38.5 31.3-69.8 69.8-69.8 38.5,0 69.8,31.3 69.8,69.8 0,38.5-31.3,69.8-69.8,69.8zm-195.3-69.8l35.7-34.8c27-26.4 59.8-45.2 95.7-55.4-28.2,20.1-46.6,53-46.6,90.1 0,37.1 18.4,70.1 46.6,90.1-35.9-10.2-68.7-29-95.7-55.3l-35.7-34.7zm355,34.8c-27,26.3-59.8,45.1-95.7,55.3 28.2-20.1 46.6-53 46.6-90.1 0-37.2-18.4-70.1-46.6-90.1 35.9,10.2 68.7,29 95.7,55.4l35.6,34.8-35.6,34.7z"/>
                                </g>
                            </svg>
                        </div>
                    </div>
                </div>
                <div class="field error">{{ error }}</div>
                <div class="send">
                    <input class="b-btn" type="submit" value="Войти">
                </div>
            </form>
        </div>
    </div>
</template>

<style scoped>

</style>