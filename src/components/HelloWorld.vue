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
      }
    },
  }
  //https://trello.com/1/cards?key=2fd7c449f94fc452a2010a6ffc70d3d0&token=55f63bda4ef69f548781eecb7657bba5694a9718561afa8016802a1a17e7b999&idList=5efee6cee1d7e04130a8ffcb&name=ホゲホゲ01
</script>
