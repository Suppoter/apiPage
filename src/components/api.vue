<template>
  <div>
    <div class="apiUrlInfo">
      <i class="el-icon-location"></i>
      <span class="apiTypeSpan">{{apiType}}</span>
      <span class="apiUrlSpan">{{apiUrl}}</span>
      <el-button size="mini">复制</el-button>
      <el-button size="mini">访问</el-button>
      <div class="apiServerList">
        环境列表:
        <el-select v-model="apiServer" size="mini">
          <el-option label="192.168.2.10" value="1">192.168.2.10 <i class="el-icon-check"></i></el-option>
          <el-option label="www.baidu.com" value="2">www.baidu.com <i class="el-icon-close"></i></el-option>
        </el-select>
      </div>
    </div>
    <el-tabs v-model="optionTabFirst" class="optionTab">
      <el-tab-pane name="first">
        <span slot="label"><i class="el-icon-share" style="margin: 0 3px"></i>接口入参</span>
        <div class="optionsWrap">
          <div class="optionsHead">
            <p style="width: 260px;">字段</p>
            <p style="width: 30px;">类型</p>
            <p style="width: 30px;">必传</p>
            <p style="width: 210px;">案例</p>
            <p style="width: 210px;">说明</p>
            <p>最后修改</p>
            <p>操作</p>
          </div>
          <el-tree ref="optionsWrapMenuList" class="optionsTree"
             v-if="isLoadingTree"
             :data="treeData"
             node-key="id"
             draggable
             highlight-current
             :props="defaultProps"
             :optionsWrap-on-click-node="false"
             :render-content="renderContent"
             :default-optionsWraped-keys="defaultoptionsWrapKeys"
             @node-click="handleNodeClick"></el-tree>
          <div>
            <el-form ref="form" :model="newApiKey" label-width="50px" size="mini">
              <el-row>
                <el-col :span="4">
                  <el-form-item label="名称">
                    <el-input v-model="newApiKey.codeName" auto-complete="off" placeholder="请输入字段名称"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="3">
                  <el-form-item label="类型">
                    <el-select v-model="newApiKey.codeType" size="mini">
                      <el-option
                        v-for="item in codeTypeSelect"
                        :label="item.label"
                        :value="item.value">
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
                <el-col :span="3">
                  <el-form-item label="必传">
                    <el-select v-model="newApiKey.codeNeed" size="mini">
                      <el-option
                        v-for="item in codeNeedSelect"
                        :label="item.label"
                        :value="item.value">
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
                <el-col :span="4">
                  <el-form-item label="案例">
                    <el-input v-model="newApiKey.codeExample" auto-complete="off" placeholder="请输入数据案例"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="2">
                  <el-form-item label="选项">
                    <span>
                      <el-checkbox v-model="newApiKey.hasCodeValue"></el-checkbox>
                    </span>
                  </el-form-item>
                </el-col>
                <el-col :span="5">
                  <el-form-item label="说明">
                    <el-input v-model="newApiKey.codeTips" auto-complete="off" placeholder="请输入描述该字段用处"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="3">
                  <el-form-item>
                    <el-button type="primary" @click="handleAddTop">添加字段</el-button>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>
                <el-col :span="10">-</el-col>
                <el-col :span="7">
                  <span class="codeValueEditList" v-if="newApiKey.hasCodeValue" v-for="item,key in newApiKey.codeValue">
                      <el-input class="codeValue_code" size="mini" v-model="item.code"></el-input>
                      -
                      <el-input class="codeValue_value" size="mini" v-model="item.value"></el-input>
                      <i class="el-icon-delete" v-if="newApiKey.codeValue.length>1" @click.stop="newCodeValue('delete',key)"></i>
                      <i class="el-icon-plus" @click.stop="newCodeValue('add',key)"></i>
                    </span>
                </el-col>
              </el-row>
            </el-form>
          </div>
        </div>
      </el-tab-pane>
      <el-tab-pane label="接口出参" name="second">接口出参</el-tab-pane>
      <el-tab-pane label="入参+出参" name="third">角色管理</el-tab-pane>
    </el-tabs>

  </div>
</template>

<script>
import apiOptionsTree from '@/components/apiOptionsTree'
const apiData =  {
  maxoptionsWrapId: 95,
  treelist: [{
    id: 1,
    codeName:"loginName",
    codeExample:"100",
    codeType:"S",
    codeNeed:"Y",
    codeTips:"状态码",
    hasCodeValue:false,
    lastEditTime:'2018-03-12 12:23',
    isEdit: false,
    children: [{
      id: 2,
      codeName:"media",
      codeExample:"100",
      codeType:"B",
      hasCodeValue:true,
      codeValue:[
        {code:"pc",value:"电脑端"},
        {code:"mobile",value:"移动端"},
        {code:"iphone",value:"苹果手机"}
      ],
      codeTips:"状态码",
      lastEditTime:'2018-03-12 12:23',
      isEdit: false,
    },{
      id: 3,
      codeName:"userType",
      codeExample:"100",
      codeType:"S",
      hasCodeValue:true,
      codeValue:[
        {code:"admin",value:"管理员"},
        {code:"test",value:"测试"},
        {code:"dev",value:"开发"}
      ],
      codeTips:"状态码",
      lastEditTime:'2018-03-12 12:23',
      isEdit: false,
    },{
      id: 4,
      codeName:"cityCode",
      codeExample:"100",
      codeType:"S",
      hasCodeValue:true,
      codeValue:[
        {code:"1",value:"北京市"},
        {code:"2",value:"深圳市"},
        {code:"3",value:"广州市"}
      ],
      codeTips:"状态码",
      lastEditTime:'2018-03-12 12:23',
      isEdit: false,
    }]
  }, {
    id: 5,
    codeName:"returnCode",
    codeExample:"100",
    codeType:"S",
    codeTips:"状态码",
    hasCodeValue:false,
    lastEditTime:'2018-03-12 12:23',
    isEdit: false,
    children: [{
      id: 6,
      codeName:"returnCode",
      codeExample:"100",
      codeType:"S",
      hasCodeValue:true,
      codeValue:[
        {code:"1",value:"选项1"},
        {code:"2",value:"选项2"},
        {code:"3",value:"选项3"}
      ],
      codeTips:"状态码",
      lastEditTime:'2018-03-12 12:23',
      isEdit: false,
    },{
      id: 7,
      codeName:"returnCode",
      codeExample:"100",
      codeType:"S",
      hasCodeValue:true,
      codeValue:[
        {code:"1",value:"选项1"},
        {code:"2",value:"选项2"},
        {code:"3",value:"选项3"}
      ],
      codeTips:"状态码",
      lastEditTime:'2018-03-12 12:23',
      isEdit: false,
    },{
      id: 8,
      codeName:"returnCode",
      codeExample:"100",
      codeType:"S",
      hasCodeValue:true,
      codeValue:[
        {code:"1",value:"选项1"},
        {code:"2",value:"选项2"},
        {code:"3",value:"选项3"}
      ],
      codeTips:"状态码",
      lastEditTime:'2018-03-12 12:23',
      isEdit: false,
    }]
  }]
}
export default {
  name: 'api',
  data(){
    return{
      apiUrl:"/get/userInfo/a.json",
      apiType:"POST",
      apiServer:"192.168.2.10",
      optionTabFirst:"first",
      maxoptionsWrapId: apiData.maxoptionsWrapId,//新增节点开始id
      non_maxoptionsWrapId: apiData.maxoptionsWrapId,//新增节点开始id(不更改)
      isLoadingTree: false,//是否加载节点树
      treeData: apiData.treelist,//节点树数据
      defaultProps: {
        children: 'children',
        label: 'name'
      },
      codeTypeSelect:[
        {value:'S',label:'String'},
        {value:'N',label:'Number'},
        {value:'O',label:'Object'},
        {value:'A',label:'Array'},
        {value:'B',label:'Boolean'}
      ],
      codeNeedSelect:[
        {value:'Y',label:'是'},
        {value:'N',label:'否'}
      ],
      defaultoptionsWrapKeys: [],//默认展开节点列表
      newApiKey:{
        codeName:"",
        codeType:"S",
        codeNeed:"N",
        codeExample:"",
        codeTips:"",
        hasCodeValue:false,
        codeValue:[
          {code:"",value:""}
        ]

      }
    }
  },
  mounted(){
    this.initoptionsWrap()
  },
  methods: {
    codemirrorChange:function(){

    },
    initoptionsWrap(){
      this.treeData.map((a) => {
        this.defaultoptionsWrapKeys.push(a.id)
      });
      this.isLoadingTree = true;
    },
    handleNodeClick(d,n,s){//点击节点
      // console.log(d,n)
      //d.isEdit = false;//放弃编辑状态
    },
    renderContent(h,{node,data,store}){//加载节点
      let that = this;
      return h(apiOptionsTree,{
        props: {
          DATA: data,
          NODE: node,
          STORE: store,
          maxoptionsWrapId: that.non_maxoptionsWrapId
        },
        on: {
          nodeAdd: ((s,d,n) => that.handleAdd(s,d,n)),
          nodeEdit: ((s,d,n) => that.handleEdit(s,d,n)),
          nodeDel: ((s,d,n) => that.handleDelete(s,d,n))
        }
      });
    },
    handleAddTop(){
      var newData = this.newApiKey;
      newData.id = ++this.maxoptionsWrapId;
      newData.lastEditTime = new Date().toJSON();
      newData.isEdit = false;
      this.treeData.push(newData);
      this.newApiKey ={
        codeName:"",
        codeType:"S",
        codeNeed:"N",
        codeExample:"",
        codeTips:"",
        hasCodeValue:false,
        codeValue:[{code:"",value:""}]
      }
      this.$message("添加成功!")
    },
    handleAdd(s,d,n){//增加节点
      console.log(s,d,n)
      if(n.level >=6){
        this.$message.error("最多只支持五级！")
        return false;
      }
      if (!d.children) {
        this.$set(d, 'children', []);
      }
      const newChild = {
        id: ++this.maxoptionsWrapId,
        codeName:"",
        codeExample:"",
        codeType:"",
        hasCodeValue:false,
        codeValue:[],
        codeTips:"",
        lastEditTime:"",
        isEdit: true,
      };
      d.children.push(newChild);
      //展开节点
      if(!n.expanded){
        n.expanded = true;
      }
    },
    handleEdit(s,d,n){//编辑节点
      console.log(s,d,n)
    },
    handleDelete(s,d,n){//删除节点
      console.log(s,d,n)
      let that = this;
      //有子级不删除
      if(d.children && d.children.length !== 0){
        this.$message.error("此节点有子级，不可删除！")
        return false;
      }else{
        //新增节点直接删除，否则要询问是否删除
        let delNode = () => {
          let list = n.parent.data.children || n.parent.data,//节点同级数据
            _index = 99999;//要删除的index
          /*if(!n.parent.data.children){//删除顶级节点，无children
            list = n.parent.data
          }*/
          list.map((c,i) => {
            if(d.id == c.id){
              _index = i;
            }
          })
          let k = list.splice(_index,1);
          //console.log(_index,k)
          this.$message.success("删除成功！")
        }
        let isDel = () => {
          that.$confirm("是否删除此节点？","提示",{
            confirmButtonText: "确认",
            cancelButtonText: "取消",
            type: "warning"
          }).then(() => {
            delNode()
          }).catch(() => {
            return false;
          })
        }
        //判断是否新增
        d.id > this.non_maxoptionsWrapId ? delNode() : isDel()

      }
    },
    newCodeValue:function (type,key) {
      if(type == "add"){
        if(this.newApiKey.codeValue.length >0){
          this.newApiKey.codeValue.splice(key+1,0,{code:"",value:""})
        }else{
          this.newApiKey.codeValue.push({code:"",value:""})
        }
      }else{
        this.newApiKey.codeValue.splice(key,1)
      }
    }
  }
}
</script>
<style scoped>
  .optionsWrap{
    width:100%;
    height:100%;
  }
  .optionTab{
    margin: 20px;
    box-shadow: 1px 1px 3px #ddd;
  }
  .optionsTree{
    height:85%;
    margin:0px auto 20px;
  }
  .optionsTree.el-tree-node.is-current>.el-tree-node__content{
    background: #ccc !important;
  }
  .optionsTree::-webkit-scrollbar-track{
    box-shadow: inset 0 0 6px rgba(0,0,0,.3);
    border-radius:5px;
  }
  .optionsTree::-webkit-scrollbar-thumb{
    background-color:rgba(50, 65, 87, 0.5);
    outline:1px solid slategrey;
    border-radius:5px;
  }
  .optionsTree::-webkit-scrollbar{
    width:10px;
  }
  .optionsTree .el-tree-node.is-current,
  .optionsTree .el-tree-node:hover{
    overflow:hidden;
  }
  .optionsTree .is-current>.el-tree-node__content .tree-btn,
  .optionsTree .el-tree-node__content:hover .tree-btn{
    display:inline-block;
  }
  .optionsTree .is-current>.el-tree-node__content .tree-label{
    font-weight:600;
    white-space:normal;
  }
  .optionsHead{
    color: #0095e0;
    display: table;
    background: #f1f1f1;
    border-collapse: collapse;
    width: 100%;
  }
  .optionsHead>p{
    display: table-cell;
    border-right: 1px solid #dedede;
    vertical-align: middle;
    font-size: 12px;
    line-height: 30px;
  }
  .apiUrlInfo{
    font-size: 14px;
    text-align: left;
    padding: 5px 10px;
  }
  .apiTypeSpan{
    background: #cc3b23;
    color: #fff;
    padding: 2px 5px;
  }
  .apiUrlSpan{
    padding: 2px 10px;
    margin: 0 20px;
    border: 1px solid #f1f1f1;
  }
  .apiServerList{
    float: right;
  }
</style>
