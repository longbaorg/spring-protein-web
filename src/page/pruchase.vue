<template>
  <div class="people">
    <div class="topButton">
      <el-button size="mini" @click="showAddUser()" type="primary">添加</el-button>

      <el-button size="mini" type="warning" @click="showChange()">修改</el-button>

      <el-button size="mini" type="danger" @click="showDelete()">删除</el-button>
    </div>
    <!-- 表格 -->
    <div class="list">
      <!-- <div class="search">
        <el-input v-model="search" placeholder="请输入内容"></el-input>
      </div>-->

      <el-table
        :data="user.slice((currentPage-1)*pagesize,currentPage*pagesize)"
        style="width: 100%"
        highlight-current-row
        border
        @current-change="handleCurrentsChange"
      >
        <el-table-column label="ID" prop="id"></el-table-column>

        <el-table-column label="名称" prop="name"></el-table-column>

        <el-table-column label="分类" prop="classification"></el-table-column>

        <el-table-column label="生物体" prop="organism"></el-table-column>

        <el-table-column label="表达系统" prop="expressionSystem"></el-table-column>

        <el-table-column label="发布时间" prop="deposited"></el-table-column>
        
        <el-table-column label="发布作者" prop="depositionAuthor"></el-table-column>

        <el-table-column label="氨基酸序列" prop="fastaSequence"></el-table-column>

        <el-table-column label="图片地址" prop="imageUrl"></el-table-column>

        <template slot="header">
          <el-input v-model="search" placeholder="根据名称搜索" />
        </template>
      </el-table>

      <!-- 分页 -->
      <div class="page">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-sizes="[5, 10, 20, 40]"
          :page-size="pagesize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="user.length"
        ></el-pagination>
      </div>
    </div>

    <!-- 添加人员弹框 -->

    <div v-if="flag" class="addUser">
      <div class="title">
        <h2>添加蛋白质</h2>
        <i class="el-icon-close" @click="clossAdd()"></i>
      </div>
      <div class="add">
        <el-form ref="form" :model="addUser" label-width="80px" :rules="rules">
          <el-form-item label="名称">
            <el-input v-model="addUser.name"></el-input>
          </el-form-item>
          <el-form-item label="分类">
            <el-input v-model="addUser.classification"></el-input>
          </el-form-item>
          <el-form-item label="生物体">
            <el-input v-model="addUser.organism"></el-input>
          </el-form-item>
          <el-form-item label="表达系统">
            <el-input v-model="addUser.expressionSystem"></el-input>
          </el-form-item>
          <el-form-item label="发布时间">
            <el-input v-model="addUser.deposited"></el-input>
          </el-form-item>
          <el-form-item label="发布作者">
            <el-input v-model="addUser.depositionAuthor"></el-input>
          </el-form-item>
          <el-form-item label="基酸序列">
            <el-input v-model="addUser.fastaSequence"></el-input>
          </el-form-item>
          <el-form-item label="图片地址">
            <el-input v-model="addUser.imageUrl"></el-input>
          </el-form-item>

          <div class="addButton">
            <el-form-item>
              <el-button type="primary" @click="addPeople()">提交</el-button>
              <el-button @click="addRest()">重置</el-button>
            </el-form-item>
          </div>
        </el-form>
      </div>
    </div>
    <!-- 修改弹框 -->

    <div v-if="flag2" class="changeUser">
      <div class="title">
        <h2>修改信息</h2>
        <i class="el-icon-close" @click="clossChange()"></i>
      </div>

      <div class="change">
        <el-form ref="form" :model="changeList" label-width="80px">
          <el-form-item label="id" hidden>
            <el-input v-model="changeList.id"></el-input>
          </el-form-item>
           <el-form-item label="名称">
            <el-input v-model="changeList.name"></el-input>
          </el-form-item>
          <el-form-item label="分类">
            <el-input v-model="changeList.classification"></el-input>
          </el-form-item>
          <el-form-item label="生物体">
            <el-input v-model="changeList.organism"></el-input>
          </el-form-item>
          <el-form-item label="表达系统">
            <el-input v-model="changeList.expressionSystem"></el-input>
          </el-form-item>
          <el-form-item label="发布时间">
            <el-input v-model="changeList.deposited"></el-input>
          </el-form-item>
          <el-form-item label="基酸序列">
            <el-input v-model="changeList.fastaSequence"></el-input>
          </el-form-item>
          <el-form-item label="图片地址">
            <el-input v-model="changeList.imageUrl"></el-input>
          </el-form-item>
          <div class="addButton">
            <el-form-item>
              <el-button type="primary" @click="changeUser()">提交</el-button>
              <el-button @click="changeRest()">重置</el-button>
            </el-form-item>
          </div>
        </el-form>
      </div>
    </div>

    <!-- 删除弹框 -->
    <div class="delete" v-if="flag3">
      <div class="title">
        <span>提示</span>
        <i class="el-icon-close" @click="closeDelete()"></i>
      </div>
      <div class="main">
        <i class="el-icon-warning"></i>
        <span>此操作将永久删除该数据，是否继续</span>
      </div>
      <div class="deleteButton">
        <el-button size="mini" @click="closeDelete()">取消</el-button>
        <el-button size="mini" type="danger" @click="deleteInfo()">确定</el-button>
      </div>
    </div>

    <div class="popContainer" v-if="flag"></div>
    <div class="popContainer" v-if="flag2"></div>
    <div class="popContainer" v-if="flag3"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      user: [],
      flag: false,
      flag2: false,
      flag3: false,
      flag4:false,
      search: "",
      currentPage: 1, //初始页
      pagesize: 10,
      addUser: [
        {
          id :'',
          name:'',
          classification: "",
          organism: "",
          expressionSystem: "",
          deposited: "",
          depositionAuthor :"",
          fastaSequence: "",
          imageUrl : ""
        }
      ],
      changeList: [
        {
          id : '',
          name:'',
          classification: "",
          organism: "",
          expressionSystem: "",
          deposited: "",
          depositionAuthor :"",
          fastaSequence: "",
          imageUrl : ""
        }
      ],
      deleteList: [
        {
          id : '',
          name :''
        }
      ],
      rules: {
        account: [
          { required: true, message: "请输入账号", trigger: "blur" },
          { max: 10, message: "不能大于10个字符", trigger: "blur" }
        ],
        password: [
          { required: true, message: "请输入密码", trigger: "blur" },
          { max: 10, message: "不能大于10个字符", trigger: "blur" }
        ],
        jobnum: [
          { required: true, message: "请输入电话", trigger: "blur" },
          { max: 10, message: "不能大于10个字符", trigger: "blur" }
        ],
        name: [
          { required: false, message: "请输入姓名", trigger: "blur" },
          { max: 10, message: "不能大于10个字符", trigger: "blur" }
        ],
      }
    };
  },
  created() {
    let _this = this;
    this.$axios
      .get("/proteinInfo/selectAll")
      .then(function(res) {
        if (res.data) {
          _this.user = res.data;
          console.log(res.data)
        }
      })
      .catch(function(err) {
        if (err.response) {
          console.log(err.response);
        }
      });
  },
  inject: ["reload"],
  methods: {
    showAddUser() {
      this.flag = !this.flag;
      console.info("++++"+this.flag)
    },
    addPeople() {
      if(this.addUser.name){
         let _this = this;
         console.info("--"+_this+"--")
      this.$axios
        .post("/proteinInfo/addProteinInfo", {
          name: _this.addUser.name,
          classification: _this.addUser.classification,
          organism: _this.addUser.organism,
          expressionSystem: _this.addUser.expressionSystem,
          deposited: _this.addUser.deposited,
          depositionAuthor: _this.addUser.depositionAuthor,
          fastaSequence: _this.addUser.fastaSequence,
          imageUrl: _this.addUser.imageUrl
        })
        .then(res => {
          if (res.data == "新增成功") {
            this.$message("添加成功");
            this.flag = !this.flag;
            // this.flag2=!this.flag2
            // this.$router.go(0)
            this.reload();
          }else{
             this.$message(res.data)
          }
        })
        .catch(function(err) {
          if (err.response) {
            console.log(err.response);
            this.$message("后台连接失败");
          }
        });
      }else{
        this.$message('请添全信息')
      }
    },
    changeUser() {
      let _this = this;
      this.$axios
        .put("/proteinInfo/updateProteinInfo", {
          id:_this.changeList.id,
          name: _this.changeList.name,
          classification: _this.changeList.classification,
          organism: _this.changeList.organism,
          expressionSystem: _this.changeList.expressionSystem,
          deposited: _this.changeList.deposited,
          depositionAuthor: _this.changeList.depositionAuthor,
          fastaSequence: _this.changeList.fastaSequence,
          imageUrl: _this.changeList.imageUrl
        })
        .then(res => {
          if (res.data == "修改成功") {
            this.$message("修改成功");
            this.flag2 = !this.flag2;
            // this.flag2=!this.flag2
            // this.$router.go(0)
            this.reload();
          }else{
            this.$message(res.data)
          }
        })
        .catch(function(err) {
          if (err.response) {
            console.log(err.response);
            this.$message("后台连接失败");
          }
        });
    },
    deleteInfo() {
      let _this = this;
      console.log(_this.deleteList.name);
      this.$axios
        .delete("/proteinInfo/deleteProteinInfo", {
          data: {
            id: _this.deleteList.id
          }
        })
        .then(res => {
          if (res.data == "删除成功") {
            this.$message("删除成功");
            this.flag3 = !this.flag3;
            this.reload();
          }else{
            this.$message(res.data)
          }
        })
        .catch(err => {
          if (err.response) {
            console.log(err.response);
            this.$message("后台连接失败");
          }
        });
    },

    showDelete() {
      if (this.deleteList.name) {
        this.flag3 = !this.flag3;
      } else {
        this.$message("请选择一条数据");
      }
    },

    clossAdd() {
      this.flag = !this.flag;
      this.$message("操作已取消");
    },
    addRest() {
      this.addUser = [];
    },
    // 点击选中
    setCurrent(row) {
      this.$refs.singleTable.setCurrentRow(row);
    },
    handleCurrentsChange(val) {
      this.currentRow = val;
      this.changeList = val;
      this.deleteList = val;

      // this.changeList = []
    },

    handleSizeChange: function(size) {
      this.pagesize = size;
      console.log(this.pagesize); //每页下拉显示数据
    },
    handleCurrentChange: function(currentPage) {
      this.currentPage = currentPage;
      console.log(this.currentPage); //点击第几页
    },

    clossChange() {
      this.flag2 = !this.flag2;
      this.$message("操作已取消");
    },
    showChange() {
      if (this.changeList.name) {
        this.flag2 = !this.flag2;
      } else {
        this.$message("请选择一条数据");
      }
    },
    closeDelete() {
      this.flag3 = !this.flag3;
      this.$message("操作已取消");
    },
    changeRest() {
      this.changeList = [];
    },
    mShow(){
       this.flag4 = !this.flag4;
    }
  }
};
</script>

<style>
.topButton {
  width: 100%;
  height: 50px;
  line-height: 50px;
  padding-bottom: 20px;
}
.el-table-column {
  text-align: center;
}
.el-submenu .el-menu-item:hover {
  background-color: rgb(232, 243, 255);
}
.list {
  position: relative;
}
.addUser {
  position: absolute;
  z-index: 1001;
  width: 650px;
  height: 750px;
  left: 0;
  top: 0;
  bottom: 0;
  right: 0;
  margin: auto;
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0 15px 25px rgba(0, 0, 0, 0.5);
}
.addUser .title {
  height: 50px;
  width: 100%;
  text-align: center;
  line-height: 30px;
  /* border-bottom: 1px solid #dcdfe6; */
  color: #409eff;
  text-shadow: 2px 2px 2px #ccc;
}
.addUser .title i {
  position: relative;
  top: -55px;
  right: -170px;
  color: black;
  font-size: 20px;
}

.addUser .add {
  margin-right: 40px;
  margin-left: 50px;
  margin-top: 30px;
}



.addUser .addButton {
  margin-left: 100px;
  margin-top: 30px;
}
.addUser .addButton .el-button {
  width: 110px;
}
.addUser .el-select {
  width: 280px;
}
.changeUser {
  position: absolute;
  z-index: 1001;
  width: 400px;
  height: 500px;
  /* border:1px solid #dcdfe6; */
  left: 0;
  top: -50px;
  bottom: 0;
  right: 0;
  margin: auto;
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0 15px 25px rgba(0, 0, 0, 0.5);
}
.changeUser .title {
  height: 50px;
  width: 100%;
  text-align: center;
  line-height: 30px;
  /* border-bottom: 1px solid #dcdfe6; */
  color: #409eff;
  text-shadow: 2px 2px 2px #ccc;
}
.changeUser .title i {
  position: relative;
  top: -55px;
  right: -170px;
  color: black;
  font-size: 20px;
}
.changeUser .change {
  margin-right: 40px;
  margin-top: 30px;
}
.changeUser .addButton .el-button {
  width: 115px;
}
.changeUser .el-select {
  width: 280px;
}
.delete {
  position: absolute;
  z-index: 1001;
  width: 430px;
  height: 140px;
  border: 1px solid #dcdfe6;
  left: 0;
  top: -50px;
  bottom: 0;
  right: 0;
  margin: auto;
  background-color: #fff;
  border-radius: 10px;
}
.title {
  width: 100%;
  height: 45px;
  line-height: 45px;
}
.title span {
  float: left;
  margin-left: 20px;
}
.delete .title i {
  float: right;
  margin-top: 10px;
  margin-right: 10px;
}
.delete .main {
  height: 50px;
  width: 100%;
  line-height: 50px;
  margin-left: 50px;
}
.delete .main i {
  color: #e6a23c;
  font-size: 20px;
  margin-right: 10px;
}
.delete .main span {
  margin-bottom: 5px;
}
.delete .deleteButton {
  float: right;
  margin-right: 20px;
  margin-top: 7px;
}

.popContainer {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  z-index: 500;
}
.page {
  position: absolute;
  right: 0;
  bottom: -50px;
}
.search {
  position: relative;
  top: -12px;
}
</style>