<template>
  <v-app>
    <v-card>
      <v-layout>
        <!-- app-bar -->
        <v-app-bar color="teal-darken-4" image="https://picsum.photos/1920/1080?random">
          <template v-slot:image>
            <v-img gradient="to top right, rgba(50, 149, 255, 0.8), rgba(0, 190, 255, 0.8)"></v-img>
          </template>          

          <template v-slot:prepend>
            <v-app-bar-nav-icon></v-app-bar-nav-icon>
          </template>
          <v-app-bar-title>Title</v-app-bar-title>
          <v-spacer></v-spacer>
          <v-btn icon>
            <v-icon>mdi-magnify</v-icon>
          </v-btn>
          <v-btn icon>
            <v-icon>mdi-heart</v-icon>
          </v-btn>
          <v-btn icon>
            <v-icon>mdi-dots-vertical</v-icon>
          </v-btn>
        </v-app-bar>
        <!-- navigation-drawer -->
        <v-navigation-drawer v-model="drawer" :rail="rail" permanent @click="rail = false">
          <v-list-item prepend-avatar="\assets\images\คน.jpg" title="aumaporn thongprasri" nav>
            <template v-slot:append>
              <v-btn icon="mdi-chevron-left" variant="text" @click.stop="rail = !rail"></v-btn>
            </template>
          </v-list-item>
          <v-divider></v-divider>

          <v-list density="compact" nav>
            <v-list-item prepend-icon="mdi-home" title="หน้าหลัก" value="home" @click="main"></v-list-item>

            <v-list-item prepend-icon="mdi-account-group" title="แสดงข้อมูลผู้ใช้" value="home" @click="data"></v-list-item>

            <v-list-item prepend-icon="mdi-view-dashboard" title="หน้าควบคุม" value="home" @click="dashboard"></v-list-item>

            <v-list-item prepend-icon="mdi-information" title="เกี่ยวกับเรา" value="home" @click="about"></v-list-item>

            <v-list-item prepend-icon="mdi-email" title="ติดต่อเรา" value="home" @click="conn"></v-list-item>

            <v-list-item prepend-icon="mdi-logout" title="Logout" value="users" @click="logOut"></v-list-item>
          </v-list>
        </v-navigation-drawer>

        <v-main>
          <NuxtPage></NuxtPage>
        </v-main>

        <v-bottom-navigation v-model="value" :bg-color="color" mode="shift">
          <v-btn>
            <v-icon>mdi-television-play</v-icon>
            <span>Video</span>
          </v-btn>

          <v-btn>
            <v-icon>mdi-music-note</v-icon>
            <span>Music</span>
          </v-btn>

          <v-btn>
            <v-icon>mdi-book-open-page-variant</v-icon>
            <span>Book</span>
          </v-btn>

          <v-btn>
            <v-icon>mdi-image</v-icon>
            <span>Image</span>
          </v-btn>
        </v-bottom-navigation>
      </v-layout>
    </v-card>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      drawer: true,
      rail: true,
    };
  },
  methods: {
    // กดแล้วเชื่อมไปหน้าต่างๆได้
    main() {
      this.$router.replace("/home");
    },

    data() {
      this.$router.replace("/data_table");
    },

    dashboard() {
      this.$router.replace("/dashboard");
    },

    about() {
      this.$router.replace("/about");
    },

    conn() {
      this.$router.replace("/connect");
    },

  
    // ล็อกเอาท์
    logOut() {
      window.sessionStorage.clear();
      this.$router.replace("/login");
    },
  },
};
</script>

<style scoped>
/* สีสำหรับแถบด้านบน (App Bar) */
.v-app-bar {
  background: linear-gradient(135deg, #2965c0, #6a11cb); /* ใช้ gradient เพื่อเพิ่มความน่าสนใจ */
  color: #FFFFFF; /* สีขาว */
  box-shadow: 0px 4px 12px rgba(0, 0, 0, 0.3); /* เงาเพิ่มมิติ */
}

/* Navigation Drawer */
.v-navigation-drawer {
  background-color: #2c3e50; /* เปลี่ยนเป็นสีโทนเข้มเพื่อความหรูหรา */
  color: #ecf0f1; /* สีเทาสว่าง */
  border-right: 1px solid rgba(255, 255, 255, 0.1); /* เพิ่มเส้นแบ่งเบาๆ */
}

.v-navigation-drawer .v-list-item:hover {
  background-color: #34495e !important; /* เพิ่มความชัดเจนเมื่อ hover */
  border-radius: 8px; /* เพิ่มความโค้งมน */
  transition: background-color 0.3s ease; /* เพิ่ม animation */
}

/* สีของปุ่มใน App Bar */
.v-btn {
  background-color: #3498db !important; /* สีน้ำเงินสว่าง */
  color: #FFFFFF !important; /* สีขาว */
  border-radius: 50px; /* เพิ่มความโค้งของปุ่ม */
  transition: transform 0.3s ease; /* Animation */
}

.v-btn:hover {
  transform: scale(1.05); /* ขยายเล็กน้อยเมื่อ hover */
  box-shadow: 0px 4px 12px rgba(0, 0, 0, 0.3); /* เพิ่มเงา */
}

/* Bottom Navigation */
.v-bottom-navigation {
  background: linear-gradient(135deg, #27ae60, #2ecc71); /* Gradient สีเขียวสด */
  color: #FFFFFF; /* สีขาว */
  border-top: 1px solid rgba(255, 255, 255, 0.2); /* เส้นแบ่งสีขาวจางๆ */
}

.v-bottom-navigation .v-btn {
  color: #FFFFFF !important; /* สีขาวสำหรับไอคอน */
}

.v-bottom-navigation .v-btn:hover {
  background-color: rgba(255, 255, 255, 0.1); /* เพิ่มสีเมื่อ hover */
  border-radius: 50%; /* ปุ่มโค้งมนเมื่อ hover */
}

/* ไอคอนในแถบเครื่องมือ */
.v-icon {
  color: #FFFFFF !important; /* สีขาวสำหรับไอคอน */
}

/* รายการใน Navigation Drawer */
.v-list-item {
  color: #FFFFFF !important; /* สีขาวสำหรับรายการ */
  transition: background-color 0.3s ease;
}

/* ปุ่ม Navigation Drawer */
.v-list-item:hover {
  background-color: #34495e !important; /* เพิ่มสีเมื่อ hover */
  border-radius: 10px; /* รูปทรงโค้งมน */
  box-shadow: 0px 3px 6px rgba(0, 0, 0, 0.2); /* เงาเบาๆ */
}

/* แถบ App Bar Icon */
.v-app-bar-nav-icon {
  background-color: rgba(255, 255, 255, 0.1); /* เพิ่มความโปร่งแสงเล็กน้อย */
  border-radius: 50%; /* รูปทรงกลม */
  transition: background-color 0.3s ease;
}

.v-app-bar-nav-icon:hover {
  background-color: rgba(255, 255, 255, 0.3); /* เพิ่มความเด่นชัดเมื่อ hover */
}

/* ปรับรูปแบบ v-list-item avatar */
.v-list-item[prepend-avatar] {
  border-radius: 50%;
  overflow: hidden;
}

.v-list-item[prepend-avatar] img {
  border-radius: 50%; /* ให้รูปภาพใน Avatar เป็นวงกลม */
}

/* ปรับให้ v-list-item title มีขนาดใหญ่และเป็นตัวหนา */
.v-list-item[title] {
  font-size: 1.2rem;
  font-weight: bold;
}

/* ปุ่ม Logout */
.v-list-item[prepend-icon="mdi-logout"] {
  background-color: #e74c3c !important; /* สีแดงเมื่อ logout */
  color: #FFFFFF !important;
  border-radius: 8px;
}

.v-list-item[prepend-icon="mdi-logout"]:hover {
  background-color: #c0392b !important;
}

/* System Bar */
.v-system-bar {
  background-color: #2c3e50 !important; /* ใช้สีเข้มเพื่อความมีสไตล์ */
  color: #ecf0f1 !important; /* สีขาวที่สว่างกว่า */
}


</style>
