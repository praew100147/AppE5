<template>
  <v-container class="modern-background">
    <v-row>
      <!-- Connection and Status Button -->
      <v-col cols="12" class="text-center">
        <v-btn
          class="ma-2 custom-btn-modern"
          :color="mqtt"
          @click="toggleMqttConnection"
          elevation="2"
          block
        >
          {{ status }}
          <v-icon
            :icon="icon"
            class="align-middle"
          ></v-icon>
        </v-btn>
      </v-col>
      
      <!-- Temperature and Humidity Status -->
      <v-col cols="12" class="status-overview text-center">
        <h1 class="title-text">ช่วงวัย : {{ state }}</h1>
      </v-col>

      <!-- Light Control Buttons -->
      <v-col cols="4" class="text-center">
        <v-btn
          class="modern-light-btn"
          :color="colorLight1"
          @click="toggleLight1"
        >
          <v-icon :icon="iconLight1"></v-icon>
          &nbsp; {{ light1 }}
        </v-btn>
      </v-col>
      <v-col cols="4" class="text-center">
        <v-btn
          class="modern-light-btn"
          :color="colorLight2"
          @click="toggleLight2"
        >
          <v-icon :icon="iconLight2"></v-icon>
          &nbsp; {{ light2 }}
        </v-btn>
      </v-col>
      <v-col cols="4" class="text-center">
        <v-btn
          class="modern-light-btn"
          :color="colorLight3"
          @click="toggleLight3"
        >
          <v-icon :icon="iconLight3"></v-icon>
          &nbsp; {{ light3 }}
        </v-btn>
      </v-col>
      
      <!-- Circular Progress for Temperature and Humidity -->
      <v-col cols="6" class="text-center">
        <v-card class="circular-card" elevation="3">
          <v-progress-circular
            :model-value="showTemp"
            :rotate="360"
            :size="120"
            :width="16"
            :color="tempColor"
          >
            <template v-slot:default> {{ showTemp }}°C </template>
          </v-progress-circular>
          <p class="progress-label">อุณหภูมิ</p>
        </v-card>
      </v-col>
      
      <v-col cols="6" class="text-center">
        <v-card class="circular-card" elevation="3">
          <v-progress-circular
            :model-value="showHumi"
            :rotate="360"
            :size="120"
            :width="16"
            :color="humiColor"
          >
            <template v-slot:default> {{ showHumi }}% </template>
          </v-progress-circular>
          <p class="progress-label">ความชื้น</p>
        </v-card>
      </v-col>
      
      <!-- Status Display -->
      <v-col cols="12">
        <v-card class="status-card-modern" elevation="2">
          <p>สถานะอุณหภูมิ: <strong>{{ status1 }}</strong></p>
          <p>สถานะความชื้น: <strong>{{ status2 }}</strong></p>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>


<script>
import mqtt from "mqtt";
import axios from "axios";

export default {
  data() {
    return {
      id: 10,
      status: "OFFLINE",
      showTemp: "0",
      showHumi: "0",
      mqtt: "#8F8F8F",
      icon: "mdi-close-circle-outline",
      colorLight1: "#8F8F8F",
      colorLight2: "#8F8F8F",
      colorLight3: "#8F8F8F",
      light1: "สถานะการทำงาน",
      light2: "มอเตอร์พ่นหมอก",
      light3: "ระบบฮีทเตอร์",
      status1: "รอแสดงผล",
      status2: "รอแสดงผล",
      state: "รอแสดงผล",
      iconLight1: "mdi-lightbulb-off-outline",
      iconLight2: "mdi-lightbulb-off-outline",
      iconLight3: "mdi-lightbulb-off-outline",
      lineNotifyToken: "FUjligDJrKv3tramPU4EaXf2aH39bRLxwH6dE4Gm0pY",
    };
  },

  watch: {
    showTemp(newTemp) {
      let newStatus1 = this.status1;

      if (this.state === 1) {
        if (newTemp > 28) {
          newStatus1 = "สูงเกินไป";
          if (this.status1 !== newStatus1) {
            this.sendLineNotify("แจ้งเตือน: อุณหภูมิสูงเกินไป! อุณหภูมิขณะนี้คือ " + newTemp + " °C");
            const payload = JSON.stringify({ "Light3": "0" });
            this.client.publish("phanu/Light3", payload);
            const payload2 = JSON.stringify({ "Light2": "1" });
            this.client.publish("phanu/Light2", payload2);
          }
        } else if (newTemp < 27) {
          newStatus1 = "ต่ำเกินไป";
          if (this.status1 !== newStatus1) {
            this.sendLineNotify("แจ้งเตือน: อุณหภูมิต่ำเกินไป! อุณหภูมิขณะนี้คือ " + newTemp + " °C");
            const payload = JSON.stringify({ "Light3": "1" });
            this.client.publish("phanu/Light3", payload);
            const payload2 = JSON.stringify({ "Light2": "0" });
            this.client.publish("phanu/Light2", payload2);
          }
        } else {
          newStatus1 = "ปกติ";
          const payload = JSON.stringify({ "Light3": "0" });
          this.client.publish("phanu/Light3", payload);
          const payload2 = JSON.stringify({ "Light2": "0" });
          this.client.publish("phanu/Light2", payload2);
        }
        this.status1 = newStatus1;
      } else if (this.state === 2) {
        if (newTemp > 27) {
          newStatus1 = "สูงเกินไป";
          if (this.status1 !== newStatus1) {
            this.sendLineNotify("แจ้งเตือน: อุณหภูมิสูงเกินไป! อุณหภูมิขณะนี้คือ " + newTemp + " °C");
            const payload = JSON.stringify({ "Light3": "0" });
            this.client.publish("phanu/Light3", payload);
            const payload2 = JSON.stringify({ "Light2": "1" });
            this.client.publish("phanu/Light2", payload2);
          }
        } else if (newTemp < 26) {
          newStatus1 = "ต่ำเกินไป";
          if (this.status1 !== newStatus1) {
            this.sendLineNotify("แจ้งเตือน: อุณหภูมิต่ำเกินไป! อุณหภูมิขณะนี้คือ " + newTemp + " °C");
            const payload = JSON.stringify({ "Light3": "1" });
            this.client.publish("phanu/Light3", payload);
            const payload2 = JSON.stringify({ "Light2": "0" });
            this.client.publish("phanu/Light2", payload2);
          }
        } else {
          newStatus1 = "ปกติ";
          const payload = JSON.stringify({ "Light3": "0" });
          this.client.publish("phanu/Light3", payload);
          const payload2 = JSON.stringify({ "Light2": "0" });
          this.client.publish("phanu/Light2", payload2);
        }
        this.status1 = newStatus1;
      } else if (this.state === 3) {
        if (newTemp > 26) {
          newStatus1 = "สูงเกินไป";
          if (this.status1 !== newStatus1) {
            this.sendLineNotify("แจ้งเตือน: อุณหภูมิสูงเกินไป! อุณหภูมิขณะนี้คือ " + newTemp + " °C");
            const payload = JSON.stringify({ "Light3": "0" });
            this.client.publish("phanu/Light3", payload);
            const payload2 = JSON.stringify({ "Light2": "1" });
            this.client.publish("phanu/Light2", payload2);
          }
        } else if (newTemp < 25) {
          newStatus1 = "ต่ำเกินไป";
          if (this.status1 !== newStatus1) {
            this.sendLineNotify("แจ้งเตือน: อุณหภูมิต่ำเกินไป! อุณหภูมิขณะนี้คือ " + newTemp + " °C");
            const payload = JSON.stringify({ "Light3": "1" });
            this.client.publish("phanu/Light3", payload);
            const payload2 = JSON.stringify({ "Light2": "0" });
            this.client.publish("phanu/Light2", payload2);
          }
        } else {
          newStatus1 = "ปกติ";
          const payload = JSON.stringify({ "Light3": "0" });
          this.client.publish("phanu/Light3", payload);
          const payload2 = JSON.stringify({ "Light2": "0" });
          this.client.publish("phanu/Light2", payload2);
        }
        this.status1 = newStatus1;
      } else if (this.state === 4) {
        if (newTemp > 25) {
          newStatus1 = "สูงเกินไป";
          if (this.status1 !== newStatus1) {
            this.sendLineNotify("แจ้งเตือน: อุณหภูมิสูงเกินไป! อุณหภูมิขณะนี้คือ " + newTemp + " °C");
            const payload = JSON.stringify({ "Light3": "0" });
            this.client.publish("phanu/Light3", payload);
            const payload2 = JSON.stringify({ "Light2": "1" });
            this.client.publish("phanu/Light2", payload2);
          }
        } else if (newTemp < 24) {
          newStatus1 = "ต่ำเกินไป";
          if (this.status1 !== newStatus1) {
            this.sendLineNotify("แจ้งเตือน: อุณหภูมิต่ำเกินไป! อุณหภูมิขณะนี้คือ " + newTemp + " °C");
            const payload = JSON.stringify({ "Light3": "1" });
            this.client.publish("phanu/Light3", payload);
            const payload2 = JSON.stringify({ "Light2": "0" });
            this.client.publish("phanu/Light2", payload2);
          }
        } else {
          newStatus1 = "ปกติ";
          const payload = JSON.stringify({ "Light3": "0" });
          this.client.publish("phanu/Light3", payload);
          const payload2 = JSON.stringify({ "Light2": "0" });
          this.client.publish("phanu/Light2", payload2);
        }
        this.status1 = newStatus1;
      } else if (this.state === 5) {
        if (newTemp > 25) {
          newStatus1 = "สูงเกินไป";
          if (this.status1 !== newStatus1) {
            this.sendLineNotify("แจ้งเตือน: อุณหภูมิสูงเกินไป! อุณหภูมิขณะนี้คือ " + newTemp + " °C");
            const payload = JSON.stringify({ "Light3": "0" });
            this.client.publish("phanu/Light3", payload);
            const payload2 = JSON.stringify({ "Light2": "1" });
            this.client.publish("phanu/Light2", payload2);
          }
        } else if (newTemp < 24) {
          newStatus1 = "ต่ำเกินไป";
          if (this.status1 !== newStatus1) {
            this.sendLineNotify("แจ้งเตือน: อุณหภูมิต่ำเกินไป! อุณหภูมิขณะนี้คือ " + newTemp + " °C");
            const payload = JSON.stringify({ "Light3": "1" });
            this.client.publish("phanu/Light3", payload);
            const payload2 = JSON.stringify({ "Light2": "0" });
            this.client.publish("phanu/Light2", payload2);
          }
        } else {
          newStatus1 = "ปกติ";
          const payload = JSON.stringify({ "Light3": "0" });
          this.client.publish("phanu/Light3", payload);
          const payload2 = JSON.stringify({ "Light2": "0" });
          this.client.publish("phanu/Light2", payload2);
        }
        this.status1 = newStatus1;
      } else {
        this.status1 = newStatus1;
      } 
    },

    showHumi(newHumi) {
      let newStatus2 = this.status2;

      if (this.state === 1) {
        if (newHumi >= 91) {
          newStatus2 = "สูงเกินไป";
          if (this.status2 !== newStatus2) {
            this.sendLineNotify("แจ้งเตือน: ความชื้นสูงเกินไป! ความชื้นขณะนี้คือ " + newHumi + " %");
          } 
        } else if (newHumi < 90) {
            newStatus2 = "ต่ำเกินไป";
            this.sendLineNotify("แจ้งเตือน: ความชื้นต่ำเกินไป! ความชื้นขณะนี้คือ " + newHumi + " %");
        } else if (newHumi >= 90) {
          newStatus2 = "ปกติ";
        } 
        else {
          newStatus2 = "เกินข้อผิดพลาด";
        }
        this.status2 = newStatus2;
      } else if (this.state === 2) {
        if (newHumi > 90) {
          newStatus2 = "สูงเกินไป";
          if (this.status2 !== newStatus2) {
            this.sendLineNotify("แจ้งเตือน: ความชื้นสูงเกินไป! ความชื้นขณะนี้คือ " + newHumi + " %");
          }
        } else if (newHumi < 85) {
          newStatus2 = "ต่ำเกินไป";
          if (this.status2 !== newStatus2) {
            this.sendLineNotify("แจ้งเตือน: ความชื้นต่ำเกินไป! ความชื้นขณะนี้คือ " + newHumi + " %");
          }
        } else if (newHumi >= 85 && newHumi <= 90) {
          newStatus2 = "ปกติ";
        }  else {
          newStatus2 = "Error";
        }
        this.status2 = newStatus2;
      } else if (this.state === 3) {
        if (newHumi > 85) {
          newStatus2 = "สูงเกินไป";
          if (this.status2 !== newStatus2) {
            this.sendLineNotify("แจ้งเตือน: ความชื้นสูงเกินไป! ความชื้นขณะนี้คือ " + newHumi + " %");
          }
        } else if (newHumi < 80) {
          newStatus2 = "ต่ำเกินไป";
          if (this.status2 !== newStatus2) {
            this.sendLineNotify("แจ้งเตือน: ความชื้นต่ำเกินไป! ความชื้นขณะนี้คือ " + newHumi + " %");
          }
        } else if (newHumi <= 85 && newHumi >= 80) {
          newStatus2 = "ปกติ";
        }  else {
          newStatus2 = "Error";
        }
        this.status2 = newStatus2;
      } else if (this.state === 4) {
        if (newHumi > 76) {
          newStatus2 = "สูงเกินไป";
          if (this.status2 !== newStatus2) {
            this.sendLineNotify("แจ้งเตือน: ความชื้นสูงเกินไป! ความชื้นขณะนี้คือ " + newHumi + " %");
          }
        } else if (newHumi < 75) {
          newStatus2 = "ต่ำเกินไป";
          if (this.status2 !== newStatus2) {
            this.sendLineNotify("แจ้งเตือน: ความชื้นต่ำเกินไป! ความชื้นขณะนี้คือ " + newHumi + " %");
          }
        } else {
          newStatus2 = "ปกติ";
        }
        this.status2 = newStatus2;
      } else if (this.state === 5) {
        if (newHumi >= 71) {
          newStatus2 = "สูงเกินไป";
          if (this.status2 !== newStatus2) {
            this.sendLineNotify("แจ้งเตือน: ความชื้นสูงเกินไป! ความชื้นขณะนี้คือ " + newHumi + " %");
          }
        } else if (newHumi < 70) {
          newStatus2 = "ต่ำเกินไป";
          if (this.status2 !== newStatus2) {
            this.sendLineNotify("แจ้งเตือน: ความชื้นต่ำเกินไป! ความชื้นขณะนี้คือ " + newHumi + " %");
          }
        } else {
          newStatus2 = "ปกติ";
        }
        this.status2 = newStatus2;
      }
    },
  },

  computed: {

    goodTemp() {
      if (this.state === 1) {
        return "27 - 28 °C";
      } else if (this.state === 2) {
        return "26 - 27 °C";
      } else if (this.state === 3) {
        return "25 - 26 °C";
      } else if (this.state === 4) {
        return "24 - 25 °C";
      } else if (this.state === 5) {
        return "24 - 25 °C";
      } else {
        return "-";
      }
    },

    goodHumi() {
      if (this.state === 1) {
        return "90 %";
      } else if (this.state === 2) {
        return "85 - 90 %";
      } else if (this.state === 3) {
        return "80 - 85 %";
      } else if (this.state === 4) {
        return "75 %";
      } else if (this.state === 5) {
        return "70 %";
      } else {
        return "-";
      }
    },

    tempColor() {
      if (this.state === 1) {
        if (this.showTemp > 28) {
          return "red";
        } else if (this.showTemp < 27) {
          return "red";
        } else if (this.showTemp >= 27 && this.showTemp <= 28) {
          return "green";
        } else {
          return "red";
        }
      } else if (this.state === 2) {
        if (this.showTemp > 27) {
          return "red";
        } else if (this.showTemp >= 26 && this.showTemp <= 27) {
          return "green";
        } else {
          return "red";
        }
      } else if (this.state === 3) {
        if (this.showTemp > 26) {
          return "red";
        } else if (this.showTemp >= 25 && this.showTemp <= 26) {
          return "green";
        } else {
          return "red";
        }
      } else if (this.state === 4) {
        if (this.showTemp > 25) {
          return "red";
        } else if (this.showTemp >= 24 && this.showTemp <= 25) {
          return "green";
        } else {
          return "red";
        }
      } else if (this.state === 5) {
        if (this.showTemp > 25) {
          return "red";
        } else if (this.showTemp >= 24 && this.showTemp <= 25) {
          return "green";
        } else {
          return "red";
        }
      }
    },

    humiColor() {
      if (this.state === 1) {
        if (this.showHumi >= 91) {
          return "red";
        } else if (this.showHumi < 90) {
          return "red";
        } else {
          return "green";
        }
      } else if (this.state === 2) {
        if (this.showHumi > 90) {
          return "red";
        } else if (this.showHumi <= 90 && this.showHumi >= 85) {
          return "green";
        } else if (this.showHumi < 85) {
          return "red";
        }
      } else if (this.state === 3) {
        if (this.showHumi > 85) {
          return "red";
        } else if (this.showHumi >= 80 && this.showHumi <= 85) {
          return "green";
        } else if (this.showHumi < 80) {
          return "red";
        }
      } else if (this.state === 4) {
        if (this.showHumi >= 76) {
          return "red";
        } else if (this.showHumi < 75) {
          return "red";
        } else {
          return "green";
        }
      } else if (this.state === 5) {
        if (this.showHumi >= 71) {
          return "red";
        } else if (this.showHumi < 70) {
          return "red";
        } else {
          return "green";
        }
      }
    },
  },

  created() {
    // this.client = mqtt.connect("ws://broker.emqx.io:8083/mqtt");
    this.client = mqtt.connect("ws://broker.hivemq.com:8000/mqtt");
    this.client.on("connect", this.onMqttConnect.bind(this));
    this.client.on("message", this.onMqttMessage.bind(this));
    this.client.on("reconnect", this.handleOnReConnect.bind(this));
  },

  beforeDestroy() {
    this.client && this.client.end();
  },

  methods: {
    onMqttConnect(topic, messege) {
      this.client.subscribe("init_revert")
      const payload = JSON.stringify(1);
      this.client.publish("init_revert", payload);

    },
    
    disconnectMqtt() {
      console.log("disconnected");
      this.client.end();
    },

    toggleMqttConnection() {
      if (this.status === "Connected") {
        this.status = "OFFLINE";
        this.mqtt = "#8F8F8F";
        this.icon = "mdi-close-circle-outline";
        this.colorLight1 = "#8F8F8F";
        this.iconLight1 = "mdi-lightbulb-off-outline";
        this.colorLight2 = "#8F8F8F";
        this.iconLight2 = "mdi-lightbulb-off-outline";
        this.colorLight3 = "#8F8F8F";
        this.iconLight3 = "mdi-lightbulb-off-outline";
        this.showTemp = "0";
        this.showHumi = "0";
        this.state = "ระบบปิดอยู่";
        this.status1 = "ระบบปิดอยู่";
        this.status2 = "ระบบปิดอยู่";
        // const payload = JSON.stringify({ "init": "0" });
        const payload = JSON.stringify(0);
        this.client.publish("init_revert", payload);
        } 
        else if (this.status === "OFFLINE") {
          this.status = "Connected";
          this.mqtt = "blue";
          this.client.subscribe("init_revert")
          // const payload = JSON.stringify({ "init": "1" });
          const payload = JSON.stringify(1);
          this.client.publish("init_revert", payload);
        }
    },

    onMqttMessage(topic, message) {
      if (topic === "init_revert") {
        const payload = JSON.parse(message.toString());

        // if (payload.init === "1") {
        if (payload === 1) {
          this.status = "Connected";
          this.mqtt = "blue";
          this.icon = "mdi-checkbox-marked-circle";
          this.colorLight1 = "#51EE5B";
          this.iconLight1 = "mdi-lightbulb-on-outline";
          this.client.subscribe("ctc/temp");
          this.client.subscribe("phanu/Light2");
          this.client.subscribe("phanu/Light3");
          this.client.subscribe("node/datatotal");
        } 
        else if (payload === 0) {
          this.colorLight1 = "#8F8F8F";
          this.iconLight1 = "mdi-lightbulb-off-outline";
          this.client.unsubscribe("ctc/temp");
          this.client.unsubscribe("phanu/Light2");
          this.client.unsubscribe("phanu/Light3");
          this.client.unsubscribe("node/datatotal");
          this.status = "OFFLINE";
          this.mqtt = "red";
          this.icon = "mdi-close-circle-outline";
          this.colorLight1 = "#8F8F8F";
          this.iconLight1 = "mdi-lightbulb-off-outline";
          this.colorLight2 = "#8F8F8F";
          this.iconLight2 = "mdi-lightbulb-off-outline";
          this.colorLight3 = "#8F8F8F";
          this.iconLight3 = "mdi-lightbulb-off-outline";
          this.showTemp = "0";
          this.showHumi = "0";
          this.state = "ระบบปิดอยู่";
          this.status1 = "ระบบปิดอยู่";
          this.status2 = "ระบบปิดอยู่";
        }
      }

      if (topic === "phanu/Light2") {
        const payload = JSON.parse(message.toString());

        if (payload.Light2 === "1") {
          this.colorLight2 = "#51EE5B";
          this.iconLight2 = "mdi-lightbulb-on-outline";
        } else {
          this.colorLight2 = "#8F8F8F";
          this.iconLight2 = "mdi-lightbulb-off-outline";
        }
      }

      if (topic === "phanu/Light3") {
        const payload = JSON.parse(message.toString());

        if (payload.Light3 === "1") {
          this.colorLight3 = "#51EE5B";
          this.iconLight3 = "mdi-lightbulb-on-outline";
        } else {
          this.colorLight3 = "#8F8F8F";
          this.iconLight3 = "mdi-lightbulb-off-outline";
        }
      }

      if (topic === "ctc/temp") {
        try {
          const payload = JSON.parse(message.toString());
          // this.showTemp = parseFloat(payload.temp).toFixed(2);
          // this.showHumi = parseFloat(payload.humi).toFixed(2);
          this.state = parseFloat(payload.state);
        } catch (error) {
          console.error("Failed to parse JSON message:", error);
        }
      }

      if (topic === "node/datatotal") {
        try {
          const payload = JSON.parse(message.toString());
          this.showTemp = parseFloat(payload.temp).toFixed(2);
          this.showHumi = parseFloat(payload.humi).toFixed(2);
          // this.state = parseFloat(payload.state);
        } catch (error) {
          console.error("Failed to parse JSON message:", error);
        }
      }
    },

    sendLineNotify(message) {
      axios.post('http://localhost:3001/send-line-notify', 
        new URLSearchParams({ message }), 
        {
          headers: {
            "Content-Type": "application/x-www-form-urlencoded",
            "Authorization": `Bearer ${this.lineNotifyToken}`
          }
        })
        .then(response => {
          console.log("Line Notify sent successfully:", response.data);
        })
        .catch(error => {
          console.error("Failed to send Line Notify:", error);
        });
    },

    handleOnReConnect() {
      this.retryTimes += 1;
      if (this.retryTimes > 5) {
        try {
          this.client.end();
          this.initData();
          this.$message.error("Connection maxReconnectTimes limit, stop retry");
        } catch (error) {
          this.$message.error(error.toString());
          console.log("Connection failed", error);
          this.status = "Offline";
        }
      }
    },
  },
};
</script>

<style scoped>
.background-container {
  background-color: #f5f5f5;
  padding: 20px;
  border-radius: 10px;
}

.custom-btn {
  font-size: 18px;
  padding: 10px 20px;
  border-radius: 10px;
}

.title-text {
  text-align: center;
  font-size: 2rem;
  font-weight: bold;
  margin: 20px 0;
  color: #333;
}

.flex-card {
  display: flex;
  justify-content: space-between;
  padding: 20px;
  background-color: #fff;
  border-radius: 10px;
}

.btn-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  margin-right: 30px;
}

.light-btn {
  font-size: 16px;
  padding: 10px 20px;
  border-radius: 10px;
  transition: transform 0.3s ease;
}

.light-btn:hover {
  transform: scale(1.05);
}

.status-container {
  display: flex;
  justify-content: space-around;
  align-items: center;
  flex-grow: 1;
}

.progress-container {
  text-align: center;
  margin: 20px;
}

.progress-title {
  font-size: 1.2rem;
  margin-bottom: 15px;
}

.progress-circle {
  margin-bottom: 15px;
}

.center-text {
  text-align: center;
  font-size: 1rem;
  color: #666;
}

.status-card {
  text-align: center;
  font-size: 18px;
  padding: 20px;
  margin-top: 20px;
  background-color: #fff;
  border-radius: 10px;
}

.status-card p {
  margin: 10px 0;
  font-size: 1.2rem;
}
</style>