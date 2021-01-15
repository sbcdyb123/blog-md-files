# `uniapp`小程序蓝牙打印踩坑之路

## 查资料

就在上周,我司的产品经理甩给了我一个打印机,顺便给了我一个安卓和`IOS`的`SDK`(心里一万只草泥马,我写的是小程序你给我安卓的`SDK`干嘛??)

说在小程序里入库时加个打印标签的功能 .我嘴角微微上扬,这对面向Google和百度的我来说并不是什么难事.于是确定好了需求细节后就开始着手开始捣鼓了.

一开始搜索了相关的文章,下载了几个`demo`,结果都是不行,控制台没报错,当时打印机就是没反应,于是我又开始了我的`google`百度之旅. 后来我发现demo打印不出东西完全是因为打印机的问题,产品经理给的打印机型号是精臣的`B3S`系列, 我去问了天猫的客服,客服又让我填了一个需求反馈表单,第二天他们的技术人员会来联系我.

时间来到第二天,精臣的技术人员联系到我,  我说我需要做一个小程序的蓝牙打印功能,  技术人员说他们公司目前出的打印机机型,都是不支持指令打印的,只目前打印传输的是图片 . 而巧的是小程序的低功耗蓝牙对传输速率有限制,   所以只支持指令打印.

你说起不起,不懂行的产品经理,给了个安卓的`SDK`就算了,还给了个不支持小程序低功耗蓝牙打印的打印机.

![UeB7z8.png](https://s1.ax1x.com/2020/07/09/UeB7z8.png)

既然他们的产品不支持, 那就换一个打印机, 于是我又在天猫探索,发现了一款便携式标签打印机是支持小程序低功耗蓝牙打印的.  我买的这款是佳博`M322` .  然后用demo测试了一下 ,  结果不出我所料,是没问题的.

[demo地址](https://github.com/sbcdyb123/BluetoothPrinter-miniprogram-uniapp)

`github`上放了两个demo,一个是`uniapp`的,一个是原生的.因为我是用`uniapp`写的,本人又比较懒,直接把`uniapp`的拉过来了. 需要的自取 .

## 实现

使用了`vue`的`mixin`:

```javascript
var tsc = require('./tsc.js')
var esc = require('./esc.js')
import {mapGetters,mapMutations} from 'vuex'
import {SET_CONNECTBLUETOOTH,SET_CURRPRINTER} from '../../../store/mutation-types.js'
import {checkErr} from '../Utils/public.js'
export default {
	data() {
		return {
			devices: [],
			currDev: null,
			connId: '',
			piaojuText:'',
		}
	},
	computed:{
		...mapGetters(['isConnectBluetooth','currPrinter'])
	},
	methods: {
		...mapMutations({
			setConnectBlue:SET_CONNECTBLUETOOTH,
			setCurrentPrinter:SET_CURRPRINTER
		}),
		destroyed: function() {
			console.log("destroyed----------")
			if (this.connId != '') {
				uni.closeBLEConnection({
					deviceId: this.connId,
					success(res) {
						console.log(res)
					}
				})
			}
	
		},
		searchBle() {
			var that = this
			console.log("initBule")
			uni.showLoading({
				title:'搜索蓝牙中'
			})
			uni.openBluetoothAdapter({//初始化蓝牙模块
				success(res) {
					console.log("打开 蓝牙模块")
					console.log(res)
					that.onDevice()//监听寻找到新设备的事件
					uni.getBluetoothAdapterState({//获取本机蓝牙适配器状态
						success: function(res) {
							console.log(res)
							if (res.available) {
								if (res.discovering) {
									uni.hideLoading()
									that.stopFindBule()
								}
								//搜索蓝牙
								//开始搜寻附近的蓝牙外围设备
								console.log("开始搜寻附近的蓝牙外围设备")
								uni.startBluetoothDevicesDiscovery({
									success(res) {
										console.log(res)
										setTimeout(()=>{
											console.log('10秒倒计时')
											that.stopFindBule()
											let printer = that.devices.find(item=>item.deviceId === that.lastPrinter.deviceId)
											if(!printer&&that.connectStatus){
												uni.showModal({
													title: '提示',
													showCancel: false,
													confirmText: '确定',
													content: '没找到指定打印机',
												});
											}
										},10000)
									}
								})
	
							} else {
								uni.hideLoading()
								console.log('本机蓝牙不可用')
							}
						},
					})
				}
			})
		},
		onDevice(){
			console.log("监听寻找到新设备的事件---------------")
			var that = this
			//监听寻找到新设备的事件
			uni.onBluetoothDeviceFound(function(devices) {
						console.log('--------------new-----------------------'+JSON.stringify(devices))
						var re = JSON.parse(JSON.stringify(devices))
						console.log(re.devices[0].name + "  " + re.devices[0].deviceId)
							let name = re.devices[0].name
							if (name != "未知设备") {
								let deviceId = re.devices[0].deviceId
								that.devices.push({
									name: name,
									deviceId: deviceId,
									services: []
								})
							}	
			})
		},
		
		stopFindBule() {
			console.log("停止搜寻附近的蓝牙外围设备---------------")
			uni.stopBluetoothDevicesDiscovery({
				success(res) {
					console.log(res)
				}
			})
		},
		//连接
		onConn(item) {
			var that = this
			uni.showLoading({
				title:'连接蓝牙中'
			})
			console.log("连接蓝牙---------------" + item.deviceId)
			let deviceId = item.deviceId
			uni.createBLEConnection({//连接低功耗蓝牙设备
				deviceId: deviceId,
				complete(res) {
					console.log(res)
					if (res.errMsg == "createBLEConnection:ok") {
						console.log("连接蓝牙-[" + item.name + "]--成功")
						that.connId = deviceId;
						setTimeout(function() {
							that.getBLEServices(deviceId)//获取蓝牙设备所有服务(service)
							console.log(item,'lastPrinter存到本地')
							that.currDev = item
							that.setCurrentPrinter(item)
							uni.setStorageSync('lastPrinter',item)
						}, 2000)
					} else {
						uni.hideLoading()
						console.log(res)
					}
					
					//连接成功 关闭搜索
					that.stopFindBule()
	
				},
			})
		},
	
		getBLEServices(_deviceId) {
			var that = this;
			let deviceId = _deviceId
			console.log("获取蓝牙设备所有服务(service)。---------------")
			uni.showLoading({
				title:'获取蓝牙设备服务中.',
				icon:'none'
			})
			uni.getBLEDeviceServices({
				// 这里的 deviceId 需要已经通过 createBLEConnection 与对应设备建立链接
				deviceId: deviceId,
				complete(res) {
					console.log(res)
					let serviceId = ""
	
					for (var s = 0; s < res.services.length; s++) {
						console.log(res.services[s].uuid)
						let serviceId = res.services[s].uuid
						uni.getBLEDeviceCharacteristics({//获取蓝牙设备某个服务中所有特征值(characteristic)
							// 这里的 deviceId 需要已经通过 createBLEConnection 与对应设备建立链接
							deviceId: deviceId,
							// 这里的 serviceId 需要在 getBLEDeviceServices 接口中获取
							serviceId: serviceId,
							success(res) {
								var re = JSON.parse(JSON.stringify(res))
	
								console.log('deviceId = [' + deviceId + ']  serviceId = [' + serviceId + ']')
								for (var c = 0; c < re.characteristics.length; c++) {
									if (re.characteristics[c].properties.write == true) {
										let uuid = re.characteristics[c].uuid
										console.log(' deviceId = [' + deviceId + ']  serviceId = [' + serviceId + '] characteristics=[' +
											uuid)
	
										for (var index in that.devices) {
											if (that.devices[index].deviceId == deviceId) {
												that.devices[index].services.push({
													serviceId: serviceId,
													characteristicId: uuid
												})
												break
												
											}
	
										}
										console.log(JSON.stringify(that.devices))
	
									}
								}
							}
						})
	
					}
					uni.hideLoading()
					that.setConnectBlue(true)
					uni.showToast({
						title:"连接蓝牙成功",
						icon:'success'
					})
	
				},
				fail(res) {
					that.setConnectBlue(false)
					let msg = checkErr(res.errCode)
					uni.showModal({
						title: '提示',
						showCancel: false,
						confirmText: '确定',
						content: `${msg}`,
					});
					uni.hideLoading()
				},
			})
	
		},
		senBlData(deviceId, serviceId, characteristicId,uint8Array) {
			console.log('************deviceId = [' + deviceId + ']  serviceId = [' + serviceId + '] characteristics=[' +characteristicId+ "]")
			var uint8Buf = Array.from(uint8Array);
			function split_array(datas,size){
				var result = {};
				var j = 0
				for (var i = 0; i < datas.length; i += size) {
					result[j] = datas.slice(i, i + size)
					j++
				}
				console.log(result)
				return result
			}
			var sendloop = split_array(uint8Buf, 20);
			// console.log(sendloop.length)
			return new Promise((resolve,reject)=>{
				function realWriteData(sendloop, i) {
					var data = sendloop[i]
					if(typeof(data) == "undefined"){
						resolve('单次打印结束')
						return
					}
					console.log("第【" + i + "】次写数据"+data)
					var buffer = new ArrayBuffer(data.length)
					var dataView = new DataView(buffer)
					for (var j = 0; j < data.length; j++) {
						dataView.setUint8(j, data[j]);
					}
					uni.writeBLECharacteristicValue({
						deviceId,
						serviceId,
						characteristicId,
						value: buffer,
						success: (res)=>{
							realWriteData(sendloop, i + 1);
						},
						fail:(err)=>{
							let msg = checkErr(err.errCode)
							uni.showModal({
								title: '提示',
								showCancel: false,
								confirmText: '确定',
								content: `${msg}`,
							});
							uni.hideLoading()
							return
						}
					})
				}
				var i = 0;
				realWriteData(sendloop, i)
			})
		},
		senBleLabel(form) {
			return new Promise((resolve,reject)=>{
				//标签模式
				uni.showLoading({
					title:'标签打印中'
				})
				let deviceId = this.currPrinter.deviceId;
				let serviceId = this.currPrinter.services[0].serviceId;
				let characteristicId = this.currPrinter.services[0].characteristicId;
				var command = tsc.jpPrinter.createNew()
				// console.log(command)
				if(this.ReagentPrintModel == '3'){
					command.setSize(40, 30)
					command.setGap(2)
					command.setCls()
					command.setText(16,8, "TSS16.BF2", 1, 1, `名称: ${form.CommonName}`)
					command.setText(16,40, "TSS16.BF2", 1, 1, `纯度: ${form.Consistency}  规格: ${form.Model}`)
					command.setBarCode(16, 72, "128M", 48, 1, 2,2,form.Barcode)
					command.setText(16,176, "TSS16.BF2", 1, 1, '开瓶时间:')
					command.setBar(96,192, 160, 2)
					// command.setQR(16, 160, "L", 3, "A", "977767937@qq.com")
					command.setPagePrint()
				}else{
					command.setSize(40, 30)
					command.setGap(2)
					command.setCls()
					command.setText(16,8, "TSS16.BF2", 1, 1, `品名: ${form.CommonName}`)
					command.setText(16,40, "TSS16.BF2", 1, 1, `位置: ${this.position}`)
					command.setText(16,72, "TSS16.BF2", 1, 1, `规格: ${form.Model}  纯度: ${form.Consistency}`)
					command.setBarCode(16, 104, "128M", 48, 1, 2,2,form.Barcode)
					// command.setQR(16, 160, "L", 3, "A", "977767937@qq.com")
					command.setPagePrint()
				}
				this.senBlData(deviceId, serviceId, characteristicId,command.getData()).then((res)=>{
					// console.log(res)
					uni.hideLoading()
					resolve(res)
				})
			})
		},
		senBleLabel2(){
			//票据模式
			let deviceId = this.currPrinter.deviceId;
			let serviceId = this.currPrinter.services[0].serviceId;
			let characteristicId = this.currPrinter.services[0].characteristicId;
			var command = esc.jpPrinter.createNew()
			command.init()
	        command.setText(this.piaojuText);
	        command.setPrintAndFeedRow(1)
			this.senBlData(deviceId, serviceId, characteristicId,command.getData())
		}
	}
}
```

页面:

```javascript
async success2print(form){
    console.log(form)
    for(let i=0;i<form.length;i++){
        console.log(`第${i+1}次打印`)
        let res = await this.senBleLabel(form[i])
        console.log(res)
    }
    uni.showToast({
        title:'入库成功',
        icon:'success'
    })
},
```

原来的demo中是只能打印一次的,无法连续打印, 就将代码改了改,支持连续打印.

打印的核心函数就是`senBleLabel`和`senBlData`,其中`senBleLabel`是设置标签模版,`senBlData`定义数据,[把数据分成最大长度为20的二进制数据](https://uniapp.dcloud.io/api/system/ble?id=writeblecharacteristicvalue).然后依次写入设备特征值.

## 模版设置

参考demo文件里的**佳博标签打印机编程手册tspl  v1.0.8**,  值得一提的就是算好距离然后打印就完事了.

由于这台打印机的`DPI`为203(`DPI`可以在打印机的机身参数上面查看).

`200DPI`: `1mm` = `8dots`   

```javascript
command.setText(16,8, "TSS16.BF2", 1, 1, `名称: ${form.CommonName}`)
```

比如这一段是设置文字,`16`代表x轴`16dots`(`2mm`),y轴是`8dots`(`1mm`),`TSS16.BF2`是文字类型,具体参数可以去手册里查看,这里不多做解释,因为我自己也是看着手册来的:smile:

`40mm×30mm`的标签 dots尺寸就是,  `320×240`. 

那支笔画一画一目了然.

![Uefd5F.jpg](https://s1.ax1x.com/2020/07/09/Uefd5F.jpg)

最终打印出来的效果.各位可以根据自己的需求修改...