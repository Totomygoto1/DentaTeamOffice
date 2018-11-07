<template>
  <div class="container-fluid mt-4">
    <h1 class="h1">Add Notes</h1>
    <b-alert :show="loading" variant="info">Loading...</b-alert>
    <b-row>
      <b-col>
        <table class="table table-striped">
          <thead>
            <tr>
              <th>ID</th>
			  <th>Notes</th>
			  <th>Visit</th>
			  <th>Customer</th>
			  <th>Dentist</th>
              <th>Updated At</th>
              <th>&nbsp;</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="post in posts" :key="post.id">
              <td>{{ post.id }}</td>
              <td>{{ post.notes }}</td>
              <td>{{ post.visitdate }}</td>
              <td>{{ post.customer }}</td>
              <td>{{ post.dentist }}</td>			  
              <td>{{ post.updatedAt }}</td>
              <td class="text-right">
                <a href="#" @click.prevent="populatePostToEdit(post)">Edit</a> -
                <a href="#" @click.prevent="deletePost(post.id)">Delete</a>
              </td>
            </tr>
          </tbody>
        </table>
      </b-col>
      <b-col lg="3">
        <b-card :title="(model.id ? 'Edit Note ID#' + model.id : 'New Note')">
          <form @submit.prevent="savePost">
            <b-form-group label="Notes">
              <b-form-textarea rows="4" v-model="model.notes"></b-form-textarea>
            </b-form-group>
            <b-form-group label="Visitdate">
              <b-form-input rows="4" v-model="model.visitdate"></b-form-input>
            </b-form-group>	
            <b-form-group label="Customer">
              <b-form-input rows="4" v-model="model.customer"></b-form-input>
            </b-form-group>	
            <b-form-group label="Dentist">
              <b-form-input rows="4" v-model="model.dentist"></b-form-input>
            </b-form-group>				
            <div>
              <b-btn type="submit" variant="success">Save Notes</b-btn>
            </div>
          </form>
        </b-card>
      </b-col>
    </b-row>
  </div>
</template>

<script>
import api from '@/api'
export default {
  data () {
    return {
      loading: false,
      posts: [],
      model: {}
    }
  },
  async created () {
    this.refreshPosts()
  },
  methods: {
    async refreshPosts () {
      this.loading = true
      this.posts = await api.getPosts()
      this.loading = false
    },
    async populatePostToEdit (post) {
      this.model = Object.assign({}, post)
    },
    async savePost () {
      if (this.model.id) {
        await api.updatePost(this.model.id, this.model)
      } else {
        await api.createPost(this.model)
      }
      this.model = {} // reset form
      await this.refreshPosts()
    },
    async deletePost (id) {
      if (confirm('Are you sure you want to delete this post?')) {
        // if we are editing a post we deleted, remove it from the form
        if (this.model.id === id) {
          this.model = {}
        }
        await api.deletePost(id)
        await this.refreshPosts()
      }
    }
  }
}
</script>


