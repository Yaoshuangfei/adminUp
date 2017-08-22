<template>
  <section>
    <el-form  label-width="180px" style="margin-left: 40px;margin-top: 40px">
      <el-form-item label="展示位置">
        <el-select v-model="value" placeholder="请选择">
            <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="上传">
        <div>视频格式为avi/mp4等，大小不超过50M</div>
            <input type="file" style="position:absolute;opacity:0;width:70px;height:30px;margin-right:10px"  @change="upload" id="fileInput">
              <el-button type="button" class="el-button el-button--primary el-button--small">
                <span >点击上传</span>
              </el-button>
      </el-form-item>
      <el-col :span="24" style="padding-bottom: 20px;">
        <el-col :span="3"  style="margin-right: 60px">
          <video v-if="url !== '' "  width="220" height="240" controls="controls">
              <source :src="url" type="video/mp4" />
          </video>
          <!-- <video style="width:100px;" :src="url" type="video/mp4"></video> -->
          <!-- <el-button type="text" @click="onSubmit">查看</el-button> -->
          <!-- <el-button type="text" @click="onSubmit">上传</el-button> -->
          <!-- <el-button type="text" @click="onSubmit">取消</el-button> -->
        </el-col>
      </el-col>
      <el-button type="primary" @click="uploadBtn">确认上传</el-button>
      <el-col :span="24" style="padding-bottom: 20px;margin-top: 20px">展示的视频</el-col>
      <el-col :span="24" style="padding-bottom: 20px;height: 300px">
        <el-col :span="3"  style="margin-right: 60px" v-for="item in urlArry">
          <video v-if="historyArry !== [] "  width="220" height="240" controls="controls"><!-- autoplay="autoplay" 直接播放 -->
              <source :src="item" type="video/mp4" />
          </video>
        </el-col>
      </el-col>
      <el-col :span="24" style="padding-bottom: 20px;">历史上传的视频</el-col>
      <el-col :span="24" style="padding-bottom: 20px;">
        <el-col :span="3"  style="margin-right: 60px" v-for="item in urlArry">
          <video v-if="urlArry !== [] "  width="220" height="240" controls="controls"><!-- autoplay="autoplay" 直接播放 -->
              <source :src="item" type="video/mp4" />
          </video>
          <!-- <video style="width:100px;" :src="url" type="video/mp4"></video> -->
          <!-- <el-button type="text" @click="onSubmit">查看</el-button> -->
          <!-- <el-button type="text" @click="onSubmit">上传</el-button> -->
          <!-- <el-button type="text" @click="onSubmit">取消</el-button> -->
        </el-col>
      </el-col>
  </el-form>

  <el-col v-if="baonian" :span="24" style="width: 100%;height: 1000px;position: absolute;top:0;background: rgba(0,0,0,.5);">
        <el-col :span="8" style="margin-top:10%;margin-left: 26%;height: 300px;width: 600px;background: #fff;border-radius: 5px">
            <el-col :span="12" :offset="8" style="margin-top: 88px;margin-bottom: 40px"><h2>是否使用上传视频功能</h2></el-col>
            <el-col :span="24">
                <el-col :span="12" :offset="4"><el-button type="primary" @click="oneBtn">试用一个月</el-button></el-col>
                <el-col :span="5"><el-button type="primary" @click="storeVideo">包年888元</el-button></el-col>
            </el-col>
        </el-col>
        <el-col v-if="passIval" :span="24" style="margin-top:-11%;margin-left: 31%;height: 150px;width: 410px;background: #eee;border-radius: 5px">
            <el-col :span="24" style="margin-top: 40px;margin-left: 70px">
                <el-col :span="5">支付密码：</el-col>
                <el-col :span="10"><el-input type="password" v-model="pasword"> </el-input></el-col>
            </el-col>
            <el-col :span="5" :offset="8" style="margin-top: 20px;">
                <el-button type="primary" @click="onPassUp">确认</el-button>
            </el-col>
            <el-col :span="5" style="margin-top: 20px;">
                <el-button type="primary" @click="clertBtn">取消</el-button>
            </el-col>
        </el-col>
  </el-col>
  </section>
</template>

<script>
  import { state } from '../../vuex/state'
  import util from '../../common/js/util'
  import {baseUrl  } from '../../api/api';

  export default {
    data() {
      return {
        historyArry:[],
        passIval:false,
        listLoading:true,
        baonian:true,
        value:'',
        pasword:'',
        formData: new FormData(),
        url:'',
        urlArry:[],
        options:[{
            value:1,
            label:'店铺内'
        },{
            value:2,
            label:'身份详情banner'
        }]
      }
    },
    methods: {
            // 视频上传返回url
              upload (event) {
                this.formData = new FormData()
                        let file = event.target.files[0]
                        console.log(this.formData)
                        console.log(file)
                        const self = this
                        // const flag = this.flag
                        if (file) {
                          console.log('存在file', file)
                          // this.fileImg = file.name
                            // console.log(this.formData)
                            this.formData.append('file', file)
                            console.log(this.formData)
                            this.Uploadimg()
                        } else {
                          // this.fileImg = ''
                          console.log('不存在file', file)
                          this.formData = new FormData()
                        }
                },
                Uploadimg(){
                    const _this= this;
                    _this.$http.post(baseUrl+'/api/attachment/upload', _this.formData, {
                        progress(event) {
                        }
                    })
                        .then(response => {
                            const info = JSON.parse(response.bodyText);
                            // const info = response.body eval('(' + data + ')');
                            const arry = info.data
                            
                            
                            _this.url = arry[0].baseUri+arry[0].uri;
                            // if(_this.urlArry.length <3){
                            //     _this.urlArry.push(arry[0].baseUri+arry[0].uri)
                            // }
                        }, error => _this.$emit('complete', 500, error.message))
            },
            // 上传视频
            uploadBtn(){
                const _this = this
                const params = {
                    index:this.value,
                    url:this.url
                }
                console.log(params)
                $.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/store/storeVideo/upload",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                      console.log(data)
                      if(data.code === 1){
                         _this.getlist()
                      }
                    }
                });
            },
            // 使用一个月
            oneBtn(){
                const _this = this
                $.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/store/storeVideo/addCheck",
                    data:JSON.stringify({}),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                      console.log(data)
                      if(data.code === 1){
                        _this.baonian = false
                      }
                    }
                });   
                
            },
            // 购买视频
            storeVideo(){
                this.passIval = true
            },
            clertBtn(){
                this.passIval = false
            },
            //确认密码
            onPassUp(){
                const _this = this
                const params = {
                    payPassword:this.pasword
                }

                console.log(params)
                $.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/store/storeVideo/pay",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                      console.log(data)
                      if(data.code === 1){
                        _this.$message({
                          message: '成功',
                          type: 'success'
                        });
                        _this.passIval = false
                        _this.baonian = false
                      }else{
                        _this.$message({
                          message: data.msg,
                          type: 'error'
                        });
                      }
                    }
                });
            },
            // 获取视频信息
            getlist(){
                const _this = this
                $.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/store/storeVideo/selectOne",
                    data:JSON.stringify({}),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                      console.log(data)
                      _this.baonian = false
                      if(data.data !== null){
                        if(data.data.status === 1|| data.data.status === 2){
                                 _this.urlArry = data.data.url.split(',')
                                 _this.baonian = false
                          }
                      }
                    }
                });   
            },
             // 获取展示的视频信息
            gethistory(){
                const _this = this
                const params = {
                  pageNum:1,
                  pageSize:5
                }
                $.ajax({
                    type:'POST',
                    dataType:'json',
                    url:baseUrl+"/api/store/storeVideo/selectList",
                    data:JSON.stringify(params),
                    contentType:'application/json;charset=utf-8',
                    error: function (XMLHttpRequest, textStatus, errorThrown) {},
                    success:function(data){
                      console.log(data)
                    }
                });   
            }
        },
    mounted() {
      this.getlist();
      this.gethistory()
    }
  }

</script>

<style>
  
  .el-form-item__label{
    text-align: left;
  }
</style>