<template>
  <div class="page">
    <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/Cluster' }">集群</el-breadcrumb-item>
        <el-breadcrumb-item>集群详情</el-breadcrumb-item>
    </el-breadcrumb>
    <!--集群-->
    <el-card style="margin-top:20px">
      <el-row>
        <el-row>
          <el-col :span="6">
            <div class="title">集群</div>
          </el-col>
          <el-col :span="18" style="text-align:right">
            <el-button type="warning" @click="addJiQun"  icon="el-icon-plus">新增集群</el-button>
          </el-col>
        </el-row>
        <el-table
          @row-click="goClusterDetail"
          style="margin-top:25px"
          :data="clustersSorted"
          :header-cell-style="{
                        'background':'#7A7A7A',
                        'color':'#FFFFFF'
                    }"
        >
          <el-table-column
            label="序号"
            type="index"
            width="50"
          >
          </el-table-column>
          <el-table-column
            label="创建时间"
            align="center"
            prop="createtime"
          >
          </el-table-column>
          <el-table-column
            label="集群名"
            align="center"
            prop="clustername"
          >
          </el-table-column>
          <el-table-column
            label="节点数量"
            align="center"
            prop="nodenum"
          >
            <template slot-scope="scope">{{scope.row.onlinenum}}/{{scope.row.allnum}}</template>
          </el-table-column>
          <el-table-column
            label="操作"
            align="center"
            prop="user_no"
            width="700"
          >
            <template slot-scope="scope">
              <el-tooltip class="item" effect="light" content="重启" placement="bottom">
                <el-button   type="warning" @click.stop="restart(scope)">重启</el-button>
              </el-tooltip>
              <el-tooltip class="item" effect="light" content="启动" placement="bottom">
                <el-button  type="warning"  @click.stop="start(scope)">启动</el-button>
              </el-tooltip>
              <el-tooltip class="item" effect="light" content="停止" placement="bottom">
                <el-button  type="warning"  @click.stop="end(scope)">停止</el-button>
              </el-tooltip>
              <el-tooltip class="item" effect="light" content="基础配置" placement="bottom">
                <el-button  type="warning"  @click.stop="basicConfig(scope)">基础配置</el-button>
              </el-tooltip>
              <el-tooltip class="item" effect="light" content="分布式配置" placement="bottom">
                <el-button  type="warning"  @click.stop="distributedConfig(scope)">分布式配置</el-button>
              </el-tooltip>
              <el-tooltip class="item" effect="light" content="删除" placement="bottom">
                <el-button  type="warning"   @click.stop="delCluster(scope)">删除</el-button>
              </el-tooltip>
            </template>
          </el-table-column>
        </el-table>
      </el-row>
    </el-card>

    <!--新增集群-->
    <el-dialog title="新增集群" :visible.sync="jiQunNameVisible" :close-on-click-modal="false" style="margin:0 auto" :show-close="false">
      <el-form :model="addform" :rules="clusterrules" ref="addclusterForm" label-width="180px" label-position='left' style="font-size:20px" >
          <el-row>
            <el-col :span="20">
              <el-form-item label="集群名:" prop="name">
                <el-input v-model="addform.name"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="20">
              <el-form-item label="加入集群用户:" prop="adduser">
                <el-input v-model="addform.adduser"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="20">
              <el-form-item label="加入集群密码:" prop="addpassword">
                <el-input v-model="addform.addpassword"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="20">
              <el-form-item label="数据库端口（自增）:" prop="autoincrement" :required="true">
                <el-input v-model="addform.autoincrement"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-radio v-model="addform.radioswitch" label="1">自动发现</el-radio>
          </el-row>
          <el-row>
            <el-col :span="20">
              <el-form-item label="广播地址:" prop="ip" :rules="addform.radioswitch==1?clusterrules.iprule:[{ required: false, trigger: 'blur' }]">
                <el-input v-model="addform.ip" :disabled="addform.radioswitch==0"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="20">
              <el-form-item label="广播端口:" prop="port" :rules="addform.radioswitch==1?clusterrules.portrule:[{ required: false, trigger: 'blur' }]">
                <el-input v-model="addform.port" :disabled="addform.radioswitch==0"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-radio v-model="addform.radioswitch" label="0">手动设置IP</el-radio>
          </el-row>
          <el-row>
            <el-col :span="20">
              <el-form-item label="手动组播IP端口:" prop="manualradioaddress" :rules="addform.radioswitch==0?clusterrules.manualradioaddress:[{ required: false, trigger: 'blur' }]">
                <el-input v-model="addform.manualradioaddress" type="textarea" autosize  :disabled="addform.radioswitch==1"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="closeAdd">取 消</el-button>
        <el-button type="warning" @click="submitadd()">确 定</el-button>
      </div>
    </el-dialog>

    <!-- 集群基础配置 -->
    <el-dialog title="集群基础配置" :visible.sync="configVisible" :close-on-click-modal="false" custom-class="customWidth"  style="margin:0 auto" :show-close="false">
      <el-form :model="addform"  :rules="clusterrules" ref="clusterForm"
               label-width="150px" label-position='left' style="font-size:20px;height:600px;overflow:auto" >
          <el-row>
            <el-col :span="19">
              <el-form-item label="集群名:" prop="name">
                <el-input v-model="addform.name"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="19">
              <el-form-item label="加入集群用户:" prop="adduser">
                <el-input v-model="addform.adduser"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="19">
              <el-form-item label="加入集群密码:" prop="addpassword">
                <el-input v-model="addform.addpassword"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="19">
              <el-form-item label="数据库端口:" prop="autoincrement">
                <el-input v-model="addform.autoincrement"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-radio v-model="addform.radioswitch" :label="1">自动发现</el-radio>
          </el-row>
          <el-row style="margin-top:15px">
            <el-col :span="19">
              <el-form-item label="广播地址:" prop="ip" :rules="addform.radioswitch==1?clusterrules.iprule:[{ required: false, trigger: 'blur' }]">
                <el-input v-model="addform.ip" :disabled="addform.radioswitch==0"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="19">
              <el-form-item label="广播端口:" prop="port" :rules="addform.radioswitch==1?clusterrules.portrule:[{ required: false, trigger: 'blur' }]">
                <el-input v-model="addform.port" :disabled="addform.radioswitch==0"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-radio v-model="addform.radioswitch" :label="0">手动设置IP</el-radio>
          </el-row>
          <el-row style="margin-top:15px">
            <el-col :span="20">
              <el-form-item label="手动组播IP端口:" prop="manualradioaddress" :rules="addform.radioswitch==0?clusterrules.manualradioaddress:[{ required: false, trigger: 'blur' }]">
                <el-input v-model="addform.manualradioaddress" type="textarea" autosize  :disabled="addform.radioswitch==1"></el-input>
              </el-form-item>
            </el-col>
          </el-row>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="closeConfig">取 消</el-button>
        <el-button type="warning" @click="submitEdit">确 定</el-button>
      </div>
    </el-dialog>

    <!-- 集群分布式配置 -->
    <el-dialog title="集群分布式配置" :visible.sync="disConfigVisible" custom-class="customDisWidth" :close-on-click-modal="false" style="margin:0 auto" :show-close="false">
      <el-form :model="addform"  :rules="clusterrules" ref="disClusterForm" label-width="100px" label-position='left' style="font-size:20px;height:600px;overflow-x:hidden;overflow-y:auto" >
          <el-row :gutter="5">
              <el-col :span="6">
                 <el-form-item label="归属集群">{{addform.name}}</el-form-item>
              </el-col>
              <el-col :span="1">
                 <el-form-item label="节点"></el-form-item>
              </el-col>
              <el-col :span="6">
                 <el-select style="width:100%" placeholder="请选择" v-model="nodeName" @change="changenode">
                    <el-option
                      v-for="(item,index) in nodeList"
                      :key="index"
                      :label="item.nodename"
                      :value="index">
                    </el-option>
                 </el-select>
              </el-col>
              <el-col :span="2">
                 <el-form-item label="数据库"></el-form-item>
              </el-col>
              <el-col :span="6">
                  <el-select style="width:100%" placeholder="请选择" v-model="database" @change="changedatabase">
                    <el-option
                      v-for="item in databases"
                      :key="item"
                      :label="item"
                      :value="item">
                    </el-option>
                 </el-select>
              </el-col>
              <el-col :span="2">
                <el-button type="warning" @click="disConfSwitch">{{showTable==true?'转JSON':'转表格'}}</el-button>
              </el-col>
          </el-row>
          <el-row v-if="showTable==false">
            <el-col :span="11">
                <el-card>
                  <el-row>
                      <el-form-item label="源JSON">
                      </el-form-item>
                  </el-row>
                  <el-row>
                      <json-viewer :value="disConfigInfo" :expand-depth=2 copyable boxed>
                      </json-viewer>
                  </el-row>
                </el-card>
            </el-col>
            <el-col :span="13">
                <el-card>
                  <el-row>
                    <el-form-item label="待发布JSON">
                    </el-form-item>
                  </el-row>
                  <el-row>
                    <textarea style="height:400px;width:100%;font-family: -WEBKIT-BODY;font-size: 13px;" 
                    v-model="updatedConfigInfo" 
                    name="updatedConfigInfo" 
                    id="updatedConfigInfo">
                    </textarea>
                  </el-row>
                </el-card>
            </el-col>
          </el-row>
          <el-tabs v-model="activeName" v-show="showTable==true" @tab-click="handleClick" type="border-card">
                <el-tab-pane label="一般配置" name="first" :key="'first'">
                  <el-form v-if="isFirst">
                    <el-row :gutter="5">
                        <el-col :span="4">
                          <el-form-item label="readQuorum"></el-form-item>
                        </el-col>
                        <el-col :span="8">
                            <el-input style="width:100%" v-model="configform.readQuorum"></el-input>
                        </el-col>
                        <el-col :span="4">
                          <el-form-item label="writeQuorum"></el-form-item>
                        </el-col>
                        <el-col :span="8">
                            <el-input style="width:100%" v-model="configform.writeQuorum"></el-input>
                        </el-col>
                    </el-row>
                    <el-row :gutter="5">
                      <el-col :span="4">
                        <el-form-item label="Auto Deploy">
                        </el-form-item>
                      </el-col>
                      <el-col :span="8">
                          <el-select placeholder="请选择" style="maring-left:33px;width:100%" v-model="configform.autoDeploy">
                              <el-option
                                v-for="item in booleanList"
                                :key="item.value"
                                :label="item.name"
                                :value="item.value">
                              </el-option>
                          </el-select>
                      </el-col>
                      <el-col :span="4">
                        <el-form-item label="executionMode">
                        </el-form-item>
                      </el-col>
                      <el-col :span="8">
                        <el-select placeholder="请选择" style="maring-left:15px;width:100%" v-model="configform.executionMode">
                          <el-option
                            v-for="item in executionModeList"
                            :key="item.value"
                            :label="item.name"
                            :value="item.value">
                          </el-option>
                        </el-select>
                      </el-col>
                    </el-row>
                    <el-row  :gutter="5">
                      <el-col :span="4">
                        <el-form-item label="Read Your Writes">
                        </el-form-item>
                      </el-col>
                      <el-col :span="8">
                          <el-select style="width:100%" placeholder="请选择" v-model="configform.readYourWrites">
                            <el-option
                              v-for="item in booleanList"
                              :key="item.value"
                              :label="item.name"
                              :value="item.value">
                            </el-option>
                          </el-select>
                      </el-col>
                      <el-col :span="4">
                        <el-form-item label="newNodeStrategy">
                        </el-form-item>
                      </el-col>
                      <el-col :span="8">
                          <el-select style="width:100%" placeholder="请选择" v-model="configform.newNodeStrategy">
                            <el-option
                              v-for="item in newNodeStrategyList"
                              :key="item.value"
                              :label="item.name"
                              :value="item.value">
                            </el-option>
                          </el-select>
                      </el-col>
                    </el-row>
                  </el-form>
                </el-tab-pane>
                <el-tab-pane label="角色配置" name="second" :key="'second'">
                  <el-form v-if="isSecond">
                      <el-row>
                        <el-table
                          style="height:400px;overflow:auto"
                          :data="configNode"
                          :header-cell-style="{'background':'#7A7A7A','color':'#FFFFFF'}">
                          <el-table-column
                            label="节点"
                            align="center"
                            prop="name"
                            width="200"
                          >
                          </el-table-column>
                          <el-table-column
                            label="状态"
                            align="center"
                            prop="user_no"
                            width="200"
                          >
                            <template slot-scope="scope">ONLINE</template>
                          </el-table-column>
                          <el-table-column
                            label="角色"
                            align="center"
                            prop="role"
                            width="150"
                          >
                            <template slot-scope="scope">
                              <el-select  placeholder="请选择" v-model="configNode[scope.$index].role">
                                <el-option
                                  v-for="item in roleList"
                                  :key="item.id"
                                  :label="item.name"
                                  :value="item.id">
                                </el-option>
                              </el-select>
                            </template>
                          </el-table-column>
                          <el-table-column
                            label="操作"
                            align="center"
                            prop="user_no">
                            <template slot-scope="scope">
                                <el-button type="warning" @click="remove(scope)">移除</el-button>
                            </template>
                          </el-table-column>
                        </el-table>
                      </el-row>
                  </el-form>
                </el-tab-pane>
                <el-tab-pane label="簇配置" name="three" :key="'three'">
                  <el-form v-if="isThree" >
                      <el-row>
                            <el-table
                              style="height:400px;overflow:auto"
                              :data="clusterConfig"
                              :header-cell-style="{'background':'#7A7A7A','color':'#FFFFFF'}">
                              <el-table-column
                                label="Cluster"
                                align="center"
                                prop="name"
                                width="200">
                              </el-table-column>
                              <el-table-column
                                label="StaticOwner"
                                align="center"
                                prop="owner"
                                width="200">
                                <template slot-scope="scope">
                                  <el-select  placeholder="请选择" clearable v-model="clusterConfig[scope.$index].owner">
                                    <el-option
                                      v-for="item in configNodepackup"
                                      v-if="item.name!='*'"
                                      :key="item.name"
                                      :label="item.name"
                                      :value="item.name">
                                    </el-option>
                                  </el-select>
                                </template>
                              </el-table-column>
                              <el-table-column
                                :label="i.name"
                                align="center"
                                :prop="i.name"
                                width="150"
                                v-for="(i,idx) in configNode"
                                :key="idx"
                                v-if="i.name!='*'">
                                <template slot-scope="scope">
                                  <span v-if="i.name==configform.clusters[scope.row.name].servers[0]">X</span>
                                  <span v-if="i.name!=configform.clusters[scope.row.name].servers[0]">O</span>
                                </template>
                              </el-table-column>
                            </el-table>
                        </el-row>
                  </el-form>
                </el-tab-pane>
          </el-tabs>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="closeDisConfig">取 消</el-button>
        <el-button type="warning" @click="submitConfig">发布</el-button>
      </div>
    </el-dialog>

  </div>
</template>

<script>
  import {isvalidName,adduser,addpassword,ip,port,nodeorder,portorder} from '@/libs/validate'
  var namerule=(rule, value,callback)=>{
    if (!value){
      callback(new Error('请输入名称'))
    }else  if (!isvalidName(value)){
      callback(new Error('请输入数字、大小写字母、汉字及下划线"_"，不超过20位'))
    }else {
      callback()
    }
  }
  var adduserrule=(rule, value,callback)=>{
    if (!value){
      callback(new Error('请输入用户'))
    }else  if (!adduser(value)){
      callback(new Error('数字、大小写字母，不超过20位'))
    }else {
      callback()
    }
  }
  var addpasswordrule=(rule, value,callback)=>{
    if (!value){
      callback(new Error('请输入密码'))
    }else  if (!addpassword(value)){
      callback(new Error('数字、大小写字母，不超过20位'))
    }else {
      callback()
    }
  }
  var iprule=(rule, value,callback)=>{
    console.log("广播ip地址校验:"+value)
    if (!value){
      callback(new Error('请输入IP地址'))
    }else  if (!ip(value)){
      callback(new Error('请输入4段IP'))
    }else {
      callback()
    }
  }
  var portrule =(rule, value,callback)=>{
    console.log("数据库端口校验:"+value)
    let reg = /^([1-9]\d*)$/
    if(!value){
      callback(new Error('请输入端口号'))
    }else if(reg.test(value)==false){
        callback(new Error('请输入非0开头的数字'))
      }else {
        callback()
      }
  }
  var nodeorderrule =(rule, value,callback)=>{
    if (!value){
      callback(new Error('请输入排序号'))
    }else  if (!nodeorder(value)){
      callback(new Error('请输入整数，范围1-99'))
    }else {
      callback()
    }
  }
  var portorderrule =(rule, value,callback)=>{
    if (!value){
      callback(new Error('请输入4位数端口范围2400-2499'))
    }else  if (!portorder(value)){
      callback(new Error('4位数端口范围2400-2499，起始端口要小于结束端口'))
    }else {
      callback()
    }
  }
  var agentpathrule =(rule, value,callback)=>{
    if (!value){
      callback(new Error('agent注册服务名'))
    }else  if (!agentpath(value)){
      callback(new Error('请输入数字及大小写字母，不超过10位'))
    }else {
      callback()
    }
  }
  var manualradioaddressrule =(rule, value,callback)=>{
    if (!value){
      callback(new Error('以ip:port组合，并以英文'+','+'隔开'))
    }else {
      var ipreg = /^([0]|[1-9][0-9]{0,2})\.([0]|[1-9][0-9]{0,2})\.([0]|[1-9][0-9]{0,2})\.([0]|[1-9][0-9]{0,2})$/
      var portreg = /^[2][4][0-9]{2}$/
      var arr = value.split(',')
      for(let i in arr){
        arr[i].split(':')
        if(arr[i].split(':').length==1){
          callback(new Error('请按照规定的格式进行配置，如：127.0.0.1:2434,127.0.0.2:2434,127.0.0.3:2434，多个IP端口间使用英文逗号进行分隔'))
          return
        }else {
          if(ipreg.test(arr[i].split(':')[0])==false){
            callback(new Error('请按照规定的格式进行配置，如：127.0.0.1:2434,127.0.0.2:2434,127.0.0.3:2434，多个IP端口间使用英文逗号进行分隔'))
            return
          }
          if(portreg.test(arr[i].split(':')[1])==false){
            callback(new Error('端口范围2400-2499'))
            return
          }
        }
      }
      callback()
    }
  }
  export default {
    data(){
      return{
        updatedConfigInfo:'',
        disConfigInfo:'',
        disConfigVisible:false,
        showTable:true,
        activeName: 'first',
        isFirst: true,
        isSecond: false,
        isThree: false,
        clustersSorted:[
            //{"createtime":"2020-07-10 18:33:31","clusterrid":"#25:1","clustername":"测试集群-1","onlinenum":0,"allnum":1},
            //{"createtime":"2020-07-10 18:32:31","clusterrid":"#25:2","clustername":"测试集群-2","onlinenum":1,"allnum":1}
        ],
        jiQunNameVisible:false,
        configVisible:false,
        tabFlag:'3',
        addform:{
          adduser:'orientdb',
          addpassword:'orientdb',
          radioswitch:'1',
          autoincrement:'2434',
          ip:'235.1.1.1',
          port:'2434'
        },
        clusterModel:{
          adduser:'orientdb',
          addpassword:'orientdb',
          radioswitch:'1',
          autoincrement:'2434',
          ip:'235.1.1.1',
          port:'2434'
        },
        configform:{},
        roleConfigNode:[
          {},
        ],
        configNode:[],
        configNodepackup:[],
        clusterConfig:[],
        configDetail:{},
        clusters:[],
        nodeList:[],
        databases:[],
        list:[],
        clusterrules: {
          name: [
            { required: true,trigger: 'blur',validator: namerule}
          ],
          adduser: [
            { required: true,trigger: 'blur',validator: adduserrule}
          ],
          addpassword: [
            { required: true,trigger: 'blur',validator: addpasswordrule}
          ],
          ip: [
            { required: true,trigger: 'blur',validator: iprule}
          ],
          port: [
            { required: true,trigger: 'blur',validator: portrule}
          ],
          autoincrement:[
            { required: true,trigger: 'blur',validator: portrule}
          ],
          manualradioaddress:[
            { required:false,trigger: 'blur',validator: manualradioaddressrule}
          ]
        },
        nodeName:'',
        agentpath:'',
        database:'',
        booleanList:[
          {name:'true',value:true},
          {name:'false',value:false},
        ],
        executionModeList:[
          {name:'undefined',value:'undefined'},
          {name:'synchronous',value:'synchronous'},
          {name:'asynchronous',value:'asynchronous'},
        ],
        newNodeStrategyList:[
          {name:'static',value:'static'},
          {name:'dynamic',value:'dynamic'},
        ],
        roleList:[
          {id:'MASTER',name:'MASTER'},
          {id:'REPLICA',name:'REPLICA'},
        ],
        canfabu:false
      }
    },
    created(){
      this.getClusters()
    },
    methods:{
      change(e){
        this.$forceUpdate();
      },
      disConfSwitch(){
        if(this.database == ''){
          this.$message.info('请先选择数据库！')
          return
        }
        if(this.showTable === true){
          this.updatedConfigInfo = JSON.stringify(this.disConfigInfo,null,2)
          this.showTable = false
        }else {
          this.showTable = true
        }
      },
      sortJson(e){
        let resObj = {}
        let obj = Object.keys(e).sort((x,y)=>JSON.stringify(e[x]).length-JSON.stringify(e[y]).length)
        for(let i=0;i<obj.length;i++){
            let key = obj[i]
            //对servers键值对排序 TODO
            if(key == 'servers'){
              let tempServers = {}
              let servers = e[key]
              let sortedKeys = Object.keys(servers).sort()
              for(let i=0;i<sortedKeys.length;i++){
                tempServers[sortedKeys[i]] = servers[sortedKeys[i]]
              }
              delete e.servers
              e[key] = tempServers
            }
            //对clusters键值对排序 TODO
            if(key == 'clusters'){
              let tempClusters = {}
              let clusters = e[key]
              let sortedKeys = Object.keys(clusters).sort()
              for(let i=0;i<sortedKeys.length;i++){
                tempClusters[sortedKeys[i]] = clusters[sortedKeys[i]]
              }
              delete e.clusters
              e[key] = tempClusters
            }
            resObj[key] = e[key]
        }
        return resObj
      },
      handleClick (tab) {
        if (tab.name === 'first') {
          this.isFirst = true
          this.isSecond = false
          this.isThree = false
        } else if (tab.name === 'second') {
          this.isFirst = false
          this.isSecond = true
          this.isThree = false
        } else if (tab.name === 'three') {
          this.isFirst = false
          this.isSecond = false
          this.isThree = true
        }
      },
      basicConfig(e){
        this.addform.rid = e.row.clusterrid
        this.nodeName = ''
        this.agentpath = ''
        this.database = ''
        this.clusterConfig = []
        this.configNode = []
        this.configNodepackup = []
        this.configform = {}
        this.getConfigDetail(e)
        this.getNodeList(e)
        this.configVisible = true
        this.canfabu = false
      },
      distributedConfig(e){
        this.addform.rid = e.row.clusterrid
        this.nodeName = ''
        this.agentpath = ''
        this.database = ''
        this.clusterConfig = []
        this.configNode = []
        this.configNodepackup = []
        this.configform = {}
        this.getConfigDetail(e)
        this.getNodeList(e)
        this.disConfigVisible = true
        //重新打开时，展示第一个tab页
        this.activeName = 'first'
        this.isFirst = true
        this.isSecond = false
        this.isThree = false
        this.canfabu = false
      },
      closeAdd(){
        this.jiQunNameVisible = false;
        this.$refs['addclusterForm'].resetFields();
      },
      closeDisConfig(){
        this.disConfigVisible = false;
        this.showTable = true;
        document.getElementById('updatedConfigInfo').value = ''
        // 点击取消 数据重置
        this.$refs['disClusterForm'].resetFields();
      },
      closeConfig(){
        this.configVisible = false;
        // 点击取消 数据重置
        this.$refs['clusterForm'].resetFields();
      },
      addJiQun(){
        this.addform = {...this.clusterModel}
        this.jiQunNameVisible = true
      },
      submitadd(e){
        let formname = 'addclusterForm'
        if(e=='edit'){
          formname = 'clusterForm'
          if(this.addform.radioswitch == 0){
            //选择自动设置，不需要校验广播ip和广播端口
            this.clusterrules.manualradioaddress[0].required = true
            this.clusterrules.ip[0].required = false
            this.clusterrules.port[0].required = false
          }else {
            this.clusterrules.manualradioaddress[0].required = false
            this.clusterrules.ip[0].required = true
            this.clusterrules.port[0].required = true
          }
        }
        
        this.$refs[formname].validate((valid) => {
          console.log("valid:"+valid)
          if (valid) {
            if(this.addform.ip != ''){
              this.addform.radioaddress = this.addform.ip + ':' + this.addform.port
            if(this.addform.radioswitch==0){
              this.addform.radioaddress = ''
            }
            this.$post('/cluster',
              {
                ...this.addform
              },'')
              .then(res=>{
                this.$message.success('成功！')
                this.addform = {}
                this.jiQunNameVisible = false
                this.configVisible = false
                this.disConfigVisible = false
                this.getClusters()
              })
              .catch(error=>{
                console.log(error)
              })
            }else {
              this.$message.error('请检查各项内容是否正确填写')
              return false;
            }
          } else {
            this.$message.error('请检查各项内容是否正确填写')
            return false;
          }
        });

      },
      config(e){
        this.tabFlag = '3'
        this.addform.rid = e.row.clusterrid
        this.nodeName = ''
        this.agentpath = ''
        this.database = ''
        this.clusterConfig = []
        this.configNode = []
        this.configNodepackup = []
        this.configform = {}
        this.getConfigDetail(e)
        this.getNodeList(e)
        this.configVisible = true
        this.canfabu = false
      },
      changenode(e){
        this.databases = this.nodeList[e].databases
        this.agentpath = this.nodeList[e].agentpath
        this.database = this.databases[0]
        this.changedatabase(this.database)
      },
      changedatabase(e){
        this.$get('/cluster/distconf',{
          agentpath:this.agentpath,
          database:this.database,
        },'')
          .then(res=>{
            if(res.data==null){
              this.configform = {}
              this.$message.info('未查到数据库的分布式配置！')
              return
            }
            this.configform = res.data
            //给json文本域赋值
            this.disConfigInfo = this.sortJson(res.data)
            
            let list = []
            let clusterConfig = []
            // 获取角色配置configNode
            for(let i in res.data.servers){
              let obj = {}
              obj.name = i
              obj.role = res.data.servers[i]
              if(res.data.servers[i]=='master'){
                obj.role = 'MASTER'
              }
              list.push(obj)
            }
            // 获取簇配置clusterConfig
            for(let i in res.data.clusters){
              if(i!='internal'){
                let obj = {}
                obj.name = i
                for(let j in res.data.clusters[i].servers){
                  obj[res.data.clusters[i].servers[j]] = res.data.clusters[i].servers[j]
                }
                if(res.data.clusters[i].owner!=undefined){
                  obj['owner'] = res.data.clusters[i].owner
                }
                clusterConfig.push(obj)
              }
            }
            this.$message.success('数据加载完成！')
            this.clusterConfig = clusterConfig
            this.configNode = list
            this.configNodepackup = list
            this.canfabu = true
            this.$forceUpdate()
          })
          .catch(error=>{
            this.configform = {}
            console.log(error);
          })
      },
      remove(e){
        this.configNode.splice(e.$index,1)
      },
      addRole(e){
        console.log("角色配置节点添加")
      },
      submitConfig(){
        let servers = {}
        let clusters = this.configform.clusters
        for(let i in this.configNode){
          servers[this.configNode[i].name] = this.configNode[i].role
        }
        for(let i in this.clusterConfig){
          if(this.clusterConfig[i].owner!=undefined&&this.clusterConfig[i].owner!=0){
            clusters[this.clusterConfig[i].name].owner = this.clusterConfig[i].owner
          }else {
            delete clusters[this.clusterConfig[i].name].owner
          }
        }
        if(this.agentpath == ''){
          this.$message.info('请选择数据库！');
        }else {
          this.configform.clusters = clusters
          this.configform.servers = servers
          let distconfigParam = JSON.stringify(this.configform)
          if(this.showTable == false){
            let updateJson = document.getElementById('updatedConfigInfo').value
            if(this.isJson(updateJson)){
              distconfigParam = updateJson
            }else {
              this.$message.info('待发布的json字符串不合法,请检查！')
              return
            }
          }
          this.$put('/cluster/distconf',
            {
              agentpath:this.agentpath,
              database:this.database,
              distconfig:distconfigParam,
            },'')
            .then(res=>{
              this.$message.success('发布成功！');
              this.configform = {};
              this.disConfigVisible = false;
              this.showTable = true;
              document.getElementById('updatedConfigInfo').val("")
            })
            .catch(error=>{
              console.log(error);
            })
        }
      },
      isJson(e){
        if(typeof e == 'string'){
          try {
            var obj = JSON.parse(e);
            if(obj != null || obj != '' && typeof obj == 'object'){
              return true
            }else {
              return false
            }
          }catch(e){
            console.log(e)
            return false
          }
        }else {
          return false
        }
      },
      changeDistconfig(){
        this.$put('/cluster/distconf',
          {
            agentpath:this.agentpath,
            database:this.database,
            distconfig:this.configform,
          },'')
          .then(res=>{
            this.$message.success('成功！')
            this.configform = {}
            this.jiQunNameVisible = false
            this.getClusters()
          })
          .catch(error=>{
            console.log(error)
          })
      },
      submitEdit(e){
        this.submitadd('edit')
      },
      restart(e){
        this.$confirm('确认重启集群？','提示',{
          confirmButtonText:'确定',
          cancelButtonText:'取消',
          type:'warning'
        }).then(()=>{
          this.$post('/cluster/restart',{clusterrid:e.row.clusterrid},'')
            .then(res=>{
              this.$message.success('操作成功！')
              this.getClusters()
            })
            .catch(error=>{
              console.log(error)
            })
        }).catch(()=>{})
      },
      start(e){
        this.$confirm('确认启动集群？','提示',{
          confirmButtonText:'确定',
          cancelButtonText:'取消',
          type:'warning'
        }).then(()=>{
          this.$post('/cluster/start',{clusterrid:e.row.clusterrid},'')
            .then(res=>{
              this.$message.success('操作成功！')
              this.getClusters()
            })
            .catch(error=>{
              console.log(error)
            })
        }).catch(()=>{})
      },
      end(e){
        this.$confirm('确认停止集群？','提示',{
          confirmButtonText:'确定',
          cancelButtonText:'取消',
          type:'warning'
        }).then(()=>{
          this.$post('/cluster/stop',{clusterrid:e.row.clusterrid},'')
            .then(res=>{
              this.$message.success('操作成功！')
              this.getClusters()
            })
            .catch(error=>{
              console.log(error)
            })
        }).catch(()=>{})
      },
      delCluster(e){
        this.$confirm('确认删除集群？','提示',{
          confirmButtonText:'确定',
          cancelButtonText:'取消',
          type:'warning'
        }).then(()=>{
          this.$del('cluster',{rid:e.row.clusterrid},'')
            .then(res=>{
              this.$message.success('操作成功！')
              this.getClusters()
            })
            .catch(error=>{
              console.log(error)
            })
        }).catch(()=>{})
      },
      goClusterDetail(e){
        let clusterrid = e.clusterrid;
        localStorage.setItem('clusterrid',clusterrid)
        this.$router.push('ClusterDetail')
      },
      getClusters(){
        this.$get('/global/clusters',{},'json')
          .then(res=>{
            this.clusters = res.data
            this.clusterSorted()
          })
          .catch(error=>{
            console.log(error);
          })
      },
      clusterSorted(){
          let that = this
          let result = that.clusters
          result.sort(function (arg1, arg2) {
            let compare1 = arg1['createtime']
            let compare2 = arg2['createtime']
            return compare1 > compare2 ? -1 : compare1 < compare2 ? 1 : 0
          })
          this.clustersSorted = result
      },
      getConfigDetail(e){
        this.$get('/cluster',{
          clusterrid:e.row.clusterrid,
        },'')
          .then(res=>{
            this.configDetail = res.data
            this.addform = res.data
            /* this.addform.ip = res.data.radioaddress.split(':')[0]
            this.addform.port = res.data.radioaddress.split(':')[1] */
          })
          .catch(error=> {
            console.log(error);
          })
      },
      getNodeList(e){
        const that = this;
        that.$get('/cluster/nodedbs',{
          clusterrid:e.row.clusterrid,
        },'')
          .then(res=>{
            that.nodeList = res.data
          })
          .catch(error=>{
            console.log(error);
          })
      }
    }
  }
</script>

<style scoped>
  .title{color: #2A2A2A;font-size: 22px;font-weight: bold}
  .add-btn{width: 148px;height: 36px;line-height: 36px;text-align: center;background: #F38900;
    border-radius: 5px;border: 1px solid #F38900;font-weight: 400;font-size: 16px;color: #FFFFFF;float: right;
    cursor: pointer;}
  .top>.title{color: #2A2A2A;font-size: 22px;font-weight: bold}
  .top>.bottom{margin-top: 80px;font-size: 58px;font-weight: bold;display: flex;align-items: center}
  .top>.bottom>.count{margin-left: 20px}
  .top>.warning-msg{margin-top: 38px;font-size: 16px;color: #333333;font-weight: 400}
  .top>.warning-msg>.msg-item{display: flex;align-items: center;padding: 14px 0;border-bottom: 1px solid #EEEEEE}
  .top>.warning-msg>.msg-item>.msg-detail{margin-left: 13px}
  .handle-list{display: flex;align-items: center;justify-content: center}
  .handle-list>.handle-btn{font-size: 14px;font-weight: 400;color: #666666;width: 81px;height: 28px;
    line-height: 28px;text-align: center;border: 1px solid #BFBFBF;border-radius: 14px;cursor: pointer;
    margin: 0 15px}
  .handle-list>.handle-btn.CFF5E5E{color: #FF5E5E;border-color: #FF5E5E}
  .handle-list>.handle-btn.C3DA4F4{color: #3DA4F4;border-color: #3DA4F4}
  .handle-list>.handle-btn.CF38900{color: #F38900;border-color: #F38900}

  .tab-con{display: flex;align-items: center}
  .tab-con>.tab-item{min-width: 142px;height: 28px;line-height: 28px;text-align: center;padding: 0 40px;
    box-sizing: border-box;border-radius: 13px;border: 1px solid #999999;font-size: 14px;font-weight: 400;
    color: #999999;margin-right: 15px;cursor: pointer;}
  .tab-con>.tab-item.active{border-color: #F38900;color: #F38900}

  /*通用 */
  .el-dialog__header {
    padding: 20px 20px 10px;
    box-sizing: border-box;
    background: #363636;
  }
  .el-dialog__title {
    color: #FFFFFF;
  }

</style>

<style>
  .el-dialog.customWidth{
    width: 55%;
  }
  .el-dialog.customDisWidth{
    width: 60%;
  }
</style>
