#Hybrid App 前端微信功能开发 ionic cordova ——微信分享
##前言
* 目前微信开放平台API，对于移动端APP应用只开放了Android和IOS原生APP调用接口。
* 关于微信JSSDK需要在微信内部使用，适用于微信公众号等开发。
* 选择开源cordova插件[https://github.com/xu-li/cordova-plugin-wechat](https://github.com/xu-li/cordova-plugin-wechat)
##开发实战

###安装插件
* 命令行<pre>cordova plugin add cordova-plugin-wechat --variable wechatappid=YOUR_WECHAT_APPID</pre>
* 或者<pre>ionic plugin add cordova-plugin-wechat --variable wechatappid=YOUR_WECHAT_APPID</pre>
注释：YOUR_WECHAT_APPID 指项目app在[微信开放平台](https://open.weixin.qq.com/cgi-bin/frame?t=home/app_tmpl&lang=zh_CN)申请到的APPID

###测试插件
* 检测微信是否安装示例：
* <pre>Wechat.isInstalled(function (installed) {
	    alert("Wechat installed: " + (installed ? "Yes" : "No"));
	}, function (reason) {
	    alert("Failed: " + reason);
	});
 </pre>
* 将以上代码放在app.js启动文件中，测试时，需要在实机测试。
* 安装好插件后，就可以直接使用了，不用其他配置，实际上该插件暴露了一个Wechat的全局变量
* 详情可参见 [https://github.com/xu-li/cordova-plugin-wechat-example/tree/master/ionic](https://github.com/xu-li/cordova-plugin-wechat-example/tree/master/ionic)


###项目实战
* 测试插件成功之后，可以看到该插件的功能还是相对完善的。
	1. 1 微信授权登录
	2. 2 微信分享文本
	3. 3 微信分享多媒体（photo, music, video etc）
	4. 4 微信分享链接
	5. 5 微信支付
* services中函数封装
	<pre>
function share(params,id){
	    	if (typeof Wechat === "undefined") {
	            alert("Wechat plugin is not installed.");
	            return false;
	        }

            switch (id) {
                case 'check-installed':
                Wechat.isInstalled(function (installed) {
                    alert("Wechat installed: " + (installed ? "Yes" : "No"));
                });
                return true;

                case 'send-link-thumb-remote':
                break;

                case 'auth':
                Wechat.auth("snsapi_userinfo", function (response) {
                    // you may use response.code to get the access token.
                    alert(JSON.stringify(response));
                }, function (reason) {
                    alert("Failed: " + reason);
                });
                return true;

                default:
                alert(id + " can not be recognized!");
                return false;
            }

	        Wechat.share(params, function () {
	            alert("分享成功！");
	        }, function (reason) {
	            alert("Failed: " + reason);
	        });
	        return true;
	    };
	</pre>
* controller 层代码
	<pre>
function shareLive(id,type){
        	Wechat.isInstalled(function (installed) {
			    if(!installed){
			    	CommonService.showMsg("请安装微信！");
			    	return false
			    }
			}, function (reason) {
			    CommonService.showMsg("Failed: " + reason);
			});
			
			$scope.params = {
				scene : type,
                message : {
                	title: "专访张小龙：产品之上的世界观",
                	description: "微信的平台化发展方向是否真的会让这个原本简洁的产品变得臃肿？在国际化发展方向上，微信面临的问题真的是文化差异壁垒吗？腾讯高级副总裁、微信产品负责人张小龙给出了自己的回复。",
                	thumb: "https://cordova.apache.org/images/cordova_256.png",
                	media: {
	                	type : Wechat.Type.LINK,
	                	webpageUrl : "http://tech.qq.com/",
	                },
                }
           };
		    CommonService.share($scope.params, id);
		};
	</pre>

* view 层代码

		<ion-item class="rn-share-logo">
			<div class="col-25 fl" ng-click = "vm.shareLive('send-link-thumb-remote',0)">
				<img src="img/friend.png" alt="" class="img-responsive" />
				<p class="text-center">好友</p>
			</div>
			<div class="col-25 fl" ng-click = "vm.shareLive('send-link-thumb-remote',1)">
				<img src="img/live.png" class="img-responsive" />
				<p class="text-center">朋友圈</p>
			</div>
		</ion-item>

##效果展示

![微信分享](http://i1.piimg.com/567571/6f9418adc7b0986a.png)

##TODO
* 微信支付
* 微信登录

##FAQ
* 欢迎相互交流学习
