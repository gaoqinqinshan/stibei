<template>
  <div class="main">
    <div class="header">
      <div class="logo">
        <a href="https://www.cqyti.com/" target="_blank">
          <img src="../assets/sport.png" style="width: 60px;height: 60px"/>
        </a>
        <div style="height: 60px;background: linear-gradient(to right, white, #737c83);">
          <router-link to="/index" style="text-decoration: none">
            <h3 style="color: #3a8ee6;letter-spacing: 3px;">大学生健康管理系统</h3>
          </router-link>
        </div>        
      </div>
      <div style="width: 500px;height: 60px;">
        <span style="color:white;letter-spacing: 5px"></span>
      </div>
      <div class="right">
        <el-dropdown style="float: right;">
                    <span style="color: #ccc;">
                        {{ user.name }}({{ roleData.roleName }})<i class="el-icon-arrow-down el-icon--right"></i>
                    </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item @click.native="updatePassword"><i style="padding-right: 8px" class="el-icon-share"></i>修改密码
            </el-dropdown-item>
            <el-dropdown-item @click.native="logout"><i style="padding-right: 8px" class="el-icon-star-off"></i>退出系统
            </el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
        <div class="time-box">
          <span class="time-time" v-text="datetime.time">12:00:00</span>
        </div>
      </div>
    </div>
    <div class="app">
      <div class="aside">
        <div class="menu">
          <h1 style="padding: 60px 0;">大学生健康管理系统</h1>

          <div class="menu-body">
            <div class="item1" v-for="(item,index) in generateMenu" :style="{'background-color': item.color}"
                 :key="index">
              <div class="item-div" @click="toDetail(item.url)">
                <!-- <img :src="item.pic" style="width: 100px;height: 100px;"/> -->
                <i :class="item.icon"></i>
                <h3>{{ item.name }}</h3>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- echart  -->
      <div class="echart" id="mychart" :style="myChartStyle"></div>
      </div>
    <el-dialog title="修改密码" :visible.sync="addDialogVisible"
               width="30%">
      <el-form :model="userForm" ref="userForm" style="padding-right: 20px" :rules="rules" label-width="20%">
        <el-form-item label="用户名">
          <el-input v-model="userForm.username" placeholder="用户名"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="loginPassword">
          <el-input type="password" v-model="userForm.password" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="确认密码" prop="checkPass">
          <el-input type="password" v-model="userForm.checkPass" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="info" @click="updateUser(userForm)">确认</el-button>
          <el-button @click="addDialogVisible=false">取消</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
    
  </div>
</template>
<!-- <template>
  <div class="block">
    <el-slider
      v-model="value1"
      vertical
      height="200px">
    </el-slider>
  </div>
</template> -->

<!-- <script>
  export default {
    data() {
      return {
        value: 0
      }
    }
  }
</script> -->
<script>
import Message from '../components/messages/index'
import Color from '../mock/color.js'
import * as echarts from "echarts";
export default {
  data() {
    var validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入密码'));
      } else {
        if (this.userForm.checkPass !== '') {
          this.$refs.userForm.validateField('checkPass');
        }
        callback();
      }
    };
    var validatePass2 = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请再次输入密码'));
      } else if (value !== this.userForm.password) {
        callback(new Error('两次输入密码不一致!'));
      } else {
        callback();
      }
    };
    return {
      key_1: '',
      value1: 0,
      myChart: {},
      pieData: [
        {
          value: 463,
          name: "通信与工程学院"
        },
        {
          value: 395,
          name: "大数据工程学院"
        },
        {
          value: 157,
          name: "人工智能学院"
        },
        {
          value: 149,
          name: "数字经济与管理学院"
        },
        {
          value: 147,
          name: "淬炼商学院"
        }
      ],
      pieName: [],
      myChartStyle: {width: "100%", height: "700px"}, //图表样式

      menus: [],
      addDialogVisible: false,
      isCollapse: false,
      userInfo: {},
      uniqueOpened: true,
      rules: {
        pass: [
          {validator: validatePass, trigger: 'blur'}
        ],
        checkPass: [
          {validator: validatePass2, trigger: 'blur'}
        ]
      },
      userForm: {
        account: '',
        password: '',
        checkPass: ''
      },
      menu: [],
      datetime: {
        date: "",
        time: ""
      },
      role: "",
      user: JSON.parse(localStorage.getItem("user")),
      // roleData:JSON.parse(localStorage.getItem("role"))
      // user:{},
      roleData: {}
    }
  },
  mounted() {
    this.initDate(); //数据初始化
    this.initEcharts();
  },
  
  methods: {
    initDate() {
      for (let i = 0; i < this.pieData.length; i++) {
        // this.xData[i] = i;
        // this.yData =this.xData[i]*this.xData[i];
        this.pieName[i] = this.pieData[i].name;
      }
    },
    initEcharts() {
      // 饼图
      const option = {
        title: {
          // 设置饼图标题，位置设为顶部居中
          text: "院内前五bmi图示",
          top: "10%",
          left: "center"
        },
        series: [
          {
            type: "pie",
            label: {
              show: true,
              formatter: "{b} : {c} ({d}%)" // b代表名称，c代表对应值，d代表百分比
            },
            radius: "60%", //饼图半径
            data: this.pieData
          }
        ]
      };
      console.log(this.seriesData);
      // const optionFree = {
      //   xAxis: {},
      //   yAxis: {},
      //   series: [
      //     {
      //       data: this.seriesData,
      //       type: "line",
      //       smooth: true
      //     }
      //   ]
      // };
      this.myChart = echarts.init(document.getElementById("mychart"));
      this.myChart.setOption(option);
      //随着屏幕大小调节图表
      window.addEventListener("resize", () => {
        this.myChart.resize();
      });
    },
    // test(){
    //   console.log("ttt")
    //   this.$http.get("/checkInfo/test").then(res=>{
    //     this.key_1 = res.key
    //   })
    // },
    async logout() {
      this.$http.get("/user/loginOut").then(res => {
        if (res.status === 200) {
          Message.success(res.data);
          localStorage.clear();
          this.$router.push("/login")
        }
      })
    },

    handleOpen() {
    },
    handleClose() {
    },
    updatePassword() {
      this.addDialogVisible = true;
      this.userForm = JSON.parse(localStorage.getItem("user"));
    },
    async updateUser(data) {
      let self = this;
      this.$http.post("/user/updateUser", data).then(res => {
        if (res.status === 200) {
          Message.success("修改成功");
          localStorage.clear();
          this.$router.push("/login");
          self.addDialogVisible = false;
        }
      });

    },
    getDatetime() {
      setInterval(() => {
        let date = new Date();
        this.datetime.date = date.getFullYear() + "-" + ((date.getMonth() + 1) < 10 ? "0" + (date.getMonth() + 1) : (date.getMonth() + 1)) + "-" + (date.getDate() < 10 ? "0" + date.getDate() : date.getDate());
        this.datetime.time = (date.getHours() < 10 ? "0" + date.getHours() : date.getHours()) + ":" + (date.getMinutes() < 10 ? "0" + date.getMinutes() : date.getMinutes()) + ":" + (date.getSeconds() < 10 ? "0" + date.getSeconds() : date.getSeconds());
      }, 1000);
    },
    toDetail(data) {
      this.$router.push(data);
    },
    getMenuByUserId() {
      this.$http.get("/resources/getMenuByUserId", {
        params: {
          "userId": this.user.id,
          "typeId": 0
        }
      }).then(res => {
        if (res.status === 200) {
          // localStorage.setItem("menu", JSON.stringify(res.data));
          // self.loadingInstance.close();
          console.log("data", res.data)
          this.menu = res.data;
        }
      })
    },
    getRoleDate() {
      this.$http.get("/role/get/" + this.user.roleId).then(res => {
        if (res.status === 200) {
          console.log("当前用户权限======", res)
          this.roleData = res.data;
        }
      });
    }
  },
  created() {
    this.getDatetime();
    this.getMenuByUserId();
    this.getRoleDate();
  },
  computed: {
    generateMenu: function () {
      // let roleData = JSON.parse(localStorage.getItem("role"));
      // let Menu = JSON.parse(localStorage.getItem("menu"));
      let roleData = this.roleData;
      let Menu = this.menu;
      console.log(this.menu);
      Menu.forEach((m) => {
        if (roleData.roleName === '辅导员' && m.name === '用户信息管理') {
          m.name = "学生信息管理"
        }
        m.color = Color[Math.floor((Math.random() * Color.length))];
      });
      return Menu;
    }
  }

}
</script>
<style lang="less">
.iconfont {
  font-size: 80px;
}

.main {
  display: flex;
  text-align: center;
  height: 100%;

  .el-menu:not(.el-menu--collapse) {
    width: 100%;
  }

  .el-menu {
    background-color: rgba(236, 245, 255, 0.5);
    /*font-family: "Microsoft YaHei";*/
    font-weight: bold;
    font-size: 28px;
    color: #E5FCFF;
    letter-spacing: 5px;
    text-shadow: 0 0 8px #FFF, 0 0 42px #FFF, 0 0 72px #FFF, 0 0 150px #FFF;
  }

  .app {
    width: 100%;
    background-color: #E2F1FF;
  }

  .aside {
    width: 100%;
    margin-top: 60px;
    z-index: 10;
    transition: all 0.3s ease-in-out;
    height: calc(100% - 60px);
    overflow-x: auto;

    .menu {
      background: url("../assets/school.jpg");
      background-size: cover;
      display: flex;
      flex-direction: column;
      align-items: center;
      width: 100%;
      font-size: 20px;
      height: 100%;

      .menu-body {
        height: 100%;
        width: 1200px;
        flex-direction: row;
        display: flex;
        flex-wrap: wrap;
        align-content: flex-start;
        justify-content: center;

        div:nth-child(n+6) {
          width: 388px;
          height: 200px;
        }

        .item1 {
          width: 228px;
          height: 200px;
          margin: 5px;
          transition: .3s;
          cursor: pointer;
          border-radius: 5px;

          &:hover {
            box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
          }

          .item-div {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            width: 100%;
            height: 100%;
            color: #000;
            text-decoration: none;
          }
        }
      }
    }
  }
}

.header {
  width: 100%;
  position: fixed;
  display: flex;
  height: 60px;
  line-height: 60px;
  background-color: #737c83;
  justify-content: space-between;
  z-index: 10;

  .logo {
    display: flex;
    flex-direction: row;
    width: 300px;
    height: 60px;
    text-align: center;
    line-height: 60px;
    color: #FFF;
    font-weight: 600;
    -webkit-transition: width 0.35s;
    transition: all 0.3s ease-in-out;
  }

  .right {
    img {
      height: 48px;
      width: 48px;
      border-radius: 50%;
    }
  }

  .time-box {
    display: flex;
    flex-direction: column;
    float: right;

    .time-time {
      font-size: 20px;
      color: #66bdff;
      letter-spacing: 1.66px;
    }
  }

}


::-webkit-scrollbar {
  width: 5px;
  height: 5px;
  background-color: #F5F5F5;
}

/*定义滚动条轨道 内阴影+圆角*/
::-webkit-scrollbar-track {
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  border-radius: 10px;
  background-color: #F5F5F5;
}

/*定义滑块 内阴影+圆角*/
::-webkit-scrollbar-thumb {
  border-radius: 10px;
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, .3);
  background-color: #bdbdbd;
}

/*滑块效果*/
::-webkit-scrollbar-thumb:hover {
  border-radius: 5px;
  -webkit-box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
  background: rgba(0, 0, 0, 0.4);
}

/*IE滚动条颜色*/
html {
  scrollbar-face-color: #bfbfbf; /*滚动条颜色*/
  scrollbar-highlight-color: #000;
  scrollbar-3dlight-color: #000;
  scrollbar-darkshadow-color: #000;
  scrollbar-Shadow-color: #adadad; /*滑块边色*/
  scrollbar-arrow-color: rgba(0, 0, 0, 0.4); /*箭头颜色*/
  scrollbar-track-color: #eeeeee; /*背景颜色*/
}

</style>
