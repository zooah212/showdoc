<template>
  <div class="hello">
    <Header> </Header>
      <div id="header"></div>



      <div class="container doc-container" id="doc-container">


        <div id="left-side">
            <LeftMenu  ref="leftMenu" :get_page_content="get_page_content" :keyword="keyword" :item_info="item_info" :search_item="search_item" v-if="item_info" ></LeftMenu>
        </div>


        <div id="right-side" >

           <div class="doc-title-box" id="doc-title-box">
              <span id="doc-title-span" class="dn"></span>
              <h2 id="doc-title">{{page_title}}</h2>
              <el-badge :value="attachment_count" class="item"  id="attachment" v-if="attachment_count"   @click.native="ShowAttachment" >
                <i class="el-icon-upload"></i> 
              </el-badge>
          </div>
          <div id="doc-body" >

          <div id="page_md_content" class="page_content_main"><Editormd v-bind:content="content" v-if="page_id" type="html" :keyword="keyword"></Editormd></div>
          </div>

            <OpBar   :page_id="page_id" :item_id='item_info.item_id' :item_info='item_info'  :page_info="page_info"  > </OpBar>

        </div>


      </div>

      <BackToTop  > </BackToTop>
      <Toc  v-if="page_id" > </Toc>


        <!-- 附件列表 -->
        <AttachmentList callback="" :item_id="page_info.item_id" :manage="false" :page_id="page_info.page_id" ref="AttachmentList"></AttachmentList>


    <Footer> </Footer>
    
  </div>
</template>



<script>
  import Editormd from '@/components/common/Editormd'
  import BackToTop from '@/components/common/BackToTop'
  import Toc from '@/components/item/show/show_regular_item/Toc'
  import LeftMenu from '@/components/item/show/show_regular_item/LeftMenu'
  import OpBar from '@/components/item/show/show_regular_item/OpBar'
  import AttachmentList from '@/components/page/edit/AttachmentList'

  export default {
    props:{
      item_info:'',
      search_item:'',
      keyword:''
    },
    data() {
      return {
        content:"###正在加载...",
        page_id:'',
        page_title:'',
        dialogVisible:false,
        share_item_link:'',
        qr_item_link:'',
        page_info:'',
        copyText:"",
        attachment_count:'',
      }
    },
  components:{
    Editormd,
    LeftMenu,
    OpBar,
    BackToTop,
    Toc,
    AttachmentList
  },
  methods:{
    //获取页面内容
    get_page_content(page_id){
      if (page_id <= 0 ) {return;};
      //根据屏幕宽度进行响应(应对移动设备的访问)
      if( this.isMobile() ||  window.innerWidth< 1300){
        this.$nextTick(() => {
          this.AdaptToMobile();
        });
      }
        var that = this ;
        var url = DocConfig.server+'/api/page/info';
        //var loading = that.$loading({target:".page_content_main",fullscreen:false});
        var params = new URLSearchParams();
        params.append('page_id',  page_id);
        that.axios.post(url, params)
          .then(function (response) {
            //loading.close();
            if (response.data.error_code === 0 ) {
              that.content = response.data.data.page_content ;
              
              that.page_title = response.data.data.page_title ;
              that.page_info = response.data.data ;
              that.attachment_count = response.data.data.attachment_count > 0 ?  response.data.data.attachment_count  :'' ;
              //切换变量让它重新加载、渲染子组件
              that.page_id = 0 ;
              that.$nextTick(() => {
                that.page_id = page_id ;
                //页面回到顶部
                 document.body.scrollTop = document.documentElement.scrollTop = 0;
              });
              
            }else{
              //that.$alert(response.data.error_message);
            }
            
          });
    },
    //根据屏幕宽度进行响应(应对移动设备的访问)
    AdaptToMobile(){
      let childRef = this.$refs.leftMenu ;//获取子组件
      childRef.hide_menu();
      this.show_page_bar = false;
      var doc_container = document.getElementById('doc-container') ;
      doc_container.style.width = '95%';
      doc_container.style.padding = '5px';
      var header = document.getElementById('header') ;
      header.style.height = '10px';
      var docTitle = document.getElementById('doc-title-box') ;
      docTitle.style.marginTop = '30px';
    },

    onCopy(){
      this.$message(this.$t("copy_success"));
    },
    ShowAttachment(){
        let childRef = this.$refs.AttachmentList ;//获取子组件
        childRef.show() ; 
    },
  },
  mounted () {
    //根据屏幕宽度进行响应(应对移动设备的访问)
    if( this.isMobile() ||  window.innerWidth< 1300){
      this.$nextTick(() => {
        this.AdaptToMobile();
      });
    }
    this.set_bg_grey();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

  .page_content_main{
    width:800px;
    margin: 0 auto ;
    height: 50%;
    overflow: visible;
  }

  .editormd-html-preview{
    width: 95%;
    font-size: 16px;
  }

  #attachment{
    float: right;
    font-size: 25px;
    margin-top: -40px;
    margin-right: 5px;
    cursor:pointer;
    color: #abd1f1;
  }

  #page_md_content{
      padding: 10px 10px 90px 10px;
      overflow: hidden;
      font-size: 11pt;
      line-height: 1.7;
      color: #333;
  }

  .doc-container {
      position: static;
      -webkit-box-shadow: 0px 1px 6px #ccc;
      -moz-box-shadow: 0px 1px 6px #ccc;
      -ms-box-shadow: 0px 1px 6px #ccc;
      -o-box-shadow: 0px 1px 6px #ccc;
      box-shadow: 0px 1px 6px #ccc;
      background-color: #fff;
      border-bottom: 1px solid #d9d9d9;
      margin-bottom: 20px;
      width: 800px;
      min-height: 750px;
      margin-left: auto;
      margin-right: auto;
      padding: 20px;
  }

  #header{
    height: 80px;
  }

  #doc-body{
    width: 95%;
    margin: 0 auto;
    background-color: #fff;
  }

  .doc-title-box{
      height: auto;
      width: auto;
      border-bottom: 1px solid #ebebeb;
      padding-bottom: 10px;
      width: 100%;
      margin: 10px auto;
      text-align: center;
  }


  pre ol{
    list-style: none;
  }

  .markdown-body pre {
    background-color: #f7f7f9;
    border: 1px solid #e1e1e8;
  }
  .hljs{
    background-color: #f7f7f9;
  }
  .tool-bar{
    margin-top: -38px;
  }
  .editormd-html-preview, .editormd-preview-container{
    padding: 0px;
    font-size: 14px;
  }







</style>
