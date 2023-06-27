<script>
import {computed, defineComponent} from 'vue'
import {vMaska} from "maska"
import {useStore} from "vuex";
import 'fa-icons';
import {store} from "core-js/internals/reflect-metadata";


export default {
    directives: {
        maska: vMaska
    },
    data: function () {
        return {
            name: "",
            surname: "",
            email: "",
            mobilePhone: "",
            password: "",
            repeatPassword: "",
            nameError: "",
            surnameError: "",
            emailError: "",
            phoneError: "",
            passwordError: "",
            secondPasswordError: "",
            error: "",
            result: "",
            legacy: "",
            legacyError: "",
            showPassword: false
        }
    },

    methods: {
        onRegister: function () {
            let validName = this.name.trim();
            let validSurname = this.surname.trim();
            if (!validName) {
                this.nameError = "Поле должно быть заполнено";
            } else if (!/^[а-яА-Я][а-я]+$/.test(this.name)) {
                this.nameError = "Имя может содержать только кириллицу без пробелов";
            } else if (validName.length < 2 || validName.length > 22) {
                this.nameError = "Имя должно содержать от 2 до 22 букв";
            } else {
                this.nameError = "";
            }

            if (!validSurname) {
                this.surnameError = "Поле должно быть заполнено";
            } else if (!/^[а-яА-Я][а-я]+$/.test(this.surname)) {
                this.surnameError = "Фамилия может содержать только кириллицу без пробелов";
            } else if (validSurname.length < 2 || validSurname.length > 22) {
                this.surnameError = "Фамилия должно содержать от 2 до 22 букв";
            } else {
                this.surnameError = "";
            }

            if (!this.mobilePhone) {
                this.phoneError = "Поле должно быть заполнено";
            } else if (!/^\+7 \([0-9]{3}\) [0-9]{3}-[0-9]{2}-[0-9]{2}/.test(this.mobilePhone)) {
                this.phoneError = "Формат номера +7 (999) 999-99-99";
            } else {
                this.phoneError = ""
            }

            if (!this.email) {
                this.emailError = "Поле должно быть заполнено";
            } else if (!/^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(this.email)) {
                this.emailError = "Email неверен";
            } else {
                this.emailError = ""
            }

            if (!this.password) {
                this.passwordError = "Поле должно быть заполнено";
                // this.password = "";
                // this.repeatPassword = "";
            } else if (!/^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{8,}$/.test(this.password)) {
                this.passwordError = "Пароль должен содержать минимум 8 символов, минимум 1 букву и 1 цифру";
                // this.password = "";
            } else {
                this.passwordError = ""
            }

            if (!this.repeatPassword) {
                this.secondPasswordError = "Повторите пароль";
                // this.password = "";
                // this.repeatPassword = "";
            } else if (this.repeatPassword !== this.password && this.passwordError === "") {
                this.secondPasswordError = "Пароли не совпадают"
                // this.password = "";
                // this.repeatPassword = "";
            } else {
                this.secondPasswordError = ""
            }

            if (!this.legacy) {
                this.legacyError = "Отметьте галочку";
            } else {
                this.legacyError = "";
            }

            if (this.nameError === "" &&
                this.surnameError === "" &&
                this.emailError === "" &&
                this.phoneError === "" &&
                this.passwordError === "" &&
                this.secondPasswordError === "" &&
                this.legacyError === "") {
                this.postRegister();
            }

        },
        toggleShow() {
            this.showPassword = !this.showPassword;
        },
        postRegister() {
            fetch( "http://5.23.49.8:8080/api/v1/user/register", {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({

                    name: this.name,
                    surname: this.surname,
                    phone: this.mobilePhone,
                    email: this.email,
                    password: this.password

                })
            }).then(response => response.json())
                .then(data => {
                    if (data["user"]) {
                        this.emitter.emit('onLogin', data.user)
                        this.$router.push('index');
                    } else if (data["message"]) {
                        this.error = data["message"];
                    }
                })
                .catch(error => this.error = error);
        }
    },
    beforeCreate() {
        this.emitter.on("onRegisterValidationError", (error) => this.error = error);
        this.emitter.emit('clearActive');
    }
}

</script>

<template>
    <div class="enter">
        <div class="form">
            <div class="header">
                Зарегистриоваться
            </div>
            <form @submit.prevent="onRegister">
                <div class="data">
                    <div class="field">
                        <div class="name">
                            Имя
                        </div>
                        <div class="place-holder">
                            <input type="text" name="name" v-model="name"/>
                        </div>
                    </div>
                    <div class="field error">{{ nameError }}</div>
                    <div class="field">
                        <div class="name">
                            Фамилия
                        </div>
                        <div class="place-holder">
                            <input type="text" v-model="surname"/>
                        </div>
                    </div>
                    <div class="field error">{{ surnameError }}</div>
                    <div class="field">
                        <div class="name">
                            Email
                        </div>
                        <div class="place-holder">
                            <input type="text" v-model="email"/>
                        </div>
                    </div>
                    <div class="field error">{{ emailError }}</div>
                    <div class="field">
                        <div class="name">
                            Мобильный телефон
                        </div>
                        <div class="place-holder">
                            <input type="text" v-maska data-maska="+7 (###) ###-##-##" v-model="mobilePhone"/>
                        </div>
                    </div>
                    <div class="field error">{{ phoneError }}</div>
                    <div class="field">
                        <div class="name">
                            Пароль
                        </div>
                        <div class="place-holder">
                            <input v-if="showPassword" type="text" v-model="password">
                            <input v-else type="password" v-model="password"/>
                            <svg @click.prevent="toggleShow" fill="#000000" height="64px" width="64px" version="1.1"
                                 xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512"
                                 xmlns:xlink="http://www.w3.org/1999/xlink" enable-background="new 0 0 512 512">
                                <g>
                                    <path d="m494.8,241.4l-50.6-49.4c-50.1-48.9-116.9-75.8-188.2-75.8s-138.1,26.9-188.2,75.8l-50.6,49.4c-11.3,12.3-4.3,25.4 0,29.2l50.6,49.4c50.1,48.9 116.9,75.8 188.2,75.8s138.1-26.9 188.2-75.8l50.6-49.4c4-3.8 11.7-16.4 0-29.2zm-238.8,84.4c-38.5,0-69.8-31.3-69.8-69.8 0-38.5 31.3-69.8 69.8-69.8 38.5,0 69.8,31.3 69.8,69.8 0,38.5-31.3,69.8-69.8,69.8zm-195.3-69.8l35.7-34.8c27-26.4 59.8-45.2 95.7-55.4-28.2,20.1-46.6,53-46.6,90.1 0,37.1 18.4,70.1 46.6,90.1-35.9-10.2-68.7-29-95.7-55.3l-35.7-34.7zm355,34.8c-27,26.3-59.8,45.1-95.7,55.3 28.2-20.1 46.6-53 46.6-90.1 0-37.2-18.4-70.1-46.6-90.1 35.9,10.2 68.7,29 95.7,55.4l35.6,34.8-35.6,34.7z"/>
                                </g>
                            </svg>
                        </div>
                    </div>
                    <div class="field error">{{ passwordError }}</div>
                    <div class="field">
                        <div class="name">
                            Повторите пароль
                        </div>
                        <div class="place-holder">
                            <input type="password" v-model="repeatPassword"/>
                        </div>
                    </div>
                    <div class="field error">{{ secondPasswordError }}</div>
                    <div class="field" id="checkbox">
                        <input type="checkbox" v-model="legacy"/>
                        <div>
                            <p>Я даю своё согласие на обработку моей персональной информации на условиях, определенных
                                <router-link :to="{ name: 'legal-rules'}">Политикой в отношении обработки персональных
                                    данных</router-link>.</p>
                        </div>
                    </div>
                    <div class="field error">{{ legacyError }}</div>
                </div>
                <div class="field error">{{ error }}</div>
                <div class="send">
                    <input class="b-btn" type="submit" value="Регистрация">
                </div>
            </form>
        </div>
    </div>
</template>

<style scoped>
#checkbox {
    display: flex;
}

#checkbox input {
    margin-right: 1rem;
}

#checkbox a {
    color: #ed2b63;
    text-decoration: none;
}

.eye-password {
    width: 5%;
}
</style>