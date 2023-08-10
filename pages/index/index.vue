<template>
	<view class="content">
		<view class="text-area">
			<text class="title">{{title}}</text>
		</view>
		<button type="default" @click="flexolinkInit">初始化SDK</button>
		<button type="default" @click="flexolinkScan">扫描设备</button>
		<button type="default" @click="flexolinkConnect">连接设备</button>
		<button type="default" @click="flexolinkDisconnect">断开设备</button>
		<button type="default" @click="flexolinkStopScan">停止扫描</button>
		<button type="default" @click="flexolinkRelease"释放SDK</button>
		<button type="default" @click="flexolinkSignalDetect">开始数据质量检测</button>
		<uni-section title="扫描列表" type="line">
		  <uni-data-select
			v-model="value"
			:localdata="range"
			@change="change"
		  ></uni-data-select>
		</uni-section>
		<view class="text-area">
			<text class="title">电量：</text><text class="title">{{battery}}</text><br>
		</view>
		<view class="text-area">
			<text class="title">版本：</text><text class="title">{{deviceVersion}}</text><br>
		</view>
		<view class="text-area">
			<text class="title">信号：</text><text class="title">{{rssi}}</text><br>
		</view>
		<view class="text-area">
			<text class="title">是否佩戴：</text><text class="title">{{isWear}}</text><br>
		</view>
		<view class="text-area">
			<text class="title">连接状态：</text><text class="title">{{connectStatus}}</text><br>
		</view>
	</view>
</template>

<script>
	var globalVar = 'abc'
	export default {
		data() {
			return {
				title: 'Hello',
				value: 0,
				range: []
			}
		},
		onLoad() {

		},
		methods: {
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
			},
			flexolinkScan(){
				const testModule = uni.requireNativePlugin('sn-flexolink');
				const result = testModule.scanDevice(e => {
					const name = e.data.bleName;
					const mac = e.data.bleMac;
					this.range.push({
					      value: name,
					      text: name 
					    })
				});
			},
			flexolinkConnect() {
			  const testModule = uni.requireNativePlugin('sn-flexolink');
			  const result = testModule.connectDevice({
					bleName:globalVar,
					bleMac:""
				},e => {
					if(e.code == 0){
						uni.showToast({
							title: "连接成功",
							icon:'none'
						});
			
						//开启实时获取数据
						testModule.setRealDataListener({
							isFilter:true
						},e => {
							let formatted = e.data.eegList.map(num => num.toFixed(1)); 
							this.title = JSON.stringify(formatted);
						})
						
						//获取设备信息
						const deviceInfoResult = testModule.getDeviceInfo();
						if(deviceInfoResult.code == 0){
							this.deviceVersion = deviceInfoResult.data.version;
						}
						
						//获取电量
						const batteryResult = testModule.getBattery();
						if(batteryResult.code == 0){
							this.battery = batteryResult.data.battery;
						}
						
						//获取信号强度
						const rssiResult = testModule.getRssi();
						if(rssiResult.code == 0){
							this.rssi = rssiResult.data.rssi;
						}
						
						//获取佩戴情况
						const wearResult = testModule.getDeviceWearStatus();
						if(wearResult.code == 0){
							if(wearResult.data.isWear){
								this.isWear = "已佩戴";
							}else{
								this.isWear = "未佩戴";
							}
						}
						//获取佩戴情况
						const connectResult = testModule.getConnectStatus();
						if(connectResult.code == 0){
							if(connectResult.data.connectStatus){
								this.connectStatus = "已连接";
							}else{
								this.connectStatus = "未连接";
							}
						}
						
						
					}else{
						uni.showToast({
							title: e.msg,
							icon:'none'
						});
					}
			
			  });
			},
			flexolinkDisconnect() {
			  const testModule = uni.requireNativePlugin('sn-flexolink');
			  testModule.closeDevice();
			},
			change(e) {
				globalVar = e;
				console.log('e:', e);
			},
			flexolinkGetDeviceInfo() {
				globalVar = e;
				console.log('e:', e);
			},
			flexolinkSignalDetect() {
				const testModule = uni.requireNativePlugin('sn-flexolink');
				const result = testModule.signalDetect(e => {
					if(e.code == 0){
						uni.showToast({
							title: "信号强度检测通过",
							icon:'none'
						});
					}else{
						uni.showToast({
							title: "信号强度检测不通过",
							icon:'none'
						});
					}
				});
			},
			flexolinkStopScan() {
				const testModule = uni.requireNativePlugin('sn-flexolink');
				testModule.stopScan();
			},
			flexolinkRelease() {
				const testModule = uni.requireNativePlugin('sn-flexolink');
				testModule.release();
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 26rpx;
		color: #8f8f94;
	}
  .text {
    font-size: 12px;
    color: #666;
    margin-top: 5px;
  }

  .uni-px-5 {
    padding-left: 10px;
    padding-right: 10px;
  }

  .uni-pb-5 {
    padding-bottom: 10px;
  }
  .uni-data-select{
	  width: 100%;
  }
  .uni-section{
	  width: 100%;
  }
</style>
