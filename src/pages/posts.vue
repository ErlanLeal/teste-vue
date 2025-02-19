<template>
  <v-container class="mt-5">
    <!-- Formulário de Novo Post -->
    <v-card class="mb-5" outlined>
      <v-card-title>
        <v-avatar class="mr-3" color="primary" size="40">
          <span class="white--text headline">U</span>
        </v-avatar>
        <span class="subtitle-1 font-weight-bold">Novo Post</span>
      </v-card-title>
      <v-card-text>
        <v-text-field
          v-model="newPost.title"
          label="Título"
          outlined
          dense
        ></v-text-field>
        <v-textarea
          v-model="newPost.body"
          label="Conteúdo"
          outlined
          dense
          rows="3"
        ></v-textarea>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="primary" @click="addPost">Postar</v-btn>
      </v-card-actions>
    </v-card>

    <v-row>
      <v-col
        v-for="(post, index) in posts"
        :key="post.id"
        cols="12"
      >
        <v-card outlined>
          <v-card-title class="d-flex align-center">
            <v-avatar class="mr-3" size="40">
              <img src="https://cdn.vuetifyjs.com/images/john.png" alt="User" />
            </v-avatar>
            <div>
              <div class="subtitle-1 font-weight-bold">User Name</div>
              <div class="caption grey--text">Há alguns minutos</div>
            </div>
            <v-spacer></v-spacer>
            <v-btn icon small @click="openEditDialog(post, index)">
              <v-icon>mdi-pencil</v-icon>
            </v-btn>
            <v-btn icon small @click="deletePost(index)">
              <v-icon>mdi-delete</v-icon>
            </v-btn>
          </v-card-title>
          <v-card-text>
            <div class="headline mb-2">{{ post.title }}</div>
            <div>{{ post.body }}</div>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>

    <v-dialog v-model="editDialog" max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline">Editar Post</span>
        </v-card-title>
        <v-card-text>
          <v-text-field
            v-model="editedPost.title"
            label="Título"
            outlined
          ></v-text-field>
          <v-textarea
            v-model="editedPost.body"
            label="Conteúdo"
            outlined
            rows="3"
          ></v-textarea>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn text @click="closeEditDialog">Cancelar</v-btn>
          <v-btn color="primary" text @click="saveEdit">Salvar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import axios from 'axios';

export default {
  name: 'PostsPage',
  data: () => ({
    posts: [],
    newPost: {
      title: '',
      body: '',
    },
    editDialog: false,
    editedPost: {},
    editIndex: -1,
  }),
  mounted() {
    this.fetchPosts();
  },
  methods: {
    async fetchPosts() {
      try {
        const response = await axios.get(
          'https://jsonplaceholder.typicode.com/posts'
        );
        this.posts = response.data.slice(0, 10);
      } catch (error) {
        console.error('Erro ao buscar posts:', error);
      }
    },
    addPost() {
      if (this.newPost.title.trim() && this.newPost.body.trim()) {

        const newPostObj = { ...this.newPost, id: Date.now() };

        this.posts.unshift(newPostObj);
        this.newPost.title = '';
        this.newPost.body = '';
      }
    },
    deletePost(index) {
      this.posts.splice(index, 1);
    },
    openEditDialog(post, index) {
      this.editDialog = true;
      this.editIndex = index;
      this.editedPost = { ...post };
    },
    closeEditDialog() {
      this.editDialog = false;
      this.editedPost = {};
      this.editIndex = -1;
    },
    saveEdit() {
      if (this.editedPost.title.trim() && this.editedPost.body.trim()) {
        this.$set(this.posts, this.editIndex, { ...this.editedPost });
        this.closeEditDialog();
      }
    },
  },
};
</script>

<style scoped>

.v-card {
  margin-bottom: 20px;
}

</style>
