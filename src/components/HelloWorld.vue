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
        <v-text-field
          label="誰に"
          single-line
          v-model="target"
        ></v-text-field>
        <v-text-field
          label="いつ"
          single-line
          v-model="time"
        ></v-text-field>
        <v-text-field
          label="メッセージ"
          single-line
          v-model="message"
        ></v-text-field>

        <v-btn @click="record">{{ recordBtnValue }}</v-btn>
        <v-btn @click="save">登録</v-btn>
        <v-btn @click="notifySlack">slackに通知</v-btn>
        <v-btn @click="remindSlack">Reminderをセット</v-btn>
        <v-btn @click="registerAccountList">アカウント登録</v-btn>
        <input id="input-39" type="text">
      </v-col>
    </v-row>
  </v-container>

</template>

<script>
  export default {
    data: () => ({
      title:"",
      target:"",
      time:"",
      message:"",
      queryParams:{
        name:"",
      },
      recognizing:false,
      recognition:null,
      recordBtnValue: "録音",
      recoredText:"",
      memberCode4:[],
      memberCode:[],

    }),
    async created() {
      // const { webkitSpeechRecognition } = window as any;
      const recognition = new window.webkitSpeechRecognition()
      recognition.lang = "ja-JP"
      recognition.continuous = true
      recognition.onresult = await this.recognize
      this.recognition = recognition
    },
    methods: {
      registerAccountList(){
        let index = 305
        this.memberCode.forEach(function(value){
          this.registerAccount(value,index)
          index++
        }.bind(this))
      },
      registerAccount(value,index){
        console.log("asdf")
        const url = 'https://egs-organization-api-external.stg.eigosapuri.jp/organization/v1/auth/register';
        const data = this.getAccountData()
        console.log(data)
        data.memberCode = value
        data.firstName = "速度"+index
        data.userName = "sokudotaro"+index
        let result = this.axios.put(url,data);
        if(result.state){
          console.log("success")
        } else {
          console.log("error")
        }
      },
      getAccountData(){
        return {
        birthdayDay: 22,
        birthdayMonth: 1,
        birthdayYear: 2021,
        firstName: "速度",
        firstNameKana: "そくど",
        lastName: "４",
        lastNameKana: "よん",
        password: "12345abcde",
        sex: 1,
        affiliation: {
          schoolYear: "高校3",
          className: "1",
          attendanceNumber: "2",
          departmentName: "",
          employeeNumber: ""
          }
        }
      },
      save(){
        this.setParam()
        this.postTask()
        this.clearInput()
      },
      register(){
        console.log("suadsf")
        this.notifySlack()
        this.registerAccount()
      },
      notifySlack(){
        this.notify()
      },
      remindSlack(){
        // /remind [target] to [message] [at|in|on] [日時]
        let sendMessage = '/remind '+ this.target +' "'+ this.message + '" ' + this.time
        console.log(sendMessage)
        this.notify(sendMessage)
      },

      async sendReminder(){
        let url = 'https://slack.com/api/reminders.add'
        let result = await this.axios.post(url,this.queryParams);
        if(result.state){
          console.log("success")
        } else {
          console.log("error")
        }
      },

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
      async notify(sendMessage){
        const url = 'https://hooks.slack.com/services/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';
        const url2 = 'https://hooks.slack.com/services/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';
        const data = {}
        if(sendMessage != undefined){
          data.text = sendMessage
        } else {
          data.text = this.title
        }
        let result = await this.axios.post(url,`payload=${JSON.stringify(data)}`);
        await this.axios.post(url2,`payload=${JSON.stringify(data)}`);
        if(result.state){
          console.log("success")
        } else {
          console.log("error")
        }
      },
      record() {
        // if(this.recognition in window){
          if (this.recognizing) {
            this.recognition.stop();
            this.recordBtnValue = "録音";
          } else {
            // alert("録音開始")
            this.recognition.start();
            this.clearInput()
            this.recordBtnValue = "停止";
          }
          this.recognizing = !this.recognizing;
        // } else {
        //   alert("このブラウザは、音声認識に対応してません")
        // }
      },
      async recognize(e) {
        console.log("asdadsfgasf")
        let word = `${e.results[e.results.length - 1][0].transcript}`
        let target = `${e.results[e.results.length - 1][0].transcript}\n`
        if(word === "登録"){
          console.log("reg")
          this.setParam()
          this.postTask()
          this.record()
        } else if (word === "通知"){
          console.log("notify")
          this.notify()
          this.record()
        } else {
          console.log("other")
          this.recoredText = target
          this.title = this.recoredText
        }

      },
  }
</script>
