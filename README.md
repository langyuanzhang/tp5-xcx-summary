# tp5-xcx-summary
学习tp5和小程序，随手记



1.	```box-sizing: border-box;```  规定两个并排的带边框的框
    border-box	为元素设定的宽度和高度决定了元素的边框盒。
    就是说，为元素指定的任何内边距和边框都将在已设定的宽度和高度内进行绘制。

2.	```border-right: 1rpx solid #d9d9d9;``` /*右边框样式：厚度，实线，颜色*/

3.	```flex-flow```属性是flex-direction属性和flex-wrap属性的简写形式，默认值为```row nowrap```。

4.	在.wxml文件的通过```data-*``` 传值，后台通过```e.currentTarget.dataset.*```取值。
    在循环出来的界面中，不同的界面可触发不同点击事件

    ```html
    <view class='btnimg' wx:for="{{imgTaps}}" wx:key="" bindtap="aaa" data-id='{{item.id}}'></view>
    ```

    ```js
    aaa: function (e) {
        console.log(e.currentTarget.dataset.id);
    }
    ```

5.	Swiper  滑块视图容器。可用于轮播图片

6.	引用工具函数
    头部引入：
    ```js
    const WxParse = require('../../utils/ wxparse.js');
    ```

    Js里使用：WxParse.lunbo
    ```js
    ../../utils/ wxparse.js中添加：

    function lunbo () {
      return 
    }
    module.exports = {
      lunbo: lunbo,
    }
    ```



7. //获取当前路径下的URL，可当工具函数使用

    ```js
    /*获取当前页url*/
    function getCurrentPageUrl() {
      var pages = getCurrentPages()    //获取加载的页面
      var currentPage = pages[pages.length - 1]    //获取当前页面的对象
      var url = currentPage.route    //当前页面url
      return url
    }
    ```
    ```js
    /*获取当前页带参数的url*/
    function getCurrentPageUrlWithArgs() {
      var pages = getCurrentPages()    //获取加载的页面
      var currentPage = pages[pages.length - 1]    //获取当前页面的对象
      var url = currentPage.route    //当前页面url
      var options = currentPage.options    //如果要获取url中所带的参数可以查看options

      //拼接url的参数
      var urlWithArgs = url + '?'
      for (var key in options) {
        var value = options[key]
        urlWithArgs += key + '=' + value + '&'
      }
      urlWithArgs = urlWithArgs.substring(0, urlWithArgs.length - 1)

      return urlWithArgs
    }
    ```

8. 标准请求
    ```js
    onLoad: function (options) {
      var that = this;
      wx.request({
        url: CONFIG.API_URL.keshi,
        data: {
          id:options.name
        },
        success: function (response) {
          that.setData({ 
            title: ti
          })
        ////  WxParse.wxParse('detail', 'html', data.content, that, 25);
        
        }  
      });
    },
    ```



9. 富文本编码转换
    现在头部引入：
    ```js
    const WxParse = require('../../utils/wxParse/wxParse.js');
    ```
    再使用：
    ```js
    WxParse.wxParse('detail', 'html', data.content, that, 25);
    ```

10. 带数据发送请求：
    ```js
    wx.request({
      url: CONFIG.API_URL.keshi, //先通过配置文件config.js添加keshi
      data: {
        id:options.name
      },
      success: function (response) {
        console.log(response);
      }
    });
    ```

    再在后台：```namespace addons\shuiguo\shop\controller```下的Api.php中添加keshi()方法。

    方法中获取传过来的参数用：$id=input('param.id');


    ###### 在.html文件中套入tp5判断语法：
    ```php
    {if condition="$category.pid eq '0'"}  哈哈  //$category.pid === 0
    {else /} 呵呵
    {/if}
    ```

    ###### 造成发送两次请求的原因：http没有改成https
    ```const BASE = 'https://yiy.11111.com'; ```
    

    ###### tp5查询数据库单条语句：
    ```php
    $data=Db::table('表名')->where('字段','eq',$id传过的值)->order('id asc')->find();
    ```

    ###### 查询多条数据：
    ```php
    $data=Db::table('addon_article_category')->where('pid','eq',$id)->select();
    ```

    ###### 连表查询，重组字段：
    ```php
    $data=Db::table('addon_article_category')->alias('a')->join('file f','f.id=a.file_id','LEFT')
    ->field('a.id,a.name,f.savepath,f.savename')->where('pid','eq',$id)->select();
    ```

    ###### 模块查询：
    ```php
    public function lunbo(){
        $Slide  =AddonShopSlids::with(['file'=>function($query){$query->field('id,savepath,savename');}])->order('rank desc,id desc')->field('id,file_id,goods_id')->select()->toArray();
        return json_encode($Slide);
    }
    ```


    ###### 添加滚动，隐藏x滚动条
    ```
    style="overflow:scroll;overflow-x:hidden;"
    ```

    ###### 循环输出富文本

    *获取数据：*
    ```lunbotext=[{…}, {…}, {…}]```

    ```json
    0:{id: 96, category_id: 158, title: "微信团队分享：“蘑菇街女装特卖”小程序实", author: "", sub: "", …}
    1:{id: 95, category_id: 158, title: "微信团队分享：小程序产品能力", author: "", sub: "", …}
    2:{id: 94, category_id: 158, title: "商业转化", author: "餐饮小程序方法论", sub: "", …}
    ```

    *Index.Wxml:*
    ```html
    <template is="wxParse" data= "{{wxParseData:replyTemArray[index]}}" />
    ```

    *Index.js:*
    ```js
    for (let i = 0; i < lunbotext.length; i++) {
      WxParse.wxParse('detail' + i, 'html', lunbotext[i].content, that, 25);
      if (i === lunbotext.length - 1) {
        WxParse.wxParseTemArray("replyTemArray", 'detail', lunbotext.length, that)
      }
    }
    ```

    ###### 图片左右滑动效果图：
    *//index.wxml*
    ```html
    <scroll-view scroll-x="true"> 
    <view class="uploadWrap" scroll-x="true"> 
    <view class="upload_Item"> 
      <image class="upload_Item_img" src="/pages/image/1.png"></image> 
    </view> 
    <view class="upload_Item"> 
      <image class="upload_Item_img" src="/pages/image/1.png"></image> 
    </view> 
    <view class="upload_Item"> 
      <image class="upload_Item_img" src="/pages/image/1.png"></image> 
    </view> 
    <view class="upload_Item"> 
      <image class="upload_Item_img" src="/pages/image/1.png"></image> 
    </view> 
    <view class="upload_Item"> 
      <image class="upload_Item_img" src="/pages/image/1.png"></image> 
    </view> 
    <view class="upload_Item"> 
      <image class="upload_Item_img" src="/pages/image/1.png"></image> 
    </view> 
    <view class="upload_Item"> 
      <image class="upload_Item_img" src="/pages/image/1.png"></image> 
    </view> 
    </view> 
    </scroll-view> 
    ```

    *//index.wxss*
    ```css
    .uploadWrap {
      height: 300rpx;
      width: 100%;
      display: flex;
      display: -webkit-box;
      flex-direction: column;
    }

    .upload_Item {
      width: 300rpx;
      height: 300rpx;
      flex: 1;
    }

    .upload_Item_img {
      width: 300rpx;
      height: 300rpx;
    }
    ```

    ###### 两个方法传值
    ###### 第一个方法中赋值：
    ```js
    that.setData({
      logourl: tempFilePaths
    })
    ```

    ###### 第二个方法取值：
    ```js
    Logourl=that.data.logourl;
    ```


    ###### 上传单张图片
    ** /**上传证件idnex.js*/ **
    ```js
    uptrafficimg: function () {
      var that = this;
      //选择相册
      wx.chooseImage({
        count: 1, // 默认9
        sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
        sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
        success: function (res) { 
          var tempFilePaths = res.tempFilePaths;
          that.setData({
            logourl: tempFilePaths
          })
          wx.uploadFile({
            url: CONFIG.API_URL.upImage, 
            filePath: tempFilePaths[0],
            name: 'file',
            formData: {
              'user': 'test'
            },
            success: function (res) {
              var data = res.data;
              console.log(data);
              //do something
            }
          })
        }
      })
    },
    ```

    ###### 服务器上传图片
    ```js
    function upImage(){
        $file=request()->file('file');
        if($file){
            $info = $file->move(ROOT_PATH . 'public' . DS . 'uploads');
            if($info){
            // 成功上传后 获取上传信息
            // 输出 jpg
                // echo $info->getExtension();
            // 输出 20160820/42a79759f284b767dfcb2a0197904287.jpg
                echo $info->getSaveName();
            // 输出 42a79759f284b767dfcb2a0197904287.jpg
                echo $info->getFilename(); 
            }else{
            // 上传失败获取错误信息
                echo $file->getError();
            }
        }       
    }
    ```

    ###### 验证手机和邮箱：
    ```js
    if (!(/^1[34578]\d{9}$/.test(e.detail.value.phone))) {
      wx.showModal({
        title: '温馨提示',
        content: '电话号码格式有误！',
      })
    }

    if (!(/^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/.test(e.detail.value.email))){
      wx.showModal({
        title: '温馨提示',
        content: '邮箱格式有误！',
      })
    }
    ```

    ###### 超出行数隐藏。。。
    ```css
    .detailsdiv1 {
      line-height: 60rpx;
      font-size: 28rpx;
      padding-left: 20rpx;
      padding-right: 10rpx;
      padding-bottom: 10rpx;
      display: -webkit-box;
      word-break: break-all;
      text-overflow: ellipsis;
      overflow: hidden;
      -webkit-box-orient: vertical;
      -webkit-line-clamp: 4;
    }
    ```

    ###### 一行隐藏
    ```css
    overflow:hidden;
    white-space:nowrap;
    text-overflow:ellipsis;
    ```

    ###### 三目运算控制显示和隐藏
    ```html
    <view class=" {{show==1?'show':'detailsdiv1'}}"> 
    ```

    ###### 页面上拉触底事件的处理函数
    ```js
    onReachBottom: function () {
      var that = this;
      console.log('上啦')
      // if (this.data.loading) return;
      // this.setData({ loading: true });
      // that.updateRefreshIcon.call(this);

      that.setData({
        page: (that.data.page + 1)
      });
      console.log(that.data.page)
      that.latlog(that.data.page);

      // setTimeout(() => {
      //   wx.showToast({
      //     title: '加载中',
      //     icon: 'loading',
      //     duration: 2000
      //   })
      // }, 3000)
    },
    ```

    ###### 旋转刷新图标
    ```js
    updateRefreshIcon: function () {
      var deg = 0;
      console.log('旋转开始了.....')
      var animation = wx.createAnimation({
        duration: 3000
      });

      var timer = setInterval(() => {
        if (!this.data.loading)
          clearInterval(timer);
        animation.rotateZ(deg).step();//在Z轴旋转一个deg角度
        deg += 360;
        this.setData({
          refreshAnimation: animation.export()
        })
      }, 3000);
    },
    ```

	  ###### 入驻的汽车信息/本地服务加载
    ```js
    function getSettledlist(){
    	$s = input('param.');
  		$count = 3;
    	$yema = $count*($s['page']-1);

    	$data=Settledlist::order('id desc')->limit($yema,$count)->select();

    	foreach ($data as $k => $v) {
    		$li=explode(",",$v['file_id']);
    		$file = array();
    		foreach ($li as $ks => $vs) {
    			$file[] = Db::name('file')->find($vs);
    		}
    		$data[$k]['file']=$file;
    	}
    	return json_encode($data);
    }
    ```

    ###### 同一个表，先把相同属性a 的 属性b值相加 ，再排序。。 排行榜
    ```js
    function rank(){
      $data=Db::table('jh_payments')
      ->field('*,sum(paymoney) as sum_pay')
      ->group('uid')
      ->order('sum_pay desc')
      ->select();
      return json_encode($data);}
    ```

    ###### 渐变
    ```css
    background: -webkit-linear-gradient(left, #7DE369 , #c9dd22); /* Safari 5.1 - 6.0 */
    background: -o-linear-gradient(right, #7DE369, #c9dd22); /* Opera 11.1 - 12.0 */
    background: -moz-linear-gradient(right, #7DE369, #c9dd22); /* Firefox 3.6 - 15 */
    background: linear-gradient(to right, #7DE369 , #c9dd22); /* 标准的语法（必须放在最后） */
    ```


    ###### 微信小程序跳转传参
    一般都是传字符串到下一页，如果要想传对象怎么办呢？
    我的解决办法是先将对象转换为json字符串然后到下个页面将json字符串，再转化为对象。如下：
    ```js
    let str=JSON.stringify(e.currentTarget.dataset.item);
    wx.navigateTo({
      url: '../toMybaby/babyDetail/babyDetail?jsonStr='+str,
    })
    ```

    ###### 另一个页面加载
    ```js
    onLoad:function(options){
      // 生命周期函数--监听页面加载
      let item=JSON.parse(options.jsonStr);
      this.setData({ward:item});
    },
    ```


    ###### PHP 将json的stdClass Object转成数组array 
    
    $user是一个stdClass 每次调用都是 $user->uid 其实也是挺方便的  

    把$user转为array数组  
    ```js
    	$user  =json_encode($user); //把她转换为json字符串  
    	$user = json_decode($user,true); //再把json字符串格式化为数组  
    ```
    或者直接是:  
    ```js
    $user = json_decode(json_encode($user),true); 
    ``` 



    ###### 判断标签
    ```js
    {switch name="$vo.odstatus"}
      {case value="0"}待支付{/case}
      {case value="1"}已完成{/case}
      {case value="2"}已取消{/case}
      {default /}default
    {/switch}
    ```


    ###### 获取天气
    *小程序获取*
    请求的参数:
    ```js
    var datas = {
        city:'哈尔滨市'
    };
    //请求的路径
    var postUrl = "https://www.sojson.com/open/api/weather/json.shtml";//请求的路径
    app.postDatas('GET', postUrl, datas, function (res) {
        console.log(res)
    })
    ```

    *服务器端获取*
    ```php
    $data =file_get_contents('http://www.weather.com.cn/data/sk/101300801.html');
    $obj=json_decode($data);
    dump($obj);
    die;
    ```

    ###### 去重 
    ```php
    ->group("building")
    ```

    ###### 记录操作日志
    ```php
    $request= \think\Request::instance();
    $param = ['name'=>'用户登录','way'=>$request->module().'/'.$request->controller().'/'.$request->action(),'username'=>session('username'),'create_time'=>date('Y-m-d H:i:s',time()),'ip'=>$_SERVER['REMOTE_ADDR']];
    //保存日志
    saveLog($param);
    ```



    ###### 带参数分页
    *订单管理订单列表*
    ```php
    public function order()
    {
        $where=array();

        $where['odstatus']=1;//默认先显示已支付

        if($this->request->get()){
            $post = $this->request->get();
            if (isset($post['order_id'])&&isset($post['odstatus'])&&isset($post['test10'])&&isset($post['ps_status'])) {
                if($post['order_id'] != ''){
                    $where['order_id']=array('like','%'.$post['order_id'].'%');
                }
                if($post['odstatus'] != ''){
                    $where['odstatus']=(int)$post['odstatus'];
                }
            }
        }

        $order_list = OrderModel::where($where)
        ->order('status desc,id desc')
        ->paginate(12,false,['query'=>request()->param()]); //带参数分页

        $this->assign('orders', $order_list);
        return $this->fetch();
    }
    ```

    ###### 强制换行
    ```
    1、word-break: break-all;       只对英文起作用，以字母作为换行依据。
    2、word-wrap: break-word;   只对英文起作用，以单词作为换行依据。
    3、white-space: pre-wrap;     只对中文起作用，强制换行。
    ```

    ###### 禁止换行
    ```css
    white-space:nowrap; 
    overflow:hidden; 
    text-overflow:ellipsis;
    ```

    ###### 模板调用方法
    > 调用公共方法settled_name，$vo.settled_id作为参数。 参考手册：模板->使用函数https://www.kancloud.cn/manual/thinkphp5/125005 
    ```html
    <td>{$vo.settled_id | settled_name}</td>
    ```

    ###### TP5的连表查询加计算总数
    ```php
    $list=Db::name("storage_name")
      ->alias('a')
      ->join('storage b','a.name=b.storage_name')
      ->field(['a.id,a.name,sum(b.number) as count'])
      ->group('a.name')
      ->paginate(15);
    ```

    ###### tp框架在视图中时间戳转换成时间
    ```php
    {$vo[' create_time']|date="Y-m-d H:i:s",###}
    ```

    ###### 单独在模型里面设置：（推荐）
    ```php
    protected $autoWriteTimestamp = true;       // int 型
    protected $autoWriteTimestamp = 'datetime';   // datetime 类型
    protected $autoWriteTimestamp = false;     // 关闭自动写入时间戳
    protected $updateTime = false;　　　　　　　// 只关闭自动写入update_time字段
    ```


    ###### 下载文件
    ```html
    <a target='_blank' style='margin-right:10px;color:blue;' href='download?id=".$v['id']."'><u><em>下载</em></u></a>
    ```
    
    *下载文件方法*
    ```php
    public function download()
    {
        $id = input('param.id');
        $file = Db::name('file_list')->where('id','eq',$id)->find();
        $file_name = $file['savepath'].$file['savename'];
        $file_dir = ROOT_PATH.'public'.DS.'uploads'."/".$file_name;
        if(!file_exists($file_dir)){
            $this->error('文件找不到');
        }else{
            //打开文件
            $file1 = fopen($file_dir,'r'); 
            //输入文件标签
            header("Content-type: application/octet-stream");
            header("Accept-Ranges: bytes");
            header("Accept-Length: ".filesize($file_dir));
            header("Content-Disposition: attachment; filename=".$file['savename']);
            //输出文件内容
            echo fread($file1,filesize($file_dir));
            fclose($file1);
            exit();
        }
    }
    ```

    ###### layui放大预览
    *放大预览图片*
    ```js
    function lotus_preview(url,w,h,offset){
        var img_infor = "<img src='" + url + "' style='margin:20px'/>";
        if(offset==null||offset==''){
            offset = 100;
        }
        if (url == null || url == '') {
            url="404.html";
        };
        if (w == null || w == '') {
            w=($(window).width()*0.9);
        };
        if (h == null || h == '') {
            h=($(window).height() - 50);
        };
        layer.open({
            offset: 'auto', //右下角弹出
            type: 1,
            area: [w+'px', h +'px'],
            fix: false, //不固定
            maxmin: true,
            shadeClose: true, //是否点击遮罩关闭
            shade:0.4,
            closeBtn: 1,
            title: false, //不显示标题
            content: img_infor
        });
    }
    ```

