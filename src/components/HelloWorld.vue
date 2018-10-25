<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      For guide and recipes on how to configure / customize this project,<br>
      check out the
      <a href="https://cli.vuejs.org" target="_blank" rel="noopener">vue-cli documentation</a>.
    </p>
    <h3>Installed CLI Plugins</h3>
    <ul>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-babel" target="_blank" rel="noopener">babel</a></li>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-eslint" target="_blank" rel="noopener">eslint</a></li>
    </ul>
    <h3>Essential Links</h3>
    <ul>
      <li><a href="https://vuejs.org" target="_blank" rel="noopener">Core Docs</a></li>
      <li><a href="https://forum.vuejs.org" target="_blank" rel="noopener">Forum</a></li>
      <li><a href="https://chat.vuejs.org" target="_blank" rel="noopener">Community Chat</a></li>
      <li><a href="https://twitter.com/vuejs" target="_blank" rel="noopener">Twitter</a></li>
      <li><a href="https://news.vuejs.org" target="_blank" rel="noopener">News</a></li>
    </ul>
    <h3>Ecosystem</h3>
    <ul>
      <li><a href="https://router.vuejs.org" target="_blank" rel="noopener">vue-router</a></li>
      <li><a href="https://vuex.vuejs.org" target="_blank" rel="noopener">vuex</a></li>
      <li><a href="https://github.com/vuejs/vue-devtools#vue-devtools" target="_blank" rel="noopener">vue-devtools</a></li>
      <li><a href="https://vue-loader.vuejs.org" target="_blank" rel="noopener">vue-loader</a></li>
      <li><a href="https://github.com/vuejs/awesome-vue" target="_blank" rel="noopener">awesome-vue</a></li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";
import _ from "lodash";

export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  created() {
    const signalr = require("@aspnet/signalr");


    let manager = new signalr.HubConnectionBuilder()
      .withUrl(
        "https://dev.people.com.ai/attendant/hubs/manager?accountId=1b18adf3-bf7d-487c-9eb2-2d2032fda252"
      )
      .build();

      manager.on('SupervisorNotification', (result) => {
        console.log('resilt', result)
      })

      manager.start().then(() => {
        manager.invoke('GetAllAttendant', '9c57663e-cb7c-4e33-ad37-03a56e5d5ee2')
      })

    let chat = new signalr.HubConnectionBuilder()
      .withUrl(
        "https://dev.people.com.ai/attendant/hubs/chat?attendantId=e98c919b-130a-4231-a8be-c331ece71a66"
      )
      .build();

    chat.on("Receive", stringRes => {
      let result = JSON.parse(stringRes);
      console.log("Received Message!");
      if (result.ResponseType === 17) {
        let message = JSON.parse(result.Message);
        console.log("Sending message...!");
        setTimeout(() => {
          chat.invoke(
            "Send", this.GenerateMessage(message)
          );
        }, 500);
      }

      console.log("Result = ", result);
    });

    chat.start().then(() => {
      console.log("Enviando mensagem iniciou a conexão...");
      // let message = chat.invoke("Send");
    });

    // x.start().then(() => {
    //     console.log('Started -> ')
    //         axios.get('https://dev.people.com.ai/attendant/api/Sector/get-structure')
    // .then((a) => {
    //   let data = a.data

    //   let company = _.find(data, { 'name': 'TesteCarga' })

    //   console.log('company', company)

    //   let sector = _.find(company.sectors, {'setorId': '9c57663e-cb7c-4e33-ad37-03a56e5d5ee2' })

    //   let queues = sector.queues

    //   console.log('ueue', queues)

    //   queues.forEach((queue) => {
    //     console.log('Removendo a fila => ' + JSON.stringify(queue) )
    //     x.invoke('DeleteQueue', JSON.stringify({ 'AccountId': 'fda46d49-f889-4b93-b986-a71e3a215bbb', 'IdQueue': queue.idQueue }))
    //   })
    // })
    // for (let index = 0; index < 1500; index++) {
    //   let queue = {"accountId":"1b18adf3-bf7d-487c-9eb2-2d2032fda252","name":"Fila_Teste_Carga_" + index,"isAllDays":true,"isWeeked":false,"isHoliday":false,"queueDays":[{"day":0,"isActive":true,"isTimed":true,"dayIntervals":[]},{"day":1,"isActive":true,"isTimed":true,"dayIntervals":[]},{"day":2,"isActive":true,"isTimed":true,"dayIntervals":[]},{"day":3,"isActive":true,"isTimed":true,"dayIntervals":[]},{"day":4,"isActive":true,"isTimed":true,"dayIntervals":[]},{"day":5,"isActive":true,"isTimed":true,"dayIntervals":[]},{"day":6,"isActive":true,"isTimed":true,"dayIntervals":[]}],"holidays":[]};
    //   setTimeout(() => {
    //     x.invoke('CreateQueue', JSON.stringify(queue))
    //   }, 1000);
    // }
    // });
    axios.defaults.baseURL = "https://dev.people.com.ai";
    axios.defaults.headers.common["X-Consumer-Custom-ID"] =
      "1b18adf3-bf7d-487c-9eb2-2d2032fda252";
    axios.defaults.headers.post["Content-Type"] = "application/json";
  },
  methods: {
    GenerateMessage(message) {
      console.log(message)
      return JSON.stringify({
        UniqueId: null,
        IdBot: message.IdBot,
        Bridge: false,
        ConversationId: message.ConversationId,
        UserId: message.UserId,
        ChannelId: message.ChannelId,
        UserName: message.UserName,
        ToQueue: message.ToQueue,
        IdSector: message.IdSector,
        IdAttendant: message.AttendantId,
        Generic: false,
        Sync: message.Sync,
        AttendantPicture:
          "https://people-sac-profile-bucket.s3.us-west-2.amazonaws.com/161020181349.jpg?AWSAccessKeyId=AKIAJWGHZ3LQWBYNMRPA&Expires=1855327795&Signature=NLQHMPH67ABEY%2FTEhiII1KdHqX0%3D",
        Content: `${JSON.stringify({
          Type: "response",
          Message: "Conexão",
          File: null,
          Timestamp: 1540484263540,
          FormattedTime: 1540484263
        })}`
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
