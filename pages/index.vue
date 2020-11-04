<template>
  <div class="mx-auto max-w-screen-md px-6 pt-12">
    <div class="bg-gray-800 rounded-lg shadow" style="marginTop: -20px">
      <div class="">
        <div
          v-for="o in data"
          :key="o.id"
          class="flex items-center px-6 py-4 list relative"
        >
          <div class="mr-5">
            <img
              :src="o.avatar"
              :alt="o.first_name"
              class="rounded-full"
              style="width: 50px"
            >
          </div>
          <div>
            <div v-if="o.id == data_edit">
              <input v-model="dataa.email" type="text">
            </div>
            <nuxt-link v-else to="/" class="font-semibold text-white">
              {{ o.email }}
            </nuxt-link>
            <div class="text-gray-500">
              <div style="marginTop: 4px" v-if="o.id == data_edit">
                <input v-model="dataa.name" type="text">
              </div>
              <div v-else>
                {{ o.first_name }} {{ o.last_name }}
              </div>
            </div>
          </div>
          <div v-if="o.id !== data_edit" class="ml-auto">
            <a
              @click="edit(o)"
              href="#"
              class="uppercase text-sm text-white text-opacity-75 hover:text-opacity-100 font-semibold"
            >
              Edit
            </a>
            <a
              href="#"
              @click="delete_data(o)"
              class="uppercase text-sm text-red-500 text-opacity-75 hover:text-opacity-100 font-semibold"
            >
              Delete
            </a>
          </div>
          <div v-else class="ml-auto">
            <a
            @click="cancel"
              href="#"
              class="uppercase text-sm text-red-500 text-opacity-75 hover:text-opacity-100 font-semibold"
            >
              Cancel
            </a>
            <a
            @click="save(o)"
              href="#"
              class="uppercase text-sm text-white text-opacity-75 hover:text-opacity-100 font-semibold"
            >
              Save
            </a>
          </div>
        </div>
      </div>
    </div>
    <div class="bg-gray-500 rounded-lg shadow mt-3">
      <input v-model="add.add_email" style="margin: 10px" placeholder="Email" type="text">
      <input v-model="add.add_firstname" style="margin: 10px" placeholder="First Name" type="text">
      <input v-model="add.add_lastname" style="margin: 10px" placeholder="Last Name" type="text">
      <a
        @click="add_data"
        style="marginLeft: 50px"
        href="#"
        class="uppercase text-sm text-opacity-75 hover:text-opacity-100 font-semibold"
      >
        Save
      </a>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      response: {},
      data_edit: '',
      dataa : {
        email: '',
        name: ''
      },
      add: {
        add_email: '',
        add_firstname: '',
        add_lastname: ''
      }
    }
  },
  computed: {
    data() {
      return this.response.data
    },
    meta() {
      const {
        page,
        per_page,
        total_pages,
        total
      } = this.response

      return {
        page,
        per_page,
        total_pages,
        total,
      }
    }
  },
  mounted() {
    this.getData()
  },
  methods: {
    async getData() {
      const response = await this.$axios.$get('users')
      this.response = response
    },
    edit (e) {
      this.dataa.email = e.email
      this.data_edit = e.id
      this.dataa.name = `${e.first_name} ${e.last_name}`
    },
    async save(e){
      const firstname = this.dataa.name
      if (!this.dataa.email.includes('@')) { 
        alert('email salah')       
        } else if (firstname == '') {
        alert('nama kosong')       
        }  else {
          const response = await this.$axios.$put(`users/${e.id}`, {
            "email": this.dataa.email,
            "first_name": firstname.substring(0, firstname.indexOf(' ')),
            "last_name": firstname.substring(firstname.indexOf(' ')+1)
          })
        this.data_edit = '' 
      }
    },
    cancel(){
      this.data_edit = ''
    },
    async delete_data(o){ 
      const response = await this.$axios.$delete(`users/${o.id}`)
    },
    async add_data(){
      const data = this.add
      if (data.add_email !== '' && 
      data.add_firstname !== '' && 
      data.add_lastname !== '') {
        if (data.add_email.includes('@')) {          
          const response = await this.$axios.$post(`users`, {
            "email": data.add_email,
            "first_name": data.add_firstname,
            "last_name": data.add_lastname
          })
        } else {
          alert('email salah')
        }
      } else {
        alert('input tidak boleh kosong')
      }
    }
  }
}
</script>

<style>
.list:after {
  content: '';
  @apply border-b absolute bottom-0 left-0 right-0 ml-24 border-gray-700;
}
</style>
