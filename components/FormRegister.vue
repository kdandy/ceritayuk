<script lang="ts" setup>
  import Swal, { SweetAlertOptions } from 'sweetalert2'

  const nameLabel: Ref = ref()
  const usernameLabel: Ref = ref()
  const emailLabel: Ref = ref()
  const passwordLabel: Ref = ref()

  const name: Ref<string> = ref('')
  const username: Ref<string> = ref('')
  const email: Ref<string> = ref('')
  const password: Ref<string> = ref('')

  watch([name, username, email, password], () => {
    const classLists: Array<string> = ['!-translate-y-6', '!border-black']
    const elLists: Array<{ el: Ref, label: Ref }> = [
      { el: name, label: nameLabel },
      { el: username, label: usernameLabel },
      { el: email, label: emailLabel },
      { el: password, label: passwordLabel },
    ]

    elLists.map(({ el,label }) => {
      if (el.value != '') classLists.forEach((item) => label?.value?.classList.add(item))
      else classLists.forEach((item) => label?.value?.classList.remove(item))
    })
  })

  const { register } = authStore()
  const { status, message } = storeToRefs(authStore())

  const isSubmited: Ref<boolean> = ref(false)
  const formRegist: Ref = ref()
  const onRegist = async (evt: any) => {
    evt.preventDefault()
    isSubmited.value = true
    const formData: FormData = new FormData(formRegist.value)

    await register(formData)

    if (!status.value) {
      const data: SweetAlertOptions = {
        title: 'Internal Server Error! Mohon coba beberapa saat lagi!',
        icon: 'error'
      }

      if (message.value.includes('empty')) data.title = 'Oopss! mohon isi semua data dengan benar!'
      else if (message.value.includes('"name" length')) data.title = 'Oopss! nama minimal harus terdiri dari 3 karakter!'
      else if (message.value.includes('"username" length')) data.title = 'Oopss! usernamemu terlalu pendek!'
      else if (message.value.includes('valid email')) data.title = 'Oopss! emailmu tidak valid!'
      else if (message.value.includes('password length')) data.title = 'Oopss! passwordmu terlalu pendek!'
      else if (message.value.includes('already used!')) data.title = 'Oopss! username atau email sudah terdaftar!'

      Swal.fire({
        title: data.title,
        icon: data.icon,
        customClass: 'drop-shadow-br !rounded-lgm'
      })
    }

    if (status.value) {
      Swal.fire({
        title: 'Registrasi berhasil! mohon segera verifikasi emailmu!',
        icon: 'success',
        customClass: 'drop-shadow-br !rounded-lgm'
      }).then((info) => {
        if (info.isConfirmed || info.dismiss) navigateTo('/login')
      })
    }

    isSubmited.value = false
  }
</script>

<template>
  <section class="container">
    <form ref="formRegist" class="mt-5">
      <div class="w-11/12 md:w-96 flex flex-col gap-6 mx-auto">
        <div class="relative">
          <input 
            type="text" 
            name="name" 
            id="name"
            v-model="name"
            class="peer relative w-full bg-secondary border px-6 py-[10px] border-black rounded-lgm outline-none placeholder:text-transparent drop-shadow-br"
          />
          <label 
            ref="nameLabel"
            for="name"
            class="card-border mt-3 bg-secondary peer-hover:-translate-y-6 peer-focus:-translate-y-6 text-sm mx-auto inset-x-0 w-max">Masukan namamu</label>
        </div>
        <div class="relative">
          <input 
            type="text" 
            name="username" 
            id="username"
            v-model="username"
            class="peer relative w-full bg-secondary border px-6 py-[10px] border-black rounded-lgm outline-none placeholder:text-transparent drop-shadow-br"
          />
          <label 
            ref="usernameLabel"
            for="username"
            class="card-border inset-x-0 mt-3 mx-auto w-max bg-secondary peer-hover:-translate-y-6 peer-focus:-translate-y-6 text-sm">Isi nicknamemu</label>
        </div>
        <div class="relative">
          <input 
            type="email" 
            name="email" 
            id="email"
            v-model="email"
            class="peer relative w-full bg-secondary border px-6 py-[10px] border-black rounded-lgm outline-none placeholder:text-transparent drop-shadow-br"
          />
          <label 
            ref="emailLabel"
            for="email"
            class="card-border inset-x-0 mt-3 mx-auto w-max bg-secondary peer-hover:-translate-y-6 peer-focus:-translate-y-6 text-sm">Isi emailmu</label>
        </div>
        <div class="relative">
          <input 
            type="password" 
            name="password" 
            id="password"
            v-model="password"
            class="peer relative w-full bg-secondary border px-6 py-[10px] border-black rounded-lgm outline-none placeholder:text-transparent drop-shadow-br"
          />
          <label 
            ref="passwordLabel"
            for="password"
            class="card-border inset-x-0 mt-3 mx-auto w-max bg-secondary peer-hover:-translate-y-6 peer-focus:-translate-y-6 text-sm">Isi passwordmu</label>
        </div>
        <div class="relative">
          <button
            type="submit"
            class="relative w-full border px-6 py-[10px] bg-accent-4 font-semibold border-black rounded-lgm hover:bg-secondary transition-all duration-300 drop-shadow-br"
            v-on:click="onRegist"
            :disabled="isSubmited"
            aria-label="Register Now"
          >Daftar Sekarang!</button>
        </div>
      </div>
    </form>
  </section>
</template>
