---
title: 小程序的授权和获取userInfo
author: HWH
tags:
  - UserInfo
  - Applet
categories:
  - - Tutorial
  - - Solutions
abbrlink: c2af53c1
date: 2022-11-20 14:52:21
---

## 写在前面
- 小程序官方在2021年4月28日后修改了`wx.getUserInfo`函数，所以导致目前网上大部分的文章已经不适用于解决目前小程序授权和获取userInfo的问题，此问题也是困扰了我一整天，算是初步解决，但解决方式比较粗糙，日后知识完善了会进一步修改。


## 新的函数
- 我的授权是采用`wx.getUserProfile()`函数，此函数只能在用户操作时调用(重点)，因此配合以按钮进行，下面是我采取的一种做法。

```html
  <view wx:if="{{isShow}}">
      <view wx:if="{{!hasUserInfo}}">
        <view class='headView'>
          <view class='headImageView'>
            <image class='headImage' src='cloud://cloud-hwh.636c-cloud-hwh-1302901969/头像.png'></image>
          </view>
          <view class='titleText'>申请获取以下权限</view>
          <view class='contentText'>获得你的公开信息(昵称,头像,手机等)</view>
          <button wx:if="{{canIUseGetUserProfile}}" bindtap="getUserProfile"> 允许授权 </button>
        </view>
      </view>
    </view>
  ```

- wxml和js设置的参数绑定(参数自行设置)，设置了一个按钮，并绑定了getUserProfile函数。

- 下面显示关于getUserProfile部分的js

```js
  getUserProfile(e) {
      // 推荐使用wx.getUserProfile获取用户信息，开发者每次通过该接口获取用户个人信息均需用户确认
      // 开发者妥善保管用户快速填写的头像昵称，避免重复弹窗
      wx.getUserProfile({
        desc: '完善个人资料', // 声明获取用户个人信息后的用途，后续会展示在弹窗中，请谨慎填写
        success: (res) => {
          console.log(res.userInfo)
          //获取成功后将userInfo保存
          this.setData({
            userInfo: res.userInfo,
            hasUserInfo: true,
            isShow: false
          })
          //将userInfo写入本地存储
          wx.setStorage({
            key: "userInfo",
            data: res.userInfo
          })
          //调用获取本地存储的函数，将存储到本地的userInfo取出，存放到app.globalData.userInfo
          //此处是为了将userInfo存到全局，以便其他页面使用
          app.getStorageUserInfo().then(function () {
            console.log(app.globalData.userInfo)
            if (app.globalData.userInfo) {
              that.setData({
                userInfo: app.globalData.userInfo,
                hasUserInfo: true
              })
            } else {
              console.log(wx.getUserProfile)
              if (wx.getUserProfile) {
                that.setData({
                  isShow: true,
                  canIUseGetUserProfile: true
                })
              }
            }
          })
        }
      })
    },
  ```

- 上面的代码实现授权和获取数据


## 解决重复授权

- 为了让已授权的用户在使用时无需重复授权，在app.js中使用

```js
  getStorageUserInfo: function () {
      var that = this;
      //进入页面获取是否授权----------------------------
      return new Promise(function (resolve, reject) {
        wx.getSetting({
          success(res) {//authSetting用户授权结果
            //scope.userInfo
            if (res.authSetting['scope.userInfo']) {//授权过
              console.log('授权过信息');
              //获取本地存储中的userInfo
              let userInfo = wx.getStorageSync('userInfo');
              console.log(userInfo)
              //判断是否存在useInfo，如果存在则存储到globalData.userInfo中
              if (userInfo != "") {
                that.globalData.userInfo = userInfo
              }
              console.log(that.globalData.userInfo)
            } else {
              console.log('没有授权')
            }
            //对执行成功的数据传送
            resolve("调用成功")
          }
        })
      })

    },
    globalData: {
      userInfo: null
    }
  ```

### 无法使用`wx.getUserInfo`或者`wx.getUserProfile`获取userInfo
  - `wx.getUserInfo()`仅能获取到匿名数据，这是在官方修改函数后导致的。
  - 我采用将userInfo数据存入本地，每个小程序允许有10M的本地存储，用来解决`wx.getUserProfile()`函数只能在用户操作下调用(前面有讲过)，在js中无法直接调用的问题。

  - 小程序的本地数据存取可以参考下面的文章

> [微信小程序中怎么存setStorage - 酷爱。 - 博客园](https://www.cnblogs.com/pp8080/p/12019757.html)

### 执行顺序问题
- 由于默认情况下主页面的onLoad函数要比app.js中的onLaunch函数先执行，所以在app.js中直接调用此函数无法实现判断用户是否需要授权，因此需要让onLoad函数中的操作在getStorageUserInfo函数之后执行(此函数在上面也有使用)

- 此处我采用Promise写法以解决这个问题(Promise写法可以参考下面的文章)

> [小程序 ES6 Promise用法讲解_u012693479的博客-CSDN博客_小程序promise详解](https://blog.csdn.net/u012693479/article/details/98214802)

- 下面看onLoad函数中的调用

```js
   let that = this;
      //在此处调用getStorageUserInfo,在执行后执行原本的操作
      app.getStorageUserInfo().then(function () {
        console.log(app.globalData.userInfo)
        //判断globalData.userInfo是否为空
        //注意如果直接在app.js中调用，每次进入小程序时globalData.userInfo将一直为空，因为此处先于app.js执行
        if (app.globalData.userInfo) {
          that.setData({
            userInfo: app.globalData.userInfo,
            hasUserInfo: true
          })
        } else {
          console.log(wx.getUserProfile)
          //获取授权和userInfo
          if (wx.getUserProfile) {
            that.setData({
              isShow: true,
              canIUseGetUserProfile: true
            })
          }
        }
      })
  ```

- `app`是在全局添加`var app = getApp()`而来的，这样使得在其他界面可以使用app.js中的全局变量和函数，即`globalData`和`getStorageUserInfo()`，使用方法可以参考这篇文章

> [微信小程序 如何使用globalData - 姜腾腾 - 博客园](https://www.cnblogs.com/jiangtengteng/p/10531297.html)

- 对于上面的问题还有两种办法可以解决，可以参考下面的文章

> [小程序Page里的函数比app.js先执行的解决办法 - shipskunkun - 博客园](https://www.cnblogs.com/shipskunkun/p/11802379.html)
