<template>
  <h1>Hey, this a text whiteboard</h1>
  <div id="whiteboard" style="background: lightgray; width: 100%; height: 100vh"></div>
</template>

<script>
import { WhiteWebSdk } from "white-web-sdk";

export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      sdkToken:
        "NETLESSSDK_YWs9T3ZxYkIwdDBkNTF5LUJjUyZub25jZT0xNjEwMDc0MjM5MjM0MDAmcm9sZT0wJnNpZz1mY2FiODdlYTQ2ZDVhNjNiZGEwZjA4N2ViYjAyOTUzYTcxZDdkYzc2MGViZGJiZGViNTMzMGZlZTAyYzU4MGEy",
      appIdentifier: "TJsFsFC2EeuJTUutVz15aw/6bdcdbrmRoNfdQ",
    };
  },
  methods: {
    initRoom() {
      // 构造创建房间的 Request
      var url = "https://api.netless.link/v5/rooms";
      const appIdentifier = "TJsFsFC2EeuJTUutVz15aw/6bdcdbrmRoNfdQ";
      const sdkToken =
        "NETLESSSDK_YWs9T3ZxYkIwdDBkNTF5LUJjUyZub25jZT0xNjEwMDc0MjM5MjM0MDAmcm9sZT0wJnNpZz1mY2FiODdlYTQ2ZDVhNjNiZGEwZjA4N2ViYjAyOTUzYTcxZDdkYzc2MGViZGJiZGViNTMzMGZlZTAyYzU4MGEy";

      var requestInit = {
        method: "POST",
        headers: {
          "content-type": "application/json",
          token: sdkToken,
        },
      };

      window
        .fetch(url, requestInit)
        .then(function (response) {
          return response.json();
        })
        .then(function (json) {
          // 创建房间成功，获取房间的 uuid
          var roomUUID = json.uuid;

          // 构造申请 Room Token 的 Request
          const urlUuid = "https://api.netless.link/v5/tokens/rooms/" + roomUUID;
          var requestInitUuid = {
            method: "POST",
            headers: {
              "content-type": "application/json",
              token: sdkToken,
            },
            body: JSON.stringify({
              lifespan: 0, // 表明 Room Token 永不失效
              role: "admin", // 表明 Room Token 有 Admin 的权限
            }),
          };
          fetch(urlUuid, requestInitUuid)
            .then(function (response) {
              return response.json();
            })
            .then(function (roomToken) {
              // 成功获取房间的 Room Token
              // this.joinRoomInit(roomUUID, roomToken);
              var whiteWebSdk = new WhiteWebSdk({
                appIdentifier: appIdentifier,
              });
              var joinRoomParams = {
                uuid: roomUUID,
                roomToken: roomToken,
              };
              whiteWebSdk
                .joinRoom(joinRoomParams)
                .then(function (room) {
                  // 加入房间成功，获取 room 对象
                  // 并将之前的 <div id="whiteboard"/> 占位符变成白板
                  room.bindHtmlElement(document.getElementById("whiteboard"));
                  // 把教具切换为「铅笔」

                  var strokeColor = [0, 0, 155]; // 用 RGB 标示的颜色，这里标示蓝色
                  var strokeWidth = 10; // 线条粗细，10 是一个很粗的值
                  // room.setMemberState({ currentApplianceName: "pencil" });
                  room.setMemberState({
                    currentApplianceName: "text", // text, ellipse, pencil
                    strokeColor: strokeColor,
                    strokeWidth: strokeWidth,
                  });
                })
                .catch(function (err) {
                  // 加入房间失败
                  console.error(err);
                });
            })
            .catch(function (err) {
              console.error(err);
            });
        })
        .catch(function (err) {
          console.error(err);
        });
    },
  },
  mounted() {
    this.initRoom();
  },
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
