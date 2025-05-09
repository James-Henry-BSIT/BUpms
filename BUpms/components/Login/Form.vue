<template>
  <div class="bg-slate-200 relative grid place-items-center">
    <div class="form-container px-2 space-y-3 text-slate-700">
      <div class="bu-logo-container grid place-items-center">
        <img src="assets/BU_title.png" alt="" class="object-contain w-60 min-w-40 bu-assets">
      </div>
      <form class="input-fields-container space-y-3">
        <div class="bu-email">
          <input autofocus required type="email" placeholder="EMAIL" v-model="_email"
            class="border-2 rounded-md w-72 p-2 tracking-widest placeholder-shown:font-semibold bg-slate-50">
        </div>
        <div class="bu-password relative flex ">
          <input autofocus required :type="isClicked ? 'text' : 'password'" placeholder="PASSWORD"
            class="w-72 tracking-widest border-2 rounded-md p-2 placeholder-shown:font-semibold bg-slate-50"
            v-model="_password">
          <span @click="eye_icon_enable" class="h-full py-1 absolute right-0 w-14 grid place-content-center">
            <IconsEyeEnable></IconsEyeEnable>
          </span>
          <span @click="eye_icon_enable" v-show="isClicked"
            class="h-full py-1 absolute right-0 w-14  grid place-content-center">
            <IconsEyeDisable></IconsEyeDisable>
          </span>
        </div>
        <div class="error-handling relative grid place-content-center w-72">
          <p class="text-center uppercase font-bold text-red-800 tracking-wider">
            {{ error }}
          </p>
        </div>
        <div class="bu-btn-container grid gap-2 ">
          <button @click="loginFunc" type="submit" :disabled="isLoading"
            class="bg-sky-400 rounded-md disabled:bg-slate-300 p-2 w-full text-white text-base font-semibold tracking-widest hover:bg-sky-500">
            Sign in
          </button>
          <div class="flex justify-center gap-x-2">
            <hr class="border-b-2 border-slate-300 rounded-xl w-full mt-1.5">
            <div class="text-wrapper text-xs">
              OR
            </div>
            <hr class="border-b-2 border-slate-300 rounded-xl w-full mt-1.5">
          </div>
          <div class="google-function space-y-4">
            <div class="label-wrapper text-center uppercase font-mono text-xs tracking-wide md:tracking-widest">
              Log in using your account on:
            </div>
            <button @click="googleLogin"
              class="bg-slate-300 rounded-md p-2 w-full hover:bg-green-500 text-sky-700 hover:text-white">
              <div class="google-btn-container flex place-content-center gap-x-2">
                <div class="google-bg-wrapper bg-slate-50 rounded-full h-9 w-9 grid place-content-center">
                  <IconsGoogleIcon></IconsGoogleIcon>
                </div>
                <div class=" font-semibold text-base tracking-tighter flex pt-2">
                  Sign in with Google
                </div>
              </div>
            </button>
          </div>
        </div>
      </form>
    </div>
  </div>
</template>
<script lang="ts" setup>
import { z } from 'zod';

const loginSchema = z.object({
  email: z.string().email().endsWith("@bicol-u.edu.ph"),
  password: z.string().min(8)
});

const store = useMyAuthStoreStore();

const { $pb } = useNuxtApp();

const isClicked = ref(false);
const teleport_btn = ref(true);
const isLoading = ref(false);
const _email = ref("");
const _password = ref("");
const error = ref("");

const email = computed({
  get: () => _email.value,
  set: (newValue) => {
    _email.value = newValue;
    handleEmailChange(newValue);
  }
});

const eye_icon_enable = () => {
  isClicked.value = !isClicked.value;
};

const display_modal = () => {
  teleport_btn.value = !teleport_btn.value;
};

const handleEmailChange = (newValue: string) => {
  // console.log(`Email changed to ${newValue}`);
  // Perform any other actions here
};

const loginFunc = async () => {
  isLoading.value = !isLoading.value;
  try {
    const data = loginSchema.safeParse({ email: _email.value, password: _password.value });
    if (data.error) {
      error.value = data.error.flatten().fieldErrors.email?.[0] || data.error.flatten().fieldErrors.password?.[0] || '';
      const buttonDisable = setTimeout(() => {
        isLoading.value = !isLoading.value;
        error.value = '';
        _email.value = '';
        _password.value = '';
        clearTimeout(buttonDisable)
      }, 2000)
      return;
    }
    const { email, password } = data.data;
    const auth = await $pb.collection('Users_tbl').authWithPassword(email, password)
    if (auth) {
      store.setUser($pb.authStore.model)
      navigateTo($pb.authStore.model?.role === 'student' ? '/client' : $pb.authStore.model?.role === 'officer1' ? '/officer1/projects' : $pb.authStore.model?.role === 'admin' ? '/admin' : $pb.authStore.model?.role === 'officer2' ? '/officer2/projects' : '/');
    }
  } catch (e) {
    error.value = 'Account does not exist'
    const buttonDisable = setTimeout(() => {
      isLoading.value = !isLoading.value;
      error.value = '';
      _email.value = ''
      _password.value = ''
      clearTimeout(buttonDisable)
    }, 1000)
  }
  // isLoading.value = !isLoading.value;
};

const googleLogin = async () => {
  try {
    // pb.authStore.clear()
    const authData = await $pb.collection('Users_tbl').authWithOAuth2({ provider: 'google' })

    // google data
    const meta = authData.meta

    // check if new account
    if (meta.isNew) {
      // console.log(meta)
      const userName = meta.name
      const setLoggedInAcc = {
        // 'username': userName,
        'role': 'student'
      }

      // console.log(authData)
      await $pb.collection('Users_tbl').update(authData.record.id, setLoggedInAcc)
      // by default can only be used on client 
      navigateTo('/client')
    } else { // redirect to landing page
      if ($pb.authStore.model?.role === 'student') {
        navigateTo('/client')
      } else if ($pb.authStore.model?.role === 'officer1') {
        navigateTo('/officer1/projects')
      } else if ($pb.authStore.model?.role === 'officer2') {
        navigateTo('/officer2/projects')
      }
      else if ($pb.authStore.model?.role === 'admin') {
        navigateTo('/admin')
      } else {
        navigateTo('/')
      }
    }
  } catch (e) {
    error.value = 'Your email must be Affiliated with bicol-university'
    const invalidEmail = setTimeout(() => {
      error.value = ''
      clearTimeout(invalidEmail)
    }, 2000)
  }
};

onMounted(() => {
  useHead({
    title: "BUpms"
  });
});
</script>