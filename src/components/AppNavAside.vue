<template>
  <aside class="container">
    <ul>
      <li v-bind:key="box.id" v-for="box of boxs" v-on:click="handleBoxClick(box);">{{ box.name }}</li>
    </ul>

    <div class="button-container"><add-button :onClick="handleCreateButtonClick"></add-button></div>

    <el-dialog
      title="Create new article category"
      width="400px"
      :visible.sync="dialogVisible"
      :before-close="handleClose"
    >
      <el-form :model="createForm">
        <el-form-item> <el-input v-model="createForm.name" autocomplete="off"></el-input> </el-form-item>

        <el-button native-type="submit" type="primary" @click="handleCreateBox($event);">create</el-button>
      </el-form>
    </el-dialog>

    <div class="user-container">
      <img src="/img/user.png" class="avatar" alt="avatar" />
      <div class="username">{{username}}</div>
    </div>
  </aside>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import axios from 'axios';
import values from 'ramda/es/values';
import compose from 'ramda/es/compose';


// TODO rename
@Component({})
export default class AppNavAside extends Vue {
  public searchStr: string = '';
  public dialogVisible = false;
  public createForm = {
    name: ''
  };
  public username  = '';

  created() {
    this.$eventHub.$on('searchStr', (searchStr: string) => {
      this.searchStr = searchStr;
    });
    this.$store.dispatch('getArticleBoxs');
    this.username = window.localStorage.getItem('username-cache')!;
  }

  get boxs() {
    const boxMap = this.$store.state.boxs;
    return compose(values)(boxMap);
  }

  handleBoxClick(box: any) {
    this.$store.commit('currentBoxId', box.id);
  }

  handleCreateBox(event: Event) {
    event.preventDefault();
    axios
      .post(`/api/auth/article-box`, {
        name: this.createForm.name
      })
      .then(() => {
        this.dialogVisible = false;
      });
  }

  handleClose() {
    this.dialogVisible = false;
  }

  handleCreateButtonClick() {
    this.dialogVisible = true;
  }
}
</script>

<style scoped>
.container {
  position: relative;
  width: 35%;
  max-width: 180px;
  background-color: #333;
  color: white;
}

ul {
  padding: 0;
  margin: 0;
  list-style: none;
}

li {
  padding-left: 20px;
  cursor: pointer;
  text-align: left;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  border-bottom: 1px solid #777;
  height: 40px;
  line-height: 48px;
}

li:hover {
  background-color: #999;
}

a {
  color: #42b983;
}

.button-container {
  margin-top: 20px;
  text-align: center;
}

.user-container {
  position: absolute;
  width: 100%;
  height: 150px;
  bottom: 0;
}

.avatar {
  width: 50px;
  height: 50px;
  display: block;
  margin: 0 auto;
  cursor: pointer;
}

.user-container .username {
  margin-top: 8px;
  text-align: center;
  cursor: pointer;
}
</style>
