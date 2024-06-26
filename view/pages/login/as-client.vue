<template>
  <div class="gradient-bg h-full w-full">
    <div class="flex items-center justify-center h-screen">
      <Form @submit="handleLogin()" class="h-auto lg:w-1/3 md:w-2/4 w-3/4 gradient-bg duration-200 rounded shadow-lg shadow-black p-10 long-text tracking-wide" >
        <div class="flex items-center gap-1 mb-5 justify-center">
          <Icon icon="material-symbols:shield-person" class="w-auto h-10" />
          <h1 class="cursor-default headlines text-2xl">Authentication</h1>
        </div>
  
        <hr class="mb-5">
        <section class="mb-5">
          <div class="flex gap-1 items-center">
            <Icon icon="material-symbols:person" class="w-auto h-5" />
            <label for="username" class="text-base">Username</label>
          </div>
          <Field type="text" name="username" placeholder="Enter your username" class="w-full bg-[#cbd2d7] focus:bg-[#e5e8eb] py-2 px-3 rounded border border-black transition-all duration-300 outline-none focus:border-white text-black mt-1"
            :rules="validateUserName"
            v-model="state.user.userName"
          />
          <ErrorMessage class="text-red-500 text-xs italic" name="username"/>
        </section>
  
        <section>
          <div class="flex justify-between">
            <div class="flex items-center gap-1">
              <Icon icon="solar:lock-password-unlocked-bold" class="w-auto h-5" />
              <label class="text-base">Password</label>
            </div>
            <div class="flex gap-1 items-center w-auto">
              <nuxt-link to="/">
                <span class="hover:opacity-50 flex gap-1 items-center">
                  <Icon icon="tabler:lock-question" class="w-auto h-3" />
                  <span class="text-xs">Forgot password?</span>
                </span>
              </nuxt-link>
            </div>
          </div>
  
          <span class="flex items-center relative">
            <Field :type="passwordFieldType" @focus="isInputFocused = true" @blur="isInputFocused = false" ref="passwordField" name="password" placeholder="Enter Password"
            class="w-full bg-[#cbd2d7] focus:bg-[#e5e8eb] py-2 px-3 pr-12 rounded border border-black transition-all duration-300 outline-none focus:border-white text-black mb-2 mt-1"
            :rules="validatePassword"
            v-model="state.user.password"
            />
            <span class="absolute right-0 cursor-pointer mr-3 text-black hover:bg-[#758e92] hover:text-[#b8c5c7] p-1 rounded-full duration-150">
              <Icon @click="showPassword" class="w-auto h-5" icon="ic:outline-remove-red-eye" />
            </span>
          </span>
          <ErrorMessage class="text-red-500 text-xs italic" name="password"/>
        </section>
  
          <button type="submit" class="btn-form bg-[#3e5566]">Login</button>
  
        <section class="mt-7">
          <div class="flex gap-2 items-center">
            <hr class="w-full">
            <p class="text-xs">OR</p>
            <hr class="w-full">
          </div>
        </section>
  
        <section class="w-full">
          <nuxt-link to="/login/as-admin">
            <button class="btn-form bg-[#081a28] flex gap-2 items-center justify-center">
              <Icon icon="material-symbols-light:admin-panel-settings-outline-rounded" class="w-auto h-7" />
              <p class="text-center">Continue as Admin</p>
            </button>
          </nuxt-link>
        </section>
  
      </Form>
    </div>
  </div>
</template>

<script setup>

//LUIS, KENT
import { ref, reactive } from 'vue'; 
import { Icon } from '@iconify/vue';
import { Form, Field, ErrorMessage } from 'vee-validate';

//REY
const state = reactive({
  user: {
    userName: null,
    password: null,
  }
});

//KENT
const passwordFieldType = ref('password');
const isInputFocused = ref(false);
let passwordInput = null;


//LUIS
const validateUserName = (value) => {
  if (!value) {
    return 'This field is required';
  }

  const minLength = 3; 
  const maxLength = 20; 
  const allowedCharacters = /^[a-zA-Z0-9_-]+$/; 
  const startsWithLetter = /^[a-zA-Z]/; 
  
  if (value.length < minLength || value.length > maxLength) {
    return `Username must be between ${minLength} and ${maxLength} characters`;
  }
  
  if (!allowedCharacters.test(value)) {
    return 'Username can only contain letters, digits, underscores, and hyphens';
  }
  
  if (!startsWithLetter.test(value)) {
    return 'Username must start with a letter';
  }
  
  return true;
};

const validatePassword = (value) => {
  if (!value) {
    return 'This field is required';
  }
  return true;
};


//KENT
const showPassword = () => {
  passwordFieldType.value = passwordFieldType.value === 'password' ? 'text' : 'password';
  if (passwordInput) {
    passwordInput.focus();
  }
};


//REY, LUIS
async function handleLogin() {
  const params = {
    userName: state.user.userName,
    password: state.user.password,
  }
  try {
  const response = await $fetch(`http://127.0.0.1:8000/api/auth/login-client`, {
      method: 'POST',
      body: JSON.stringify(params)
  });

  if (response.data) {
      localStorage.setItem('_token', response.data.token);
      navigateTo('/client');
  }
  } catch (error) {
  state.errors = error.response;
  console.log(state.errors && state.errors._data && state.errors._data.errors && state.errors._data.errors.userName && state.errors._data.errors.userName[0]);
  navigateTo('/error-page');
  }
    }
</script>
