<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <v-img
          :src="require('../assets/logo.svg')"
          class="my-3"
          contain
          height="200"
        />
      </v-col>

      <v-col class="mb-4">
        <h1 class="display-2 font-weight-bold mb-3">
          Trello叩いてみる！
        </h1>
        <v-text-field
          label="タスク内容"
          single-line
          v-model="title"
          ></v-text-field>
        <v-btn @click="save">登録</v-btn>
        <v-btn @click="notifySlack">slackに通知</v-btn>
        <input id="input-39" type="text">
      </v-col>
    </v-row>
  </v-container>

</template>

<script>
  export default {
    name: 'HelloWorld',

    data: () => ({
      title:"",
      queryParams:{
        key:"",
        token:"",
        idList:"",
        name:""
      }
    }),
    methods: {
      save(){
        this.setParam()
        this.postTask()
        this.clearInput()
      },
      notifySlack(){
        this.notify()
      },
      setParam(){
        this.queryParams.name = this.title
      },
      clearInput(){
        this.title = ""
      },
      async postTask() {
        let url = 'https://trello.com/1/cards'
        let result = await this.axios.post(url,this.queryParams);
        if(result.state){
          console.log("success")
        } else {
          console.log("error")
        }
      },
      async notify(){
        const url = '';
        const url2 = '';

        const data = {
          text: this.title
        };
        let result = await this.axios.post(url,`payload=${JSON.stringify(data)}`);
        await this.axios.post(url2,`payload=${JSON.stringify(data)}`);
        if(result.state){
          console.log("success")
        } else {
          console.log("error")
        }
      },

    },
  }
</script>
