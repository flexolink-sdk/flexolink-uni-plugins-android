#购买柔灵产品
开发者可联系业务人员购买柔灵肌电产品与脑电产品。
#生成 App Key
每个应用程序都需要一个唯一的应用程序密钥(App Key)来初始化SDK。
获取方式：请联系业务人员获取
# 示例代码
1、添加原生插件
在demo中找到nativeplugins/sn-flexolink 插件，复制到uni项目根目录。
2、配置原生插件
在uniapp项目中的manifest.json配置文件中选择App原生插件配置，添加本地插件<柔灵android插件>。
3、初始化SDK
调用init方法初始化SDK并获取授权信息，获取授权成功后才能调用SDK的API。调用示例：
flexolinkInit(){
  const testModule = uni.requireNativePlugin('sn-flexolink');
  testModule.init({
    apiKey:"rla9ccbea81fa9",
    apiSecret:"e6599d04cc09e75d3c975352f12588bc"
  }, e => {
    if(e.code == 0){
      uni.showToast({
        title: "初始化成功",
        icon:'none'
      });
    }else{
      uni.showToast({
        title: e.msg,
        icon:'none'
      });
    }
  });
}
