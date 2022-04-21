<template>
  <!-- <div id="app">
    <router-view />
  </div> -->
  <div>
    <div class="login" >
      <div class="login-title">登录</div>
      <div class="login-all">
        <div class="input-title">用户名</div>
        <input type="text" v-model.trim="user.user" class="input-all" @blur="userblur" placeholder="请输入用户名">
        <div class="error" v-show="showUser">*用户名不能为空</div>
      </div>
      <div class="login-all">
        <div class="input-title">密码</div>
        <input type="password" v-model.trim="user.password" class="input-all" placeholder="请输入密码" @blur="userPassword" autocomplete="new-password">
        <div class="error" v-show="showPassword">*密码不能为空</div>
      </div>
      <div class="login-all">
        <div class="input-title">邮箱</div>
        <input type="email" v-model.trim="user.email" class="input-all" placeholder="请输入邮箱" @blur="userEmail">
        <div class="error" v-show="showEmail">*邮箱不能为空</div>
      </div>
      <div class="login-all">
        <div class="input-title">手机号</div>
        <input type="text" v-model.trim="user.number" class="input-all" @blur="userNumber" placeholder="请输入手机号">
        <div class="error" v-show="showNumber">*手机号不能为空</div>
      </div>
      <div class="login-all" >
        <div class="input-title">性别</div>
        <select class="input-all" v-model="user.age">
          <option value="0" class="input-age">女</option>
          <option value="1" class="input-age">男</option>
        </select>
      </div>
      <div class="login-all">
        <button class="input-submit" @click="btnClick">
          <span v-show="updateuser">新增用户</span>
          <span v-show="!updateuser">修改用户</span>
        </button>
        <!-- <button class="input-submit" @click="btnClick2" v-show="!updateuser">修改用户</button> -->
      </div>
    </div>
    <div class="userlist">
      <div class="search">
        <i class="iconfont icontuding">&#xe632;</i>
        <input type="text" @input="usersearch" v-model="search" placeholder="请输入用户名搜索">
      </div>
      <div class="userinfo" v-if="userlistpage.length">
        <table class="table" >
          <tbody class="table-tbody" >
            <tr class="user-title">
              <td>id</td>
              <td>用户名</td>
              <td class="email">邮箱</td>
              <td>手机号</td>
              <td>性别</td>
              <td>操作</td>
            </tr>
            <tr v-for="(item, index) in userlistpage" :key="index" class="userlist-item">
              <td>{{index + ((page - 1) * 10) + 1}}</td>
              <td>{{item.user}}</td>
              <td>{{item.email}}</td>
              <td>{{item.number}}</td>
              <td>{{item.age == '1' ? '男' : '女'}}</td>
              <td><span @click="deleteuser(item.user,item.id)" class="span">删除</span>/<span @click="updateuser2(item.id)" class="span">修改</span></td>
            </tr>
          </tbody>
        </table>
        <div class="page">
          <span class="page-count" v-if="!search">一共{{userlist.length}}名用户</span>
          <span class="page-count" v-if="search">一共{{userlistinfo.length}}名用户</span>
          <button class="btn" @click="upperpage">上一页</button>
          <span v-for="item in pagecount" :key="item" class="page-item" :class="{back:page == item}" @click="userpage(item)">{{item}}</span>
          <button class="btn" @click="nextpage">下一页</button>
          <span class="page-count">当前页数{{page}}/{{pagecount}}</span>
      </div>
      </div>
      <div v-else class="nouser">
        无用户
      </div> 
    </div>
  </div>
</template>

<script>
export default {
  name: 'index',
  data () {
    return {
      id: 0,
      search: '',
      userid: 0,
      page: 1,
      pagecount: 1,
      updateuser: true,
      showUser: false,
      showPassword: false,
      showEmail: false,
      showNumber: false,
      user: {
        id: 0,
        user: '',
        password: '',
        email: '',
        number: '',
        age: 0
      },
      userlist: [],
      userlistpage: [],
      userlistinfo: []
    }
  },
  created(){
    this.user.age = 0
  },
  watch: {
   userlist: {
      deep: true,
      handler(val) {
        this.handlerall(val)
      }
    }
  },
  methods: {
    // jhh user是否为空
    userblur(){
      // console.log(this.user.user)
      this.showUser = this.user.user == undefined || this.user.user == '' ? true : false
    },
    // jhh password是否为空
    userPassword(){
      this.showPassword = this.user.password == undefined || this.user.password == '' ? true : false
    },
    // jhh email是否为空
    userEmail(){
      this.showEmail = this.user.email == undefined || this.user.email == '' ? true : false
    },
    // jhh number是否为空
    userNumber(){
      this.showNumber = this.user.number == undefined || this.user.number == '' ? true : false
    },
    //新增修改
    btnClick(){
      // jhh 输入框有空
      this.userblur()
      this.userPassword()
      this.userEmail()
      this.userNumber()
      // jhh 如果没空添加数据
      if(!this.showUser && !this.showPassword && !this.showEmail && !this.showNumber){
        //判断是新增还是修改
        if(this.updateuser){
          this.search = ''
          this.user.id = this.id++
          // jhh this.user指向同一个内存地址，需要复制这个对象再push
          this.userlist.push(JSON.parse(JSON.stringify(this.user)))
          // alert('新增成功')
          // console.log(this.userlist)
        }else{
          let status = false 
          console.log(this.userid)
          this.userlist.forEach((val, index) => {
            if(val.id == this.userid){
              this.user.id = val.id
              this.userlist.splice(index,1,JSON.parse(JSON.stringify(this.user)))
              alert('修改成功')
              status = true
            }
          })
          if(!status){
            alert('要修改的用户不存在')
          }
          this.search = ''
          this.updateuser = true
          this.user = {
            id: 0,
            user: '',
            password: '',
            email: '',
            number: '',
            age: 0
          }
        }
      }
    },
    deleteuser(user,index){
      if(confirm('确认删除用户' + user + '吗')){
        // let val = this.search ? this.userlistinfo : this.userlist
        this.userlist.splice(index,1)
        this.search = ''
        alert('删除成功')
      }
    },
    updateuser2(index){
      this.userid = index
      this.user.user = this.userlist[index].user
      this.user.password = this.userlist[index].password
      this.user.email = this.userlist[index].email
      this.user.number = this.userlist[index].number
      this.user.age = this.userlist[index].age
      this.updateuser = false
    },
    pagination(pageNo, pageSize, array) {
      var offset = (pageNo - 1) * pageSize;
      // console.log(offset)
      return (offset + pageSize >= array.length) ? array.slice(offset, array.length) : array.slice(offset, offset + pageSize)
    },
    userpage(item){
      this.page = item
      let val = this.search ? this.userlistinfo : this.userlist
      this.userlistpage = this.pagination(this.page,10,val)
    },
    nextpage(){
      if(this.page < this.pagecount){
        this.page += 1
        let val = this.search ? this.userlistinfo : this.userlist
        this.userlistpage = this.pagination(this.page,10,val)
      }
    },
    upperpage(){
      if(this.page > 1){
        this.page -= 1
        let val = this.search ? this.userlistinfo : this.userlist
        this.userlistpage = this.pagination(this.page,10,val)
      }
    },
    usersearch(){
      this.userlistinfo = []
      this.userlist.forEach(val => {
        if(val.user.indexOf(this.search) > -1){
          this.userlistinfo.push(val)
        }
      })
      this.handlerall(this.userlistinfo)
    },
    handlerall(val) {
      this.page = 1
        this.pagecount = Math.ceil(val.length / 10)
        this.userlistpage = this.pagination(this.page,10,val)
        // console.log(this.pagecount)
    }
  }
}
</script>
<style>
.userlist{
    float:left;
    width:700px;
    margin-top:60px;
    background:rgb(233, 233, 233);
    padding:10px 0;
    height:535px;
  }
  .userinfo{
    height:535px;
    position:relative;
  }
  .search{
    margin-bottom:50px;
  }
  .table{
    margin:0 auto;
    border-collapse:collapse;
    table-layout: fixed;
    width:600px;
  }
  .user-title{
    background:rgb(124, 124, 124);
    border:1px solid rgb(167, 167, 167);
    color:rgb(255, 255, 255);
    width:100%;
  }
  .email{
    width:180px;
  }
  td{
    border:1px solid rgb(167, 167, 167);
    text-align:center;
    padding:5px 0;
    overflow:hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    cursor:pointer;
  }
  .userlist-item{
    background:white;
  }
  .span{
    color:rgb(96, 206, 242);
  }
  .nouser{
    width:700px;
    height:400px;
    display: flex;
    font-size:18px;
    justify-content: center;
    align-items: center;
  }
  .btn{
    border:none;
    background:white;
    border-radius:5px;
    margin:0 10px;
  }
  .page{
    position:absolute;
    left:0;
    right:0;
    bottom:100px;
    text-align:center;
  }
  .page-item{
    padding:0 5px;
  }
  .page-count{
    font-size:14px;
  }
  .back{
    background:rgb(72, 212, 255);
    border-radius:3px;
  }
  .login{
    float:left;
    width:400px;
    border:1px solid rgb(219, 219, 219);
    text-align:center;
    margin:60px 5%;
    border-radius:10px;
    box-shadow:0px 10px 10px rgb(175, 175, 175);
  }
  .login-title{
    font-size:30px;
    font-weight:blod;
    padding:30px 0 0 0;
  }
  .login-all{
    padding:20px 0 0 0;
  }
  .input-title{
    text-align:left;
    margin-left:50px;
    color:rgb(172, 172, 172);
  }
  .input-all{
    width:300px;
    padding:10px 0;
    outline:none;
    border:1px solid rgb(159, 159, 159);
    border-radius:5px;
  }
  .input-all::-webkit-input-placeholder{
    color: #CCCCCC;
  }
  .input-submit{
    padding:12px 60px;
    background:rgb(72, 132, 253);
    border:none;
    border-radius:6px;
    margin-bottom:10px;
    color:white;
  }
  .error{
    text-align:left;
    margin-left:50px;
    font-size:10px;
    color:red;
  }
  .search{
    text-align:center;
    position:relative;
  }
  .search input{
    width:500px;
    height:30px;
    border:none;
    border-radius:5px;
    padding:0px 0px 0px 25px;
    outline:none;
  }
  .icontuding{
    position:absolute;
    left:93px;
    top:6px;
  }
</style>